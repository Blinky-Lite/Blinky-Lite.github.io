---
title: Architecture
layout: default
parent: Introduction
nav_order: 2
---
# Blinky-Lite<sup>TM</sup> Architecture

<details open markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
- TOC
{:toc}
</details>

The **Blinky-Lite<sup>TM</sup>** Architecture consists of three major components: 
- Application Box
- Message Trays
- Controller Cubes

<p align = "center"><img src = "/assets/images/architecture.jpg"></p>
<p align = "center">Fig. 1 Blinky-Lite Architecture</p>

## The Application Box
The Application Box is comprised of three services:
- ### The MQTT Broker  
  The MQTT broker provides:.
  - <ins>Communication exchange</ins>   
    The communication protocol for **Blinky-Lite<sup>TM</sup>** is [MQTT]. MQTT uses a publish-subscribe paradigm. The MQTT Broker relays messages between message trays and the Application Server. For prompt communications, the MQTT broker can also relay messages directly between message trays without having to go through the Application Server 
  - <ins>Data Pooling</ins>  
    Because of the publish-subscribe paradigm, The MQTT broker effectively protects the message trays from being overloaded from too many communication requests from the Application Server.
  - <ins>Security</ins>  
    The MQTT broker acts an additional layer of security because both the message trays and Application Server must authenticate to the MQTT broker to pass messages. Since the message tray must initiate the connection to the MQTT Broker, this eliminates the need for external SSH tunnels into your private network for remote access. Finally, the MQTT Broker can be easily configured to only accept specific and unique topics to and from each message tray adding another layer of security.
- ### The Application Server
  The Application Server tasks are:
  - <ins>Alarm Scanning</ins>
    The Application Server collects messages from the message trays via the MQTT Broker scans for alarms and routes these alarms to user alarm applications and the SMS messenger
  - <ins>Data Archival</ins>
    The Application Server routes the messages from the message trays  to the database server for status update and archival. The Application Server also hosts the user applications. the Application Server checks user requests via role-based access routing and and routes the requests to the appropriate message trays and database server.
- ### The MongoDb Database Server

Each of these three services can be hosted on the same computer inside a firewall or on the cloud, or each service can be hosted on its own computer inside a firewall or on the cloud. For reliability and ease of installation, the preferred method is to have each service hosted own its own machine in the cloud. We recommend the following providers:
- [Cloud MQTT] for the MQTT Broker
- [Heroku] for the Application Server
- [MongoDb Atlas] for the MongoDb Database Server

----
[Cloud MQTT]:https://www.cloudmqtt.com/
[Heroku]:https://www.heroku.com/
[MongoDb Atlas]:https://www.mongodb.com/atlas/database
[MQTT]:https://mqtt.org/