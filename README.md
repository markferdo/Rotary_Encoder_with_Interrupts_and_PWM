# Brightness Control with Pulse Width Modulation

#### Model: RP2040 
#### Language: C

## Interrupt

#### The rotary encoder has two outputs, A and B. We choose A as the clock and check the state of B. 
#### An Interrupt is triggered when A goes to the rising edge. Then we monitor the state of the B to detect the direction. 
#### Once we identify the direction we can increase and decrease the brightness using rotary turning and PWM.

#### Clockwise -> B is low when A is on the rising edge.

#### Counter-clockwise -> B is high when A is on the rising edge.

#### Here is the Interrupt function

    void rotary_rotation_isr(uint gpio, uint32_t event_mask) {
        bool rotary_B_state = gpio_get(ROTARY_B);
        rotary_event_t event; 

        if(rotary_B_state) {
            event.direction = rotate_counterclockwise;
        }else {
            event.direction = rotate_clockwise;
        }
        queue_try_add(&rotary_queue,&event);
    }

#### In main we can enable the ISR

    gpio_set_irq_enabled_with_callback(ROTARY_A, GPIO_IRQ_EDGE_RISE, true, &rotary_rotation_isr);

## PWM

#### With the help of the duty cycle we can change the brightness 0 - 100 %. 
#### Here is the PWM function

    void assign_pwm(const uint led_pin) {
        const uint slice_num = pwm_gpio_to_slice_num(led_pin);
        const uint channel = pwm_gpio_to_channel(led_pin);
        pwm_set_enabled(led_pin,false);
        pwm_config config = pwm_get_default_config();
        pwm_config_set_clkdiv(&config,125);
        pwm_config_set_wrap(&config, 999);
        pwm_init(slice_num, &config,true);
        pwm_set_chan_level(slice_num, channel, DUTY_CYCLE_LOW);
        gpio_set_function(led_pin, GPIO_FUNC_PWM);
        pwm_set_enabled(led_pin,true);
    }
## Brightness handling with the encoder turning

    if (event.direction == rotate_clockwise) {

        if (duty_cycle >= DUTY_CYCLE_MAX) {
            duty_cycle = DUTY_CYCLE_MAX;
        }
        else {
            duty_cycle += DUTY_CYCLE_STEP;
        }
        led_brightness(duty_cycle);
    } else if (event.direction == rotate_counterclockwise) {
        if(duty_cycle > DUTY_CYCLE_LOW) {
            duty_cycle -= DUTY_CYCLE_STEP;
            if(duty_cycle  <= DUTY_CYCLE_LOW + DUTY_CYCLE_STEP){
                duty_cycle = DUTY_CYCLE_LOW;
            }
        }
        led_brightness(duty_cycle);
    }
