# Project 3 OLED Display

## Abstract
This paper is about the process of developing a computer game in embedded system. The program in C++ involves Adafruit_SSD1306 and Adafruit_GFX Library and understand how to use it. Make use and implement finite system machine (FSM) concept on this program. The design circuitry and connection of the system mainly with Teensy microcontroller and OLED display. Also, push button and potentiometer use as input control of this game.

## 1. Introduction
The project is to design an embedded system of a simple computer game titled "balloon vs birds". The project show how programming proceed and understand importance charateristic of the circuit. The game design and implement finite system machine (FSM) concept which display state of start screen, end screen, pause screen and running. With this concept it introduce the programming of enum and switch case. The circuit comprises of Teensy Microcontroller, 128x64 OLED display SDD1306 with I²C serial bus, 10kohm potentiometer and two push button switches. The circuit design require pins for I²C serial bus, serial clock (SCL) and serial data (SDA) connection to microcontroller. Futhermore, game control by push button switches (up and down) and potentiometer (left and right).

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

### d. Bitmap image coverted to hex by LCDAssistant application

#### 1. Start-Screen, logo and game title
<p align="center">
  <img src="https://github.com/jvnsep/Project3OLEDDisplay/blob/master/result/logo.bmp" width="400" height="200">
</p>

#### 2. balloon bitmap image
<p align="center">
  <img src="https://github.com/jvnsep/Project3OLEDDisplay/blob/master/result/balloon.bmp" width="50" height="50">
</p>

#### 3. bird bitmap image
<p align="center">
  <img src="https://github.com/jvnsep/Project3OLEDDisplay/blob/master/result/bird.bmp" width="50" height="50">
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
<p align="center">
 <img src="https://github.com/jvnsep/Project3OLEDDisplay/blob/master/result/1%20start.png" width="300" height="300">
</p>

#### 2. Game-Screen : running state
press two button simultaneously to pause
<p align="center">
 <img src="https://github.com/jvnsep/Project3OLEDDisplay/blob/master/result/2%20run.png" width="300" height="300">
</p>
	
#### 3. Pause-Screen : paused state
<p align="center">
 <img src="https://github.com/jvnsep/Project3OLEDDisplay/blob/master/result/3%20pause.png" width="300" height="300">
</p>

#### 4. End-Screen : game-over state
<p align="center">
 <img src="https://github.com/jvnsep/Project3OLEDDisplay/blob/master/result/4%20end.png" width="300" height="300">
 </p>
 
### c. Video 

Link: [Game Video](https://github.com/jvnsep/Project3OLEDDisplay/blob/master/result/game.mp4)

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

Assigned Pins for push buttons, potentiometer and OLED.

	// Declaration for an SSD1306 display connected to I2C (SDA, SCL pins)
	#define OLED_RESET     4 // Reset pin # (or -1 if sharing Arduino reset pin)
	Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, OLED_RESET);

	// constant set pin numbers:
	const uint8_t UP_BTN = 12;             // input pin for up button
	const uint8_t DN_BTN = 9;              // input pin for down button
	const uint8_t LR_PTN = A9;             // Analog input pin for potentiometer

Define variables, arrays, constant and state data type

	// contant and variables for balloon location (direction, and start, new and old position)
	const uint8_t STR_X = display.width()/8, STR_Y = display.height()/2;  // start position
	uint8_t new_x = STR_X, new_y = STR_Y;                           // current position
	int8_t dir_x = 0, dir_y = 0;                                    // direction
	uint8_t old_x = 0, old_y = 33;                                  // previous position

	bool hit_state = false; // indicate true if a bird position within balloon position

	// Indexes into the birds array variable
	#define XPOS   0            // x axis position
	#define YPOS   1            // y axis position
	#define XDIR   2            // x direction
	#define YDIR   3            // y direction

	int8_t arr_birds[10][4];    // array variables for birds position
	uint8_t f, n = 6;           // varible for count and number of birds

	int8_t life = 3;            // balloon life of game per play
	int8_t score = 0;           // birds pass the balloon

	unsigned long birds_delaytime = 500;        // initial value to be decrease so speed up birds update
	unsigned long birds_updatetime= millis();   // time of update of birds position

	enum states {S_Running, S_Idle, S_Paused, S_GameOver}; // FSM state

	states Current_State = S_Idle;  // initial state, start screen for the game

create for each Push buttons Functions

	// up push button control
	bool pressedup(){
	  static uint32_t previousTime = millis();      // 0 for initial value for previousTime
	  static bool lastbuttonstate = LOW;            // low for initial value for lastbuttonstate
	  const uint16_t BOUNCEDELAY = 100;

	  if (digitalRead(UP_BTN) != lastbuttonstate){  // if push button press
	    previousTime = millis();                    // reset previousTime_ms if button press
	  }

	  if ((millis() - previousTime >=BOUNCEDELAY)) {
	    return true;
	  } else {return false;}
	  lastbuttonstate = digitalRead(UP_BTN);   // read lastbutton state

	}

