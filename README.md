# VSD-FGPA-BOARD-REPO BY ATHRV SHARMA
> I am creating this repository for the fgpa board provided by `VSDIAT`. This repository covers all the steps, tasks and the installation process for the fgpa board.
> This all is based upon the steps mentioned in the datasheet, you can access the datasheet [here.](https://www.vlsisystemdesign.com/wp-content/uploads/2025/01/VSDSquadronFMDatasheet.pdf)

Here is all that is covered in this repository:
- [x] Installation and settings
- [x] Understanding the verilog code, pin placements, and the pcf file
- [x] Programming the board
- [x] Credits 

# Installation and Settings :
So, we start off by installing `Oracle VirtualBox` and then creating a new Operating System. You can download it from [Here](https://www.virtualbox.org/wiki/Downloads)
>[!NOTE]
> We must also install the vsd virtual hard disk file for this.

This is how it should look like

![Image](https://github.com/user-attachments/assets/022d7a69-67a7-42e5-a6b8-92a4d148bafd)

These are the settings that **SHOULD** be followed:

![Image](https://github.com/user-attachments/assets/bd08553f-8216-44c6-8153-820a3d332124)

 This is how VirtualBox should look now:

 ![Image](https://github.com/user-attachments/assets/055349d1-4461-40f6-a710-7c26d7fe086d)


 # Here is a detailed way on how to program the FGPA Board




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
