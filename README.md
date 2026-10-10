# Caltech10 CPU

An 8-bit accumulator CPU implemented in **ABEL** for the **Lattice ispMACH4256V CPLD**. The goal is to translate the Caltech10 instruction set into a hardware datapath: Boolean logic, explicit carry chains, address generation, and a control state machine coordinating memory access and subroutine execution.

The project connects processor hardware with assembly programming through my companion [Caltech10 Assembler](https://github.com/Z4KH/Caltech10Assembler), which generates machine code for this CPU.

## Architecture

| Property | Design |
| --- | --- |
| Organization | Harvard architecture; separate program and data buses |
| Datapath | 8-bit accumulator and status register |
| Address registers | 8-bit `X` index register and `S` stack pointer |
| Program counter | 13 bits; addresses 8K external 16-bit instruction words |
| Data addressing | 256 external byte locations |
| Instructions | Fixed 16-bit words; ordinary instructions execute in one cycle |
| Subroutines | Four-cycle `CALL`, three-cycle `RTS` |

The control unit latches and decodes instructions from external program memory, selects operands, and coordinates four functional blocks:

- **ALU:** a configurable Boolean function block feeds an explicit ripple-carry adder. Arithmetic, logic, and comparison operations produce carry, overflow, sign, and zero flags for conditional branches.
- **Data access:** direct and `X`/`S` indexed addressing combine a register with an 8-bit offset. Pre/post increment and decrement support sequential access and stack operations.
- **Program access:** the counter advances sequentially or selects an absolute target or signed relative offset. Branches execute the following instruction as a delay slot.
- **Control:** instruction decoding drives the datapath and read/write signals. A one-hot state machine sequences `CALL` and `RTS`, saving and restoring the return address as two bytes through the `S` stack pointer.

The top-level module connects these blocks and exposes the accumulator, flags, and address registers for inspection. See the [CPU reference](docs/CPUDocs.pdf) for instruction encodings and architectural details.

## Build and run on hardware

Use **ispLEVER with ABEL support**, an **ispMACH4256V CPLD**, and a compatible programming connection. Execution also requires external program/data memory and board connections for the buses, clock, and reset.

1. Create an ABEL project in ispLEVER targeting your device and package. Add the five files in `src/` and select `CPU` as the top-level module.
2. Assign pins for your board, compile and fit the design, and generate the device programming file. Review the fitter and timing reports.
3. Program the CPLD using the Lattice programming software and procedure appropriate to your board and cable. Load the assembled program into external program memory, then apply reset and clock to execute it.

See [Lattice's ispLEVER documentation](https://www.latticesemi.com/products/designsoftwareandip/fpgaandlds/ispleverclassic) for tool setup and the device implementation flow.

## Simulate and test

The bundled Windows executable is an **instruction-set simulator** for running and debugging assembly programs, with register/memory inspection, single stepping, and breakpoints. Simulation of the ABEL implementation is a separate step in the hardware toolchain.

From the repository root, launch it in PowerShell:

```powershell
cd simCaltech10
.\simCaltech10.exe
```

For a manual smoke test using the included Euclidean GCD routine:

1. Type `L`, then enter `gcdEuclidCaltech10.obj` at the filename prompt.
2. Type `!D` to select data memory. Type `0/30` followed by Enter to store hexadecimal `30` (48) at address `00`; type `1/12` followed by Enter to store hexadecimal `12` (18) at address `01`.
3. Type `0S` to run the routine at address `0000`. On return, the accumulator should contain `06`, the GCD of 48 and 18.

Commands are keystrokes; numeric arguments are hexadecimal. Use `R` to inspect registers, `T` to single-step, `H` for help, and `Q` to quit. See the [simulator manual](simCaltech10/simCaltech10Manual.pdf) for the full reference. For your own programs, use the [companion assembler](https://github.com/Z4KH/Caltech10Assembler) and load the resulting `.obj` file with `L`.

## Repository structure

```text
src/
  CPU.abl          Top-level module and bus connections
  ALU.abl          Accumulator, Boolean logic, adder, and flags
  CU.abl           Instruction decoder and call/return state machine
  DAU.abl          Data addressing and X/S register updates
  PAU.abl          Program counter and branch address calculation
docs/
  CPUDocs.pdf      Architecture and instruction-set reference
simCaltech10/
  simCaltech10.exe         Windows instruction-set simulator
  simCaltech10Manual.pdf   Simulator command reference
  gcdEuclidCaltech10.obj   Annotated GCD machine-code example
  README.md               Simulator overview
```

Implemented by **Zachary Pestrikov**. Original CPU specification, simulator, and top-level template by **Glen George**.
