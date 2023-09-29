# MediEase-Simplifying-Health-Management
## Introduction:

The goal of this project is to develop a device that can show comprehensive details regarding medications, such as names, dosages, and when to take them either before or after meals. In order to keep users on track with their prescriptions and maintain their health, it also has a sound alert to remind them at the precise times they need to take their medication.

## C code:
// Reminds to take medicine at 8am, 2pm, 8pm 


#include <LiquidCrystal.h>
#include <Wire.h>
#include <RTClib.h>

RTC_DS3231 rtc;

const int rs = 12, en = 11, d4 = 5, d5 = 4, d6 = 3, d7 = 2; // lcd pins

LiquidCrystal lcd(rs, en, d4, d5, d6, d7);

int buzz = 13;

const int buttonPin2 = 9;

const int buttonPin3 = 8;

const int buttonPin1 = A0;

const int buttonPin4 = 7; // the pin that the pushbutton is attached to

int val2 = 0;

int val3 = 0;

int pushVal = 0;

int bS1 = 0;     // current state of the button

int lBS1 = 0;    // previous state of the button

int bS2 = 0;     // current state of the button

int lBS2 = 0;

int bS3 = 0;     // current state of the button

int lBS3 = 0;

int bS4 = 0;     // current state of the button

int lBS4 = 0;

// configure the pins to the right mode

int buzz8amHH = 8; // HH - hours ##Set these for reminder time in 24hr Format

int buzz8amMM = 00; // MM - Minute

int buzz8amSS = 00; // SS - Seconds

int buzz2pmHH = 8; // HH - hours

int buzz2pmMM = 1; // MM - Minute

int buzz2pmSS = 00; // SS - Seconds

int buzz8pmHH = 8; // HH - hours

int buzz8pmMM = 2; // MM - Minute

int buzz8pmSS = 00; // SS - Seconds

int nowHr, nowMin, nowSec;

void gwsMessege() {

  // print get well soon message
  
  lcd.clear();
  
  lcd.setCursor(0, 0);
  
  lcd.print("Stay Healthy :)");
  
  lcd.setCursor(0, 1);
  
  lcd.print("Get Well Soon :)");
  
}

void helpScreen() {

  // function to display 1st screen in LCD
  
  lcd.clear();
  
  lcd.setCursor(0, 0);
  
  lcd.print("Press Buttons");
  
  lcd.setCursor(0, 1);
  
  lcd.print("for Reminder...!");
  
}

void timeScreen() {

  // function to display Date and time in LCD screen
  
  DateTime now = rtc.now();

  lcd.clear();

  lcd.setCursor(0, 0);
  
  lcd.print("Time:");
  
  lcd.setCursor(6, 0);
  
  lcd.print(nowHr = now.hour(), DEC);
  
  lcd.print(":");
  
  lcd.print(nowMin = now.minute(), DEC );
  lcd.print(":");
  
  lcd.print(nowSec = now.second(), DEC);
  
  lcd.setCursor(0, 1);
  
  lcd.print("Date: ");
  
  lcd.print(now.day(), DEC);
  
  lcd.print("/");
  
  lcd.print(now.month(), DEC);
  
  lcd.print("/");
  
  lcd.print(now.year(), DEC);
  
  delay(500);
  
}

void setup() {

  Wire.begin();
  
  rtc.begin();
  
  rtc.adjust(DateTime(2019, 1, 10, 7, 59, 30)); // manual time set

  lcd.begin(16, 2);
  
  lcd.clear();
  
  lcd.setCursor(0, 0);
  
  lcd.print("Welcome To Our");
  
  lcd.setCursor(0, 1);
  
  lcd.print("Medicine Reminder");
  
  delay(1000);
  
  gwsMessege();
  
  delay(3000);
  
  helpScreen();
  
  delay(2000);

  timeScreen();
  
  delay(3000);
  
  lcd.clear();

  pinMode(buttonPin1, INPUT);
  
  pinMode(buttonPin2, INPUT);
  
  pinMode(buttonPin3, INPUT);
  
  pinMode(buttonPin4, INPUT);
  
  pinMode(buzz, OUTPUT);
  
  Serial.begin(9600);
}

void ValSet() {

  Serial.println(pushVal);
  
  switch (pushVal) 
  {
    case 1:
    
      lcd.clear();
      
      lcd.setCursor(0, 0);
      
      lcd.print("Reminder set ");
      
      lcd.setCursor(0, 1);
      
      lcd.print("for Once/day !");
      
      break;
      
    case 2:
    
      lcd.clear();

      lcd.setCursor(0, 0);
      
      lcd.print("Reminder set ");
      
      lcd.setCursor(0, 1);
      
      lcd.print("for Twice/day !");
      
      break;
      
    case 3:
    
      lcd.clear();
      
      lcd.setCursor(0, 0);
      
      lcd.print("Reminder set ");
      
      lcd.setCursor(0, 1);
      
      lcd.print("for Thrice/day !");
      
      break;
  }
  
}


