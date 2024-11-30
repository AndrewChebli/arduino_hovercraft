## Overview

Our hovercraft is equipped with various components to ensure stability, obstacle detection, and precise navigation. The code integrates input from an IMU (Inertial Measurement Unit) and ultrasonic sensors to guide the hovercraft, with motors and fans providing the necessary movement and control.

## Features

	•	Autonomous Navigation: The hovercraft adjusts its path based on sensor input to avoid obstacles and maintain stability.
	•	Angle Correction: Real-time angle adjustments using the MPU6050 IMU to ensure a straight or corrected path.
	•	Obstacle Detection: Ultrasonic sensors detect obstacles in front and on the sides, enabling the hovercraft to decide when to turn.
	•	Fan Control: Motors and fans work together to control movement and speed.
	•	Servo Integration: A servomotor steers the hovercraft based on the calculated angle corrections.

## Components Used

	•	Fans: Provide thrust and lift for the hovercraft.
	•	Batteries: Power the components and fans.
	•	Servo Motor: Controls the direction of the hovercraft.
	•	Ultrasonic Sensors: Detect obstacles in front and on the side.
	•	MPU6050 IMU: Measures angles and orientation for real-time navigation.

## How It Works

	1.	Initialization:
	  •	The IMU calculates its offsets for accurate readings.
	  •	Servo motor and ultrasonic sensors are initialized.
	2.	Angle Correction:
	  •	The hovercraft monitors its orientation using the IMU and adjusts its path to maintain or correct its direction.
	3.	Obstacle Avoidance:
	  •	Ultrasonic sensors measure the distance to obstacles.
	  •	Based on the distance, the hovercraft decides whether to turn left, turn right, or continue forward.
	4.	Fan and Servo Control:
	  •	Fans provide propulsion, and the servomotor controls the hovercraft’s turning angle.

## Code

The main functionality is handled in the loop() function, which continuously updates sensor data, adjusts the hovercraft’s angle, and decides the next action based on obstacle distances.
	•	correct_angle(): Ensures the hovercraft maintains or corrects its orientation.
	•	TurnRight() and TurnLeft(): Maneuver the hovercraft when an obstacle is detected.
	•	fans_on() and fans_off(): Control the fans for movement and stopping.

## Items Used

	•	Fans: For propulsion and lift.
	•	Batteries: Power supply for all components.
	•	Servo Motor: Direction control.
	•	Ultrasonic Sensors: Obstacle detection (front and side).
	•	MPU6050 IMU: Angle and orientation measurement.

## Project Image:
![8D2DE304-9558-4C83-A301-A767DB5C9F82](https://github.com/AndrewChebli/arduino_hovercraft/assets/97767309/adf4af92-81e7-4bb8-9700-967516ed37f2)
