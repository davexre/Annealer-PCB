# Annealer PCB
 An Arduino shield for induction cartridge case annealers based on the GinaErick design. 

## Origins
 This design began from a simple DIY induction based cartridge case annealer created by a couple people on the
 [accurateshooter.com forum](http://forum.accurateshooter.com/threads/induction-brass-annealer-redux.3908353/). 
 Get ready for some reading - as of this writing, that thread is over 103 pages long!
 
 After creating a working, basic, analog annealer, I decided it was time to convert the thing to digital 
 control, and started implementing Arduino based controls. That naturally led to implementing the Arduino
 shield here to make it easier and cleaner to hook up the Arduino to all of the necessary hardware.
 
 ## Features
 - Arduino Uno R3 format shield
 - Support for boards with 5v, 3.3v, and 2v ADCs
 - Sensors
   - Two thermistors
   - Voltage
   - Current
   - Two Photo detector or IR transmitter/receiver pairs
 - Two buttons (typically, Start and Stop) via a 4-pin header
 - Two relays (typically, for Induction board and solenoid control)
 - Two auxiliary headers (signal pin and ground, could be used for a variety of control purposes)
 - Multipurpose header (3 signals and ground, can hook up an encoder with button, or any other use)
 - Two LEDs for troubleshooting and confirmation of code
 - Reset button
 
 ## Example Implementation
 My code for implementation is here: https://github.com/davexre/Annealer-Control
 
 ## Configuration
 The board was originally designed with [SparkFun's](https://www.sparkfun.com/) [RedBoard Artemis](https://www.sparkfun.com/products/15444)
 in mind. The Artemis processor has ADCs that use 2v as their reference. They can safely handle 
 3.3v, but saturate at 2v. So, the board incorporates several voltage dividers to keep things within
 the scale that the ADCs can successfully read. The documentation here covers ways to configure the
 board for other reference voltages. There are four solderable open/closed jumper pads that you can
 use to alter several circuit paths appropriately. SparkFun has a [tutorial on using jumper pads](https://learn.sparkfun.com/tutorials/how-to-work-with-jumper-pads-and-pcb-traces) on their site.
 
 ### Voltage
 Input to pin A0.
 
 The voltage sensor circuit expects to be fed by a voltage divider - I'm using an Octopus Voltage Divider Brick,
 which is a 16:1 voltage divider. For a 48v power supply on the annealer, that means a maximum input voltage of 4v
 coming in to J1 on the board. The design then incoporates a second voltage divider using a 10k resistor and a 20k
 trimpot, so you can adjust the input voltage precisely to match the ADC (so, exactly 50% for the Artemis). 
 
 However, if you board supports 3.3v, you can set the trimpot to cut 4v down to 3.3v. For a 5v board, you can bypass the 
 on-board voltage divider completely by cutting the VOL-BYPASS jumper, and soldering the other side closed. This will
 pass the input on J1 directly to A0. Be careful! 
 
 It may also be possible to use the onboard voltage divider to cut the voltage back to anything reasonable. The design
 uses a [Bourns 3296W](https://www.bourns.com/pdfs/3296.pdf) trimpot, which is available in resistances up to 2Mohm.
 A 200kOhm trimpot could be used (instead of the spec'ed 20kOhm trimpot) in conjunction with the specified 10kOhm resistor
 to bring 48v in line with the ADC. But... I prefer to keep higher voltage off the shield, thus the voltage brick. 
 
 ### More to come
