---
categories:
- Everyday
date: "2021-04-11T12:58:34Z"
meta: null
status: publish
tags: []
title: Using Grafana to monitor power usage on Kamstrup Power meter
---

Finally got a chance to write this long overdue blog post about getting the Kamstrup meter data into InfluxDB and graphing it using Grafana.

I lost the instance of HA, where I had the previous Grafana installation and all the data, so my current setup has very limited data at the moment (Something something about real men and backups :smirk:).


![<img src="https://xipher.dk/static/assets/images/2021-04-11-23-08-53.png">](https://xipher.dk/static/assets/images/2021-04-11-23-08-53.png)

Be warned this is a work in progess :smiley:

A little bit of background, I am currently running all my home automations in Home Assistant(HA), and I wanted this to be part of HA as well, this will not be a full guide on how to install Add-Ons in Home Assistant, but more on setting up the integrations inside of HA.

If you do not already have an MQTT broker you need to install one, I am using the *Mosquitto broker* that is available as an add-on, when you have installed it, you need to setup a user in HA that will have access to post messages to the MQTT queue, the Add-On documentation for this is very good. (I am running Hass.io on a Raspberry PI 4, so very easy to install via Supervisor)

Next things to install are the InfluxDB and Grafana Add-ons, these 2 require minimal configuration in HA.

My primary system is Windows so in order to test that MQTT works I am using *MQTT Explorer* from http://mqtt-explorer.com/ which I find very usefull when troubleshooting and testing, since you can both see incoming values and inject data.

[![MQTT Explorer](https://xipher.dk/static/assets/images/2021-04-11-23-27-10.png)](https://xipher.dk/static/assets/images/2021-04-11-23-27-10.png)

At this point I had to make a decision, since Grafana cannot graph data directly from MQTT, you have to decide on how to get the data from MQTT into InfluxDB, and there are multiple ways to do this. In my initial HA setup from last year, I setup an instance of Telegraf (https://www.influxdata.com/time-series-platform/telegraf/) to get data from MQTT into InfluxDB, this worked really well, the "downside" of this, is that the data is not directly available inside of Home Assistant, only in InfluxDB/Grafana

A similar solution is to use Node Red to read the MQTT message queue and push the data into InfluxDB, this also works fine, but again you do not have the data available directly inside HA.

So I decided to try, and configure HA to use InfluxDB to store its metric data, and then create *sensors*  for all the data points coming from the Kamstrup Meter

First thing I had to do, was configure HA to use influxDB, this can be done by adding something like this to the configuration.yaml file (You have to create a DB/User inside of InfluxDB before this)

```
influxdb:
  host: localhost
  port: 8086
  database: home-assistant
  username: homeassistant
  password: VerySecurePasswordGoesHere
  max_retries: 3
  default_measurement: state
```

Then you have to restart HA, and you should start seeing data flowing into InfluxDB

Then it was time to create the sensors inside of HA, this can be done on MQTT topics, for the purpose of testing I added **all the things**, you might want to remove the ones you do not care about, to preserve resources. (Particularly if you do not produce energy yourself, there are several topics you can ignore)

[![](/assets/images/2021-04-11-23-53-24.png)](https://xipher.dk/static/assets/images/2021-04-11-23-53-24.png)

Here you can see the *activePower**Minus*** values are **0**, because I do not produce any power

Here is a sensor config that incluces everything, this can also be found [here](https://github.com/Claustn/esp8266-kamstrup-mqtt/blob/master/Home_Assistant/sensors.yml)

```
sensor:  
  - platform: mqtt
    name: "Kamstrup activePowerPlus "
    unit_of_measurement: "watts"
    state_topic: "kamstrup/power/activePowerPlus"
  - platform: mqtt
    name: "Kamstrup activePowerMinus "
    unit_of_measurement: "watts"
    state_topic: "kamstrup/power/activePowerMinus"
  - platform: mqtt
    name: "Kamstrup activePowerPlusL1 "
    unit_of_measurement: "watts"
    state_topic: "kamstrup/power/activePowerPlusL1"
  - platform: mqtt
    name: "Kamstrup activePowerMinusL1 "
    unit_of_measurement: "watts"
    state_topic: "kamstrup/power/activePowerMinusL1"
  - platform: mqtt
    name: "Kamstrup activePowerPlusL2 "
    unit_of_measurement: "watts"
    state_topic: "kamstrup/power/activePowerPlusL2"
  - platform: mqtt
    name: "Kamstrup activePowerMinusL2 "
    unit_of_measurement: "watts"
    state_topic: "kamstrup/power/activePowerMinusL2"
  - platform: mqtt
    name: "Kamstrup activePowerPlusL3 "
    unit_of_measurement: "watts"
    state_topic: "kamstrup/power/activePowerPlusL3"
  - platform: mqtt
    name: "Kamstrup activePowerMinusL3 "
    unit_of_measurement: "watts"
    state_topic: "kamstrup/power/activePowerMinusL3"
  - platform: mqtt
    name: "Kamstrup reactivePowerPlus "
    unit_of_measurement: "watts"
    state_topic: "kamstrup/power/reactivePowerPlus"
  - platform: mqtt
    name: "Kamstrup reactivePowerMinus "
    unit_of_measurement: "watts"
    state_topic: "kamstrup/power/reactivePowerMinus"
  - platform: mqtt
    name: "Kamstrup powerFactorL1 "
    unit_of_measurement: "%"
    state_topic: "kamstrup/power/powerFactorL1"
  - platform: mqtt
    name: "Kamstrup powerFactorL2 "
    unit_of_measurement: "%"
    state_topic: "kamstrup/power/powerFactorL2"
  - platform: mqtt
    name: "Kamstrup powerFactorL3 "
    unit_of_measurement: "%"
    state_topic: "kamstrup/power/powerFactorL3"
  - platform: mqtt
    name: "Kamstrup powerFactorTotal "
    unit_of_measurement: "%"
    state_topic: "kamstrup/power/powerFactorTotal"
  - platform: mqtt
    name: "Kamstrup L1 "
    unit_of_measurement: "V"
    state_topic: "kamstrup/voltage/L1"
  - platform: mqtt
    name: "Kamstrup L2 "
    unit_of_measurement: "V"
    state_topic: "kamstrup/voltage/L2"
  - platform: mqtt
    name: "Kamstrup L3 "
    unit_of_measurement: "V"
    state_topic: "kamstrup/voltage/L3"
  - platform: mqtt
    name: "Kamstrup L1 "
    unit_of_measurement: "A"
    state_topic: "kamstrup/current/L1"
  - platform: mqtt
    name: "Kamstrup L2 "
    unit_of_measurement: "A"
    state_topic: "kamstrup/current/L2"
  - platform: mqtt
    name: "Kamstrup L3 "
    unit_of_measurement: "A"
    state_topic: "kamstrup/current/L3"
  - platform: mqtt
    name: "Kamstrup activeImportKWh "
    unit_of_measurement: "KWh"
    state_topic: "kamstrup/energy/activeImportKWh"
  - platform: mqtt
    name: "Kamstrup activeExportKWh "
    unit_of_measurement: "KWh"
    state_topic: "kamstrup/energy/activeExportKWh"
  - platform: mqtt
    name: "Kamstrup activeImportKWhL1 "
    unit_of_measurement: "KWh"
    state_topic: "kamstrup/energy/activeImportKWhL1"
  - platform: mqtt
    name: "Kamstrup activeExportKWhL1 "
    unit_of_measurement: "KWh"
    state_topic: "kamstrup/energy/activeExportKWhL1"
  - platform: mqtt
    name: "Kamstrup activeImportKWhL2 "
    unit_of_measurement: "KWh"
    state_topic: "kamstrup/energy/activeImportKWhL2"
  - platform: mqtt
    name: "Kamstrup activeExportKWhL2 "
    unit_of_measurement: "KWh"
    state_topic: "kamstrup/energy/activeExportKWhL2"
  - platform: mqtt
    name: "Kamstrup activeImportKWhL3 "
    unit_of_measurement: "KWh"
    state_topic: "kamstrup/energy/activeImportKWhL3"
  - platform: mqtt
    name: "Kamstrup activeExportKWhL3 "
    unit_of_measurement: "KWh"
    state_topic: "kamstrup/energy/activeExportKWhL3"
  - platform: mqtt
    name: "Kamstrup reactiveImportKWh "
    unit_of_measurement: "KWh"
    state_topic: "kamstrup/energy/reactiveImportKWh"
  - platform: mqtt
    name: "Kamstrup reactiveExportKWh "
    unit_of_measurement: "KWh"
    state_topic: "kamstrup/energy/reactiveExportKWh"
```
Doing it this way means that all the Kamstrup meter data will show up as sensors in HA, and you can use them in your automations

[![](/assets/images/2021-04-11-23-41-28.png)](https://xipher.dk/static/assets/images/2021-04-11-23-41-28.png)


I published the grafana Dashboard json [here](https://github.com/Claustn/esp8266-kamstrup-mqtt/blob/master/Grafana/power_usage_info.json)


I am looking forward to testing this some more, to see how stable it is... (I have heard of people having issues with too many MQTT based sensors, but will let it run for a while and report back :smile:)
