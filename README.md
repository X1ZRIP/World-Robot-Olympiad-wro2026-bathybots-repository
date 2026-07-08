# Team Bathybot - Official WRO 2026 Future Engineers Repository

[cite_start]Welcome to the official GitHub repository of **Team Bathybot**, proudly competing in the **World Robot Olympiad (WRO) 2026 - Future Engineers Category**[cite: 62]. This repository contains the complete documentation, mechanical design rationale, source code, and engineering progress of our fully autonomous robotic vehicle. 

[cite_start]Real engineering is about creating a solution and sharing that idea with others to push concepts forward[cite: 70]. [cite_start]In accordance with the international competition guidelines, all information and documentation within this GitHub repository is provided in English[cite: 74]. [cite_start]This repository is set to public visibility and will remain accessible for at least 12 months post-competition to inspire future teams[cite: 104].

---

## 1. Project Overview & Rules Compliance

The Future Engineers category challenges students to simulate real-world autonomous driving conditions. As part of our engineering deliverables, this repository has been structured to fulfill all official requirements:
* [cite_start]**Documentation Checklist:** We have included comprehensive descriptions, information, and justifications regarding our vehicle's mobility, power system, sensing, and obstacle management protocols[cite: 76].
* [cite_start]**Visual Evidence:** The repository includes photographs of the vehicle from all sides (top, bottom, left, right, front, back), alongside an official team photograph[cite: 77]. 
* [cite_start]**Autonomous Demonstration:** Included are public YouTube links showcasing the vehicle operating autonomously[cite: 78]. [cite_start]Each video demonstrates autonomous driving for a minimum duration of 30 seconds [cite: 79][cite_start], with one dedicated video provided per challenge[cite: 80].
* [cite_start]**Source Code & Manufacturing:** The root directory contains all source code programmed for the competition[cite: 81], alongside the necessary `.llsp3` files.
* [cite_start]**Commit History Rules:** Our repository maintains a rigorous version control history with at least three major commits tracking our engineering progress[cite: 83]. [cite_start]The first commit was made at least two months prior to the event [cite: 84][cite_start], the second at least one month prior [cite: 85][cite_start], and the final main commit at least two weeks before the competition[cite: 90].

---

## 2. Electromechanical Architecture & Port Mapping

Our autonomous vehicle is built upon the highly versatile LEGO SPIKE Prime hardware ecosystem. To ensure a robust connection between the mechanical components and the software, we established a strict, modular port mapping architecture. [cite_start]This clarifies exactly how the software modules relate to the electromechanical components[cite: 96].

### 2.1. Main Controller
* **Hub Central SPIKE Prime:** The brain of our vehicle is the LEGO SPIKE Prime Hub. It manages power distribution, sensor data acquisition, and motor control simultaneously. 

### 2.2. Drivetrain & Actuators (Outputs)
The vehicle's mobility system features an independent rear-wheel differential drive, powered by two dedicated servomotors.
* **Port A - Left Rear Motor:** Controls the independent traction of the left rear wheel.
* **Port B - Right Rear Motor:** Controls the independent traction of the right rear wheel.
* **Mobility Justification:** By driving the rear wheels independently, the vehicle can utilize differential steering. Applying equal power moves the robot straight. Applying power to only one motor, or reversing one while advancing the other, allows for zero-radius turning. This eliminates the need for a complex steering rack, reducing weight, saving space, and minimizing mechanical failure points during high-speed autonomous runs.

### 2.3. Sensor Array & Perception (Inputs)
To manage the track limits and randomly placed obstacles safely, the vehicle utilizes a four-sensor array designed for immediate environmental feedback.
* **Port C - Left Ultrasonic Sensor:** Mounted facing outward to the left, this sensor continuously monitors the distance to the inner or outer track walls, ensuring the vehicle does not cross the left boundary.
* **Port D - Front Ultrasonic Sensor:** The primary radar. Mounted facing strictly forward, it is responsible for the early detection of track obstacles (traffic blocks) in the vehicle's driving lane.
* **Port E - Color Sensor:** Mounted underneath the chassis, facing the floor. This sensor is utilized to detect track colors, control zones, and starting/finishing lines as required by the WRO mat topology.
* **Port F - Right Ultrasonic Sensor:** Symmetrical to Port C, this sensor monitors the distance to the right-side walls, providing data for right-side boundary corrections.

