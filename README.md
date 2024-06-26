# FPGYAY
In progress Artix-7 Based FPGA development board, currently in schematic capture phase 

## Power Distribution
The Artix 7 itself requires 3 different voltages, and the DDR3L memory requries another two voltage levels:
1. VCCINT and VCCBRAM: 1V, internal voltage for CLBs and on chip RAM
2. VCCAUX: 1.8V, auxillary voltage for other internal circuitry
3. VCCIO: ~1.2V - ~3.3V, voltage for IO banks (one for each bank)
4. DDR3L: 1.35V, voltage for DDR3 low power
5. DDR VREF: 1/2 DDR3L (0.675V)

In addition, there is a recommended power-up sequence specified by Xilinx: VCCINT -> VCCBRAM -> VCCAUX -> VCCO

Below is the completed schematic for the main power distribution system 

![image](https://github.com/Jerry123213/FPGYAY/assets/65368615/499739c8-85f9-4b9f-a947-bfd6391e638e)
The main voltages (1-4 from above) are produced by stepping down 5V either from USB or a barrel jack using a quad-buck converter. The specific one used is the ADP5052, and formulas are provided on the datasheet. The parameters used in the calculations are as follows:

- Switching Frequency: ~600kHz
- Max Ouput Current: 8A
- Step Transient: 60% of max current
- Output Ripple: ±1% of max current 

In addition, there is logic to prevent output contention if both a barrel jack and USB connector are connected. The barrel jack always has priority, if it is connected, then the 5V power rail from the USB connector is disabled. The logic is schematic below: 

![image](https://github.com/Jerry123213/FPGYAY/assets/65368615/8dfb406b-65a0-4b4c-8801-849ca9a39297)

The barrel jack is assume to be around 12V, stepping it down to 5V. Due to the output contention prevention above, the 5V input into the quad-buck is shared among both input modes. 

## Ethernet Interface 
The schematic for the interface itself is completed, but the pins on the FPGA have not been chosen yet (this depends purely on PCB layout considerations)

The PHY chosen is the DP83848, which is RMII/MII compatible, which means it is also 10/100Base-T compatible. This board will use an RMII interface between the FPGA and PHY. The PHY will be configured to run at 100Base-T by the bootstrap pins specified in the datasheet. The PHY address can be configured in hardware by using the DIP switches. Each pin connected to the DIP switch has a weak internal pulldown resistor, so an open switch is low, and closed switch is high. Alternatively, both the PHY address and speed can be programmed through the MDIO interface, but must be programmed each time the board is powered on. 

The 50Mhz clock for RMII is provided by a CMOS oscillator. 

Below is the completed schematic page for the RMII interface

![image](https://github.com/Jerry123213/FPGYAY/assets/65368615/c4e91a69-209c-4720-9682-d300be647374)

The yellow LED indicates a good link when static, and data transfer when blinking. The green LED should always be off. 

## USB Interface 
The USB bus connects to a USB to bridge (FT2232HQ), which will be configured for JTAG and UART. The USB interface only supports USB 2.0, although a USB 3.0 interface may be added in the future. The FT2232HQ is configured using FTDI's propreitary software, after which the configuration is stored in EEPROM. 

Below is the completed schematic page for the USB interface. 

![image](https://github.com/Jerry123213/FPGYAY/assets/65368615/36512ebd-f8ba-4a5c-9a3f-b7d3840a09b8)

## Configuration Setup
The configuration is set by configuration pins M0_[0:2], the default value should be set to 001, although the option to set them with DIP switches is included. The default configuration is the Master SPI configuration, which also allows for JTAG programming. The configuration schematic is found above with the USB interface. 

## Audio Interface 
The audio interface is provided by the LM4550B audio codec, which is widely used in consumer electronics. There will be three connectors: Line In, Line Out and Mic In. The LM4550B communicates with the FPGA over AC97'. The LM4550B has two voltage rails, 5V analog and 3.3V digital, and the datasheet recommends seperate analog and digital grounds. However, I have kept analog and digital grounds the same in order to reduce complexity, and minimize possible error, as split grounds require more complex routing, and are often unnessasary. 

Below is the schematic of the completed audio interface 

![image](https://github.com/Jerry123213/FPGYAY/assets/65368615/7f8312e2-1b1c-4f4d-ad38-d76b723d8e0d)

## DDR3L Interface
The board will use a DDR3 interface, but specifically use DDR3 low power in order to reduce power consumption. In addition, a reference voltage 

Below is the schematic for the DDR3 interface with temporary connections to the FPGA

![image](https://github.com/Jerry123213/FPGYAY/assets/65368615/f6efbd96-d907-4ab3-9172-a541a80ad89c)

 
