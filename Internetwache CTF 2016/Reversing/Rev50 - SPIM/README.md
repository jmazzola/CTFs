# rev50 - 'SPIM'

>   My friend keeps telling me, that real hackers speak assembly fluently.
>   Are you a real hacker? Decode this string: "IVyN5U3X)ZUMYCs"

>   Attachment: [rev50.zip](./rev50.zip)

Once we open up the .zip, we're greeted with a [README.txt](./README.txt) that looks like MIPS assembly. (Who would have guessed without that clever challenge name~)

I personally hate MIPS and everything it stands for. However, it's fairly easy to read and understand what this program is doing.

I tidied up the assembly to be a bit more readable and understandable for me to reverse like so:

```
program:
[00400000] 8fa40000  lw $4, 0($29)            ; 183: lw $a0 0($sp) # argc 
[00400004] 27a50004  addiu $5, $29, 4         ; 184: addiu $a1 $sp 4 # argv 
[00400008] 24a60004  addiu $6, $5, 4          ; 185: addiu $a2 $a1 4 # envp 
[0040000c] 00041080  sll $2, $4, 2            ; 186: sll $v0 $a0 2 
[00400010] 00c23021  addu $6, $6, $2          ; 187: addu $a2 $a2 $v0 
[00400014] 0c100009  jal 0x00400024           ; 188: jal main 
[00400018] 00000000  nop                      ; 189: nop 
[0040001c] 3402000a  ori $2, $0, 10           ; 191: li $v0 10 
[00400020] 0000000c  syscall                  ; 192: syscall 

main:
[00400024] 3c081001  lui $8, 4097 [flag]      ; 7: la $t0, flag 
[00400028] 00004821  addu $9, $0, $0          ; 8: move $t1, $0 
[0040002c] 3401000f  ori $1, $0, 15           ; 11: sgt $t2, $t1, 15 
[00400030] 0029502a  slt $10, $1, $9          
[00400034] 34010001  ori $1, $0, 1            ; 12: beq $t2, 1, exit 
[00400038] 102a0007  beq $1, $10, 28 
[0040003c] 01095020  add $10, $8, $9          ; 14: add $t2, $t0, $t1 
[00400040] 81440000  lb $4, 0($10)            ; 15: lb $a0, ($t2) 
[00400044] 00892026  xor $4, $4, $9           ; 16: xor $a0, $a0, $t1 
[00400048] a1440000  sb $4, 0($10)            ; 17: sb $a0, 0($t2) 
[0040004c] 21290001  addi $9, $9, 1           ; 19: add $t1, $t1, 1 
[00400050] 0810000b  j 0x0040002c [for]       ; 20: j for 
[00400054] 00082021  addu $4, $0, $8          ; 24: move $a0, $t0 
[00400058] 0c100019  jal 0x00400064           ; 25: jal printstring 
[0040005c] 3402000a  ori $2, $0, 10           ; 26: li $v0, 10 
[00400060] 0000000c  syscall                  ; 27: syscall (exit)

printstring:
[00400064] 34020004  ori $2, $0, 4            ; 30: li $v0, 4 
[00400068] 0000000c  syscall                  ; 31: syscall (print_string)
[0040006c] 03e00008  jr $31                   ; 32: jr $ra 
```

In layman terms, in our **main** function, we're taking the **flag** which in our case is the string we're trying to decrypt:

```
IVyN5U3X)ZUMYCs
```

and doing an adorable for loop to 15, as stated here: (which is the same strlen as our inputted string!~)

```
// var t0 = "IVyN5U3X)ZUMYCs"
[00400024] 3c081001  lui $8, 4097 [flag]      ; 7: la $t0, flag 
// $t1 (i) = count of loop
[00400028] 00004821  addu $9, $0, $0          ; 8: move $t1, $0 
// var t2 = (i > 15) ? 1 : 0
[0040002c] 3401000f  ori $1, $0, 15           ; 11: sgt $t2, $t1, 15 
[00400030] 0029502a  slt $10, $1, $9         
// if(t2 == 1) exit we're done with the loop 
[00400034] 34010001  ori $1, $0, 1            ; 12: beq $t2, 1, exit 
[00400038] 102a0007  beq $1, $10, 28 
// t2 = t0 + i
[0040003c] 01095020  add $10, $8, $9          ; 14: add $t2, $t0, $t1 
```

and in this for loop we're literally taking each element in the string and xoring with the current loop count.

```
// var $a0 = flag[i];
[00400040] 81440000  lb $4, 0($10)            ; 15: lb $a0, ($t2) 
// $a0 ^= $t1 (i)
[00400044] 00892026  xor $4, $4, $9           ; 16: xor $a0, $a0, $t1
[00400048] a1440000  sb $4, 0($10)            ; 17: sb $a0, 0($t2) 
// i++
[0040004c] 21290001  addi $9, $9, 1           ; 19: add $t1, $t1, 1 
```

So since at the time, I didn't have Python installed, I made this quick little C# script to spew out the answer.

```csharp
static void Main(string[] args)
{
    string enc = "IVyN5U3X)ZUMYCs";
    byte[] encBytes = Encoding.ASCII.GetBytes(enc);

    for (int i = 0; i < 15; i++)
    {
        encBytes[i] ^= (byte)i;
    }

    Console.WriteLine(Encoding.ASCII.GetString(encBytes));
}
```

And we get our flag: 

```
IW{M1P5_!S_FUN}
```

:3