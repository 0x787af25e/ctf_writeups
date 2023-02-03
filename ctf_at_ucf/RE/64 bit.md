# 64bit

_The flag is the key._

_NON-STANDARD FLAG FORMAT: signed integer_

This challenge is worth 25 points. So far, all the previous challenges did not require much understanding of code - this was the first that I took a while to understand.

Running the 64 bit ELF executable, we find that there is a prompt for the password, which is likely to be a signed integer.

Decompilation with Ghidra gave very unexpected results! The `encrypt` function did do something according to the disassembled results, but Ghidra thought that it did nothing. There was also an `extraout_EAX` variable which did not seem very natural. Either way, the program would take in an integer, encrypt it, and compare it to `0xdeadbeef`. 

```
int main(void) {
  int extraout_EAX;
  int __edflag;
  uint local_10 [2];
  
  local_10[0] = 0;
  puts("enter key:");
                    /* %d */
  __edflag = (int)local_10;
  __isoc99_scanf(&DAT_0040065f);
  encrypt((char *)(ulong)local_10[0],__edflag);
  if (extraout_EAX == L'\xdeadbeef') {
    puts("win :)");
  }
  else {
    puts("try again ");
  }
  return 0;
}
```
```
void encrypt(char *__block,int __edflag)

{
  return;
}
```

[This video](https://www.youtube.com/watch?v=9Fo99X2YNKA) provided a hint about what was going on. I suppose that calling convention was not followed by the compiler, since `eax` was shown to be a 4 byte integer in the diassembly results. In any case, I decided to use this as an opportunity to learn the very basics of x86 assembly, and start by attacking the disassembled result directly. The slides created for UC Berkeley's CS 161 Computer Security class provided a solid explanation of calling convention, complete with the stack visualised.

```
pwndbg> disass main
Dump of assembler code for function main:
   0x0000000000400567 <+0>:     push   rbp
   0x0000000000400568 <+1>:     mov    rbp,rsp
   0x000000000040056b <+4>:     sub    rsp,0x10
   0x000000000040056f <+8>:     mov    DWORD PTR [rbp-0x8],0x0
   0x0000000000400576 <+15>:    mov    edi,0x400654
   0x000000000040057b <+20>:    call   0x400420 <puts@plt>
   0x0000000000400580 <+25>:    lea    rax,[rbp-0x8]
   0x0000000000400584 <+29>:    mov    rsi,rax
   0x0000000000400587 <+32>:    mov    edi,0x40065f
   0x000000000040058c <+37>:    mov    eax,0x0
   0x0000000000400591 <+42>:    call   0x400440 <__isoc99_scanf@plt>
   0x0000000000400596 <+47>:    mov    eax,DWORD PTR [rbp-0x8]
   0x0000000000400599 <+50>:    mov    edi,eax
   0x000000000040059b <+52>:    call   0x400556 <encrypt>
   0x00000000004005a0 <+57>:    mov    DWORD PTR [rbp-0x4],eax
   0x00000000004005a3 <+60>:    cmp    DWORD PTR [rbp-0x4],0xdeadbeef
   0x00000000004005aa <+67>:    jne    0x4005b8 <main+81>
   0x00000000004005ac <+69>:    mov    edi,0x400662
   0x00000000004005b1 <+74>:    call   0x400420 <puts@plt>
   0x00000000004005b6 <+79>:    jmp    0x4005c2 <main+91>
   0x00000000004005b8 <+81>:    mov    edi,0x400669
   0x00000000004005bd <+86>:    call   0x400420 <puts@plt>
   0x00000000004005c2 <+91>:    mov    eax,0x0
   0x00000000004005c7 <+96>:    leave
   0x00000000004005c8 <+97>:    ret
End of assembler dump.
pwndbg> disass encrypt
Dump of assembler code for function encrypt:
   0x0000000000400556 <+0>:     push   rbp
   0x0000000000400557 <+1>:     mov    rbp,rsp
   0x000000000040055a <+4>:     mov    DWORD PTR [rbp-0x4],edi
   0x000000000040055d <+7>:     mov    eax,DWORD PTR [rbp-0x4]
   0x0000000000400560 <+10>:    xor    eax,0x4d2
   0x0000000000400565 <+15>:    pop    rbp
   0x0000000000400566 <+16>:    ret
```
From here, it is evident that the `encrypt` function takes in a value from the `edi` register, which is taken in from `scanf`. This value is compared with `0xdeadbeef` after encryption. The encryption appears to be a simple XOR operation with `0x4d2`, so the flag would just be `0xdeadbeef` XOR `0x4d2`. 

**Flag**: `flag{-559039939}`
