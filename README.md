# Haywire

Reproducing the published checkm8 SecureROM dump of Apple's Lightning Digital AV Adapter (S5L8747).

## What this is

In September 2019, axi0mX published **checkm8**, an unpatchable bootrom exploit affecting Apple's A5–A11-era chips and related S5L parts. Because the flaw lives in mask ROM, it can't be fixed in software — which is exactly what makes it valuable for legitimate security research: a stable, public window into how Apple's earliest boot code works.

The Lightning Digital AV Adapter is one of the more interesting checkm8 targets. It isn't a phone — it's a dongle with its own system-on-chip (S5L8747) running a minimal iOS. Community researchers (notably @a1exdandy's s5l8747x "Haywire" adaptation of the public checkm8 exploit) showed the adapter's SecureROM could be dumped just like any other vulnerable device.

This repo documents my independent reproduction of that published work — May 2025, on my own bench, with my own adapted tooling. Nothing here is a new exploit. The point was to prove I could take public research end-to-end — target acquisition, DFU, exploitation, extraction, verification — and to learn exactly where the published path stops.

## The bench

- **Host:** MacBook Air (macOS)
- **Target:** Apple Lightning Digital AV Adapter, enumerating in DFU as `CPID:8747 … SRTG:[iBoot-1413.8]` (device ECID redacted)
- **Tooling:** my own ipwndfu workspace (`ipwndfu-haywire-master`), ported to Python 3 and maintained for current macOS, plus the public `obscurantistic_checkm8` implementation — banner credits in its own output: checkm8 by @axi0mX, s5l8747x/Haywire implementation by @a1exdandy, libirecovery/iOS port by @nyan_satan
- **Observation:** DCSD serial cable on the bench (serial-port enumeration during setup)

Note: early notes misidentified the adapter's SoC; the device's own DFU-mode serial string (`CPID:8747`) settled it as S5L8747 before any exploitation ran.

## What worked

1. **Pwned DFU.** The public checkm8 flow ran clean against the adapter: USB device discovery → heap grooming (`leaking…`) → use-after-free trigger → payload delivery → `PWND:[checkm8]`, `exploit success!` in about 1.2 seconds.
2. **SecureROM dump.** With the device in pwned DFU, `./ipwndfu --dump-rom` produced `SecureROM-s5l8747xsi-1413.8-RELEASE.dump`.
3. **Verification.** `strings` against the dump recovers the ROM's own self-identification — `SecureROM for s5l8747xsi, Copyright 2011, Apple Inc.`, `RELEASE`, `iBoot-1413.8` — matching the version string the device reported in DFU. The dump is genuine.

## Where it stopped (documented failures)

- **NOR dump failed.** `--dump-nor` errored out: the pwned-DFU device had no matching configuration in the tooling.
- **iBoot / raw memory dumps failed.** The tooling exposes no iBoot-dump primitive, and direct `--dump=<address>,<length>` memory reads died inside the tool's struct packing. Raw `dd` against the USB device node was never a viable path either.
- **No interactive console.** Nothing in this work produced a working interactive console on the adapter.
- **No firmware modification.** Nothing was written back to the device — no NOR flash, no patches, no MFi changes. Read-only the entire way.

## What this is not

- Not a new vulnerability or exploit — checkm8 is @axi0mX's 2019 public research; the S5L8747 adaptation is @a1exdandy's public work. I reproduced it.
- Not a bypass tool and not firmware redistribution — there are no binaries, no dumps, no payloads, and no step-by-step exploitation instructions in this repo, and there never will be.
- Not a complete device compromise — one read-only ROM dump, plus a documented wall of failed follow-ons.

## Why it's here

"Hardware security research" gets thrown around a lot. This is the honest version: reproduce public work rigorously, verify your artifacts, document your failures as carefully as your successes, and touch nothing you don't own. Strictly white-hat, strictly read-only.
