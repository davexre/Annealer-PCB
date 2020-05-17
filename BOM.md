# Annealer PCB BOM

In addition to the Annealer shield board, you'll need some components to finish out the build and use the shield.
This Bill of Materials lists out the set of parts required to assemble the whole thing, and suggested purchase
locations for each. 

## Parts List

- 2x 5mm LEDs
- Molex Polarized Connectors
  - 7x 1x2
  - 1x 1x3
  - 4x 1x4
- 3x Bourns 3296W-1-203ALF trimpots
- 7x 10k ohm axial lead resistor
- 4x 330 ohm axial lead resistor
- 1x mini pushbutton SPST switch

### Connectors

Molex makes a number of different versions. The board is designed for .1" (2.54mm) pitch connectors, but you can even use
male headers, if that's your preference. Some examples from SparkFun:

- https://www.sparkfun.com/products/8233
- https://www.sparkfun.com/products/8232
- https://www.sparkfun.com/products/8231

And the corresponding connectors for your cables (don't forget the pins!):

- https://www.sparkfun.com/products/8095
- https://www.sparkfun.com/products/8096
- https://www.sparkfun.com/products/8097
- https://www.sparkfun.com/products/8100

A number of examples are available from Mouser and other places, too. For instance:

- https://www.mouser.com/ProductDetail/molex/22-23-2021/?qs=ILqg114nvd4YKlRlbo3yMg%3D%3D&countrycode=US&currencycode=USD
- https://www.mouser.com/ProductDetail/molex/22-11-2036/?qs=SdQWveS8jHNIXMa9OcvBPw%3D%3D&countrycode=US&currencycode=USD
- https://www.mouser.com/ProductDetail/molex/22-23-2042/?qs=52ipEG6BoUGnVIQV2y3Jgg%3D%3D&countrycode=US&currencycode=USD

And the corresponding connectors and pins:

- https://www.mouser.com/ProductDetail/molex/10-11-2023/?qs=T%2FNdvNikOF379zD2RopdYA%3D%3D&countrycode=US&currencycode=USD
- https://www.mouser.com/ProductDetail/molex/10-11-2033/?qs=bhsdBC0mzWCpS6OmMejH%252bg%3D%3D&countrycode=US&currencycode=USD
- https://www.mouser.com/ProductDetail/molex/10-11-2043/?qs=WHdfnlF6O9swyDlL62cnjQ%3D%3D&countrycode=US&currencycode=USD
- https://www.mouser.com/ProductDetail/molex/50-29-1900-cut-strip/?qs=SMSg0crzni%252bi9hjs4v%252bjvg%3D%3D&countrycode=US&currencycode=USD

### Trimpots

Really, any trimpot 10k or higher will work, as long as it has .1" pitch with three inline pins. The Bourns 3296W is what the
board was designed for. 

https://www.mouser.com/Passive-Components/Potentiometers-Trimmers-Rheostats/Trimmer-Resistors-Through-Hole/_/N-9q0yt?P=1yzekj2&Keyword=bourns+20k+trimpot+3296w&FS=True

### Switch

This is just the usual PTH momentary switch that gets used in Arduino projects.

https://www.sparkfun.com/products/97

### LEDs and resistors

Take your pick - these are the normal fare. The resistors are all normal axial lead, 1/4 watt resistors. I used 1%, but since
you're trimming these, you can use 5% or even 10%, if you like. 
