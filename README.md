# Brightness Control with Pulse Width Modulation

## Interrupt

#### The rotary encoder has two outputs, A and B. We choose A as the clock and check the state of B. An Interrupt is triggered when A goes to the rising edge. Then we monitor the state of the B to detect the direction. Once we identify the direction we can increase and decrease the brightness using rotary turning and PWM.

#### Clockwise -> B is low when A is on the rising edge.

#### Counter-clockwise -> B is high when A is on the rising edge.

    void rotary_rotation_isr(uint gpio, uint32_t event_mask) {
        bool rotary_B_state = gpio_get(ROTARY_B);
        rotary_event_t event; // object of the struct. Add data to queue

        if(rotary_B_state) {
            event.direction = rotate_counterclockwise; //accessing struct element and passing to enum
        }else {
            event.direction = rotate_clockwise;
        }
        queue_try_add(&rotary_queue,&event);
    }


