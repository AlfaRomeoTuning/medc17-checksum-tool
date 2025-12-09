# MEDC17 Checksum Tool

Checksum analyzer and corrector for Bosch MED17/EDC17 ECU firmware binaries.

## Overview

Analyzes and corrects checksums in Bosch MED17 and EDC17 ECU firmware files. Supports CRC32, ADD32, and ADD16 algorithms.

> [!WARNING]
> Currently tested primarily with calibration changes. Code section modifications may require additional validation.

## Features

- ✅ **Automatic block detection** - Identifies all checksum blocks in the binary
- ✅ **Three algorithms** - CRC32, ADD32, and ADD16 support
- ✅ **Instant CRC32 solving** - GF(2) matrix algebra
- ✅ **RSA signature forging** - Generates valid Bleichenbacher signatures
- ✅ **Safe operation** - Never overwrites original files

---

<div align="center">

[<img src="./hs-logo.png" width="50%">](https://howellsystems.co.uk)

</div>

### Need Help with Your VW Group ECU Project?

Got a custom ECU tool you want built? Need reverse engineering work done? Working on something ambitious for VW Group control units?

**[Howell Systems Ltd](https://howellsystems.co.uk)** provides professional reverse engineering and custom embedded software development for the automotive aftermarket. 

**[Get in touch →](https://howellsystems.co.uk)**



---

## Requirements

Python 3.7+ with `rich` package:
```bash
pip install rich
```

## Usage

### Analyze checksums (read-only)
```bash
python main.py firmware.bin
```

### Correct checksums
```bash
python main.py firmware.bin --correct --output firmware_corrected.bin
```

### Options
```
  -h, --help            Show help
  -c, --correct         Correct invalid checksums
  -o OUTPUT, --output   Output file path for corrected binary
```

## How It Works

### Checksum Block Structure
ECU firmware contains multiple Bosch blocks with checksums protecting different regions:
- **Absolute constants** (0xC0)
- **Customer Block** (0x30)
- **Application software** (0x40)
- **Dataset** (0x60)
- **Startup Block** (0x10)

### Two-Pass Correction
1. **Pass 1 (ADD32/ADD16)**: Modifies last 4 bytes of checksummed regions using simple arithmetic
2. **Pass 2 (CRC32)**: Uses GF(2) matrix algebra to solve for exact dCSAdjust value instantly

### CRC32 Mathematical Solving
Traditional methods brute-force billions of values. This tool models CRC32 as 32 linear equations over GF(2), constructs a 32×32 matrix, and solves using Gaussian elimination.

## Technical Details

| Algorithm | Initial Value | Expected Result | Method |
|-----------|---------------|-----------------|---------|
| **CRC32** | 0xFADECAFE | 0x35015001 | IEEE 802.3 (0xEDB88320) bit-reversed polynomial |
| **ADD32** | 0xFADECAFE | 0xCAFEAFFE | 32-bit dword sum (overflow ignored) |
| **ADD16** | 0xFADECAFE | 0xCAFEAFFE | Sum of 16-bit words from 32-bit dwords |

## TODO

Future enhancements planned:

- [ ] **Sync blocks** - WinOLS references these, what are they??
- [ ] **ECM/Code monitoring checksums** - Make sure all monitoring checksums are corrected
- [ ] **CVN (Calibration Verification Number) fixing**
- [ ] **Extended testing** - Validation with code section modifications beyond calibration changes

Contributions welcome!

## License

MIT License - see [LICENSE](LICENSE)

## Credits

Copyright (c) 2025 Connor Howell

## Disclaimer

> [!NOTE]
> For educational and research purposes. Use responsibly on firmware you own or have permission to modify. This implementation may not produce identical output to commercial tools like WinOLS.
