
# What is it?

This project adds OSC functionality to Joystick Gremlin.  It is based on a customized version of Joystick Gremlin by WhiteMagic augmented with specific hooks to support the custom plugin.  The customized version I call Joystick Gremlin Ex (JGEX) as it adds hooks, but also quality of life features not present in version 13.3 of JG that I wanted to see and use because it makes programming games simpler for me.

The OSC plugin for JGEX listens for OSC messages sent over the network and converts these messages to Gremlin actions, such as joystick and macro output.

The plugin is designed to work with tools like TouchOSC or any software that sends OSC messages that can be customized.

## What is OSC?

OSC stands for Open Sound Control, a UDP based protocol similar to MIDI but can send any command between music software.   For details on OSC consult https://en.wikipedia.org/wiki/OSC

In this case, we use software like TouchOSC to allow a touchscreen on any platform to control a game via Gremlin.   The plugin interprets specific custom OSC commands and sends those to the game either as a keyboard macro or a joystick input via VJOY.

A list of commands can be found in the commands section

## How does it work?

### Server component - JGEX - on gaming host


The OSC plugin for JGEX implements an OSC server on the gaming host.  The server listens to OSC messages and converts them to events sent to the game such as a keyboard macro or a joystick action (via VJOY).

The response time is in milliseconds as the OSC protocol is very fast.

The server has to be the gaming computer.

The OSC file contains two important configuration items:  it needs to know the IP address of the gaming host it's runing on (localhost cannot be used), and it needs to know the pair of UDP ports used by OSC (one to receive, one to send).   The configuration must match the host configuration for any clients.

When a JGEX profile is started, it enables the OSC server and enables Gremlin to convert the OSC messages received to Joystick or keyboard output and send those to the game.

### Client component - TouchOSC - on local network clients

The client (which can be more than one) can be any computer, smartphone or tablet on the network running TouchOSC.   TouchOSC currently supports all major platforms including Windows, MacOS, Android and iOS.  

TouchOSC is not the only client that can be used - any application that can send OSC messages in the correct format will work.

While the client can also be the host, this is not recommended.  MS Windows forces a focus change when a touch input device is used, which disrupts process inputs in game and causes flicker and/or focus loss for inputs like keyboard macros.  Therefore it is best to use a networked device (wifi or other) for the touch screen functionality.   More than one device can be used concurrently as well.


Software like TouchOSC is used to draw the touch surface and controls on it and send OSC messages to Gremlin based on the user's interaction with the touch surface.

TouchOSC also provides Lua scripting for its controls to manage the logic and allow for more complex local interactions or logic to manage the touch surface.

# Example LUA scripts

Example showing how to press and hold the N key while the control is touched, and release the N key when the control is no longer touched.

	function onValueChanged()
	  if(self.values.touch and self.values.x == 1) then 
	     -- press
	    sendOSC('/KEY/+N', self.values.x)
	  elseif (self.values.x == 0 and not self.values.touch) then  
	    -- release
	    sendOSC('/KEY/-N', self.values.x)
	  end
	end



In this example, the left control key is held, a delay of 250ms is added, and then the keys w, a, s, d are held pressed while the control is touched.  Gremlin is also asked to say "Hello World" via text to speech when the control is touched. The keys are released when the control is no longer touched.

	function onValueChanged()
	  if(self.values.touch and self.values.x == 1) then 
	    -- press
	    sendOSC('/KEY/+lctr[250]+wasd/say/hello world', self.values.x)
	  elseif (self.values.x == 0 and not self.values.touch) then  
	    -- release
	    sendOSC('/KEY/-wasd-lctr', self.values.x)
	  end
	end	

In this example, axis 1 on VJoy device 1 is set based on the value of the control.  OSC controls range in value from 0.0 to 1.0.  This will be converted by JGEX to a value -1.0 to +1.0.  In this case the value of the axis 0..1 should be in the X argument sent via OSC.

	function onValueChanged()
	    sendOSC('/vjoy/d1a1', self.values.x)
	end

In this example, we are now sending a button press while the control is held (device 2, button 2).  Devices are 1 based, so are buttons.

	function onValueChanged()
	    sendOSC('/vjoy/d2b2', self.values.x)
	end


# OSC commands

JGEX recognizes OSC commands in the usual OSC format of nested commands.  These are text strings and they are not case sensitive but the order matters.


	/say/speech text

Converts the string to speech.



	/vjoy/[vjoy command]
	
Sends joystick output via VJOY.  Available commands for [vjoy command]:

d[1-8]: specifies which Vjoy device the output goes to.  

b[1-128]: specifies which button is pressed.

r[1-128]: releases specified button.

tb[1-128]: toggles the button.

p[delay]: pulses the button for the specific duration.  If not specified, 250ms is the default.

a[1..8]: specifies the axis to output the value to.  If not followed by a v command, the value of the axis is taken from the OSC x argument, expected to be in the floating point range 0 to 1.

v[-1000..+1000]: when following an axis command, sets that axis to the specific value divided by 1000 for a range -1 to +1 (so -1000 is -1, +1000 is +1).



Examples:

Holds buttons 2, 5 on device 1, and toggles button 6.

	/vjoy/d1b2b5bt6
	

Pulse button 1 on device 3 for half a second:

	/vjoy/d3b1p500


Set device 2 axis 6 to center:

	/vjoy/d2a6v0
	
Set device 2 axis 6 to minimum:

	/vjoy/d2a6v-1000

Set device 2 axis 6 to maximum:

	/vjoy/d2a6v1000
	
	
Set device 1 axis 1 using the x value, and says "something" with text to speech, and taps the N key:

	/vjoy/d1a1/key/n/say/something
	
	


# List of files:

These templates currently has a few LUA examples of self centering radio buttons to simulate a spring loaded button with two speeds on either side (heading).

Also shows how to send commands to the OSC plugin in Gremlin to set axis and button values.

#### xplane.tosc

Example TouchOSC template for x-plane. 

#### sc.tosc

Example TouchOSC template for Star Citizen.




#### osc.py

Joystick Gremlin Ex plugin that adds OSC functionality.



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


#### Joystick Gremlin 

The excellent Joystick Gremlin by Whitemagic (Lionel Ott).    
This the custom version of Gremlin is based on version 13.3 of Joystick Gremlin, modified to support additional convenience features. Gremlin is is simply the best tool I found to control multiple raw hardware for gaming, in terms of its architecture and feature set.  It allows for very  complex hardware setups with multiple devices and customized plugins.

The original project can be found here:
https://whitemagic.github.io/JoystickGremlin/

The custom project needed to support TouchOSC from Gremlin including necessary hooks can be found here: https://github.com/muchimi/JoystickGremlinEx




#### HIDHIDE

This tool hides raw hardware only exposing the VJOY devices.  Essential to not confuse games or simulators.

https://github.com/ViGEm/HidHide

#### Hexler TouchOSC


A touch enabled surface designer initially setup for the OSC (open sound control) and MIDI protocols to control musical instruments, DAWs and live performances.  Supports multiple platforms.  Has a free version but the license is well worth the price.  Simple set of controls, but very powerful because of the available LUA based scripting and works on any platform, thus making your phone, tablet or touch-enabled desktop function as an input device.

https://hexler.net/touchosc#_

I also recommend the Protokol tool to diagnose any OSC issues.





