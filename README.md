# MediEase-Simplifying-Health-Management
## Introduction:

The goal of this project is to develop a device that can show comprehensive details regarding medications, such as names, dosages, and when to take them either before or after meals. In order to keep users on track with their prescriptions and maintain their health, it also has a sound alert to remind them at the precise times they need to take their medication.

## C code:
// Reminds to take medicine at 8am, 2pm, 8pm 


#include <LiquidCrystal.h>

#include <Wire.h>

#include <RTClib.h>

#include <EEPROM.h>


int pushVal = 0;                           

int val;

int val2;

int addr = 0;


RTC_DS3231 rtc;


const int rs = 12, en = 11, d4 = 5, d5 = 4, d6 = 3, d7 = 2;        &nbsp;  &nbsp;   &nbsp;        &nbsp;   &nbsp;           // lcd pins 

LiquidCrystal lcd(rs, en, d4, d5, d6, d7);


#define getWellsoon 0                                           

#define HELP_SCREEN 1

#define TIME_SCREEN 2


//bool pushPressed;         &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;                          //flag to keep track of push button state 

int pushpressed = 0;

const int ledPin =  LED_BUILTIN;         &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;                  // buzzer and led pin

int ledState = LOW;

int Signal = 0;



int buzz = 13;                                      

int push1state, push2state, push3state, stopinState = 0;     

int push1Flag, push2Flag, Push3Flag = false;      &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;           // push button flags 

int push1pin = 9;

int push2pin = 8;

int push3pin = 7;

int stopPin = A0;

int screens = 0;    &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;             // screen to show

int maxScreen = 2;           &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;    // screen count

bool isScreenChanged = true;


long previousMillis = 0;           

long interval = 500;       &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;               // buzzing interval

unsigned long currentMillis;


long previousMillisLCD = 0;    &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   // for LCD screen update

long intervalLCD = 2000;    &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;         // Screen cycling interval

unsigned long currentMillisLCD;


//   Set Reminder Change Time

int buzz8amHH = 8;          &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   //    HH - hours        

int buzz8amMM = 00;       &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;      //    MM - Minute

int buzz8amSS = 00;         &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;    //    SS - Seconds


int buzz2pmHH = 14;          &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   //    HH - hours

int buzz2pmMM = 00;           &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;  //    MM - Minute

int buzz2pmSS = 00;        &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;     //    SS - Seconds


int buzz8pmHH = 20;         &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;    //    HH - hours

int buzz8pmMM = 00;        &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;     //    MM - Minute

int buzz8pmSS = 00;         &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;    //    SS - Seconds


 


int nowHr, nowMin, nowSec;         &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;               // to show current mm,hh,ss


// All messeges

void gwsMessege(){            &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;      // print get well soon messege
lcd.clear();

    lcd.setCursor(0, 0);

    lcd.print("Stay Healthy :)");     // Give some cheers

    lcd.setCursor(0, 1);

    lcd.print("Get Well Soon :)");    // wish

}


void helpScreen() {             &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;    // function to display 1st screen in LCD

    lcd.clear();

    lcd.setCursor(0, 0);

    lcd.print("Press Buttons");

    lcd.setCursor(0, 1);

    lcd.print("for Reminder...!");

    

 }


void timeScreen() {                &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp; // function to display Date and time in LCD screen

  DateTime now = rtc.now();            &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;    // take rtc time and print in display

    lcd.clear();

    lcd.setCursor(0, 0);

    lcd.print("Time:");

    lcd.setCursor(6, 0);

    lcd.print(nowHr = now.hour(), DEC);

    lcd.print(":");

    lcd.print(nowMin = now.minute(), DEC);

    lcd.print(":");

    lcd.print(nowSec = now.second(), DEC);

    lcd.setCursor(0, 1);

    lcd.print("Date: ");

    lcd.print(now.day(), DEC);

    lcd.print("/");

    lcd.print(now.month(), DEC);

    lcd.print("/");

    lcd.print(now.year(), DEC);

}