---

## 3. Software Architecture & Algorithmic Logic

The source code is developed using the block-based visual environment of the LEGO Education SPIKE App. The algorithm is a reactive state machine designed to loop continuously, analyzing sensor inputs and adjusting the motor outputs in milliseconds. [cite_start]The code is divided into three primary modules to clarify the system's logic[cite: 96].

### 3.1. Initialization & Forward Kinematics Module
When the program begins, the code configures the initial hardware states. It links Port A and Port B as a unified movement pair but allows independent percentage control for turning. The default movement speed is set aggressively to maximize lap times during obstacle-free zones. Once initialized, the default state is continuously driving forward unless interrupted by a sensor event.

### 3.2. Micro-Evasion (Wall Centering) Module
This module runs in parallel to prevent the vehicle from crashing into the perimeter walls, relying on Ports C and F.
* **Left Boundary Correction:** If the Left Ultrasonic Sensor (Port C) reads a distance below 15 cm, the robot stops its forward motion, reverses the Left Rear Motor (Port A), and drives the Right Rear Motor (Port B) forward to pivot the vehicle away from the wall.
* **Right Boundary Correction:** Symmetrically, if the Right Ultrasonic Sensor (Port F) reads a distance below 15 cm, the robot halts and executes a leftward pivot to regain the center of the lane.

### 3.3. Macro-Evasion (Obstacle Management) Module
This module handles the dynamic traffic blocks. It overrides the Micro-Evasion module when an obstacle is detected in the direct path.
* **Trigger:** The Front Ultrasonic Sensor (Port D) detects an object closer than 40 cm.
* **Logic:** The robot immediately halts to prevent a collision. It then queries the lateral sensors. If the Right Sensor (Port F) indicates open space, the vehicle reverses slightly, pivots to the right (powering Port A more than Port B), drives past the obstacle, and corrects its trajectory back to the lane. If the right side is blocked, it executes a mirrored bypass maneuver to the left.

---

## 4. Setup, Compilation, and Execution Process

[cite_start]To ensure transparency and allow the judges or other teams to replicate our results, the process for compiling, loading, and executing the code is detailed below[cite: 96]. [cite_start]Note that judges may not have the specific programming software installed, so this guide serves as a comprehensive operational manual[cite: 109].

### 4.1. Prerequisites
1. Ensure the physical vehicle is assembled exactly according to the provided photographs and that all cables are routed to their assigned ports (A-F) as detailed in Section 2.
2. Download and install the official **LEGO Education SPIKE App** on a compatible computer or tablet.
3. Download the `jovenes_ing_codigo_sin_obstaculos2.llsp3` file from this GitHub repository.

### 4.2. Compilation and Loading
1. Open the LEGO SPIKE App and select "Open Project".
2. Locate the downloaded `.llsp3` file and open it. The software will automatically compile the visual blocks.
3. Turn on the physical SPIKE Prime Hub by pressing the center button.
4. Connect the Hub to the computer via Bluetooth or the provided USB cable.
5. In the software's bottom-right corner, select an empty memory slot (e.g., Slot 0).
6. Click the "Download" icon to flash the compiled source code directly to the Hub's non-volatile memory.

### 4.3. Execution on the Track
1. Place the vehicle in the designated starting zone on the WRO track.
2. Ensure there are no false obstacles within 50 cm of the front, left, or right sensors to prevent early algorithm triggers.
3. On the SPIKE Prime Hub, use the left/right arrow buttons to navigate to the correct memory slot (Slot 0).
4. Press the center button once to execute the program. The vehicle will instantly enter autonomous mode.
5. To perform an emergency stop, press the center button again.

---

## 5. Engineering Challenges & Iterations

During development, we encountered several mechanical and programmatic challenges. Initially, our differential drive suffered from wheel slippage during sharp turns. We solved this by shifting the physical weight of the SPIKE Hub directly over the rear axle, increasing the normal force on the rear tires. 

Furthermore, we experienced ultrasonic crosstalk when Ports C, D, and F fired simultaneously. We refined our software module by introducing a polling delay, ensuring the sensors pulse sequentially rather than simultaneously, significantly increasing our environmental mapping accuracy.

***
*End of documentation. For further inquiries or detailed CAD models, please refer to the specific folders within this repository.*