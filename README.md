# AntiWettKeepsYourClothDry
using Arduino Uno, Water Sencor, Blue LED and a Servo Motor I have created a Device that will keep your cloth dry if it starts to rain why they are drying.
#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>
#include <Servo.h>

// Pin Definitions
const int waterSensorPin = A0;  // Analog pin for water sensor
const int ledPin = 13;          // Digital pin for LED
const int servoPin = 9;         // Digital pin for Servo Motor

// OLED Display Setup
#define SCREEN_WIDTH 128
#define SCREEN_HEIGHT 64
Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, -1);

// Servo motor
Servo myServo;

// Threshold for water sensor detection
int waterThreshold = 600; // Adjust as needed (depends on your sensor)

void setup() {
  // Start Serial Communication for Debugging
  Serial.begin(9600);

  // Setup the LED pin
  pinMode(ledPin, OUTPUT);

  // Setup the water sensor pin (A0)
  pinMode(waterSensorPin, INPUT);

  // Setup the servo motor
  myServo.attach(servoPin);

  // Initialize OLED Display
  if (!display.begin(SSD1306_SWITCHCAPVCC, 0x3C, SCREEN_WIDTH, SCREEN_HEIGHT)) {
    Serial.println(F("SSD1306 allocation failed"));
    for (;;);
  }
  display.display();
  delay(2000); // Pause for 2 seconds

  // Initialize the Servo Motor to a neutral position
  myServo.write(90); // 90 degrees (centered position)
}

void loop() {
  // Read water sensor value
  int waterLevel = analogRead(waterSensorPin);
  Serial.print("Water Sensor Value: ");
  Serial.println(waterLevel);

  // Display water sensor reading on OLED
  display.clearDisplay();
  display.setTextSize(1);
  display.setTextColor(SSD1306_WHITE);
  display.setCursor(0, 0);
  display.print("Water Level: ");
  display.print(waterLevel);

  // Check if water is detected
  if (waterLevel > waterThreshold) {
    // Water detected: Turn on LED and move servo
    digitalWrite(ledPin, HIGH);
    myServo.write(0); // Move servo to 0 degrees (e.g., fully open)
    display.setCursor(0, 20);
    display.print("Water Detected!");
  } else {
    // No water detected: Turn off LED and reset servo
    digitalWrite(ledPin, LOW);
    myServo.write(90); // Reset servo to neutral position (90 degrees)
    display.setCursor(0, 20);
    display.print("No Water Detected.");
  }

  // Display updated information on OLED
  display.display();

  // Wait for a short time before taking another reading
  delay(500);
}