void setup() {


  Serial.begin(9600);                     &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;    // start serial debugging

  if (! rtc.begin()) {                   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;      // check if rtc is connected 

    Serial.println("Couldn't find RTC");

    while (1);

  }

  if (rtc.lostPower()) {

    Serial.println("RTC lost power, lets set the time!");

  }


//    rtc.adjust(DateTime(F(__DATE__), F(__TIME__)));             &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;     // uncomment this to set the current time and then comment in next upload when u set the time

  rtc.adjust(DateTime(2019, 1, 10, 7, 59, 30));        &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;           // manual time set


  lcd.begin(16, 2);

  lcd.clear();

  lcd.setCursor(0, 0);

  lcd.print("Welcome To");                            &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;             // print a messege at startup

  lcd.setCursor(0, 1);

  lcd.print("Circuit Digest");

  delay(1000);

  pinMode(push1pin, INPUT);                          &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;             // define push button pins type

  pinMode(push2pin, INPUT);

  pinMode(push3pin, INPUT);

  pinMode(stopPin, INPUT);

  pinMode(ledPin, OUTPUT);

  delay(200);

  Serial.println(EEPROM.read(addr));

  val2 = EEPROM.read(addr);                          &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;  // read previosuly saved value of push button to start from where it was left previously

  switch (val2) {

    case 1:

      Serial.println("Set for 1/day");

      push1state = 1;

      push2state = 0;

      push3state = 0;

      pushVal = 1;

      break;

    case 2:

      Serial.println("Set for 2/day");

      push1state = 0;

      push2state = 1;

      push3state = 0;

      pushVal = 2;


      break;

    case 3:

      Serial.println("Set for 3/day");

      push1state = 0;

      push2state = 0;

      push3state = 1;

      pushVal = 3;


      break;

  }



}


void loop() {

  push1();                                          &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;      //call to set once/day 

  push2();                                            &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   //call to set twice/day 

  push3();                                          &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;     //call to set thrice/day 

    if (pushVal == 1) {                          

    at8am();                                        
  }

  else if (pushVal == 2) {                         &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;      // if push button 2 pressed then remind at 8am and 8pm

    at8am();                                            

    at8pm();                                           

  }

  else if (pushVal == 3) {                             &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;  // if push button 3 pressed then remind at 8am and 8pm

    at8am();

    at2pm();                                            //function to start uzzing at 8mm

    at8pm();

  }

  

  currentMillisLCD = millis();                        &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   // start millis for LCD screen switching at defined interval of time

  push1state = digitalRead(push1pin);                 &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   // start reading all push button pins

  push2state = digitalRead(push2pin);

  push3state = digitalRead(push3pin);

  stopinState = digitalRead(stopPin);

  

  stopPins();                                          &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;    // call to stop buzzing

  changeScreen();                                  &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;        // screen cycle function



}


// push buttons

void push1() {                   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;  // function to set reminder once/day 

  if (push1state == 1) {

    push1state = 0;

    push2state = 0;

    push3state = 0;

//    pushPressed = true;

    EEPROM.write(addr, 1);

    Serial.print("Push1 Written : "); Serial.println(EEPROM.read(addr)); 

    pushVal = 1;                                             

    lcd.clear();

    lcd.setCursor(0, 0);

    lcd.print("Reminder set ");

    lcd.setCursor(0, 1);

    lcd.print("for Once/day !");

    delay(1200);

    lcd.clear();

  }

}


void push2() {                     &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   //function to set reminder twice/day

  if (push2state == 1) {

    push2state = 0;

    push1state = 0;

    push3state = 0;

//    pushPressed = true;

    EEPROM.write(addr, 2);

    Serial.print("Push2 Written : "); Serial.println(EEPROM.read(addr));

    pushVal = 2;

    lcd.clear();

    lcd.setCursor(0, 0);

    lcd.print("Reminder set ");

    lcd.setCursor(0, 1);

    lcd.print("for Twice/day !");

    delay(1200);

    lcd.clear();

  }

}


void push3() {              &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;        //function to set reminder thrice/day

  if (push3state == 1) {

    push3state = 0;

    push1state = 0;

    push2state = 0;

//    pushPressed = true;

    EEPROM.write(addr, 3);

    Serial.print("Push3 Written : "); Serial.println(EEPROM.read(addr));

    pushVal = 3;

    lcd.clear();

    lcd.setCursor(0, 0);

    lcd.print("Reminder set ");

    lcd.setCursor(0, 1);

    lcd.print("for Thrice/day !");

    delay(1200);

    lcd.clear();

  }

}


