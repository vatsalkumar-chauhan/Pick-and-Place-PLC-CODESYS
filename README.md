# Pick-and-Place-PLC-CODESYS
Sequential ladder logic for a Pick and Place robot using CODESYS. Includes fault detection, E-Stop safety, position sensor feedback and reset logic.
# Pick and Place Robot — PLC Ladder Logic (CODESYS)

**Author:** Vatsalkumar Chauhan  
**Course:** B.Tech Robotics and Automation Engineering  
**Institution:** Parul Institute of Technology, Parul University  
**Tool:** CODESYS V3.5 SP22  
**Language:** Ladder Logic (IEC 61131-3)

---

## About This Project

This is a PLC ladder logic program I made for a Pick and Place robot 
as part of my PLC Hands-on Workshop training. The program controls 
the full sequence of the robot — from picking up an object to placing 
it at the target position — using step-based sequential logic.

The program runs on CODESYS simulation and is designed to work with 
a real PLC hardware setup as well.

---

## What the Robot Does

The robot follows this sequence automatically after the Start button 
is pressed:

1. **Arm Down** — Robot arm moves down toward the object
2. **Grip** — Gripper closes and holds the object
3. **Extend** — Arm extends to move object to target position
4. **Arm Up** — Arm lifts up after reaching target position
5. **Release** — Gripper opens and releases the object
6. **Return to Idle** — System resets and waits for next cycle

---

## State Diagram

![Pick and Place PLC State Diagram](flowchart.png)

**Note:** An E-Stop input is connected to every motion rung in the 
program. If E-Stop is activated at any point during the sequence, 
all motion outputs stop immediately. The system remains halted until 
the fault is cleared and Reset_Button is pressed.

---

## Inputs and Outputs

### Inputs
| Variable | Type | Description |
|---|---|---|
| Start_Button | BOOL | Starts the pick and place cycle |
| Stop_Button | BOOL | Stops the system and resets to idle |
| Reset_Button | BOOL | Resets system after a fault |
| E_Stop | BOOL | Emergency stop — halts all motion immediately |
| Sensor_Object | BOOL | Detects object presence at pick position |
| Sensor_Position | BOOL | Confirms arm reached target position |

### Outputs
| Variable | Type | Description |
|---|---|---|
| Arm_Down | BOOL | Moves arm downward |
| Arm_Up | BOOL | Moves arm upward |
| Arm_Extend | BOOL | Extends arm to target position |
| Gripper_Close | BOOL | Closes gripper to grip object |

### Internal Step Flags
| Variable | Description |
|---|---|
| Step_Idle | System is idle and waiting |
| Step_ArmDown | Arm moving down |
| Step_Grip | Gripper closing |
| Step_Extend | Arm extending to target |
| Step_ArmUp | Arm moving up |
| Fault | Fault detected |

---

## Timers Used

| Timer | Time | Purpose |
|---|---|---|
| T_ArmDown | 3 seconds | Waits for arm to reach down position |
| T_Grip | 2 seconds | Waits for gripper to close fully |
| T_Fault | 5 seconds | Timeout if arm does not reach target position |

---

## Ladder Logic — Program Structure

The program has 18 rungs total. Here is a summary of each:

| Rung | Description |
|---|---|
| 1 | Start sequence — Start_Button sets Step_ArmDown |
| 2 | Resets Step_Idle when Step_ArmDown starts |
| 3 | Activates Arm_Down output during ArmDown step |
| 4 | Starts T_ArmDown timer during ArmDown step |
| 5 | After 3s timer — sets Step_Grip, resets Step_ArmDown |
| 6 | Activates Gripper_Close during Grip step |
| 7 | Starts T_Grip timer during Grip step |
| 8 | After 2s timer — sets Step_Extend, resets Step_Grip |
| 9 | Activates Arm_Extend output during Extend step |
| 10 | Starts T_Fault timer — stops automatically when position detected |
| 11 | Sets Fault if arm does not reach position within 5 seconds |
| 12 | Resets all steps and returns to idle after fault + reset button |
| 13 | Sets Step_ArmUp when position sensor confirms target reached |
| 14 | Resets Step_Extend when Step_ArmUp starts |
| 15 | Activates Arm_Up output during ArmUp step |
| 16 | Cycle complete — sets Step_Idle, resets Step_ArmUp |
| 17 | Opens gripper to release object when arm is up |
| 18 | Stop button — resets all steps and returns to idle |

---

## Safety Features

- **E-Stop** — Connected to every motion rung. Stops all outputs instantly
- **Fault Detection** — 5 second timeout if arm does not reach position
- **Reset Button** — Separate from E-Stop. Required to recover from fault
- **Stop Button** — Clean shutdown at any point in the cycle

---

## Screenshots

### Static Code View
*(Add your static code screenshots here)*

### Live Simulation
*(Add your simulation screenshots here — timer counting, active rungs)*

---

## How to Run

1. Open CODESYS V3.5 or later
2. Open the project file `Pick_and_Place_PLC.project`
3. Go to **Online → Simulation Mode**
4. Click **Login** then **Start**
5. Force `Step_Idle = TRUE` to initialise
6. Force `Start_Button = TRUE` to begin the cycle
7. Force `Sensor_Position = TRUE` when arm reaches extend position

---

## What I Learned

This project taught me how to write proper sequential ladder logic 
using Set/Reset coils instead of basic output coils. I also learned 
how to implement fault detection using timers, and how important 
safety interlocks like E-Stop are in real industrial systems. The 
most challenging part was getting the fault timer to reset correctly 
when the position sensor was detected — which required adding 
Sensor_Position as a normally closed contact in series with the 
timer input.

---

## Related

This PLC workshop was part of my B.Tech final year preparation 
alongside my main project AUTOM8 — a staircase-climbing campus 
delivery robot built using Arduino Mega, DC motors, and ultrasonic 
sensors.
