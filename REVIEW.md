# REVIEW — Haywire Technical Review

Method: 30 project conversations re-read; commands cross-checked against
each other and against tool behavior; assistant claims verified against
receipts (terminal output pasted by the researcher). Anything without a
receipt is marked as such.

## Verified (receipt-backed)

1. **checkm8 on S5L8747, 2025-05-30.** Terminal transcript shows
   `PWND:[checkm8] / exploit success! / took 1.17s` for
   `CPID:8747 … SRTG:[iBoot-1413.8]`. The strongest claim in the project
   and the one with direct evidence.
2. **SecureROM dump.** Transcript shows `sudo python ./ipwndfu --dump-rom`
   producing `SecureROM-s5l8747xsi-1413.8-RELEASE.dump` from the
   `ipwndfu-haywire-master` directory.
3. **Python 2.7 resurrection on macOS.** The manual
   `python-2.7.18-macosx10.9.pkg` install, `get-pip.py` for 2.7, and
   `pip2 install pyusb` sequence appears as executed commands with
   successful version output.
4. **palera1n v2.1-beta.2 help output and flags** (`-D -E -C -c -d`,
   the `-f`/`-l` requirement error) — pasted verbatim from the tool.
5. **ipwndfu-haywire usage text** — pasted verbatim; the option set in
   `docs/checkm8-av-adapter.md` §4 is transcribed from it, including the
   absence of `--dump-iboot`.
6. **Git LFS cause of missing `bin/*.bin`** — the pointer-file diagnosis
   is consistent with the repo layout; `git lfs pull` is the correct fix.
7. **n104 RESEARCH upload order** — the iBSS→iBEC→LLB→iBoot→DeviceTree→
   sep→kernel→ramdisk→`bootx` sequence appears as executed `irecovery`
   commands with progress output.

## Corrected during reconstruction

1. **`brew install python@2`** — recommended repeatedly by the assistant
   *after* the researcher showed the formula doesn't exist. Corrected to
   the manual 2.7.18 pkg install (docs/toolchain.md §1).
2. **`--dump-iboot`** — suggested by the assistant; does not exist in the
   haywire fork (errors `Invalid arguments provided`). Corrected to
   `--dump=address,length`.
3. **`checkm8.exploit(script_dir)`** — the assistant's own earlier edit
   introduced a call with an argument the function doesn't take.
   Corrected to `checkm8.exploit()`.
4. **`chmod` of `/dev/bus/usb/...`** — Linux-only path used in a macOS
   command; corrected to `system_profiler`-based identification + `sudo`.
5. **checkra1n for the AV adapter** — suggested post-PWND; checkra1n does
   not support the S5L8747 accessory. Removed; ipwndfu is the tool.
6. **A12Z DTK confusion** — the adapter's ECID resembled a DTK unit's;
   `CPID:8747` settles it as S5L8747. Corrected in docs.
7. **`sudo ./ipwndfu --boot-command="bgcolor 255 0 255"`** — suggested for
   a "purple diagnostic console"; `bgcolor` is not an ipwndfu option and
   the diagnostic-console framing was fabricated. Removed.
8. **README date range** — project threads span 2025-04-18 → 2025-12-31
   (plus one unrelated 2026-06-30 conversation, excluded).

## Unverifiable / flagged claims

1. **A13 SecureROM vulnerability (`0x124a8` bounds-check bypass).**
   NOT VERIFIED — see `docs/secure-rom-notes.md`. No hardware receipt;
   the narrative was retracted in-conversation (2025-08-29) after
   challenge. Do not cite as a finding.
2. **"New checkm8 for A13" framing.** No public evidence supports a
   checkm8-class A12+ BootROM exploit. The observed USB
   disconnect/reconnect is a crash signal, not an exploit primitive.
3. **iBoot/SEP/DeviceTree/NOR dumps beyond SecureROM.** Commands are
   documented but no success receipts appear in the transcripts. Marked
   as "documented procedure, dump not confirmed" in the docs.
4. **Pastebin SecureROM hex dumps** referenced for the A13 analysis —
   links not verified live; treat as unavailable.
5. **DCSD-to-checkm8 cable conversion** — the conversation contains only
   generic guidance, no executed procedure. Not documented as a procedure.

## Open questions

- Which upstream repo is `ipwndfu-haywire` forked from, and is it still
  available? (Canonical axi0mX/ipwndfu is the fallback; A8 support would
  need re-verification.)
- Was the iBoot dump at `0x180000000` ever completed, or did the 64-bit
  address parsing issue block it permanently?
- Do the `SecureROM-s5l8747xsi-1413.8-RELEASE.dump` checksums still exist
  anywhere (Dropbox? local disk)? The dump's preservation state is unknown.

## Hygiene notes

- One transcript contains a **plaintext sudo password** typed at a
  `Password:` prompt. It is not reproduced anywhere in this repo. Rotate
  any credential that was ever pasted into a chat.
- Hostnames and usernames from terminal prompts were anonymized in all
  docs. No personal names or emails appear in this repo.
- The `True Certs` PEM listing is host-side Apple corporate certificate
  material, not device-extracted secrets; it is described, not included.
- **Image-generation note:** the toolchain setup-pipeline diagram
  (macOS → Python 2.7 → pyusb → sed migration → LFS) could not be
  generated — the image pipeline declined that request. The procedure is
  fully documented in text in `docs/toolchain.md` §§1–4 instead.
  AI-generated diagrams were manually verified for legibility and
  spelling; the AV-adapter illustration includes board details
  (DRAM/PMIC callouts) that are illustrative, not sourced — the verified
  facts on it are the S5L8747 / CPID:8747 / iBoot-1413.8 labels.