void stopPins() {             &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;        //function to stop buzzing when user pushes stop push button

  if (stopinState == 1) {

//    stopinState = 0;

//    pushPressed = true;

    pushpressed = 1;

    lcd.clear();

    lcd.setCursor(0, 0);

    lcd.print("Take Medicine  ");

    lcd.setCursor(0, 1);

    lcd.print("with Warm Water");

    delay(1200);

    lcd.clear();

  }

}



void startBuzz() {                &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;      // function to start buzzing when time reaches to defined interval


//  if (pushPressed == false) {

 if (pushpressed == 0) {

    Serial.println("pushpressed is false in blink");

    unsigned long currentMillis = millis();

    if (currentMillis - previousMillis >= interval) {

      previousMillis = currentMillis;      

      Serial.println("Start Buzzing");

      if (ledState == LOW) {                 
        ledState = HIGH;

      }  else {

        ledState = LOW;

      }

      digitalWrite(ledPin, ledState);

    }

  }

  else if (pushpressed == 1) {

    Serial.println("pushpressed is true");

    ledState = LOW;

    digitalWrite(ledPin, ledState);

  }

}


void at8am() {                   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;     // function to start buzzing at 8am

  DateTime now = rtc.now();

  if (int(now.hour()) >= buzz8amHH) {

    if (int(now.minute()) >= buzz8amMM) {

      if (int(now.second()) > buzz8amSS) {

        /////////////////////////////////////////////////////


        startBuzz();

        /////////////////////////////////////////////////////

      }

    }

  }

}


void at2pm() {                       &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;     // function to start buzzing at 2pm

  DateTime now = rtc.now();

  if (int(now.hour()) >= buzz2pmHH) {

    if (int(now.minute()) >= buzz2pmMM) {

      if (int(now.second()) > buzz2pmSS) {

       

        ///////////////////////////////////////////////////

        startBuzz();

        //////////////////////////////////////////////////

      }

    }

  }

}


void at8pm() {                      &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;       // function to start buzzing at 8pm

  DateTime now = rtc.now();

  if (int(now.hour()) >= buzz8pmHH) {

    if (int(now.minute()) >= buzz8pmMM) {

      if (int(now.second()) > buzz8pmSS) {

        

        /////////////////////////////////////////////////////

        startBuzz();

        /////////////////////////////////////////////////////

      }

    }

  }

}


//Screen Cycling

void changeScreen() {                 //function for Screen Cycling

  // Start switching screen every defined intervalLCD

  if (currentMillisLCD - previousMillisLCD > intervalLCD)            &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   // save the last time you changed the display

  {

    previousMillisLCD = currentMillisLCD;

    screens++;

    if (screens > maxScreen) {

      screens = 0;  // all screens over -> start from 1st

    }

    isScreenChanged = true;

  }


  // Start displaying current screen

  if (isScreenChanged)     &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;   &nbsp;// only update the screen if the screen is changed.

  {

    isScreenChanged = false; 

    switch (screens)

    {

      case getWellsoon:

        gwsMessege();             

        break;

      case HELP_SCREEN:              

        helpScreen();               

        break;

      case TIME_SCREEN:

        timeScreen();                 

        break;

      default:

        //NOT SET.
 break;
}
        
  }

}


## Error-Free Compilation Output:

![123](https://github.com/Advith147/MediEase-Simplifying-Health-Management/assets/109427643/e18e369e-31a0-4645-827f-0de8c5359440)

## RISCDUINO UNO Compilation Output:


## BOM Cost:

| **Component name** | **Quantity Required** | **Unit price** | **Total Price**** (Unit price\*Quantity)** |
| --- | --- | --- | --- |
| DS3231 RTC Module Precise Real Time Clock I2C AT24C32 | 1 | 209 | 209 |
| --- | --- | --- | --- |
| Electronic Spices 16 x 4 Yellow/Green color LCD display module | 1 | 479 | 479 |
| Led | 4 | 20 | 80 |
| Jumper Wires| 10 | 10 | 100 |
| 10K Potentiometer | 2 | 50 | 100 |
| Push Buttons | 1 | 50 | 50 |
| Breadboard | 1 | 50 | 50 |
| 10K,1K Resistors | 5 | 10 | 50 |
