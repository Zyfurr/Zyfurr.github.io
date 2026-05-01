---
layout: page
title: Lab 1
---

# Lab 4: Labs 1 and 2 Using Jupyter Notebooks

## Description

In this lab, you will be reworking labs 1 and 2 using Jupyter Notebooks instead of Vitis. Jupyter Notebooks is an interactive computing environment that enables users to edit and run code through the use of notebooks. The code within these notebooks can be run in chunks for easy debugging, with the outputs readily available to you. Jupyter notebooks can be used for many things other than coding such as creating plots, interactive widgets, etc. Connection to your AMD FPGA through Jupyter Notebooks provides a more abstracted interface to allow users with less hardware background to program on an FPGA. In this lab you will learn to use this powerful environment to interface with your AMD FPGA from hardware intialization to running your matrix multiply application on the implemented microprocessor. 

## Lab Objectives

1. Download, flash and boot your AMD FPGA using AMD Pynq
2. Access Jupyter Notebooks
3. Initialize and synthesize your hardware design
4. Run Lab 1's Hello World application
5. Modify and Run Lab 2's matrix multiply application

## Before You Start

{: .info-title}
> Before you open Vivado, you will need to install the AUP-ZU3 board files. These files contain the necessary information for Vivado to recognize the AUP-ZU3 board and its components, such as the FPGA, ARM processor, and peripherals. Without these files, you will not be able to create a hardware design for the AUP-ZU3 board in Vivado.
> 
> 1. Download the AUP-ZU3 board files from the following link: [AUP-ZU3 Board Files](https://www.github.com/RealDigitalOrg/aup-zu3-bsp)
> 2. Extract the downloaded files to a location on your computer where you can easily access them.
> 3. Copy the extracted `board_files` folder to the following directory: `{VITIS_INSTALL_ROOT}\Vivado\2025.2\data\boards\`. For example, if you installed Vivado in `C:\Xilinx\Vivado\2025.2`, you would copy the `board_files` folder to `C:\Xilinx\Vivado\2025.2\data\boards\`.

## Directions

### Flashing FPGA Board with AMD Pynq
1. Navigate to the URL: [https://www.pynq.io/boards.html](https://www.pynq.io/boards.html)
2. Retrieve your micro sd card and ensure it is at least 16 GB.
3. Download the corresponding image from the website. The image should be for the AUP-ZU3 board. For the specific version there should be a sticker near the processors cooler noting whether it is the 8 GB model or 4 GB model.
4. Once downloaded unzip the folder.
5. Insert your micro sd card into your sd card reader connected to the computer. Ensure that the sd card is empty.
6. Navigate to your desired sd card flashing program of choice, for a free option you can use [Balena Etcher](https://etcher.balena.io/). Choose the file within the unzipped folder as your image and flash it to your sd card.
7. Once flashing is finished connect your FPGA to power using one of the provided USB-C cables. With the other connect it to the DRP I USB 3.0 port and then to your computer, this cable will provide a network of USB-C to communicate from Jupyter Notebooks to your FPGA. Ensure that the power switch is switched to OFF and the boot switch is on SD mode.
8. Insert the sd card containing the Pynq image into the FPGA and power on the board with the power switch.
9. At this point the board will go through a process to boot from the image on the sd card. The FPGA will go through a cycle of LEDs blinking.
    1. The PS LED1 should be blinking in a heart beat pattern.
    2. After ~30 seconds the DONE LED located near the USB 3.0 ports should be lit up solid green. Additionally the white LEDs located near the dip switches at the bottom of the board should light up.
10. Once these steps are finished the board is ready to be interfaced with!

### Accessing Jupyter Notebooks
1. Now that your FPGA board is booted up and running Pynq you can navigate to [http://192.168.3.1/lab](http://192.168.3.1/lab) to open Jupyter Notebooks.
2. You shold be prompted for a password if everything is connected correctly. The password is "xilinx".
3. You should now be greeted with the homepage of Jupyter. You can take some time and read through the different beginner resource folders located in the home directory. Once ready you can move on to the next step.

### Implementing Your Hardware Design Through Jupyter Notebooks
1. To get your hardware running on the FPGA you need to navigate to your Vivado projects home directory. Once there you need to locate your exported bitstream you generated from lab 1. This file should be located at `[project directory]/[project name].runs/impl_1/` and have the file ending .bit.
2. Once located drag this file into your Jupyter Notebooks home directory
3. Now you need to grab your Hardware Handoff (HWH) file for your project. This file is used to tell PYNQ specifics about your hardware design like what IPs are being used. This file should be located at `[project directory]/[project name].gen/sources_1/bd/[project name]/hw_handoff/` and have the file ending .hwh.
4. Once located drag this file into your Jupyter Notebooks home directory as well.
5. Now that you have all the necessary hardware files to configure your FPGA you need a way to implement them on the board. Navigate to the top of the home dashboard in Jupyter and hit the blue pluss sign. Add a new Python3 notebook and rename it to "Lab4.ipynb".
6. In this new notebook you are able to write code and run it in chunks. Click in the open box in the text editor and paste the code provided below.
   ```python
        from pynq import Overlay
        overlay = Overlay("[bitstream file name].bit")
    ```
7. This simple code sniped is all that is needed to implement your hardware design. Select your code snipped by clicken on the brackets next to it and hit the run button located at the top of the text editor. If you get no error messages you have successfully loaded your hardware design. 


{: .new-title }
> Congratulations!
>
> You've successfully built and run your first application on the Zynq SoC! In future labs, we will build more complex hardware designs and applications, but this is the foundation for everything we will do in this course.

## Next Steps

In the next lab, we will implement an example compute intensive application on the ARM processor and CPU optimization techniques, and benchmarking the performance of our application. 

## Troubleshooting

If you encounter any issues during this lab, here are some common troubleshooting steps:
1. If your platform is not available in Vitis after you created it, you may have to delete your workspace and start over. My Vitis has been glitching on new workspaces.
2. If you are having trouble with the serial monitor, make sure you have the correct COM port selected and that your baudrate is set to 115200.
