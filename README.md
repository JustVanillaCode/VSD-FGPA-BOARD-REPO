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


 # Understanding Verilog, pin placements and the PCF file:
 > You can access the verilog code [here.](https://github.com/thesourcerer8/VSDSquadron_FM/blob/main/led_blue/top.v)

##The code is as follows:

//----------------------------------------------------------------------------
//                                                                          --
//                         Module Declaration                               --
//                                                                          --
//----------------------------------------------------------------------------
module top (
  // outputs
  output wire led_red  , // Red
  output wire led_blue , // Blue
  output wire led_green , // Green
  input wire hw_clk,  // Hardware Oscillator, not the internal oscillator
  output wire testwire
);

  wire        int_osc            ;
  reg  [27:0] frequency_counter_i;

  assign testwire = frequency_counter_i[5];
 
  always @(posedge int_osc) begin
    frequency_counter_i <= frequency_counter_i + 1'b1;
  end


//----------------------------------------------------------------------------
//                                                                          --
//                       Counter                                            --
//                                                                          --
//----------------------------------------------------------------------------

//----------------------------------------------------------------------------
//                                                                          --
//                       Internal Oscillator                                --
//                                                                          --
//----------------------------------------------------------------------------
  SB_HFOSC #(.CLKHF_DIV ("0b10")) u_SB_HFOSC ( .CLKHFPU(1'b1), .CLKHFEN(1'b1), .CLKHF(int_osc));


//----------------------------------------------------------------------------
//                                                                          --
//                       Instantiate RGB primitive                          --
//                                                                          --
//----------------------------------------------------------------------------
  SB_RGBA_DRV RGB_DRIVER (
    .RGBLEDEN(1'b1                                            ),
    .RGB0PWM (1'b0), // red
    .RGB1PWM (1'b0), // green
    .RGB2PWM (1'b1), // blue
    .CURREN  (1'b1                                            ),
    .RGB0    (led_red                                       ), //Actual Hardware connection
    .RGB1    (led_green                                       ),
    .RGB2    (led_blue                                        )
  );
  defparam RGB_DRIVER.RGB0_CURRENT = "0b000001";
  defparam RGB_DRIVER.RGB1_CURRENT = "0b000001";
  defparam RGB_DRIVER.RGB2_CURRENT = "0b000001";

endmodule

## Let us break it down:
This Verilog code defines a simple hardware module called `top` that controls an RGB LED and uses an internal oscillator for timing. Let’s break down what each part does in a more relatable way.

First, we have the **internal oscillator** setup. Think of this as a small clock inside the FPGA that helps everything keep time. The code initializes this oscillator and configures it to divide its output frequency by 2, which means it will tick at a slower rate that can be used for other operations. We also make sure to power it up and turn it on so it can do its job.

Next up is the **RGB LED driver**. This part of the code is responsible for controlling an RGB LED, which can produce different colors by mixing red, green, and blue lights. Here, we activate the driver and set it to only turn on the blue LED, while keeping the red and green LEDs off. This is done using something called PWM (pulse-width modulation), which adjusts how much power goes to each LED to control its brightness.

The driver is connected to the actual LED pins on the hardware, which are labeled `led_red`, `led_green`, and `led_blue`. This means that whatever signal the driver sends will directly affect the corresponding LED.

Lastly, we have some lines that set the current for each color of the LED. This helps to ensure that the LEDs don’t get too much power, which could burn them out.

In summary, this code is setting up an FPGA to:
- Generate a clock signal that keeps everything in sync.
- Control a counter to manage timing for different processes.
- Light up the blue LED, while the others stay off.
- Protect the LEDs by managing how much current flows through them.

Overall, it’s a straightforward example of how to use internal components in an FPGA to make something work, emphasizing the functionality of both the oscillator and the LED driver.
