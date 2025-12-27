---
description: Whenever I want to learn a new programming language one of the first
  things I do is to make a fizzbuzz program. Fizzbuzz programs loop through a range
  of numbers, usually 1 through 99, and print 'fizz' if the number is divisible by
  3 and 'buzz' if the number is divisible by 5. It prints 'fizzbuzz' if it's divisible
  by both.
pubDate: '2024-05-06'
title: Fizz Buzz in Cobol
---


Whenever I want to learn a new programming language one of the first things I do is to make a fizzbuzz program.  Fizzbuzz programs loop through a range of numbers, usually 1 through 99, and print 'fizz' if the number is divisible by 3 and 'buzz' if the number is divisible by 5.  It prints 'fizzbuzz' if it's divisible by both.

I've always kind of wanted to know what COBOL was all about.  It has been around for over 60 years and it was one of the first programming languages.  It is still being used today, especially in finance, because of its use in mainframes and its high speed and reliability.  There aren't a lot of people learning COBOL and it's not taught to computer science students, but it's not that hard.

I spent some time today putting this program together.

```
      * Fizz Buzz
      * number divisible by 3, print fizz
      * number divisible by 5, print buzz
      * number divisible by 15, print fizzbuzz
       identification division.
       program-id. fizzbuzz.
       environment division.
       data division.
       working-storage section.
           01 counter pic 9(3) value 1.
           01 c pic 9(3).
           01 d pic 9(3).
           01 e pic 9(3).
           01 f pic 9(3).
       procedure division.
           
           perform func with test after until counter>99.
           stop run.

           func.
           display 'counter : ' counter with no advancing.
           divide counter by 3 giving c remainder d
           divide counter by 5 giving e remainder f
           display ' ' with no advancing.
           if d = 0 then
               display 'fizz' with no advancing
           end-if.
           if f = 0 then
               display 'buzz' with no advancing
           end-if.
           display ' '.
           add 1 to counter.

       end program fizzbuzz.
```

I complied it with the GnuCOBOL compiler, and it works!  Any banks looking for COBOL programmers?
