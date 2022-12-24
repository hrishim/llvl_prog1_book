# Condition Flags

In [ARM Registers and Execution State](#ARM-Registers-and-Execution-State) we introduced the condition flags. In the previous section, we have already used arithmetic instructions like **cmp** that set the condition flags and instructions like **b.gt** that makes use of condition flags. In this section we introduce more such instructions and then work through a series of examples. We will also explain how the condition flags are used to implement conditional execution of instructions.

Condition flags are a set of bits that store state information about a previous operation. There are four condition flags:

| Flag | Name | Description |
|---- | ---- | --- |
| N | Negative flag |  Set to 1 if the result of the last flag-setting instruction was negative  |
| Z | Zero flag | Set to 1 if the result of the last flag-setting instruction was zero, and to 0 otherwise |
| C | Carry flag | Set to 1 if the last flag-setting instruction resulted in a carry condition, for example an unsigned overflow on an addition |
| V | oVerflow flag | Set to 1 if the last flag-setting instruction resulted in an overflow condition, for example a signed overflow on an addition |

Each flag is a 1-bit value, so that there can only be two valid values:  0 (clear) or 1 (set). These flags indicate simple properties such as whether or not the result was negative, and can also be used in various combinations to identify conditions such as "less than or equal to".  

The N flag represents the sign of the result of the previous operation. If say the operation was an addition (or subtraction, multiplication, division etc) and the result was a negative number then this flag is set. If the result was a positive number then this flag is cleared.

The Z flag, when set, indicates that the output of the previous instruction was zero. 

The C flag is called the carry flag. This flag is set if an arithmetic operation results in an arithmetic *carry* or *borrow*. It is set if the result of an unsigned operation overflows the result register.

The V flag indicates that the previous operation resulted in an arithmetic *overflow*. For example a signed overflow on an addition. It operates in the same way as the C flag, but for signed operations. For example, 0x7fffffff is the largest positive two's complement integer that can be represented in 32 bits, so 0x7fffffff + 0x7fffffff triggers a signed overflow, but not an unsigned overflow (or carry). The result, 0xfffffffe, is correct if interpreted as an unsigned quantity, but represents a negative value (-2) if interpreted as a signed quantity.

## Flag setting instructions

The condition flags are set by dedicated comparison instruction like CMP, CMN and TST. These comparison instructions only modify the flags and have no other effect. There are also certain Arithmetic and Logical instructions with "S" suffix that can modify the condition flags. For example: ADDS, SUBS, ADCS, SBCS, ANDS, and BICS.

Integer instructions like ADDS, SUBS, ADCS, SBCS, CMP, CMN can affect all the four condition flags whereas logical instructions like ANDS, BICS and TST can set only N and Z flags.

The section titled **"Alphabetical list of A64 base instructions"** in the [ARM Architecture Reference Manual ARMv8, for ARMv8-A architecture profile](https://developer.arm.com/documentation/ddi0487/ga/?lang=en) provides detailed information about each instruction. We provide only a overview of these instructions. Readers are encouraged to consult the manual for details of each instruction.


Let us now look in more detail on how the condition flag bits are set for each operation.

**Addition operation**:

For addition operation instructions like ADDS, ADCS:
- N bit is set when MSB of result is 1
- Z bit is set when result is 0
- C bit is set when addition results in carry
- V bit is set when:
  - Positive + Positive = Negative
  - Negative + Negative = Positive

ADDS is an add instruction that will add two operands and also set appropriate flags. Like many integer instructions this one also has multiple forms. Two commonly used variants are -

Immediate (`ADDS <Xd>, <Xn|SP>, #<imm>{, <shift>}`)  and the 
Shifted register (`ADDS <Xd>, <Xn>, <Xm>{, <shift> #<amount>}`). Both these types of ADDS instructions can operate on *X* or *W* registers. Both forms can be used without any shifting to add two operands and store the result, and also set the condition flags. The assembly code syntax for ADDS is identical to the ADD instruction we have used earlier - just replace ADD with ADDS.

The ADCS instruction has only one form - `ADCS <Xd>, <Xn>, <Xm>`. Of course, it can operate on either *X* or *W* registers. The instruction adds the contents of its two input operands and the *Carry flag*, and writes the results to the destination register. It also updates the condition flags based on the result.


**Subtraction operation**:

For subtraction instructions like SUBS, SBCS, and CMP the condition flags are set as follows:
- N bit is set when MSB of result is 1
- Z bit is set when result is 0
- C bit is set when subtraction results in borrow
- V bit is set when:
   - Negative - Positive = Positive
   - Positive - Negative = Negative

SUBS has forms similar to ADDS. This instruction subtracts its second operand from its first and writes the result, and also sets the condition flags.

CMP is another instruction that can be used in assembly programs. It is actually an alias to the corresponding form of SUBS instruction. This instruction subtracts the value of its second operand from its first operand and sets condition flags based on the result. The result itself is discarded and not stored in any register.

SBCS instruction has only one form - `SBCS <Xd>, <Xn>, <Xm>`. It subtracts a register value and the value NOT(Carry flag) from another register value and writes the result to the destination register. It also sets the condition flags. The SBC instruction is a non-flag-setting equivalent of SBCS.

**Logical operation**:

For logical operation instructions like ANDS, BICS:
- N bit is set when MSB of result is 1
- Z bit is set when result is 0
- C bit is always cleared to 0 
- V bit is always cleared to 0 

Here is a quiz to test your understanding of flags. You may want to review the section on binary arithmetic and the contents above before taking the quiz.

{{#quiz ./quizzes/condition_flags.toml}}


<!--

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
-->

<!--
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
-->

## Program flow control instructions

Branch instructions in the ISA  gives programmers the ability to decide what code to execute next. These instructions can be broadly categorized to two types:
- Unconditional branches:  They can branch to code backward or forward up to 128MB from the current program counter location. The state of the condition flags has no impact on whether the instruction will branch.
- Conditional branches: Conditional branches use the NZCV condition flags to decide whether branch operation should be performed or not. Most conditional branches have a range of +/-1MB.

Majority of the branch instructions use relative addressing. That is, they branch to an offset from the current address and are called relative branches. An example is the **B.cond** instruction, where 'cond' is one of the fourteen possible condition suffixes. In effect, attaching one of the condition codes to the B instruction causes it to branch to specified offset if the condition (cond) is true. Otherwise, the instruction does nothing, and is essentially a NOP. The following table lists the available condition codes, their meanings, and the flags that are tested:

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

The first few condition codes would be quite obvious as they test individual flags, but the others rely on specific combinations of flags. In addition to the above PC-relative instructions, the instruction set includes absolute branches. The BR Xn instruction performs an absolute branch to the address in Xn.

Generally *conditional* branch instructions are used after previous flag-setting instructions. The branch is either taken or not-taken based on previous operations. However, the ISA also includes some special conditional branches that do not rely on the state set by previous instructions.
- CBZ Rt, label  : Compare and branch if zero
- CBNZ Rt, label : Compare and branch if not zero

The above instructions compare the source register, either 32-bit or 64-bit, with zero and then conditionally perform a branch. The branch offset has a range of +/- 1MB. However, they do not affect the condition code flags (NZCV).

There are two similar test and branch instructions 
- TBZ Rt, bit, label  : Test and branch if Rt<bit> zero
- TBNZ Rt, bit, label : Test and branch if Rt<bit> is not zero

These instructions test the bit in the source register at the bit position specified by the immediate and conditionally branch depending on whether the bit is set or clear. The branch offset has a range of +/- 32kB. As with CBZ/CBNZ, these instructions do not affect the condition code flags (NZCV).

The table below summarizes the supported branch instructions along with brief explanation of the operation:

| Instruction | Description |
| ---------   | ---------   | 
| B (offset) | Program relative branch forward or back 128MB. A conditional version, for example B.EQ, has a 1MB range. |
| BR Xn | Absolute branch to address in Xn |
| CBZ Rt, label | Compare and branch if zero. If Rt is zero, branch forward or back up to 1MB. |
| CBNZ Rt, label | Compare and branch if non-zero. If Rt is not zero, branch forward or back up to 1MB. |
| TBNZ Rt, bit, label | Test and branch if zero. Branch forward or back up to 32kB. |
| TBNZ Rt, bit, label | Test and branch if non-zero. Branch forward or back up to 32kB. |

## Other conditional instructions

There are four instructions that can conditionally select (between two values), increment, negate or invert values. The table below lists these operations:

| Instruction | Description |
| ------ | ------ |
| CSEL  | Select between two registers based on a condition.  |
| CSINC | Select between two registers based on a condition. Result would be value of the first source register or the second source register incremented by one.|
| CSINV | Select between two registers based on a condition. Result would be value of the first source register or the inverted second source register.|
| CSNEG | Select between two registers based on a condition. Result would be value of the first source register or the negated second source register.|

One typical use of these instructions is conditional counting. For example:

```armasm 
CSINC X0, X1, X0, NE 
```

The above instruction sets the return register X0 to X1 if the Zero flag is clear, otherwise it increments X0. This class of instructions can also be used to avoid the use of branches. For example, consider the code below written in the C programming language:

```c
if (i == 0) { 
  x = x + 5; 
} else { 
  x = x - 2;
}
```

We will see C programming in more detail in later sections. The above code is fairly simple. There are two variables named `x` and `i`. For this example, we can think of variables as registers. If the value of `i` is 0 then `x` is increased by 5. If not, then `x` is decreased by 2.

This might be written in assembly using following code:

```armasm
CMP w0, #0 // if (i == 0)
SUB w2, w1, #2 // x = x - 2
ADD w1, w1, #5 // x = x + 5
CSEL w1, w1, w2, EQ // select between the two results
```

Here are some additional conditional operations from the ISA. Readers can refer to the ARM ARM for more details about them.

- Condition Set (CSET/CSETM) : Conditionally select between 0 and 1 (CSET) or 0 and -1 (CSETM). Used, for example, to set the condition flags as a boolean value or mask in a general register.
- Conditional compare (CCMP/CCMN):  Sets the condition flags to the result of a comparison if the original condition is true. If not true, the conditional flags are set to a specified condition flag state. The conditional compare instruction is very useful for expressing nested or compound comparisons


## What we learnt this chapter
   - Condition flags and their meaning
   - Instructions that set condition code - CMP, TST, ADDS, SUBS, ADCS, SBCS, ANDS, BICS
   - Program flow control instructions
   - Instructions that make use of conditional flags 

---
