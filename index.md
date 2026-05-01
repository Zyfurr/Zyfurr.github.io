---
layout: page
title: Lab 4
---

# Lab 4: Reworking Labs 1 and 2 Using Jupyter Notebooks

## Description

In this lab, you will be reworking labs 1 and 2 using Jupyter Notebooks instead of Vitis. Jupyter Notebooks is an interactive computing environment that enables users to edit and run code through the use of notebooks. The code within these notebooks can be run in chunks for easy debugging, with the outputs readily available to you. Jupyter notebooks can be used for many things other than coding such as creating plots, interactive widgets, etc. Connection to your AMD FPGA through Jupyter Notebooks provides a more abstracted interface to allow users with less hardware background to program on an FPGA. In this lab you will learn to use this powerful environment to interface with your AMD FPGA from hardware intialization to running your matrix multiply application on the implemented microprocessor. 

## Lab Objectives

1. Download, flash, and boot your AMD FPGA using AMD PYNQ
2. Access Jupyter Notebooks
3. Initialize and synthesize your hardware design
4. Run Lab 1's "Hello World" application
5. Modify and Run Lab 2's matrix multiply application

## AMD PYNQ

{: .info-title}
> PYNQ INFO: LINUX, JUPYTER, PYTHON INFO

## Directions

### Flashing Your FPGA Board with AMD PYNQ
1. Navigate to the URL: [https://www.pynq.io/boards.html](https://www.pynq.io/boards.html)
2. Retrieve your micro sd card and ensure it is at least 16 GB.
3. Download the corresponding image from the website. The image should be for the AUP-ZU3 board. For the specific version there should be a sticker near the processors cooler noting whether it is the 8 GB model or 4 GB model.
4. Once downloaded unzip the folder.
5. Insert your micro sd card into your sd card reader connected to the computer. Ensure that the sd card is empty.
6. Navigate to your desired sd card flashing program of choice, for a free option you can use [Balena Etcher](https://etcher.balena.io/). Choose the file within the unzipped folder as your image and flash it to your sd card.
7. Once flashing is finished, connect your FPGA to power using one of the provided USB-C cables. With the other connect it to the DRP I USB 3.0 port and then to your computer, this cable will provide a network over USB-C to communicate from Jupyter Notebooks to your FPGA. Ensure that the power switch is switched to OFF and the boot switch is on SD mode.
8. Insert the sd card containing the PYNQ image into the FPGA and power on the board with the power switch.
9. At this point the board will go through a process to boot from the image on the sd card. The FPGA will go through a cycle of LEDs blinking.
    1. The PS LED1 should be blinking in a heart beat pattern.
    2. After ~30 seconds the DONE LED located near the USB 3.0 ports should be lit up solid green. Additionally the white LEDs located near the dip switches at the bottom of the board should light up.
10. Once these steps are finished the board is ready to be interfaced with!

### Accessing Jupyter Notebooks
1. Now that your FPGA board is booted up and running PYNQ you can navigate to [http://192.168.3.1/lab](http://192.168.3.1/lab) to open Jupyter Notebooks.
2. You shold be prompted for a password if everything is connected correctly. The password is "xilinx".
3. You should now be greeted with the homepage of Jupyter. You can take some time and read through the different beginner resource folders located in the home directory. Once ready you can move on to the next step.

### Implementing Your Hardware Design Through Jupyter Notebooks
## PYNQ Overlay

{: .info-title}
> PYNQ INFO: LINUX, JUPYTER, PYTHON INFO

1. To get your hardware running on the FPGA you need to navigate to your Vivado projects home directory. Once there you need to locate your exported bitstream you generated from lab 1. This file should be located at `[project directory]/[project name].runs/impl_1/` and have the file ending .bit.
2. Once located drag this file into your Jupyter Notebooks home directory.
3. Now you need to grab your Hardware Handoff (HWH) file for your project. This file is used to tell PYNQ specifics about your hardware design like what IPs are being used. This file should be located at `[project directory]/[project name].gen/sources_1/bd/[project name]/hw_handoff/` and have the file ending .hwh.
4. Once located drag this file into your Jupyter Notebooks home directory as well.
5. Now that you have all the necessary hardware files to configure your FPGA you need a way to implement them on the board. Navigate to the top of the home dashboard in Jupyter and hit the blue plus sign. Add a new Python3 notebook and rename it to "lab4_init.ipynb".
6. In this new notebook you are able to write code and run it in chunks. Click in the open box in the text editor and paste the code provided below.
   ```python
        from pynq import Overlay
        overlay = Overlay("[bitstream file name].bit")
    ```
7. This simple code snippet is all that is needed to implement your hardware design. Select your code snipped by clicking on the brackets next to it and hit the run button located at the top of the text editor. If you get no error messages you have successfully loaded your hardware design.

### Running Your First Application
1. Since the image you loaded onto your FPGA on the micro sd card contained a linux implementation you are now able to use a linux command line to compile and run programs on the FPGA. To do this first create a new text file in the home directory named 'Lab4.cpp'.
2. In this file paste and finish the following code:
    ```c
     #include <iostream>
        
     int main(void) {
       // Print "Hello Zynq!" to the console.
            
       return 0;
     }
    ```
3. Now add a new terminal in your notebook. You can now interface with the FPGA using a linux command line. Lucky for us this linux build comes with the g++ compiler pre-installed so all we need to do now is run the command `g++ Lab4.cpp`. You might need to navigate to the Jupyter Notebook directory if you aren't already there. This directory is located at `/home/xilinx/jupyter_notebooks`.
4. Once our project is done compiling run the command `./a.out`. You should see the output in the terminal "Hello Zynq!".
5. Congratulations! You've officially run your first program on your FPGA using Jupyter Notebooks.

### Modifying and Running Your Matrix Multiplication App
1. To re-implement lab 2 on Jupyter you can use much of the same code as you previously used with one key change. You need to replace the xiltimer.h library with the standard time library provided by C, time.h. The code for this program is provided below. This slightly modified lab 2 app will double the matrix size 5 times and run the desired matrix multiply algorithm each time as well as reporting how long the computation took to complete.
    ```c
    #include <stdio.h>
    #include <stdlib.h>
    #include <string.h>
    #include <time.h> 
    #include <iostream>
    
    #define IDX(i, j, N) ((i) * (N) + (j))
    #define TILE_SIZE 32
    
    
    void naive_matrix_multiply(int N, const float* A, const float* B, float* C);
    
    void cache_aware_matrix_multiply(int N, const float* A, const float* B, float* C);
    
    void tiled_matrix_multiply(int N, const float* A, const float* B, float* C);
    
    void fill_matrix(int N, float* buffer);
    
    void init_random() {
        srand(time(NULL));
    }
    
    
    int main() {
        int N = 64; // Matrix size
        init_random();
   
    // Benchmark the three implementations here:
    clock_t c;
    for (int i = 0; i < 5; ++i) {
        size_t matrix_size = (size_t)N * (size_t)N;
        float* A = (float*)malloc(matrix_size * sizeof(float));
        float* B = (float*)malloc(matrix_size * sizeof(float));
        float* C = (float*)malloc(matrix_size * sizeof(float));
        if (A == NULL || B == NULL || C == NULL) {
            printf("Failed to allocate matrices for N = %d\n", N);
            free(A);
            free(B);
            free(C);
            return 1;
        }
        
        fill_matrix(N, A);
        fill_matrix(N, B);
        memset(C, 0, matrix_size * sizeof(float)); // Initialize C to zero
        printf("N = %d, Tile_Size = %d\n", N, TILE_SIZE);
        
        c = clock();
        
        // Call the desired matrix multiplication function here, e.g.:
        // naive_matrix_multiply(N, A, B, C);
        // cache_aware_matrix_multiply(N, A, B, C);
        // tiled_matrix_multiply(N, A, B, C);
        
        c = clock() - c;
        
        printf("Elapsed time for tiled: %f seconds\n", ((float)c) / CLOCKS_PER_SEC);

        free(A);
        free(B);
        free(C);
        N *= 2;
    }

    return 0;
    }
    
    void naive_matrix_multiply(int N, const float* A, const float* B, float* C) {
        for (int i = 0; i < N; i++) {
            for (int j = 0; j < N; j++) {
                C[IDX(i, j, N)] = 0;
                for (int k = 0; k < N; k++) {
                    C[IDX(i, j, N)] += A[IDX(i, k, N)] * B[IDX(k, j, N)];
                }
            }
        }
    }
    
    void cache_aware_matrix_multiply(int N, const float* A, const float* B, float* C) {
        for (int i = 0; i < N; ++i) {
            for (int k = 0; k < N; ++k) {
                float r = A[IDX(i, k, N)];
                for (int j = 0; j < N; ++j) {
                    C[IDX(i, j, N)] += r * B[IDX(k, j, N)];
                }
            }
        }
    }
    
    void tiled_matrix_multiply(int N, const float* A, const float* B, float* C) {
        for (int i = 0; i < N; i += TILE_SIZE) {
            for (int j = 0; j < N; j += TILE_SIZE) {
                for (int k = 0; k < N; k += TILE_SIZE) {
                    // Compute the tile C[i:i+TILE_SIZE][j:j+TILE_SIZE]
                    for (int ii = i; ii < i + TILE_SIZE && ii < N; ++ii) {
                        for (int kk = k; kk < k + TILE_SIZE && kk < N; ++kk) {
                            float r = A[IDX(ii, kk, N)];
                            for (int jj = j; jj < j + TILE_SIZE && jj < N; ++jj) {
                                C[IDX(ii, jj, N)] += r * B[IDX(kk, jj, N)];
                            }
                        }
                    }
                }
            }
        }
    }
    
    void fill_matrix(int N, float* buffer) {
        init_random();
        for (int i = 0; i < N; ++i) {
            for (int j = 0; j < N; ++j) {
                buffer[IDX(i, j, N)] = rand();
            }
        }
    }
    ```
2. You are now able to test your different matrix multiplication methods using Jupyter Notebooks.


{: .new-title }
> Congratulations!
>
> You've successfully reworked labs 1 & 2 using Jupyter Notebooks. You now have access to a powerful alternative way of interfacing with your FPGA board.

## Troubleshooting

If you encounter any issues during this lab, here are some common troubleshooting steps:
1. Empty out your SD card and re-flash it.
2. If Jupyter Notebooks can't connect to the FPGA it might not be connected to your computer. You can connect it either via USB-C to USB-C cable using the DRP 1 port or via wifi adapter on the same port. 
