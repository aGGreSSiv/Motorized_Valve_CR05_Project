# Motorized Valve (CR05) Control 🚰

![Platform](https://img.shields.io/badge/Platform-ESPHome-orange)
![Hardware](https://img.shields.io/badge/Hardware-ESP32-green)
![Device](https://img.shields.io/badge/Device-CR05_Valve-blue)
[![AliExpress](https://img.shields.io/badge/Buy_Now-AliExpress-red)](https://www.aliexpress.com/item/1005006683654073.html)


This project integrates a **CR05 Motorized Valve** into Home Assistant using an ESP32 and ESPHome. It provides a reliable alternative to solenoid valves, especially for water with high limescale content where solenoids often fail.

## Overview

I previously used this type of valve with Blynk Legacy, but with Blynk's discontinuation, I finally migrated the system to Home Assistant. This setup ensures your valves are smart, reliable, and fully integrated into your home automation.

### Why Choose the CR05 Valve?

There are several types of these valves (CR01, CR02, CR03, CR04, CR05). **CR05** is the ideal choice for this project because:

*   **Mechanism**: It consists of a motor, a gearbox (for high torque), and 2 limit switches (triggered at "fully open" and "fully closed" positions).
*   **Control**: Unlike simple solenoid valves, you cannot just plug this in. It requires a proper driver circuit to handle the motor direction and stop it at the limits. This is where our ESP32 project comes in.

**Key Benefits of this project:**
1.  **Limescale Resistance**: Motorized ball valves are much more resistant to limescale build-up than solenoid valves.
2.  **Feedback**: The limit switches provide feedback. You know for sure if the valve is physically open or closed.
3.  **Safety Logic**: You can create automations to alert you if the valve fails to close/open.
4.  **Redundancy (Recommended)**: For critical applications (e.g., main water line), it is highly recommended to use **two valves in series**. You can set up logic where one valve verifies the operation of the other. If one fails (e.g., limit switch not triggered within 1 minute), the other can shut off the water.

## Hardware Requirements

*   **Motorized Valve**: CR05 Type, 12V Version (Recommended). ([Product Link](https://www.aliexpress.com/item/1005006683654073.html))
    *   *Note on Voltage*: The 5V version is too slow and weak. Even if you accidentally buy a 5V version, it often works better with 12V supply (proceed at your own risk, but 12V is standard recommendation).
    *   *Pipe Size*: Choose the correct size for your plumbing (e.g., DN15 is common for households).
*   **Microcontroller**: ESP32 (Lolin32 or similar).
*   **Motor Driver**: L298N (or similar L29xx based driver).
*   **Power Supply**: 12V DC Adapter (Powers the valve and the L298N; the ESP32 can be powered via the 5V output of the L298N or a separate USB).

## Wiring

The system requires **7 GPIO pins**:
*   **3 Pins** for the Motor Driver (ENA, IN1, IN2).
*   **2 Pins** for the Limit Switches (Open Limit, Closed Limit).
*   *Note: The YAML also defines an LEDC pin for PWM speed control if needed.*

### Useful Diagrams

**Valve Connection & Pinout:**

![Motorized Valve Details](MotorizedValve.png)

**L29xx Motor Driver Wiring:**

![L29xx Motor Driver](L29xx%20Motor%20Driver.png)

*You can use the 5V output from the L298N driver to power your ESP32.*

**Schematic Diagram:**

![Schematic Diagram](Schematic%20Diagram01.png)

*Note: The connections in the schematic are symbolic. Please refer to the YAML file for the exact GPIO pin mappings.*

## Important Configuration Notes

### 1. Initial Testing (Critical)
**Before permanently installing:**
Open the valve housing and separate the motor/gearbox from the valve mechanism if possible, OR just dry run it.
*   Power up the system.
*   Send an **"Open"** command.
*   Manually trigger the **"Open Limit Switch"**. The motor **MUST STOP**.
*   If the motor stops when you trigger the *Closed* switch instead, your limit switches are wired in reverse. Swap the connections.

### 2. Logic Explanation
The ESPHome configuration uses an "inverted" logic for the limit switches (assuming `INPUT_PULLUP`):
*   **Pressed (Triggered)** = Logic 0 (in hardware) -> Inverted to `ON` in ESPHome.
*   **Released (Not Triggered)** = Logic 1 (in hardware) -> Inverted to `OFF` in ESPHome.

This ensures that `binary_sensor.is_on` correctly means "The limit switch is currently pressed."

### 3. Usage Tip
If using multiple valves, consider adding a master valve for the main water inlet. Connect it to a water leak sensor in Home Assistant to automatically shut off all water if a leak is detected.

## File Structure
*   `valve_control.yaml`: The complete ESPHome configuration file.
*   `images/`: Contains the connection diagrams.


