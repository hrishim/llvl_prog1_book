# Condition Flags

In [Arm Registers and Execution State](#Arm-Registers-and-Execution-State) we introduced the condition flags. In the previous section, we have already used arithmetic instructions like **cmp** that set the condition flags and instructions like b.gt that makes use of condition flags. In this section we introduce more such instructions and then work through a series of examples. We will also explain how the condition flags are used to implement conditional execution of instructions.

Condition flags are a set of bits that store state information about a previous operation. There are four condition flags:

| Flag | Name | Description |
|---- | ---- | --- |
| N | Negative flag |  Set to 1 if the result of the last flag-setting instruction was negative  |
| Z | Zero flag | Set to 1 if the result of the last flag-setting instruction was zero, and to 0 otherwise |
| C | Carry flag | Set to 1 if the last flag-setting instruction resulted in a carry condition, for example an unsigned overflow on an addition |
| V | oVerflow flag | Set to 1 if the last flag-setting instruction resulted in an overflow condition, for example a signed overflow on an addition |

Each flag is a 1-bit value, so that there can only be two valid values:  0 (clear) or 1 (set). These flags indicate simple properties such as whether or not the result was negative, and can also be used in various combinations to identify conditions such as "less than or equal to".  

The N flag represents the sign of the result of the previous operation. If say the operation was an addition (or subtraction, mutiplication, division etc) and the result was a negative number then this flag is set. If the result was a positive number then this flag is cleared.

The Z flag, when set, indicates that the output of the previous instruction was zero. 

The C flag is called the carry flag. This flag is set if an arithmetic operation results in an arithmetic *carry* or *borrow*. For example an unsigned overflow on an addition and can be used to perform unsigned arithmetic operations.

The V flag indicates that the previous operation resulted in an arithmetic *overflow*. For example a signed overflow on an addition. The C flag is set if the result of an unsigned operation overflows the result register. The V flag operates in the same way as the C flag, but for signed operations. For example, 0x7fffffff is the largest positive two's complement integer that can be represented in 32 bits, so 0x7fffffff + 0x7fffffff triggers a signed overflow, but not an unsigned overflow (or carry): the result, 0xfffffffe, is correct if interpreted as an unsigned quantity, but represents a negative value (-2) if interpreted as a signed quantity.

**Flag setting instructions**:

The condition flags are set by dedicated comparison instruction like CMP, CMN and TST. These comparison instructions only modify the flags and have no other effect. There are also certain Arithmetic and Logical instructions with S suffix (indicating that the instruction sets flags) that can modify the condition flags. For example: ADDS, SUBS, ADCS, SBCS, ANDS, and BICS.

Integer instructions like ADDS, SUBS, ADCS, SBCS, CMP, CMN can affect all the four condition flags whereas logical instructions like ANDS, BICS and TST can set only N and Z flags.

Let us now look in more detail on how the condition flag bits for each operation.

**Addition operation**:

For addition operation instructions like ADDS, ADCS:
- N bit is set when MSB of result is 1
- Z bit is set when result is 0
- C bit is set when addition results in carry
- V bit is set when:
  - Positive + Positive = Negative
  - Negative + Negative = Positive

**Subtraction operation**:

For subtraction operation instructions like SUBS, SBCS:
- N bit is set when MSB of result is 1
- Z bit is set when result is 0
- C bit is set when subtraction results in borrow
- V bit is set when:
   - Negative - Positive = Positive
   - Positive - Negative = Negative

**Logical operation**:

For logical operation instructions like ANDS, BICS:
- N bit is set when MSB of result is 1
- Z bit is set when result is 0
- C bit is always cleared to 0 
- V bit is always cleared to 0 

Let us now go through some examples to determine the NZCV values after a flag setting instruction is executed: 

Example 1:

| Operation | Operand 1 | Operand 2 | Result |	
|---- | ---- | ---- | ---- |
| ADDS | 0x50000000 | 0x50000000 | 0xA0000000 |
 
 <details>
  <summary>Click to see the solution after you solve the problem</summary>

```
 N = 1, Z = 0, C = 0, V = 1
```  
</details>

Example 2:

| Operation | Operand 1 | Operand 2 | Result |	
|---- | ---- | ---- | ---- |
| Addition    | 0x80000000 | 0x80000000 |	0x00000000 |
 
 <details>
  <summary>Click to see the solution after you solve the problem</summary>

```
 N = 0, Z = 1, C = 1, V = 1
```  
</details>

Example 3:

| Operation | Operand 1 | Operand 2 | Result |	
|---- | ---- | ---- | ---- |
| Addition    | 0x80000000 | 0x90000000 |	0x10000000 |
 
 <details>
  <summary>Click to see the solution after you solve the problem</summary>

```
 N = 0, Z = 0, C = 1, V = 1
```  
</details>

Example 4:

| Operation | Operand 1 | Operand 2 | Result |	
|---- | ---- | ---- | ---- |
| Subtraction | 0x00001678 | 0x00001000 |	0x00000678 |
 
 <details>
  <summary>Click to see the solution after you solve the problem</summary>

```
 N = 0, Z = 0, C = 1, V = 0
```  
</details>

Example 5:

| Operation | Operand 1 | Operand 2 | Result |	
|---- | ---- | ---- | ---- |
| Subtraction | 0x00000006 | 0x00000007 |	0xFFFFFFFF |
 
 <details>
  <summary>Click to see the solution after you solve the problem</summary>

```
 N = 1, Z = 0, C = 0, V = 0
```  
</details>

Example 6:

| Operation | Operand 1 | Operand 2 | Result |	
|---- | ---- | ---- | ---- |
| Subtraction | 0xFFFFFFFF | 0xFFFFFFFB |	0x00000004 |
 
 <details>
  <summary>Click to see the solution after you solve the problem</summary>

```
 N = 0, Z = 0, C = 1, V = 0
```  
</details>

Example 7:

| Operation | Operand 1 | Operand 2 | Result |	
|---- | ---- | ---- | ---- |
| Subtraction | 0xD0000004 | 0xD0000003 |	0x00000001 |
 
 <details>
  <summary>Click to see the solution after you solve the problem</summary>

```
 N = 0, Z = 0, C = 1, V = 0
```  
</details>

Example 8:

| Operation | Operand 1 | Operand 2 | Result |	
|---- | ---- | ---- | ---- |
| Subtraction | 0x70000000 | 0xF0000000 |	0x80000000 |
 
 <details>
  <summary>Click to see the solution after you solve the problem</summary>

```
 N = 1, Z = 0, C = 0, V = 1
```  
</details>

Example 9:

| Operation | Operand 1 | Operand 2 | Result |	
|---- | ---- | ---- | ---- |
| Subtraction | 0xB0000000 | 0xB0000000 |	0x00000000 |
 
 <details>
  <summary>Click to see the solution after you solve the problem</summary>

```
 N = 0, Z = 1, C = 1, V = 0
```  
</details>

We have worked out how the condition flags are set. Let us now see how does that enable conditional execution of code by introducing instructions that read the condition flags.

**Program flow control instructions**

Program flow instructions gives the ability to make decisions on what code to execute next. The instruction set enables conditional execution of program flow control branch instructions. The branch instructions can be broadly categorized to two types:
- Unconditional branches: Unconditional branches do not consider the NZCV flags and they can branch backward or forward up to 128MB from the current program counter location.
- Conditional branches: Conditional branches use the NZCV condition flags to decide whether branch operation should be performed or not. Most conditional branches have a range of +/-1MB.

The instruction set provides a number of different kinds of branch instructions. Majority of the branch instructions use relative addressing. That is, they branch to an offset from the current address and are called relative branches. An example is B.cond instruction, where 'cond' is one of the fourteen possible condition suffixes. In effect, attaching one of the condition codes to the B instruction causes it to branch to specified offset if the condition(cond) is true. Otherwise, it does nothing, and is essentially a nop. The following table lists the available condition codes, their meanings, and the flags that are tested:

| Mnemonic | Meaning (integer)| Condition Flags | 
| ------ | ------ | ------ |
| eq | Equal | Z == 1|
| ne | Not Equal | Z == 0 |
| cs or hs | Carry Set | C == 1 |
| cc or lo | Carry clear | C == 0 |
| mi | Minus, negative | N == 1 |
| pl | Plus, positive or zero | N == 0 |
| vs | Overflow | V == 1 |
| vc | No Overflow | V == 0 | 
| hi | Unsigned higher | C == 1 && Z == 0 |
| ls | Unsigned lower or same | !(C == 1 && Z == 0) |
| ge | Signed greater than or equal | N == V |
| lt | Signed less than | N != V |
| gt | Signed greater than | Z == 0 && N == V |
| le | Signed lessthan or equal | !(Z == 0 && N == V) |

The first few  condition codes would be quite obvious as they test individual flags, but the others rely on specific combinations of flags. However, the understanding of exact flags that would be used for each condition code will be rarely required.

In addition to the above PC-relative instructions, the instruction set includes absolute branches. The BR Xn instruction performs an absolute branch to the address in Xn.

The instruction set includes some special conditional branches so that explicit comparison instructions are not needed.
- CBZ Rt, label  : Compare and branch if zero
- CBNZ Rt, label : Compare and branch if not zero
These instructions compare the source register, either 32-bit or 64-bit, with zero and then conditionally perform a branch. The branch offset has a range of +/- 1MB. These instructions do not read or write the condition code flags (NZCV).

There are two similar test and branch instructions 
- TBZ Rt, bit, label  : Test and branch if Rt<bit> zero
- TBNZ Rt, bit, label : Test and branch if Rt<bit> is not zero
These instructions test the bit in the source register at the bit position specified by the immediate and conditionally branch depending on whether the bit is set or clear. The branch offset has a range of +/- 32kB. As with CBZ/CBNZ, these instructions do not read or write the condition code flags (NZCV).

The table below summarizes the supported branch instructions along with brief explanation of the operation:

| Instruction | Description |
| ---------   | ---------   | 
| B (offset) | Program relative branch forward or back 128MB. A conditional version, for example B.EQ, has a 1MB range. |
| BR Xn | Absolute branch to address in Xn |
| CBZ Rt, label | Compare and branch if zero. If Rt is zero, branch forward or back up to 1MB. |
| CBNZ Rt, label | Compare and branch if non-zero. If Rt is not zero, branch forward or back up to 1MB. |
| TBNZ Rt, bit, label | Test and branch if zero. Branch forward or back up to 32kB. |
| TBNZ Rt, bit, label | Test and branch if non-zero. Branch forward or back up to 32kB. |

**Conditional select**

The instruction set also provides instructions to perforn conditional select with optional increment, negate, or invert. These can be used to conditionally select between one source register and a second incremented, negated, inverted, or unmodified source register. 

The table below summarizes the conditional select instructions:

| Instruction | Description |
| ------ | ------ |
| CSEL  | Select between two registers based on a condition.  |
| CSINC | Select between two registers based on a condition. Result would be value of the first source register or the second source register incremented by one.|
| CSINV | Select between two registers based on a condition. Result would be value of the first source register or the inverted second source register.|
| CSNEG | Select between two registers based on a condition. Result would be value of the first source register or the negated second source register.|

One typical use of these instructions is conditional counting. For example:
CSINC X0, X1, X0, NE 
The above instruction sets the return register X0 to X1 if Zero flag clear, otherwise it increments X0.

This class of instructions can also be used to avoid the use of branches. 

NOTE: The following example can be later moved to C programming section

For example, consider the simple C code:

```
if (i == 0) x = x + 5; else x = x - 2;

```

This might be written in assembly using following code:

```
CMP w0, #0 // if (i == 0)
SUB w2, w1, #2 // x = x - 2
ADD w1, w1, #5 // x = x + 5
CSEL w1, w1, w2, EQ // select between the two results
```
The instruction set also provides following additional instructions that use condition codes:
- Condition Set (CSET/CSETM) : Conditionally select between 0 and 1 (CSET) or 0 and -1 (CSETM). Used, for example, to set the condition flags as a boolean value or mask in a general register.
- Conditional compare (CCMP/CCMN):  Sets the condition flags to the result of a comparison if the original condition is true. If not true, the conditional flags are set to a specified condition flag state. The conditional compare instruction is very useful for expressing nested or compound comparisons

**Add/Subtract with Carry**
In addition to conditional instructions described above, the Carry bit of the condition code can also be used to extend add and subtract operations. These instructions are unconditionally executed but use the condition flags as an extra input to the instruction. For example: ADC(add with carry), SBC(Subtract with carry). 

The operations ADC and SBC perform additions and subtractions that also use the carry condition flag as an input as shown below.

```
ADC{S}: Rd = Rn + Rm + C
SBC{S}: Rd = Rn - Rm - 1 + C
```
These instructions can be used for performing multi-precision arithmetic and checksums.



## What we learned this chapter
   - Condition flags and their meaning
   - Instructions that set condition code - CMP, TST, ADDS, SUBS, ADCS, SBCS, ANDS, BICS
   - Program flow control instructions
   - Instructions that make use of conditional flags 

---
