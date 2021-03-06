---
title: "Mangarevan arithmetic"
date: 2016-04-06T18:37:27+11:00
draft: false
tags: ["math", "obscura", "hack"]
---
PNAS recently published a cognitive anthropology paper titled [Mangarevan invention of binary steps for easier calculation](http://www.pnas.org/content/111/4/1322). The paper describes an arithmetic system that had been used for hundreds of years by islanders living in Mangareva (a small island in French Polynesia) for the purpose of “counting a small group of highly valued objects such as turtles, fish, coconuts, octopuses, and breadfruits”. This system is not too different from the decimal system that we’re using today, except that a number in the Mangarevan language can contain a small segment of binary code, which employs four numerals to represent 10 multiplied by the first four powers of 2. The numerals are takau (10), paua (20), tataua (40), and varu (80), and they are abbreviated as K, P, T, and V respectively by the authors. For example, 273 is decomposed as 3VPK3 (3*80+20+10+3, pronounced “toru varu paua takau toru”). The four extra numerals should make arithmetic more complicated, but the authors point out that the complexity is compensated by the ease of binary arithmetic (which is why computers use binary numbers). Indeed, Table 2B in the paper shows that addition involving {K, P, T, V}, which has 36 possible combinations, can be solved with repeated applications of three simple rules (K+K=P, P+P=T, and T+T=V). It is not hard to see that shortcuts like this were important for a culture that did not develop written notations for the numbers.

I wrote a simple  program to simulate the Mangarevan system. The following is a multiplication table generated by the program:

{{< figure src="/blog/mangarevan.png">}}

Although the authors argue that various tricks can be used to simplify multiplications involving binary numbers, you can see that multiplication is still pretty complicated. This makes me wonder how easy it is to actually use the Mangarevan system. It would be very interesting if naive subjects are trained to use it. The performance can be compared to a control group in which subjects are trained to use an artificial notation that is purely decimal.
