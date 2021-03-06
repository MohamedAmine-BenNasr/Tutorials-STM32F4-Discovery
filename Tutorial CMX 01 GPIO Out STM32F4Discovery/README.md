#          Tutorial CMX 01 GPIO Out STM32F4Discovery


### GPIO Operations on STM32 Microcontrollers using HAL

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
5. Set any GPIO to Output (I used PD12, PD13, PD14 and PD15).
6. Go to Project > Generate code.
7. Enter a project name and select MDK-ARM V5.
8. Generate the code and open the project in Keil uVision.

![1](https://user-images.githubusercontent.com/32094503/32344214-5ee429c0-c006-11e7-9e41-81a7959d3e71.PNG)

![2](https://user-images.githubusercontent.com/32094503/32344267-8fc4462e-c006-11e7-9424-34c0d5781aa7.PNG)

Now let’s see what the code generator did 

``` C++
static void MX_GPIO_Init(void)
{

  GPIO_InitTypeDef GPIO_InitStruct;

  /* GPIO Ports Clock Enable */
  __HAL_RCC_GPIOH_CLK_ENABLE();
  __HAL_RCC_GPIOD_CLK_ENABLE();

  /*Configure GPIO pin Output Level */
  HAL_GPIO_WritePin(GPIOD, GPIO_PIN_12|GPIO_PIN_13|GPIO_PIN_14|GPIO_PIN_15, GPIO_PIN_RESET);

  /*Configure GPIO pins : PD12 PD13 PD14 PD15 */
  GPIO_InitStruct.Pin = GPIO_PIN_12|GPIO_PIN_13|GPIO_PIN_14|GPIO_PIN_15;
  GPIO_InitStruct.Mode = GPIO_MODE_OUTPUT_PP;
  GPIO_InitStruct.Pull = GPIO_NOPULL;
  GPIO_InitStruct.Speed = GPIO_SPEED_FREQ_LOW;
  HAL_GPIO_Init(GPIOD, &GPIO_InitStruct);

}

```

The concept is simple, init struct is defined, this struct is filled with information. 
This information will be processed by the HAL library at the function call HAL_GPIO_Init(). 

Lines 193 to 196 enable the clock for the GPIO ports. 

The init struct consists of 4 values that can be set. 

#### 1. Pin 

  - The Pin(s) that are about to be initialised 
  - e.g. GPIO_PIN_12 (numbers reach from 0 to 15, or GPIO_PIN_ALL) 

#### 2. Mode 

  - The mode of the selected pins (Input / Output / etc.). 
  - e.g. GPIO_MODE_INPUT. 
  - Possible assignments are the following : 
  
``` Json
      GPIO_MODE_INPUT                                 floating input
      GPIO_MODE_OUTPUT_PP                             output push-pull
      GPIO_MODE_OUTPUT_OD                             output open drain
      GPIO_MODE_AF_PP alternate                       function output push-pull
      GPIO_MODE_AF_OD alternate                       function output open drain
      GPIO_MODE_AF_INPUT                              alternate function input 
```

#### 3. Pull 

- Pull-up or Pull-down resistors for the specified pins. 
- Can be the following: 
``` Json
      GPIO_NOPULL 
      GPIO_PULLUP 
      GPIO_PULLDOWN 
````
#### 4. Speed 

- Specifies the speed for the selected pins 
- Can be the following: 
``` Json
      GPIO_SPEED_LOW 
      GPIO_SPEED_MEDIUM 
      GPIO_SPEED_HIGH 
```
### How to add / remove / change GPIO pins 
Example shows push-pull output declaration of four GPIO port D pins 

It is really not that hard, just fill the init struct with the desired values and call the HAL_GPIO_Init() 
function with the corresponding GPIO port. 

If you need yet another pin with the same specifications and GPIO port as a pin that has already been 
declared, it is even simpler. A bitwise or masking of the Pin argument with the new pin does the job. 

``` C++
  /*Configure GPIO pin : PA0 */
  GPIO_InitStruct.Pin = GPIO_PIN_0;
  GPIO_InitStruct.Mode = GPIO_MODE_INPUT;
  GPIO_InitStruct.Pull = GPIO_NOPULL;
  HAL_GPIO_Init(GPIOA, &GPIO_InitStruct);

  /*Configure GPIO pin : PD12 */
  GPIO_InitStruct.Pin = GPIO_PIN_12;
  GPIO_InitStruct.Mode = GPIO_MODE_OUTPUT_PP;
  GPIO_InitStruct.Pull = GPIO_NOPULL;
  GPIO_InitStruct.Speed = GPIO_SPEED_FREQ_LOW;
  HAL_GPIO_Init(GPIOD, &GPIO_InitStruct);
 ```
 
### Using a GPIO output inside the program 

Changes of the output state of an output pin are written to the GPIOx_ODR register (output data register). This works best with masking. 

Turning on an output pin 
``` C++
/* turn on PD12 */
GPIOD -> ODR |= GPIO_PIN_12; 
```

Turning off an output pin 
``` C++
/* turn off PD12 */
GPIOD -> ODR &= ~GPIO_PIN_12; 
```

Toggle an output pins state 
``` C++
/* toggle PD12 */
GPIOD -> ODR ^= GPIO_PIN_12;
```
An output pin can also be set using the integrated HAL library function 
```C++
/* set PD12 */
HAL_GPIO_WritePin(GPIOD, GPIO_PIN_12, GPIO_PIN_SET);
/* reset PD12 */ 
HAL_GPIO_WritePin(GPIOD, GPIO_PIN_12, GPIO_PIN_RESET); 
```

### Reading a GPIO input inside the program

The current state of an input can be read from its GPIOx_IDR register (input data register)
Again, this works best using bit masking.

Reading an input pin
```C++
/* read PA0 */
if(GPIOA -> IDR & GPIO_PIN_0)
{
/* user code */
}
```
Or with the HAL library function
```C++
/* read PA0 */
if(HAL_GPIO_ReadPin(GPIOA, GPIO_PIN_0))
{
/* user code */
}
```

### Document Created by Mohamed Amine Ben Nasr

This tutorial is very basic and might not show the best way to use the STM32 environment. 
It still might help you get into the whole HAL philosophy of STM if you are coming from another 
platform. This document is free of copyright.

