# Team Bathybot - Official WRO 2026 Future Engineers Repository

<div align="center">
  <strong>World Robot Olympiad 2026 | Future Engineers Category | Autonomous Driving Challenge</strong>
</div>

---

## 1. Cover
Welcome to the official open-source repository of **Team Bathybot**. This repository contains the complete engineering documentation, mechanical design rationale, source code, and project history for our fully autonomous robotic vehicle designed for the WRO 2026 Future Engineers category. 

Our documentation is structured to provide a transparent, detailed view of our engineering journey, from initial differential-drive concepts to our current mechanically steered architecture.

---

## 2. Team Introduction
We are **Team Bathybot**, a dedicated robotics team pushing the boundaries of what is possible with the LEGO SPIKE Prime hardware ecosystem. Our team is composed of passionate students focused on mechanical engineering, software architecture, and algorithm optimization. We believe in iterative design, rigorous testing, and open-source collaboration.

---

## 3. Project Overview
The core objective of this project is to engineer a 1/18 scale autonomous vehicle capable of navigating a randomized track environment without human intervention. Using a strictly unified hardware platform, the robot must process environmental data in real-time, make driving decisions, and execute complex maneuvers to avoid obstacles while remaining within the track's physical boundaries.

---

## 4. WRO Future Engineers Challenge
The WRO Future Engineers category simulates real-world autonomous driving. The robot must operate on a closed-circuit track featuring straightaways, internal and external curves, and varying lane widths. Furthermore, the robot must detect and avoid randomly placed traffic blocks (representing pedestrians or stalled vehicles) dynamically, changing its racing line on the fly and completing the circuit as fast as possible.

---

## 5. Engineering Objectives
To succeed in this challenge, Team Bathybot established the following core engineering objectives:
* **Dimensional Compliance:** Strict adherence to the $\le 300 \times 200 \times 300$ mm size limit and $\le 1.5$ kg weight limit.
* **Mechanical Reliability:** Transitioning from an unstable differential drive to a robust, physical front-steering axle for predictable cornering.
* **Sensor Efficiency:** Maximizing data throughput by relying exclusively on an array of three ultrasonic sensors, discarding unnecessary optical sensors to reduce computational load.
* **Algorithmic Resilience:** Developing a reactive state machine capable of micro-corrections (wall following) and macro-evasions (obstacle bypassing).

---

## 6. Repository Structure
In accordance with WRO guidelines, this repository is organized to facilitate easy navigation for judges and open-source contributors:
* `/src`: Contains the main `.llsp3` block code files.
* `/models`: Contains CAD representations and physical dimensions.
* `/docs`: Includes engineering diaries, logs, and competition rubrics.
* `/media`: Contains photographs (all required angles) and official video links.

---

## 7. Development Timeline
Our vehicle is the product of continuous iteration. Below is the chronological evolution of our hardware and software architectures.

### • May 2026: Initial Concept
Brainstorming and theoretical analysis of the WRO 2026 ruleset. Selection of the SPIKE Prime platform for its rapid prototyping capabilities.

### • June 2026: Version 1
Initial build featuring a Rear-Wheel Drive (RWD) differential steering system. The design was compact but suffered from significant mechanical play in the transmission axles, leading to erratic turning behaviors.

### • Late June 2026: Version 2
Integration of the full sensor suite (3 ultrasonic sensors + 1 color sensor). Development of the baseline lane-tracking algorithm. We discovered that differential steering at high speeds was too aggressive and unpredictable for the SPIKE Hub's polling rate.

### • July 9, 2026: National Competition (Colegio Las Cumbres)
We competed with an intermediate build. The competition highlighted the critical need for physical steering rather than relying on differential motor speeds, as the robot struggled with stability during sharp obstacle evasion maneuvers.

### • Version 3 (Post-Competition Improvements)
Following the July 9 competition, a complete mechanical overhaul was initiated. This version, currently under active development, introduces a mechanical front steering axle, unifies the rear motors for pure drive power, and completely removes the color sensor to streamline processing.

---

## 8. Mechanical Design
The transition to Version 3 required a fundamental rethinking of our mechanical chassis.

* **Chassis:** Built using a reinforced LEGO Technic frame, designed to keep the center of gravity as low as possible.
* **Rear-Wheel Drive (Unified):** The two rear motors are now mechanically and logically linked. Instead of differential turning, they provide pure, synchronized forward and reverse thrust.
* **Steering Axle:** A mechanical front steering rack was engineered. Driven by an independent servomotor, this axle physically turns both front wheels simultaneously, mimicking real-world automotive Ackermann steering principles.
* **Weight Distribution:** The SPIKE Hub and battery were relocated centrally to balance the load between the steering axle and the drive wheels, preventing understeer.
* **Structural Improvements:** Cable management was entirely redone to optimize internal space and allow unobstructed movement of the steering rack.

---

## 9. Electronics
The electronic ecosystem is entirely self-contained within the official LEGO hardware constraints.

* **LEGO SPIKE Prime Hub:** Acts as the central processing unit, power supply, and motor controller.
* **Port Mapping:**
    * **Port A:** Unified Rear Drive Motor(s)
    * **Port B:** Front Steering Motor
    * **Port C:** Left Ultrasonic Sensor
    * **Port D:** Front Ultrasonic Sensor
    * **Port F:** Right Ultrasonic Sensor

---

