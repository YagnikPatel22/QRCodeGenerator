

---

# 👑 Understanding QR Code Generation in C++ with `qrcodegen`

> 🕒 A 20-minute walkthrough of how QR Code generation works under the hood using `qrcodegen.cpp`

---

## 📌 Introduction

- QR codes are 2D barcodes that encode data like text, URLs, etc.
- The `qrcodegen` C++ library by **Project Nayuki** is a compact, self-contained QR generator.
- This guide breaks down the **flow** and **key components** of the codebase.

---

## 🧠 Structure of the Library

The code is divided into:

- `qrcodegen.hpp`: Header file (declarations)
- `qrcodegen.cpp`: Source file (definitions)

### Core Classes:

- `QrSegment`: Encodes input into optimized bitstreams.
- `QrCode`: Converts segments into a scannable QR matrix.

---

## 🔠 `QrSegment` – Encoding Input Data

Responsibilities:

- Segment input by encoding type.
- Convert characters into binary form.

### Supported Modes:

- `NUMERIC` – Digits only (max compression)
- `ALPHANUMERIC` – Letters, digits, and a few symbols
- `BYTE` – Binary and UTF-8
- `KANJI`, `ECI` – Specialized uses

---

## 🧠 Segment Creation Functions

```cpp
makeNumeric("12345");
makeAlphanumeric("HELLO");
makeBytes(dataBytes);
```

Each creates:

- Mode indicator
- Character count
- Bitstream (`BitBuffer`)

The library auto-selects the most efficient mode for your input.

---

## 🧼 Calculating Total Bits

Checks if the content fits in a version:

```cpp
QrSegment::getTotalBits(segments, version);
```

- Ensures legal segment sizes
- Prevents overflow
- Helps version selection

---

## 🧱 `QrCode` – Building the QR

Main responsibilities:

- Arrange the grid
- Add error correction
- Apply optimal masking

```cpp
QrCode::encodeSegments(segments, QrCode::Ecc::LOW);
```

---

## 🧼 QR Version & Error Correction

Tries different versions until the content fits:

```cpp
for (version = min; version <= max; version++) {
    // Validate fit
}
```

ECC levels:

- LOW (7%)
- MEDIUM (15%)
- QUARTILE (25%)
- HIGH (30%)

---

## 🔲 Function Patterns

Decorative and reserved zones in the grid:

- 🔍 Finder patterns
- ➕ Alignment patterns
- ➖ Timing lines
- 🧠 Format & version info

```cpp
drawFunctionPatterns();
```

---

## 🧪 Error Correction with Reed–Solomon

Key step for reliability:

```cpp
reedSolomonComputeRemainder();
addEccAndInterleave();
```

- Converts data to a polynomial
- Divides to get ECC (remainder)
- Interleaves data + ECC blocks

---

## 🧵 Interleaving Blocks

Increases resilience:

- Split data into multiple blocks
- Add ECC to each
- Merge column-by-column

---

## 🖌️ Drawing Data on the Grid

```cpp
drawCodewords(codewords);
```

- Fills the grid from bottom-right
- Zigzag pattern
- Skips reserved function modules

---

## 🥸 Applying a Mask

Ensures visual readability:

```cpp
applyMask(maskPattern);
```

Prevents:

- Solid color blocks
- Repetitive patterns

Applies all 8 masks → evaluates → chooses best.

---

## 💅 Penalty Score – Choosing the Best Mask

```cpp
getPenaltyScore();
```

Lower score = better QR code.

Evaluates:

1. Long runs
2. 2×2 blocks
3. False finder patterns
4. Color balance

---

## 🎉 Final QR is Ready!

After applying the best mask and finalizing format bits, the QR code is ready to:

- 🖨️ Render as ASCII or image
- 📲 Be scanned by any QR reader
- 🔁 Export to external tools

---

## 🧠 Summary

✅ `QrSegment`: Encodes the input  
✅ `QrCode`: Builds the grid, adds ECC, applies masks  
✅ Uses **Reed–Solomon** for error correction  
✅ Applies masks and chooses best appearance  
✅ Ready for output, scanning, or printing

---

## 🔥 Bonus: Sample Usage

```cpp
auto qr = QrCode::encodeText("HELLO WORLD", QrCode::Ecc::LOW);
int size = qr.getSize();
for (int y = 0; y < size; y++) {
    for (int x = 0; x < size; x++)
        std::cout << (qr.getModule(x, y) ? "██" : "  ");
    std::cout << '\n';
}
```

🔳 Prints the QR in your terminal using full blocks.

---

## 👨‍💻 Author

**Yagnik N. Patel**  
📧 [yagnikptl22@gmail.com](mailto:yagnikptl22@gmail.com)  
🔗 [LinkedIn](https://www.linkedin.com/in/yagnik-n-patel)  

---

## 💡 Credits

- 💼 QR generation library by [Project Nayuki](https://www.nayuki.io/page/qr-code-generator-library)

---

Let me know if you'd like:
- GitHub Shields for stars/forks
- A downloadable `.pdf` or `.md` file
- ASCII + PNG side-by-side preview

Happy coding, Yagnik! 🚀
