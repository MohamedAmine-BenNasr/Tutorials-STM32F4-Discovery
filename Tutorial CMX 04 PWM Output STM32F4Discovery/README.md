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

5. Click the configuration tab and click on the TIM4 button 
6. The TIM4 is configured in PWM 1 mode and the pulsation is 0 (the upper code is dragged into the main code), the fast mode is deactivated and the polarity is set high.

![3](https://user-images.githubusercontent.com/32094503/34652043-4ce940a0-f3d9-11e7-9c94-bb4be8bcdb1c.PNG)

7. Go to Project > Generate code.
8. Enter a project name and select MDK-ARM V5.
9. Generate the code and open the project in Keil uVision.

The code generator should output the desired code as usual(the TIM4 and GPIO initialization functions).
``` C++
/* TIM4 init function */
static void MX_TIM4_Init(void)
{

  TIM_MasterConfigTypeDef sMasterConfig;
  TIM_OC_InitTypeDef sConfigOC;

  htim4.Instance = TIM4;
  htim4.Init.Prescaler = 0;
  htim4.Init.CounterMode = TIM_COUNTERMODE_UP;
  htim4.Init.Period = 0;
  htim4.Init.ClockDivision = TIM_CLOCKDIVISION_DIV1;
  if (HAL_TIM_PWM_Init(&htim4) != HAL_OK)
  {
    _Error_Handler(__FILE__, __LINE__);
  }

  sMasterConfig.MasterOutputTrigger = TIM_TRGO_RESET;
  sMasterConfig.MasterSlaveMode = TIM_MASTERSLAVEMODE_DISABLE;
  if (HAL_TIMEx_MasterConfigSynchronization(&htim4, &sMasterConfig) != HAL_OK)
  {
    _Error_Handler(__FILE__, __LINE__);
  }

  sConfigOC.OCMode = TIM_OCMODE_PWM1;
  sConfigOC.Pulse = 0;
  sConfigOC.OCPolarity = TIM_OCPOLARITY_HIGH;
  sConfigOC.OCFastMode = TIM_OCFAST_DISABLE;
  if (HAL_TIM_PWM_ConfigChannel(&htim4, &sConfigOC, TIM_CHANNEL_1) != HAL_OK)
  {
    _Error_Handler(__FILE__, __LINE__);
  }

  HAL_TIM_MspPostInit(&htim4);

}

/** Configure pins as 
        * Analog 
        * Input 
        * Output
        * EVENT_OUT
        * EXTI
*/
static void MX_GPIO_Init(void)
{

  /* GPIO Ports Clock Enable */
  __HAL_RCC_GPIOH_CLK_ENABLE();
  __HAL_RCC_GPIOD_CLK_ENABLE();

}
``` 

### principle of using PWM signals
