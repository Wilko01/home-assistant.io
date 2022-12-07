---
title: Daikin AC
description: Instructions on how to integrate Daikin AC devices with Home Assistant.
ha_category:
  - Climate
  - Energy
  - Sensor
  - Switch
ha_release: 0.59
ha_iot_class: Local Polling
ha_config_flow: true
ha_quality_scale: platinum
ha_codeowners:
  - '@fredrike'
ha_domain: daikin
ha_zeroconf: true
ha_platforms:
  - climate
  - sensor
  - switch
ha_integration_type: integration
---

<p class='note warning'>
  Daikin has removed their local API in newer products. They offer a cloud API accessible only under NDA, which is incompatible with open source. This affects units fitted with the BRP069C4x wifi adapter. Units listed under Supported Hardware below continue to have access to local control. Additionally the older but commonly available BRP072A42 adapter can be fitted to most if not all newer units for access to local control.
  Note: The newest (manufacturing date 2022) Daikin Stylish FTXA25A2V1BW and the FTXA35B2V1BE can easily be added via the integration and are fully supported.
</p>

The `daikin` integration integrates Daikin air conditioning systems into Home Assistant.

There is currently support for the following device types within Home Assistant:

- [Climate](#climate)
- [Sensor](#sensor)
- [Switch](#switch)

## Supported hardware

- The European versions of the Wifi Controller Unit (BRP069A41, 42, 43, 45), which is powered by the [Daikin Online Controller](https://play.google.com/store/apps/details?id=eu.daikin.remoapp) application. The new version of WiFi Controller Unit BRP069Bxx is also confirmed to work, tested and working devices are the BRP069B41 and BRP069B45.
- The Daikin Stylish FTXA25A2V1BW and the FTXA35B2V1BE are fully supported and is using the successor of the Daikin Online Controller named [ONECTA](https://play.google.com/store/apps/details?id=com.daikineurope.online.controller&hl=en). Confirmed working with modes: auto / heat / cool / de-humidify / fan, temperature control, power on / off, streamer and lots of sensors: compressor frequency, cool energy consumption, estimated power consumption, heat energy consumption, humidity, inside temperature, outside temperature, target humidity, today's total energy consumption.
- The Australian version of the Daikin Wifi Controller Unit BRP072A42, which is operated by the [Daikin Mobile Controller (iOS)](https://itunes.apple.com/au/app/daikin-mobile-controller/id917168708?mt=8) ([Android](https://play.google.com/store/apps/details?id=ao.daikin.remoapp)) application. Confirmed working on a Daikin Cora Series Reverse Cycle Split System Air Conditioner 2.5kW Cooling FTXM25QVMA with operation mode, temp, fan swing (3d, horizontal, vertical).
  - BRP072Cxx based units (including Zena devices)*.
- The United States version of the Wifi Controller Unit (BRP069A43), which is powered by the [Daikin Comfort Control](https://play.google.com/store/apps/details?id=us.daikin.comfortcontrols) application. Confirmed working on a Daikin Wall Units FTXS09LVJU, FTXS15LVJU, FTXS18LVJU and a Floor Unit FVXS15NVJU with operation mode, temp, fan swing (3d, horizontal, vertical).
- The Australian version of the Daikin Wifi Controller for **AirBase** units (BRP15B61), which is operated by the [Daikin Airbase](https://play.google.com/store/apps/details?id=au.com.daikin.airbase) application.
- **SKYFi** based units, which is operated by the SKYFi application*.

<div class='note'>

- The integration for BRP072Cxx and SKYFi based units need API-key / password respectively. The API-key/password can be found on a sticker under the front cover. The other models are auto detected and the API-key and password field must be left empty.
  
- The Daikin Stylish FTXA25A2V1BW and the FTXA35B2V1BE can be installed by just adding the integration and add the IP address of the AC inside unit. If there are multiple inside units, just add another integration with the IP address.
</div>

{% include integrations/config_flow.md %}

<div class='note'>
  
If your Daikin unit does not reside in the same network as your Home Assistant instance, i.e. your network is segmented, note that a couple of UDP connections are made during discovery:

- From Home Assistant to the Daikin controller: `UDP:30000` => `30050` or for the 2022 year model `TCP:80`
- From the Daikin controller to Home Assistant: `UDP:<random port>` => `30000`

If this situation applies to you, you may need to adjust your firewall(s) accordingly.

</div>

## Climate

The `daikin` climate platform integrates Daikin air conditioning systems into Home Assistant, enabling control of setting the following parameters:

- [**set_hvac_mode**](/integrations/climate/#service-climateset_hvac_mode) (`off`, `heat`, `cool`, `heat_cool`, or `fan_only`)
- [**target temperature**](/integrations/climate#service-climateset_temperature)
- [**turn on/off**](/integrations/climate#service-climateturn_on)
- [**fan mode**](/integrations/climate#service-climateset_fan_mode) (speed)
- [**swing mode**](/integrations/climate#service-climateset_swing_mode)
- [**set_preset_mode**](/integrations/climate#service-climateset_preset_mode) (away, none)

Current inside temperature is displayed.

<div class='note'>
  
Some models do not support setting of **fan speed** or **swing mode**.
  
</div>

<div class='note'>

Preset mode **away** translates to Daikin's "Holiday Mode":<br/>
<br>
_"Holiday mode" is used when you want to turn off your units when you leave you home for a longer time._<br>
<br>
_When "Holiday mode" is enabled, the following action take place:_

- _All connected units are turned OFF._
- _All schedule timers are disabled._

</div>

## Sensor

The `daikin` sensor platform integrates Daikin air conditioning systems into Home Assistant, enabling displaying the following parameters by device:

- Inside temperature
- Inside humidity
- Hourly energy consumption in cool mode
- Hourly energy consumption in heat mode
- Today's total energy consumption (updated hourly, resets at 00:00)

The integration displays the following parameters for the outdoor compressor:

- Outside temperature
- Outside compressor Estimated power consumption (sum of all devices)
- Outside compressor Energy consumption (sum of all devices, resets at 00:00)
- Outside compressor frequency

<div class='note'>

- Some models only report outside temperature when they are turned on.
- Some models do not have humidity sensor.
- Some models do not report the power/energy consumption.
- Some models do not report the compressor frequency.

</div>

<div class='note'>

- The 'Outdoor compressor Energy consumption' and 'Outdoor compressor Estimated power consumption' sensors are updated every time 100 Wh are consumed by all different operating modes summed together.
- The 'Outdoor compressor Estimated power consumption' sensor is derived from the above energy consumption and not provided by the AC directly.
- The 'cool/heat' energy sensors are updated hourly with the previous hour energy consumption
  of a given mode and a given AC.
- The 'cool' mode also includes the 'fan' and 'dehumidifier' modes' power consumption.
- If you have multiple indoor devices, the 'Outdoor compressor' sensors will be created multiple times but will all report the same values. You can disable all but one.

</div>

## Switch

AirBase and SKYFi units exposes zones (typically rooms) that can be switched on/off individually.

<div class='note'>

Zones with the name `-` will be ignored, just as the AirBase application is working.

</div>

Additionally the Daikin Streamer (air purifier) function can be toggled on supported devices using a switch. Note that it isn't currently possible to reliably detect whether a specific device has streamer support, so the switch may appear in the UI even if the functionality isn't actually supported.

## Region Changing

The European and United States controllers (Most likely the Australian controllers too) have an HTTP API endpoint that allows you to change the controllers region so that other regional apps can be used. (Sometimes these controllers get exported to regions that can not download the app for the controllers region.)

`http://Daikin-IP-Address/common/set_regioncode?reg=XX` Replace XX with your region code of choice.

Currently known region codes:

- AU
- EU
- JP
- US
- TH

If you experience problems with certain apps like the Daikin ONECTA try setting a lower-case region code (e.g. 'eu').
