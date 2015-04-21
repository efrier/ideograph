// *****************************************************************
// Control program for IDEOgraph - disc drawing and cutting machine
// Ethan Frier for IDEO Chicago
// epfrier@gmail.com
// December 2014 
// *****************************************************************


#include <AccelStepper.h>
#include <Servo.h>

int pirPin = 2;                    // motion sensor connected to digital pin 2
int ave[100];
int servoPin = 9;                  // servo connected to digital pin 9
Servo servo;
AccelStepper table(1, 5, 4);       // table stepper connected to Sparkfun Easydriver STEP to pin 5 and DIR to pin 4
AccelStepper arm(1, 6, 7);         // arm stepper connected Sparkfun Easydriver STEP to pin 6 and DIR to pin 7
unsigned long previousMillis = 0;  // servo timer
const long interval = 1000;  


void setup() {
  Serial.begin(9600);
  
  pinMode(pirPin, INPUT);
  
  delay(1000);                    // delay is to keep the servo from twitching on startup 
  
  servo.attach(servoPin);
  
  table.setMaxSpeed(-100);        
  table.setSpeed(-100);           // set table speed in steps per second

  arm.setMaxSpeed(.25);          
  arm.setSpeed(.25);              // set arm speed in steps per second - .25 for cut, unlimited for draw 
  arm.moveTo(540);                // total number of steps arm takes before shutdown - 540 for 13.5in disc

}

void loop() {
       
  unsigned long currentMillis = millis();            // timing function for servo control
  const long interval = 1000;
  if(currentMillis - previousMillis >= interval) {
        
    previousMillis = currentMillis;           
  
    int total = 0;

    for (int i = 0; i < 100; i++) {                  // array storing motion sensor data for smoothing
      ave[i] = digitalRead(pirPin);                  // assign motion data to array
      total += ave[i];                               // add total number of motion instances in array
    }

    float average;                                  
    average = total / 100;                           // average motion sensor data to determine smooth motion data for last 100 moments 
    Serial.print(average);
    Serial.print("    ");
   
    if (average > 0) {                               // when no motion is sensed
      servo.write(150);                              // servo position to hover over paper = 150
    }
    
    else {                                           // when motion is sensed
      servo.write(130);                              // servo position to cut = 80, to draw = 130
    }       
   
    int pirVal = digitalRead(pirPin);
    if(pirVal == LOW){                               // print to serial if motion is detected
      Serial.print("motion");  
      Serial.print("    ");

    }
    if(pirVal == HIGH){                              // print to serial if no motion is detected 
      Serial.print("still"); 
      Serial.print("     ");

    }
    Serial.println(arm.distanceToGo());              // print to serial steps left in cycle 

  }    

  if (arm.distanceToGo() > 0)
  {
    arm.run();                   // start the table 
    table.runSpeed();            // start the arm 
  }
  
  else
  {
    arm.stop();                  // stop the table when arm is at center
    table.stop();                // stop the arm when it reaches the center 
  }   

}
