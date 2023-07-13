# templates for format! println! etc
- see [`std::fmt`](https://doc.rust-lang.org/stable/std/fmt/index.html) doc

```
Template                 | string Argument list       | Result
-------------------------|----------------------------|--------------------------------
"number of {}: {{ {} }}" | "elephants", 19            | "number of elephants: { 19 }"
"from {1} to {0}"        | "the grave","the cradle"   | "from the cradle to the grave"
"v = {:?}"               | vec![0,1,2,5,12,29]        | "v = [0, 1, 2, 5, 12, 29]"
"name = {:?}"            | "Nemo"                     | "name = \"Nemo\""
"{:8.2} km/s"            | 11.186                     | " 11.19 km/s"
"{:20} {:02x} {:02 x}"   | "adc #42", 105, 42         | "adc #42 69 2a"
"{1:02x} {2:02x} {0}"    | "adc #42", 105, 42         | "69 2a adc #42"
"{:02?}"                 | [110, 11, 9]               | "[110, 11, 09]"
"{:02x?}"                | [110, 11, 9]               | "[6e, 0b, 09]"
"{ls:02x}{ms:02x}{isn}"  | isn="adc #42",ls=105,ms=42 | "69 2a adc #42"

Features in use                           | Template string     | Result             |
-------------------------------------     | -----------------   | ----------------   |
Default                                   | "{}"                | "bookends"         |
Minimum ﬁeld width                        | "{:4}"              | "bookends"         |
                                          | "{:12}"             | "bookends    "     |
Text length limit                         | "{:.4}"             | "book"             |
                                          | "{:.12}"            | "bookends"         |
Field width, length limit                 | "{:12.20}"          | "bookends    "     |
                                          | "{:4.20}"           | "bookends"         |
                                          | "{:4.6}"            | "booken"           |
                                          | "{:6.4}"            | "book  "           |
Aligned left, width                       | "{:<12}"            | "bookends    "     |
Centered, width                           | "{:^12}"            | "  bookends  "     |
Aligned right, width                      | "{:>12}"            | "    bookends"     |
Pad with '=', centered, width"            | {:=^12}"            | "==bookends=="     |
Pad '*', aligned right, width, limit      | "{:*>12.4}"         | "********book"     |
----------------------------------------  | ------------------- | ------------------ |
Default                                   | "{}"                | "1234"             |
Forced sign                               | "{:+}"              | "+1234"            |
Minimum ﬁeld width                        | "{:12}"             | "        1234"     |
                                          | "{:2}"              | "1234"             |
Sign, width                               | "{:+12}"            | " +1234      "     |
Leading zeros, width                      | "{:012}"            | "000000001234"     |
Sign, zeros, width                        | "{:+012}"           | "+00000001234"     |
Aligned left, width                       | "{:<12}"            | "1234        "     |
Centered, width                           | "{:^12}"            | "    1234    "     |
Aligned right, width                      | "{:>12}"            | "        1234"     |
Aligned left, sign, width                 | "{:<+12}"           | "+1234       "     |
Centered, sign, width                     | "{:^+12}"           | "   +1234    "     |
Aligned right, sign, width                | "{:>+12}"           | "       +1234"     |
Padded with '=', centered, width          | "{:=^12}"           | "====1234===="     |
Binary notation                           | "{:b}"              | "10011010010"      |
Width, octal notation                     | "{:12o}"            | " 2322"            |
Sign, width, hexadecimal notation         | "{:+12x}"           | " +4d2"            |
Sign, width, hex with capital digits      | "{:+12X}"           | " +4D2"            |
Sign, explicit radix preﬁx, width, hex    | "{:+#12x}"          | " +0x4d2"          |
Sign, radix, zeros, width, hex            | "{:+#012x}"         | "+0x0000004d2"     |
                                          | "{:+#06x}"          | "+0x4d2"           |
Default                                   | "{}"                | "1234.5678"        |
Precision                                 | "{:.2}"             | "1234.57"          |
                                          | "{:.6}"             | "1234.567800"      |
Minimum ﬁeld width                        | "{:12}"             | " 1234.5678"       |
Minimum, precision                        | "{:12.2}"           | " 1234.57"         |
                                          | "{:12.6}"           | " 1234.567800"     |
Leading zeros, minimum, precision         | "{:012.6}"          | "01234.567800"     |
Scientiﬁc                                 | "{:e}"              | "1.2345678e3"      |
Scientiﬁc, precision                      | "{:.3e}"            | "1.235e3"          |
Scientiﬁc, minimum, precision             | "{:12.3e}"          | " 1.235e3"         |
                                          | "{:12.3E}"          | " 1.235E3"         |


| Example   | Trait              | Purpose                                      |
|-----------|--------------------|----------------------------------------------|
| {}        | std::fmt::Display  | Text, numbers, errors: the catchall trait    |
| {bits:#b} | std::fmt::Binary   | Numbers in binary                            |
| {:#5o}    | std::fmt::Oct      | Numbers in octal                             |
| {:4x}     | std::fmt::LowerHex | Numbers in hexadecimal, lowercase digits     |
| {:016X}   | std::fmt::UpperHex | Numbers in hexadecimal, uppercase digits     |
| {:.3e}    | std::fmt::LowerExp | Floating-point numbers in scientiﬁc notation |
| {:.3E}    | std::fmt::UpperExp | Same, uppercase E                            |
| {:#?}     | std::fmt::Debug    | Debugging view, for developers               |
| {:p}      | std::fmt::Pointe   | Pointer as address, for developers           |

```
