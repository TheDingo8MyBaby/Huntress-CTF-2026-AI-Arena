<h1 align="center">⚙️ ELF Aware</h1>
<p align="center">
  <b>Huntress CTF 2026: AI Arena Write-Up</b>
</p>
<p align="center">
  <img src="https://img.shields.io/badge/Platform-Huntress_CTF-25BFCD?style=for-the-badge" />
  <img src="https://img.shields.io/badge/Category-Binary_Reverse_Engineering-orange?style=for-the-badge" />
  <img src="https://img.shields.io/badge/Difficulty-Intro-brightgreen?style=for-the-badge" />
  <img src="https://img.shields.io/badge/Solve-ELF_Static_Analysis-blue?style=for-the-badge" />
</p>
<p align="center">
  Three modes, seven assigned ELF files, and a readelf-to-objdump workflow
</p>

---

# 📋 Environment

| Item       | Value |
| ---------- | ----- |
| Event      | Huntress CTF 2026: AI Arena |
| Category   | Binary Reverse Engineering |
| Difficulty | Intro |
| Author     | John Hammond, and Just Hacking Training |
| Points     | 2 Single Solve · 3 Time Trial · 5 Mastery |
| Challenge  | `binary-reverse-engineering-day-01-c-elf-orientation` |
| Task       | Read ELF headers, section tables, symbols, and a function's return constant |
| Tools Used | Game Master proxy, `curl`, `unzip`, `readelf`, `objdump`, `sha256sum` |

---

# 🗺️ Overview

The prompt says:

> Before you can reverse anything, you have to learn to read. These plain C binaries are your primer: headers, section tables, symbol tables. Start with `readelf -h -S` and work your way down.

The task is static ELF inspection. `readelf -h` provides class, endianness, machine, type, and entry point. `readelf -S` shows the custom `.huntress` section and whether `.symtab` remains. `readelf -s` identifies the analysis function when symbols are present, and `objdump -d` reveals the immediate value returned by that function.

No ELF was executed.

---

# 📑 Table of Contents

