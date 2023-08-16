---
title: "CTFLearn Tone Dialing"
date: 2023-08-16T10:53:23-07:00
categories: ["CTF", "blog"]
---

- [Problem here](https://ctflearn.com/challenge/889)

This problem consists of two parts:
- Obtain the code from wav file
- Decode

## Obtain the code from wav file

I used [dtmf-decoder](https://github.com/ribt/dtmf-decoder) that extract the tone dialing as decimal.

I already have Python3 environment and I don't want to mess it up.
I made a modification of its installation.

```
$ git clone https://github.com/ribt/dtmf-decoder.git
$ cd dtmf-decoder/
$ python3 -m pip install -r requirements.txt --upgrade
$ chmod +x dtmf.py
$ cp dtmf.py ~/bin/dtmf
```

Then I simply run the command and obtain the code.
```
$ dtmf you_know_what_to_do.wav
```

## Decode

The obtained code is a series of decimals and we want to split it into ascii
code. However, the problem is how to tokenize it though we know each token
should be in the range of 0 to 127. Let's say we have `1081`. It can be either
`10` and `81` or `108` and `1`. The answer was in the audio file itself. There
are pauses between numbers, which indicates the separator.

After tokenizing, we can use `printf '%x'` to convert into hex and `xxd` to
convert them into ascii encoded string.

```
$ printf '%x' 67 84 70 108 101 97 110 123 67 82 89 80 84 79 71 82 65 80 72 89 125 | xxd -r -p
CTFlean{CRYPTOGRAPHY}%
```
