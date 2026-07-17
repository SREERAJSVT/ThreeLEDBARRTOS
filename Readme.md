# STM32 FreeRTOS Multi-Task LED Blinker

## Overview
This project demonstrates basic multitasking on an STM32 microcontroller using FreeRTOS. The objective of this assignment is to run three separate FreeRTOS tasks concurrently, where each task controls a different LED at a specific blink rate and sends debug messages over UART to a computer terminal.

## Hardware Used
* **Microcontroller:** STM32 Nucleo-64 board (e.g., NUCLEO-F446RE)
* **Components:** Breadboard, 3x LEDs (used a mix of standard LEDs and an LED bar graph in the physical setup), 3x Current-limiting resistors (220Ω - 330Ω)
* **Cables:** USB Mini-B cable for programming/serial monitor, assorted jumper wires.

## Pin Connections (Wiring)
As seen in the project photo, the external LEDs are wired on a breadboard and connected to the Arduino-style headers on the Nucleo board. All LED cathodes (short legs) share a common ground.

| LED Color (Code Variable) | STM32 Pin | Arduino Header | Behavior |
| :--- | :--- | :--- | :--- |
| **Green** (`GreenLed_Pin`) | **PA5** | **D13** | Toggles every 1000 ms |
| **Blue** (`BlueLed_Pin`) | **PA6** | **D12** | Toggles every 800 ms |
| **Red** (`RedLed_Pin`) | **PA7** | **D11** | Toggles every 400 ms |

## Software & RTOS Configuration
The project is built using **STM32CubeIDE** and utilizes **FreeRTOS** for task scheduling. 

### Task Breakdown
The RTOS scheduler manages three independent tasks created in `main.c`:

1.  **Firstled_handler:** 
    * Priority: 2
    * Action: Toggles the Green LED, prints `"task1\n"` via serial, and delays for 1000ms.
2.  **Secondled_handler:** 
    * Priority: 2
    * Action: Toggles the Red LED, prints `"task2\n"` via serial, and delays for 800ms.
3.  **Tryledhandler:** 
    * Priority: 2
    * Action: Toggles the Blue LED, prints `"task3\n"` via serial, and delays for 400ms.

### Serial Output (UART)
The project utilizes **USART2** configured at a **115200 baud rate**. When connected to a serial terminal (like PuTTY or Tera Term) on the computer, the board actively prints which task is currently executing to verify that the RTOS scheduler is switching contexts correctly.

## How to Run the Project
1. Wire the LEDs to pins D11, D12, and D13, ensuring resistors are placed in series with the LEDs to ground.
2. Connect the STM32 board to your PC via USB.
3. Open the project in **STM32CubeIDE**.
4. Click **Build** (hammer icon) to compile the code.
5. Click **Debug** (bug icon) to flash the code to the STM32.
6. Press **Resume** (play icon) to start the FreeRTOS scheduler.
7. Open a Serial Terminal at `115200` baud to view the `printf` task logs.

## Important Developer Notes / Known Issues
* **Delay Mechanism:** Currently, the code uses `HAL_Delay()` inside the task loops. Because `HAL_Delay()` relies on the SysTick timer and executes a blocking `while` loop, it keeps the CPU busy. In a future update, this should be replaced with `osDelay()` or `vTaskDelay()` so the RTOS can put the task to sleep and yield CPU time to other tasks more efficiently. The `taskYIELD()` function is currently used as a workaround to force a context switch.
* **printf Routing:** To view the terminal output, the `_write` function was overridden in the main file to redirect standard C library `printf` calls to `huart2`.