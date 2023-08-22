---
title: "CTFLearn Reykjavik"
date: 2023-08-18T14:31:08-07:00
categories: ["CTF","blog"]
---

- [Problem link](https://ctflearn.com/challenge/990)

With given executable binary, first we want to extract text from it. However,
all possible text from `strings` command are false flags.

Let's examine the bahavior of the program. It prompts the usage.
```
$ ./Reykjavik
Usage: Reykjavik CTFlearn{flag}
```

OK, let's give it an another try.
```
$ ./Reykjavik CTFlearn{flag}
Welcome to the CTFlearn Reversing Challenge Reykjavik v2: CTFlearn{flag}
Compile Options: ${CMAKE_CXX_FLAGS} -O0 -fno-stack-protector -mno-sse

Sorry Dude, 'CTFlearn{flag}' is not the flag :-(
```

From all the external observation, we can assume the code is something like
following psuedo code.If the original program is structured like this, we can
obtain the flag from the memory when it compares with parameters.

```
if(args.isEmpty){
  print usage
}else{
  val flag = generate_correct_flag()
  if (args != flag){
    print sorry
  } else {
    print correct message
  }
}
```

Now we can use disassembly and decompile tool such as
[Cutter](https://cutter.re/). After decompiling, we can find a conditional
statement which points to either printing correct or sorry message. OK, the
structure seems pretty alighned with our assumption but the true case and false
case are flipped.

```
iVar2 = strcmp(&uStack_38, puVar1);
if (iVar2 == 0){
...
```

We put break points here and check the stack values. After executing of
`strcmp`, we find the address offset `0x7fff1a75c3c0` on top of the stack.
That is the address of correct flag.

## Takeaways

This is a great problem which requires the hacker to have a standard process,
such as observation, analysis, assumption and exploit. Otherwise it can be much
difficult to walk through the assembly code for the full length.

It is my first time to use Cutter. The decompiled code in C requires some
trainings to be able to read. After the submission, there were some comments
said we should follow the `rsp` register. I have no idea why `rsp` register here
because `strcmp` implementation seems capsulated.
