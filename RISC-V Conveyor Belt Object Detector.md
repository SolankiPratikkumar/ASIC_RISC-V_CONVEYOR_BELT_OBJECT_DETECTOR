
# RISC-V CONVEYOR BELT OBJECT DETECTOR


## Abstract

Our Target in this project is to make a detector that detects objects running on the conveyor belt by IR Proximity Sensor and output is obtained in LED and Buzzer. As automation is increasing this project can be used as an indicator in the Industry Conveyor Belt Process Initiation

## Hardware

![Idea_Object Detector](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/43a98878-a10f-4490-8124-b4db6dd9fac8)

## Block Diagram

![IR_BLOCK_DIAGRAM](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/044369d4-b92c-492b-a292-eb22ccbd8983)

## Working Principle

* In this project,  the object is detected either stationary or moving by an Infrared Radiation(IR) and Photoelectric Sensor. The IR LED and the photodiode are linked in a parallel configuration, serving as both a transmitter and receiver. When an obstacle is in the path of the IR LED, which emits light, the reflected light is captured by the photodiode, acting as a receiver. This reflection causes a reduction in the photodiode's resistance, leading to the generation of a significant number of charge carriers and the creation of an electrical signal.
  
![IR-sensor-Working](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/f4c65e8b-b122-4a53-909c-bee121a5c4cf)

* Then that signal is passed through the potentiometer to meet the distant object up to 500cm and the obtained signal is kept as an input to the RISC-V core which will process the signal and provide output to the Buzzer and LED.
* So, by this process, we would get the detection of a moving object and our output target will be met

**Details IR Sensor Module Circuit Diagram**

![How-to-make-IR-Sensor-Module-Circuit-Diagram](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/70e12018-2a69-44f8-ae4b-11d4439fee29)

* When the Photodiode receives reflected infrared light, its resistance significantly decreases, leading to a drop in voltage across the photodiode. This higher voltage is then directed to the Inverting input of the LM393/LM358 IC.
* Subsequently, the IC compares this voltage with the threshold voltage. In this scenario, the Inverting input voltage surpasses the Non-Inverting input voltage, causing the IC output to be Low. Consequently, the sensor output registers as Low
* On the other hand, if the Photodiode or IR receiver (IR-RX) fails to detect infrared light, the photodiode's resistance becomes very high. As a result, a lower voltage from the photodiode is supplied to the Inverting input of the IC. The LM393/LM358 IC then compares this voltage with the threshold voltage.
* In this situation, the Inverting input voltage is less than the Non-Inverting input voltage, leading to a High output from the IC. Hence, the sensor output is recognized as High.

![downloadirsensorrrrrr](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/a65b808c-c264-4c35-b55a-f99d602d5d5c)

* The IR sensor is equipped with an integrated variable resistor, specifically a 10k preset. This component serves the purpose of configuring the operational range. By turning the preset knob, which is adjustable, the detection distance can be set within an effective range.
  
* Clockwise rotation of the preset knob extends the detection range, while counterclockwise rotation reduces it.
  

![IR-Sensor-Module-Sensitivity-set](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/f7923c27-cb3f-4615-b1c9-81bce4acd238)

## GPIO PINS:

![Screenshot from 2023-12-09 15-08-44](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/b2730883-9460-497e-9787-24667c4378bf)

* x30[0] is the input pin where the sensor value is received
* x30[1] is pin to provide output and it will blink output LED
* x30[2] is pin to provide output and it will sound output Buzzer
  
## C-Code with Inline Assembly Code:

```
int main() {
    int sensor_input;  //Declares an integer variable named sensor_input. This variable is likely intended to store input from a sensor
    int buzzer;  // Declares an integer variable named buzzer. This variable might be used to control a buzzer.
    int led;   // Declares an integer variable named led. This variable might be used to control an LED
    int mask = 0xFFFFFFF9;   // Declares an integer variable named mask and initializes it with the hexadecimal value 0xFFFFFFF9. This value is a bitmask used for bitwise operations. In binary, 0xFFFFFFF9 is 11111111111111111111111111111001.. This bitmask is often used to manipulate specific bits in a variable while leaving others unchanged.
  
while(1)
{
     asm (
                
		"andi %0, x30, 1\n\t"  //The andi instruction performs a bitwise AND operation between the contents of register x30 and the immediate value 1 and stores resulting in the first operand 0 pin in x30
		: "=r"(sensor_input)   //The result of this above operation is stored in the sensor_input variable
		:                      // The first colon separates the output operand constraint from the input operand constraints (which are not present here)
                :                      // The second colon separates the input operand constraints from the clobbered registers (modified registers) 
                 );                    // This first code snippet extracts the least significant bit of register x30 and stores it in the sensor_input variable. The result in sensor_input will be either 0 or 1, depending on the value of the least significant bit in register x30



    if (sensor_input == 1) {            // entered into if loop when sensor input value is received as 1
        asm (
            "ori x30, x30, 6\n\t"      //immediate or of x30 register value with 6(110) then sets the bits 0, 1, and 2 of register x30 
            "andi %0, x30, 4\n\t"      //immediate and of x30 above value with 4(100),checks if the third bit of register x30 is set and sets the value of buzzer
            "andi %1, x30, 2"          //immediate and of x30 above value with 2(010), checks if the second bit of register x30 is set and sets the value of LED 
            : "=r"(buzzer), "=r"(led)  //gives a signal to the Buzzer and LED by updated set(1) values of x30 register 
            : "r"(mask)                // Specifies that the value of the mask will be placed in an x30 register to use within the inline assembly code
        );     
    }    //this code snippet modifies the contents of register x30 based on the value of sensor_input. If sensor_input is 1, specific bits in x30 are set, and the values of buzzer and led are determined based on the state of certain bits



    else {                           // entered into if loop when sensor input value is received as 0 
        asm (
            "and x30, x30, %2\n\t"  //performs a bitwise AND operation between the contents of register x30 and the value of mask, effectively clearing bits that are not set in mask
            "andi %0,x30, 4\n\t"  //immediate and of x30 above value with 4(100),checks if the third bit of register x30 is set and sets the value of buzzer
            "andi %1, x30, 2"     //immediate and of x30 above value with 2(010), checks if the second bit of register x30 is set and sets the value of LED
            : "=r"(buzzer), "=r"(led)  //gives a signal to the Buzzer and LED by updated reset(0) values of x30 register
            : "r"(mask)       // Specifies that the value of the mask will be placed in an x30 register to use within the inline assembly code
        );
    }    //this code snippet modifies the contents of register x30 by applying a bitwise AND operation with mask. The values of buzzer and led are then determined based on the state of certain bits in the modified x30

   } 
    return 0;
}


```

