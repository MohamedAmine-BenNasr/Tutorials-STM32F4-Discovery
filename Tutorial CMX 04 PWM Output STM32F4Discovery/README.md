# Tutorial CMX 04 PWM Output STM32F4Discovery

### PWM Output generation on STM32 Microcontrollers using HAL
In this tutorial, I will show you, how to implement PWM outputs on STM32F4xx devices. This is for a lot of people pretty hard work, but believe me, it’s quite quick and easy. I will go step by step on how to make a PWM output on specific timer.

I used the following equipment:

- STM32F4 Discovery Board.
- Keil uVision 5 with the necessary packages for STM32F4 Discovery boards installed.
- STLink USB Driver.
- STM 32 CubeMX.

### STM32CubeMX 

Generating the config. files from STM32CubeMX.  

1. Open STM32CubeMX and open a new project. 
2. Select the STM32F4 Discovery from the Borards tab.
3. Set and configure the RCC (HSE) to Crystal/Ceramic Resonator.
4. Set and configure TIM4 as PWM Generation CH1 (correspond to PD12 PIN).

![1](https://user-images.githubusercontent.com/32094503/34651893-36c09bf4-f3d7-11e7-87fb-3ea2d6e8723e.PNG)

6. Click the configuration tab and click on the TIM4 button 
7. The TIM4 is configured in PWM 1 mode and the pulsation is 0 (the upper code is dragged into the main code), the fast mode is deactivated and the polarity is set high.

![3](https://user-images.githubusercontent.com/32094503/34652043-4ce940a0-f3d9-11e7-9c94-bb4be8bcdb1c.PNG)
