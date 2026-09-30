# UED — Universal Entity Definition

A single-file registry of 64-bit identifiers for data types, real-world entities, formats, and relations.

## Format

Each line: `<16-hex-digit ID> "<name>"`

0000000100000029 "atom"
0000002900000001 "h"
0000002900000002 "he"

## ID Structure

| parent (32 bits) | item (32 bits) |
| 00000001 | 00000029 | → "atom"
| 00000029 | 00000001 | → "h" (child of atom)


Top-level entities have parent `00000001`.

## Properties

Entities are tagged with composable properties (`type`, `group`, `field`, `domain`, etc.) defined in the `prop` section.

## Coverage

Atoms · Scripts (ISO 15924) · Calendars · Countries · Currencies · Data types · File formats · Colors · Barcodes · Units · Time · Geo · and more.

## License

© A. Sobolev 2023–2026