**Explaining above C-Code**

* Initialization:
Three integer variables (sensor_input, buzzer, and led) are declared to store values.
mask is initialized to 0xFFFFFFF9.
* Declares an integer variable named mask and initializes it with the hexadecimal value 0xFFFFFFF9. This value is a bitmask used for bitwise operations. In binary, 0xFFFFFFF9 is 11111111111111111111111111111001. This bitmask is often used to manipulate specific bits in a variable while leaving others unchanged.

The bitmask here has certain bits set to 0 (cleared) and others set to 1 (preserved). The specific bits that are set to 0 are the 1st, 2nd, and 3rd from the right. The rest of the bits are set to 1. The bitmask is commonly used to clear specific bits in a variable without affecting the others.

* Infinite Loop: 
The program enters an infinite loop (while(1)) for continuous operation.

* "andi %0, x30, 1\n\t":

This is the actual assembly code written in a string. It is a RISC-V assembly instruction.
andi: Stands for "AND Immediate." It performs a bitwise AND operation between two operands.
%0: This is a placeholder for the first operand, which will be replaced by a variable when the assembly code is used.
x30: Refers to register x30 in the RISC-V architecture.
, 1: Specifies the immediate value 1 that will be used in the AND operation.
\n\t: Newline and tab characters, which are part of the assembly code syntax.


* Reading Sensor Input:

* The first inline assembly block reads the value from register x30 and performs a bitwise AND operation with 1. The result is stored in the sensor_input variable.
  
* Conditional Block:
The code then checks if sensor_input is equal to 1 using an if statement.

* "ori x30, x30, 6\n\t":

ori: Stands for "OR Immediate." It performs a bitwise OR operation between two operands.
x30: Refers to register x30 in the RISC-V architecture.
, x30, 6: Specifies that register x30 should be ORed with the immediate value 6.
The result is that bits 0 and 1 of register x30 are set to 1 (bitwise OR with 6 sets bits 0, 1, and 2)

* "andi %0, x30, 4\n\t":

andi: Stands for "AND Immediate." It performs a bitwise AND operation between two operands.
%0: Refers to the first output operand, which is buzzer in this case.
x30, 4: Specifies that register x30 should be ANDed with the immediate value 4.
The result is that %0 (buzzer) will be set to 4 if the third bit of register x30 is set; otherwise, it will be set to 0

* "andi %1, x30, 2":

Similar to the previous instruction, this performs a bitwise AND operation between register x30 and the immediate value 2.
%1: Refers to the second output operand, which is led in this case.
The result is that %1 (led) will be set to 2 if the second bit of register x30 is set; otherwise, it will be set to 0

* If sensor_input is 1:
 If true, another inline assembly block is executed. It performs bitwise OR (ori) and AND (andi) operations on register x30, modifying specific bits and the results are stored in the buzzer and led variables which will turn on Buzzer and LED


* "and x30, x30, %2\n\t":

and: Stands for "AND" and performs a bitwise AND operation between two operands.
x30: Refers to register x30 in the RISC-V architecture.
x30, %2: Specifies that register x30 should be ANDed with the value of mask.
The result is that only the bits set in mask will be preserved in register x30, and other bits will be cleared.


* If sensor_input is not 1:

If false, a different inline assembly block is executed. It performs bitwise AND (and) and AND (andi) operations on register x30. The results are again stored in the buzzer and led variables.

* Loop Continuation:
This loop continues indefinitely.

* Return Statement:
The return 0; statement is present but is practically unreachable in an infinite loop.


## Testing GCC C-Code:

