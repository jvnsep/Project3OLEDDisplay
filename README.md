# Project 3 OLED Display

## Abstract
This paper is about the process of developing a computer game in embedded system. The program in C++ involves Adafruit_SSD1306 and Adafruit_GFX Library and understand how to use it. Make use and implement finite system machine (FSM) concept on this program. The design circuitry and connection of the system mainly with Teensy microcontroller and OLED display. Also, the need manipulators which control for games are push buttons and potentiometer.

## 1. Introduction
The project design an embedded system of a simple computer game. The project show how programming proceed and understand importance charateristic of the circuit. The game design and implement finite system machine (FSM) concept which display state of start screen, end screen, pause screen and running. With this concept it introduce the programming syntax of enum and switch case. The circuit comprises of Teensy Microcontroller, a a 128x64 OLED display SDD1306 with I²C serial bus, 10kohm potentiometer and two push button switches. The circuit design require pins for I²C serial bus, serial clock (SCL) and serial data (SDA) connection to microcontroller. Futhermore, game control by push button switches (up and down) and potentiometer (left and right).

## 2. Method

### a. Project Development

![alt text](https://github.com/jvnsep/Project3OLEDDisplay/blob/master/result/flow.png "Development Flow Chart")

### b. Circuit Design
Circuit Diagram: 
![alt text](https://github.com/jvnsep/Project3OLEDDisplay/blob/master/result/schematic%20diagram.pdf "Circuit Diagram")

Materials:
1. Teensy 3.2 Microcontroller
2. QDSP-6064 4-Digits 7-Segments LED display
3. 4pcs IRLU8743PbF Power MOSFETs
2. 8pcs. 390Ω resistors connected from μC pins to anodes LED display
3. 4pcs. 1kΩ resistors conneted to drain of mosfets
4. 2pcs push button switches connected from μC pins to ground 
