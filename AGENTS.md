# CSMP Agent Library Agent Guide

**Project:** Cisco CoAP Simple Management Protocol (CSMP) agent library and sample
**Languages:** C
**Build system:** GNU Make (target-specific makefiles)
**License:** See LICENSE file

## Overview

This repo implements a CSMP agent in C with an OSAL (Operating System Abstraction Layer) for multiple target platforms: Linux POSIX, FreeRTOS POSIX port, and Silicon Labs EFR32 Wi-SUN. The top-level `build.sh` drives the build.

## Repository Layout

```
.
├── Makefile              # Top-level Makefile: linux, freertos, efr32_wisun, clean
├── build.sh              # Convenience build driver (linux/freertos/clean)
├── linux.target          # Linux build rules
├── freertos.target       # FreeRTOS build rules
├── efr32_wisun.target    # Silicon Labs EFR32 Wi-SUN build rules
├── include/              # Public headers: csmp_info.h, csmp_service.h, iana_pen.h
├── src/                  # Library source
│   ├── coap/             # CoAP encoding/decoding
│   ├── csmpagent/        # CSMP agent logic and TLVs
│   ├── csmpapi/          # Public API implementation
│   ├── csmpservice/      # Service layer
│   ├── csmptlv/          # TLV serialization
│   └── lib/              # Internal utilities
├── sample/               # Sample application (CsmpAgentLib_sample)
├── test/                 # Sample PCAP and firmware images for testing
├── docs/                 # PDF guides and integration docs
├── osal/                 # OS abstraction layer
└── Vendors/              # Vendor-specific integration docs (Silabs, Renesas)
```

## Build Commands

### Linux

```bash
chmod +x build.sh
./build.sh linux
```

The sample executable is produced at `sample/CsmpAgentLib_sample`.

### FreeRTOS

```bash
git submodule update --init --recursive
./build.sh freertos
```

### Clean

```bash
./build.sh clean
```

## Running the Sample

```bash
# Minimal: FND IPv6 address is required
sample/CsmpAgentLib_sample -d <FND-IPv6-address>

# Full options
sample/CsmpAgentLib_sample -d 2020::2020 -min 10 -max 100 -eid 00173B1122334455
```

## Test / Debug

- Wireshark decode: `Analyze → Decode As → UDP port 61628 → CoAP`
- Enable debug output by adding `CFLAGS += -DPRINTDEBUG` in the target Makefile.
- Sample PCAPs are in `test/*.pcap`.

## Key Files

| Task | Location |
|------|----------|
| Public API | `include/csmp_service.h` |
| Sample app | `sample/CsmpAgentLib_sample.c` |
| TLV definitions | `src/csmpagent/tlvs/` |
| Build driver | `build.sh` |
| Linux rules | `linux.target` |
| FreeRTOS rules | `freertos.target` |

## Conventions

- Use `make -f <target>` or `build.sh <target>` for platform-specific builds.
- FreeRTOS requires submodule initialization.
- Sample binary name and static library names are platform-specific (`csmp_agent_lib.a`, `csmp_agent_lib_freertos.a`, etc.).

## Common Gotchas

- A valid FND IPv6 address must be supplied to run the sample.
- For non-Linux targets, set the correct `CC=` in the target Makefile.
- The `efr32_wisun` target needs the Simplicity SDK and a Wi-SUN border router; see `Vendors/Silabs/Readme.md`.
- Renesas Wi-SUN FAN integration notes live under `Vendors/Renesas/Readme.md`.
