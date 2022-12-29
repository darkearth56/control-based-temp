I wanted somthing to control a heater in a shed. So when the temperature dropped below and set temp the heater would come on
and stay on until it reached a set temperature.

Also did not want the heater to come on if the set outside temperature was reached. 
and the heater was plugged into a plug socket.


Automatic Device Control based on Temperature

Device ON/OFF Automation based on temperature, outside temperature.

Device turns on if the following conditions are met:

temperature inside lower than your set threshold value
temperature outside is higher than your set maximum value (to prevent turning the device on)

Device turns on if the following conditions are met:

temperature inside is lower than your threshold value minus the hystersis value

Manual device control.

Adapt the given values too your needs.
If you manually turn the device on and the inside temperature is above the threshold your device will turn off.

What you need:

a device entity. if you control your device via a switch entity (via smart plug for example) you can create a template device entity.

sensor entities for inside and outside temperature.

Credit to [fabiosoft](https://community.home-assistant.io/t/automatic-temperature-fan-control/350172)