## 10. Sensor System
The vehicle perceives its environment through echolocation, utilizing high-frequency sound waves to map the track boundaries and obstacles.

* **Front Ultrasonic (Port D):** The primary radar. Dedicated exclusively to detecting dynamic obstacles (traffic blocks) in the vehicle's driving lane.
* **Left Ultrasonic (Port C):** Monitors the distance to the left inner/outer track walls for continuous lane centering.
* **Right Ultrasonic (Port F):** Symmetrical to the left sensor, handling right-side boundary management.
* **Why the Color Sensor Was Removed:** During the redesign process following the Las Cumbres competition, the color sensor was removed to provide additional physical space for the new steering mechanism. Furthermore, removing it simplified the control architecture, allowing the main loop to run faster. The final navigation strategy now relies *exclusively* on the three ultrasonic sensors.

---

## 11. Software Architecture
The `.llsp3` codebase is structured as a continuous loop, handling multiple parallel tasks:

* **Initialization:** Sets motor limits, zeroes the steering rack, and establishes baseline speeds.
* **Navigation:** Commands constant forward thrust to Port A.
* **Wall Following:** A background process constantly polling Ports C and F. If the distance drops below a threshold, it calculates a proportional angle to feed to the steering motor (Port B).
* **Obstacle Avoidance:** An interrupt function triggered by Port D. Overrides wall following to execute a hard steering maneuver around the block.
* **Steering Logic (Current Development):** The algorithm translating ultrasonic distances into precise servo angles for the mechanical axle is currently in the active calibration phase.

---

## 12. Navigation Algorithm
Our algorithm is a reactive state machine. Unlike predictive path planning (which requires mapping), our robot reacts instantly to its immediate surroundings.
1.  **State 0 (Clear Path):** Drive forward at maximum safe speed, keeping steering at 0 degrees.
2.  **State 1 (Boundary Proximity):** Adjust steering angle slightly (micro-correction) away from the closest wall.
3.  **State 2 (Obstacle Detected):** Brake, check lateral sensors, apply maximum steering lock to the open side (macro-correction), bypass, and return to State 0.

---

## 13. Engineering Decisions
The most significant engineering decision was abandoning differential steering in favor of a mechanical front steering axle. While differential steering is easier to build and program, it relies heavily on perfect tire traction. On dusty tracks, differential robots drift and lose localization. The mechanical steering axle guarantees a fixed turning radius regardless of floor friction, drastically improving the reliability of our evasion maneuvers.

---

## 14. Testing Process
Testing is conducted in three phases:
1.  **Bench Testing:** Verifying the steering servo angles and ultrasonic polling rates while the robot is elevated.
2.  **Straight-Line Calibration:** Ensuring the unified rear drive pushes the robot perfectly straight without steering input.
3.  **Track Simulation:** Running the robot on the official WRO mat to fine-tune the trigger distances for wall following (currently set to ~15 cm) and obstacle detection (~40 cm).

---

## 15. Competition Results
At the National Competition at Colegio Las Cumbres on July 9, the Version 2 robot demonstrated excellent straight-line speed but struggled with obstacle evasion consistency. The differential turning proved too violent, occasionally causing the robot to clip the inner boundaries after passing a traffic block. These results directly informed the Version 3 overhaul.

---

## 16. Improvements After July 9 Competition
The post-competition engineering review led to the birth of Version 3:
* Implementation of the steering rack.
* Removal of the color sensor.
* Rewriting the software to handle analog steering angles rather than differential wheel speeds.

---

## 17. Current Limitations
As Version 3 is currently in development, we are actively working on resolving:
* **Steering Calibration:** Finding the perfect proportional gain for the wall-following algorithm so the steering does not oscillate (fish-tail) down the straights.
* **Polling Latency:** Managing the slight delay when reading three ultrasonic sensors sequentially in the SPIKE block environment.

---

## 18. Future Improvements
Looking ahead to the International Finals, we plan to implement:
* A custom Proportional-Integral-Derivative (PID) controller for the steering axle to achieve perfectly smooth curves.
* A localized Odometry system using the built-in motor encoders to track the exact distance traveled, aiding in the final parking maneuver.

---

## 19. Installation Guide
1.  Download and install the latest version of the **LEGO Education SPIKE App**.
2.  Clone this repository: `git clone https://github.com/YourUsername/Bathybot-WRO-2026.git`
3.  Open the SPIKE app, select "Open Project", and navigate to the `/src` folder to load the `.llsp3` file.

---

## 20. Running the Robot
1.  Ensure the physical build matches the Port Mapping in Section 9 exactly.
2.  Power on the SPIKE Hub.
3.  Connect via Bluetooth or USB and download the program to Slot 0.
4.  Place the robot behind the start line, ensure a 50cm clear perimeter, and press the center button to begin the autonomous run.

---

## 21. Engineering Philosophy
Team Bathybot embraces the ethos of "Fail Fast, Learn Faster." The complete teardown of our robot after the July 9 competition exemplifies our commitment to not settling for "good enough." We prioritize mechanical solutions to physical problems, rather than trying to patch bad hardware with complex software.

---

## 22. Credits
* **World Robot Olympiad Association:** For providing an incredible platform for future engineers.
* **Team Bathybot Members:** For their relentless dedication to mechanics and code.
* *World Robot Olympiad and the WRO logo are trademarks of the World Robot Olympiad Association Ltd.*