Balloon update function, will update new position and display balloon, and before old position balloon display black.

	/ balloon updates
	void balloon(){
	  static unsigned long balloon_updatetime;    // variable which the time to update balloon position 
	  uint32_t potVal = 256-analogRead(LR_PTN);   // ADC value for the potentiometer

	  // updates balloon every 100ms   
	  if (millis() > balloon_updatetime + 100) {

	    // x axis position, direction and limit every updates
	    if (potVal>=138) {new_x +=1;} // potvalue > 137 then x position increment by 1 pixel
	    if (potVal<=118) {new_x-=1;}  // potvalue < 119 then x position decrement by 1 pixel
	    if (new_x<3) {new_x=3;}       // limit x position less than 3 pixel
	    if (new_x>display.width()-4) {new_x=display.width()-4;} // limit x position 4 pixel from screen width

	    if (pressedup()) {new_y -=1;} // up button pressed then y position decrement by 1 pixel
	    if (presseddn()) {new_y +=1;} // down button pressed then y position decrement by 1 pixel
	    if (new_y<3) {new_y=3;}       // limit y position less than 3 pixel
	    if (new_y>display.height()-14) {new_y=display.height()-14;} // limit x position 14 pixel from screen height

	    display.drawBitmap(old_x,old_y,balloon_bmp, 14, 14, BLACK);     // erase balloon from old position
	    if (hit_state) {                                                // if balloon hit by birds
	      display.drawBitmap(new_x,new_y,balloon_bmp, 14, 14, BLACK);   // erase ballon from new position too
	      new_x=STR_X; new_y=STR_Y;                                     // assign new position
	    }
	    else {
	      display.drawBitmap(new_x,new_y,balloon_bmp, 14, 14, WHITE);   // show balloon in new position
	    }
	    display.display();  // display balloon

	    balloon_updatetime = millis(); // reset update time
	    old_y = new_y; old_x = new_x;  // assign as old position
	   }
	}
	
Birds update function and randomize function, will update each bird poasition, randomize direction and start position.

	// random position of birds
	void randomize(u_int32_t startx){
	  for(f=0; f<n; f++) { // loop all birds
	    arr_birds[f][XPOS] = random(startx,display.width());  // x position
	    arr_birds[f][YPOS] = random(1,display.height());      // y position
	  }
	}

	// birds updates
	void birds() {
	 static int8_t arr_previous[10][4];     // old variable array of birds position

	  // update time of birds position and direction, it will decrease hence speed up 
	  if (millis() > birds_updatetime) {
	    birds_updatetime += birds_delaytime;  // increment the time for next update

	    for(f=0; f< n; f++) { // loop for all birds
	      arr_birds[f][XPOS] -= arr_birds[f][XDIR]; // x position change due to randomize direction
	      arr_birds[f][YPOS] += arr_birds[f][YDIR]; // y position change due ro randomize direction

	      if (arr_birds[f][XPOS]<=1) { // birds reach zero x position
		arr_birds[f][XPOS] = display.width();             // start x position at far end of display
		arr_birds[f][YPOS] = random(8,display.height());  // y position randomize 

		birds_delaytime = birds_delaytime - 10;           // decrease delay time by 10ms
		if (birds_delaytime < 10) {birds_delaytime = 10;} // limit delay time to 10ms
		score += 1;
	      }

	      if (arr_birds[f][YPOS]<=8) {arr_birds[f][YPOS]=8;}  // limit x position not lower than 8
	      if (arr_birds[f][YPOS]>=display.height()-1 ){arr_birds[f][YPOS]=display.height()-1;} // limit y position not higher than screen height

	      arr_birds[f][XDIR] = random(1, 6);  // randomize x direction from 1 to 6 pixels
	      arr_birds[f][YDIR] = random(-3, 3); // randomize y direction until 3 pixels left or right from previous position                              
	    }
	    for(f=0; f< n; f++) { // loop to all birds updates position
	      display.drawBitmap(arr_previous[f][XPOS],arr_previous[f][YPOS],bird_bmp, 8, 8, BLACK);  // previous position erase
	      display.drawBitmap(arr_birds[f][XPOS],arr_birds[f][YPOS],bird_bmp, 8, 8, WHITE);        // show birds at current position
	    }
	    display.display(); // Show the display buffer on the screen

	    for(f=0; f< n; f++) { // all birds
	      arr_previous[f][XPOS] = arr_birds[f][XPOS]; // assign previous x position
	      arr_previous[f][YPOS] = arr_birds[f][YPOS]; // assign previous y position
	    }
	  }
	}

