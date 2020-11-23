# tinygo-stusb4500
### TinyGo driver for real-time capabilities of the STUSB4500 USB PD sink controller with I²C

###### Rewrite of Arduino driver [STUSB4500](https://github.com/ardnew/STUSB4500)

----

## Features
- [x] Re-written entirely in pure Go
  - Improved stability and compatibility over Arduino predecessor [STUSB4500](https://github.com/ardnew/STUSB4500)
- [x] Tested on TinyGo 1.14, 1.15, 1.16
- [x] **Compatible with all I²C-capable [devices supported by TinyGo](https://github.com/tinygo-org/tinygo#supported-boardstargets)**
- [x] USB Power Delivery v2.0 and v3.0 compatible state machine
- [x] Designed for [STUSB4500 Compact Breakout](https://www.tindie.com/products/oxplot/stusb4500-compact-breakout/)
   - Same chip used on the [SparkFun Power Delivery Board](https://www.sparkfun.com/products/15801), although it hasn't been tested. Feel free to send me one!
- [x] Detect cable orientation (CC1/CC2 termination)
- [x] Completely enable/disable power output
- [x] Provides user callback support for:
   |Callback    |Description
   |:-----------|:-------------------------|
   |`OnInitFail`|I²C initialization failure|
   |`OnResetFail`|I²C reconnection to STUSB4500 failed|
   |`OnConnect`|I²C connection to STUSB4500 succeeded|
   |`OnConnectFail`|I²C connection to STUSB4500 failed|
   |`OnError`|I²C or STUSB4500 runtime error|
   |`OnCableAttach`|USB Type-C cable connected|
   |`OnCableDetach`|USB Type-C cable disconnected|
   |`OnCapabilities`|USB PD capabilities received from source|
   
- [x] Can analyze and identify all available power profiles
- [x] Can request and use any arbitrary power profile +5-20V/0-5A in 50mV/10mA increments
   - Does not lose VBUS on power transition, maintains power output
- [x] Set up to 3 custom power profiles for fallback
- [ ] Reformat NVM for default power profiles in standalone mode (not yet implemented)

## Installation

1. [Install TinyGo](https://tinygo.org/getting-started/), of course.
2. [Fetch the TinyGo drivers](https://github.com/tinygo-org/drivers/):
   - `go get tinygo.org/x/drivers`
3. If this driver has been merged into that repository, you are done with installation.
4. Navigate into new `drivers` installation directory:
   - `cd $( go env GOPATH )/src/tinygo.org/x/drivers`
5. Fetch this driver, naming it `stusb4500`:
   - `git clone https://github.com/ardnew/tinygo-stusb4500.git stusb4500`

## Usage

### Wiring


### Troubleshooting (from previous Arduino project)
> The PD protocol has *very* rigid timing requirements. These can be difficult to accommodate even in normal circumstances, but is even more difficult since there is another device (our STUSB4500) mediating communication over an I²C bus, which is rather slow. This is partly why the interrupts are vital to successful operation.
> 
> Point being, it may be difficult to achieve reliable results on some systems (such as AVR-based devices like Arduino Uno), and you may need to experiment with different microcontrollers. 
> 
> If you have issues receiving cable attach/detach or PD capability discovery events, ensure the following:
> - You are using a **hardware** I²C port on the microcontroller
> - The I²C (SDA/SCL) and interrupt (ALRT/ATCH) wires connecting microcontroller and STUSB4500 are as short as possible
> - GND on STUSB4500 is connected to GND on microcontroller
> - VDD (VPP on oxplot breakout) is pulled up to 3.3V (or to same level as I2C bus voltage)
> - VSYS (VCC on oxplot breakout) is pulled down to ground (use microcontroller ground pin)
>   - The Sparkfun breakout pulls this high to VDD ([according to their schematic](https://cdn.sparkfun.com/assets/9/2/6/8/6/SparkFun_PowerDeliveryBoardSchematic.pdf)), but the [datasheet](https://www.st.com/resource/en/datasheet/stusb4500.pdf) says to connect it to ground if it is not used (and it isn't used, because the device is powered by VBUS from the USB-C receptacle). So I'm not sure about this one. Try pulling this in the opposite direction if you're having issues.
