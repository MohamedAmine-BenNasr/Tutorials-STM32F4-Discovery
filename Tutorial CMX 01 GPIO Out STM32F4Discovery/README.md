#          Tutorial CMX 01 GPIO Out STM32F4Discovery



You can use the STM32CubeMX tool to create the necessary config. files to enable the GPIO Pins. 
In this tutorial I’m going to explain how you can modify the generated GPIO configs and add additional GPIOs. 
This tutorial uses the following equipment: 


- STM32F4 Discovery Board.
- Keil uVision 5with the necessary packages for STM32F4 Discovery boards installed.
- STLink USB Driver.
- STM 32 CubeMX.

### STM32CubeMX 

Generating the config . files from STM 32 CubeMX. 
1. Open STM32CubeMX and open a new project.
2. Select the STM32F4 Discovery from the Borards tab.
3. Set and configure the RCC (HSE) to Crystal/Cerami c Resonator.
4. Configure the SYSCLK at 168 MHz and peripheral clocks APB1 at 21 MHz.
5. Set any GPIO to Output (I used P D1 2, PD13, PD14 and PD15).
6. Go to Project > Generate code.
7. Enter a project name and select MDK-ARM V5.
8. Generate the code and open the project in Keil uVision.

![1](https://user-images.githubusercontent.com/32094503/32344214-5ee429c0-c006-11e7-9e41-81a7959d3e71.PNG)

![2](https://user-images.githubusercontent.com/32094503/32344267-8fc4462e-c006-11e7-9424-34c0d5781aa7.PNG)
