---
layout: page
title: "MQTT Sensor"
description: "Instructions how to integrate MQTT sensors within Home Assistant."
date: 2015-05-30 23:21
sidebar: true
comments: false
sharing: true
footer: true
logo: mqtt.png
ha_category: Sensor
ha_release: 0.7
ha_iot_class: depends
---


This `mqtt` sensor platform uses the MQTT message payload as the sensor value. If messages in this `state_topic` are published with *RETAIN* flag, the sensor will receive an instant update with last known value. Otherwise, the initial state will be undefined.

To use your MQTT sensor in your installation, add the following to your `configuration.yaml` file:

```yaml
# Example configuration.yml entry
sensor:
  - platform: mqtt
    state_topic: "home/bedroom/temperature"
```

Configuration variables:

- **state_topic** (*Required*): The MQTT topic subscribed to receive sensor values.
- **name** (*Optional*): The name of the sensor. Default is 'MQTT Sensor'. 
- **qos** (*Optional*): The maximum QoS level of the state topic. Default is 0.
- **unit_of_measurement** (*Optional*): Defines the units of measurement of the sensor, if any.
- **expire_after** (*Optional*): Defines the number of seconds after the value expires if it's not updated. Default is 0 (=never expire).
- **value_template** (*Optional*): Defines a [template](/topics/templating/) to extract a value from the payload.

## {% linkable_title Examples %}

In this section you find some real life examples of how to use this sensor.

### {% linkable_title Get battery level %}

If you are using the [Owntracks](/components/device_tracker.owntracks/) and enable the reporting of the battery level then you can use a MQTT sensor to keep track of your battery. A regular MQTT message from Owntracks looks like this: 

```bash
owntracks/tablet/tablet {"_type":"location","lon":7.21,"t":"u","batt":92,"tst":144995643,"tid":"ta","acc":27,"lat":46.12}
```

Thus the trick is extract the battery level from the payload.

```yaml
# Example configuration.yml entry
sensor:
  - platform: mqtt
    state_topic: "owntracks/tablet/tablet"
    name: "Battery Tablet"
    unit_of_measurement: "%"
    value_template: {% raw %}'{{ value_json.batt }}'{% endraw %}
```

### {% linkable_title Get temperature and humidity %}

If you are using a DHT sensor and a NodeMCU board (esp8266), you can retrieve temperature and humidity with a MQTT sensor. A code example can be found [here](https://github.com/mertenats/open-home-automation/tree/master/ha_mqtt_sensor_dht22). A regular MQTT message from this example looks like this: 

```json
office/sensor1
  {
    "temperature": 23.20,
    "humidity": 43.70
  }
```

Then use this configuration example to extract the data from the payload:

```yaml
# Example configuration.yml entry
sensor:
  - platform: mqtt
    state_topic: 'office/sensor1'
    name: 'Temperature'
    unit_of_measurement: '°C'
    value_template: {% raw %}'{{ value_json.temperature }}'{% endraw %}
  - platform: mqtt
    state_topic: 'office/sensor1'
    name: 'Humidity'
    unit_of_measurement: '%'
    value_template: {% raw %}'{{ value_json.humidity }}'{% endraw %}
```
