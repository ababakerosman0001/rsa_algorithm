RSA Encryption & Decryption on Zynq FPGA
Project Overview

This project implements a simplified RSA cryptographic system on a Zynq-based FPGA platform.

The system allows a user to:

Encrypt an integer message

Decrypt an integer message

Toggle between operational modes using a push button

Display results on onboard LEDs

The design demonstrates hardware-software co-design where:

The FPGA fabric handles state control and I/O

The Zynq processor (ARM core) performs RSA modular exponentiation

System Description

The system processes inputs from:

Push Button → Controls system state

Switches → Provide input message

LEDs → Display encryption/decryption result

Operating States

The system toggles between three states:

State	Description
State 0	Idle (Do Nothing)
State 1	Encrypt Mode
State 2	Decrypt Mode
State Transitions

System starts in State 0

First button press → Switch to Encrypt mode

Second button press → Switch to Decrypt mode

Third button press → Reset back to Idle

RSA Configuration

For demonstration purposes, the RSA parameters are:

Modulus (n) = 15
Encryption exponent (e) = 7
Decryption exponent (d) = 3


Note: These small parameters are used for educational demonstration only and are not secure for real-world cryptography.

Mathematical Background

RSA encryption:

𝐶
=
𝑀
𝑒
m
o
d
 
 
𝑛
C=M
e
modn

RSA decryption:

𝑀
=
𝐶
𝑑
m
o
d
 
 
𝑛
M=C
d
modn

Modular exponentiation is implemented using the efficient square-and-multiply algorithm.

System Architecture
Hardware–Software Interaction
Push Button / Switches
          ↓
        FPGA
  (State Control Logic)
          ↓
     Zynq ARM Core
 (RSA Computation in C)
          ↓
        FPGA
          ↓
         LEDs

Design Responsibilities
FPGA

Detect push button input

Maintain state machine (Idle / Encrypt / Decrypt)

Send input data to processor

Display output on LEDs

Zynq Processor

Perform modular exponentiation

Return encrypted/decrypted result

Pure RSA C Implementation

A standalone C implementation was first developed and tested using:

Modular exponentiation

Console-based input

Encryption/decryption selection

This ensured algorithm correctness before hardware integration.

Zynq Implementation (Vitis)

The RSA algorithm was ported to the Zynq processor using:

xgpio.h

xparameters.h

GPIO configuration:

Button channel → Input

LED channel → Output

The system continuously:

Reads button state

Updates encryption/decryption mode

Computes RSA operation

Displays result on LEDs

Build was successfully completed in Vitis.

Example Results
Example 1

Encrypting message = 9

Example 2

Decrypting message = 2

Example 3

Encrypting message = 3

The LED output correctly reflected modular exponentiation results.

Tools & Technologies

Zynq FPGA platform

Xilinx Vitis

C programming

GPIO interfacing

Finite State Machine design

Modular arithmetic implementation

Educational Objectives

This project demonstrates:

FPGA-based state machine design

Hardware–software co-design

Embedded C programming on Zynq

Implementation of modular exponentiation

Practical understanding of RSA fundamentals

Limitations

Small modulus size (educational only)

No large integer arithmetic

No real-world security application

Future Improvements

Implement larger RSA key sizes

Add UART interface for message input

Implement hardware acceleration for modular exponentiation

Add debouncing logic for push button

Expand LED display to seven-segment or LCD
