# Quantum DevKit

Interactive toolkit of everyday developer utilities, written entirely in **Quantum** (`.sa`).

Menu-driven CLI covering encoding, hashing, text transforms, number bases, and JSON checks — no external language runtime beyond the Quantum interpreter.

## Requirements

- [Quantum Language](https://github.com/QuantumLogicsLabs/Quantum-Language) **v2.0.0** (or compatible)
- `qrun` on your `PATH` (Windows seed binary, or via Wine on Linux)

```bash
qrun --version
# Quantum Language v2.0.0
```

## Run

From the project root:

```bash
qrun devkit.sa
```

You get a numbered menu:

```
========================================
       Quantum DevKit v0.1
========================================

  1. JSON Formatter
  2. JSON Validator
  3. Base64 Encoder
  4. Base64 Decoder
  5. Hash Generator (SHA-256 / MD5)
  6. Text Analyzer
  7. Case Converter
  8. URL Encoder
  9. URL Decoder
 10. Number System Converter
  0. Exit
```

Choose an option, enter input when prompted, then press Enter to return to the menu.

## Features

| # | Feature | What it does |
|---|---------|----------------|
| 1 | **JSON Formatter** | Pretty-print (indent 2 or 4) or minify. Validates first and works on the original text, so numbers, key order and `\uXXXX` escapes are preserved. |
| 2 | **JSON Validator** | Strict check with **error position**. Rejects trailing/double commas, missing colons or values, bad numbers, bad escapes, raw control characters and trailing garbage. Reports the top-level type. |
| 3 | **Base64 Encoder** | Encode text → Base64 |
| 4 | **Base64 Decoder** | Decode Base64 → text (alphabet, length, padding checks) |
| 5 | **Hash Generator** | SHA-256 or MD5 hex digest |
| 6 | **Text Analyzer** | Character, word, line, sentence counts; most frequent word |
| 7 | **Case Converter** | UPPER, lower, Title Case, snake_case, camelCase |
| 8 | **URL Encoder** | Percent-encode (spaces, `&`, `=`, unicode, etc.) |
| 9 | **URL Decoder** | Percent-decode (rejects bad `%` sequences) |
|10 | **Number Converter** | Decimal ↔ binary ↔ hex (both directions; negatives; optional `0x` / `0b`) |

All errors are reported via `try` / `catch` / `throw` (no silent failures for invalid input).

## Project layout

```
Quantum-DevKit/
├── devkit.sa              # Entry point (menu loop)
├── README.md
├── CHECKLIST.md           # Verified feature status
├── lib/
│   ├── ui.sa              # Banner, menu, prompt, errors
│   ├── codec.sa           # Base64, URL, SHA-256, MD5
│   ├── text.sa            # Case conversion + text analysis
│   ├── numbers.sa         # Manual base conversion (no bin()/parseInt base)
│   └── jsonutil.sa        # Strict JSON validate + format/minify
├── tests/
│   ├── test_menu.sa
│   ├── test_codec.sa
│   ├── test_text.sa
│   ├── test_numbers.sa
│   ├── test_json.sa
│   └── test_integration.sa
└── audit/                 # Environment audit notes (Phase 1)
```

Modules are loaded with relative `# @use` directives, for example:

```sa
# @use ./lib/codec.sa
```

## Tests

From `tests/` (or with paths adjusted):

```bash
cd tests
qrun test_menu.sa
qrun test_codec.sa
qrun test_text.sa
qrun test_numbers.sa
qrun test_json.sa
qrun test_integration.sa
```

Known vectors used in tests include:

- `base64_encode("Man")` → `TWFu`
- `sha256("abc")` → `ba7816bf8f01cfea414140de5dae2223b00361a396177a9cb410ff61f20015ad`
- `md5("abc")` → `900150983cd24fb0d6963f7d28e17f72`

## Implementation notes

- **Pure `.sa`** — no other languages in the app.
- **Natives used where available**: `base64_encode` / `base64_decode`, `url_encode` / `url_decode`, `sha256`, `md5`.
- **JSON**: Quantum's native `JSON.parse` is permissive (accepts `{"a"}`, `[1,]`) and `JSON.stringify` alters data (`\u00e9` becomes `u00e9`, `1.0` becomes `1`, big integers become floats). So the DevKit does **not** use them: `lib/jsonutil.sa` has its own non-recursive validator and a pretty-printer/minifier that work on the original text.
- **Numbers**: `bin()` and `parseInt(base)` are not available in Quantum v2.0.0; conversion is implemented manually in `lib/numbers.sa`.
- **Reserved name**: the keyword `hash` is reserved — digests are exposed as `digest_sha256` / `digest_md5`.

## Known limits

- Integer conversion uses the VM numeric type; **very large integers may lose precision**.
- JSON `\uXXXX` escapes are validated and preserved as written (the DevKit does not decode them).
- The JSON validator checks syntax only (no duplicate-key or size checks).
- Interactive text analysis prompts for a **single line** (embedded `\n` in programmed tests still works).

## License

MIT — see [LICENSE](LICENSE).