```
#include <stdio.h>
#include <stdlib.h>
#include <time.h>

int main() {
    int sensor_input;      // bit 0
    int buzzer;      // bit 1
    int mask = 0xFFFFFFF2;
    int min = 1;
    int max = 100;
    int j = 0;
    int k = 0;
    srand(time(NULL));  // Seed the random number generator with the current time

    while (j < 11) {
        int randomNum = rand();
        printf("Random number: %d\n", randomNum);
        sensor = randomNum % 2;
        printf("Sensor value: %d\n", sensor);

        if (sensor == 1) { 
            printf("Presence of Object\n");
            buzzer = 1;
            /* asm(
            "and x30, x30, %1\n\t"
            "or x30, x30, %0\n\t"
            :"=r"(buzzer)
            :"r"(shift)); */
            for (int k = 0; k < 1000000; k++);
        }
        else { 
            printf("Presence of Object is not detected\n");
            buzzer = 0; 
            /* asm(
            "and x30, x30, %1\n\t"
            "or x30, x30, %0\n\t"
            :"=r"(buzzer)
            :"r"(shift)); */
            for (int k = 0; k < 1000000; k++);
        }

        j++;
    }

    return 0;
}

```


## Commands for Testing of GCC-Code

* Run the following commands to run Test commands:
  
```
gcc test_object.c
./a.out
```

![Screenshot from 2023-10-05 22-47-17](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/5b9c2a7a-2cf1-48de-be71-c2a5ccf3f280)


## Commands for Conversion of C-Code to Assembly Code

```
riscv64-unknown-elf-gcc -march=rv32i -mabi=ilp32 -ffreestanding -nostdlib -o out objectdetector.c 
riscv64-unknown-elf-objdump -d -r out > asm.txt
```

## Assembly Code

```

out:     file format elf32-littleriscv


Disassembly of section .text:

00010054 <main>:
   10054:	fe010113          	addi	sp,sp,-32
   10058:	00812e23          	sw	s0,28(sp)
   1005c:	02010413          	addi	s0,sp,32
   10060:	ff900793          	li	a5,-7
   10064:	fef42623          	sw	a5,-20(s0)
   10068:	001f7793          	andi	a5,t5,1
   1006c:	fef42423          	sw	a5,-24(s0)
   10070:	fe842703          	lw	a4,-24(s0)
   10074:	00100793          	li	a5,1
   10078:	02f71063          	bne	a4,a5,10098 <main+0x44>
   1007c:	fec42783          	lw	a5,-20(s0)
   10080:	006f6f13          	ori	t5,t5,6
   10084:	004f7713          	andi	a4,t5,4
   10088:	002f7793          	andi	a5,t5,2
   1008c:	fee42223          	sw	a4,-28(s0)
   10090:	fef42023          	sw	a5,-32(s0)
   10094:	fd5ff06f          	j	10068 <main+0x14>
   10098:	fec42783          	lw	a5,-20(s0)
   1009c:	00ff7f33          	and	t5,t5,a5
   100a0:	004f7713          	andi	a4,t5,4
   100a4:	002f7793          	andi	a5,t5,2
   100a8:	fee42223          	sw	a4,-28(s0)
   100ac:	fef42023          	sw	a5,-32(s0)
   100b0:	fb9ff06f          	j	10068 <main+0x14>

```

## Unique Assembly Instruction

Number of different instructions: 9
List of unique instructions:
```
bne
and
j
li
andi
ori
sw
addi
lw

```
![Screenshot from 2023-11-02 18-59-09](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/9ea142dc-037e-4713-a21e-f14c2d4196d4)



## Spike Working Functionality

* In Spike Simulation Code we have verified the C-code by using 2 iteration
* Firstly, when the object is not detected then input = 0 then we have printed input_objectdetected=0 by which the program has entered into the "else" loop and at output led=0 and buzzer=0
* Secondly, when the object is detected than input = 1 then we have printed input_objectdetected=1 by which we entered into "if" loop because input=1 and made the output led = 1 and buzzer=1 and Finally the output was both printed as the object is detected


## Spike Simulation Code

```
#include <stdio.h>

int main() {
    int sensor_input;
    int buzzer;
    int led;
    int mask = 0xFFFFFFF0;
    int input = 0x00000001;

     asm (
                "or x30, x30, %1\n\t"
		"andi %0, x30, 1\n\t"
		: "=r"(sensor_input) 
		: "r"(input) 
                :
                 );

    if (sensor_input == 1) {
        asm (
            "or x30, x30, 6\n\t"
            "andi %0, x30, 4\n\t"
            "andi %1, x30, 2"
            : "=r"(buzzer), "=r"(led)
        );
    } else {
        asm (
            "and x30, x30, %2\n\t"
            "andi %0,x30, 4\n\t"
            "andi %1, x30, 2"
            : "=r"(buzzer), "=r"(led)
            : "r"(0xFFFFFFF9)
        );
    }

    if (buzzer) {
        printf("input is 1. Buzzer is on. Value of BUZZER is %d\n", buzzer);
    } else {
        printf("input is 0. Buzzer is off. Value of BUZZER is %d\n", buzzer);
    }

    if (led) {
        printf("input is 1. LED is on. Value of LED is %d\n", led);
    } else {
        printf("input is 0. LED is off. Value of LED is %d\n", led);
    }

    return 0;
}

```

## Commands to Get Output of Spike