Hit function will define when birds hit the balloon.

	// instant birds hit the balloon
	void hit() {
	  static unsigned long update_hit;  // delay time to change the hit_state to false
	  for(f=0; f< n; f++) { // loop all birds check if hit
	    if (new_x >= arr_birds[f][0] -4 && new_x <= arr_birds[f][0] + 4         // x axis the balloan at -4 and +4 of a bird 
		&& new_y >= arr_birds[f][1] -4 && new_y <= arr_birds[f][1] + 4) {   // y axis the balloan at -4 and +4 of a bird 
	      life = life -1;     // life decrement
	      lifescore();        // display life and score
	      hit_state = true;   // hit state true will not show the balloon
	      randomize(display.width()-20); // start random position of birds
	      break;
	    }
	  }
	  if (millis() > update_hit) { // delay before hit state is false
	    update_hit += 1000; // 1000ms before hit state is false      
	    lifescore();        // display life and score
	    hit_state = false;  // hit state true will show the balloon
	  }
	}
	
Message function and, Life Score function, will display instruction every screen state and, life and score of the game.

	// show life and score
	void lifescore(){
	  // life symbol in circle
	  if (life>=3){display.fillCircle(12,2,1,1);} else {display.fillCircle(12,2,1,0);}  // 3rd circle
	  if (life>=2){display.fillCircle(8,2,1,1);} else {display.fillCircle(8,2,1,0);}    // 2nd circle
	  if (life>=1){display.fillCircle(4,2,1,1);} else {display.fillCircle(4,2,1,0);}    // 1st circle
	  // update score
	  drawpixels(95,0,130,5,BLACK);   // erase score
	  display.setTextSize(1);         // Normal 1:1 pixel scale
	  display.setTextColor(WHITE);    // Draw white text
	  display.setCursor(60,0);        // from top-left corner, x axis 60 pixel and y axis o
	  display.print(F("Score: "));    // text
	  display.print(score);           // score

	}

	// text display
	void message(){
	  if (life<=0){ // display end screen 
	    display.clearDisplay();             // clear screen
	    display.setTextSize(2);             // Normal 1:1 pixel scale
	    display.setTextColor(WHITE);        // Draw white text
	    display.setCursor(12,12);           // Start at top-left corner
	    display.println(F("GAME OVER"));    // diplay game over

	    drawpixels(0,32,128,48,WHITE);      // display fill rectagular white
	    display.setTextSize(1);             // Normal 1:1 pixel scale
	    display.setTextColor(BLACK);        // Draw white text
	    display.setCursor(25,36);           // Start at top-left corner
	    display.print(F("Your Score: "));   // text 
	    display.print(score);               // score
	  }
	  // instruction text
	  display.setTextSize(1);                   // Normal 1:1 pixel scale
	  display.setTextColor(WHITE);              // Draw white text
	  display.setCursor(0,display.height()-7);  // Start at top-left corner
	  switch (Current_State){
	    case S_Idle:      display.println(F("Press btn to start"));  break;     // start screen instruction
	    case S_Paused:    display.println(F("Press btn to continue"));  break;  // paused screen instruction
	    case S_GameOver:  display.println(F("Press btn to restart"));  break;   // end screen instruction
	    case S_Running:  break; // do nothing
	  } 
	  display.display();    // display buffer screen
	}
	
Set up clock frequency, OLED display and push button pinmode.

	// initialize clock , analog resoution, pin modes and current state to idle
	void setup() {
	  Wire.setClock(300000);

	   // SSD1306_SWITCHCAPVCC = generate display voltage from 3.3V internally
	  if(!display.begin(SSD1306_SWITCHCAPVCC, 0x3C)) { // Address 0x3C for 128x64
	    Serial.println(F("SSD1306 allocation failed"));
	    for(;;); // Don't proceed, loop forever
	  }
	  display.clearDisplay();   // adfuit display  

	  analogReadResolution(8);          // sets resolution 16 bits, so analogRead() be 0 to 256
	  pinMode(UP_BTN, INPUT_PULLUP);    // declare the up push button as an input pullup
	  pinMode(DN_BTN, INPUT_PULLUP);    // declare the down push button as an input pullup

	  Current_State = S_Idle;           // inital state idle
	}

