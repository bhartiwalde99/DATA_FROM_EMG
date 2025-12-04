This repository contains firmware for a 6-motor bionic hand controlled using EMG gesture detection.
The system uses two EMG channels to detect short and long muscle activations, enabling grip switching, opening/closing, and grip locking.

The STM32 microcontroller sends finger angle commands over UART to the motor-control board.
#Features
--> EMG Gesture Recognition
EMG1 → PB0
EMG2 → PA7
Detects short (<500 ms) and long (>1000 ms) flexes
--> Supports 2 gesture combos:
EMG2 active → Close / Lock grip
EMG1 active → Open / Next grip

#6-Finger Bionic Hand Control
Each hand motion is sent as a 6-byte angle packet using a custom UART protocol.

#Predefined Grip Patterns
--> Includes 5 grips:
Power Grip
Lateral Grip
Pinch Grip
Tripod Grip
Point Grip

#Smooth Stepwise Motion
--> Each action sends two packets:
Partial movement
Final grip positions
Creates smooth transitions.

#Boot Motion
--> On startup:
Full open
Full close
Apply default grip (Power Grip)

#UART Communication
Baud Rate: 9600
Sync Byte: 199
Packet Format:
[199] [A1 A2 A3 A4 A5 A6]
Where A1..A6 are angles (0–90°) for 6 fingers.

#Grip Patterns (Default)
{"PowerGrip",   {90, 90, 90, 90, 90, 90}},
{"LateralGrip", {60, 60, 60, 60, 80, 40}},
{"PinchGrip",   {30, 0, 0, 0, 70, 40}},
{"TripodGrip",  {40, 40, 0, 0, 70, 30}},
{"PointGrip",   {0, 90, 90, 90, 40, 0}}

#Gesture Action Table
Gesture	Duration	Action
EMG2 only	Short	Close hand fully
EMG2 only	Long	Toggle grip lock
EMG1 only	Short	Open hand fully
EMG1 only	Long	Next grip + move hand

#Project File Structure
STM32-EMG-Bionic-Hand/
│
├── Core/
│   ├── Inc/
│   │   └── main.h
│   └── Src/
│       └── main.c     ← Firmware logic
│
├── Drivers/
│
└── README.md          ← This file

#Hardware Connections
Function	STM32 Pin
EMG Sensor 1	PB0
EMG Sensor 2	PA7
UART TX	PA2
UART RX	PA3
Motor driver receives UART packets
6 motors controlled externally

#Build Requirements
Software
STM32CubeIDE
HAL Drivers
Hardware
STM32 MCU (L4, F4, or compatible)
EMG sensors (2-channel)
Motor driver board
6 actuators (fingers)
UART cable or onboard connectivity

#Firmware Flow
START
 │
 ├── bootSequence()
 │     ├── Open hand
 │     ├── Close hand
 │     └── Set default grip
 │
 └── LOOP
        ├── detectGestures()
        │     ├── EMG1/EMG2 read
        │     ├── short or long gesture?
        │     ├── open/close hand
        │     ├── next grip
        │     └── lock/unlock grip
        │
        ├── stepwiseHandMotion()
        └── Delay 50 ms

#How to Modify Grip Patterns
--> Edit this array:
GripPatterns gripPatterns[] = {
   {"PowerGrip",   {90, 90, 90, 90, 90, 90}},
   {"LateralGrip", {60, 60, 60, 60, 80, 40}},
   ...
};

#Testing Tips
Check EMG polarity (active LOW or HIGH)
Ensure clean EMG signal with correct grounding
Use UART terminal to see outgoing packets
Slowly test each gesture separately
