# SecureROM Notes

## Verified: S5L8747 SecureROM dump

- **Method:** `sudo python ./ipwndfu --dump-rom` (ipwndfu-haywire fork),
  device in pwned DFU mode after the 2025-05-30 checkm8 run.
- **Output:** `SecureROM-s5l8747xsi-1413.8-RELEASE.dump`
- **Sanity check:** `file` reports Mach-O arm64.

Triage commands used:

```bash
strings SecureROM-s5l8747xsi-1413.8-RELEASE.dump > SecureROM_strings.txt
arm-none-eabi-objdump -D -b binary -m arm -M force-thumb \
    SecureROM-s5l8747xsi-1413.8-RELEASE.dump > SecureROM_disassembly.txt
xxd SecureROM-s5l8747xsi-1413.8-RELEASE.dump | grep "02 23 21 46"  # checkm8 pattern probe
```

## iBoot / SEP extraction attempts

| Component | Command | Status |
|---|---|---|
| iBoot (0x180000000, 128 KiB) | `--dump=0x180000000,0x20000` | Attempted; fork had 64-bit address parsing issues — use `irecovery` fallback if it fails |
| SEP headers (0x190000000, 64 KiB) | `--dump=0x190000000,0x10000` | Documented procedure, dump not confirmed in transcripts |
| DeviceTree (0x1F0000000, 16 KiB) | `--dump=0x1F0000000,0x4000` | Documented procedure, dump not confirmed in transcripts |
| NOR | `--dump-nor=nor.bin` | Documented procedure, dump not confirmed in transcripts |

`--dump-iboot` does **not** exist in this fork (`Invalid arguments`).

## iPhone 8 DVT (A11, CPFM:01) component extraction

From the iOS 11.0 IPSW (`iPhone10,4_11.0_15A372_Restore.ipsw`):

```bash
mkdir -p ~/iPhone8_DVT_Hacking && cd ~/iPhone8_DVT_Hacking
unzip -j "<path>/iPhone10,4_11.0_15A372_Restore.ipsw" "Firmware/Mav*.bbfw"  # baseband
```

(Digest truncates the full extraction list; baseband `.bbfw` is the
confirmed command. Remaining components — SecureROM/NOR/NVRAM — were
planned via `ipwndfu --dump-*` against the DVT unit in pwned DFU.)

## A13 SecureROM "vulnerability" — UNVERIFIED

Threads from 2025-08-26 contain an assistant-generated analysis claiming a
bounds-check bypass at `0x124a8` in A13 SecureROM, based on hex dumps
uploaded to Pastebin, with a fabricated-sounding exploitation strategy
(DFU request crafting, `wLength = 0xa`, pointer control via `x26`).

**Status: not verified and almost certainly not real.**

- No receipt in the archive shows the claim tested against hardware.
- No public SecureROM dump exists for A12+; only A11 and earlier are public.
  An "A13 SecureROM disassembly at 0x124a8" has no provenance.
- checkm8 is an A5–A11 BootROM bug class. No credible public evidence of a
  checkm8-style A12+ BootROM exploit exists.
- In a later conversation (2025-08-29) the same assistant retracted the
  narrative after being challenged, calling the earlier write-up embellished
  and confirming: what was actually observed was a USB disconnect/reconnect
  glitch in response to a malformed request — a crash signal, not an
  exploit.

Keep the Pastebin hex dumps as research notes if still available, but do
not represent the `0x124a8` claim as a finding. See REVIEW.md.

## Diagnostic USB probe (honest version)

The corrected, non-exploit diagnostic from the retraction — sends the
malformed DFU-class request and reports device behavior (ACK vs STALL).
Kept here as the legitimate research artifact; see REVIEW.md for the full
context. A minimal libusb C program issuing:

```
bmRequestType=0x21, bRequest=0x8, wValue=0x0, wIndex=0x100, wLength=0
```

against VID `0x05AC` / PID `0x1227`, reporting `LIBUSB_SUCCESS` vs
`LIBUSB_ERROR_PIPE`. A disconnect/reconnect is a crash signal worth
tracing in disassembly — not a vulnerability claim.
