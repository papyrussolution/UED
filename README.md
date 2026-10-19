# Universal Entity Definition (UED)

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Format: Plain Text](https://img.shields.io/badge/Format-Plain%20Text-green.svg)](#file-format)
[![Integrity: SHA--256](https://img.shields.io/badge/Integrity-SHA--256-orange.svg)](#versioning--integrity)

**Universal Entity Definition (UED)** is a flat-file, 64-bit universal type and entity registry. It provides an immutable single source of truth (SSOT) for common real-world concepts, physical units, data formats, and compact semantic types across multiple programming languages.

---

## Why UED?

In modern software development, integrating standard reference data (currencies, countries, file formats, units of measure, barcode standards) usually requires dozens of disparate libraries, ad-hoc JSON files, or custom enums.

UED solves this by providing:
1. **Single Source of Truth:** A single text file compiles directly into native C/C++ headers and Java definitions.
2. **Zero-Overhead Runtime:** All entities are 64-bit integers (`uint64_t`). Comparisons happen in a single CPU cycle (`cmp reg, reg`) without string allocations or runtime parsing.
3. **Strict Immutability:** Once an ID or mnemonic is assigned to an entity, it never changes or gets removed.
4. **Strong Semantic Typing:** Differentiates semantically distinct entities (e.g., GLN vs GTIN-13) and packs dynamic values (colors, license plates, timestamps, geolocations) into 64 bits.

---

## 64-Bit ID Anatomy

An identifier is always an unsigned 64-bit integer where the high bits represent the **meta-type (category)** and the low bits represent the **payload / item ID**:

```
+-----------------------------------+-----------------------------------+
|       Meta-Type / Category        |         Payload / Item ID         |
+-----------------------------------+-----------------------------------+
```

The split dynamically adapts based on payload size:
* **32-bit Meta / 32-bit Payload (Standard Enums):** 
  * `000000490000007F` &rarr; `"webp"` (Data format)
  * `0000003F00000074` &rarr; `"jp"` (Country: Japan)
* **32-bit Meta / 32-bit Payload (Predefined Consts with Packed Values):**
  * `0000004FFFBDB76B` &rarr; `"darkkhaki"` (Color with packed ARGB: `0xFFBDB76B`)
* **16-bit Meta / 48-bit Payload (Packed High-Level Types):**
  * `40A230405AB1C03E` &rarr; Packed Russian vehicle license plate `"У114АХ750"`
* **8-bit Meta / 56-bit Payload (Continuous Types, up to 127 base types):**
  * Geolocation coordinates, timestamps, 56-bit custom decimal floating point numbers (`decimal`).

---

## File Format

UED uses a minimalist, line-oriented plain-text syntax:

### 1. Basic Declaration
`<64-bit-hex-id> "<mnemonic>"`
```text
0000000100000029 "atom"       // Category: Chemical element
000000290000001a "fe"         // Element: Iron (child of atom)
0000000100000041 "currency"   // Category: Currency
0000004100000348 "usd"        // US Dollar
```

### 2. Localization (i18n)
`<64-bit-hex-id> <lang-code> "<localized-name>"`
```text
0000003f00000074 ru "Япония"
0000003f00000074 it "Giappone"
0000003f00000074 en "Japan"
```

---

## Repository Structure

| File | Description |
| :--- | :--- |
| **`UED.TXT`** | Human-editable source registry with comments, taxonomy, and relations. |
| **`ued-id.dat`** | Canonical compiled distribution (stripped comments, normalized formatting, verified by SHA-256). |
| **`ued-id.h`** | Generated C / C++ header file (`#define UED_... 0x...ULL`). |
| **`ued-id.java`** | Generated Java definitions (`public static final long UED_...`). |

---

## Usage in Code

### C / C++
```c
#include "ued-id.h"

// Instant 1-cycle comparison, zero memory overhead
if (entity.type == UED_ATOM_FE) {
    // Process Iron
}

if (unit.dimension == UED_DIMENSION_ACCELERATION) {
    // Process physical unit
}
```

### Java
```java
if (file.getFormat() == Ued.DATAFORMAT_WEBP) {
    // Process WebP image
}
```

---

## Domain Coverage

* **Fundamental Science:** Chemical Elements (1–118), Physical Dimensions, Standard & Historic Units of Measure (UOM).
* **International Standards:** ISO 15924 (Scripts), ISO 3166 (Countries), ISO 4217 (Currencies), Calendars, Languages & Locales.
* **Computer Culture:** File formats (media, binary, source codes), Data types (endianness-aware integers/reals), CPU architectures, CSS/RGB colors.
* **Logistics & Commerce:** Barcode symbologies (EAN, UPC, Code128, QR, Aztec, DataMatrix), GLN, National tax identifiers (RU INN, KPP, SNILS, CL RUT, AR DNI), Packaging types.
* **Time & Coordinates:** SDN Gregorian dates, UTC/Timezone timestamps, 2D geolocation coordinates.

---

## Tooling & Compiler

The canonical compiler and verification tools are maintained as part of the [OpenPapyrus](https://github.com/papyrussolution/OpenPapyrus) ecosystem. The compiler validates strict backwards compatibility, checks SHA-256 integrity, and generates header files for target programming languages.

### Contributing & Porting
Contributions to add missing global standards or to build code generators for other languages (**Rust, Go, Python, C#, TypeScript**) are very welcome!

---

## License

This project is open-source and free to use in both commercial and non-commercial projects. See [LICENSE](LICENSE) for details.

© A. Sobolev (2023–2026)