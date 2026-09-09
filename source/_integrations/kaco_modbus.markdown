---
title: KACO Modbus
description: Instructions on how to integrate a KACO solar inverter with Home Assistant over Modbus TCP.
ha_category:
  - Energy
ha_release: '2026.10'
ha_iot_class: Local Polling
ha_codeowners:
  - '@g4bri3lDev'
ha_domain: kaco_modbus
ha_platforms:
  - sensor
ha_config_flow: true
ha_integration_type: device
ha_quality_scale: bronze
---

The **KACO Modbus** {% term integration %} connects Home Assistant to a
[KACO new energy](https://kaco-newenergy.com/) solar inverter, and shows what
it is producing. The inverter is read directly over your local network, so no
cloud account or manufacturer portal is involved.

## Use cases

Reading the inverter directly gives you production figures at the same rate the
inverter measures them, without waiting on KACO's portal. That makes it possible
to put solar production on the [Energy dashboard](/docs/energy/), to run
appliances while there is surplus to use, and to be told the same day when the
array stops producing.

## Supported devices

KACO inverters that provide SunSpec over Modbus TCP, which covers the current
three-phase range including the NX3, TL3 and NH3 families.

## Prerequisites

- The inverter needs a network connection to Home Assistant, over its Ethernet port.
- Modbus TCP needs to be enabled on the inverter. This is a setting on the
  inverter itself, reachable either from its display or through its web
  interface, usually under the SunSpec or Modbus protocol settings.
- The inverter accepts one Modbus connection at a time, so anything else
  polling it over Modbus needs to be stopped first.

{% include integrations/config_flow.md %}

{% configuration_basic %}
Host:
  description: "The hostname or IP address of the inverter."
Port:
  description: "The Modbus TCP port to connect to. KACO inverters use `502` unless this has been changed."
Modbus unit ID:
  description: "The inverter's Modbus unit ID, also called its device address. Leave this at `1` unless the inverter is reached through an RS485-to-TCP gateway, which needs the address configured on that gateway."
{% endconfiguration_basic %}

## Supported functionality

### Sensors

- **AC power**: The power the inverter is currently feeding into the grid.
- **Total energy produced**: The inverter's lifetime energy counter. Add this to
  the [Energy dashboard](/docs/energy/) as a solar production source, as
  described in [integrating your solar panels](/docs/energy/solar-panels/).
- **Operating state**: What the inverter is currently doing, such as producing,
  asleep, or in a fault condition.

## Data updates

The **KACO Modbus** {% term integration %} {% term polling polls %} the inverter
every 30 seconds.

## Examples

### Get told when the inverter faults

A faulted inverter stops producing and does not announce it. This blueprint
notifies you when the operating state turns to **Fault**.

{% my blueprint_import badge blueprint_url="https://www.home-assistant.io/blueprints/integrations/kaco_modbus_inverter_fault_notification.yaml" %}

## Troubleshooting

### Cannot connect to the inverter

The address is right but setup fails, or the entities go unavailable.

1. Check Modbus TCP is still enabled on the inverter, under its SunSpec or
   Modbus protocol settings.
2. Stop anything else polling the inverter over Modbus. It accepts one
   connection at a time.
3. Confirm the port and unit ID. Both are on the inverter's Modbus settings
   page, and a gateway has its own unit ID.

### The device is not a KACO inverter

Another SunSpec inverter answers at that address. Check the IP address, which
may have changed if the inverter's DHCP lease moved to another device.

## Known limitations

Only inverters that offer Modbus TCP are supported. KACO inverters that provide
only the manufacturer's serial protocol can't be used with this integration.

## Removing the integration

This integration follows standard integration removal. Disabling Modbus TCP on
the inverter afterwards is optional.

{% include integrations/remove_device_service.md %}
