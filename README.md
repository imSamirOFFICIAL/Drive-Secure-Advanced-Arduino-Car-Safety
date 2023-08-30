# Drive Secure: Advanced Arduino Car Safety
This project aims to enhance vehicle safety using an Arduino-based system. The system integrates various sensors and components such as hall effect sensors, ultrasonic sensors, temperature sensors, LEDs, and a motor controller. Through real-time monitoring and control, the system ensures seat belt usage, prevents collision through distance detection, manages speed, and alerts the driver in case of potentially hazardous conditions like alcohol detection and high temperature. The project highlights the integration of these technologies to create a comprehensive car safety solution, demonstrating the potential of embedded systems in enhancing road safety.

<p align="center"><img src="https://user-images.githubusercontent.com/52858312/264367768-cdf6fb7d-f225-4312-ab7d-3ed4da8a61f5.jpg"width=30% height=30%></p>

# Component List:
> 1. Arduino Uno R3
> 2. MQ-3 Alcohol Detector Gas Sensor
> 3. A3144 Hall Effect Sensor (2x)
> 4. HC-SR04 Ultrasonic Distance Sensor
> 5. LM35 Temperature Sensor
> 6. 16x2 I2C LCD Module Display
> 7. L293D Motor Driver Shield
> 8. DC Geared Motor (4x)
> 9. Plastic Mag Wheel (4x)
> 10. 100K Potentiometer
> 11. Active Buzzer
> 12. Push Button (2x)
> 13. White (2x) & Red (3x) LED
> 14. 220 Ω Resistor (5x)
> 15. 3000mAh 3.7v Battery

# Arduino Code:
    #include  <Wire.h>
    
    #include  <AFMotor.h>
    
    #include  <LiquidCrystal_I2C.h>
    
      
    
    AF_DCMotor motor1(1);
    
    AF_DCMotor motor2(2);
    
    AF_DCMotor motor3(3);
    
    AF_DCMotor motor4(4);
    
      
    
    LiquidCrystal_I2C lcd(0x27, 16, 2);
    
      
    
    const  int SeatBeltPin = 2;
    
    const  int OdometerPin = 3;
    
    const  int UltrasonicTrigPin = 4;
    
    const  int UltrasonicEchoPin = 5;
    
    const  int AlertBuzzerPin = 6;
    
    const  int AlertLEDPin = 7;
    
    const  int LEDButtonPin = 8;
    
    const  int FrontLeftLEDPin = 9;
    
    const  int FrontRightLEDPin = 10;
    
    const  int BrakeButtonPin = 11;
    
    const  int BackLeftLEDPin = 12;
    
    const  int BackRightLEDPin = 13;
    
    const  int AcceleratorPin = A0;
    
    const  int AlcoholGasPin = A1;
    
    const  int TemperaturePin = A3;
    
      
    
    bool magnetDetected1 = false;
    
    bool magnetDetected2 = false;
    
    int traveledDistance = 0;
    
    const  float wheelSizeCm = 6.5;
    
      
    
    void  setup()  {
    
    motor1.setSpeed(0);
    
    motor2.setSpeed(0);
    
    motor3.setSpeed(0);
    
    motor4.setSpeed(0);
    
    motor1.run(RELEASE);
    
    motor2.run(RELEASE);
    
    motor3.run(RELEASE);
    
    motor4.run(RELEASE);
    
    pinMode(SeatBeltPin, INPUT);
    
    pinMode(OdometerPin, INPUT);
    
    pinMode(UltrasonicTrigPin, OUTPUT);
    
    pinMode(UltrasonicEchoPin, INPUT);
    
    pinMode(BrakeButtonPin, INPUT_PULLUP);
    
    pinMode(LEDButtonPin, INPUT_PULLUP);
    
    pinMode(AlertBuzzerPin, OUTPUT);
    
    pinMode(AlertLEDPin, OUTPUT);
    
    pinMode(BackLeftLEDPin, OUTPUT);
    
    pinMode(BackRightLEDPin, OUTPUT);
    
    pinMode(FrontLeftLEDPin, OUTPUT);
    
    pinMode(FrontRightLEDPin, OUTPUT);
    
    lcd.init();
    
    lcd.backlight();
    
    }
    
      
    
    void  loop()  {
    
    int seatBeltValue = digitalRead(SeatBeltPin);
    
    int odometerValue = digitalRead(OdometerPin);
    
    int acceleratorValue = analogRead(AcceleratorPin);
    
    int alcoholValue = analogRead(AlcoholGasPin);
    
    int reading = analogRead(TemperaturePin);
    
    float voltage = reading * (5.0 / 1024.0);
    
    float temperatureC = (voltage * 100) - 10;
    
    int brakeButtonState = digitalRead(BrakeButtonPin);
    
    int ledPushButtonState = digitalRead(LEDButtonPin);
    
    digitalWrite(UltrasonicTrigPin, LOW);
    
    delayMicroseconds(2);
    
    digitalWrite(UltrasonicTrigPin, HIGH);
    
    delayMicroseconds(10);
    
    digitalWrite(UltrasonicTrigPin, LOW);
    
    unsigned  long duration = pulseIn(UltrasonicEchoPin, HIGH);
    
    float distanceCm = duration * 0.034 / 2;
    
      
    
    int motorSpeed = map(acceleratorValue, 1023, 0, 0, 255);
    
    motor1.setSpeed(motorSpeed);
    
    motor2.setSpeed(motorSpeed);
    
    motor3.setSpeed(motorSpeed);
    
    motor4.setSpeed(motorSpeed);
    
      
    
    if  (brakeButtonState == LOW || odometerValue == HIGH || alcoholValue > 500 || temperatureC > 100 || distanceCm < 10)  {
    
    motor1.run(RELEASE);
    
    motor2.run(RELEASE);
    
    motor3.run(RELEASE);
    
    motor4.run(RELEASE);
    
    digitalWrite(AlertBuzzerPin, HIGH);
    
    digitalWrite(AlertLEDPin, HIGH);
    
    digitalWrite(BackLeftLEDPin, HIGH);
    
    digitalWrite(BackRightLEDPin, HIGH);
    
    }  else  {
    
    digitalWrite(AlertBuzzerPin, LOW);
    
    digitalWrite(AlertLEDPin, LOW);
    
    digitalWrite(BackLeftLEDPin, LOW);
    
    digitalWrite(BackRightLEDPin, LOW);
    
    if  (ledPushButtonState == LOW)  {
    
    digitalWrite(FrontLeftLEDPin, HIGH);
    
    digitalWrite(FrontRightLEDPin, HIGH);
    
    }  else  {
    
    digitalWrite(FrontLeftLEDPin, LOW);
    
    digitalWrite(FrontRightLEDPin, LOW);
    
    }
    
    if  (seatBeltValue == LOW && !magnetDetected1)  {
    
    traveledDistance += wheelSizeCm;
    
    magnetDetected1 = true;
    
    }  else  if  (seatBeltValue == HIGH)  {
    
    magnetDetected1 = false;
    
    }
    
    if  (odometerValue == LOW && !magnetDetected2)  {
    
    magnetDetected2 = true;
    
    }  else  if  (odometerValue == HIGH)  {
    
    magnetDetected2 = false;
    
    }
    
    motor2.run(FORWARD);
    
    motor2.run(FORWARD);
    
    motor2.run(FORWARD);
    
    motor2.run(FORWARD);
    
    }
    
    lcd.setCursor(0, 0);
    
    lcd.print("Traveled: ");
    
    lcd.print(traveledDistance);
    
    lcd.print(" cm");
    
    delay(100);
    
    }
