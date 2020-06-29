# IKEA TRÅDFRI ICC-A-1 Module.

### Loading Silicon Labs EmberZNet Zigbee coordinator firmware on IKEA TRÅDFRI ICC-A-1 Module.

[s-hadinger](https://github.com/s-hadinger) have the EZSP upp and running on [Sonoff Zigbee Bridge](https://github.com/arendst/Tasmota/issues/8583) with help of [mtx512](https://github.com/mtx512)
our maker of bootoader and coordinator firmware.
If all going well sud it woring with [HA and OpenHab](https://sprut.ai/client/article/2583).  
[MPM1107](https://github.com/MPM1107) like to implenting it for Z2M. If hi and the other in Z2M team making it working then it being a graet thing in the HA comunity.


### ICC-1 / ICC-A-1 Module:

* Microcontroller: EFR32MG1P132F256GM32
* 2 Mbit SPI NOR Flash: IS25LQ020B
* Crystal: 38.4 MHz

[FCC ICC-1 RF module User Manual](https://fccid.io/FHO-ICC-1/User-Manual/User-Manual-3055710)

### Module Pinout:

From [DIY Stuff.nl](https://diystuff.nl/tradfri/tradfri-zigbee-light-link-module):

| Pad | EFR32 pins | Description |
|------------|-----------|-------|
| 01         | GND       | |
| 02         | PB15      | |
| 03         | PB14      | |
| 04         | PC10      | |
| 05         | PC11      | Present at 2 locations on PCB |
| 06         | PF0       | SWCLK |
| 07         | PF1       | SWDIO |
| 08         | PF2       | SWO   |
| 09         | PF3 / GND | ICC-1 / ICC-A-1 |
| 10         | RESETn    | | 
| 11         | VDD       | PAVDD, IOVDD, VREGVDD, VREGSW, VREGVSS | 
| 12         | GND       | | 
| 13         | PB13      | | 
| 14         | PB12      | | 
| 15         | PA1       | | 
| 16         | PA0       | Normaly LL buttom | 
| 17         | PC11      | Present at 2 locations on PCB | 


### Internal :

| EFR32 pins | Description |
|------------|-----------|
| PB11 | Flash CS |
| PD15 | Flash MOSI |
| PD14 | Flash MISO |
| PD13 | Flash SCLK |
| PF3 | Flash enable (on ICC-A-1 / Pin 09 on ICC-1) |

### Peripheral mapping
From /RIOT-OS

| Peripheral | Number  | Hardware        | Pins                              | Comments                                            |
|------------|---------|-----------------|-----------------------------------|-----------------------------------------------------|
| ADC        | 0       | ADC0            | CHAN0: internal temperature       | Ports are fixed, 14/16-bit resolution not supported |
| HWCRYPTO   | &mdash; | &mdash;         |                                   | AES128/AES256, SHA1, SHA256                         |
| RTT        | &mdash; | RTCC            |                                   | 1 Hz interval. Either RTT or RTC (see below)        |
| RTC        | &mdash; | RTCC            |                                   | 1 Hz interval. Either RTC or RTT (see below)        |
| SPI        | 0       | USART1          | MOSI: PD15, MISO: PD14, CLK: PD13 |                                                     |
| Timer      | 0       | TIMER0 + TIMER1 |                                   | TIMER0 is used as prescaler (must be adjecent)      |
| UART       | 0       | USART0          | RX: PB15, TX: PB14                | Default STDIO output                                |
| LEUART     | 0       | LEUART0         | RX: PB15, TX: PB14                | Alternate to using UART                             |


### Known module pins

| Pad | EFR32 pins | Standard use | E1524 | E1743 |  ICTC-G-1 | E1603 |
|------------|-----------|-------|-------|-------|-------|-------|
| 01         | GND       | GND | GND |GND | GND |  |
| 02         | PB15      |  | Left |  |  |  |
| 03         | PB14      |  | Down |  |  |  |
| 04         | PC10      |  | On/Off | On/Up |  |  |
| 05         | PC11      |  | Right | LED |  |  |
| 06         | PF0       | SWD Debug |  |  | TP2 |  |
| 07         | PF1       | SWD Debug |  |  | TP1 |  |
| 08         | PF2       | SWD Debug |  |  | TP3 |  |
| 09         | PF3       |  |  |  | TP4 |  |
| 10         | RESETn    | Reset | Reset | Reset | Reset TP5 |  |
| 11         | VDD       |  3.3V | 3.3V | 3.3V | 3.3V | 3.3V |
| 12         | GND       | GND | GND | GND | GND | GND |
| 13         | PB13      | PMW 1 | LED |  |  |  |
| 14         | PB12      | PMW 2 |  | Off/Down |  | On/Off |
| 15         | PA1       | PMW 3 | Up |  | LLB TP6 | LED |
| 16         | PA0       | PMW 4 | LLB | LLB | LED TP7 | LLB |
| 17         | PC11      |  |  |  |  |


### EZSP use :

| Pad | EFR32 pins | Description |
|------------|-----------|-------|
| 04         | PC10      | TX |
| 05         | PC11      | RX |
| 06         | PF0       | SWCLK |
| 07         | PF1       | SWDIO |
| 08         | PF2       | SWO   |
| 11         | VDD       | 3.3V | 
| 12         | GND       | GND |
| 16         | PA0       | Force bootloader (LL) buttom | 


### Flashing:

[Flashing ICC-1](https://github.com/MattWestb/IKEA-TRADFRI-ICC-A-1-Modul/tree/master/Flashing-MG).  


### Firmware:

Dumped one new (never pared) LED1837R5 with BlackMagic-espidf.  

Bootloader: First stage bootloader looks OK.  
Seccund stage bootloader updated with mtx512 and can force bootloader boot and monitoring.

[mtx512](https://github.com/mtx512) have compilles a inital [firmware set](https://github.com/mtx512/efr32/tree/master/icc-a-1).  

Then having the new bootloader in place it its only booting in bootloader mode thrue pressing the LigthingLink button (if using a controll device)(PA0 low) and poweron and then upload the EZSP from the bootloader over serial. 


#### Zigbee EmberZNet: ver 6.7.6.0: 

[Release Notes](https://www.silabs.com/documents/public/release-notes/emberznet-release-notes-6.7.6.0.pdf) 2.2 API Changes: EZSP Protocol Version 8 and Secure EZSP Protocol Version 2. (Changed in release 6.7.0.0). Both EZSP and Secure EZSP have adopted a new frame format with the following changes: (1) the fields of "Frame Control" and "Frame ID" are now two bytes; (2) no longer use "Legacy Frame ID"; (3) consume two bits of "Frame Control" to indicate the frame format version
which is version 1 now. 

Disabling fallback to older prottokoll making its a braking thing for zigpy / bellows thats making the 6.7.x.x and newer relase not working with HA and OpenHab.  

Zigpy have starting looking getting v8 support and also some Zigbee2Mqtt fans are trying implenting EZSP in Z2M. 


#### Zigbee EmberZNet: ver 6.6.4.0:

[Release Notes](https://www.silabs.com/documents/public/release-notes/emberznet-release-notes-6.6.4.0.pdf) Laterst version with EZSP Protocol Version 7 and Secure EZSP Protocol Version 1. 

Its backword compatible with the old EZSP protocol (v 4/5) used in zigpy / bellows and making it working in with HA and OpenHab.