main function is loop through switch case for FSM

	// loop through FSM
	void loop() {
	  switch (Current_State){
	    case S_Idle:
		  display.drawBitmap(0,0,logo_bmp, SCREEN_WIDTH, SCREEN_HEIGHT, WHITE); // start screen
		  message();  // instruction to start
		  if (pressedup() || presseddn()) { // press either button
		    birds_delaytime = 500;          // birds update delay
		    birds_updatetime= millis();     // birds current update time
		    randomize(display.width()-20);  // random start position of birds
		    display.clearDisplay();         // clear display for transition of display
		    Current_State = S_Running;      // change state to running
		  }
		  break;
	    case S_Running:
		  balloon();  // update balloon
		  birds();    // update birds
		  hit();      // check if hit
		  if (pressedup() && presseddn()) {Current_State = S_Paused;}         // simutaneous pressed to pause
		  if (life<=0){ display.clearDisplay(); Current_State = S_GameOver;}  // life zero game over
		  break;
	    case S_Paused:
		  message();  // instruction to continue
		  if (pressedup() || presseddn()) {   // press either push button
		    drawpixels(0,display.height()-7,128,display.height(),BLACK); // clear pixels at instruction
		    birds_updatetime = millis();      // keep update time 
		    lifescore();                      // display life and score
		    Current_State = S_Running;        // change state to running
		  }
		  break;
	    case S_GameOver:
		  message();  // instruction to restart
		  if (pressedup() || presseddn()) {   // press either push button
		    display.clearDisplay();     // clear screen
		    life = 3; score = 0;        // restart initial life and score
		    birds_delaytime = 500;      // reset birds delay update time
		    birds_updatetime= millis(); // keep update time 
		    randomize(display.width()-20);  // random birds position
		    lifescore();                // display life and score
		    Current_State = S_Running;  // change state to running
		  }
		  break;
	  } 
	}


### c. Problem

To pause the game the two push buttons must press simultaneously however it bounce. This may cause by inconsistent timing during press function of the two buttons. Another drawback, there is no delay from state to state, for instant at game-over state if button is press instantly go to running state. 

### d. Future Improvement

In the game there is no increase of the birds count, only the speed of birds update hence as it level up it become faster, To improve the birds count will add to the difficulty. Also, to improve the above problem the debounce for simultaneous press buttons and the delay change from state to state.

## Appendices

