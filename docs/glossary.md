# Glossary

Terms as used in this project. Entries marked *(uncertain)* are fields or
terms whose exact expansion is not confirmed in the source conversations.

## Boot & exploit terms

- **DFU (Device Firmware Upgrade)** — Apple's lowest-level device mode:
  black screen, no iBoot, USB Product ID `0x1227`. The entry point for
  BootROM-level research.
- **Pwned DFU** — DFU mode after a successful checkm8 run
  (`PWND:[checkm8]`): signature checks are disabled, unsigned code can be
  executed.
- **SecureROM / BootROM** — The immutable first-stage bootloader burned into
  the SoC. Cannot be patched; a bug here is unpatchable.
- **checkm8** — Unpatchable BootROM exploit (axi0mX, 2019) affecting A5–A11
  chips. Heap overflow via malformed DFU USB request. Does **not** apply to
  A12 and later.
- **LLB (Low-Level Bootloader)** — Second boot stage, verified by SecureROM.
- **iBoot** — Apple's third-stage bootloader; also the recovery-mode
  environment that `irecovery` talks to.
- **iBSS / iBEC** — Shipped iBoot stages used to bootstrap a device from DFU
  into recovery (`irecovery -f`). RESEARCH variants are Apple's internal
  signed debug builds.
- **SEP** — Secure Enclave Processor; its firmware (`sep-firmware`) is a
  separate bootchain component.
- **NOR** — The flash memory holding bootloaders and firmware; dumpable and
  (dangerously) flashable from pwned DFU.
- **NVRAM** — Non-volatile RAM holding boot configuration variables.
- **alloc8 / 24kpwn** — NOR-resident exploits bundled with ipwndfu for
  persistent (tethered) exploitation on old devices.
- **`--demote`** — ipwndfu option enabling JTAG on supported targets.

## checkm8 device fingerprint fields

Printed by the exploit on every run, e.g.
`CPID:8747 CPRV:10 CPFM:03 SCEP:10 BDID:00 ECID:000003C107A529B5 IBFL:00`.

- **CPID** — Chip ID (`8747` = S5L8747, `8015` = A11/T8015).
- **ECID** — Exclusive Chip ID; unique per device.
- **BDID** — Board ID.
- **SRTG** — Firmware version tag, e.g. `[iBoot-1413.8]`.
- **CPRV / CPFM / SCEP / IBFL** *(uncertain)* — Additional fingerprint
  fields (chip revision / fabrication / security-domain / iBoot-flag
  related); exact expansions not confirmed in sources.

## Tooling terms

- **ipwndfu** — axi0mX's Python USB DFU exploitation tool. Python 2.7-only
  upstream; the `ipwndfu-haywire` fork adds A8/S5L8747 support.
- **palera1n** — checkm8-based jailbreak tool (v2.1-beta.2 used here);
  handles DFU entry, fakefs setup, and demotion on A11 iOS 15–16.
- **fakefs** — palera1n's fake filesystem used for rootful/SSV jailbreaks.
- **irecovery** — libimobiledevice tool for talking to iBoot/recovery mode:
  `-f` uploads a file, `-c` sends a command (e.g. `bootx`).
- **libusb / pyusb** — Userspace USB library and its Python binding;
  what ipwndfu uses to speak DFU.
- **Git LFS** — Git Large File Storage; ipwndfu's `bin/*.bin` exploit
  payloads are LFS-tracked.
- **DCSD** — Apple's diagnostic cable standard (e.g. HWTE Alex DCSD cable);
  exposes serial consoles, distinct from checkm8 USB exploitation.
- **GID / UID keys** — Apple's global and per-device AES keys used by
  ipwndfu's `--decrypt-gid` / `--decrypt-uid` options.
