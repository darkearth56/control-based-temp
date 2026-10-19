# Automatic Device Control based on Temperature

A Home Assistant blueprint that switches a device on and off from an inside temperature, with an outside temperature limit and hysteresis.

**Current version: 1.3.0** · [Changelog](CHANGELOG.md)

[![Open your Home Assistant instance and show the blueprint import dialog with this blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Fdarkearth56%2Fcontrol-based-temp%2Fblob%2Fmain%2Fcontrol-based-on-temperature.yaml)

## Why I made it

I wanted something to control a heater in a shed. When the temperature drops below a set level the heater comes on, and it stays on until the shed reaches a set temperature. I also didn't want the heater to come on if it was already warm enough outside. The heater is plugged into a smart plug socket.

## How it works

| | Condition |
|---|---|
| **Turns on** | Inside temperature is **at or below** your inside threshold **and** outside temperature is **at or below** your outside limit |
| **Turns off** | Inside temperature is **at or above** your inside threshold **plus** the hysteresis |
| **In between** | Leaves the device as it is |

The automation re-checks every time either temperature sensor reports a new value.

**Example** — threshold 8 °C, hysteresis 2 °C, outside limit 10 °C:
- Shed drops to 8 °C on a 5 °C night → heater on.
- Shed warms to 10 °C → heater off.
- Shed at 9 °C → no change, so the heater doesn't flick on and off.
- Shed at 7 °C but it's 12 °C outside → stays off.

**Manual control:** if you switch the device on by hand while it's already at or above threshold + hysteresis, it will be switched off at the next sensor reading.

**Sensor dropouts:** if either sensor goes `unavailable` or `unknown`, nothing is switched until it reports a number again.

### More than one inside sensor (optional)

Temperature isn't the same everywhere — a shed or greenhouse is usually colder near the floor and warmer near the roof. Add extra sensors under **Extra inside temperature sensors** and pick how to combine them:

| Combine | Uses | Good for |
|---|---|---|
| **Average** (default) | The mean of all inside sensors | General comfort, steadier switching |
| **Lowest** | The coldest sensor | Protecting the coldest spot (frost, condensation) |

Any sensor that is unavailable is left out. If they're all unavailable, nothing is switched.

### Damp protection (optional, off by default)

Cold air holding a lot of moisture is what rusts tools and grows mould. Turn on **Damp protection** and add one or more **humidity sensors**; the highest reading (the dampest spot) is used.

| | Condition |
|---|---|
| **Turns on** | Usual temperature rule, **or** the dampest sensor is **at or above** *Damp on humidity* (and inside is below *Damp maximum temperature*) — still only when outside is at or below your outside limit |
| **Stays on** | Past threshold + hysteresis while the dampest sensor is still **above** *Damp off humidity* |
| **Turns off** | Warm enough **and** dry enough, **or** inside reaches *Damp maximum temperature* |

Suggested settings:

| Use | Damp on | Damp off | Damp max temp |
|---|---|---|---|
| Shed or garage with tools | 75 % | 65 % | 18 °C |
| Greenhouse (mould / botrytis) | 85 % | 75 % | 15–18 °C |

Warming air lowers its humidity — roughly 5 % for every 1 °C — so a short run is often enough. If it's damp all the time, ventilation or a dehumidifier will deal with the cause; the heater only keeps the air above its dew point.

If all humidity sensors are unavailable, damp protection is skipped and the device works on temperature alone. Turning **Damp protection** off ignores the humidity settings completely, so you can switch it off without removing the sensors.

## What you need

- Home Assistant **2024.8** or later.
- The device as a `switch`, `fan`, `light` or `input_boolean` entity — for example the smart plug the heater is plugged into.
- A temperature sensor inside, and one outside. If you don't have an outside sensor, select the inside one for both.
- Optional: more inside temperature sensors, and inside humidity sensors for damp protection.

## Installing

**Option 1 — one click:** press the **Import Blueprint** button above, then **Preview** and **Import blueprint**.

**Option 2 — manual:**
1. In Home Assistant go to **Settings → Automations & scenes → Blueprints**.
2. Press **Import blueprint** (bottom right).
3. Paste this URL and press **Preview**, then **Import blueprint**:
   ```
   https://github.com/darkearth56/control-based-temp/blob/main/control-based-on-temperature.yaml
   ```

## Using it

1. Go to **Settings → Automations & scenes → Blueprints**.
2. Find **Automatic Device Control based on Temperature** and press it to create an automation.
3. Fill in:

   | Setting | What to put |
   |---|---|
   | **Device** | The switch or smart plug to control |
   | **Temperature Inside** | Sensor in the room or shed |
   | **Temperature Outside** | Outdoor sensor (or the inside one again if you don't have one) |
   | **Outside temperature limit** | Device won't turn on above this outside temperature. Set it high (e.g. 50 °C) to ignore the outside temperature |
   | **Inside temperature threshold** | Device turns on at or below this |
   | **Hysteresis** | How far above the threshold to heat before turning off |
   | **Confirm delay** | Seconds to wait before checking the device switched; resends once if not. 0 turns it off |
   | **Extra inside temperature sensors** | Optional — more inside sensors |
   | **Combine inside temperatures** | Average or Lowest, when there's more than one |
   | **Damp protection** | Off by default. Turn on to also heat when it's damp |
   | **Humidity sensors** | Inside humidity sensors for damp protection |
   | **Damp on / Damp off humidity** | Start heating at or above *on*, keep heating until at or below *off* |
   | **Damp maximum temperature** | Damp protection never heats above this |

4. Press **Save** and give the automation a name.

## Updating

When a new version is released, update your copy from Home Assistant:

1. **Settings → Automations & scenes → Blueprints**.
2. Press **⋮** next to this blueprint → **Re-import blueprint**.
3. Confirm. Your automations keep their settings and use the new version straight away.

If you've edited your local copy of the blueprint, re-importing will replace your edits.

## Changes

See [CHANGELOG.md](CHANGELOG.md) for what each version changed and fixed.

## Credit

Based on the "Automatic Fan Control based on Presence and Temperature" blueprint by [fabiosoft](https://community.home-assistant.io/t/automatic-temperature-fan-control/350172), adapted here for heater control with an outside temperature limit.
