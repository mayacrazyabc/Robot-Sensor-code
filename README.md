#include <Servo.h>

// Create Servo Objects
Servo leftServo;
Servo rightServo;

// Sensor Pins
const int aSensor = A1;
const int bSensor = A0;
const int cSensor = A2;

// Function to Read Distance
long getDistance(int pin)
{
  pinMode(pin, OUTPUT);

  digitalWrite(pin, LOW);
  delayMicroseconds(2);

  digitalWrite(pin, HIGH);
  delayMicroseconds(5);

  digitalWrite(pin, LOW);

  pinMode(pin, INPUT);

  long duration = pulseIn(pin, HIGH, 30000);

  if (duration == 0)
    return 300;

  long distance = duration / 29 / 2;

  return distance;
}

void setup()
{
  Serial.begin(9600);

  leftServo.attach(13);
  rightServo.attach(12);

  // Stop Robot
  leftServo.write(90);
  rightServo.write(90);
}

void loop()
{
  long aDistance = getDistance(aSensor);
  long bDistance = getDistance(bSensor);
  long cDistance = getDistance(cSensor);

  Serial.print("A Sensor: ");
  Serial.print(aDistance);
  Serial.print(" cm   ");

  Serial.print("B Sensor: ");
  Serial.print(bDistance);
  Serial.print(" cm   ");

  Serial.print("C Sensor: ");
  Serial.print(cDistance);
  Serial.println(" cm");

  // All Sensors Clear → Move Forward
  if (aDistance > 20 && bDistance > 20 && cDistance > 20)
  {
    leftServo.write(180);
    rightServo.write(0);
  }

  // Object on B Sensor → Turn Right
  else if (bDistance < 20)
  {
    leftServo.write(180);
    rightServo.write(180);
    delay(400);
  }

  // Object on C Sensor → Turn Left
  else if (cDistance < 20)
  {
    leftServo.write(0);
    rightServo.write(0);
    delay(400);
  }

  // Object on A Sensor
  else if (aDistance < 20)
  {
    if (bDistance > cDistance)
    {
      // Turn Left
      leftServo.write(0);
      rightServo.write(0);
    }
    else
    {
      // Turn Right
      leftServo.write(180);
      rightServo.write(180);
    }

    delay(500);
  }

  // Stop
  else
  {
    leftServo.write(90);
    rightServo.write(90);
  }

  delay(100);
}
