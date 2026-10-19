# Changelog

## 1.3.1 — 2026-10-19

### Changed
- **Credits corrected.** The README gave fabiosoft's name with the title of Sam04's blueprint. This blueprint started in 2022 from Sam04's *Automatic Fan Control based on Presence and Temperature*, and its temperature-only form follows fabiosoft's *Automatic Temperature Fan Control*. Both are now credited, in the README and in the blueprint description inside Home Assistant. No changes to how the blueprint works.

## 1.3.0 — 2026-10-19

### Added
- **Optional dew point protection.** Pick an inside humidity sensor and the blueprint works out the inside dew point (Magnus formula). The device then also turns on when the inside temperature comes within the **dew point margin** (default 1.5 °C) of the dew point, to stop condensation, and only turns off once the inside is at threshold + hysteresis **and** at least margin + 1 °C above the dew point.
- New inputs: **Humidity Inside (optional)** and **Dew point margin**.

### Notes
- Leave the humidity sensor empty and nothing changes: the blueprint works on temperature only, exactly as 1.2.x.
- The outside temperature limit still applies to dew point switching.
- The automation re-checks when either temperature sensor reports. Most combined temperature/humidity sensors report both together, so the humidity sensor isn't a separate trigger.

## 1.2.2 — 2026-10-18

### Changed
- **Confirm delay default lowered from 60 s to 30 s.** An EcoFlow AC outlet was measured taking about 20 s to switch off under load, so 30 s leaves a margin while resending a missed command sooner. Automations that already set their own value keep it.

## 1.2.1 — 2026-10-18

### Changed
- **Confirm delay is now a slider** (0–300 s, 5 s steps) instead of a number box, so it's easier to tune for slow sockets. Default stays at 60 s and existing settings are kept.

## 1.2.0 — 2026-10-18

### Added
- **Confirm delay with one retry.** After switching the device on or off, the automation waits (default **60 s**) and checks the device really changed. If it didn't, the command is sent once more. This helps with cloud-controlled sockets (for example an EcoFlow AC outlet) that sometimes accept a command but don't act on it. Set the delay to 0 to turn it off and get the 1.1.0 behaviour.

### Notes
- Existing automations pick up the 60 s default automatically after re-importing; no settings need changing.
- While the automation is waiting, new temperature readings are ignored (`mode: single`); the next reading after the wait is handled normally.

## 1.1.0 — 2026-10-17

### Fixed
- **Device never turned off when it got warm.** The turn-off check was written backwards: it switched the device off when the inside temperature was *at or below* threshold + hysteresis, and never when it was above it. A heater turned on by hand, or one that overshot, stayed on indefinitely. It now turns off when the inside temperature is *at or above* threshold + hysteresis.
- **Hysteresis didn't work.** Because of the same bug, the device was turned off again anywhere between the threshold and threshold + hysteresis. It now stays on through that band, as hysteresis should.
- **An unavailable sensor could switch the device on.** An `unavailable` or `unknown` reading was treated as 0 °C, which counts as cold and could turn a heater on. The automation now does nothing until both sensors report a number again.
- **Couldn't be used with a smart plug.** The device selector only accepted `fan` entities. It now accepts `switch`, `fan`, `light` and `input_boolean`.
- **Re-import didn't work.** `source_url` pointed at the repository rather than the blueprint file, so Home Assistant's "Re-import blueprint" couldn't fetch it.

### Changed
- Instructions rewritten so they match what the blueprint actually does (the old text said "turns on" for both cases, "minus" instead of "plus" the hysteresis, and described the outside limit backwards). Spelling corrected throughout.
- Removed the "set to 0 to disable" note on the outside temperature limit — 0 never disabled it, it only allowed the device on when it was freezing outside. Set it high (e.g. 50 °C) instead.
- Slider ranges made realistic: outside limit −30 to 50 °C, inside threshold −10 to 40 °C, hysteresis 0.5 to 10 °C (previously up to 300 °C / 100 °C). Existing values inside these ranges are unaffected.
- "Fan" input relabelled "Device". The input's internal key is still `fan`, so existing automations keep working without changes.
- Uses `action:` instead of the older `service:` keyword; requires Home Assistant 2024.8 or later.
- Added `author` and version number.
- Credit to fabiosoft, author of the original blueprint this is based on, now shown in the blueprint's description inside Home Assistant as well as in the README.

Fixes and documentation for 1.1.0 prepared with help from Claude (Anthropic).

## 1.0.0
- Initial release.
