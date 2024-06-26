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
The main voltages (1-4 from above) are produced by stepping down 5V either from USB or a barrel jack using a quad-buck converter. The specific one used is the ADP5052, and calculations are provided on the datasheet. The parameters used in the calculations are as follows:
- 

In addition, there is logic to prevent output contention if both a barrel jack and USB connector are connected. 


## Ethernet Interface 
The schematic for the interface itself is completed, but the pins on the FPGA have not been chosen yet (this depends purely on PCB layout considerations)

This board will be compatable with 10/100Base-T, with an RMII interface between the FPGA and PHY. The PHY chosen is the DP83848 and will be configured to run at 100Base-T by the bootstrap pins specified in the datasheet. The PHY address can be configured in hardware by using the DIP switches. Each pin connected to the DIP switch has a weak internal pulldown resistor, so an open switch is low, and closed switch is high. Alternatively, both the PHY address and speed can be programmed through the MDIO interface, but must be programmed each time the board is powered on. 

The 50Mhz clock for RMII is provided by a CMOS oscillator. 

Below is the completed schematic page for the RMII interface

![image](https://github.com/Jerry123213/FPGYAY/assets/65368615/088b04b5-96a6-4d7f-b282-79d82e6af0a1)

## USB Interface 
The USB bus connects to a USB to bridge (FT2232HQ), which will be configured for JTAG and UART. The USB interface only supports USB 2.0, although a USB 3.0 interface may be added in the future. The FT2232HQ is configured using FTDI's propreitary software, after which the configuration is stored in EEPROM. 

Below is the completed schematic page for the USB interface. Note that there are some parts that are incomplete (no reference voltage for XADC on FPGA), but they are independent from the USB interface, they just happen to be on the same schematic symbol

![image](https://github.com/Jerry123213/FPGYAY/assets/65368615/f0cd28b9-ca86-4b23-b3dd-4d46f42ed582)

## Audio Interface 
The audio interface is provided by the LM4550B audio codec, which is widely used in consumer electronics. There will be three connectors: Line In, Line Out and Mic In. The LM4550B communicates with the FPGA over AC97'. The LM4550B has two voltage rails, 5V analog and 3.3V digital, and the datasheet recommends seperate analog and digital grounds. However, I have kept analog and digital grounds the same in order to reduce complexity, and minimize possible error, as split grounds require more complex routing, and are often unnessasary. 

Below is the schematic of the completed audio interface 

![image](https://github.com/Jerry123213/FPGYAY/assets/65368615/d0eeb176-5b55-455a-87d4-4baca390e1a5)

## DDR3L Interface
The board will use a DDR3 interface, but specifically use DDR3 low power in order to reduce power consumption. The connections to the FPGA are temporary, as it largely depends on the physical layout of the components and which pins make the most sense to route where. However, it's recommended to keep each bus in the same FGPA byte group.

Below is the schematic for the DDR3 interface with temporary connections to the FPGA

![image](https://github.com/Jerry123213/FPGYAY/assets/65368615/f6efbd96-d907-4ab3-9172-a541a80ad89c)

 
