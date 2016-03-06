# Home-Automation-Hub-Design
This is a description of my Node-Red, Raspberry Pi based home automation hub

I'm afraid this isn't (currently) a set of code to create an HA hub. I can't quite yet work out whether that would expose too much sensitive information. Instead it was suggested that I create a set of screen-shots to illustrate my HA Hub.

## The basic hardware and software
The hub runs on a Raspberry Pi 2. The Pi also runs Mosquitto for an MQTT broker and InfluxDB with Grafana for recording timeseries data such as sensor data and then creating a dashboard with charts ([Example](https://www.google.com/url?q=https%3A%2F%2Fsnapshot.raintank.io%2Fdashboard%2Fsnapshot%2FzhDlu2zmRADWjSyUIE36S1JzFqdbJolk&sa=D&sntz=1&usg=AFQjCNFn3rcyGvHWXUgCeviFy2TOFeShgg)).

## The interfaces
The system interfaces with a number of external devices. I currently have 4 I/O channels. 

1. An RFXtrx433E which is a 433MHz transceiver that listens/talks to a wide variety of commerical kit including Oregon Scientific, LightwaveRF, HomeEasy and Nexa devices that provide sensors and controllers, remote switches and even a front door bell. 
2. In addition, I have some sensor platforms built mainly around Arduino's. I tend to use [Ciesco](https://www.wirelessthings.net/) wireless devices with the Arduino's and have a Ciesco SRF based transceiver on the Pi as well.
3. I have one Arduino connected direct via USB serial.
4. I have an Edimax Wi-Fi Smartswitch and I'm very likely to use [ESP8266](esp8266.com) based Wi-Fi devices in the future. These have the advantage of range and cheapness though at the cost of a higher power consumption.

OF course, I have added various network-based inputs too such as monitors for key servers (via ping) and I am currently reworking my weather inputs that come from the Internet.

I also have some Internet based outputs such as IFTTT, Pushbullet and EmonCMS.

## The basic flow of data
```
Input from hardware
  MQTT publish to HARDWARE-IN/<device-id>
    Split into commands and sensor messages (some inputs like PIR's might be both)

    MQTT Publish sensor msgs to SENSORS/<physical-sensor-location>/<device-id>
      Split the sensor details into individual readings and republish to individual sensor types such as TEMPERATURE/<physical-sensor-location>/<device-id>, HUMIDITY/#, etc.

    Translate incoming command msgs (e.g. from remote controls, etc) to COMMAND/# MQTT msgs
      Listen for COMMAND/# msgs and translate into specific control msgs and forward to the output controllers.
```
Whilst this is a little complex, it decouples all of the inputs and outputs. As incoming data is received, it is normalised and enhanced with metadata (e.g. timestamps, sensor types, sensor locations, etc.). Having the MQTT outputs in a variety of formats allows maximum use of the data with minimal programming.

## The UI
I currently have some web pages that I created myself which use my own templates, I've explained this in a couple of other posts on flows so I won't repeat that here. I've also started to use `node-red-contrib-ui`, not shown here. That will likely replace my custom pages as it is likely to require less maintenance. All my pages are mobile friendly.

## Security
Note that currently, there is minimal security turned on so I don't allow any of the pages nor the MQTT traffic to go outside my local network. HTTPS with passwords for the admin interface would be an absolute minimum before I would allow access from the Internet. Even then, I would much prefer to have a separate set of web pages that access the MQTT data only on the server and that then push filtered information out to the Internet. I'll probably set this up via my NAS at some point as that already has a full web server that is already secured for the web.

## Screenshots
In lieu of some actual code (for now), here are some screen-shots to illustrate how things fit together. Please feel free to get in touch via the Google Group or my GitHub account, I'm happy to answer questions as best I can.
