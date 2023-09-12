#include "Wire.h"
#include <MPU6050_light.h>
#include <Servo.h>
#define fan_Pin 5
#define thrust_fan_Pin 7
#define front_sense_echo 3  //P6
#define front_sense_trig 13
#define side_sense_echo 2  //P10 on RIGHT side
#define side_sense_trig 11
#define Servo_Pin 9
Servo ServoMotor;
#define filter_window 5
MPU6050 mpu(Wire);
unsigned long timer = 0;
float front_duration;
float side_duration;
float front_distance;
float side_distance;
float angle;
float offset;
bool turning_left, turning_right;
float thrustFanSpeed = 130;

//-----------------------------------------------------------angle correction
void correct_angle()
{
  update_angle();
      delay(1000);
      unsigned long correct_angle_timeout = 0;
      unsigned long correct_angle_start = millis();
      while(abs(angle + offset) > 1)
      {
        update_angle();
        if((angle+offset)>1)
        {
          writeToServoMotor(90);
          fans_on();
          while((angle+offset)>1)
          {
            update_angle();
            correct_angle_timeout = millis();
            if(correct_angle_timeout - correct_angle_start > 3000)
            {
              fans_off();
              delay(1000);
              return;
            }
          }
          fans_off();
        }
        else if((angle+offset)<-1)
        {
          writeToServoMotor(-90);
          fans_on();
          while((angle+offset)<-1)
          {
            update_angle();
            correct_angle_timeout = millis();
            if(correct_angle_timeout - correct_angle_start > 3000)
            {
              fans_off();
              delay(1000);
              return;
            }
          }
          fans_off();
        }
      }
      fans_off();
      delay(1000);
}
//write to servo---------------------------------------------
void writeToServoMotor(float degree) {
  degree += 90;
  if (degree >= 180) {
    ServoMotor.write(179);
  } else if (degree <= 0) {
    ServoMotor.write(1);
  } else {
    ServoMotor.write(degree);
  }
}
//-----------------------------------------------------read front
void read_front() {
  digitalWrite(front_sense_trig, LOW);
  delayMicroseconds(2);

  digitalWrite(front_sense_trig, HIGH);
  delayMicroseconds(12);
  digitalWrite(front_sense_trig, LOW);
  front_duration = pulseIn(front_sense_echo, HIGH);
  front_distance = (front_duration / 2) * 0.0343;
}
//-----------------------------------------------------read side
void read_side() {
  digitalWrite(side_sense_trig, LOW);
  delayMicroseconds(2);

  digitalWrite(side_sense_trig, HIGH);
  delayMicroseconds(12);
  digitalWrite(side_sense_trig, LOW);
  side_duration = pulseIn(side_sense_echo, HIGH);
  side_distance = (side_duration / 2) * 0.0343;
}
//-----------------------------------------------------shut off both fans
void fans_off() {
  analogWrite(thrust_fan_Pin, 0);
  analogWrite(fan_Pin, 0);
}
//-----------------------------------------------------turn on both fans
void fans_on() {
  analogWrite(thrust_fan_Pin, thrustFanSpeed);
  analogWrite(fan_Pin, 255);
}
//-----------------------------------------------------update the mpu and "angle"
void update_angle() {
  mpu.update();
  if ((millis() - timer) > 10) {
    angle = (mpu.getAngleZ()/2) ;  // print data every 10ms
    Serial.print("front: ");
    Serial.print(front_distance);
    Serial.print("\tSide: ");
    Serial.print(side_distance);
    Serial.print("\tAngle : ");
    Serial.println(angle);
    timer = millis();
  }
}
//-----------------------------------------------------decide right of left
void right_or_left() {
  if (side_distance > 50)  //turn right
  {
    offset += 75;
    turning_right = true;
  } else if (side_distance <= 50)  //turn left
  {
    offset += -65;
    turning_left = true;
  }
}

