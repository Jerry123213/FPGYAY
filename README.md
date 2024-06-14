# FPGYAY
In progress Artix-7 Based FPGA development board

## Current Progress

## TODO
- Add JTAG connection (not from USB)
- 

## Power Distribution
The Artix 7 requires 3 different voltages, and the DDR3L memory requries another voltage level:
1. VCCINT and VCCBRAM: 1V, internal voltage for CLBs and on chip RAM
2. VCCAUX: 1.8V, auxillary voltage for other internal circuitry
3. VCCIO: ~1.2V - ~3.3V, voltage for IO banks (one for each bank)
4. DDR3L: 1.35V, voltage for DDR3 low power

In addition, there is a recommended power-up sequence specified by Xilinx: VCCINT -> VCCBRAM -> VCCAUX -> VCCO

There are other VCC pins, but they can share the same voltage level (although not the same source) as the ones listed above. This board will use a quad-buck converter chip to step down 5V (either from USB VBus or external power supply) to all desried levels. In addition, the enable pin for each individual buck will connected to the voltage rail of the previous voltage level to ensure proper power sequencing. The specific chip used here is the ADP5052, and the calculations for component values are outlined in the datasheet. This chip also has a fifth voltage output pin which supplies voltage from an LDO. This pin is unused for now. 

Below is the completed schematic page for the quad-buck converter

![image](https://github.com/Jerry123213/FPGYAY/assets/65368615/1491945d-57e8-4511-b0a1-8486f8cb4a76)


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
