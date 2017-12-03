#include <Wire.h> 
#include "Adafruit_LEDBackpack.h"
#include "Adafruit_GFX.h"
//coin sensor floats high, goes low on coin detect
#define coinHopperPin 2 // Pin for signal from Cube Hopper coin sensor

int buttonPin = 8;  // GUI button
int ledPin = 13;  // 
int relayPin = 12;  // solid state relay for cube hopper 12v motor
bool relay = LOW;  // motor off
bool buttonState = HIGH;
int counter;  //count coins ejected
int sets; 
int cycles;  // number of payCycles
int payCycle; // coins to eject per cycle
int count;

long currentTime;

volatile int state;
volatile int hits;
volatile long pulseStartTime;
volatile long pulseStopTime;

Adafruit_7segment matrix = Adafruit_7segment();

void setup() {
  
  // INT.0 = '0' (pin 2), INT.1 = '1' (pin 3)
  // coin hopper coin sensor is high when no coin, goes low when coin present
  // count coin at fall from 5v, then stop motor when coin clears (sensor raises back to 5v)
  attachInterrupt(0, countCoins, FALLING); // count when falling from 5v
  digitalWrite(coinHopperPin, INPUT_PULLUP); //set internal pull up resistor
  pinMode(buttonPin, INPUT_PULLUP);
  pinMode(ledPin, OUTPUT);
  pinMode(relayPin, OUTPUT);
  matrix.begin(0x70);
  matrix.println(counter);
  matrix.writeDisplay();
  
  hits = 0;
  state = LOW;
  counter = 0;
  sets = 0;
  count = 0;
  
  cycles = 4; //number of pay cycles
  payCycle = 1; // coins paid per cycle
  
}

void countCoins() {
    pulseStartTime = millis();
    counter++;
}


void loop() {

  currentTime = millis();
  buttonState = digitalRead(buttonPin);

  if (sets < cycles) {
    matrix.println(count); // duration of each pulse
    matrix.writeDisplay();
  }
  else {
    int payOut = cycles * payCycle; // payout is number of coins oer cycles time num cycles
    matrix.println(payOut); // duration of each pulse
    matrix.writeDisplay();
  }
  
  if (counter >= payCycle) {
      delay(50); // clear coin from shoot
      digitalWrite(relayPin, LOW);  //stop motor
      relay = LOW; //motor is off
      count = counter; // save count to be displayed
      counter = 0;  // reset number of coins per cycle
      sets++; // each cycle is a set
      
      delay(500); //wait between each cycle
      }
   else {
    if (sets < cycles) {
      digitalWrite(relayPin, HIGH);  //start motor
      relay = HIGH; //motor is on
      }
   else {
      digitalWrite(relayPin, LOW);  //stop motor
      relay = LOW; //motor is off
      }
   }
     

  if (buttonState == LOW && relay == LOW) {
      digitalWrite(relayPin, HIGH);  //start motor
      relay = HIGH; //motor is on
      delay(100); // debounce
      }
  else if (buttonState == LOW && relay == HIGH) {
      digitalWrite(relayPin, LOW);  //stop motor
      relay = LOW; //motor is off
      delay(100);
      }
      
}
