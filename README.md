# VSD-FGPA-BOARD-REPO BY ATHRV SHARMA
> I am creating this repository for the fgpa board provided by `VSDIAT`. This repository covers all the steps, tasks and the installation process for the fgpa board.
> This all is based upon the steps mentioned in the datasheet, you can access the datasheet [here.](https://www.vlsisystemdesign.com/wp-content/uploads/2025/01/VSDSquadronFMDatasheet.pdf)

Here is all that is covered in this repository:
- [x] Installation and settings
- [x] Understanding the verilog code, pin placements, and the pcf file
- [x] Programming the board

# Installation and Settings :
So, we start off by installing `Oracle VirtualBox` and then creating a new Operating System. You can download it from [Here](https://www.virtualbox.org/wiki/Downloads)
>[!NOTE]
> We must also install the vsd virtual hard disk file for this.

This is how it should look like

![Image](https://github.com/user-attachments/assets/022d7a69-67a7-42e5-a6b8-92a4d148bafd)






These are the settings that SHOULD be followed:

![Image](https://github.com/user-attachments/assets/bd08553f-8216-44c6-8153-820a3d332124)

 This is how VirtualBox should look now:

 ![Image](https://github.com/user-attachments/assets/055349d1-4461-40f6-a710-7c26d7fe086d)


 # Here is a detailed way on how to program the FGPA Board


[CLICK HERE FOR DETAILED VIDEO](https://github.com/user-attachments/assets/b5a4fa6d-240e-46fb-a59e-a8429fde97f8)
In this video, we start the Oracle VirtualBox and power up the VM, now we start the programming:
- We open the terminal and use the command `cd`
- Next, we open the file - `VSDSquadron_FM` using the command `cd VSDSquadron_FM`.
- Now we open the sub division of this file `blink_led`, using the `cd blink_led` command.
- This file already has all the components to light up the RGB led. So, now we use command `make build` and `sudo make flash` which are the final programs, if executed perfectly, then, the RGB led should light up.
- This is how the expected output is like:

  
 ![Image](https://github.com/user-attachments/assets/7042545a-2f7c-462c-ae57-067b17416e2f)



> MY LED WAS CAPTURED ON THE RED TERMINAL, THE LED SHOULD BE FLASHING RGB.
- This is the code in the terminal:

  ![Image](https://github.com/user-attachments/assets/1e8c8043-26dc-4d33-b3c5-9ade8a0ed746)

 # Understanding Verilog, pin placements and the PCF file:
>[!NOTE]
> You can access the verilog code [here.](https://github.com/thesourcerer8/VSDSquadron_FM/blob/main/led_blue/top.v)

## Let us start:
In the start, we are declaring a module `top`; here are its inputs and output ports:
- There are three wires which control the red, green, and blue LEDs, `led_red`, `led_blue`, and `led_green`.
- The `hw_clk` is the input wire meant for an external hardware clock.
- The `testwire` is intended for debugging or observing an input signal.
- The wire, `wire int_osc` carries the signal from the internal oscillator.
- `reg [27:0] frequency_counter_i` is a 28-bit register that acts as a counter. It increments on every positive edge of the `int_osc` signal.
-` assign testwire = frequency_counter_i[5]` is the line that assigns the 6th bit (bit 5) of the `frequency_counter_i` counter to the `testwire` output. This is a simple way to observe a toggling signal that is a divided version of the internal oscillator. `always @(posedge int_osc)` means that the code inside the block will execute whenever the `int_osc` signal transitions from low to high (positive edge).` frequency_counter_i <= frequency_counter_i + 1'b1` is the line which increments the counter by 1 on each positive edge of `int_osc`.
- `SB_HFOSC` this is an instantiation of a specific hardware primitive, likely from a Lattice Semiconductor FPGA library. It represents an internal high-frequency oscillator.
- `#(.CLKHF_DIV ("0b10"))` this sets the `CLKHF_DIV` parameter to "0b10" (binary 2). This parameter controls the division factor of the oscillator's output frequency.
- `u_SB_HFOSC` this is the instance name.
- `.CLKHFPU(1'b1), .CLKHFEN(1'b1)`, `.CLKHF(int_osc)`: These are port connections:
  - `CLKHFPU(1'b1)` this enables the power-up of the oscillator.
  - `CLKHFEN(1'b1)` this enables the oscillator.
  - `CLKHF(int_osc)` this connects the oscillator's output to the `int_osc` wire.
- `SB_RGBA_DRV` this instantiates another hardware primitive, likely also from a Lattice FPGA library, which is an RGB LED driver.
- Port Connections:
  - `RGBLEDEN(1'b1)` this enables the RGB LED driver.
  - `RGB0PWM (1'b0)`, `RGB1PWM (1'b0)`, `RGB2PWM (1'b1)` these control the PWM (pulse-width modulation) signals for the red, green, and blue LEDs. In this case, only the blue LED is turned on `(1'b1)`, while red and green are off `(1'b0)`.
  - `CURREN (1'b1)` this enables the current control.
  - `RGB0 (led_red)`, `RGB1 (led_green)`, `RGB2 (led_blue)`; These connect the driver's outputs to the module's output wires that control the actual LED pins.
- `defparam RGB_DRIVER.RGB0_CURRENT = "0b000001"`, etc, these lines set the current limiting parameters for each LED color.

  Summary:

## This code configures an FPGA to:

 1. Generate an internal clock signal using an internal oscillator.
 2. Use this clock to drive a 28-bit counter.
 3. Output a toggling signal based on the counter for testing.
 4. Turn on the blue LED of an RGB LED using a dedicated driver primitive.
 5. Set the current limiting resistors for the rgb led.
 6. In essence, it's a basic setup that demonstrates how to use internal oscillators and LED drivers in an FPGA design.

## Understanding the PCF

### Here is the PCF code :
set_io  led_red	39
set_io  led_blue 40
set_io  led_green 41
set_io  hw_clk 20
set_io  testwire 17

### Explanation :
These lines are instructions for a chip (like an FPGA) that tell it how its internal signals are connected to its physical pins. Think of it like a label on a wire:

`set_io led_red 39`: This line means "The signal called 'led_red' (which controls a red LED) is connected to pin number 39." So, when the chip wants to turn on the red LED, it sends a signal to pin 39.

`set_io led_blue 40`: This means "The signal called 'led_blue' (which controls a blue LED) is connected to pin number 40."
`set_io led_green 41`: This means "The signal called 'led_green' (which controls a green LED) is connected to pin number 41."
`set_io hw_clk 20`: This means "The hardware clock signal (which provides timing) is connected to pin number 20." The clock is the heartbeat of the chip, and this line tells it where to find that heartbeat.
`set_io testwire 17`: This means "A general-purpose signal called 'testwire' (used for testing) is connected to pin number 17."
Essentially, these lines are creating a map that the chip uses to know where to send and receive signals. Without this map, the chip wouldn't know which pins to use, and your circuit wouldn't work. It's like telling a robot which wires to connect to which parts.


# THIS WAS ALL FOR TASK 1, THANK YOU.


# TASK 2 

## UART AND LOOPBACK

Now, in the step 1, we have to access and study the given code from [VSDSquadron_FM, UART](https://github.com/thesourcerer8/VSDSquadron_FM/blob/main/uart_loopback/uart_trx.v) repo. The code is as follows: 


`// 8N1 UART Module, transmit only`

`module uart_tx_8n1 (
    clk,        // input clock
    txbyte,     // outgoing byte
    senddata,   // trigger tx
    txdone,     // outgoing byte sent
    tx,         // tx wire
    );`

    /  Inputs  /
    input clk;
    input[7:0] txbyte;
    input senddata;

    /  Outputs  /
    output txdone;
    output tx;

    /  Parameters  /
    parameter STATE_IDLE=8'd0;
    parameter STATE_STARTTX=8'd1;
    parameter STATE_TXING=8'd2;
    parameter STATE_TXDONE=8'd3;

    /  State variables  /
    reg[7:0] state=8'b0;
    reg[7:0] buf_tx=8'b0;
    reg[7:0] bits_sent=8'b0;
    reg txbit=1'b1;
    reg txdone=1'b0;

    /  Wiring  /
    assign tx=txbit;

    /  always  /
    always @ (posedge clk) begin
        // start sending?
        if (senddata == 1 && state == STATE_IDLE) begin
            state <= STATE_STARTTX;
            buf_tx <= txbyte;
            txdone <= 1'b0;
        end else if (state == STATE_IDLE) begin
            // idle at high
            txbit <= 1'b1;
            txdone <= 1'b0;
        end

        // send start bit (low)
        if (state == STATE_STARTTX) begin
            txbit <= 1'b0;
            state <= STATE_TXING;
        end
        // clock data out
        if (state == STATE_TXING && bits_sent < 8'd8) begin
            txbit <= buf_tx[0];
            buf_tx <= buf_tx>>1;
            bits_sent = bits_sent + 1;
        end else if (state == STATE_TXING) begin
            // send stop bit (high)
            txbit <= 1'b1;
            bits_sent <= 8'b0;
            state <= STATE_TXDONE;
        end

        // tx done
        if (state == STATE_TXDONE) begin
            txdone <= 1'b1;
            state <= STATE_IDLE;
        end

    end
    
`endmodule`

### Let us break it down step by step:
- At the top we have `module`. This declares the modules and the ports. 

 `module uart_tx_8n1(...)` defines a module, namley the `uart_tx_8n1`. It does a very important job,
 - Serial Data Transmission: It takes an 8-bit parallel data byte (txbyte) and converts it into a serial stream of bits on the tx output.
 - UART Protocol Implementation: It implements the 8N1 UART protocol, which defines the format of the serial data:
   - Start bit (low)
   - 8 data bits (least significant bit first)
   - Stop bit (high)
 - Synchronization: It uses a clock signal (`clk`) to synchronize the transmission process.
 - Transmission Control: It uses the `senddata` input to trigger the transmission and the `txdone` output to indicate when the transmission is complete.
 - State Machine: It uses a state machine to manage the different stages of the transmission process (idle, start bit, data bits, stop bit).

- `clk` : This is a clock input which synchronizes the module's (we defined earlier) operations. 

- `txbyte` : This is the  8 bit  data that is to be transmitted.

- `senndata` : This is a signal that is used to trigger the transmission of `txbyte`

- `txdone` : This signifies and indicates that the transfer by `txbyte` is done.

- `tx` : This is the output signal which carries the serial data.



 ### State Variables and Parameters : 

 We start with the most obvious question,
 _What ARE parameters???_
 - Parimeters define the different states of finite state machines (FSM), which is used to control the transmission process.
   - Now we have our first parameter : `STATE_IDLE`, this is waiting for the `senndata` signal.
   - The module `STATE_STARTTX` sends the start bit.
   - The `STATE_TXING` module is responsible for transmitting the data bits.
   - The module `STATE_TXDONE` signifies that the module has completed transmitting.
     
 - State Variables
   - `state` : This stores the current state of the  FSM .
   - `buf_tx` : As the name suggests, this is a buffer that holds the module `txbyte` data during transmission.
   - `bits_sent` : Just like the name, this counts the amount of bits that were sent (transmitted)
   - `txdone` : This is used for storing the status of completion of transmission. 


 ### WIRING
 There is not much to wiring, but the code, 
`assign tx=txbit;` connects the internal `txbit` to the output `tx`.


### UART Transmission Flow (Clock-Triggered)

- Idle Detection & Initiation: 

 When the clock signal goes high, the system checks if a new data transmission is requested by looking at the `senddata` input. If `senddata` is active (high) and the UART is currently in the `STATE_IDLE`, it means we're ready to start a new transmission. The module then switches to `STATE_STARTTX`, loads the data byte (referred to as `txbyte`) into the transmission buffer (`buf_tx`), and sets the `txdone` flag to low—this tells us that the transmission process has just begun.

- Start Bit Transmission:  

As soon as we're in the `STATE_STARTTX`, the module sets the `txbit` output to low, generating a start bit. This is essential as it indicates to the receiving device that a new transmission is about to take place. The UART then shifts into `STATE_TXING` to start sending the actual data.

- Data Bit Serialization:  

 Now in the `STATE_TXING`, the module begins sending the data bits that were loaded into `buf_tx`. It starts with the least significant bit (LSB) and sends this out through the `txbit` output. After transmitting each bit, the module shifts `buf_tx` to the right to get ready for the next bit. There's a `bits_sent` counter that keeps track of how many bits have been sent. This process continues until all 8 data bits are out there.

- Stop Bit Transmission:  

 After all 8 data bits have been transmitted, the next step is to send out the stop bit. While still in the `STATE_TXING`, once the 8th bit is sent, the `txbit` output is set high to signal that the data frame has ended. The `bits_sent` counter is reset, and we transition to the `STATE_TXDONE`.

- Transmission Completion Signal:  

 In `STATE_TXDONE`, the module sets the `txdone` output to high. This is an important signal that lets any connected devices know that the transmission has been completed successfully.

- Return to Idle State:  

Finally, the system goes back to the `STATE_IDLE`, getting ready to receive a new `senddata` signal. This setup allows for continuous data transfers, making the process efficient and smooth for future transmissions.

## Desin Documentation 

 ### Here, I have created a block digram about the UART loopback structure - 


 ![Image](https://github.com/user-attachments/assets/cedb5a7e-1556-4b7a-ae6d-b59cd9bcaa22)

>[!NOTE]
>I made the digram in *CANVA*, you can access it [here](https://www.canva.com/design/DAGiEBV-yvU/XAYJV6tATJ-uTedK6lcQpA/edit?ui=eyJIIjp7IkEiOnRydWV9fQ).


 ### Here is the detailed circuit diagram showing connections between the FPGA and any peripheral devices used - 

 ![Image](https://github.com/user-attachments/assets/2ae47bf7-0c97-4417-a678-933195863348)


 This too was made in *Canva*, the link is [here](https://www.canva.com/design/DAGiFdc4tgk/7zvOy1_zbCe8kE50TtqO4g/edit?ui=eyJIIjp7IkEiOnRydWV9fQ).



## Implementation:
### Transmitting code to the FPGA board-
- First, we have to open the `ORACLE VIRTUAL BOX`, after the VM is powered up, we can open the `VSDSquadron_FM` folder.
- Next, we should create the following files in a subfolder inside the `VSDSquadron_FM` - [`Makefile`](https://github.com/JustVanillaCode/VSD-FGPA-BOARD-REPO/blob/main/Makefile), [`VSDSquadron_FM.pcf`](https://github.com/JustVanillaCode/VSD-FGPA-BOARD-REPO/blob/main/VSDSquadronFM.pcf), [`top.v`](https://github.com/JustVanillaCode/VSD-FGPA-BOARD-REPO/blob/main/top.v) and  [`uart_trx.v`](https://github.com/JustVanillaCode/VSD-FGPA-BOARD-REPO/blob/main/uart_trx.v).
  
>[!NOTE]
> All these files MUST be under a folder, I have named it `task_2_uart_loopback`
