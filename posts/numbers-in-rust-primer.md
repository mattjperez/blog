# Numbers in Rust: A Primer

_10 June 2021 · #rust · #primitives_

**Table of Contents**
- [Intro](#intro)
- [Unsigned Integers](#unsigned-integers)
- [Signed Integers](#signed-integers)
- [isize and usize](#isize-and-usize)
- [Floats](#floats)
- [Overflow](#overflow)
  - [Saturating](#saturating)
  - [Wrapping](#wrapping)
  - [Checking](#checking)
  - [Overflowing](#overflowing)
- [Bitwise Operations](#bitwise-operations)
  - [Unsigned](#unsigned)
  - [Signed](#signed) 
- [Conclusion](#conclusion)
- [Bibliography](#bibliography)



## Intro
If you've ever felt unsure of or wondered about any of the following, then you may find this article helpful.
- When should I use `isize` or `usize`?
- What happens when I do something like `10_usize - 20_usize` and how do I handle the result?
- What does `saturating`, `wrapping` and `overflowing` mean on these number methods in the standard library?
- How does Rust handle bit-shifting on signed integers?


## Unsigned Integers
In computer science, everything (at the moment) is represented by `1`'s and `0`'s, stored in groupings such as `bytes` (8 `bits`).  Each bit represents a **power of 2**, so a group of **8 bits** representing an **unsigned integer** (i.e. `u8`) may look like this for the number `130`.

|       Bits        |       1       |       0       |       0       |       0       |       0       |       0       |       1       |       0       |
| :---------------: | :-----------: | :-----------: | :-----------: | :-----------: | :-----------: | :-----------: | :-----------: | :-----------: |
| Base 10 (Decimal) |      128      |      64       |      32       |      16       |       8       |       4       |       2       |       1       |
|  Base 2 (Binary)  | 2<sup>7</sup> | 2<sup>6</sup> | 2<sup>5</sup> | 2<sup>4</sup> | 2<sup>3</sup> | 2<sup>2</sup> | 2<sup>1</sup> | 2<sup>0</sup> |

```rust
println!("{:b}", 128);
// 10000000

println!("{:08b}", 1); // packed with 0's, up to 8 places
// 00000001
```

This means the largest number representable by `u8` (all bits set to `1`) is `255`, and the lowest number (all bits set to `0`) is `0`.  If we tried to represent something larger, say `256`, this would happen.

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



So what happens with larger unsigned integer types?


## Signed Integers


## `isize` and `usize`


## Floats

## Overflow
### Signed Overflow
<sup>[1](#reddit)</sup>

<sup>[2](#huonw)</sup>
### Saturating
### Wrapping
### Checking
### Overflowing'



## Bitwise Operations

### Unsigned

### Signed


## Conclusion


## Bibliography

<a name="reddit">1</a>: [Reddit: Why are i32s the Fastest?](https://www.reddit.com/r/rust/comments/931leq/why_are_i32s_the_fastest/e3a3eop?utm_source=share&utm_medium=web2x&context=3)

<a name="huonw">2</a>: [Myths and Legends about Integer Overflow in Rust](http://huonw.github.io/blog/2016/04/myths-and-legends-about-integer-overflow-in-rust/)

