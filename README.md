# Simple Instruction Set Architecture (ISA) Assembler and Simulator

This project implements a simple assembler and simulator for a custom Instruction Set Architecture. The assembler converts assembly code into binary machine code, and the simulator executes this machine code.

## Table of Contents
- [Overview](#overview)
- [Instruction Set](#instruction-set)
- [Register Set](#register-set)
- [Using the Assembler](#using-the-assembler)
- [Using the Simulator](#using-the-simulator)
- [File Formats](#file-formats)
- [Error Handling](#error-handling)
- [Examples](#examples)

## Overview

The project consists of two main components:

1. **Assembler (`assembler.py`)**: Converts assembly language instructions into 16-bit binary machine code according to the ISA specifications.
2. **Simulator (`simulator.py`)**: Executes the binary machine code produced by the assembler, simulating the behavior of a simple processor.

## Instruction Set

The ISA supports the following instruction types:

### Type A: 3-register operations
Format: `<opcode(5)>00<reg1(3)><reg2(3)><reg3(3)>`
- `add R1 R2 R3`: R1 = R2 + R3
- `sub R1 R2 R3`: R1 = R2 - R3
- `mul R1 R2 R3`: R1 = R2 * R3
- `xor R1 R2 R3`: R1 = R2 XOR R3
- `or R1 R2 R3`: R1 = R2 OR R3
- `and R1 R2 R3`: R1 = R2 AND R3

### Type B: Register and immediate operations
Format: `<opcode(5)>0<reg1(3)><immediate(7)>`
- `movi R1 $Imm`: R1 = Imm (immediate value)
- `rs R1 $Imm`: R1 = R1 >> Imm (right shift)
- `ls R1 $Imm`: R1 = R1 << Imm (left shift)

### Type C: 2-register operations
Format: `<opcode(5)>00000<reg1(3)><reg2(3)>`
- `movr R1 R2`: R1 = R2 (register to register)
- `div R1 R2`: R1 = R1 / R2 (Division, R0 = quotient, R1 = remainder)
- `not R1 R2`: R1 = ~R2 (bitwise NOT)
- `cmp R1 R2`: Compare R1 and R2, set FLAGS register

### Type D: Memory operations
Format: `<opcode(5)>0<reg1(3)><memory_addr(7)>`
- `ld R1 mem_addr`: R1 = memory[mem_addr]
- `st R1 mem_addr`: memory[mem_addr] = R1

### Type E: Jump operations
Format: `<opcode(5)>0000<memory_addr(7)>`
- `jmp mem_addr`: Unconditional jump to mem_addr
- `jlt mem_addr`: Jump to mem_addr if less than flag is set
- `jgt mem_addr`: Jump to mem_addr if greater than flag is set
- `je mem_addr`: Jump to mem_addr if equal flag is set

### Type F: Halt operation
Format: `<opcode(5)>00000000000`
- `hlt`: Halt the program execution

## Register Set

The ISA includes 8 registers:
- 7 general-purpose registers: R0 to R6
- 1 FLAGS register (R7)

The FLAGS register is used to store flags like overflow, less than, greater than, and equal to.

## Using the Assembler

The assembler reads assembly code from stdin and outputs the corresponding binary machine code to stdout.

```bash
python assembler.py < input_assembly.txt > output_binary.txt
```

### Assembly Code Rules:

1. Variables must be declared at the beginning of the program.
2. Each line should contain a single instruction or variable declaration.
3. Labels can be defined with the format `label: instruction`.
4. The program must end with a single `hlt` instruction.
5. Immediate values should be prefixed with `$` and must be in the range 0 to 127.
6. Memory addresses can be variables or direct addresses (7-bit binary).

## Using the Simulator

The simulator reads binary machine code from stdin and outputs the state of the program counter and registers after each instruction.

```bash
python simulator.py < output_binary.txt > simulation_result.txt
```

The simulator outputs:
- Program counter value (7 bits)
- Values of all registers (R0-R6 and FLAGS) after each instruction (16 bits each)
- The content of memory after program execution

## File Formats

### Assembly File Format

```
var X
var Y
mov R1 $10
mov R2 $20
add R3 R1 R2
st R3 X
hlt
```

### Binary File Format

Each instruction is converted to a 16-bit binary string according to the ISA specification.

## Error Handling

The assembler performs various error checks:
- Variables must be declared at the beginning
- Proper usage of the `hlt` instruction (must be the last instruction)
- Valid instruction names and proper syntax
- Valid register names
- Valid immediate values (0-127)
- Valid memory addresses
- Valid labels

Error messages include the line number and specific error details.

## Examples

### Example 1: Addition Program

**Assembly Code**:
```
var result
mov R1 $10
mov R2 $20
add R3 R1 R2
st R3 result
hlt
```

**Binary Output**:
```
0001000001001010
0001000010010100
0000000011001010
0010100011000000
1101000000000000
```

### Example 2: Program with Labels and Jumps

**Assembly Code**:
```
var x
mov R1 $10
mov R2 $20
loop: add R1 R1 R2
cmp R1 R3
jlt end
sub R1 R1 R2
jmp loop
end: st R1 x
hlt
```

When running the simulator with this binary code, it will simulate the execution of each instruction, displaying the program counter and register values at each step, and finally show the memory content after program termination.
