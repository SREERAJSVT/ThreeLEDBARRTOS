# STM32 FreeRTOS Multi-Task LED Blinker

## Overview
This project demonstrates concurrent multitasking on an STM32 microcontroller using FreeRTOS. The system runs three independent RTOS tasks simultaneously. Each task controls a specific LED on a breadboard, toggling it at a unique frequency while printing debug information to a computer via a UART serial connection.

## Demo
## Demo

https://github.com/user-attachments/assets/4d47f1a1-9d6a-4c10-85bb-15dbc41c972d


The project features three LEDs (a green LED bar graph segment, an orange LED, and a red LED) blinking independently at 1000ms, 800ms, and 400ms intervals, respectively.

## Hardware Setup
* **Microcontroller:** STM32 Nucleo-64 (NUCLEO-F446RE)
* **Components:** 
  * 1x Half-size Breadboard
  * 1x 10-Segment LED Bar Graph (Green segment used)
  * 1x Orange/Yellow LED *(Mapped to the `BlueLed_Pin` in code)*
  * 1x Red LED
  * 3x Current-limiting resistors
* **Wiring:** USB Mini-B cable for programming and serial monitoring, assorted jumper wires.
**Hardware Wiring:**  
<p float="left">
  <img src="https://github.com/user-attachments/assets/227792de-8a9b-4b84-8f03-55f1ae3ea95f" alt="Setup Angle 1" width="400" />
  <img src="https://github.com/user-attachments/assets/0092d21e-a795-4dab-b9d0-296f5e6a8573" alt="Setup Angle 2" width="400" />
</p>
### Pin Connections
The LEDs are wired to the Arduino-style headers on the Nucleo board. All LED cathodes (short legs) share a common ground via the breadboard's ground rail.

| Code Variable | Physical LED Color | STM32 Pin | Arduino Header | Behavior |
| :--- | :--- | :--- | :--- | :--- |
| `GreenLed_Pin` | Green (Bar Graph) | **PA5** | **D13** | Toggles every 1000 ms |
| `RedLed_Pin` | Red LED | **PA7** | **D11** | Toggles every 800 ms |
| `BlueLed_Pin` | Orange LED | **PA6** | **D12** | Toggles every 400 ms |

## Software Architecture
The project is built using **STM32CubeIDE** and relies on **FreeRTOS** for task scheduling. 

### FreeRTOS Tasks
The scheduler manages three independent tasks created in `main.c`:

1. **Firstled_handler:** 
   * **Priority:** 2
   * **Action:** Toggles the Green LED on D13, prints `"task1\n"` via UART, and delays for 1000ms.
2. **Secondled_handler:** 
   * **Priority:** 2
   * **Action:** Toggles the Red LED on D11, prints `"task2\n"` via UART, and delays for 800ms.
3. **Tryledhandler:** 
   * **Priority:** 2
   * **Action:** Toggles the Orange LED on D12, prints `"task3\n"` via UART, and delays for 400ms.

### Serial Output (UART)
The project utilizes **USART2** configured at a **115200 baud rate**. The `_write` function is overridden in the code to redirect standard C library `printf` calls to the USB Virtual COM Port. Opening a terminal (like PuTTY or Tera Term) will display real-time logs indicating which task is currently executing.

## How to Run the Project
1. Wire the LEDs to pins D11, D12, and D13, ensuring resistors are placed in series to protect the LEDs.
2. Connect the STM32 board to your computer via USB.
3. Open the project in **STM32CubeIDE**.
4. Click **Build** to compile the `.elf` file.
5. Click **Debug** to flash the code to the STM32 microcontroller.
6. Press **Resume** to start the FreeRTOS scheduler and watch the LEDs blink.
7. Open a Serial Terminal at `115200` baud to view the `printf` logs.

## Developer Notes
* **Delay Mechanism:** The current iteration uses `HAL_Delay()` followed by `taskYIELD()` inside the task loops to manage execution time. While functional for this demonstration, standard FreeRTOS best practices recommend replacing `HAL_Delay()` with `vTaskDelay()` or `osDelay()` to prevent blocking the CPU and allow the RTOS scheduler to manage sleep states more efficiently.
