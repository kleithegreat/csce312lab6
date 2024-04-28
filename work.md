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
0x4 is %rsp
| instruction | icode | srcA (rA/0x4/0xf) | srcB (rB/0x4/0xf) | dstE (rB/0x4/0xf) | dstM (rA/0xf) |
| --- | --- | --- | --- | --- | --- |
halt | 0 | 0xf | 0xf | 0xf | 0xf |
nop | 1 | 0xf | 0xf | 0xf | 0xf |
rrmovq rA, rB | 2 | rA | 0xf | rB | 0xf |
irmovq V, rB | 3 | 0xf | 0xf | rB | 0xf |
rmmovq rA, D(rB) | 4 | rA | rB | 0xf | 0xf |
mrmovq D(rB), rA | 5 | 0xf | rB | 0xf | rA |
OPq rA, rB | 6 | rA | rB | rB | 0xf |
jXX Dest | 7 | 0xf | 0xf | 0xf | 0xf |
cmovXX rA, rB | 2 | rA | 0xf | rB if cnd else 0xf | 0xf |
pushq rA | A | 0x4 | 0xf | 0x4 | 0xf |
popq rA | B | 0x4 | 0x4 | 0x4 | rA |

**srcA** =
- rA if icode in {2, 4, 6}
- 0x4 if icode in {A, B}
- 0xf otherwise

**srcB** =
- rB if icode in {4, 5, 6}
- 0x4 if icode = B
- 0xf otherwise

**dstE** =
- rB if icode in {3, 6}
- rB if icode = 2 and Cnd
- 0x4 if icode in {A, B}
- 0xf otherwise

**dstM** =
- rA if icode in {5, B}
- 0xf otherwise
## execute
| instruction | icode | ifun | ALU_A (valA/valC/-8/8/X) | ALU_B (valB/0/X) | alufun (0/1/2/3) | set_cc (0/1) | valE (op1 OP op2) | CC (ZF/SF/OF) | Cnd (0/1) |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| halt | 0 | 0 | 
| nop | 1 | 0 | 
| rrmovq rA, rB | 2 | 0 | 
| irmovq V, rB | 3 | 0 | 
| rmmovq rA, D(rB) | 4 | 0 | 
| mrmovq D(rB), rA | 5 | 0 | 
| OPq rA, rB | 6 | fn |
| jXX Dest | 7 | fn |
| cmovXX rA, rB | 2 | fn |
| pushq rA | A | 0 |
| popq rA | B | 0 |

## memory
| instruction | Mem. read (1: read) | Mem. write (1: write) | Mem. addr (valE/valA) | Mem. Data (valA/valP) |
| --- | --- | --- | --- | --- |
| halt | 0 | 0 | X | X |
| nop | 0 | 0 | X | X |
| rrmovq rA, rB | 0 | 0 | X | X |
| irmovq V, rB | 0 | 0 | X | X |
| rmmovq rA, D(rB) | 0 | 1 | valE | valA |
| mrmovq D(rB), rA | 1 | 0 | valE | X |
| OPq rA, rB | 0 | 0 | X | X |
| jXX Dest | 0 | 0 | X | X |
| cmovXX rA, rB | 0 | 0 | X | X |
| pushq rA | 0 | 1 | valE | valA |
| popq rA | 1 | 0 | valA | X |

## pc update
| instruction | icode | PC update |
| --- | --- | --- |
| halt | 0 | 0 |
| nop | 1 | valP |
| rrmovq rA, rB | 2 | valP |
| irmovq V, rB | 3 | valP |
| rmmovq rA, D(rB) | 4 | valP |
| mrmovq D(rB), rA | 5 | valP |
| OPq rA, rB | 6 | valP |
| jXX Dest | 7 | cnd ? valC : valP |
| cmovXX rA, rB | 2 | valP |
| pushq rA | A | valP |
| popq rA | B | valP |




## new fetch shit
1: read pc
2: start reading from memory using pc
    - initiate counter
    - disable outside changes to pc
...
stop when instruction consumed

new idea: wait 12 cycles before reading new pc
use pc at first read and a counter