```
riscv64-unknown-elf-gcc -march=rv64i -mabi=lp64 -ffreestanding -o out file.c
spike pk out
```

## Spike Simulation Output

![Screenshot from 2023-10-31 23-07-13](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/f249c9dc-d965-43de-a5da-7c8d3251489d)


## Functionality Simulation Commands:

* We will do the functional simulation for the processors that are being created for the assembly program that is being created for my application. The processor.v and the testbench.v is uploaded and those can be seen above.


```
iverilog -o test processor.v testbench.v
./test
gtkwave waveform.vcd
```

![Screenshot from 2023-11-01 15-49-54](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/2daf0c9a-8450-44cf-b29a-38218b506d4e)

## Functional Simulation Output and Instruction Verification:

* We get the output waveform with input toggle at 4000 seconds

![Screenshot from 2023-11-02 19-02-55](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/4b67d0f1-1da5-4078-b59d-9c7fc990ab7f)


*  Here firstly, we have taken input as 1 so the expected output must be '11' at pins 2 and 3 and we can verify that in the waveform led=1 and buzzer=1 for 1 input with write=1 and ID_instruction is running with clk which confirms the running of assembly language instruction of "004F7713" with assembly code line of  "100a0:	004f7713  andi  a4,t5,4" 
  
![Screenshot from 2023-11-02 19-16-37](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/9cbb1527-241c-4efc-918a-a7351979411c)

![Screenshot from 2023-11-02 19-17-14](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/3a68d41e-4346-47eb-b9ec-6e9a012ae2b5)


* Here secondly, we have taken input as 0 so the expected output must be '00' at pins 1 and 2 and we can verify that in the waveform led=0 and buzzer=0 for 0 input with write=1 and ID_instruction is running with clk which confirms the running of assembly language code of 004F7713 assembly code line of  "100a0:	004f7713  andi  a4,t5,4" which will and immediate values of 4 and t5 that will be store in a5
* Here the same loop of code as repeated after 14 times going to zero of the assembly code

![Screenshot from 2023-11-02 21-55-52](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/bfa425e0-45a4-4146-bfab-1edaa601ce19)

![Screenshot from 2023-11-02 22-07-46](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/d9334d96-3bac-4619-ab57-1a903b2390c4)

![Screenshot from 2023-11-02 19-17-14](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/b9f6d4c5-06d9-4daf-bee7-10107535412c)



* Now, the third example shows that input gets low  to 0 but the output stays '11' for some delay and the instruction written over here is "FEF42023" from assembly code of " 100ac: fef42023 sw a5,-32(s0)" which stores the value -32 value in a5 register 
![Screenshot from 2023-11-02 19-32-38](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/ec442482-b922-4267-b41f-4873b77cce0a)

![Screenshot from 2023-11-02 19-32-59](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/f140fad0-ac71-4de2-bd36-eb7177e4d515)

* Similar example for before dumping "11" in the output which is ori instruction "006F6F13" which in assembly code written as " 10080:	006f6f13    ori	t5,t5,6" 
![Screenshot from 2023-11-02 20-20-32](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/b8a0379d-e391-4c80-b740-739990e27b2f)

![Screenshot from 2023-11-02 20-20-52](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/7a12c6db-d5af-444f-9f6c-049213d50fef)


* Similar, more code is example of assembly instruction "02f71063"  with assembly code running  line  "10078:  02f71063 bne  a4,a5,10098 <main+0x44>"

![Screenshot from 2023-11-02 20-32-41](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/1754622e-28b8-4de9-ad26-be29517d730a)

## Synthesis and Gate Level Simulation:

* These changes involve commenting out the module definitions for both sky130_sram_2kbyte_1rw1r_32x256_8_data and sky130_sram_2kbyte_1rw1r_32x256_8_inst.

* Furthermore, the previously instantiated SRAM modules are adjusted from sky130_sram_2kbyte_1rw1r_32x256_8_data and sky130_sram_2kbyte_1rw1r_32x256_8_inst to sky130_sram_1kbyte_1rw1r_32x256_8 since the processor doesn't actually require 2k RAM.

* The synthesis process is executed twice, once with writing_inst_done=1 and once with writing_inst_done=0, resulting in two netlists named synth_test.v and synth_processor.v, respectively.

* When writing_inst_done=1, this implies that the UART is bypassed, preventing the .vcd file from consuming excessive storage space (over 20GB). Subsequently, gate-level simulation and verification are performed using the corresponding netlist.

```
yosys
read_liberty -lib sky130_fd_sc_hd__tt_025C_1v80_256.lib 
read_verilog processor.v 
synth -top wrapper 
dfflibmap -liberty sky130_fd_sc_hd__tt_025C_1v80_256.lib
abc -liberty sky130_fd_sc_hd__tt_025C_1v80_256.lib
write_verilog synth_processor.v
```

* Yosys Invoke and .lib file imported and verilog module instantiation:
  
![Screenshot from 2023-11-02 23-21-04](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/c9340e57-bdbd-4f57-a8cc-0859ed57d2c7)

* Top module wrapper synthesis where we see generic cells and sky130 sram 2 cells
  
![Screenshot from 2023-11-02 23-22-15](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/9ad6f8ed-4ace-4d42-9760-da3e716e792c)

