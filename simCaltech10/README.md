# Caltech10 Simulator

`simCaltech10.exe` runs and debugs Caltech10 machine-code programs on Windows.
It does not simulate the ABEL source files. See the [simulator manual](simCaltech10Manual.pdf)
for commands and the [project README](../README.md#simulate-and-test) for a GCD walkthrough.
The simulator was built by Glen George.

[`gcdEuclidCaltech10.obj`](gcdEuclidCaltech10.obj) is an annotated Euclidean GCD routine. Its inputs are
stored at data-memory addresses `0x00` and `0x01`; the result is returned in the
accumulator.
