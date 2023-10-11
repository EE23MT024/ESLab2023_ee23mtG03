## Objectives
Program your microontroller to transmit:<br>
"F0" if SW1 is pressed<br>
"AA" if SW2 is pressed <br>
Over UART with baud rate 9600 and odd parity. <br>
Your program should also listen for incoming data on the UART with the same baud and parity config; if "AA" is received LED should be GREEN; if "F0" is recieved, the LED should be BLUE and if any error is detected LED should be RED. Test this by communicating with your neighboring group.<br>

## Observation
 Baud rate 9600.
 We get integer and fractional part for baud rate speciification to register by  given expression<br>
   > 16000000/(16*9600)=104.1666 <br>
  > 0.167*64+0.5=11
integer part is taken as 104 and fravtion part is taken as 11
UART Function:
This function configures UART communication:
It configures GPIO pins for UART communication.
It sets up the UART7 communication parameters, such as the baud rate, data format, and control settings.
It enables UART7 interrupts and configures the NVIC (Nested Vector Interrupt Controller) for UART7.
## Result
![1](https://github.com/EE23MT024/ESLab2023_ee23mtG03/blob/3dd42cd3d38fb2d859f3eab3f668bed7cae54b8f/Lab_08/1.jpg)
![2](https://github.com/EE23MT024/ESLab2023_ee23mtG03/blob/b51268efcbb4d9c1df78104fa9049df74104e0bd/Lab_08/2.jpg)
