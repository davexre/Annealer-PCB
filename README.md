# Annealer PCB
 An Arduino shield for induction cartridge case annealers based on the GinaErick design. 

## Pictures!

- [Front of board design](images/AnnealerPCBv3-front.png)
- [Back of board design](images/AnnealerPCBv3-back.png)

Final boards will actually print with an inductor symbol on the front and back, too - OSH Park's rendering doesn't 
show it correctly. Pictures of the actual boards will replace the images here when they arrive.

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
   - Two [break beam sensors](https://www.adafruit.com/product/2167)
 - Two buttons (typically, Start and Stop) via a 4-pin header
 - Two relays (typically, for Induction board and solenoid control)
 - Two auxiliary headers (signal pin and ground, could be used for a variety of control purposes)
 - Multipurpose header (3 signals and ground, can hook up an encoder with button, or any other use)
 - Two LEDs for troubleshooting and confirmation of code
 - I2C connector to add an LCD or other I2C based device
 - Power On LED connector for front panel
 - Reset button
 
 You can find a [Bill of Materials](BOM.md) in this repository, too.
 
 ## IMPORTANT NOTE
 
 If you use the I2C connector, be aware that you cannot use Proximity sensor 2 or Thermistor 2 on some boards (like
 the Arduino Uno). Any board that ties SDA and SCL pins to A4/A5 will encounter this issue. I recommend that you pick the
 functions you wish to use, and only install connectors or headers for those functions (ie, only install the I2C connector
 **or** the Proximity 2 and Thermistor 2 connectors!
 
 
 ## Example Implementation
 My code for implementation is here: https://github.com/davexre/Annealer-Control - note that I don't make use of 
 all of the features on this board at this time! 
 
 ## Configuration
 The board was originally designed with [SparkFun's](https://www.sparkfun.com/) [RedBoard Artemis](https://www.sparkfun.com/products/15444)
 in mind. The Artemis processor has ADCs that use 2v as their reference. They can safely handle 
 3.3v, but saturate at 2v. So, the board incorporates several voltage dividers to keep things within
 the scale that the ADCs can successfully read. The documentation here covers ways to configure the
 board for other reference voltages. There are four solderable open/closed jumper pads that you can
 use to alter several circuit paths appropriately. SparkFun has a [tutorial on using jumper pads](https://learn.sparkfun.com/tutorials/how-to-work-with-jumper-pads-and-pcb-traces) on their site.
 
 ### Pin Map
 ```
#ifdef _V4_BOARD
  #define  VOLTAGE_PIN     A0
  #define  CURRENT_PIN     A1
  #define  THERM1_PIN      A2
  #define  OPTO1_PIN       A3
  #define  THERM2_PIN      A4
  #define  OPTO2_PIN       A5
  #define  START_PIN       2   
  #define  STOP_PIN        3
  #define  INDUCTOR_PIN    4
  #define  SOLENOID_PIN    5
  #define  AUX1_PIN        6
  #define  AUX2_PIN        7   
  #define  INDUCTOR_LED    8
  #define  SOLENOID_LED    9
  #define  ENCODER_A_PIN   10
  #define  ENCODER_B_PIN   11
  #define  ENCODER_BUTTON  12
#endif
```


 ### Voltage
 Input to pin A0.
 
 The voltage sensor circuit expects to be fed by a voltage divider - I'm using an Octopus Voltage Divider Brick,
 which is a 16:1 voltage divider (see below). For a 48v power supply on the annealer, that means a maximum input voltage of 4v
 coming in to J1 on the board. The design then incoporates a second voltage divider using a 10k resistor and a 20k
 trimpot, so you can adjust the input voltage precisely to match the ADC (so, exactly 50% for the Artemis). 
 
 However, if you board supports 3.3v, you can set the trimpot to cut 4v down to 3.3v. For a 5v board, you can bypass the 
 on-board voltage divider completely by cutting the VOL-BYPASS jumper, and soldering the other side closed. This will
 pass the input on J1 directly to A0. Be careful! 
 
 It may also be possible to use the onboard voltage divider to cut the voltage back to anything reasonable. The design
 uses a [Bourns 3296W](https://www.bourns.com/pdfs/3296.pdf) trimpot, which is available in resistances up to 2Mohm.
 A 200kOhm trimpot could be used (instead of the spec'ed 20kOhm trimpot) in conjunction with the specified 10kOhm resistor
 to bring 48v in line with the ADC. But... I prefer to keep higher voltage off the shield, thus the voltage brick. 
 
 To tune the voltage divider after assembly, attach 5v to pin 1 at connector J1. Measure the voltage between A0 and GND.
 Tune trimpot VR1 until you measure 2.5v, indicating that the voltage divider network is dividing voltage by 2 exactly. 
 
 *Note:* it seems like the Octopus Voltage Divider Brick is no longer made by ElecFreaks. So, 
 I [ginned up another PCB design](https://github.com/davexre/Voltage-Divider-Brick) that implements something very
 similar to the Octopus brick. You can substitute whatever resistors you need to get the output voltage within the scale
 that your board can support. You could potentially just use the voltage divider already on the Annealer board to replace
 it, but personally, I prefer to keep potentially damaging voltages (pun intended) away from my main board. The brick 
 adds a small layer of protection, in this case.
 
 ### Current
 Input to pin A1.
 
 The current sensor circuit expects to receive the output from something like a Hall effect sensor, such as the [Gravity
 20A Current Sensor](https://www.dfrobot.com/product-1570.html) made by DF Robot. The pinout on J2 expects to power a 
 device, so pin 1 is 5V supply. Pin 2 is ground. Pin 3 is the return from the [ACS712 Hall effect sensor IC](http://www.allegromicro.com/~/media/Files/Datasheets/ACS712-Datasheet.ashx?la=en). The sensor returns 
 1/2 of the supply voltage at 0A (and can signal +/- 20A). At +20A, it returns 4.5v. 
 
 If you have a 5v board, you can safely bypass the voltage divider network by cutting the CUR-BYPASS jumper and soldering
 the other side closed. 
 
 For other boards, the voltage divider can be tuned to pull the max input value down to match the ADC reference voltage.
 Divide your reference voltage by 4.5 to get the scale factor, and then multiply by 5 to get a target voltage. For instance, 
 for the Artemis, 2/4.5 = .444. Multiply by 5v to get a target of 2.222 volts. For 3.3V reference, your target is 3.667.
 Attach 5v to pin 3 on J2, and then measure voltage between A1 and GND. Tune trimpot VR2 until you reach the target voltage. 
 
 For using sensors other than the Gravity sensor block lined above, you can use this circuit as needed. Changing the jumper
 setting links pin 3 on J2 to A1 directly, or you can simply use the voltage divider network to configure the correct input
 voltage for your sensor. 
 
 ### REF-VOLT jumper
 
 The REF-VOLT jumper feeds the thermistor and proximity sensors an appropriate logic level voltage for the board
 in use. To get accurate readings, and avoid damage to your board, you need to supply these devices with your reference
 voltage. 
 
 The default setting is 3.3v - you can easily cut the jumper and solder to the 5.5v side, as well.
 That's easy if you have a 3.3v or 5v board. If you have an Artemis based board, though, you need to supply a
 2v reference for accurate readings. So, we do that with a voltage divider on the board. By default, the board divides 3.3v down to 2v for the source voltage.
 To set this up, feed 3.3v into the 3.3v pin on the shield. Then probe the middle pad of the SRC_VOLT jumper and GND.
 Adjust VR3 until you reach 2v.
 
 To configure for 3.3v reference, cut the SRC_DIV jumper and solder the other side. This connects the 3.3v supply straight
 both thermistor voltage dividers. 
 
 To configure for 5v, cut both the SRV_VOLT and SRC_DIV jumpers and resolder the opposite sides. This connects the 5v
 supply straight to both thermistor voltage dividers and both proximity devices.
 
 
 ### Thermistors
 Input to pins A2 and A4, respectively. _**SEE NOTE ABOVE ABOUT I2C**_
 
 These are basic 10K nominal thermistors. They're generally used by wiring them in a voltage divider configuration with a
 10k resistor, and determining the resistance of the thermistor at the current temperature, and then cross referencing that
 to a table, or using a Steinhart style equation to arrive at the actual temperature. One example of [this kind of thermistor](https://www.adafruit.com/product/372_)
 is sold by Adafruit. 
 
 There's also a voltage divider feeding the thermistors to ensure they get the correct logic level voltage. That's easy if
 you have a 3.3v or 5v board. If you have an Artemis based board, though, you need to supply a  2v reference for accurate
 readings. So, we do that with a voltage divider on the board. By default, the board divides 3.3v (from the REF-VOLT jumper)
 down to 2v for the source voltage.
 
 To set this up, feed 3.3v into the 3.3v pin on the shield. Then probe the middle pad of the REF-VOLT jumper and GND.
 Adjust VR3 until you reach 2v.
 
 See "REF-VOLT jumper" above to determine how to send the correct source voltage to the thermistors. If you need to bypass 
 the voltage divider (to send 3.3v or 5v directly to the thermistors), cut the THERM-DIV jumper and solder to the other
 side. 
 
 
 ### Proximity Sensors
 Input to pins A3 and A5, respectively. _**SEE NOTE ABOVE ABOUT I2C**_
 
 J3 and J10 are both 5 pin jumpers that are intended to drive the [Adafruit IR Break Beam sensors](https://www.adafruit.com/product/2167). 
 
 Pin 1 connects to a digital pin, so you'll need to use A3 and A5 with digitalRead. You need to set INPUT_PULLUP on 
 those pins, as well. Pin 2 should be your reference voltage (3.3v or 5v) and is set by the REF-VOLT jumper (see above). 
 Pin 4 is 5v for the IR transmitter. Pins 3 and 5 are ground.
 
 
 ### Start/Stop buttons, relays, AUX, and Encoder connectors
 
 Each of these connectors provides direct access to one or more digital pins. You can use these for input or output as
 required. The example implementation uses the start/stop buttons, and the relays, currently. 
 
 J8 is a 4 pin connector with pins 2 & 4 connected to GND. Pin 1 connects to D6, and Pin 3 connects to D7. The intention
 here is to connect each pair to a basic momentary switch/button and use them for control over the anneal cycle. You likely
 want to use the INPUT_PULLUP option on the pin configuration, or implement your own pullup or pulldown resistor for each
 pin. 
 
 J6, J7, J9, and J11 are all two pin connectors. Pin 2 on each connects to GND. Pin 1 connects as follows: J6 - D4, J7 - D5,
 J9 - D2, J11 - D3. The example uses J6 and J7 to operate the inductor board SSR, and the trapdoor solenoid SSR, 
 respectively.
 
 The Encoder connector provides access to 3 additional digital pins (D10 on pin 1, D11 on pin 2, D12 on pin3), with
 the fourth pin connecting to GND. This could be used for any purpose required, but one common purpose might be to interface
 with an encoder knob and click switch. 
 
 
 ### I2C
 
 _**SEE NOTE ABOVE ABOUT PIN CONFLICTS WITH I2C AND OTHER DEVICES**_
 
 The I2C connector breaks out GND on pin 1, VCC (selectable 3.3V or 5V) on pin 2, SDA on pin 3, and SCL on pin 4, similar
 to the pin order found on common I2C LCDs. This gives you a way to connect any I2C device to your board. Between this and
 the Multipurpose connector, you can easily implement an LCD and clickable encoder for input and display. 
 
 There's a solderable jumper for voltage selection. It defaults to 3.3v, with 5V selectable on the other side of the jumper.
 Obviously, use the correct voltage for your device(s)!
 
 
