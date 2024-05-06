# Caltech10CPU
This is the implementation of the Caltech10CPU for EE/CS10a in ABEL. It can be simulated or loaded onto an appropriate CPLD(such as ispMACH4256V CPLD) using the ispLEVER software.

The Caltech10 CPU is an 8-bit Harvard-architecture, accumulator-based CPU. It has an 8K x 16-bit program memory space, a
256 x 8-bit data memory space, and a 256 x 8-bit input/output space. The CPU has an 8-bit accumulator, two 8-bit data addressing
registers (X and S) and a 13-bit program counter. All instructions are a single 16-bit word and almost all instructions
execute in a single clock cycle.

Author: Zachary Pestrikov