* Mapping Flipflops using dfflibmap command in yosys:
  
![Screenshot from 2023-11-02 23-23-15](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/40bb10fc-5777-4a9e-a041-7b53ba69dcb4)

* Mapping standard cells to sky130 cells using ABC command in yosys:
  
![Screenshot from 2023-11-02 23-24-19](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/6b0252c5-bc21-439a-b8f2-fd67e0f68fbb)

*  Executing Verilog synth_test files and Dumping all modules:
  
![Screenshot from 2023-11-02 23-25-20](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/f27ecb72-5f90-4f6c-b095-650a91509d5f)

* Before conducting gate-level simulation using the synthesized netlist, certain modifications are implemented in the synthesized netlist file.
* Before the synthesis stage, we had altered the instantiation names of the SRAM module to align them with the standard cells. sky130_sram_1kbyte_1rw1r_32x256_8_inst and sky130_sram_1kbyte_1rw1r_32x256_8_data, since the module definition is present in that file.
  
![Screenshot from 2023-11-03 00-24-58](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/5167042a-59e4-4303-92db-7a4c6247f8b5)

* The sky130_sram_1kbyte_1rw1r_32x256_8.v file, we replace the memory instructions with the processor instructions obtained from the processor.v file
  
![Screenshot from 2023-11-02 23-53-51](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/f485cdcf-9c9d-468b-9984-025f330a4bf0)


* Command to run Gate Level Simulation:
  
```
iverilog -o synth_processor.v testbench.v synth_test.v sky130_sram_1kbyte_1rw1r_32x256_8.v sky130_fd_sc_hd.v primitives.v

./syth_test.v
```

![Screenshot from 2023-11-02 23-54-16](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/136cc239-08e8-4be1-adb1-da860b0c9b0c)

* GLS Output Waveform Generation:

![Screenshot from 2023-11-03 00-03-25](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/43e83ba0-e521-4a16-be07-3392fb1f1253)

![Screenshot from 2023-11-03 00-04-10](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/ef92802b-528f-4ff0-b1da-498a62ce896e)

* Also we can observe the ID_instruction running in synthesis stage
  
![Screenshot from 2023-11-03 00-04-58](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/44b0c928-7e78-4908-a231-880574dcc777)

* We generate the netlist by using the following commands
   
```
show wrapper
```

![Screenshot from 2023-11-03 00-12-54](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/6b16ee9c-da8e-4f1c-9138-f2ad6a4ea4e4)

![Screenshot from 2023-11-03 00-13-32](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/03f60251-c602-41eb-bcd7-e48d0e3101cf)


## Physical Design,Place and Route(PNR)

* The Physical Design Place and Route (PNR) flow in ASIC design is a critical process that transforms the synthesized netlist into a physically implementable design on the target chip. Here is a brief description of the PNR flow as Floorplan, Static Timing Analysis (STA), Power Planning, Placement, 
Clock Tree Synthesis (CTS), Routing

![PD-Flow](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/960ff35b-5c04-46c4-9806-93b842c317dc)

![asic_flow](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/2d9f48f7-c515-49c3-97fb-4f90612865e1)

Below are the stages and the respective tools that are called by Openlane for the functionalities as described:

* Synthesis involves the creation of a gate-level netlist using Yosys, incorporating cell mapping through ABC, and executing pre-layout Static Timing Analysis (STA) using OpenSTA.
* Floorplanning encompasses the definition of the core area for the macro, placement of cell sites and tracks (init_fp), and arrangement of macro input and output ports using ioplacer. Additionally, it includes the generation of the power distribution network (pdn).
* Placement involves global placement through Replace, followed by detailed placement to legalize the globally placed components using OpenDP.
* Clock Tree Synthesis (CTS) entails the synthesis of the clock tree using TritonCTS.
* Routing comprises global routing to generate a guide file for the detailed router (FastRoute), and subsequent detailed routing using TritonRoute.
* GDSII generation involves the creation of the final GDSII layout file from the routed DEF file using Magic.
  
## OpenLane

* OpenLane is an RTL to GDSII automated flow that integrates various components such as OpenROAD, Yosys, Magic, Netgen, CVC, SPEF-Extractor, CU-GR, Klayout, and several custom scripts for design exploration and optimization.
* This comprehensive flow covers the entire spectrum of ASIC implementation, starting from RTL and concluding with GDSII.

## MAGIC

* MAGIC, short for "Mask and Graphics for Integrated Circuits," is an open-source layout tool used in ASIC (Application-Specific Integrated Circuit) design.
* It provides a graphical environment for editing and verifying the physical layout of integrated circuits. Designers use MAGIC to create and modify the arrangement of components on a chip, ensuring adherence to design rules.
* It plays a key role in tasks such as layout editing, verification, and parasitic extraction, and is often used in conjunction with other tools in the ASIC design flow.
* As an open-source tool, MAGIC fosters collaboration and is widely used in both academic and industry settings for custom IC design and digital ASIC design

## Preparing the Design Flow

* Preparing the design and including the lef files: The commands to prepare the design and overwrite in an existing run folder the reports and results along with the command to include the lef files is given below:
  
```
sed -i's/max_transition   :0.04/max_transition   :0.75'*/*.lib
```

* Perform below to start the interface of OpenLane and run the below commands:
  
