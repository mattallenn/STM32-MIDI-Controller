# STM32 MIDI Controller

This project acts a MIDI controller to send commands to a DAW such as Reaper. Not only is this a great learning platform, but it lets you control parameters for plugins and effects such as: turning a digital effect on/off, controlling an expression pedal, etc.

The project is broken into 3 parts: 

- Electronics (PCB, switches, potentiometers, etc)

- Software (USB Driver, Firmware)

- Mechanical (Enclosure, knobs, mounting)

## Electronics

### PCB

The heart of the electronics is a 4-layer PCB I designed in KiCad.

The purpose of the board is to connect to the host computer via USB, and convert the switches and knob movements into MIDI commands that will be send the the host.

This is achieved using an STM32F411RET6 MCU. I chose this primarily because I have the Nucleo Devboard with the same MCU, so it allowed me to protoype without risk of running into an unexpected firmware bug and having to order a second board. Also, I wanted to practice designing an MCU-based PCB and implement everything myself on the STM32 software stack. Setting up a USB driver like this is much different than including a library in Arduino.

I definitely could have achieved this layout with a 2-layer board, but I wanted to practice with 4 layers. My stackup was SIG/GND - GND - PWR - SIG/GND. I've read online that SIG/GND - GND - GND - SIG/GND is *slightly* better for EMI reasons, but :shrug:. Either way, it doesn't matter for my uses here. 

Also, I added a copper ring around the outside of the board. This really makes no different afaik, but it looks cool and I saw it in some other designs. There's quite a few things in this board that are likely sub-optimal for a production run, but I was having a lot of fun experimenting with techniques and trying new things.

I added some unnecessary feautures on this board such as programmable SMD LEDs and headers for unnused GPIO. This won't be accessible when inside of the enclosure, but I'm ordering 5 PCBs, so I can hopefully use the excess boards for different projects. 

![PCB Front Render](images/STOMPR_front.png)
![PCB Back Render](images/STOMPR_back.png)

### Switches & Knobs

To stay flexible and keep the PCB size under 100x100mm (cheaper from the fab), I'm just soldering wires to the pots and switches, and using JST connectors on the PCB.

I ordered the switches, pots, and a power LED from `lovemyswitches.com`. They've been super helpful and the prices were more than reasonable.

The specific components I got:

- [Pots](https://lovemyswitches.com/16mm-potentiometers-1-4-smooth-shaft-solder-lug/) x4 (10k Ohm, Linear)

- [Switches](https://lovemyswitches.com/pro-grade-spst-momentary-foot-switch-normally-open-soft-touch-solder-lug/) x4

- [LED](https://lovemyswitches.com/pre-wired-led-with-bezel-hump-style-blue-led) x1


## Software

All of the software is written using STM's CubeMX for initial code generation (pinout, clock configuration, etc) and CubeIDE for writing, flashing, and debugging the C code.

One the trickiest parts of this project has been getting the USB driver to work. I was able to get the STM32 to show up as a virtual COM port and a CDC device fairly easily, but getting it to work as a direct MIDI device has not been easy. As of now, I'm using it as virtual COM port and sending the commands through that. On the host computer, I have a python script that "translates" that data into MIDI commands. Hopefully I can get the direct MIDI communication working soon, though.

Other than that, it's pretty straightforward. The switches are mapped to GPIO input pins, and the pots are mapped to ADC pins. Based on their values and a bit of state logic, CC (controla change) and PC (program change) MIDI messages are sent to the host device.

Phil's Lab series of STM32 USB tutorials were immensely helpful.






