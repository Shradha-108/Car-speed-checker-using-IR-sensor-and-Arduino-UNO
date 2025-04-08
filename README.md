# Car-speed-checker-using-IR-sensor-and-Arduino-UNO
This project involves the development of a speed detection system using Arduino and IR sensors to monitor and alert traffic authorities about speeding violations on highways. The system calculates vehicle speed and triggers real-time alerts when a car exceeds the speed limit.
#include <LiquidCrystal.h>

// Initialize the LCD (RS, E, D4, D5, D6, D7)
LiquidCrystal lcd(7, 8, 9, 10, 11, 12);

#define IR_SENSOR_1 2 // First IR sensor
#define IR_SENSOR_2 3 // Second IR sensor
#define BUZZER 4      // Buzzer pin

const float distance = 0.5; // Distance between sensors in meters (adjust as needed)
unsigned long t1 = 0, t2 = 0;
float speed = 0;

void setup() {
    pinMode(IR_SENSOR_1, INPUT);
    pinMode(IR_SENSOR_2, INPUT);
    pinMode(BUZZER, OUTPUT);
    lcd.begin(16, 2);
    lcd.print("Speed Checker");
    delay(2000);
    lcd.clear();
}

void loop() {
    if (digitalRead(IR_SENSOR_1) == LOW) { // Vehicle crosses first sensor
        t1 = millis();
        lcd.clear();
        lcd.setCursor(0, 0);
        lcd.print("T1: ");
        lcd.print(t1 / 1000.0, 3); // Display time in seconds
        lcd.print(" sec");
        delay(500); // Debounce delay
    }
    
    if (digitalRead(IR_SENSOR_2) == LOW) { // Vehicle crosses second sensor
        t2 = millis();
        lcd.clear();
        lcd.setCursor(0, 0);
        lcd.print("T2: ");
        lcd.print(t2 / 1000.0, 3); // Display time in seconds
        lcd.print(" sec");
        
        if (t1 > 0) {
            float time_taken = (t2 - t1) / 1000.0; // Convert ms to seconds
            speed = distance / time_taken; // Speed in m/s
            speed *= 3.6; // Convert to km/h
            
            lcd.setCursor(0, 1);
            lcd.print("Speed: ");
            lcd.print(speed);
            lcd.print(" km/h");
            
            if (speed > 60) { // Speed limit (adjust as needed)
                digitalWrite(BUZZER, HIGH);
                lcd.clear();
                lcd.setCursor(0, 0);
                lcd.print("Over Speeding!");
                delay(3000);
                digitalWrite(BUZZER, LOW);
            }
            delay(2000);
        }
        t1 = 0; // Reset t1 for the next reading
    }
}
