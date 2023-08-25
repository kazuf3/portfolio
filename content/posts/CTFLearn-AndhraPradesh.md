---
title: "CTFLearn AndhraPradesh"
date: 2023-08-17T10:04:41-07:00
categories: ["CTF", "blog"]
---

A problem to reverse engineer and find the correct condition.

- [Problem link](https://ctflearn.com/challenge/1030)

In this challenge, I have to change the value of `con1`~`con5` in order to pass tests in `_start`~`test4`.

```
; Andrha Pradesh Assembler Challenge for CTFLearn
; This challenge focuses on cmp, je and jne

section .data
    welcome db "Hello CTFlearn Andhra Pradesh Assembler Challenge!",0x0a,0x00
    noflag db "Sorry no flag for you :-(",0x0a,0x00
    alldone db "All Done!",0x0a,0x00
    baddata db "Baad Data!",0x0a,0x00
    congrats db "Congrats!! You found the flag!!", 0x0a, 0x00
    data    dw 0xbb35,0xbb4c,0xbb3a,0xbb54,0xbb5b,0xbb57,0xbb66,0xbb52,0xbb5d,0xbb30,\
               0xbb5f,0xbb5c,0xbb5b,0xbb66,0xbb57,0xbb56,0xbb57,0xbb5c,0xbb41,0xbb4c,\
               0xbb5b,0xbb54,0xbb6b,0xbb59,0xbb6b,0xbb63

;   ###################################################################
;   Change the values of these five constants to solve the challenge
    con1 db ??? ; C syntax for hex constant
    con2 db ???  ;
    con3 db ???
    con4 db ????h   ; this form for hex constants is popular among assembly language programmers
    con5 db ????h
;   ####################################################################
;   Do not change any code below here

section .bss
    buffer resb 32

section .text
    global _start

_start:
    xor r8, r8      ; init the exit status to 0

    mov rax, 1      ; sys_write system call
    mov rdi, 1      ; stdout (write to screen)
    mov rsi, welcome   ; memory location of string to write
    mov rdx, 51     ; number of characters in string to write
    syscall

    xor rax, rax    ; clear the rax register
    mov al, [con1]  ; move the value of con1 to the low byte of rax
    cmp al, 0xab
    je _test2

    mov r8, 1       ; exit status
    jmp _noflagforyou

_test2:
    xor rax, rax
    mov al, [con2]
    cmp al, 0xcb
    jne _test3

    mov r8, 2       ; exit status
    jmp _noflagforyou

_test3:
    mov r8, 3       ; exit status
    xor rax, rax
    mov al, [con3]
    cmp al, 0x20
    ja  _noflagforyou

    mov r8, 4       ; exit status
    xor rax, rax
    mov al, [con3]
    cmp al, 20h
    jb _noflagforyou

_test4:
    ; https://en.wikibooks.org/wiki/X86_Assembly/X86_Architecture
    mov r8, 5h      ; exit status
    xor rax, rax
    mov al, [con4]
    mov ah, [con5]
    cmp ax, 0baadh
    jne _noflagforyou

    mov r8, 6h      ; exit status
_checkflag:
    xor rdx, rdx    ; clear the rdx register
    xor rcx, rcx    ; init the rcx counter to zero
    xor rbx, rbx    ; clear the rbx register
    mov bl, BYTE [con1]
    add bl, BYTE [con3]
    mov dl, BYTE [con4]
    mov dh, BYTE [con5]

_Loop1:
    xor rax, rax
    mov ax, WORD [data+rcx*2]
    sub ax, dx

    xor rax, rbx

    cmp rax, 32
    jb _baddata
    cmp rax, 126
    ja _baddata

    mov [buffer+rcx], BYTE al
    inc rcx
    cmp rcx, 26
    jb _Loop1
    mov [buffer+rcx], BYTE 0x0a

_printcongrats:
    mov rax, 1      ; sys_write system call
    mov rdi, 1      ; stdout
    mov rsi, congrats ; memory location of string to write
    mov rdx, 32     ; number of characters in string to write
    syscall

_printflag:
    mov rax, 1      ; sys_write system call
    mov rdi, 1      ; stdout
    mov rsi, buffer ; memory location of string to write
    mov rdx, 27     ; number of characters in string to write
    syscall

    mov r8, 0h      ; exit status
    jmp _alldone

_baddata:
    mov rax, 1      ; sys_write system call
    mov rdi, 1      ; stdout
    mov rsi, baddata ; memory location of string to write
    mov rdx, 11     ; number of characters in string to write
    syscall
    jmp _alldone

_noflagforyou:
    mov rax, 1      ; sys_write system call
    mov rdi, 1      ; stdout
    mov rsi, noflag ; memory location of string to write
    mov rdx, 26     ; number of characters in string to write
    syscall

_alldone:
    mov rax, 1      ; sys_write system call
    mov rdi, 1      ; stdout
    mov rsi, alldone ; memory location of string to write
    mov rdx, 10     ; number of characters in string to write
    syscall

_byebye:
    mov rax, 60     ; exit system call
    mov rdi, r8     ; return code saved in register r8
    syscall


```

The following table shows each conditional instruction work.
| Instruction after `cmp A, B`| Jump condition |
| --------------------------- | -------------- |
| je                          | A == B         |
| jne                         | A != B         |
| ja                          | A > B          |
| jb                          | A < B          |

In `_test3`, there are two conditional jumps. In order to proceed to `_test4`, I
want to jump on neither destination, so we have to set `A==B`.

In `_test4`, the register `AX` consists of two registers `AL` and `AH`. We want
to set `AX == AH++AL`.

The correct values are:
```
    con1 db 0xab  ; C syntax for hex constant
    con2 db 0x00  ;
    con3 db 0x20
    con4 db 00ADh   ; this form for hex constants is popular among assembly language programmers
    con5 db 00BAh
```

## Takeaways

This is a good problem for assembly beginners. The thinking process is similar
to ordinary debugging but in a low-level.

Also, the way of using registers are unique technique. Same bits may have
different name and we have to think which is higher and which is lower bits.
