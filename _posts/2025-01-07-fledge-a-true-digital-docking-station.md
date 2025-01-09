---
layout: post
title: Fledge - A true digital docking station!
date: 2025-01-07 16:49 +0000
categories: [iot, programming]
---
# Fledge - What is it?
Fledge is what I like to describe the closest thing you get in the IoT field as a docking station, or an USB hub, why? Fledge takes multiple types of inputs and shares it to a more "known/supported" interface, although this is just one of its multiple yet simple features.

<a href="/assets/img/posts/dock-station.jpg" style='display: flex; justify-content: center' data-lity>
  <img src="/assets/img/posts/dock-station_thumbnail.jpg"/>
</a>

Additionally, Fledge is an open source data platform that enables and accelerates interconectivity of assets on the "edge' (assets, sensors, actuators) with backend IT systems. 

## Why do we need a "Docking Station" in IoT?
As it turns out, much of the work of IoT projects involves getting information that:
1. Lives disconnected from the business (aka **Data Silos**)
2. Needs to be created, and properly contextualized and shared, at an affordable cost and on a timely manner! ;).

To achieve our objectives we need: 
* Ensure **at least** one-way communication to extract data from an asset, per each protocol.
* Health checks.
* Management of devices.

#### An attempt to describe of a "minimal" IoT project

<a href="/assets/img/posts/iot-arch.jpg" style='display: flex; justify-content: center' data-lity>
  <img src="/assets/img/posts/iot-arch.jpg"/>
</a>


While this seems simple, scalability tends to be a problem, turns out the real world generates a lot of information. Plus, not all information is created equally, enter the industrial protocols!

This is what we inherit from previous automation eras. Manufacturers of PLCs and Industrial equipment came up with their own ways to share information across their equipment. Oh well. 

As you can see already, there's a bit of overhead **before** we start providing business value, which is one of the reasons why IoT is complex in the IT industry. 

Zooming out of those reasons, that's why I believe a docking station is a **must** in the IoT industry. We need something that takes all these protocols, allows us to operate the data, and push it into the nice IT world, where we have solutions for *almost* everything :p. 

Imagine doing this per each combination of protocols, assets, it would be too much overhead, ideally we need a platform that enables us to accomplish as many of the common features in IoT projects, so we (as developers) focus on bringing **actual business value**. 

## Fledge
<a href="/assets/img/posts/fledge.png" style='display: flex; justify-content: center' data-lity>
  <img src="/assets/img/posts/fledge.png"/>
</a>

After that long intro, let me present you Fledge, an **open source, edge-oriented, IoT platform**.
I believe their vision sums up pretty well what they aim to achieve:
{% quote cite="Fledge's vision"
   url="https://lfedge.org/projects/fledge/"
%}
  Fledge’s project and community deliver data management for automation, advanced process control and IIoT, accelerating global smart manufacturing / Industry 4.0. As manufacturing becomes more data driven, the convergence of OT’s & IT’s people, processes and technology gains greater importance...
{% endquote %}

Additionally, Fledge is very well supported, if you noticed the link I included goes to Linux Foundation Edge, marked as an "Impact Project". Big name contributors included. 

Why I *personally* believe that Fledge is a good choice as an IoT accelerator, based on my (biased and limited experience) I've noticed that IoT platforms try to do **too much**. 

In Spanish we say "el que mucho abarca poco aprieta", something among the lines of "You should not bite off more than you can chew". 

This tends to make platforms grow in size and scope, hurting performance and developer experience, which in turn creates customer frustration, and ultimately hurting the project's success possibilities. We can see this as:
{% quote cite="Software AG - Why IoT projects fail"
   url="https://www.softwareag.com/en_corporate/resources/iot/wp/why-iot-projects-fail.html"
%}
60% of those involved in both successful and unsuccessful IoT projects said that they had problems with scalability
{% endquote %} 

{% quote cite="Software AG - Why IoT projects fail"
   url="https://www.softwareag.com/en_corporate/resources/iot/wp/why-iot-projects-fail.html"
%}
60% of respondents with successful IoT initiatives engaged outside vendors
{% endquote %} 

{% quote cite="Software AG - Why IoT projects fail"
   url="https://www.softwareag.com/en_corporate/resources/iot/wp/why-iot-projects-fail.html"
%}
70% of respondents struggle to integrate IoT solutions into existing workflows
{% endquote %} 

### How Fledge addresses this?
In my view Fledge tackles these issues by: 
* Open source (no vendor lock in!)
* Clearly defined scope - **data management and processing of different sources and destinations**
* Low resource usage
* Built with management in mind (deployment of projects, integrated management API)
* **Enables plug-in development for third party systems**

## Closing thoughts
I believe developer experience needs to be as streamlined as possible, developer experience makes it easier to ship and mantain features. In *my opinion* developing in this platform is simple which becomes comfortable, there's a learning curve but I will try to explain it later on this Fledge series, to make it easier for you in order to get started!

Additionally, the fact that you can literally just start experimenting with it gives you the oportunity to actually learn how IoT can be done in a production project, it is one thing to work with your RPI or your ESP32, when doing IoT you will probably be managing hundreds of these devices and processing their information. One very sad truth is that a lot of IoT platforms are closed source, if you as a junior developer, interested in IoT cannot get access to big player platforms on the market, how are you supposed to kickstart your career as a IoT developer? Well you could start with Fledge.

On my next article I will show you how Fledge can be used as an IoT accelerator for both IoT at home and later on business cases.

As always, any comments or questions are welcome, just contact me through the listed media.