1. [Reading the Prompt](#reading-the-prompt)
2. [Retrieving and Inspecting the Artifacts](#retrieving-and-inspecting-the-artifacts)
3. [Single Solve](#single-solve)
4. [Time Trial](#time-trial)
5. [Mastery](#mastery)
6. [Submitted Answers and Flags](#-submitted-answers-and-flags)
7. [Solution Summary](#solution-summary)
8. [Lessons and Takeaways](#lessons-and-takeaways)

---

# Reading the Prompt

Each mode asks for the same nine kinds of evidence: ELF class, byte order, architecture, object type, `.huntress` marker, `.symtab` presence, analysis symbol, the constant returned by `recover_code`, and entry point.

| Mode | Challenge ID | Assignment |
| ---- | ------------ | ---------- |
| Single Solve | `binary-reverse-engineering-day-01-c-elf-orientation` | One ELF in `evidence.zip` |
| Time Trial | `binary-reverse-engineering-day-01-c-elf-orientation/time_trial` | Four ELF slots in one `evidence.zip` |
| Mastery | `binary-reverse-engineering-day-01-c-elf-orientation/mastery` | Two new ELF tasks, one per evidence ZIP |

The challenge panel specifies a Time Trial target of **one task under 5 seconds** and a Mastery target of **two tasks under 5 seconds**. The successful Time Trial and Mastery start responses supplied server deadlines of **2026-10-01 16:54:46 UTC** and **2026-10-01 16:55:24 UTC**, respectively. The Game Master accepted both submissions before those deadlines.

---

# Retrieving and Inspecting the Artifacts

Start or resume the selected mode, then download its assigned archive through the artifact operation:

```sh
curl -sS \
  'http://10.0.0.219/huntress-ctf-proxy?op=artifact&challenge=binary-reverse-engineering-day-01-c-elf-orientation' \
  -o evidence.zip
unzip -l evidence.zip
unzip -p evidence.zip symbol-orientation.elf > symbol-orientation.elf
sha256sum evidence.zip symbol-orientation.elf
```

Inspect the ELF without running it:

```sh
readelf -h -S symbol-orientation.elf
readelf -s symbol-orientation.elf
readelf -p .huntress symbol-orientation.elf
objdump -d symbol-orientation.elf
```

For a function such as:

```text
0000000000401120 <recover_code>:
  401120: b8 39 05 00 00    mov $0x539,%eax
  401125: c3                ret
```

the x86-64 return value is the immediate `0x539`, or `1337` in base 10. If a slot has no `recover_code` symbol, submit the literal `none` for that field, as allowed by the question's answer format.

---

# Single Solve

The assigned `evidence.zip` was **1,905 bytes** and contained `symbol-orientation.elf` (**15,560 bytes**).

- **Download:** [evidence.zip](/api/huntress/game-master/download/d4449fe51118404cb182c1acb60269bffe30902ada224592a7c0d1a9ccc47cfa)
- **Archive SHA-256:** `90bb41ce9d55b924436486cb123db1d0a79a885697599511c44da5c336fad9b4`
- **ELF SHA-256:** `5098bda9538efc474e224fab8918dec9aa6f80996b213ffa78791aa923ebedbe`
- **Timer:** None

The file is ELF64, little endian, x86-64, and `ET_EXEC`. Its `.huntress` section contains `BR01-SYMBOL-ORIENTATION`; `.symtab` is present and exports `recover_code`. Disassembly shows it returns `1337`, and the entry point is `0x401030`.

| Answer ID | Submitted answer |
| --------- | ---------------- |
| `elf_class` | `ELF64` |
| `endianness` | `little` |
| `machine` | `x86-64` |
| `elf_type` | `ET_EXEC` |
| `marker` | `BR01-SYMBOL-ORIENTATION` |
| `has_symtab` | `true` |
| `exported_symbol` | `recover_code` |
| `recover_code` | `1337` |
| `entry_point` | `0x401030` |

The Game Master returned `CORRECT`.

---

# Time Trial

The successful Time Trial assigned a **7,451-byte** `evidence.zip` containing four ELF slots.

- **Download:** [evidence.zip](/api/huntress/game-master/download/aa13ca3a32884f30ba2eafca20631015b27258e3b3ad4c2dbd20f6896faf5e6b)
- **Archive SHA-256:** `e35bd4b002d44757745212ef4a3e0ef7b031f2c8ac2048c0311c1fd0b55902fd`
- **Server deadline:** 2026-10-01 16:54:46 UTC
- **Displayed target:** One task under 5 seconds
- **Agent-side start/download/analyze/submit elapsed:** 1.191 seconds

Slots 0 and 1 contained identical orientation binaries. Slot 2 used a different analysis symbol; slot 3 was an immediate-recovery variant. Answers were submitted under all 36 slot-prefixed IDs.

| Slot | ELF SHA-256 | Answer IDs and submitted values |
| ---- | ---------- | ------------------------------- |
| 0 | `5098bda9538efc474e224fab8918dec9aa6f80996b213ffa78791aa923ebedbe` | `slot0_elf_class` → `ELF64`; `slot0_endianness` → `little`; `slot0_machine` → `x86-64`; `slot0_elf_type` → `ET_EXEC`; `slot0_marker` → `BR01-SYMBOL-ORIENTATION`; `slot0_has_symtab` → `true`; `slot0_exported_symbol` → `recover_code`; `slot0_recover_code` → `1337`; `slot0_entry_point` → `0x401030` |
| 1 | `5098bda9538efc474e224fab8918dec9aa6f80996b213ffa78791aa923ebedbe` | `slot1_elf_class` → `ELF64`; `slot1_endianness` → `little`; `slot1_machine` → `x86-64`; `slot1_elf_type` → `ET_EXEC`; `slot1_marker` → `BR01-SYMBOL-ORIENTATION`; `slot1_has_symtab` → `true`; `slot1_exported_symbol` → `recover_code`; `slot1_recover_code` → `1337`; `slot1_entry_point` → `0x401030` |
| 2 | `fe00f21a2e5f24729b722f595398f68e9045602b78888b143594c3d61b045f89` | `slot2_elf_class` → `ELF64`; `slot2_endianness` → `little`; `slot2_machine` → `x86-64`; `slot2_elf_type` → `ET_EXEC`; `slot2_marker` → `BR01-SECTION-BEACON`; `slot2_has_symtab` → `true`; `slot2_exported_symbol` → `inspect_section`; `slot2_recover_code` → `none`; `slot2_entry_point` → `0x401030` |
| 3 | `1a77ad2615a5e7b2ae7deb48ac0071d525981b2c1d10065f95426a26208119d5` | `slot3_elf_class` → `ELF64`; `slot3_endianness` → `little`; `slot3_machine` → `x86-64`; `slot3_elf_type` → `ET_EXEC`; `slot3_marker` → `BR01-IMMEDIATE-RECOVERY`; `slot3_has_symtab` → `true`; `slot3_exported_symbol` → `recover_code`; `slot3_recover_code` → `7331`; `slot3_entry_point` → `0x401030` |

The Game Master returned `CORRECT` before the deadline.

---

# Mastery

Mastery assigned two new **evidence.zip** files, one per task. The first contained a stripped profile; the second contained an immediate-recovery ELF.

| Set | Download | Archive SHA-256 | ELF SHA-256 |
| --- | -------- | --------------- | ----------- |
| 1 | [evidence.zip](/api/huntress/game-master/download/5060237ec29547608a102263f6693b56cac7d4b192a0427285730cbec9cdb54f) | `0f1b9afeaef7d504563e460df9ee52ad7d010736429b5a1b137419c94f3f4978` | `0334283017d279785eebf542e3ff3f7ac3fad36c2db8a216499f31a1d8db4385` |
| 2 | [evidence.zip](/api/huntress/game-master/download/63597ac2a1694ac49257f442d6aa991a9048d6e8479f42aca0a46b743db79624) | `7151db62bad2b6865693a1e6dd9af63f8aaf14a6227a388eb44d1c7b3931ed84` | `1a77ad2615a5e7b2ae7deb48ac0071d525981b2c1d10065f95426a26208119d5` |

- **Server deadline:** 2026-10-01 16:55:24 UTC
- **Displayed target:** Two tasks under 5 seconds
- **Agent-side start/download/analyze/submit elapsed:** 1.408 seconds

Both ELF files were ELF64 little-endian x86-64 `ET_EXEC` files with entry point `0x401030`.

| Set | Marker | `.symtab` | Analysis symbol | `recover_code` | Submitted answer IDs |
| --- | ------ | --------- | --------------- | -------------- | -------------------- |
| 1 | `BR01-STRIPPED-PROFILE` | `false` | `none` | `none` | `set1.elf_class` → `ELF64`; `set1.endianness` → `little`; `set1.machine` → `x86-64`; `set1.elf_type` → `ET_EXEC`; `set1.marker` → `BR01-STRIPPED-PROFILE`; `set1.has_symtab` → `false`; `set1.exported_symbol` → `none`; `set1.recover_code` → `none`; `set1.entry_point` → `0x401030` |
| 2 | `BR01-IMMEDIATE-RECOVERY` | `true` | `recover_code` | `7331` | `set2.elf_class` → `ELF64`; `set2.endianness` → `little`; `set2.machine` → `x86-64`; `set2.elf_type` → `ET_EXEC`; `set2.marker` → `BR01-IMMEDIATE-RECOVERY`; `set2.has_symtab` → `true`; `set2.exported_symbol` → `recover_code`; `set2.recover_code` → `7331`; `set2.entry_point` → `0x401030` |

The Game Master returned `CORRECT` before the deadline.

---

# 🚩 Submitted Answers and Flags

The ELF properties above were the submitted answers. Each accepted mode returned a separate flag:

| Mode | Game Master result | Returned flag |
| ---- | ------------------ | ------------- |
| Single Solve | `CORRECT` | `aafeaaffe3c81e52a3d9329576b363b8` |
| Time Trial | `CORRECT` | `0b3558da6468f8248cc6c84819ca5471` |
| Mastery | `CORRECT` | `c332ba387cf5d1f512e34d9f63391ea8` |

---

# Solution Summary

```mermaid
flowchart TD
    A["Download the assigned evidence ZIP"] --> B["List its ELF files"]
    B --> C["Run readelf -h -S on each file"]
    C --> D["Read .huntress and check for .symtab"]
    D --> E["Read symbols with readelf -s"]
    E --> F["Use objdump -d to recover return constants"]
    F --> G["Submit every slot or set under its exact answer ID"]
    G --> H["Game Master returns CORRECT and a flag"]
```

---

# Lessons and Takeaways

* **Start with the ELF header.** Class, endianness, machine, object type, and entry point are all available before disassembly.
* **Inspect sections before symbols.** The custom `.huntress` marker and `.symtab` presence describe the binary's analysis state.
* **A stripped binary can still contain executable code.** If its symbol table is gone, use `none` for absent symbol fields and follow the instructions where code remains.
* **Read immediates in context.** On x86-64, a value loaded into `EAX` immediately before `ret` is returned in `RAX`.
* **Keep bundle slots separate.** Time Trial used four slot-prefixed groups; Mastery used two set-prefixed groups.
* **Never confuse artifact answers with flags.** Submit the ELF properties and constants; the Game Master returns the flag after validation.

---

Huntress CTF 2026: AI Arena • ELF Aware • Binary Reverse Engineering • 2 / 3 / 5 pts