// ----------------------------------------------------------Turning Right
void TurnRight() {
  while ((angle + offset) > 3)  //first 90 degrees
  {
    writeToServoMotor(angle + offset);
    update_angle();
  }
  fans_off();
  delay(1000);
  read_front();
  update_angle();
  if (front_distance > 30) {//going forward towards the wall
    while (front_distance > 30) {
      update_angle();
      writeToServoMotor(angle + offset);
      update_angle();
      fans_on();
      update_angle();
      update_angle();
      update_angle();
      read_front();
      update_angle();
    }
  }
  update_angle();
  fans_off();
  update_angle();
  offset += 75;
  writeToServoMotor(angle + offset);
  update_angle();
  delay(1000);
  update_angle();
  fans_on();
  while ((angle + offset) > 30)  //seconds 90 degrees
  {
    update_angle();
    writeToServoMotor(angle + offset);
  }
  fans_off();
  delay(1000);
  turning_right = false;
  fans_on();
}
//-----------------------------------------------------------Turning Left
void TurnLeft() {
  read_front();
  update_angle();
  update_angle();
  fans_off();
  update_angle();
  delay(1000);
  update_angle();
  fans_on();
  //offset += 30;
  while ((angle + offset) < -20) {
    writeToServoMotor(angle + offset);
    update_angle();
  }
  fans_off();
  delay(1000);
  read_front();
  update_angle();
  offset += 10;
  if (front_distance > 30) {
    while (front_distance > 30) {
      update_angle();
      writeToServoMotor(angle + offset);
      update_angle();
      fans_on();
      update_angle();
      read_front();
      update_angle();
    }
  }
  fans_off();
  offset += -65;
  writeToServoMotor(angle + offset);
  delay(1000);
  fans_on();
  while ((angle + offset) < -40) {
    update_angle();
    writeToServoMotor(angle + offset);
  }
  fans_off();
  delay(1000);
  turning_left = false;
  fans_on();
}
//-----------------------------------------------------
void setup() {
  turning_left = false;
  turning_right = false;
  offset = 0;

  //-------------------------IMU init
  Wire.begin();
  byte status = mpu.begin();
  Serial.print(F("MPU6050 status: "));
  Serial.println(status);
  while (status != 0) {}  // stop everything if could not connect to MPU6050

  Serial.println(F("Calculating offsets, do not move MPU6050"));
  delay(1000);
  mpu.calcOffsets();  // gyro and accelero
  Serial.println("Done!\n");
  //----------------------------initialize servo
  ServoMotor.attach(Servo_Pin);
  writeToServoMotor(0);
  //--------------------------initialize US sensors
  pinMode(side_sense_trig, OUTPUT);
  pinMode(side_sense_echo, INPUT);
  Serial.begin(9600);
  pinMode(front_sense_trig, OUTPUT);
  pinMode(front_sense_echo, INPUT);
  //--------------------------------------initialize fans
  pinMode(fan_Pin, OUTPUT);
  pinMode(thrust_fan_Pin, OUTPUT);
  for(int i = 0; i<filter_window;i++)
  {
    update_angle();
  }
  delay(1000);
}

void loop() {
  update_angle();
  read_front();
  fans_on();
  if (front_distance <= 80) 
  {
    update_angle();
    fans_off();
    update_angle();
    delay(1000);
    update_angle();
    //correct_angle();
    update_angle();
    fans_on();
    update_angle();
    while(front_distance>35)
    {
      read_front();
      update_angle();
      writeToServoMotor(angle+offset);
    }
    if (front_distance <= 35) {
      fans_off();
      //correct_angle();
      update_angle();
      delay(1000);
      update_angle();
      read_side();
      update_angle();
      right_or_left();
      update_angle();
      writeToServoMotor(angle + offset);
      update_angle();
      delay(200);
      update_angle();
      fans_on();
      update_angle();
      if (turning_right == true) {
        TurnRight();
        //fix_right();
        update_angle();
        offset +=-30;
      } else if (turning_left == true) {
        //offset += 35;
        TurnLeft();
        //fix_left();
        thrustFanSpeed = 255;
        fans_on();
        update_angle();
        offset += 45;
      }
    }
    update_angle();
    writeToServoMotor(angle + offset);
  }
  update_angle();
  writeToServoMotor(angle + offset);
}
