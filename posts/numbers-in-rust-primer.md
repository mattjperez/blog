# Numbers in Rust: A Primer

_10 June 2021 · #rust · #primitives_

**Table of Contents**
- [Intro](#intro)
- [Theory](#theory)
  - [Unsigned Integers](#unsigned-integers)
  - [Signed Integers](#signed-integers)
  - [isize and usize](#isize-and-usize)
  - [Overflow](#overflow)
  - [Floats](#floats)
- [Practice](#practice)
  - [Constants](#constants)
  - [`isize` and `usize`](#isize-and-usize)
  - [Conversion](#conversion)
  - [Handling Overflow](#handling-overflow)
- [Conclusion](#conclusion)
- [Further Reading](#further-reading)



## Intro

Hey everyone, welcome to my first Rust blog post!
My goal with this post is to provide a beginner-friendly guide that covers 90% of what you might come across in general Rust when it comes to numbers.
While this isn't at all an exhaustive guide, I hope that readers will gain enough from it to jumpstart their searches for more in-depth information.

This blog is broken into two parts:
- Theoretical: Acts as a general overview of numerical data representation.
- Practical: Builds off of the theory and shows how to improve your usage of numbers in Rust.

You may find this blog useful if:
- You're coming to Rust from a higher-level or interpreted language, like Python.
- You'd like a refresher on numerical data representation, especially as it relates to Rust.
- You've wondered when to use `isize` or `usize`
- You don't know how to prevent cases like `10_u8 - 20_u8` from panicking.
- You're not sure what the `saturating`, `wrapping` and `overflowing` integer methods in the standard library do.

## Theory

### Integers
In computer science, everything (at the moment) is represented by `1`'s and `0`'s, stored in groupings such as `bytes` (8 `bits`).  
Each bit represents a **power of 2**, so a group of **8 bits** representing the number `130` may look like this.

|       Bits        |       1       |       0       |       0       |       0       |       0       |       0       |       1       |       0       |
| :---------------: | :-----------: | :-----------: | :-----------: | :-----------: | :-----------: | :-----------: | :-----------: | :-----------: |
| Base 10 (Decimal) |      128      |      64       |      32       |      16       |       8       |       4       |       2       |       1       |
|  Base 2 (Binary)  | 2<sup>7</sup> | 2<sup>6</sup> | 2<sup>5</sup> | 2<sup>4</sup> | 2<sup>3</sup> | 2<sup>2</sup> | 2<sup>1</sup> | 2<sup>0</sup> |

`128 + 0 + 0 + 0 + 0 + 0 + 2 + 0 == 130`
The above representation is that of an `unsigned` integer with `8-bits`, i.e. Rust's `u8`.  

>For simplicity, we'll only be looking at the `8-bit` variants of integers in this major section. You can apply these topics to the larger variants by extending the number of bits towards the left-hand side, i.e. 256, 512, etc.

Rust makes viewing these representations fairly straightforward using one of its convenient formatting 

```rust
println!("{:b}", 128);
// 10000000


println!("{:08b}", 1); // packed with 0's, up to 8 places
// 00000001
```
The largest number representable by `u8` (all bits set to `1`) is `255`, and the lowest number (all bits set to `0`) is `0`.  If we tried to represent something larger, say `256`, this would happen.

```rust
let num: u8 = 256;
println!("{}", num);

// 
 --> src/main.rs:2:19
  |
2 |     let num: u8 = 256;
  |                   ^^^
  |
  = note: `#[deny(overflowing_literals)]` on by default
  = note: the literal `256` does not fit into the type `u8` whose range is `0..=255`
// rustc's error messages are really thoughtful, thanks compiler team!

```
The two notes above are especially helpful because they 

a.  try to provide as much detail as possible to the context of the error, possibly helping you fix the issue immediately, and 

b. give you keywords to search for more in-depth information (`overflowing_literals`). 

These numbers are `unsigned` because they do not contain a `sign bit`, a bit used to indicate whether a number is positive or negative. 
Therefore **unsigned integers can only represent positive values**.
This makes them perfect for modeling situations where negative values don't exist, like a shopping cart; you can't have `-1 headphones` in your cart, right? 
As you'll see in the section on `signed integers`, the maximum number you can represent in a `u8` is larger than that of an `i8`.
This is true for `unsigned` type to their `signed` counterpart. 



### Signed Integers
There are different ways to represent signed integers, Rust uses a method called [two's complement](https://en.wikipedia.org/wiki/Two%27s_complement). 
Ben Eater has a [video on two's complement](https://www.youtube.com/watch?v=4qH4unVtJkE&t=29s) where he does an amazing job of explaining things. The next few sub-sections are merely a recap of his video so feel free to skip it if you're familiar with the concept. I'm including this as a convenient reference when looking at how Rust handles integer overflow.

#### Unsigned Operations

To add two `unsigned` integers, convert them to their binary representations and add them as you would with pen and paper, by carrying the bit to the next .

```
128 = 10000000
+ 2 = 00000010
---------------
130 = 10000010
```

#### Introducing a Sign Bit, Signed Magnitude

A simple way to represent positive and negative numbers is by adding a `sign bit`. In this case, `0` represents positive integers while `1` represents negative integers.

```
64 with sign bit
0 1 0 0 0 0 0 0

-64 with sign bit
1 1 0 0 0 0 0 0
```

You'll notice we've lost an entire power of 2 by introducing a sign bit. The range we can represent with 8 bits has gone from `0..=255` to `-127..=127`.

While that's the cost to represent negatives, we've also lost the ability to add/subtract bits how we did previously, at least when negative numbers are involved.

```
8 + (-8)
  0 0 0 0 1 0 0 0
+ 1 0 0 0 1 0 0 0
-----------------
  1 0 0 1 0 0 0 0  == -16 ???
```

#### One's Complement

Sign bit and mirrored bits

```
24 in 8-bit ones-complement
 +/-  64 32  16  8  4  2  1
  0   0   0   1  1  0  0  0

-24 in 8-bit ones-complement
 +/-  64  32  16  8  4  2  1
  1   1   1   0  0  1  1  1

```
Almost, but not quite there. The following scenarios still happen.
```
There are two distinct possibilities for 0.
 +/-  64 32  16  8  4  2  1
  0   0   0   0  0  0  0  0  =  0
  1   0   0   0  0  0  0  0  = -0

!!!!!!!!Fix the next example

Operations with two negative numbers are off-by-one
(-8) + (-10)  
  1 1 1 1 0 1 1 1  // -8
+ 1 1 1 1 0 1 0 1  // -10
-----------------
  1 1 1 1 1 1 0 0  == -1


```


#### Two's Complement

One's complement, but with `-0` removed, has the effect of converting the *largest bit* to a negative.

```
24 as u8
 128  64 32  16  8  4  2  1
  0   0   0   1  1  0  0  0

24 as i8
-128  64  32  16  8  4  2  1
  0    0   0   1  1  0  0  0
  
Bit Addition/Subtraction is restored!
You also now know why the min and max values representable with twos-complement are off-by-one!
```



#### Flipping Signs with Two's Complement

1. Invert the bits
2. Add 1
```
24 as u8
 128  64 32  16  8  4  2  1
  0   0   0   1  1  0  0  0
  
1) Invert the bits
  1   1   1   0  0  1  1  1
2) Add 1 (carry bits forward)
  1   1   1   0  1  0  0  0
  
-24 as i8
-128  64  32  16  8  4  2  1
  1   1   1   0  1  0  0  0

-128 + 64 + 32 + 8 
-128 + 104
-24
  
```

There you have it, this is how signed integers are represented in Rust.

## Floats
Floats in Rust follow the 2008 revision of the IEEE-754 standard on 
[single-precision floating-points](https://en.wikipedia.org/wiki/Single-precision_floating-point_format)(e.g. `f32`, `float`, `binary32`)
and [double-precision floating-points](https://en.wikipedia.org/wiki/Double-precision_floating-point_format)(e.g. `f64`, `double`, `binary64`) representations of floating-points.

> Don't use floats for financial operations

```
 1/2, 1/4, 1/8, 1/16, 1/32
```

Better precision -> smaller range

larger range -> worse precision

Mantissa vs exponent


![alt text](../assets/590px-Float_example.svg.png "Single-Precision Floating-Point Representation")
![alt text](../assets/618px-IEEE_754_Double_Floating_Point_Format.svg.png "Double-Precision Floating-Point Binary Representation")


23 binary digits of precision for f32

56 binary digits of precision for f64


## Practice

### Constants
As great as it is to know what happens under the hood, it would be nice to have convenient ways to... 
- check what is the difference in precision for Pi as an `f32` vs an `f64`
- see what the minimum or maximum value for a given type is, like `i128` or `u128`.
- represent `infinity` or `negative infinity` or `NaN`

That's where constants come in. 

> fun fact: in the source code, NAN is represented as 0.0_f32/0.0_f32, INFINITY as 1.0_f32/0.0_f32, and NEG_INFINITY as -1.0_f32/0.0_f32
```rust

//TODO: Add examples
```

Check a given type's documentation to see what kind of constants they have available. 
For example, [`std::i128`](https://doc.rust-lang.org/std/i128/index.html) and [`std::f32`](https://doc.rust-lang.org/std/f32/consts/index.html)


### `isize` and `usize`
According to [The Rust Reference](https://doc.rust-lang.org/reference/types/numeric.html#machine-dependent-integer-types), these integer types are machine-dependent and use the same number of bits as that of a pointer in the machine. This makes `usize` and `isize` proportional to the size of the machine's address space, and this is determined by its architecture (x86-64, etc). These types can be 16-bit, 32-bit, or 64-bit depending on the machine. However, due to many pieces of Rust code assuming sizes of 32-bit or 64-bit (common pointer sizes in modern architectures), 16-bit support is limited.
The primary concerns around machine-dependent types are that of portability and security. 
By using `isize` or `usize` at inappropriate points in your code, you're introducing variation in how your code *could* operate. These could manifest as overflows on machines with 16-bit pointers due to the lack of support as mentioned at the beginning of this section or something less obvious (which is arguably worse). 

So when should I use them? 
After some a few posts on the [Rust User Forum](https://users.rust-lang.org), reddit, and stackoverflow, 
their usage tends to gravitate around two things:

- indexing into an array
- offsets

Even when it comes to the above, `usize` appears to be the dominant type used in these cases, with few general use-cases for `isize`.
> If you have any opinions on this or any major use-cases you think would be useful for others, please feel free to open an issue/pull-request/discussion!

You could argue that this narrow scope actually leads to further introspection when it comes to what you're writing.
It forces you to think more deeply about the bounds of the operations you'll be performing, and in doing so, make it easier to select the most appropriate type for the situation. 
From a holistic view, this makes your logic more robust and better aligned with the *spirit* of Rust.

Basically, stick to explicit integer types as much as possible and be wary when using machine-dependent integers outside of the narrow scope above.


### Conversion

#### Converting with `From`
For numeric types, `From` is only implemented for *lossless* conversions. 
```rust
let ten_as_i64 = i64::from(10_i32) // Ok!
let ten_as_i32 = i32::from(10_i64) // Panics!
```


#### Casting with `as`
`as` works for **both** *lossless* and *lossy* conversions.

When casting from one integer type to another integer type, with the same number of bits, it is a [no-op](https://en.wikipedia.org/wiki/NOP_(code)), as in nothing happens.
Unlike `From`, the true value of the integer is not maintained and the bits are merely interpreted as the destination type's encoding.
```rust
println!("128_u8 `as` i8 becomes {}", 128_u8 as i8);
// 128_u8 `as` i8 becomes -128
```

[Rust Reference on Type Casting](https://doc.rust-lang.org/1.49.0/reference/expressions/operator-expr.html#type-cast-expressions)

[Rust Reference on Casting Semantics](https://doc.rust-lang.org/1.49.0/reference/expressions/operator-expr.html#semantics)

https://doc.rust-lang.org/stable/rust-by-example/types/cast.html


### Handling Overflow
[Rust Reference on Overflow](https://doc.rust-lang.org/1.49.0/reference/expressions/operator-expr.html#overflow)

> Integer operators will panic when they overflow when compiled in debug mode. The -C debug-assertions and -C overflow-checks compiler flags can be used to control this more directly. The following things are considered to be overflow:
> - When +, * or - create a value greater than the maximum value, or less than the minimum value that can be stored. This includes unary - on the smallest value of any signed integer type.
> - Using / or %, where the left-hand argument is the smallest integer of a signed integer type and the right-hand argument is -1.
> - Using << or >> where the right-hand argument is greater than or equal to the number of bits in the type of the left-hand argument, or is negative.

> The following is a paraphrasing from [Myths and Legends about Integer Overflow in Rust](http://huonw.github.io/blog/2016/04/myths-and-legends-about-integer-overflow-in-rust/)

Rust's standard library provides four additional methods to handle bit overflows.
These explicit implementations give you precise control over your numerical operations to ensure a defined behavior.

- `wrapping_<add/sub/mul/div/etc...>`
- `saturating_<add/sub/mul/div/etc...>`
- `overflowing_<add/sub/mul/div/etc...>`
- `checked_<add/sub/mul/div/etc...>`
- `unchecked_<add/sub/mul/div/etc...>` // nightly-only (unchecked_math)

These methods will prevent panicking when overflow occurs and let you overflow purposefully if that's your intention (like in hashing algorithms and ring buffers).

```rust
//TODO: Elaborate on these further, especially in regard to signed integers`
```
- `wrapping_..` returns the straight two's complement result
- `saturating_..` returns the largest/smallest value (as appropriate) of the type when overflow occurs
- `overflowing_..` returns the two's complement result along with a boolean indicating if overflow occured
- `checked_..` returns an `Option` that's `None` if overflowing occurs
- `unchecked_..` assumes overflow cannot occur. Results in undefined behavior when `result > <int>::MAX || result < <int>::MIN`



```rust
//TODO: examples
```


#### Rust vs C/C++ on overflows
[Reddit: Why are i32s the Fastest?](https://www.reddit.com/r/rust/comments/931leq/why_are_i32s_the_fastest/e3a3eop?utm_source=share&utm_medium=web2x&context=3)

## Conclusion


## Further Reading
Further Reading and apis, endianness in Rust, Binary Multiplication and Division, bitwise operations 
