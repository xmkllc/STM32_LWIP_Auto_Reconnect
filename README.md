## STM32 LWIP Auto Reconnect

### Intro
This is a demo program to show how to deal with LWIP initialization without Ethernet cable.

The demo is a STM32CubeIDE generated project using LWIP without an OS on a STM32F407VGTx
Discovery board. The generated code works out-of-box if the board powers on with Ethernet
cable plugged in. Even the Ethernet cable is disconnected and reconnected again, the
network recovers without problem. 

However, if the board powers on without the Ethernet cable, once the cable is plugged in
the network will never work again.

Searching the internet reveals not much help. I eventually figured out a way to solve the
problem. I created a timer that checks PHY_LINKED_STATUS periodically. On first instance
of Ethernet connection after power on, it will try to bring up the network interface and
start DHCP. The system will work afterwards. The actual code with comments can be found
in stm32f4xx_it.c.

### Hardware
- STM32F407VGTx Discovery
- STM32F4DIS-BB expansion board (PHY: Lan8742A)

### CubeMX Configuration
- Start without default settings
- Clock: enable HSE (PCLK1 42MHz)
- ETH:
   - RMII
   - PHY addr: 0 (default is wrong)
   - Ethernet global interrupt: enabled
   - Auto negotiation: enabled
- LWIP:
   - LWIP_NETIF_LINK_CALLBACKS: enabled
   - LWIP_PERF: enabled
- TIM3:
   - Prescaler: 42000-1 (PCLK1:42MHz)
   - Counter period: 1000 (gives 1s)
   - TIM3 global interrupt: enabled
- LEDs:

### Code
I include iperf later for testing. For some reason lwiperf.c is not included after code generation.
I have to manually add that file.

### LEDs:
The orange led blinks for TIM3 interrupt. The red is on when PHY_LINKED_STATUS is down.
The blue is on when NETIF_FLAG_LINK_UP is down. Both are on when the board is powered on
without Ethernet cable. Both are off when network is working.
