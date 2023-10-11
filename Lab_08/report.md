## Objectives
Program your microontroller to transmit:<br>
"F0" if SW1 is pressed<br>
"AA" if SW2 is pressed <br>
Over UART with baud rate 9600 and odd parity. <br>
Your program should also listen for incoming data on the UART with the same baud and parity config; if "AA" is received LED should be GREEN; if "F0" is recieved, the LED should be BLUE and if any error is detected LED should be RED. Test this by communicating with your neighboring group.<br>
## C Code
#include <stdint.h>
#include <stdbool.h>
#include "tm4c123gh6pm.h"

#define MASK_BITS   0x11        //  mask bits for user switch 1 and 2
void Config()
{
    SYSCTL_RCGC2_R |= 0x00000020;       // enable clock to GPIOF
        GPIO_PORTF_LOCK_R = 0x4C4F434B;     // unlock commit register
        GPIO_PORTF_CR_R = 0x01;             // make PORTF0 configurable
        GPIO_PORTF_DEN_R = 0x1F;            //set PORTF pins 4 pin
        GPIO_PORTF_DIR_R = 0x0E;            // set PORTF4 pin as input user switch pin
        GPIO_PORTF_PUR_R = 0x11;            // PORTF4 is pulled up

        GPIO_PORTF_IM_R &= ~MASK_BITS;      // mask interrupts for sw1 and sw2
        GPIO_PORTF_IS_R &= ~MASK_BITS;      // edge sensitive interrupts
        GPIO_PORTF_IEV_R  &= ~MASK_BITS;    // falling edge triggers the interrupt
        GPIO_PORTF_IBE_R &= ~MASK_BITS;     // Interrupt is controlled by GPIOIEV
        //NVIC_PRI7_R = (NVIC_PRI7_R & 0xFF1FFFFF) | (2 << 21);  // 2 priority of interrupt 30(port F)
        NVIC_EN0_R |= (1 << 30);            // enable interrupt on port F
        GPIO_PORTF_ICR_R = MASK_BITS;       // clears RIS and MIS for edge sensitive interrupt
        GPIO_PORTF_IM_R |= MASK_BITS;       // unmask interrupts for sw1 and sw2

        SYSCTL_RCGCUART_R |= 0x80;
        SYSCTL_RCGC2_R |= 0x10;
        GPIO_PORTE_DEN_R |= 0x03;
        GPIO_PORTE_DIR_R |= 0x02;
}
void UART()
{
        GPIO_PORTE_AFSEL_R |= 0x03;
        GPIO_PORTE_PCTL_R |= 0x000011;
        UART7_CTL_R &= 0xFFFFFFFE;
        UART7_IBRD_R = 104;
        UART7_FBRD_R = 11;
        UART7_LCRH_R |= 0x62;
        UART7_CC_R &= 0xFFFFFFF0;
        UART7_CTL_R |= ((1<<0)|(1<<8)|(1<<9));

        UART7_IM_R &= 0x00;
        UART7_ICR_R &= 0x00;
        UART7_IM_R |=(1<<4);
        NVIC_EN1_R |= (1<<31);
}
void UART_Handler(void)
{
    UART7_IM_R &= 0x00;
    if(UART7_FR_R & (1<<6))
    {
        if(UART7_DR_R == 0xF0)
        {
            GPIO_PORTF_DATA_R |= 0x04;
        }
        else if(UART7_DR_R == 0xAA)
        {
            GPIO_PORTF_DATA_R |= 0x08;
        }
        else if(UART7_RSR_R & 0xF)
        {
            GPIO_PORTF_DATA_R |= 0x02;
        }
    }

    int j;
    for(j=0;j<800000;j++)
    {

    }
    GPIO_PORTF_DATA_R &= 0x00;
    UART7_ECR_R &= ~(0xF);
    UART7_ICR_R &= 0x00;
    UART7_IM_R |= (1<<4);
}
void GPIOF_Handler(void)
{
    GPIO_PORTF_IM_R &= ~MASK_BITS;
    int i;
    if(GPIO_PORTF_RIS_R & 0x01)
     {
         for(i=0;i<16000;i++)
         {

         }
         if(~(GPIO_PORTF_DATA_R)&0x01)
         {
             UART7_DR_R = 0xAA;
         }
     }

     if(GPIO_PORTF_RIS_R & 0x10)
     {
         for(i=0;i<16000;i++)
         {

         }
         if(~(GPIO_PORTF_DATA_R)&0x10)
         {
             UART7_DR_R = 0xF0;
         }
     }
     GPIO_PORTF_ICR_R = MASK_BITS;
     GPIO_PORTF_IM_R |= MASK_BITS;
}

int main(void)
{
    Config();
    UART();

    while(1)
    {

    }
}

## Observation
## Result
![1](https://github.com/EE23MT024/ESLab2023_ee23mtG03/blob/3dd42cd3d38fb2d859f3eab3f668bed7cae54b8f/Lab_08/1.jpg)
![2](https://github.com/EE23MT024/ESLab2023_ee23mtG03/blob/b51268efcbb4d9c1df78104fa9049df74104e0bd/Lab_08/2.jpg)
