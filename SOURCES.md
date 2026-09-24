# Sources

Every factual claim in the README traces to James Anderson's own chat messages
(REQUEST fragments) and pasted terminal output in his DeepSeek archive
(`~/workspace/deepseek-archive/conversations.json`, 965 conversations).
Assistant RESPONSE text was never treated as evidence. Device ECIDs are
redacted in the README; no passwords, keys, or personal data appear anywhere
in this draft.

## 20fe747d-77b3-41f4-aeee-fdd1e9e71fd3 — "Exploiting Apple A8 Chip with checkm8 Success" (2025-05-30)

The core session. All terminal output below was pasted by James himself.

- **Target identification:** req4 ("because its a lightning adapter thats why");
  req7 (the adapter "has a mini iOS inside")
- **SoC correction:** req2–req3 (an initial misidentification of the adapter's
  chip was corrected once the device reported `CPID:8747` — S5L8747)
- **checkm8 success:** req1 and req41 — device serial string
  `CPID:8747 … SRTG:[iBoot-1413.8] PWND:[checkm8]`, `exploit success!`
  (~1.2 s). req41's tool banner credits "checkm8 exploit by @axi0mX",
  "s5l8747x/Haywire implementation by @a1exdandy", "libirecovery/iOS port
  by @nyan_satan"
- **SecureROM dump:** req46 — `sudo python ./ipwndfu --dump-rom` →
  `Saved: SecureROM-s5l8747xsi-1413.8-RELEASE.dump`
  (run from his `ipwndfu-haywire-master` working directory)
- **Dump verification:** req53 (`strings …/SecureROM-s5l8747xsi-1413.8-RELEASE.dump
  | grep "iBoot-1413.8"` returned a hit); req55 (strings output shows
  `SecureROM for s5l8747xsi, Copyright 2011, Apple Inc.`, `RELEASE`,
  `iBoot-1413.8`)
- **Failed NOR dump:** req14 — `sudo ipwndfu --dump-nor=nor.bin` →
  `ERROR: Device seems to be in pwned DFU Mode, but a matching configuration
  was not found.`
- **Failed memory dumps:** req48, req50 —
  `--dump=0x180000000,0x20000` → `struct.error` traceback in `usbexec.py`
  (`cmd_memcpy`); req47 shows the tooling has no `--dump-iboot` primitive
  (`ERROR: Invalid arguments provided.`)
- **Failed raw reads:** req15, req17, req44 (`dd` against USB/serial device
  nodes failed)
- **DFU device identity:** req32, req33, req45 (ioreg / irecovery output:
  CPID 0x8747, iBoot-1413.8, DFU mode; ECID redacted in README)

## 48c20497-bf30-4d50-9d51-85252bc910fc — "macOS 15.4 ipwndfu Installation Guide" (2025-05-20)

- **Bench setup:** req6 (DCSD cable in use; `/dev/cu.usbserial-*` port
  enumeration); reqs 3–12 (bringing ipwndfu up on then-current macOS)

## c1bc709f-1455-4677-a4cb-82b924310af7 — "Update Python script for macOS compatibility" (2025-06-23)

- **Tooling adaptation:** req1 (porting his ipwndfu workspace from Python 2
  to Python 3 for macOS 15.5)

## Deliberately excluded

- Conversations `068f26ce`, `2e2266ed`, `ab6219f3`, `bda03c9b` (Aug 2025,
  iPhone 11 / A13 SecureROM exploit claims): **unverified** — the chat record
  itself flags the A13 material as unsubstantiated, and it falls outside the
  verified scope (reproduced checkm8 SecureROM dump of the Lightning AV
  adapter only; iBoot and other dumps failed). Nothing from these sessions
  appears in the README.
- Aspirational messages about MFi patches, firmware modification, or debug
  consoles: no verified successful outcome exists, so they are covered only
  by the README's "Where it stopped" / "What this is not" sections.
- Dump contents beyond the minimal self-identification strings, all hex
  dumps, all Pastebin material: never published, per the no-firmware rule.
- The Apple certificate PEM listing pasted in req5 of the core session:
  irrelevant to the project; omitted.
