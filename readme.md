# What is it?

Set of files to duplicate GameGlass functionality using TouchOSC and the OSC protocol parser implemented as a Joystick Gremlin plugin.

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

Currently it's in a very raw format - you need to edit the port and the IP address of your machine in the code.   Also make sure the game machine has the UDP ports open to receive traffic from the external device.

Why is an external device needed?  On windows, any touch enabled screen changes the focus to that window, which takes away the focus from the gaming window - causing flicker - loss of input or other graphical glitches.


### osc.xml

Test Gremlin profile.  References the osc plugin above.



### Resources

#### Custom version of Gremlin 13.x

Adds x64 and a few decorators needed to support the plugin functionality.


https://github.com/muchimi/JoystickGremlinEx


#### Joystick Gremlin main site

The excellent Joystick Gremlin by Whitemagic the custom version is derived from is found here.  This is simply the best tool I found for this work for complex hardware setups with multiple devices.

https://whitemagic.github.io/JoystickGremlin/


#### HIDHIDE

This tool hides raw hardware only exposing the VJOY devices.  Essential to not confuse games or simulators.

https://github.com/ViGEm/HidHide

#### Hexler TouchOSC


A touch enabled surface designer initially setup for the OSC (open sound control) and MIDI protocols to control musical instruments, DAWs and live performances.  Supports multiple platforms.  Has a free version but the license is well worth the price.  Simple set of controls, but very powerful because of the available LUA based scripting and works on any platform, thus making your phone, tablet or touch-enabled desktop function as an input device.

https://hexler.net/touchosc#_

I also recommend the Protokol tool to diagnose any OSC issues.





