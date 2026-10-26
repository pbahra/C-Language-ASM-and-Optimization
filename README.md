# Hex Dump Utility & Compiler Optimization Analysis

A C utility that reads a binary file and prints it in a readable format, plus a line-by-line analysis of the x86-64 Assembly GCC generates at -O0, -O1, and -O3.

## What It Does
Each output line shows:
- the byte offset in hex
- 16 bytes of data in hex
- the same bytes as ASCII (non-printable characters shown as `.`)

## Build & Run
```bash
gcc -O0 -o hexdump hexdump.c
./hexdump binary.out
```

Generate the Assembly for each optimization level:
```bash
gcc -O0 -S -o hexdumpO0.s hexdump.c
gcc -O1 -S -o hexdumpO1.s hexdump.c
gcc -O3 -S -o hexdumpO3.s hexdump.c
```

## Key Findings
| Level | Assembly lines | Stack usage | Main changes |
|-------|---------------|-------------|--------------|
| -O0 | 267 | ~144 bytes | 3 separate functions; variables reloaded from memory every iteration; `__ctype_b_loc()` called once per character |
| -O1 | 206 | 72 bytes | Functions inlined into one; values kept in registers; `__ctype_b_loc()` hoisted out of the loop |
| -O3 | 214 | 72 bytes | Loops aligned to 16-byte boundaries; minor instruction scheduling |

**Takeaway:** Most gains came from -O0 → -O1; -O3 added little because the program is I/O-bound.

Full write-up: [analysis.md](analysis.md)
