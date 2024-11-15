# Brightness Control with Pulse Width Modulation

## Interrupt

### The rotary encoder has two outputs, A and B. We choose A as the clock and check the state of B when A is on the rising edge. 

### Clockwise -> B is low when A is on the rising edge.

### Counter-clockwise -> B is high when A is on the rising edge.

### An Interrupt is triggered when A goes to the rising edge. Then we monitor the state of the B to detect the direction. Once we identify the direction we can increase and decrease the brightness using rotary turning and PWM.