void loop() {

  if (pushVal == 1) {
  
    at8am();
    
  } else if (pushVal == 2) {
  
    at8am();
    
    at8pm();
    
  } else if (pushVal == 3) {
  
    at8am();
    
    at2pm();
    
    at8pm();
    
  }

  bS1 = digitalRead(buttonPin1);
  
  bS2 = digitalRead(buttonPin2);
  
  bS3 = digitalRead(buttonPin3);
  
  bS4 = digitalRead(buttonPin4);
  

  if (bS2 != lBS2) {
  
    if (bS2 == HIGH) {
    
      Serial.println("Button 2 Pressed");
      
      pushVal = 1;
      
      delay(1000);
      
    }
  }
  
  lBS2 = bS2;

  if (bS3 != lBS3) {
  
    if (bS3 == HIGH) {
    
      Serial.println("Button 3 Pressed");
      
      pushVal = 2;
      
      delay(1000);
    }
    
  }
  
  lBS3 = bS3;

  if (bS4 != lBS4) {
  
    if (bS4 == HIGH) {
    
      Serial.println("Button 4 Pressed");
      
      pushVal = 3;
      
      delay(1000);
      
    }
  }
  
  lBS4 = bS4;

  if (bS1 != lBS1) {
  
    if (bS1 == HIGH) {
    
      val3 = pushVal;
      
      pushVal = 0;
      
      digitalWrite(buzz, LOW);
      
      pinstop();
      
      pushVal = val3;
      
    }
  }
  lBS1 = bS1;

  timeScreen();
  
  ValSet();
}

void push1() {

  lcd.clear();
  
  lcd.setCursor(0, 0);
  
  lcd.print("Reminder set ");
  
  lcd.setCursor(0, 1);
  
  lcd.print("for Once/day !");
  
  delay(1200);
  
  lcd.clear();
}

void push2() {

  lcd.clear();
  
  lcd.setCursor(0, 0);
  
  lcd.print("Reminder set ");
  
  lcd.setCursor(0, 1);
  
  lcd.print("for Twice/day !");
  
  delay(1200);
  
  lcd.clear();
}

void push3() {

  lcd.clear();

  lcd.setCursor(0, 0);
  
  lcd.print("Reminder set ");
  
  lcd.setCursor(0, 1);
  
  lcd.print("for Thrice/day !");
  
  delay(1200);
  
  lcd.clear();
}

void pinstop() {

  lcd.clear();
  
  lcd.setCursor(0, 0);
  
  lcd.print("Take Medicine  ");
  
  lcd.setCursor(0, 1);
  
  lcd.print("with Warm Water");
  
  delay(5000);
  
  lcd.clear();
}

void at8am() {

  DateTime t = rtc.now();
  
  if (int(t.hour()) == buzz8amHH && int(t.minute()) == buzz8amMM && (int(t.second()) == buzz8amSS || int(t.second()) < buzz8amSS + 10)) {
  
    digitalWrite(buzz, HIGH);
    
    lcd.clear();
    
    lcd.setCursor(0, 0);
    
    lcd.print("Time to take ");
    
    lcd.setCursor(0, 1);
    
    lcd.print("Morning medicines.");
    
    delay(5000);
    
  }
}

void at2pm() {

  DateTime t = rtc.now();
  
  if (int(t.hour()) == buzz2pmHH && int(t.minute()) == buzz2pmMM && (int(t.second()) == buzz2pmSS || int(t.second()) < buzz2pmSS + 10)) {
  
    digitalWrite(buzz, HIGH);
    
    lcd.clear();
    
    lcd.setCursor(0, 0);
    
    lcd.print("Time to take ");
    
    lcd.setCursor(0, 1);
    
    lcd.print("Afternoon medicines.");
    
    delay(5000);
    
  }
}

void at8pm() {

  DateTime t = rtc.now();
  
  if (int(t.hour()) == buzz8pmHH && int(t.minute()) == buzz8pmMM && (int(t.second()) == buzz8pmSS || int(t.second()) < buzz8pmSS + 10)) {
  
    digitalWrite(buzz, HIGH);
    
    lcd.clear();
    
    lcd.setCursor(0, 0);
    
    lcd.print("Time to take ");
    
    lcd.setCursor(0, 1);
    
    lcd.print("Night medicines.");
    
    delay(5000);
    
  }
  
}



## Arduino uno Error-Free Compilation Output:

![123](https://github.com/Advith147/MediEase-Simplifying-Health-Management/assets/109427643/e18e369e-31a0-4645-827f-0de8c5359440)

## RISCDUINO UNO Compilation Output:

![65](https://github.com/Advith147/MediEase-Simplifying-Health-Management/assets/109427643/b5e5e9e0-e657-4f48-a693-50f8a17411d3)

## BOM Cost:

| **Component name** | **Quantity Required** | **Unit price** | **Total Price**** (Unit price\*Quantity)** |
| --- | --- | --- | --- |
| DS3231 RTC Module Precise Real Time Clock I2C AT24C32 | 1 | 209 | 209 |
| Electronic Spices 16 x 4 Yellow/Green color LCD display module | 1 | 479 | 479 |
| Led | 4 | 20 | 80 |
| AT24C256 I2C Interface EEPROM Memory Module | 1 | 100 | 100 |
| Jumper Wires| 10 | 10 | 100 |
| 10K Potentiometer | 2 | 50 | 100 |
| Push Buttons | 1 | 50 | 50 |
| Breadboard | 1 | 50 | 50 |
| 10K,1K Resistors | 5 | 10 | 50 |