### i. Codes

	/* 
	  ***************************************************************************************************
	  ** Project 3: "balloon vs birds" game (Mini OLED Display, FSM, I2C)                                  **
	  ***************************************************************************************************
	  This is an embedded system project with Mini OLED Display and serial bus Inter-Integrated Circuit (I²C).
	  This project program concept as Finite State Machine (FSM).
	  This program uses the Adafruit_GFX, Adafruit_SSD1306 libraries.

	  The circuit:
	    1. Teensy 3.1/3.2 Microcontroller
	    2. a 128x64 OLED display SDD1306 I²C version
	    3. 10kΩ potentiometer with wiper connected from Pin A9
	       and other two terminal from 3.3V to ground  
	    4. 2pcs push button switches connected from μC pins to ground 

	  Created: 10 June 2019 by Joven Sepulveda 
		    Weltec School of Engineering
		    MG7013 Embedded Systems
	*/

	#include <Arduino.h>
	#include <SPI.h>
	#include <Wire.h>
	#include <Adafruit_GFX.h>
	#include <Adafruit_SSD1306.h>

	#define SCREEN_WIDTH 128 // OLED display width, in pixels
	#define SCREEN_HEIGHT 64 // OLED display height, in pixels

	// Declaration for an SSD1306 display connected to I2C (SDA, SCL pins)
	#define OLED_RESET     4 // Reset pin # (or -1 if sharing Arduino reset pin)
	Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, OLED_RESET);

	// constant set pin numbers:
	const uint8_t UP_BTN = 12;             // input pin for up button
	const uint8_t DN_BTN = 9;              // input pin for down button
	const uint8_t LR_PTN = A9;             // Analog input pin for potentiometer

	// start screen, game title, balloon and birds bitmap coverted to hex number
	static const unsigned char PROGMEM logo_bmp[] = 
	{ 
	0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00,
	0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00,
	0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00,
	0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00,
	0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00,
	0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00,
	0x00, 0x00, 0x30, 0x75, 0xB0, 0x00, 0x00, 0x00, 0x00, 0x18, 0x30, 0x03, 0x00, 0x00, 0x00, 0x00,
	0x00, 0x00, 0x30, 0x3D, 0xB0, 0x00, 0x00, 0x00, 0x00, 0x18, 0x30, 0x03, 0x00, 0x00, 0x00, 0x00,
	0x00, 0x00, 0x30, 0x71, 0xB0, 0x00, 0x00, 0x00, 0x00, 0x18, 0x00, 0x03, 0x00, 0x00, 0x00, 0x00,
	0x00, 0x00, 0x3E, 0x79, 0xB3, 0xC7, 0x9F, 0x06, 0xCE, 0x1F, 0x37, 0x9F, 0x38, 0x00, 0x00, 0x00,
	0x00, 0x00, 0x33, 0x4D, 0xB6, 0x6C, 0xD9, 0x86, 0xD8, 0x19, 0xB6, 0x33, 0x60, 0x00, 0x00, 0x00,
	0x00, 0x00, 0x33, 0x3D, 0xB6, 0x6C, 0xD9, 0x86, 0xDC, 0x39, 0xB6, 0x33, 0x70, 0x00, 0x00, 0x00,
	0x00, 0x00, 0x33, 0x6D, 0xB6, 0x6C, 0xF9, 0x82, 0x8E, 0x39, 0xB6, 0x33, 0x38, 0x00, 0x00, 0x00,
	0x00, 0x00, 0x33, 0x6D, 0xB6, 0x6C, 0xF9, 0x83, 0x86, 0x39, 0xB6, 0x33, 0x18, 0x00, 0x00, 0x00,
	0x00, 0x00, 0x3E, 0x3D, 0xB3, 0xF7, 0xF9, 0x83, 0x9C, 0x7F, 0x36, 0x1F, 0x70, 0x00, 0x00, 0x00,
	0x00, 0x00, 0x00, 0x00, 0x00, 0x38, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00,
	0x00, 0x00, 0x00, 0x00, 0x00, 0x38, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00,
	0x00, 0x00, 0x00, 0x00, 0x00, 0xF8, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00,
	0x00, 0x00, 0x00, 0x00, 0x00, 0x06, 0x00, 0x00, 0x00, 0x00, 0x20, 0x00, 0x00, 0x00, 0x00, 0x00,
	0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x0C, 0x00, 0x60, 0x00, 0x00, 0x00, 0x00, 0x00,
	0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x1C, 0x00, 0xE0, 0x00, 0x00, 0x00, 0x00, 0x00,
	0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x38, 0x00, 0xE0, 0x00, 0x00, 0x00, 0x00, 0x00,
	0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x38, 0x01, 0xE0, 0x00, 0x00, 0x00, 0x00, 0x00,
	0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0xF8, 0x03, 0xC0, 0x01, 0xE0, 0x00, 0x00, 0x00,
	0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x7F, 0x00, 0x70, 0x00, 0x70, 0x00, 0x00, 0x00,
	0x00, 0x00, 0x00, 0x00, 0xFC, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x20, 0x00, 0x00, 0x00,
	0x00, 0x00, 0x00, 0x01, 0xFE, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00,
	0x00, 0x00, 0x00, 0x03, 0xFF, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00,
	0x00, 0x00, 0x00, 0x03, 0xFF, 0x00, 0x07, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00,
	0x00, 0x00, 0x00, 0x03, 0xFF, 0x00, 0x03, 0x40, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00,
	0x00, 0x00, 0x00, 0x03, 0xFF, 0x00, 0x03, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00,
	0x00, 0x00, 0x00, 0x03, 0xFF, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00,
	0x00, 0x00, 0x00, 0x01, 0xFE, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00,
	0x00, 0x00, 0x00, 0x00, 0xFC, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00,
	0x00, 0x00, 0x00, 0x00, 0x78, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00,
	0x00, 0x00, 0x00, 0x00, 0x38, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00,
	0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x08, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00,
	0x00, 0x00, 0x00, 0x00, 0x38, 0x00, 0x00, 0x00, 0x00, 0xFC, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00,
	0x00, 0x00, 0x00, 0x00, 0x38, 0x00, 0x00, 0x01, 0x80, 0x74, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00,
	0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x03, 0x80, 0x60, 0x00, 0x7C, 0x00, 0x00, 0x00, 0x00,
	0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x80, 0x60, 0x00, 0x70, 0x00, 0x00, 0x00, 0x00,
	0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x40, 0x00, 0xE0, 0x00, 0x00, 0x00, 0x00,
	0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x01, 0x00, 0x00, 0x00, 0x00, 0xE0, 0x00, 0x00, 0x00, 0x00,
	0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x03, 0xF0, 0x00, 0x00, 0x00, 0xC0, 0x00, 0x00, 0x00, 0x00,
	0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x03, 0xA0, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00,
	0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x03, 0x00, 0x00, 0x00, 0x00, 0x00, 0x03, 0x00, 0x00, 0x00,
	0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x03, 0x00, 0x00, 0x00, 0x00, 0x00, 0x03, 0x00, 0x00, 0x00,
	0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x07, 0x00, 0x00, 0x00,
	0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x03, 0x00, 0x00, 0x00,
	0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x0F, 0x00, 0x00, 0x00,
	0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0xC0, 0x00, 0x00,
	0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00,
	0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00,
	0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00,
	0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x78, 0x00, 0x00, 0x00, 0x00,
	0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x1F, 0x00, 0x00, 0x00, 0x00,
	0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x04, 0x00, 0x00, 0x00, 0x00,
	0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x3C,
	0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0xF0,
	0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x18,
	0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00,
	0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00,
	0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00,
	0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00
	};

	// bird bitmp coverted to hex number
	static const unsigned char PROGMEM bird_bmp[] =
	{0x04, 0x18, 0x10, 0x30, 0x30, 0x60, 0x20, 0x00 };

	// balloon bitmap converted to hex number
	static const unsigned char PROGMEM balloon_bmp[] =
	{0x00, 0x00, 0x07, 0xE0, 0x0F, 0xF0, 0x1F, 0xF8, 0x1F, 0xF8, 0x1F, 0xF8, 0x1F, 0xF8, 0x1F, 0xF8,
	0x0F, 0xF0, 0x07, 0xE0, 0x03, 0xC0, 0x01, 0xC0, 0x00, 0x00, 0x01, 0xC0, 0x01, 0xC0, 0x00, 0x00 };

	// contant and variables for balloon location (direction, and start, new and old position)
	const uint8_t STR_X = display.width()/8, STR_Y = display.height()/2;  // start position
	uint8_t new_x = STR_X, new_y = STR_Y;                           // current position
	int8_t dir_x = 0, dir_y = 0;                                    // direction
	uint8_t old_x = 0, old_y = 33;                                  // previous position

	bool hit_state = false; // indicate true if a bird position within balloon position

	// Indexes into the birds array variable
	#define XPOS   0            // x axis position
	#define YPOS   1            // y axis position
	#define XDIR   2            // x direction
	#define YDIR   3            // y direction

	int8_t arr_birds[10][4];    // array variables for birds position
	uint8_t f, n = 6;           // varible for count and number of birds

	int8_t life = 3;            // balloon life of game per play
	int8_t score = 0;           // birds pass the balloon

	unsigned long birds_delaytime = 500;        // initial value to be decrease so speed up birds update
	unsigned long birds_updatetime= millis();   // time of update of birds position

	enum states {S_Running, S_Idle, S_Paused, S_GameOver}; // FSM state

	states Current_State = S_Idle;  // initial state, start screen for the game

	// up push button control
	bool pressedup(){
	  static uint32_t previousTime = millis();      // 0 for initial value for previousTime
	  static bool lastbuttonstate = LOW;            // low for initial value for lastbuttonstate
	  const uint16_t BOUNCEDELAY = 100;

	  if (digitalRead(UP_BTN) != lastbuttonstate){  // if push button press
	    previousTime = millis();                    // reset previousTime_ms if button press
	  }

	  if ((millis() - previousTime >=BOUNCEDELAY)) {
	    return true;
	  } else {return false;}
	  lastbuttonstate = digitalRead(UP_BTN);   // read lastbutton state

	}

	// up push button control
	bool presseddn(){
	  static uint32_t previousTime = millis();      // 0 for initial value for previousTime
	  static bool lastbuttonstate = LOW;            // low for initial value for lastbuttonstate
	  const uint16_t BOUNCEDELAY = 100;

	  if (digitalRead(DN_BTN) != lastbuttonstate){  // if push button press
	    previousTime = millis();                    // reset previousTime_ms if button press
	  }

	  if ((millis() - previousTime >=BOUNCEDELAY)) {
	    return true;
	  } else {return false;}
	  lastbuttonstate = digitalRead(DN_BTN);   // read lastbutton state
	}

	// balloon updates
	void balloon(){
	  static unsigned long balloon_updatetime;    // variable which the time to update balloon position 
	  uint32_t potVal = 256-analogRead(LR_PTN);   // ADC value for the potentiometer

	  // updates balloon every 100ms   
	  if (millis() > balloon_updatetime + 100) {

	    // x axis position, direction and limit every updates
	    if (potVal>=138) {new_x +=1;} // potvalue > 137 then x position increment by 1 pixel
	    if (potVal<=118) {new_x-=1;}  // potvalue < 119 then x position decrement by 1 pixel
	    if (new_x<3) {new_x=3;}       // limit x position less than 3 pixel
	    if (new_x>display.width()-4) {new_x=display.width()-4;} // limit x position 4 pixel from screen width

	    if (pressedup()) {new_y -=1;} // up button pressed then y position decrement by 1 pixel
	    if (presseddn()) {new_y +=1;} // down button pressed then y position decrement by 1 pixel
	    if (new_y<3) {new_y=3;}       // limit y position less than 3 pixel
	    if (new_y>display.height()-14) {new_y=display.height()-14;} // limit x position 14 pixel from screen height

	    display.drawBitmap(old_x,old_y,balloon_bmp, 14, 14, BLACK);     // erase balloon from old position
	    if (hit_state) {                                                // if balloon hit by birds
	      display.drawBitmap(new_x,new_y,balloon_bmp, 14, 14, BLACK);   // erase ballon from new position too
	      new_x=STR_X; new_y=STR_Y;                                     // assign new position
	    }
	    else {
	      display.drawBitmap(new_x,new_y,balloon_bmp, 14, 14, WHITE);   // show balloon in new position
	    }
	    display.display();  // display balloon

	    balloon_updatetime = millis(); // reset update time
	    old_y = new_y; old_x = new_x;  // assign as old position
	   }
	}

	// random position of birds
	void randomize(u_int32_t startx){
	  for(f=0; f<n; f++) { // loop all birds
	    arr_birds[f][XPOS] = random(startx,display.width());  // x position
	    arr_birds[f][YPOS] = random(1,display.height());      // y position
	  }
	}

	// birds updates
	void birds() {
	 static int8_t arr_previous[10][4];     // old variable array of birds position

	  // update time of birds position and direction, it will decrease hence speed up 
	  if (millis() > birds_updatetime) {
	    birds_updatetime += birds_delaytime;  // increment the time for next update

	    for(f=0; f< n; f++) { // loop for all birds
	      arr_birds[f][XPOS] -= arr_birds[f][XDIR]; // x position change due to randomize direction
	      arr_birds[f][YPOS] += arr_birds[f][YDIR]; // y position change due ro randomize direction

	      if (arr_birds[f][XPOS]<=1) { // birds reach zero x position
		arr_birds[f][XPOS] = display.width();             // start x position at far end of display
		arr_birds[f][YPOS] = random(8,display.height());  // y position randomize 

		birds_delaytime = birds_delaytime - 10;           // decrease delay time by 10ms
		if (birds_delaytime < 10) {birds_delaytime = 10;} // limit delay time to 10ms
		score += 1;
	      }

	      if (arr_birds[f][YPOS]<=8) {arr_birds[f][YPOS]=8;}  // limit x position not lower than 8
	      if (arr_birds[f][YPOS]>=display.height()-1 ){arr_birds[f][YPOS]=display.height()-1;} // limit y position not higher than screen height

	      arr_birds[f][XDIR] = random(1, 6);  // randomize x direction from 1 to 6 pixels
	      arr_birds[f][YDIR] = random(-3, 3); // randomize y direction until 3 pixels left or right from previous position                              
	    }

	    for(f=0; f< n; f++) { // loop to all birds updates position
	      display.drawBitmap(arr_previous[f][XPOS],arr_previous[f][YPOS],bird_bmp, 8, 8, BLACK);  // previous position erase
	      display.drawBitmap(arr_birds[f][XPOS],arr_birds[f][YPOS],bird_bmp, 8, 8, WHITE);        // show birds at current position
	    }
	    display.display(); // Show the display buffer on the screen

	    for(f=0; f< n; f++) { // all birds
	      arr_previous[f][XPOS] = arr_birds[f][XPOS]; // assign previous x position
	      arr_previous[f][YPOS] = arr_birds[f][YPOS]; // assign previous y position
	    }

	  }
	}

	// assign pixels  (black or white)
	void drawpixels(int16_t x0,int16_t y0,int16_t x1,int16_t y1,int16_t c) {
	  int16_t x, y;
	  for(x=x0; x<x1; x++) {  // loop from x0 to x1
	    for(y=y0; y<y1; y++) {  // loop frm y0 to y1
	      display.drawPixel(x, y, c); // assign color (black or white)
	    }
	  }
	}

	// show life and score
	void lifescore(){
	  // life symbol in circle
	  if (life>=3){display.fillCircle(12,2,1,1);} else {display.fillCircle(12,2,1,0);}  // 3rd circle
	  if (life>=2){display.fillCircle(8,2,1,1);} else {display.fillCircle(8,2,1,0);}    // 2nd circle
	  if (life>=1){display.fillCircle(4,2,1,1);} else {display.fillCircle(4,2,1,0);}    // 1st circle
	  // update score
	  drawpixels(95,0,130,5,BLACK);   // erase score
	  display.setTextSize(1);         // Normal 1:1 pixel scale
	  display.setTextColor(WHITE);    // Draw white text
	  display.setCursor(60,0);        // from top-left corner, x axis 60 pixel and y axis o
	  display.print(F("Score: "));    // text
	  display.print(score);           // score

	}

	// instant birds hit the balloon
	void hit() {
	  static unsigned long update_hit;  // delay time to change the hit_state to false
	  for(f=0; f< n; f++) { // loop all birds check if hit
	    if (new_x >= arr_birds[f][0] -4 && new_x <= arr_birds[f][0] + 4         // x axis the balloan at -4 and +4 of a bird 
		&& new_y >= arr_birds[f][1] -4 && new_y <= arr_birds[f][1] + 4) {   // y axis the balloan at -4 and +4 of a bird 
	      life = life -1;     // life decrement
	      lifescore();        // display life and score
	      hit_state = true;   // hit state true will not show the balloon
	      randomize(display.width()-20); // start random position of birds
	      break;
	    }
	  }

	  if (millis() > update_hit) { // delay before hit state is false
	    update_hit += 1000; // 1000ms before hit state is false      
	    lifescore();        // display life and score
	    hit_state = false;  // hit state true will show the balloon
	  }
	}

	// text display
	void message(){
	  if (life<=0){ // display end screen 
	    display.clearDisplay();             // clear screen
	    display.setTextSize(2);             // Normal 1:1 pixel scale
	    display.setTextColor(WHITE);        // Draw white text
	    display.setCursor(12,12);           // Start at top-left corner
	    display.println(F("GAME OVER"));    // diplay game over

	    drawpixels(0,32,128,48,WHITE);      // display fill rectagular white
	    display.setTextSize(1);             // Normal 1:1 pixel scale
	    display.setTextColor(BLACK);        // Draw white text
	    display.setCursor(25,36);           // Start at top-left corner
	    display.print(F("Your Score: "));   // text 
	    display.print(score);               // score
	  }

	  // instruction text
	  display.setTextSize(1);                   // Normal 1:1 pixel scale
	  display.setTextColor(WHITE);              // Draw white text
	  display.setCursor(0,display.height()-7);  // Start at top-left corner
	  switch (Current_State){
	    case S_Idle:      display.println(F("Press btn to start"));  break;     // start screen instruction
	    case S_Paused:    display.println(F("Press btn to continue"));  break;  // paused screen instruction
	    case S_GameOver:  display.println(F("Press btn to restart"));  break;   // end screen instruction
	    case S_Running:  break; // do nothing
	  } 
	  display.display();    // display buffer screen
	}

	// initialize clock , analog resoution, pin modes and current state to idle
	void setup() {
	  Wire.setClock(300000);

	   // SSD1306_SWITCHCAPVCC = generate display voltage from 3.3V internally
	  if(!display.begin(SSD1306_SWITCHCAPVCC, 0x3C)) { // Address 0x3C for 128x64
	    Serial.println(F("SSD1306 allocation failed"));
	    for(;;); // Don't proceed, loop forever
	  }
	  display.clearDisplay();   // adfuit display  

	  analogReadResolution(8);          // sets resolution 16 bits, so analogRead() be 0 to 256
	  pinMode(UP_BTN, INPUT_PULLUP);    // declare the up push button as an input pullup
	  pinMode(DN_BTN, INPUT_PULLUP);    // declare the down push button as an input pullup

	  Current_State = S_Idle;           // inital state idle
	}

	// loop through FSM
	void loop() {
	  switch (Current_State){
	    case S_Idle:
		  display.drawBitmap(0,0,logo_bmp, SCREEN_WIDTH, SCREEN_HEIGHT, WHITE); // start screen
		  message();  // instruction to start
		  if (pressedup() || presseddn()) { // press either button
		    birds_delaytime = 500;          // birds update delay
		    birds_updatetime= millis();     // birds current update time
		    randomize(display.width()-20);  // random start position of birds
		    display.clearDisplay();         // clear display for transition of display
		    Current_State = S_Running;      // change state to running
		  }
		  break;
	    case S_Running:
		  balloon();  // update balloon
		  birds();    // update birds
		  hit();      // check if hit
		  if (pressedup() && presseddn()) {Current_State = S_Paused;}         // simutaneous pressed to pause
		  if (life<=0){ display.clearDisplay(); Current_State = S_GameOver;}  // life zero game over
		  break;
	    case S_Paused:
		  message();  // instruction to continue
		  if (pressedup() || presseddn()) {   // press either push button
		    drawpixels(0,display.height()-7,128,display.height(),BLACK); // clear pixels at instruction
		    birds_updatetime = millis();      // keep update time 
		    lifescore();                      // display life and score
		    Current_State = S_Running;        // change state to running
		  }
		  break;
	    case S_GameOver:
		  message();  // instruction to restart
		  if (pressedup() || presseddn()) {   // press either push button
		    display.clearDisplay();     // clear screen
		    life = 3; score = 0;        // restart initial life and score
		    birds_delaytime = 500;      // reset birds delay update time
		    birds_updatetime= millis(); // keep update time 
		    randomize(display.width()-20);  // random birds position
		    lifescore();                // display life and score
		    Current_State = S_Running;  // change state to running
		  }
		  break;
	  } 
	}
