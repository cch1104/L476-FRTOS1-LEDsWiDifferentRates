# L476-FRTOS1-LEDsWiDifferentRates
# STM32 CMSIS-RTOS2 Multi-Task LED Blinking Demo

## Overview

This project demonstrates the use of **CMSIS-RTOS2 (FreeRTOS)** on an STM32 microcontroller by creating three independent tasks that blink LEDs at different rates.

The example illustrates:

* RTOS task creation
* Task scheduling
* Concurrent execution of multiple threads
* GPIO control from separate tasks
* Usage of `osDelay()` for task timing

---

## Hardware Configuration

| LED        | GPIO Pin | Blink Rate |
| ---------- | -------- | ---------- |
| Slow LED   | PC0      | 1000 ms    |
| Medium LED | PC1      | 500 ms     |
| Fast LED   | PC2      | 250 ms     |

### Pin Definitions

```c
#define LEDSLOW_Pin      GPIO_PIN_0
#define LEDSLOW_GPIO_Port GPIOC

#define LEDMEDIUM_Pin    GPIO_PIN_1
#define LEDMEDIUM_GPIO_Port GPIOC

#define LEDFAST_Pin      GPIO_PIN_2
#define LEDFAST_GPIO_Port GPIOC
```

---

## RTOS Task Configuration

Three threads are created during system initialization.

### Task 1 – Slow LED

```c
LEDSTaskHandle = osThreadNew(
    StartLEDSTask,
    NULL,
    &LEDSTask_attributes
);
```

Priority:

```c
osPriorityNormal
```

Function:

```c
void StartLEDSTask(void *argument)
{
    for(;;)
    {
        HAL_GPIO_TogglePin(GPIOC, LEDSLOW_Pin);
        osDelay(1000);
    }
}
```

Behavior:

* Toggles PC0 every 1000 ms

---

### Task 2 – Medium LED

```c
LEDMTaskHandle = osThreadNew(
    StartLEDMTask,
    NULL,
    &LEDMTask_attributes
);
```

Priority:

```c
osPriorityBelowNormal
```

Function:

```c
void StartLEDMTask(void *argument)
{
    for(;;)
    {
        HAL_GPIO_TogglePin(GPIOC, LEDMEDIUM_Pin);
        osDelay(500);
    }
}
```

Behavior:

* Toggles PC1 every 500 ms

---

### Task 3 – Fast LED

```c
LEDFTaskHandle = osThreadNew(
    StartLEDFTask,
    NULL,
    &LEDFTask_attributes
);
```

Priority:

```c
osPriorityBelowNormal
```

Function:

```c
void StartLEDFTask(void *argument)
{
    for(;;)
    {
        HAL_GPIO_TogglePin(GPIOC, LEDFAST_Pin);
        osDelay(250);
    }
}
```

Behavior:

* Toggles PC2 every 250 ms

---

## Scheduler Startup Sequence

```c
osKernelInitialize();

LEDSTaskHandle = osThreadNew(...);
LEDMTaskHandle = osThreadNew(...);
LEDFTaskHandle = osThreadNew(...);

osKernelStart();
```

Execution flow:

1. Initialize HAL
2. Configure system clock
3. Initialize GPIO
4. Initialize RTOS kernel
5. Create tasks
6. Start scheduler
7. Tasks execute concurrently

---

## GPIO Initialization

```c
__HAL_RCC_GPIOC_CLK_ENABLE();

GPIO_InitStruct.Pin =
    GPIO_PIN_0 |
    GPIO_PIN_1 |
    GPIO_PIN_2;

GPIO_InitStruct.Mode = GPIO_MODE_OUTPUT_PP;
GPIO_InitStruct.Pull = GPIO_NOPULL;
GPIO_InitStruct.Speed = GPIO_SPEED_FREQ_LOW;

HAL_GPIO_Init(GPIOC, &GPIO_InitStruct);
```

All LEDs are configured as push-pull outputs.

---

## Task Timing Diagram

```text
Time(ms)

0      250    500    750   1000

Fast LED
Toggle Toggle Toggle Toggle Toggle ...

Medium LED
Toggle       Toggle       Toggle ...

Slow LED
Toggle               Toggle ...
```

---

## Expected Result

After programming the board:

* PC2 LED blinks fastest (250 ms)
* PC1 LED blinks at medium speed (500 ms)
* PC0 LED blinks slowest (1000 ms)

All three LEDs operate simultaneously because each LED is controlled by its own RTOS thread.

---

## RTOS Concepts Demonstrated

### Multi-Tasking

Three tasks run independently:

```text
Task 1 -> Slow LED
Task 2 -> Medium LED
Task 3 -> Fast LED
```

### Context Switching

When a task calls:

```c
osDelay(...)
```

the scheduler places the task into the Blocked state and allows another ready task to run.

### Cooperative Resource Usage

Since each task controls a different GPIO pin, no mutex or semaphore is required.

---

## Build Environment

* STM32CubeIDE
* STM32 HAL Driver
* CMSIS-RTOS2 API
* FreeRTOS Kernel

---

## Learning Objectives

After completing this project, students should understand:

* How to create RTOS threads
* How CMSIS-RTOS2 schedules tasks
* The effect of task priorities
* How `osDelay()` affects scheduling
* Concurrent execution in embedded systems
* GPIO control from multiple tasks

---

## Example Output

Observed on hardware:

```text
PC0 -> Blink every 1000 ms
PC1 -> Blink every 500 ms
PC2 -> Blink every 250 ms
```

https://youtube.com/shorts/hrzerZ0qfn0?feature=share
