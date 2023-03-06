---
title: Architecture
layout: default
parent: Overview
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
The Application Box is comprised of three services, the MQTT Broker, the Application Server, and the Database Server. Each of these three services can be hosted on the same computer inside a firewall or on the cloud, or each service can be hosted on its own computer inside a firewall or on the cloud. For reliability and ease of installation, the preferred method is to have each service hosted own its own machine in the cloud.
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
    The Application Server routes the messages from the message trays  to the database server for status update and archival. 
  - <ins>User application server</ins>   
    The Application Server also hosts the user applications. The Application Server checks user requests via role-based access routing and and routes the requests to the appropriate message trays and database server. The application server is written using the [Node-RED] programming environment.  
    Node-RED makes it easy: 
    - to read, edit, and document the code 
    - for the user to change the code
    - to version control the code
    - to port the code to other machines
    <p align = "center"><img src = "/assets/images/nodeRedEnv.png"></p>
    <p align = "center">Fig. 2 Node-RED Programming Environment</p>

- ### The Database Server
  Instead of using name-value pairs for describing the data as with most other control system platforms, **Blinky-Lite<sup>TM</sup>** uses a class structure to describe systems in the message trays. This structure makes communicating between subsystems and data archival much more streamlined and easy to read and modify. The data in the class structure is stored in [JSON] objects which is a common lightweight data-interchange format ideal for communicating with mobile browser-based web applications. Most of the data in the control system is time-sequenced so a non-SQL type of database such as [MongoDB] or [RethinkDB] is much easier to configure and use. Currently, **Blinky-Lite<sup>TM</sup>** is configured to interface smoothly with a MongoDB database.[^1] 

  The database is divided into collections of JSON object documents to not only handle data archiving but other services as well such as access logs and app configurations. The list of collections in a **Blinky-Lite<sup>TM</sup>** project is:
  - <span style="color:#ff6100">***trays***</span> - tray configuration
  - <span style="color:#ff6100">***archiver***</span> - tray data archiving
  - <span style="color:#ff6100">***users***</span> - list of users, their credentials, and their roles
  - <span style="color:#ff6100">***appConfig***</span> - configurations for the standard applications
  - <span style="color:#ff6100">***appStyleSheets***</span> - custom CSS sheets for the standard applications
  - <span style="color:#ff6100">***accessLog***</span> - record of all visits to the project
  - <span style="color:#ff6100">***settingsLog***</span> - record of all tray settings made in the project
  - <span style="color:#ff6100">***restAccessLog***</span> - record of all RESTful interface visits to the project

## Message Trays
The concept of message trays is what makes **Blinky-Lite<sup>TM</sup>** truly versatile. The message trays communicate to a device using whatever protocol and hardware is supported by the device, such as Modbus, serial (UART, SPI, I2C, etc.), RS232, MQTT, etc. The message tray packages the device information into a **Blinky-Lite<sup>TM</sup>** Tray JSON object in a standard format that is discussed in [Need REF!]. 

The tray message is then published to the Application Box MQTT broker.  Since the Application Server subscribes to all the message tray topics in a **Blinky-Lite<sup>TM</sup>** project, the Application  Server will  receive this message. In this way, the applications on the Application Server can be standardized and reused from project to project. Also, any other tray that subscribes to the message tray's unique topic can also receive the tray's JSON object. 

The message tray can also subscribe to other message tray topics or messages sent from the Application Server. These messages can the be used to alter the state of the tray or devices the tray is connected to. As shown in Figure 1, more than one device can be connected to a single tray. Thus, the tray can function as a micro-control system in a truly Edge computing manner.

Each message tray runs as a separate Node-RED flow. Usually, there is only a single tray flow running on a single Node-RED process running on a microcomputer such as a [Raspberry Pi]. However, it is also possible to run multiple tray flows on a single Node-RED instance running on a computer other than a Raspberry Pi, such as computer in the cloud. Local and cloud trays are discussed in [Need Ref!]

## Cube Controllers
Cube controllers are not necessarily required in a **Blinky-Lite<sup>TM</sup>** project. For example, if the device is a Modbus device, then the tray can be directly connected to the device. An example ModBus tray can be found in [Need Ref!]. If the device is, for example, a stepper motor, then some hardware is required to interface the motor to translate tray commands. This is usually done through a micro-controller such as an [Arduino], [Teensy], or [Raspberry Pi Pico]. We have developed a number of serial and MQTT interfaces to micro-controllers that can be found at [Needs Ref!].

----
[^1]: MongoDB is not open-source software so the user must provide their own instance of a MongoDB database server.

[MQTT]:https://mqtt.org/
[Node-RED]:https://nodered.org/
[JSON]:https://www.json.org/json-en.html
[MongoDB]:https://www.mongodb.com/
[RethinkDB]:https://rethinkdb.com/
[Raspberry Pi]:https://www.raspberrypi.org/
[Arduino]:https://www.arduino.cc/
[Teensy]:https://www.pjrc.com/teensy/
[Raspberry Pi Pico]:https://www.raspberrypi.com/products/raspberry-pi-pico/
