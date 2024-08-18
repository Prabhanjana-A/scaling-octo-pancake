#include <Wire.h>
#include <LiquidCrystal_I2C.h>
#include <SoftwareSerial.h>

// Constants
const int alcoholPin = A0;
const int motorPin1 = 9;
const int motorPin2 = 10;
const int buzzerPin = 6;
const int ledPin = 5;
const int threshold = 300; // Replace with the actual threshold value

// LCD and GSM Initialization
LiquidCrystal_I2C lcd(0x27, 16, 2); // Adjust the I2C address as needed
SoftwareSerial gsm(7, 8); // RX, TX for GSM module

void setup() {
  // Pin setup
  pinMode(alcoholPin, INPUT);
  pinMode(motorPin1, OUTPUT);
  pinMode(motorPin2, OUTPUT);
  pinMode(buzzerPin, OUTPUT);
  pinMode(ledPin, OUTPUT);

  // LCD and GSM initialization
  lcd.init();
  lcd.backlight();
  gsm.begin(9600);

  // Ensure motor is off at startup
  digitalWrite(motorPin1, LOW);
  digitalWrite(motorPin2, LOW);
}

void loop() {
  // Read alcohol level
  int alcoholValue = analogRead(alcoholPin);

  // Display alcohol level on LCD
  lcd.setCursor(0, 0);
  lcd.print("Alcohol Level: ");
  lcd.print(alcoholValue);

  if (alcoholValue > threshold) {
    // Stop the motor
    digitalWrite(motorPin1, LOW);
    digitalWrite(motorPin2, LOW);

    // Activate the buzzer
    digitalWrite(buzzerPin, HIGH);

    // Display alcohol detection message on LCD
    lcd.setCursor(0, 1);
    lcd.print("Alcohol Detected");

    // Blink the LED to indicate detection
    for (int i = 0; i < 10; i++) {
      digitalWrite(ledPin, HIGH);
      delay(500);
      digitalWrite(ledPin, LOW);
      delay(500);
    }

    // Send location via GSM
    sendLocation();
  } else {
    // Deactivate the buzzer and LED
    digitalWrite(buzzerPin, LOW);
    digitalWrite(ledPin, LOW);

    // Display safe message on LCD
    lcd.setCursor(0, 1);
    lcd.print("Safe to Start  ");
  }

  delay(1000); // Wait before the next reading
}

void sendLocation() {
  // Simulate sending an SMS with GPS location
  Serial.println("Simulating SMS:");
  Serial.println("AT+CGNSINF"); // Command to get GPS location
  delay(1000);

  Serial.println("AT+CMGF=1"); // Set SMS to text mode
  delay(1000);
  Serial.println("AT+CMGS=\"+1234567890\""); // Replace with actual contact number
  delay(1000);
  Serial.println("Alcohol detected. Location: Latitude, Longitude"); // Simulated GPS data
  // gsm.write(26); // ASCII code for Ctrl+Z to send the SMS (not applicable in simulation)
}
