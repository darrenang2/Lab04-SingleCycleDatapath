## Section 1
I had some issues with getting the test cases to pass as I was trying to wire everything from scratch instead of using tunnels.\
Once I switched to tunnels everything was simpler and I managed to pass all the test cases.

## Section 2
`and $t0 $t1 $t2`\
`addi $t3 $t4 123`\
`lw $t5, 135($t1)`\
`sw $t3, 136($t1)`\
`beq $t3, $zero, -10`
## Assembly
|Instruction           |	Binary Representation                   |	Hex Equivalent  |
|----------------------|------------------------------------------|-------------------|
|`and $t0, $t1, $t2`   |	000000 01001 01010 01000 00000 100100	|   0x012A4024      |
|`addi $t3, $t4, 123`  |	001000 01100 01011 0000000001111011	    |   0x218B007B      |
|`lw $t5, 135($t1)`    |	100011 01001 01101 0000000010000111	    |   0x8D2D0087      |
|`sw $t3, 136($t1)`    |	101011 01001 01011 0000000010001000	    |   0xAD2B0088      |
|`beq $t3, $zero, -10` |	000100 01011 00000 1111111111110110	    |   0x1160FFF6      |

## Tracing

#### **1. `lw $v0, 31($zero)`**  
   $v0 = 0x00000056

---

#### **2. `add $v1, $v0, $v0`**  
   $v1 = 0x00000056 + 0x00000056
       = 0x000000AC

---

#### **3. `sw $v1, 132($zero)`**  
   Mem[132] = $v1 = 0x000000AC

---

#### **4. `sub $a0, $v1, $v0`**  
   $a0 = $v1 - $v0
       = 0x000000AC - 0x00000056
       = 0x00000056

---

#### **5. `addi $a1, $v1, 12`**  
   $a1 = $v1 + 12
       = 0x000000AC + 0x0000000C
       = 0x000000B8

---

#### **6. `and $a2, $a1, $v1`**  
   $a2 = $a1 & $v1
       = 0x000000B8 & 0x000000AC
       = 0x000000A8

---

#### **7. `or $a3, $a2, $v0`**  
   $a3 = $a2 | $v0
       = 0x000000A8 | 0x00000056
       = 0x000000FE

---

#### **8. `nor $t0, $a2, $v0`**  
   $t0 = ~($a2 | $v0)
       = ~(0x000000A8 | 0x00000056)
       = ~(0x000000FE)
       = 0xFFFFFF01

---

#### **9. `slt $a2, $a1, $a0`**  
   if ($a1 < $a0) then $a2 = 1 else $a2 = 0
   $a1 = 0x000000B8
   $a0 = 0x00000056

   $a1 > $a0, so $a2 = 0

---

#### **10. `beq $a2, $zero, -8`**  
   if ($a2 == 0) then branch back by 8 instructions
   - `$a2 == 0`, so the branch is **taken**.
   - **Branch offset** is `-8`, meaning it jumps back 8 instructions (2 instructions before `slt`).

---

#### **11. `lw $t0, 132($zero)`** (After the branch)  
   $t0 = Mem[132]
       = 0x000000AC

---

### **Final Register and Memory State**
| Register | Value |
|----------|--------|
| `$v0` | `0x00000056` |
| `$v1` | `0x000000AC` |
| `$a0` | `0x00000056` |
| `$a1` | `0x000000B8` |
| `$a2` | `0x00000000` |
| `$a3` | `0x000000FE` |
| `$t0` | `0x000000AC` |

| Memory Address | Value |
|---------------|--------|
| `31` | `0x00000056` |
| `132` | `0x000000AC` |