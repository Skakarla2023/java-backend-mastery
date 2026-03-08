<h2 align="center"><b>Bitwise operators</b></h2>


- Bitwise operators are used to perform operations directly on the binary representation of the number(bits).
- Instead of working on the whole numbers, these operators perfrom operations in low level  bit-by-bit, enabling fast and efficient low level operations.
- Bitwise operatord work only with Integer datatypes such as int, long, short, byte and char.

  Below are the main bitwise operators in Java:

| Operator | Name                        | Description                                                                                                                        |
| -------- | --------------------------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| `&`      | Bitwise AND                 | Compares each bit of two numbers. Result bit is **1 only if both bits are 1**, otherwise 0.                                        |
| `\|`     | Bitwise OR                  | Compares each bit of two numbers. Result bit is **1 if at least one bit is 1**.                                                    |
| `^`      | Bitwise XOR                 | Compares each bit. Result bit is **1 if the bits are different**, otherwise 0.                                                     |
| `~`      | Bitwise NOT (Complement)    | **Flips all bits** of the number (1 becomes 0, 0 becomes 1).                                                                       |
| `<<`     | Left Shift                  | **Shifts bits to the left** by a specified number of positions. Zeros are filled on the right. Equivalent to multiplying by (2^n). |
| `>>`     | Right Shift                 | **Shifts bits to the right**. Leftmost bits depend on sign (sign extension). Equivalent to dividing by (2^n).                      |
| `>>>`    | Unsigned Right Shift (Java) | Shifts bits to the right **and fills leftmost bits with 0**, ignoring the sign bit.                                                |



Observe the following code for better understanding:

```java
class BitwiseOp {
    public static void main(String[] args) {
        int a = 5;
        int b = 3;

        System.out.println(a+" & "+b+" = "+ (a & b));
        System.out.println(a+" | "+b+" = "+ (a | b));
        System.out.println(a+" ^ "+b+" = "+ (a ^ b));
        System.out.println(a+" >> "+b+" = "+ (a >> b));
        System.out.println(a+" << "+b+" = "+ (a << b));
        System.out.println("~"+a+" = "+ (~a));
        System.out.println("~"+b+" = "+ (~b));
    }
}
```

Output:

```
5 & 3 = 1
5 | 3 = 7
5 ^ 3 = 6
5 >> 3 = 0
5 << 3 = 40
~5 = -6
~3 = -4
```


### Bitmasking

Bit masking is a technique used to manipulate or check specific bits of a number using bitwise operators. It helps perform operations efficiently and allows multiple states (like true/false flags) to be stored inside a single integer. The common operators used in bit masking are &, |, ^, and ~.


The following Java code demonstrates several bit masking operations:

```java
class BitMasking {
    public static void main(String[] args) {
        int n = 5;


        // check if even or odd
        if((n & 1) == 0) {
            System.out.println("Even");
        } else {
            System.out.println("Odd");
        }



        // check if ith bit is set
        int i = 2;
        if((n & (1 << i)) == 0) {
            System.out.println("i-th bit is not set");
        } else {
            System.out.println("i-th bit is set");
        }


        // set ith bit
        int a = n;
        i = 3;
        int res = (a | (1 << i));
        System.out.println("After setting " + i + "-th bit: " + res);


        // toggle ith set bit
        int ans = (a ^ (1 << i));
        System.out.println("After toggling " + i + "-th bit: " + ans);


        // remove ith set bit
        int b = (a & ~(1 << i));
        System.out.println("After removing " + i + "-th bit: " + b);



        // count number of set bits
        int count = 0;
        while(a > 0) {
            a = (a & (a-1));
            count++;
        } 
        System.out.println("Number of set bits: " + count);



        // power of 2
        if((n & (n-1)) == 0){
            System.out.println(n + " is a power of 2");
        } else {
            System.out.println(n + " is not a power of 2");
        }


        // get rightmost set bit
        int rmsb = (n & (-n));
        System.out.println("Rightmost set bit: " + rmsb);


        // remove rightmost set bit
        int remove = (n & (n-1));
        System.out.println("After removing rightmost set bit: " + remove);



        // swap 2 numbers without using 3rd variable
        int x = 4;
        int y = 5;
        x = x ^ y;
        y = x ^ y;
        x = x ^ y;
        System.out.println("After swapping: x = " + x + ", y = " + y);

    }
}
```

Output:
```

Odd
i-th bit is set
After setting 3-th bit: 13
After toggling 3-th bit: 13
After removing 3-th bit: 5
Number of set bits: 2
5 is not a power of 2
Rightmost set bit: 1
```
