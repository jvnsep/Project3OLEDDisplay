# Project 3 OLED Display

## Abstract
This paper is about the process of developing a computer game in embedded system. The program in C++ involves Adafruit_SSD1306 and Adafruit_GFX Library and understand how to use it. Make use and implement finite system machine (FSM) concept on this program. The design circuitry and connection of the system mainly with Teensy microcontroller and OLED display. Also, the need manipulators which control for games are push buttons and potentiometer.

## 1. Introduction
The project is to design an embedded system of a simple computer game titled "balloon vs birds". The project show how programming proceed and understand importance charateristic of the circuit. The game design and implement finite system machine (FSM) concept which display state of start screen, end screen, pause screen and running. With this concept it introduce the programming syntax of enum and switch case. The circuit comprises of Teensy Microcontroller, a a 128x64 OLED display SDD1306 with I²C serial bus, 10kohm potentiometer and two push button switches. The circuit design require pins for I²C serial bus, serial clock (SCL) and serial data (SDA) connection to microcontroller. Futhermore, game control by push button switches (up and down) and potentiometer (left and right).

## 2. Method

### a. Project Development
<p align="center">
  <img src="https://github.com/jvnsep/Project3OLEDDisplay/blob/master/result/flow.png" width="400" height="400">
</p>
  
### b. Circuit Design
Circuit Diagram: 
<p align="center">
  <img src="https://github.com/jvnsep/Project3OLEDDisplay/blob/master/result/circuit.png" width="500" height="500">
</p>
  
Materials:
1. Teensy 3.2 Microcontroller
2. 128x64 OLED display SDD1306 I²C version
3. 10kohm potentiometer
4. 2pcs push button switches

### c. Program Design
<p align="center">
  <img src="https://github.com/jvnsep/Project3OLEDDisplay/blob/master/result/program.png" width="400" height="400">
</p>

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

### b. Finite State Machine

#### 1. Start-Screen : idle state
<img src="https://github.com/jvnsep/Project3OLEDDisplay/blob/master/result/1%20start.png" width="300" height="300">

#### 2. Game-Screen : running state
<img src="https://github.com/jvnsep/Project3OLEDDisplay/blob/master/result/2%20run.png" width="300" height="300">

#### 3. Pause-Screen : paused state
<img src="https://github.com/jvnsep/Project3OLEDDisplay/blob/master/result/3%20pause.png" width="300" height="300">

#### 4. End-Screen : game-over state
<img src="https://github.com/jvnsep/Project3OLEDDisplay/blob/master/result/4%20end.png" width="300" height="300">

## 4. Discussions

### a. Circuit

From circuit diagram above, the main component aside from teensy microcontroller, is 128x64 OLED display SDD1306 with I²C serial bus. This OLED display have 4 pins, they are Vcc supply 3.3v, GND, SCL (serial clock) and SDA (serial data). Each of this pins connected to corresponding pins of Teensy 3.2 Microcontroller. Game control conections are the two push buttons connected to input pin will be use as up and down as well as start, pause and reset game control. additionally, potentiometer use to manipulate left and right control. The actual circuit is shown below image.

Actual Circuit: 
![alt text](https://github.com/jvnsep/Project3OLEDDisplay/blob/master/result/picture.png "Circuit Picture")


### b. Program

SPI and Wire library provide communicate, and Adafruit_GFX and Adafruit_SSD1306 for graphic
#include <Arduino.h>
#include <SPI.h>
#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>
