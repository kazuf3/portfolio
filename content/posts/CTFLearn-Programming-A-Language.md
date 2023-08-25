---
title: "CTFLearn Programming a Language"
date: 2023-08-16T15:46:18-07:00
categories: ["CTF", "blog"]
---

A problem to implement a stack machine.

- [Problem here](https://ctflearn.com/challenge/1050)

This problem asks to program a stack machine.
My Python3 answer is below. The size of stack is unchecked.

```
from collections import deque
import sys

class StackLang:
    def __init__(self):
        self.stack = deque()
        self.stack.append(0)
    def run(self, filename):
        with open(filename, 'r') as file:
            txt = file.read()

        for i in txt:
            if i == '-':
                self.minus()
            elif i == '+':
                self.plus()
            elif i == '>':
                self.gt()
            elif i == '<':
                self.lt()
            elif i == '@':
                self.at()
            elif i == '.':
                self.dot()
            elif i == '€':
                self.euro()

    def minus(self):
        value = self.stack.pop()
        value = value - 1
        self.stack.append(value)

    def plus(self):
        value = self.stack.pop()
        value = value + 1
        self.stack.append(value)

    def gt(self):
        value = self.stack.popleft()
        self.stack.append(value)

    def lt(self):
        value = self.stack.pop()
        self.stack.appendleft(value)

    def at(self):
        value1 = self.stack.pop()
        value2 = self.stack.pop()
        self.stack.append(value1)
        self.stack.append(value2)

    def dot(self):
        value = self.stack.pop()
        self.stack.append(value)
        self.stack.append(value)

    def euro(self):
        ls = [ chr(i) for i in self.stack]
        print("".join(ls))


def main(argv, arc):
    print(argv)
    machine = StackLang()
    machine.run(argv[1])

if __name__ == '__main__':
    main(sys.argv, len(sys.argv))
```

## Takeaways

This is an quintessential problem to building a stack machine. First I thought I
should write it in Scala because it would make the code clearer with (1) case
class will comply to the BNF, (2) the parser combinator will help DSL
construction with the separation of instructions and parser.

I tried Python this time for a practice. It is not easy to make the program code
clean, though it seems much shorter than what I will do in Scala.
