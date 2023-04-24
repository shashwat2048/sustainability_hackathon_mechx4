# sustainability_hackathon_mechx4
hackathon submission
//project main.ino
#include <SoftwareSerial.h>
#include <LiquidCrystal_I2C.h>
#include <Servo.h>

Servo myservo;  // create servo object to control a servo
// twelve servo objects can be created on most boards

int pos = 0;    // variable to store the servo position

//Define ultrasonic sensor pins
#define TRIG_PIN1 7
#define ECHO_PIN1 6
#define TRIG_PIN2 11
#define ECHO_PIN2 10

// Define moisture sensor pin
#define MOISTURE_PIN A0

// Define IR sensor pin
#define IR_PIN 4

// Define servo motor pin
#define SERVO_PIN 12

// Define buzzer pin
#define BUZZER_PIN 5
SoftwareSerial mySerial(2,3);

// Define GSM module pins and settings


// Define LCD screen settings
LiquidCrystal_I2C lcd(0x27, 16, 2);

void setup() {
  Serial.begin(9600);
  Serial.println("init... start");
  // Initialize ultrasonic sensors
  pinMode(TRIG_PIN1, OUTPUT);
  pinMode(ECHO_PIN1, INPUT);
   pinMode(TRIG_PIN2, OUTPUT);
  pinMode(ECHO_PIN2, INPUT);

mySerial.begin(9600);
  // Initialize moisture sensor
  pinMode(MOISTURE_PIN, INPUT);

  // Initialize IR sensor
  pinMode(IR_PIN, INPUT);

  // Initialize servo motor
  pinMode(SERVO_PIN, OUTPUT);

  // Initialize buzzer
  pinMode(BUZZER_PIN, OUTPUT);

  // Initialize LCD screen
   lcd.init();
   lcd.backlight();
  myservo.attach(12);
 myservo.write(90); 
  // Initialize GSM module

 Serial.println("init... end");
}

void loop() {
   
   // Read ultrasonic sensor values
   Serial.println("start");
  long duration1, distance1, duration2, distance2;
  digitalWrite(TRIG_PIN1, LOW);
  delayMicroseconds(2);
  digitalWrite(TRIG_PIN1, HIGH);
  delayMicroseconds(10);
  digitalWrite(TRIG_PIN1, LOW);
  duration1 = pulseIn(ECHO_PIN1, HIGH);
  distance1 = duration1 * 0.034 / 2;
  digitalWrite(TRIG_PIN2, LOW);
  delayMicroseconds(2);
  digitalWrite(TRIG_PIN2, HIGH);
  delayMicroseconds(10);
  digitalWrite(TRIG_PIN2, LOW);
  duration2 = pulseIn(ECHO_PIN2, HIGH);
  distance2 = duration2 * 0.034 / 2;
Serial.println(distance1);
Serial.println(distance2);


Serial.println(distance1);

  // Read moisture sensor value
  int moistureValue = analogRead(MOISTURE_PIN);
  Serial.println(moistureValue);

  // Read IR sensor value
  int irValue = digitalRead(IR_PIN);
Serial.println(irValue);
  // Determine waste type based on sensor values
  String wasteType = "";
  if (distance1 <10 || distance2 <10) {
    digitalWrite(BUZZER_PIN, HIGH);
    wasteType = "Metal";
    lcd.setCursor(0, 1);
    lcd.print("Dustbin full ");
    delay(2000);
    lcd.clear();
    Serial.println("Dustbin is full ");
      mySerial.println("AT+CMGF=1");    //Sets the GSM Module in Text Mode
  delay(1000);  // Delay of 1000 milli seconds or 1 second
  mySerial.println("AT+CMGS=\"+919756143731\"\r"); // Replace x with mobile number
  delay(1000);
  mySerial.println("Dustbin is full");// The SMS text you want to send
  delay(100);
   mySerial.println((char)26);// ASCII code of CTRL+Z
  delay(1000);
    digitalWrite(BUZZER_PIN, LOW);
    
  }
  else if (moistureValue < 800 && irValue == LOW) {
    wasteType = "Wet";
    lcd.setCursor(0, 0);
    lcd.print("Wet Waste ");
    Serial.println("Wet Waste ");
    servoL();  
    delay(200);
  } else if (moistureValue > 800 && irValue == LOW) {
    wasteType = "Dry";
    lcd.setCursor(0, 0);
    lcd.print("Dry Waste ");
    Serial.println("Dry Waste ");
    servoR();
    delay(200);
  }

  // Open servo motor to deposit waste
  
}

//



void servoL(){
    myservo.write(180);              // tell servo to go to position in variable 'pos'
    delay(2000);                       // waits 15ms for the servo to reach the position
 
   // goes from 180 degrees to 0 degrees
    myservo.write(90);              // tell servo to go to position in variable 'pos'
    delay(2000);                       // waits 15ms for the servo to reach the position
 
    }
    void servoR(){
    myservo.write(0);              // tell servo to go to position in variable 'pos'
    delay(2000);                       // waits 15ms for the servo to reach the position
 
   // goes from 180 degrees to 0 degrees
    myservo.write(90);              // tell servo to go to position in variable 'pos'
    delay(2000);                       // waits 15ms for the servo to reach the position
 
    }
