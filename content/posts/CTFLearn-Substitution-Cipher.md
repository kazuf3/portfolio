---
title: "CTFLearn Substitution Cipher"
date: 2023-08-24T01:38:00-07:00
categories: ["CTF", "blog"]
---

A problem to solve substitution cipher, which as *Dancing Man* by Sir Authur Conan Doyle.

- [Problem link](https://ctflearn.com/challenge/238)

The given encrypted text is:

MIT YSAU OL OYGFSBDGRTKFEKBHMGCALSOQTMIOL. UTFTKAMTR ZB DAKQGX EIAOF GY MIT COQOHTROA HAUT GF EASXOF AFR IGZZTL. ZT CTKT SGFU, MIT YSACL GF A 2005 HKTLTFM MODTL MIAF LMADOFA GK A CTTQSB LWFRAB, RTETDZTK 21, 1989 1990, MIT RKTC TROMGKL CAL WHKGGMTR TXTKB CGKSR EAF ZT YGWFR MIT EGFMOFWTR MG CGKQ AM A YAOMIYWS KTHSOTL CITKT IGZZTL, LMBST AOD EASXOF, AMMAEQ ZGMI LORTL MG DAKQL, "CIAM RG EGFMKGSSOFU AF AEMWAS ZGAKR ZGVTL OF MIT HKTHAKTFML FADT, OL ODHWSLOXT KADHAUTL OF CIOEI ASCABL KTYTKTFETL MIT HALLCGKR, CIOEI DGFTB, AFR MITB IAR SOMMST YKGFM BAKR IOL YKWLMKAMTR EGSGK WFOJWT AZOSOMB COMI AFR OFROLHTFLAMT YGK MTAEI GMITK LMWROTL, AKT ACAKRL ZARUTL, HWZSOLITR ZTYGKT CTSS AL A YOKT UKGLL HSAFL CTKT GKOUOFASSB EIAKAEMTKL OF MIT LMKOH MG CIOEI LTTD MG OM CITF MTDHTKTR OF AFR IASSGCOFU MITB'KT LODHSB RKACOFU OF UOXTL GF" HKOFEOHAS LHOMMST ROLMGKM, KTARTKL EGDOEL AKT WLT, CAMMTKLGF MGGQ MCG 16-DGFMIL AYMTK KTLOLMAQTL A DGKT EKTAM RTAS MG EASXOF GYMTF IGZZTL MG ARDOML "LSODB, "ZWM OM'L FADTR A FOUIM GWM LIT OL HGOFM GY FGM LTTF IGZZTL MIT ZGGQL AM MIAM O KTDAOFOFU ZGGQ IADLMTK IWTB AKT AHHTAKAFET: RTETDZTK 6, 1995 DGD'L YKADTL GY EASXOF UOXTF A CAUGF, LGDTMODTL MIAM LG OM'L YAMITKT'L YADOSB FG EAFETSSAMOGFLIOH CAL HKTLTFML YKGD FGXTDZTK 21, 1985 SALM AHHTAK AZLTFET OF AFGMITKCOLT OM IAHHB MG KWF OM YGK MIOL RAR AL "A SOMMST MG MGSTKAMT EASXOF'L YADOSB RKACF ASDGLM EGDDTFRTR WH ZTOFU HTGHST OFLMAFET, UTM DAKKOTR ZB A RAFET EASXOF'L GWMSAFROLOFU MIT FTCLHAHTK GK MAZSGOR FTCLHAHTK ZWLOFTLL LIGC OL GF!" AFR LHKOFML GY EIOSRKTF'L RAR'L YKWLMKAMTR ZB MWKF IWDGK, CAL HWZSOE ROASGU MITKT'L FGM DWEI AL "'94 DGRTKFOLD" CAMMTKLGF IAL RTSOUIML GY YAFMALB SOYT CAMMTKLGF LABL LTKXTL AL AF AKMOLML OL RTLMKWEMOGF ZWLOFTLL, LHAETYAKTK GY MIT GHHGKMWFOMOTL BGW ZGMI A MGHOE YGK IOL IGDT MGFUWT-OF-EITTQ HGHWSAK MIAM OM CAL "IGF" AFR JWAKMTK HAUT DGKT LHAEOGWL EAFETSSAMOGF MIT HAOK AKT ESTAKSB OF HLBEIOE MKAFLDGUKOYOTK'L "NAH" LGWFR TYYTEM BGW MIOFQTK CAMMTKLGF ASLG UKTC OFEKTROZST LHAET ZWBL OF EGDDGFSB CIOST GMITKCOLT OM'L FADT OL FGMAZST LMGKBSOFT UAXT MIT GHHGKMWFOMOTL BGW EAFETSSAMOGF MIT "EASXOF GYYTK MG DAQT IOD OFEGKKTEM AFLCTKL CAMMTK AKMCGKQ GMITK GYMTF CIOEI OL TXORTFM MG GMITK LMKOH OL MG MITOK WLT GY KWSTL MIAM LIGCF GF LAFROYTK, CIG WLTL A EKGCJWOSS ZT LTTF "USWTR" MG MIT GFSB HTKL AFR IOL YAMITK LWHHGKM OL SWFEISOFT UAXT MITLT MIOF A BTAK OF DWSMODAMTKOAS AFR GZMAOF GF LAFMALB, IOL WLT, CAMMTKL ROASGUWT OL AF "AKMOLM'L LMAMWL AL "A ROD XOTC OF MIT TLLTFMOASSB MG DAQT IOD LTTD MG OFESWRTR MIAM EASXOF OL AF GRR ROASGUWT DGLM GY MIT ESWZ IAL TVHKTLLOGF GWMLORT AXAOSAZST MG

Since we know this is substitution cipher, we want to check the frequency of
each letter. Then we can apply [common English letter
frequency](https://www3.nd.edu/~busiforc/handouts/cryptography/letterfrequencies.html).

| Letter | Occurrences | Candidates             |
| ------ | ----------- | ---------------------- |
| T	     | 236         | E                      |
| A	     | 189         | T, A, I, N, O, S, H, R |
| M	     | 183         | T, A, I, N, O, S, H, R |
| L	     | 174         | T, A, I, N, O, S, H, R |
| O	     | 160         | T, A, I, N, O, S, H, R |
| G	     | 149         | T, A, I, N, O, S, H, R |
| F	     | 141         | T, A, I, N, O, S, H, R |
| ...    | ...         | ...                    |
| J	     | 3           | Q, J, X, Z             |
| V	     | 2           | Q, J, X, Z             |
| N	     | 1           | Q, J, X, Z             |
| P	     | 0           | Q, J, X, Z             |

Assume T=>E, the first word `MIT` is very likely `THE` since `MIT` occurs 29
times. Also, the single letter `A` occurs 11 times so I would say it is `A`. 

| A | I | M | T |
|---|---|---|---|
| A | H | T | E |

Next, word `OM` that occurs 4 times. Since `M` is `T` we assume it is `IT` since
`A` is already known as `A`. `MIAF` is `THAN` since we already have `T`. `AFR`
should be `AND`.

| A | F | I | M | O | R | T |
| - | - | - | - | - | - | - |
| A | N | H | T | I | D | E |

`ZT` is `BE`. `IOL` is `HI*` so either `HIM` or `HIS`. We assume it's `HIS`
because `M` has much higher occurence so as in common English. `AKT` is `A*E` we
assume `ARE`. `CAL` is `*AS`, we assume `WAS`.

| A | F | I | K | L | M | O | R | T | Z |
| - | - | - | - | - |---|---|---|---|---|
| A | N | H | R | S | T | I | D | E | B |

`FADT` is `NA*E`, which is likely `NAME`. `ZARUTL` is `BAD*ES`, which should be
`BADGES`.

| A | D | F | I | K | L | M | O | R | T | U | Z |
|---|---|---|---|---|---|---|---|---|---|---|---|
| A | M | N | H | R | S | T | I | D | E | G | B |

Now we have enough hints. So far we have tracing short words. Let's fill other
characters from longer words. Let's find `B`. For example `TXTKB` is `E*ER*`,
and likely `EVERY`. `CIOEI` is `*HI*H` `WHICH`.

| A | B | C | D | E | F | I | K | L | M | O | R | T | U | X | Z |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| A | Y | W | M | C | N | H | R | S | T | I | D | E | G | V | B |

`HGOFM` is `**INT` which should be `POINT`. `JWAKMTK` is `**ARTER` which seems `QUARTER`.

| A | B | C | D | E | F | G | H | I | J | K | L | M | O | R | T | U | W | X | Z |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| A | Y | W | M | C | N | O | P | H | Q | R | S | T | I | D | E | G | U | V | B |

`AKMCGKQ` is `ARTWOR*` so `ARTWORK`. `EASXOF` is `CA*VIN` so `CALVIN`? `ESTAKSB`
is `C*EAR*Y` and yes, `S` is `L`. `ZGVTL` is `BO*ES` and `BOXES`. `YADOSB` is
`*AMILY`, `FAMILY`.

| A | B | C | D | E | F | G | H | I | J | K | L | M | O | Q | R | S | T | U | V | W | X | Y | Z |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| A | Y | W | M | C | N | O | P | H | Q | R | S | T | I | K | D | L | E | G | X | U | V | F | B |

Though there are still holes(`N` and `P`), we have enough letters to translate. The first sentence is:

```
(Before) MIT YSAU OL OYGFSBDGRTKFEKBHMGCALSOQTMIOL.
(After) THE FLAG IS IFONLYMODERNCRYPTOWASLIKETHIS.
```

Haha, funny.

## Takeaways

First, we counted the letter occurence and put candidate into them. This was
very useful when filling the holes in words. Then we begin with short words to
guess what it is. When we have multiple word candidates, we can always keep them
and look for other and later come back.

When we have enough letters in the table, we can go for longer words. Which is
faster but impossible when the table size is not enough.

For those least occured letters, they are not likely significant so we can leave
them empty.

Lastly, we can always use tools for this job by searching substitution cipher.
