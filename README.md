# FPGYAY
In progress Artix-7 Based FPGA development board

## Current Progress

## TODO

## Ethernet Interface 
The schematic for the interface itself is completed, but the pins on the FPGA have not been chosen yet (this depends purely on PCB layout considerations)

This board will be compatable with 10/100Base-T, with an RMII interface between the FPGA and PHY. The PHY chosen is the DP83848 and will be configured to run at 100Base-T by the bootstrap pins specified in the datasheet. The PHY address can be configured in hardware by using the DIP switches. Alternatively, both the PHY address and speed can be programmed through the MDIO interface, but must be programmed each time the board is powered on. 

Below is the completed schematic page for the RMII interface

![image](https://github.com/Jerry123213/FPGYAY/assets/65368615/088b04b5-96a6-4d7f-b282-79d82e6af0a1)
