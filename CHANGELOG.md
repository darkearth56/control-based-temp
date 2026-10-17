# Changelog

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
