# Quantum DevKit — Feature Checklist

Only mark **done** after real `qrun` verification with captured output.

| # | Feature | Status | Notes |
|---|---------|--------|-------|
| A | UI + main menu skeleton | **done** | banner, print_menu, prompt, pause, show_error |
| B | Base64 Encoder | **done** | "Man" → TWFu |
| B | Base64 Decoder | **done** | TWFu → Man; length/padding/alphabet checks |
| B | URL Encoder | **done** | spaces/&/= + unicode |
| B | URL Decoder | **done** | round-trip; rejects bad % sequences |
| B | Hash Generator (SHA-256/MD5) | **done** | known vectors; no reserved `hash` name |
| C | Case Converter | **done** | upper, lower, title, snake, camel |
| C | Text Analyzer | **done** | chars, words, lines, sentences, top word freq |
| D | Number System Converter | **done** | dec/bin/hex both ways; negatives; invalid rejected |
| E | JSON Validator | **done** | own state-machine check + position; matches Python strict json.loads on 584 mutated cases |
| E | JSON Formatter | **done** | pretty (indent 2/4) + minify on original text; keeps numbers, key order, \u escapes; empty {} / [] inline |

## Tests (all verified 2026-11-11)
- `tests/test_menu.sa`
- `tests/test_codec.sa`
- `tests/test_text.sa`
- `tests/test_numbers.sa`
- `tests/test_json.sa` (87 assertions, prints PASS/FAIL summary)
- `tests/test_integration.sa` — one call per feature surface

## Project layout
```
Quantum-DevKit/
  devkit.sa          # entry: menu loop
  CHECKLIST.md
  lib/
    ui.sa
    codec.sa         # base64, url, sha256, md5
    text.sa          # case + analyzer
    numbers.sa       # base conversion
    jsonutil.sa      # validate + format
  tests/
  audit/
```

## Known limits
- Number conversion uses floating numeric type; very large integers may lose precision.
- JSON `\uXXXX` is accepted structurally; native parse may not fully decode all escapes.
- Text analyzer is single-line oriented for interactive input (multi-line via `\n` in test strings works).