```
make mount
%./flow.tcl -interactive
% package require openlane 0.9
% prep -design Object_sensor_PNR
```

![Screenshot from 2023-11-17 00-31-14](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/5f7601c1-a00e-40d0-898e-585b21605063)

**Changed JSON File**

![Screenshot from 2023-11-18 15-49-37](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/876be501-b353-4bc0-8876-cdf386fa9db0)

* Over here we have made changes and iterated multiple times the values of  "CLOCK_PERIOD", "PL_TARGET_DENSITY", "FP_CORE_UTIL", "PL_MACRO_CHANNEL" for running all the steps of PNR
  
* Here in the Openlane we have run all the steps command in one go because we need to do multiple iterations
  
## Synthesis

* In ASIC (Application-Specific Integrated Circuit) design, synthesis is a crucial step that involves the translation of a high-level hardware description of a digital circuit, typically written in a hardware description language (HDL) such as Verilog or VHDL, converted into a gate-level netlist. The netlist represents the logical functionality of the circuit using lower-level components like gates, flip-flops, and other digital elements.
* Logic Synthesis is the process of transforming HDL code into a logic circuit based on a compiled technology-specific library and user-specified optimization
constraints.
* ASIC design Synthesis includes the steps of HDL Input, Pre-Synthesis Checks, Optimization, Technology Mapping, Cell Mapping, Netlist Generation, Timing Analysis, constraint handling, and Output Generation.
* The goal is to achieve a design that meets performance, area, and power requirements.
* Logic Synthesis = Translation + Mapping + Optimization
* So firstly there is a translation(read) of Verilog, VHDL code into Generic Boolean Netlist(GTECH) which is then Mapping/Optimization (compile) into the Standard Cell on Targeted Technology Mapping
  
* Command used in Openlane for Synthesis is as follows:
  
  ```
  run_synthesis
  ```
  
![Screenshot from 2023-11-14 16-04-54](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/f11e93da-4e05-40d1-b38a-bc81c1a01fb6)

**Synthesis_Report**

![Screenshot from 2023-11-15 10-23-35](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/dfc0b041-0266-49e0-9233-917fb85b3c0e)

![Screenshot from 2023-11-15 10-33-24](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/2f9d2aa7-7630-4a07-ac88-101ef79c767a)

![Screenshot from 2023-11-15 10-34-41](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/f91b8e84-8f55-42e2-90d6-07530a4ff062)

* By this report we can analyse the Area, Power and the Timing summary for the Synthesis process
  
## Floorplan

* In ASIC design with OpenLane, the floorplanning process involves defining the physical layout of the chip on the silicon die. It includes the allocation of space for various components and establishes the core area, cell sites, and routing tracks.
* Here's a brief explanation of the floorplan process in OpenLane, they are Initiating Floorplan (init_fp), Predefined Macro Placement, Input-Output port Placement (ioplacer), Power Distribution Network (pdn), Tracks and Rows for layout
* Here the pads and pins are assigned as a part of Design Import by reading an IO assignment file or reading in a DEF file
* Floorplan environment variables or switches:
  
```
FP_CORE_UTIL - floorplan core utilisation; FP_ASPECT_RATIO - floorplan aspect ratio ; FP_CORE_MARGIN - Core to die margin area ; FP_IO_MODE - defines pin configurations (1 = equidistant/0 = not equidistant); FP_CORE_VMETAL - vertical metal layer; FP_CORE_HMETAL - horizontal metal layer
```
Note: Usually, the vertical metal layer and horizontal metal layer values will be 1 more than that specified in the file
  
* Command used in Openlane for Floorplan is as follows:
  
  ```
  run_floorplan
  ```
  
![Screenshot from 2023-11-14 16-04-54](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/e0f45189-e5db-4627-88a5-3621695427f5)

**Floorplan_Report**

![Screenshot from 2023-11-15 10-53-17](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/c3a07837-cdcd-4931-ab9e-d346edc50246)

![Screenshot from 2023-11-18 12-32-10](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/0ff023c5-cec2-4071-ad6f-936bd1f2913f)

![Screenshot from 2023-11-15 10-54-03](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/56085393-4238-4637-bada-1885d5685d17)

* Here, in Floorplan we can analyse the Die area, Core area, Number of Endcap and Tap cells and also the Number of voltage sources added

* Following the completion of the floorplan run, a .def file is generated in the results/floorplan directory.
* To visualize the floorplan wrapper, navigate to the  /home/solanki-pratikkumar/OpenLane/designs/Objectsensor_PNR/runs/RUN_2023.11.14_06.47.11/results/floorplan  directory and execute the following command using Magic:
  
```
$ magic -T /home/solanki-pratikkumar/.volare/volare/sky130/versions/1341f54f5ce0c4955326297f235e4ace1eb6d419/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.nom.lef def read wrapper.def &
```

![Screenshot from 2023-11-18 13-25-15](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/bae102aa-0118-4cc9-9f33-45d235774c37)

![Screenshot from 2023-11-18 13-24-42](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/1cb212c0-976d-42b6-ba33-8407d92e3690)

![Screenshot from 2023-11-18 13-26-42](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/77b9c07b-6e19-47d4-b8b0-ad2b1008b80b)

