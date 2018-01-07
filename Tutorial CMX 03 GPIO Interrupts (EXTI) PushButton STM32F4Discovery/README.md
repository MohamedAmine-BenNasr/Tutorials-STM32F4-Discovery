#          Tutorial CMX 03 GPIO External Interrupts (EXTI) PushButton STM32F4Discovery


### GPIO External Interrupts (EXTI) on STM32 Microcontrollers using HAL

The STM32 microcontroller family offers multiple GPIO interrupt pins. The STM32CubeMX Software 
comes in handy when configuring the parameters of these pins. However, the actual usage of  

This tutorial uses the following equipment: 


- STM32F4 Discovery Board.
- Keil uVision 5 with the necessary packages for STM32F4 Discovery boards installed.
- STLink USB Driver.
- STM 32 CubeMX.

### STM32CubeMX 

Generating the config. files from STM32CubeMX.  

1. Open STM32CubeMX and open a new project. 
2. Select the STM32F4 Discovery from the Borards tab.
3. Set and configure the RCC (HSE) to Crystal/Ceramic Resonator.
4. Set any GPIO to Output (I used PD12 and PD13).
5. Set PA0 as GPIO_EXTI0 (this is the blue user button on the STM32F4 Discovery). 



![1](https://user-images.githubusercontent.com/32094503/32407023-7947c880-c182-11e7-825f-5300c28d2ebb.PNG)

6. Click the configuration tab and click on the GPIO button 
7. Here you can set the interrupt parameters for EXTI0
I set the GPIO mode to detect falling edges (since the user button has a pull-up resistor) 
Hit Apply and Ok to save the changes. 

![3](https://user-images.githubusercontent.com/32094503/32407034-b18e54e8-c182-11e7-95e2-bd2d8b02d605.PNG)

8. Next, click the NVIC button 
9. Enable EXTI lines 0 (since we use EXTI0) 
10. The priority is 0, this is because we are not using FreeRTOS 

![2](https://user-images.githubusercontent.com/32094503/32407033-ac8630b0-c182-11e7-8f16-9fd1a3ae7b73.PNG)

13. Go to Project > Generate code. 
14. Enter a project name and select MDK-ARM V5. 
15. Generate the code and open the project in Keil uVision. 

The code generator should output the desired code as usual. 
Note, that the EXTI initialisation takes part within the GPIO init function call. 

``` C++
static void MX_GPIO_Init(void)
{

  GPIO_InitTypeDef GPIO_InitStruct;

  /* GPIO Ports Clock Enable */
  __HAL_RCC_GPIOH_CLK_ENABLE();
  __HAL_RCC_GPIOA_CLK_ENABLE();
  __HAL_RCC_GPIOD_CLK_ENABLE();

  /*Configure GPIO pin Output Level */
  HAL_GPIO_WritePin(GPIOD, GPIO_PIN_12|GPIO_PIN_13, GPIO_PIN_RESET);

  /*Configure GPIO pin : PA0 */
  GPIO_InitStruct.Pin = GPIO_PIN_0;
  GPIO_InitStruct.Mode = GPIO_MODE_IT_FALLING;
  GPIO_InitStruct.Pull = GPIO_NOPULL;
  HAL_GPIO_Init(GPIOA, &GPIO_InitStruct);

  /*Configure GPIO pins : PD12 PD13 */
  GPIO_InitStruct.Pin = GPIO_PIN_12|GPIO_PIN_13;
  GPIO_InitStruct.Mode = GPIO_MODE_OUTPUT_PP;
  GPIO_InitStruct.Pull = GPIO_NOPULL;
  GPIO_InitStruct.Speed = GPIO_SPEED_FREQ_LOW;
  HAL_GPIO_Init(GPIOD, &GPIO_InitStruct);

  /* EXTI interrupt init*/
  HAL_NVIC_SetPriority(EXTI0_IRQn, 0, 0);
  HAL_NVIC_EnableIRQ(EXTI0_IRQn);

}

```

### Do something on an interrupt event

Open the file stm32f0xx_it.c 
That is where the magic happens. Somewhere on line 99 should be a function called EXTI0_IRQHandler. 
Each time an EXTI interrupt is triggered, this function gets called. The statement between the two user 
code segments simply clears the interrupt flag of the EXTI channels. 
Now to execute something on an EXTI interrupt event, you could simply add your code between the 
user code blocks. This code is then executed with every EXTI event on any enabled EXTI channel. 

```C++
void EXTI0_IRQHandler(void)
{
	for(int i = 0 ; i < 1000000 ; i ++); // stabilization of the input signal
	
	if(__HAL_GPIO_EXTI_GET_FLAG(GPIO_PIN_0))	
     HAL_GPIO_TogglePin(GPIOD,GPIO_PIN_12);         

  HAL_GPIO_EXTI_IRQHandler(GPIO_PIN_0);
	
  /* USER CODE BEGIN EXTI0_IRQn 1 */

  /* USER CODE END EXTI0_IRQn 1 */
}

```

Thus an entire GPIO port or port pins from different GPIO ports can be configured as external interrupts. Interrupts in all pins is an unnecessary stuff. We can also decide when to sense an interrupt – on rising/falling or both edges. All these settings are separately stored, allowing high flexibility.

![exti](https://user-images.githubusercontent.com/32094503/32409826-95dd98b8-c1b3-11e7-85a9-8d588149a26f.png)

The EXTI lines 0 – 4 are individually and directly connected to the NVIC interface while the remaining higher order EXTI lines are grouped into two – one ranging from EXTI 5 to 9 and the other from 10 – 15. The upper EXTI pins are thus not individually and directly connected to the NVIC. Owing to this EXTI 0 – 4 have separate unique interrupt vector addresses while the rest have two separate interrupt vector addresses.

![exti_to-nvic](https://user-images.githubusercontent.com/32094503/32409849-7421b0dc-c1b4-11e7-948e-a04984f39229.png)


### Filter EXTI interrupt events

But you might want to do different things, when different interrupt pins are triggered. 
You can filter the interrupt event with a simple if-statement. 
It is important to place this if statement before clearing the interrupt flags. 

```C++
void EXTI0_IRQHandler(void)
{
	for(int i = 0 ; i < 1000000 ; i ++); // stabilization of the input signal
	
	if(__HAL_GPIO_EXTI_GET_FLAG(GPIO_PIN_0))	
     HAL_GPIO_TogglePin(GPIOD,GPIO_PIN_12);         

  HAL_GPIO_EXTI_IRQHandler(GPIO_PIN_0);
	
  /* USER CODE BEGIN EXTI0_IRQn 1 */

  /* USER CODE END EXTI0_IRQn 1 */
}

```

Stabilization of the input signal.
```C++
for(int i = 0 ; i < 1000000 ; i ++);
```

The instructions within the if-statement is only executed when an EXTI interrupt on EXTI channel 0 occurs. 
```C++
if(__HAL_GPIO_EXTI_GET_FLAG(GPIO_PIN_0))	
     HAL_GPIO_TogglePin(GPIOD,GPIO_PIN_12);  
```
Keep the following makro in mind when filtering EXTI interrups: 
It takes the EXTI channel as an argument, where x is any numer from 0 to 15. 
It returns true, when the interrupt flag for the given channel is set. 
```C++
__HAL_GPIO_EXTI_GET_FLAG(GPIO_PIN_x)
 
```


### Document Created by Mohamed Amine Ben Nasr

This tutorial is very basic and might not show the best way to use the STM32 environment. 
It still might help you get into the whole HAL philosophy of STM if you are coming from another 
platform. This document is free of copyright.



