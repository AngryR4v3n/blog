---
layout: post
title: Fledge and UNS - A quick and easy implementation
date: 2025-01-11 11:17 +0000
categories: [iot, programming]
---
# Introduction
Since my [last post](% post_url 2025-01-07-fledge-a-true-digital-docking-station), I introduced the IoT data platform [Fledge](https://lfedge.org/projects/fledge/), and how it can be leveraged to work as an accelerator for IoT projects.
In this article I plan to demonstrate just that, and what better way to show it than by implementing one of the latest trends into IIoT concepts - The Unified Namespace (UNS). 

## The Unified Namespace
As I understand it, with my limited experience, the UNS tries to achieve a simple yet important goal - the goal of setting a "one source of truth". I am saying this from the conclusions I gathered while listening to the material Walker Raynolds and Kudzai Manditereza have shared in their social media. I definitely recommend you check it out for yourself, as they dive into other features of UNS which I won't cover today.

The UNS comes as an alternative to other traditional architectures such as the famous pyramid-shaped network (ISA-95). ISA-95 is described as an automation pyramid, each level represents integration between plant and enterprise systems.
<a href="/assets/img/posts/ISA-95.jpeg" style='display: flex; justify-content: center' data-lity>
  <img src="/assets/img/posts/ISA-95.jpeg"/>
</a>

On this type of architecture, connections between services are point-to-point, meaning each component is tightly coupled. This makes complex integrating new nodes to the architecture, that is 3rd party systems, as each node will require its unique implementation.

For example:

You are tasked with designing a service that requires information from a particular asset/line and ERP-related information, your service would need to connect to two different systems and both implementations would be totally different, particularly given that probably by extracting data out of a line/asset you might need to connect to an OPC-UA server, while the ERP would have some sort of API. This introduces complexity to the system, slowing down development of new features and ultimately slowing you down to bring the actual value to your customer.

As mentioned previously, the UNS is supposed to work as a single source of truth, around the web you will find posts where the implementation most commonly found is the one based on a MQTT broker, that is the UNS lives inside the broker.

UNS takes advantage of the concept of the topic in MQTT, for getting information on MQTT topics please check out [HiveMQ's explanation](https://www.hivemq.com/blog/mqtt-essentials-part-5-mqtt-topics-best-practices/)

Taking advantage of how MQTT topics and subscriptions work, we could come up with a UNS that looks like this: 
```
Company/
├─ Location/
│  ├─ Line1/
│  │  ├─ Machine1/
│  │  │  ├─ Temp
│  │  │  ├─ RPM
│  │  │  ├─ ...
│  │  ├─ Machine2/
│  ├─ Line2/
│  │  ├─ ...
│  ├─ Line3/
│  │  ├─ ...
├─ LocationB/
│  ├─ ...
```

Such structures are not uncommon, a similar "tree" can be seen OPC-UA servers. 

One benefit of UNS is that contrary to the more traditional approach, we can easily implement fan-out pattern from it, therefore giving us a global access to all relevant pieces of information, so each service can access it directly through the MQTT broker.

## Implementing the UNS in Fledge
### Setting up Fledge
I highly recommend the [dockerized version](https://fledge-iot.readthedocs.io/en/latest/quick_start/installing.html#using-docker-containerizer-to-install-fledge)
After starting the container, head to ``http://localhost:8082``

On the admin panel you will see two sections that will be relevant for this instructable:
 1. South - Data relative to the assets.
 2. North - Endpoints where the data is going to be sent.

### Starting services
<a href="/assets/img/posts/step-1.jpg" style='display: flex; justify-content: center' data-lity>
  <img src="/assets/img/posts/step-1.jpg"/>
</a>

Between north and south you can implement filters, notifications, and other operations that will help contextualize the data, you can read more about Fledge's capabilities on their very useful [documentation](https://fledge-iot.readthedocs.io/en/latest/processing_data.html). Today I won't be explaining them but if you are curious about Fledge, they are a must to act on data that is being collected.

Head into South and click "Add"

<a href="/assets/img/posts/step-2.jpg" style='display: flex; justify-content: center' data-lity>
  <img src="/assets/img/posts/step-2.jpg"/>
</a>

Click on "available plugins" and select **opcua**, once that's done you should have it listed on your available plugins, assign it a name and click next.

<a href="/assets/img/posts/step-3.jpg" style='display: flex; justify-content: center' data-lity>
  <img src="/assets/img/posts/step-3.jpg"/>
</a>

We will be shown with a screen like this one, to continue we need to get an OPC server.
<a href="/assets/img/posts/step-4.jpg" style='display: flex; justify-content: center' data-lity>
  <img src="/assets/img/posts/step-3.jpg"/>
</a>

### Getting an OPC server
You can feel free to use your own OPC server, if you don't have one, I made public a Dockerfile this will be useful for generating dummy data.

You can find it [here](https://github.com/AngryR4v3n/opc-server) this will be useful to demonstrate the ingestion of data by Fledge.

To start the Dockerized OPC server, on terminal type:
```bash
sudo docker run -d -e OPC_STRUCTURE=uns.json -e TAG_FREQUENCY=0.5 opc-python-server
```
This will start the server and will generate random info every 500ms, based on the structure defined in ``uns.json``

To get the server URL run on your console:
```bash
docker inspect docker_container_id
```
Look for the key NetworkSettings -> IPAddress.

Your OPC server should be available at: ``opc.tcp://your_ip:4840/``

To verify that the OPC server is running, and to obtain nodeids (that will be useful in a second), I utilized UA Expert program for that.

<a href="/assets/img/posts/uaexpert.png" style='display: flex; justify-content: center' data-lity>
  <img src="/assets/img/posts/uaexpert.png"/>
</a>

### Setting up a South Service
Take all the tags IDs you need, these will go inside the "OPCUA Object Subscriptions" field on Fledge.

<a href="/assets/img/posts/step-5.png" style='display: flex; justify-content: center' data-lity>
  <img src="/assets/img/posts/step-5.png"/>
</a>

Click on save, and you should have assets with measurements being ingested

<a href="/assets/img/posts/step-6.png" style='display: flex; justify-content: center' data-lity>
  <img src="/assets/img/posts/step-6.png"/>
</a>
### Observing measurements
These measurements are currently being ingested but not being sent elsewhere, one of the cool **default** features of Fledge is the *store and forward* mechanism, meaning that as long as data is not being sent "North" the data stays within Fledge waiting to be serviced. I found this feature very useful, since whenever we get close to the edge we must be prepared for cases where connectivity might be limited, such feature reduces the chances of losing information.


Check out the **"Assets & Readings"** section, there you can access the plot of each individual tag, in Fledge called **"Assets"**.

<a href="/assets/img/posts/step-7.png" style='display: flex; justify-content: center' data-lity>
  <img src="/assets/img/posts/step-7.png"/>
</a>

<a href="/assets/img/posts/step-8.png" style='display: flex; justify-content: center' data-lity>
  <img src="/assets/img/posts/step-8.png"/>
</a>

### Sending data to the North
Fledge at this point is accumulating data, to send data from Fledge, we need a "North" service. Here is where we start actually implementing the UNS. 
We will be sending data to a MQTT broker, unfortunately we cannot use a "downloadable" plugin, since it simply does not exist -officially- yet. 

One of the positive sides of Fledge is that you can start writing your own plugins. I took the liberty to create my own north mqtt plugin, how did I learn that? Well, I very much recommend the following links - specially the one from Youtube ;)
* https://www.youtube.com/watch?v=YOijv_pLW1M
* https://fledge-iot.readthedocs.io/en/latest/plugin_developers_guide/index.html

To get the plugin that I developed for publishing values to MQTT feel free to clone the following [repository](https://github.com/AngryR4v3n/fledge-python-mqttnorth)

To deploy the plugin enter into your Fledge container and run the following commands:
```bash
sudo docker exec -ti <your_docker_id> bash
# Once inside the container
cd /usr/local/fledge/python/plugins/north/
mkdir mqttnorth
# exit the container
```
Go to the location where you cloned the repository and do the following:
```bash
docker cp <your_location>/mqttnorth.py container_id:/usr/local/fledge/python/plugins/north/mqttnorth
```
The plugin contains Python dependencies that are not installed by default on fledge, to fix that:

Go to the Fledge UI and head to the settings and activate Developer features, then refresh the site.

<a href="/assets/img/posts/step-9.png" style='display: flex; justify-content: center' data-lity>
  <img src="/assets/img/posts/step-9.png"/>
</a>

Then you will find the developer settings -> python packages.
Make sure you install the dependencies that are listed in `requirements.txt` file

Head to the north section and click add, you should see listed in North plugins: ``mqttnorth``, give a name to the service and check the box "Add as a Service"

<a href="/assets/img/posts/step-10.png" style='display: flex; justify-content: center' data-lity>
  <img src="/assets/img/posts/step-10.png"/>
</a>

At this point, we need to start a broker, I personally used the [HiveMQ docker image](https://hub.docker.com/r/hivemq/hivemq-ce)
To get the server URL run on your console:
```bash
docker inspect docker_container_id
```

Now time to fill the information to initialize our plugin:

<a href="/assets/img/posts/step-10.png" style='display: flex; justify-content: center' data-lity>
  <img src="/assets/img/posts/step-10.png"/>
</a>


The following configurations are important:
* `Asset name prefix to be removed`: If you noticed, all assets that were created (tags) contain a same prefix, in order to make names cleaner in the MQTT broker you can add the substring to be deleted.
* `Topic Prefix`: This is the topic where the tree-like structure from the OPC server will be forwarded to inside our mqtt broker.

Click on "Save"

<a href="/assets/img/posts/step-11.png" style='display: flex; justify-content: center' data-lity>
  <img src="/assets/img/posts/step-11.png"/>
</a>

To view your UNS use mqtt-explorer or any other mqtt client and subscribe to all topics under the topic prefix you selected
<a href="/assets/img/posts/mqtt.png" style='display: flex; justify-content: center' data-lity>
  <img src="/assets/img/posts/mqtt.png"/>
</a>

So there you go, a quite long post! 

On the next post I intend to reflect the capabilities of Fledge.

See you!