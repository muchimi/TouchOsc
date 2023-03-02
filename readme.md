# List of files:

#### xplane.tosc

test TouchOSC template - don't mind the name.

The template currently has a few LUA examples of self centering radio buttons to simulate a spring loaded button with two speeds on either side (heading).

Also shows how to send commands to the OSC plugin in Gremlin to set axis and button values.


#### osc.py

Joystick Gremlin plugin (requires a custom version of Joystick Gremlin forked from 13.3 and updated to my particular needs I thought were missing.

My custom repository is here:

https://github.com/muchimi/JoystickGremlinEx

The changes include a bit more resiliency when loading plugins that include missing references or cause some other type of load issue when Gremlin scans it for information.  

They also include decorators to simplify the startup and proper shutdown of the OSC UDP server - hence why the custom version of Gremlin is needed for this.

This custom plugin gets dropped into your Joystick Gremlin profile folder (usually %USERPROFILE%\Joystick Gremlin)

This plugin includes an OSC server based on Python-OSC included in its entirety in the plugin to avoid distribution issues with the compiled version of Gremlin.



### osc.xml

Test Gremlin profile.  References the osc plugin above.