![Screenshot from 2023-11-18 13-44-13](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/b146d548-c3f2-468b-9a77-3f46e7aa1622)

* Over here we can observe the wrapper of the floorplan with tapcell and data_mem and inst_mem block vacant and ready for further process to get the placement cells
  
## Placement 

* In the context of OpenLane ASIC design, placement refers to the process of determining the physical locations of standard cells and other components on the chip's silicon die. This step is crucial for achieving optimal performance, signal integrity, and overall efficiency in the final integrated circuit. Here's a brief explanation of the placement process in OpenLane:

* Global Placement (Replace): The placement process starts with a global placement step, often performed using the "Replace" tool in OpenLane. This step involves assigning approximate locations to the standard cells and macros on the chip, considering factors such as signal delay, power consumption, and area utilization. Global placement provides a high-level arrangement of components.

* Detailed Placement (OpenDP): Following global placement, detailed placement is performed to refine and legalize the positions of the cells. OpenDP (OpenLane's Detailed Placement tool) is commonly used for this task. Detailed placement aims to meet specific design constraints, such as avoiding overlap between cells, adhering to the floorplan, and optimizing the placement for performance and power.

* Legalization: Legalization ensures that the detailed placement adheres to the physical and manufacturing constraints specified in the design rules. This step guarantees that the final placement is manufacturable and meets the requirements of the target technology.

* Optimization is the process of iterating through a design such that it meets timing, area, and power specifications.
* Depending on the stage of the design, optimization can include the following operations: Adding buffers, Resizing gates, Restructuring the netlist, Remapping
logic, Swapping pins, Deleting buffers, Moving instances, Applying useful skew, Layer optimization, and Track optimization.

* Command used in Openlane for Placement is as follows:
  
  ```
  run_placement
  ```
  
![Screenshot from 2023-11-14 16-04-54](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/7808df5b-ae58-4f79-b4d6-a59d1c4964bc)
  
![Screenshot from 2023-11-14 16-05-10](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/c9215fb0-576d-4246-a080-98f4570d4bd9)


**Placement_Report**

![Screenshot from 2023-11-18 15-52-43](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/be8d6d5e-efaa-46dd-b6e4-b0116ba9ce54)

![Screenshot from 2023-11-18 15-59-40](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/9b2e359c-5222-495b-bcd9-e3251e366c14)


* Finally, run the following command in the latest result directory which was in my case:
  /home/solanki-pratikkumar/OpenLane/designs/Objectsensor_PNR/runs/RUN_2023.11.14_06.47.11/results/placement

```
 magic -T /home/solanki-pratikkumar/.volare/volare/sky130/versions/1341f54f5ce0c4955326297f235e4ace1eb6d419/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.nom.lef def read wrapper.def &
```

![Screenshot from 2023-11-18 15-46-50](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/5e127378-1c6d-4d0c-be1c-899a892c1552)

![Screenshot from 2023-11-18 15-31-09](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/004681d1-87cd-4804-b938-a00410a9b6c4)

![Screenshot from 2023-11-18 15-36-56](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/52ffd654-7490-4e57-804d-84752cfa6261)

## Clock Tree Synthesis(CTS):

* Clock Tree Synthesis (CTS) is the process of inserting buffers in the clock path, with the goal of minimizing clock skew and latency to optimize timing distribution across the chip
* Here's a brief explanation of the Clock Tree Synthesis process in OpenLane: Input Constraints, Hierarchical Clock Tree Construction, Buffer Insertion, Clock Skew Optimization, Clock Routing, Clock Tree Verification, Integration with Overall Design Flow
  
*  Command used in Openlane for CTS and PDN is as follows:
  
```
run_cts
gen_pdn
```

![Screenshot from 2023-11-14 16-05-10](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/19828fe8-88eb-4fe5-aeb0-33bc1b7ae2d3)

**CTS_Report**

![Screenshot from 2023-11-18 18-26-58](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/f064a118-6854-4f72-a07d-9badd72387d3)

## Routing 

* The primary goal of routing is to define the paths that signals will take to traverse the chip, connecting various logic elements, memory cells, and other components. Here's a brief explanation of the routing process:
* Global routing involves determining the approximate paths for interconnections at a high level. It establishes the general routes that signals will follow, considering the overall chip architecture.
* Detailed routing is the subsequent step where the specific paths for each net are refined. This process considers the detailed placement of components on the chip and selects the physical resources (metal layers) for the interconnections.
* Routing Resources: Routing utilizes the available routing resources, typically metal layers, on the chip. The choice of metal layers depends on factors such as signal speed, capacitance, and the overall manufacturing process.
* Obstacle Avoidance: Routing algorithms need to navigate around obstacles like other components, power grids, and clock networks. Algorithms ensure that routes are established while adhering to design rules and constraints.
* Timing Considerations: Timing considerations play a crucial role in routing. Signals must reach their destinations within specified timing constraints to ensure correct circuit operation. Timing-driven routing is employed to meet these constraints.
* Power Considerations: Routing also considers power consumption. Power-aware routing aims to minimize the length of critical paths, reducing dynamic power consumption associated with the charging and discharging of capacitances.
* Signal Integrity: Signal integrity is maintained by avoiding excessive delays, reflections, and crosstalk. Proper routing techniques and metal layers are chosen to prevent signal degradation during transmission.
* Verification: After routing, the design undergoes verification to ensure that all connections meet design rules, constraints, and performance criteria. This includes checks for timing closure, signal integrity, and manufacturability.

* Command used in Openlane for Routing is as follows:
  ```
  run_routing
  ```
  
![Screenshot from 2023-11-14 16-05-54](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/6e213d89-bdef-4d23-80e1-f85b6e7fb2c7)

![Screenshot from 2023-11-14 16-25-02](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/a57dc5ae-bec4-4a1c-82c1-a66f9e619dab)

* Following the completion of the routing run, a .def file is generated in the results/routing directory.
* To visualize the routing wrapper, navigate to the  /home/solanki-pratikkumar/OpenLane/designs/Objectsensor_PNR/runs/RUN_2023.11.14_06.47.11/results/routing  directory and execute the following command using Magic:
```
magic -T /home/solanki-pratikkumar/.volare/volare/sky130/versions/1341f54f5ce0c4955326297f235e4ace1eb6d419/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.nom.lef def read wrapper.def &
```

![Screenshot from 2023-11-14 20-28-33](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/4b788cd0-a895-4ed1-ad74-3bf99fe191f8)

![Screenshot from 2023-11-18 18-35-00](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/cb9d4f89-9d1f-4340-9615-53fe851cbb42)

![Screenshot from 2023-11-18 18-40-04](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/7cc92cc5-6aea-4256-ab70-9a953f746c22)


**Routing_Report** 

![Screenshot from 2023-11-18 19-53-05](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/89d882ca-4649-4fc5-b5c6-db9889f40ede)

**Die Area and Nets**

![Screenshot from 2023-11-14 18-36-00](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/59b66f88-f5c8-43e8-a492-b0817d0e3538)

**Congestion Report and Wirelength**

![Screenshot from 2023-11-14 18-33-36](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/0a59392a-7f13-419b-bd6f-90df444a5d89)

**Power Report**

![Screenshot from 2023-11-14 18-30-49](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/20ee17a1-566d-4a3c-aac9-b09b9d242f2c)

**Timing Report and Area Report**

![Screenshot from 2023-11-14 18-30-30](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/2bab5c94-a854-4e7a-b5ba-e8c77415ba80)

**DRC violation is zero**

![Screenshot from 2023-11-18 19-53-29](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/39433200-6800-49d9-915d-105302fce7d1)

## Performance Calculation

* Given a Clock period of 10ns in json file, setup slack we got after routing is 6.95ns
  
```
                              1
Max Performance =  ------------------------
                     clock period - slack(setup)
```

```
Max Performance = 0.3278 Ghz
```

**Antenna_Checks**

```
run_magic
run__magic_spice_export
run_magic_drc
run_antenna_check
```

![Screenshot from 2023-11-14 16-05-54](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/6e213d89-bdef-4d23-80e1-f85b6e7fb2c7)

![Screenshot from 2023-11-14 16-25-02](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/a57dc5ae-bec4-4a1c-82c1-a66f9e619dab)


## Runs Folder 

https://iiitbac-my.sharepoint.com/:f:/g/personal/solanki_pratikkumar_iiitb_ac_in/EtlDFc0uvV1Mglrv_FZIR70BGGnBbyo5gUHmoT9rewXXzQ?e=MezLpU


## Output Photo

![Screenshot from 2023-12-09 15-18-51](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/7b40f960-77d6-428d-905c-af3ed78f764c)

![Screenshot from 2023-12-09 15-20-46](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/f6bf8dae-047a-485c-8b4b-c1c5a0a4f80b)

![Screenshot from 2023-12-09 15-21-50](https://github.com/SolankiPratikkumar/IIITB_PRATIKKUMAR_ASIC/assets/140999250/e92229fc-97a4-49d0-886b-35ca7c4bb87d)


## Final Presentation Video

https://iiitbac-my.sharepoint.com/:f:/g/personal/solanki_pratikkumar_iiitb_ac_in/EtlDFc0uvV1Mglrv_FZIR70BGGnBbyo5gUHmoT9rewXXzQ?e=jiDvpn

## Word of Thanks

* I sincerely Thank Prof.Kunal Gosh(Founder/VSD) and also Mr. Mayank Kabra (Founder Chipcron Pvt.Ltd) for Teaching and Helping me to complete this course smoothly

## Acknowledgement

* Kunal Ghosh, VSD Corp. Pvt. Ltd.
* Mayank Kabra, Founder Chipcron Pvt.Ltd
* Bhargav DV, MS Colleague
* Pruthvi Parate, MS Colleague
* Nitesh Sharma, M.Tech Colleague
 
## References


* https://circuitdigest.com/microcontroller-projects/interfacing-ir-sensor-module-with-arduino
* https://chat.openai.com/
* https://www.elprocus.com/infrared-ir-sensor-circuit-and-working/
* https://www.electroduino.com/what-is-ir-sensor-module-how-ir-sensor-module-works/
* https://github.com/SakethGajawada/RISCV_GNUReferences  
* https://www.youtube.com/watch?v=fFCuOSerek4&t=302s&ab_channel=EasyHomeMadeProjects
