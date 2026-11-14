# FAILED_RESEARCHING

> Three Nikon firmware research lines that are completely untenable · November 2026
>
> [中文](README.md) | English

**No firmware, keys, decrypted images, or flashable files are included.** This
repository keeps only the three cases that the evidence disproved most
thoroughly: why they fail, how far the disproof goes, and what a real
implementation would cost.

---

## 1. Scope: what counts as "completely untenable"

Only one kind of conclusion is kept here: **static evidence or hardware testing
is enough to reject "change data / reuse an existing path" as a way to reach the
goal.**

Excluded by definition:

- lines that are blocked on an external input but still directionally sound
  (QEMU boot, lens driver);
- lines that are only partially closed (the L distortion-data sign ambiguity);
- lines that succeeded (D7200 1080p high bitrate).

Under this rule, only 3 of the 8 research lines from November 2026 qualify:

| # | Line | Goal | Disproof | Details |
| --- | --- | --- | --- | --- |
| 1 | **D750 TIF storage** | Add TIFF (RGB) to the stills menu | No TIFF encoder and no menu text in any language; only a filename template and EXIF/XMP parsing tables | [01](projects/01-d750-tif.md) |
| 2 | **D750 AVI / 320×240 30i** | Add AVI recording and interlaced low-res | No AVI/RIFF writer, no MJPEG path, no 320×240 or interlaced mode; only MOV/H.264 | [02](projects/02-d750-avi.md) |
| 3 | **D7200 640×424 30p** | Repurpose the 1080p30 slot to record 640×424 | All three edits failed on hardware: striped garble, 1080p60 instead, still striped; the encoder likely has no such mode | [03](projects/03-d7200-640x424.md) |

### The boundary of "untenable"

What is rejected is the **"static patch / reuse existing data" path**, not
"this can never be done".

- TIF and AVI would need new encoders/container writers, capture pipeline work,
  menu items, and persistence — a feature port, not a byte edit;
- 640×424 would need encoder/scaler changes or a different capture path — not a
  table edit either.

So the precise claim is: **without changing code, using only existing data,
these three are completely untenable.**

---

## 2. The three cases

### 2.1 D750 TIF: a string is not an encoder

The firmware does contain `.TIF`, but none of the hits means "can shoot TIFF":

- `0x126b4b6` `DSC_0000.TIF` is a DCF filename template;
- `0x1bf9ec` `JPG\0TIF\0` is an XMP metadata extension table;
- `0x11edf40` is a TIFF/EXIF tag table for **reading** metadata, not a pixel
  encoder.

D810/D850/D5 also carry `DSC_0000.TIF` while supporting `TIFF (RGB)` — the
template is platform-wide and proves nothing about the current body. D750 also
has no `TIFF`/`TIF` menu text in any language.

**Disproof: no encoder + no menu text + no settings persistence.** See
[projects/01-d750-tif.md](projects/01-d750-tif.md).

### 2.2 D750 AVI: a capability bit is not a muxer

`.AVI` appears only in the file-type registry (ID 5, capability bit `0x10`);
its accessors are just `ldrb [r0,#0x29]` and bit set/clear — not an AVI muxer.

Four layers are missing:

1. no AVI/RIFF writer (`avih/movi/idx1/LIST/strh/strf/hdrl/00dc` all zero hits;
   only a WAV `RIFF` template);
2. no Motion-JPEG video path (`MJPG`/`MJPEG` zero hits); only MOV/H.264;
3. no 320×240 or interlaced movie mode (only 1080p/720p/640×424 progressive);
4. no `Movie file type` menu and no `AVI` menu text.

**Disproof: no writer + no encoding path + no capture mode + no menu.** See
[projects/02-d750-avi.md](projects/02-d750-avi.md).

### 2.3 D7200 640×424: three index spaces

The movie-mode table really does contain 640×424 30p (entries 19/20), and the
mapping function has a group-2 branch. All three edits were rejected on
hardware:

| Version | Edit | Hardware result |
| --- | --- | --- |
| v1 | replace table records only | container says 640×424 / 29.97p and timing is right, but the image is regularly striped |
| v2 | mapping function returns native index `0x12/0x13` | records 1920×1080 59.94p; 10 s becomes 4.8 s |
| v3 | v1 + scaler-size field `0x2C46A4` | display says 640×424, still striped |

**Disproof: mode table, encoder index, and buffer stride are three different
index spaces; fixing one does not fix the others.** See
[projects/03-d7200-640x424.md](projects/03-d7200-640x424.md).

---

## 3. The common pattern

Three features, one mistake: **treating "a string/table entry exists" as "a
functional path exists".**

Before patching, ask in order:

1. **Is there a writer/encoder?** Extensions, file-type registries, and
   capability bits do not count;
2. **Is there a capture/scaler path?** A mode-table row or a resolution constant
   does not count;
3. **Is there menu text + descriptor + enum + persistence?** A label alone does
   not count;
4. **Is playback/filesystem compatibility present?** Being able to write a file
   does not count;
5. **Is the change data or code?** Is there an existing record to reuse?
6. **Can it be falsified cheaply on hardware?** Flash the stealth build first;
   inspect the file, not the menu.

If any of 1–4 is missing, the cost jumps from "a few bytes" to "port a feature";
at that point "untenable" refers to the patch path, not the feature itself.

---

## 4. Why only these three

The other five lines are outside this scope and are not expanded here:

- 3 blocked on external inputs (D750 QEMU, D780 QEMU, LiveView AF/lens link);
- 1 partially closed (Nikon L distortion-data sign ambiguity);
- 1 verified success (D7200 1080p 64/60 Mbps).

Their directions were not disproved; they lack data, lack hardware input, or
already shipped. Including them would dilute the "completely untenable" claim.

---

## 5. Layout

```text
FAILED_RESEARCHING/
  README.md                    Chinese master document
  README.en.md                 This file
  TIMELINE.md                  Timeline for these three cases
  NOTICE.md                    No-firmware statement, trademarks, references
  LICENSE                      CC BY 4.0
  projects/
    01-d750-tif.md             D750 TIF: no encoder
    02-d750-avi.md             D750 AVI: no writer
    03-d7200-640x424.md        D7200 640×424: three hardware failures
```

Project notes are in Chinese; this README is the English overview.

---

## 6. Legal

- This repository contains **no** Nikon firmware, keys, decrypted modules,
  patched images, or flashable files;
- All addresses, offsets, and values are local static-analysis/experiment
  records, not flashing instructions;
- Nikon, EXPEED, and related marks belong to Nikon Corporation; this project is
  not affiliated with, authorized by, or endorsed by Nikon;
- Modifying firmware can damage a camera, void warranties, and lose data; the
  user bears all risk;
- References and third-party work are listed in [NOTICE.md](NOTICE.md).

Text is licensed under [CC BY 4.0](LICENSE). When citing conclusions, keep the
source and the boundaries above.
