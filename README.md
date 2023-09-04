# esphome-config-sprinkler

## Overview

**[ESPHome-based](https://esphome.io/) irrigation controller for two zones,
single pump and up to 6 valves**

The controller is capable of working without persistent connection with [Home
Assistant](https://www.home-assistant.io/). That is, Home Assistant is only
used as the UI (and possibly automations) but does not perform actual
operations including scheduling and valve management.

## Motivation

Looking for alternatives to replace old K-Rain offline controller author found
8 relay controller for Raspberry Pi Pico and decided to use it for the new
controller.

In fact, there are multiple irrigation controller implementations for ESPHome
and Home Assistant - the implementation is intended to be simple enough and
introduce no new UI controlling it, as well as requiring no persistent
connectivity with Home Assistant. The Home Assistant instance could even be
coming online only when changes to controller configuration are needed.

## Controller configuration

* Two zone groups
  * Lawn with 3 zones
  * Flowerbed with 2 zones
* Single pump with water tank
* Valve on the inlet pipe to refill the tank

Optionally you could add LCD display to see controller status and RTC to
provide accurate clock.

## Home Assistant entities

Following Home Assistant entities are exposed by the controller:

![Home Assistant config](/docs/assets/controller-hass-config-1.jpg) ![Home Assistant config](/docs/assets/controller-hass-config-2.jpg) ![Home Assistant config](/docs/assets/controller-hass-config-3.jpg)

![Home Assistant controls](/docs/assets/controller-hass-controls.jpg)

![Home Assistant diagnostics](/docs/assets/controller-hass-diagnostics.jpg)

![Home Assistant sensors](/docs/assets/controller-hass-sensors.jpg)

## Software

* [ESPHome](https://esphome.io/) 
 * [Sprinkler component](https://esphome.io/components/sprinkler) is the key to the functionality
 * [Dynamic 'on-time' component](https://github.com/hostcc/esphome-component-dynamic-on-time) is
   to allow changing schedules without recompiling the firmware (as with
   [`on_time` trigger](https://esphome.io/components/time/#time-on-time) for
   time components)

## Hardware

**NOTE 1** Author is not affiliated with the hardware vendors below, that is what I
used to build the controller.

**NOTE 2** There are no strict requirements to use Raspberry Pi Pico -
ESP8266/ESP32 would also work given it has sufficient GPIOs via an extender and
configuration modifications to change GPIO pins.

* [Raspberry Pi Pico W](https://www.raspberrypi.com/products/raspberry-pi-pico/)
* [Industrial 8-Channel Relay Module for Raspberry Pi Pico (Pico-Relay-B)](https://www.waveshare.com/pico-relay-b.htm)
* A 5V power supply, Meanwell HDR-15-5 (5V, 2.4A, DIN rail mounted) has been
  used 
* Irrigation (valves, pipe, pump, water tank) - likely very specific to this
  implementation, though the solution could be adjusted accordingly

Optionally:
* A DS3231 RTC. DS1307 would also work but has lower accuracy and is a 5V unit
  (most of Raspberry Pi Pico pins are 3.3V)
* A PCD8544 display

### GPIO layout

|GPIO  | Purpose                           |[In stock Pico-Relay-B](https://www.waveshare.com/wiki/Pico-Relay-B)|
|------|-----------------------------------|:-:|
|GPIO21| Relay 1 (lawn, south zone)        | Y |
|GPIO20| Relay 2 (lawn, north zone)        | Y |
|GPIO19| Relay 3 (lawn, west zone)         | Y |
|GPIO18| Relay 4 (flowerbed, south zone)   | Y |
|GPIO17| Relay 5 (flowerbed, west zone)    | Y |
|GPIO16| Relay 6 (not used)                | Y |
|GPIO15| Relay 7 (water tank refill valve) | Y |
|GPIO14| Relay 8 (water pump)              | Y |
|GPIO13| RGB LED                           | Y |
|GPIO6 | Buzzer                            | Y |
|GPIO0 | Rain sensor                       | N |
|GPIO1 | Water tank empty sensor           | N |
|GPIO4 | Input (not used)                  | N |
|GPIO5 | Input (not used)                  | N |
|GPIO2 | PCD8544 CLK                       | N |
|GPIO3 | PCD8544 MOSI                      | N |
|GPIO22| PCD8544 Backlight                 | N |
|GPIO7 | PCD8544 Reset                     | N |
|GPIO10| PCD8544 CS                        | N |
|GPIO11| PCD8544 DC                        | N |
|GPIO8 | RTC SDA                           | N |
|GPIO9 | RTC SCL                           | N |

### Pico-Relay-B hardware modifications

The stock Pico-Relay-B lacks GPIO exposed via a terminal - those are needed to
connect rain sensor and water tank empty one, the crew terminal (4 pins) has
been added (lower left corner).

Also, the hardware initially had no RTC, DS3231 has been added (top left
corner).

Finally, PCD8544 display has been added to show controller state (not shown on
the image below).

![Waveshare Pico-Relay-B modifications](/docs/assets/waveshare-hw.jpg)

## How to use

Include following fragment to your ESPHome configuration (`sprinkler` name of
the package could be anything), it is recommended to use specific release
version (see
[Releases](https://github.com/hostcc/esphome-config-sprinkler/releases)) to
ensure your configuration is stable over the time.

```yaml
packages:
  sprinkler: github://hostcc/esphome-config-sprinkler/main.yaml@<release version>
```

The configuration could further be adjusted using substitutions, see
[main.yaml](main.yaml) for details.

You can also remove certain portions of the configuration marked as `Optional`
in [main.yaml](main.yaml) if you don't have particular hardware or need to use that
functionality.


## Sample installation

![Installation overview](/docs/assets/controller-hw-overview.jpg)

![Display, 1st page](/docs/assets/controller-display-1st-page.jpg)

![Display, 2nd page](/docs/assets/controller-display-2nd-page.jpg)

### Wiring diagram

TBD
