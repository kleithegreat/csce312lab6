## fetch
### instruction memory
read 0th byte and assign to byte_0 output

if 0th byte is:
    - 2fn
    - 30
    - 40
    - 50
    - 6fn
    - A0
    - B0
    then read next byte and assign to byte_1 output

if 0th byte is:
    - 30
    - 40
    - 50
    - 7fn
    - 80
    then read next 8 bytes and assign to bytes 2-9 output


## decode/write back

| instruction | srcA (rA/0x4/0xf) | srcB (rB/0x4/0xf) | dstE (rB/0x4/0xf) | dstM (rA/0xf) |
| --- | --- | --- | --- | --- |
halt | 0xf | 0xf | 0xf | 0xf |
nop | 0xf | 0xf | 0xf | 0xf |
rrmovq rA, rB | rA | 0xf | rB | 0xf |
irmovq V, rB | 0xf | 0xf | rB | 0xf |
rmmovq rA, D(rB) | rA | rB | 0xf | 0xf |
mrmovq D(rB), rA | 0xf | rB | 0xf | rA |
OPq rA, rB | rA | rB | rB | 0xf |
jXX Dest | 0xf | 0xf | 0xf | 0xf |
cmovXX rA, rB | rA | 0xf | rB | 0xf |
pushq rA | rA | 0xf | 0xf | 0xf |
popq rA | 0xf | 0xf | rA | 0xf |