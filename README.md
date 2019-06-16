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

![alt text](https://github.com/jvnsep/Project3OLEDDisplay/blob/master/result/circuit.png "Circuit Diagram")

Materials:
1. Teensy 3.2 Microcontroller
2. 128x64 OLED display SDD1306 I²C version
3. 10kohm potentiometer
4. 2pcs push button switches

### c. Program Design

![alt text](https://github.com/jvnsep/Project3OLEDDisplay/blob/master/result/program.png "Program Flow Chart")

## 3. Results

### a. Pins Assigment

| OLED Pin No. | Function    | Teensy Pin No. |
|:-------------:|:------------|:--------------:|
|GND	|ground	|GND |
|VCC	|supply	|3.3v |
|SCL	|serial clock	|SCL0|
|SDA  |serial data |SDA0 |
|	|up button	|12 |
|	|down button	|9|
| |potentiometer |A9|

### b. Finite State

#### 1. Idle/Start State


#### 2. Running State


#### 3. Paused State


#### 4. Game over/End State


## 4. Discussions

### a. Circuit

From circuit diagram above, the main component aside from teensy microcontroller, is 128x64 OLED display SDD1306 with I²C serial bus. This OLED display have 4 pins, they are Vcc supply 3.3v, GND, SCL (serial clock) and SDA (serial data). Each of this pins connected to corresponding pins of Teensy 3.2 Microcontroller. Game control conections are the two push buttons connected to input pin will be use as up and down as well as start, pause and reset game control. additionally, potentiometer use to manipulate left and right control. The actual circuit is shown below image.

Actual Circuit: 
![alt text](https://github.com/jvnsep/Project3OLEDDisplay/blob/master/result/picture.png "Circuit Picture")
