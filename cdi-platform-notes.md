# CD-i platform notes — a checklist for the next disc

A running checklist, carried from one CD-i documentation pipeline to the next
and added to by each. It now covers **three discs, four years and two
continents apart**:

- **Ultra CD-i Soccer** (Krisalis / Philips, UK, 1997) — a game: 144 small
  files, raw CLUT bitmaps, run-length sprites, one MPEG-1 intro, 2.4 % of a CD
  used.
- **Origami** (EagleVision, Netherlands, 1993) — a presentation: 46 files,
  98 % of a CD, 79 % of it multi-channel real-time video and audio, nothing
  compressed and not a single bitmap file.
- **Link: The Faces of Evil** (Animation Magic / Philips Interactive Media of
  America, USA, 1993) — a streaming game: **14 files in a flat root**, 77 % of
  a CD, 99.7 % of its bytes in five real-time files, and half the disc
  deliberately empty.

They have almost nothing in common at the content level, which makes the things
they *do* share worth trusting. Those are marked **[all]** when all three agree
and **[2 of 3]** when two do. Findings from only one disc are named, and are the
ones to test rather than assume.

The tools referenced live in the pipeline repositories:

- [cdi-ultracdisoccer-doc](https://github.com/vs-sr-dev/cdi-ultracdisoccer-doc)
- [cdi-origami-doc](https://github.com/vs-sr-dev/cdi-origami-doc)
- [cdi-linkthefacesofevil-doc](https://github.com/vs-sr-dev/cdi-linkthefacesofevil-doc)

`cdilib.py`, `cdifs.py`, `cdihead.py`, `os9mod.py`, `cdistrings.py`,
`cdiaudio.py`, `cdidyuv.py`, `cdirta.py`, `cdipic.py`, `cdicensus.py` and
`cdisym.py` are platform-general and should work unmodified on another disc.
`cdigfx.py`, `cdispr.py`, `cditeams.py`, `cdipf.py` and `cdianim.py` carry
title-specific tables and are worth reading rather than running.

**This document is the canonical copy.** Pipelines should link to it rather
than fork it.

---

## 1. Open the image first, and open it raw

```
chdman extractcd -i "TITLE.chd" -o _work/disc.cue -ob _work/disc.bin
```

Expect a single `MODE2_RAW` track — or, on an already-extracted dump, a `.cue`
saying `TRACK 01 CDI/2352`. **Do not** work from a cooked 2,048-byte image: on
CD-i the subheader is where the interleaving lives, and you lose it.

Sector layout, 2,352 bytes:

```
0..11    sync   00 FF FF FF FF FF FF FF FF FF FF 00
12..15   header MM SS FF mode, MSF in BCD, LBA + 150
16..23   subheader: file, channel, submode, coding -- stored twice
24..     user data: 2,048 (Form 1) or 2,324 (Form 2)
2348..   EDC, on Form 2 sectors
```

The Form bit is submode `0x20`. `cdilib.Disc.data()` sizes each sector from it;
anything that assumes 2,048 everywhere will silently corrupt real-time files.

Submode bits: `0x01` EOR, `0x02` video, `0x04` audio, `0x08` data, `0x10`
trigger, `0x20` Form 2, `0x40` real-time, `0x80` EOF.

**Note the capacity used before anything else.** A CD holds about 333,000
sectors. Soccer uses 7,875 of them, Link 255,924, Origami 326,400. That one
number sets your expectations for everything that follows: a disc at 2 % of
capacity will have dead files lying around, and a disc at 98 % will not.

---

## 2. Check the pre-file-system region — this is the highest-yield first move

**[all]** **Run this before anything else.** It turned up 5.2 MB on every disc
so far, and on none of them can the program reach it.

```
python tools/cdihead.py map
python tools/cdihead.py check
python tools/cdihead.py wav OUT/
```

The path table sits a long way in — **LBA 2,269 on Soccer and Link, 2,268 on
Origami** — and everything before it belongs to nobody. Do not assume it is
zeroes. Test, in this order:

1. **Is it plain zero?** Fine, move on.
2. **Is it the ECMA-130 scrambler sequence?** Generate the 2,340-byte LFSR
   stream (x^15 + x + 1, preset `$0001`, LSB first, reset per sector), take
   bytes 12.. to line up with Form 2 user data, and XOR. If it comes out zero,
   the region is *scrambled* zeroes.
3. **Does the XOR produce something structured?** On all three discs a large
   part of it became 16-bit little-endian PCM.

Signals that a region is real audio rather than noise:

- `mean|x[n] - x[n-1]| / mean|x[n]|` well below 1 — 0.12 on Soccer, 0.124 on
  Link. Noise sits near 1.
- Left channel equal to right on every frame, or nearly so.
- A harmonic series in the averaged FFT rather than a flat floor.

**`cdihead.py map` labels anything non-zero "scrambled PCM", and that label is
inherited, not earned.** Verify it on your disc before writing the word "audio"
down: compute the smoothness ratio, check L against R, and take an FFT.

**[all]** **Compare the head against the tail.** On all three discs the tail
padding is *plain* zeroes while the head padding is *scrambled*. Two filler
mechanisms in one image means two different tools touched it, and it has now
held on three unrelated pressings.

**Link has a third mechanism**: four sectors at 255,770–255,773, 2,324 bytes
each, **every byte `0x20`** — ASCII spaces. Not zero, not scrambled zero.
Classify padding by content, not by position.

**The audio does not have to start at sector 0.** It is aligned to the *end* of
the region, not the beginning:

```
Origami   0-15 zeroes, 16-17 volume descriptors, 18-2267 audio  (2,250 sectors)
Link      0-15 zeroes, 16-17 volume descriptors, 18 zero,
                                                19-2268 audio  (2,250 sectors)
Soccer    a mix: 1,064 sectors of scrambled zero, 1,203 of PCM
```

On the two 1993 discs the audio is **exactly 2,250 sectors ending immediately
before the path table**.

### The head-region audio is the same recording on two unrelated discs

This is the strongest result the pipelines have produced, and it was found by
comparing rather than by analysing.

*Link: The Faces of Evil* (USA, Animation Magic, Philips Interactive Media of
America) and *Origami* (Netherlands, EagleVision) share **no developer, no
publisher, no continent and no content**. Descramble both head regions and
they are **byte-identical**:

```
Link     sectors 19-2268    5,229,000 B    md5 a0ed87f2e98b43f91281d16390fb178b
Origami  sectors 18-2267    5,229,000 B    md5 a0ed87f2e98b43f91281d16390fb178b
```

All 5,229,000 bytes. Same MD5. **The recording is not title content — it is an
artefact of the CD-i authoring chain**, laid down by whatever tool wrote the
pre-file-system region on both 1993 masters.

*Ultra CD-i Soccer*, four years later, carries a **different** recording in the
same place — two mono clips of 7.41 s and 8.31 s, bit-identical left and right,
fundamentals near 151 and 161 Hz. No 512-byte run of one appears in the other.

So: whatever the filler is, it changed between 1993 and 1997, or between
mastering facilities. **Hash the descrambled head region of every new disc and
compare it against both** before spending any time analysing it. That is a
thirty-second check and it may answer the question outright.

The 1993 recording, measured:

```
29.39 s at 44,100 Hz stereo      1,296,000 frames
smoothness ratio                     0.124
peak                                23,345
L == R exactly                        1.40 % of frames
corr(L, R)                           0.9988
fundamental                       169-171 Hz, drifting
second harmonic                   339-341 Hz
one silence                        8.6-8.9 s, so two takes
```

A drifting ~170 Hz fundamental with strong harmonics is what a male speaking
voice looks like, and 29 seconds in two takes is what a slate or an
announcement looks like. Nothing identifies the speaker. This is a question
only ears can close.

### Why it is scrambled: the mechanism, and how many bytes are lost

Origami worked this out and Link confirms it on identical data.

**[2 of 3]** The head sectors have correct sync, a correct MSF header, a valid
subheader — and a **wrong EDC on every single one**. The subheader is
`00 00 20 00` on all 2,250 sectors of both 1993 discs: Form 2 with none of the
data, audio or video bits set, a sector declaring itself to be of no type at
all.

Descramble, and the recovered audio has a discontinuity at every sector
boundary. Measure that jump against the signal's own lagged differences, **per
channel and in stereo frames**:

```
Link, left channel
  mean |x[k] - x[k-1]| away from boundaries    123.4
  mean |x[k] - x[k-1]| at a sector boundary    455.6
  lag 6 expectation                            405.8
  lag 7 expectation                            455.0   <== match
```

**Exactly seven stereo frames — 28 bytes — are lost per sector.** That is
sync (12) + header (4) + subheader (8) + the four-byte EDC field. So the object
underneath is a **2,352-byte-per-block CD-DA stream**: the CD-i authoring system
wrote its own sector headers over the first 24 bytes of each block, **zeroed the
four bytes at the EDC position**, and left the other 2,324 alone. A data-mode
ripper descrambles the lot on the way out, which is why the dump shows audio XOR
scrambler rather than audio.

The EDC field is worth checking directly, because it settles what happened
there. On Link it is literally `00 00 00 00` on all 2,250 sectors — not
scrambled zero, not a valid EDC, and not audio:

```
EDC field values across 2,250 sectors:  {'00000000': 2250}
descrambles to zero:                    0 / 2250
```

Extending the extraction to 2,328 bytes per sector to try to recover those four
bytes makes the discontinuity far worse (8,839 against 455), which confirms they
were overwritten rather than merely discarded. **Seven frames per sector are
gone and cannot be recovered.**

*(An earlier revision of this document, written from Origami alone, put the loss
at six frames / 24 bytes. That accounted for sync + header + subheader and
missed the zeroed EDC field. Link's per-channel measurement lands on lag 7 to
within 0.1 %.)*

Run this lag test on your disc. A boundary jump matching lag 7 is the same
mechanism.

One difference worth recording: Soccer's head clips are **bit-identical** left
and right; the 1993 recording correlates at 0.9988 but is not identical — a mono
source through a stereo converter rather than a duplicated channel.

---

## 3. The Green Book file system

Sector 16 is the volume descriptor. It is ISO 9660's skeleton with big-endian
numbers only, so **read the second half of every both-endian pair**.

```
1      "CD-I "        standard identifier
8      system id      expect "CD-RTOS"
40     volume id      <- often the working title, not the box title
84     volume space size (blocks)
130    logical block size
136    path table size
148    path table LBA
190    volume set id
318    publisher
446    data preparer  <- frequently blank
574    application id <- THE BOOT PATH, e.g. "CMDS/cdi_demo"
702/739/776  copyright / abstract / bibliographic file names
813    creation date, YYYYMMDDHHMMSSss
```

Things to grab immediately:

- **[all]** **The application identifier is the executable's path.** Compare it
  with the title on the box and with the OS-9 module name inside the file. On
  Soccer all three disagreed (`Ultra CD-i Soccer` on the box, `CD-i Soccer` as
  the volume, `CMDS/cdi_demo` as the application, `cdi_main.mod` as the module).
  On Origami and Link all three agree, and the path has no directory component
  at all — the boot file sits in the root.
- **[all]** **`copyright`, `abstract` and `biblio`, if named, are plain text on
  the disc.** Always `cat` all three. Soccer's held marketing copy and the
  complete credits. Origami's held the credits, a placeholder abstract
  (`ORIGAMI is great!`) and three different spellings of the publisher's own
  name, one of them inside the copyright notice. **Link's `BIBLIOGRAPHY` is the
  complete credit roll** — producer, script, three programmers, graphic design,
  video, audio and music — and it is the only place several of those names
  appear.
- **`cat` anything in the root that nothing references, too.** Origami has a
  `message.txt` that no file names and the executable never opens, holding the
  studio's address and the programmer's **private home address and telephone
  number**. If you turn up personal data belonging to a living private
  individual, record that it is there and what kind of thing it is, and think
  before giving it a second publication.
- **Read the publisher and data preparer fields as carefully as the application
  identifier.** The preparer field is usually blank. On Origami it is a person's
  name, and the same name is the sole programming credit. On Link it is
  `_ISG_CDI_TOOLS_1.6` — the disc naming its own authoring tool. And Link's
  **publisher field is misspelled**: `Philips Intractive Media of America, Inc.`,
  no `e` in *Interactive*, in the one string on the disc that a CD-i player
  reads and can display. The `COPYRIGHT` file three sectors away spells it
  correctly, and so does the DYUV logo in the bumper. **Typos live in the fields
  that were typed, not in the ones that were drawn.**

There is **no root directory record in the descriptor**. CD-i reaches the root
through the path table only, and the root extent's own `.` record carries the
true directory length. `cdilib.walk()` handles this.

Directory records are ISO 9660 up to the name, padded to even, then **ten bytes
of system use**: owner group (2), owner user (2), attributes (2), reserved (2),
file number (1), reserved (1).

Attribute bits: 0 owner-read, 2 owner-exec, 4 group-read, 6 group-exec,
8 world-read, 10 world-exec, 12 CDDA, 15 directory. Expect `0x0555` for files
and `0x8111` for directories; anything else is worth a look (Soccer's flagged
the path table exposed as a file, and so does Link's).

**The file number byte in the system-use area is `1` on real-time files and `0`
on everything else.** On Link that byte is the only mechanical difference
between a 30 MB stream and an executable at the file-system level, and it is
what tells the driver to hand the file to the real-time reader. Check it before
you trust any directory size.

**A flat root is normal for a streaming disc.** Soccer has 12 directories and
144 files; Origami has 5 and 46; **Link has none and 14**. Directory count
correlates with how much the program owns rather than streams.

**Watch for case collisions when extracting.** Soccer had a root file
`intro_gfx` and a root directory `INTRO_GFX`; on Windows or macOS a naive
extractor drops one. `cdifs.py extract` detects this and suffixes the loser
`.file`. If your extracted file count is one short of the directory listing,
that is why.

---

## 4. Census the disc before reading any of it

Three cheap passes that pay for themselves:

```
python tools/cdifs.py list    # LBA, size, date, attributes
python tools/cdifs.py map     # who owns each sector, with submode flags
```

- **All-zero files.** `collections.Counter(bytes)` per file. Soccer had sixteen
  totalling 1,070,080 bytes — an entire abandoned localisation. This takes ten
  seconds and is often the best leftover-hunting move on a CD-i disc, because
  the format usually has no space pressure to force anyone to delete them.
  Origami had none and sits at 98 % of capacity; Link had none and sits at 77 %.
  **A disc with no slack is a disc with no dead files**, so measure the capacity
  first and set your expectations from it.
- **Directory dates.** Every record has a six-byte date. Bucket them by month:
  the shape of the schedule falls out, and files whose dates cluster far from
  everything else are usually a different generation of the same assets. On
  Soccer the directories all carried the mastering timestamp, so only *file*
  dates meant anything.
- **Compare every file's date against the volume creation date.** On Link the
  volume descriptor is stamped `1993-06-23 17:48:35`, three seconds after the
  last real-time stream was written — and the executable is stamped
  `1993-06-24 11:02:55`, **seventeen hours later**. The last change to that game
  was a code change made overnight and dropped into a closed image. A file newer
  than the volume descriptor is always worth a sentence.
- **Gaps in the sector map.** Anything owned by `<free>` inside the file area
  deserves a hexdump. On Link the gaps are plain zeroes and they are structural:
  two runs of 2,872 sectors sit immediately in front of the two files the game
  must start streaming without a hitch — 38 seconds of disc for the drive to
  settle.
- **Hash every file's payload *and* its subheaders.** Link presses the same
  30 MB file three times — `ldata.rtr`, `ldata1.rtr`, `ldata2.rtr`, byte-
  identical down to the channel and submode bytes — at LBA 2,992, 115,786 and
  235,940. That is 11.5 % of the disc spent so a single-speed drive is never far
  from the game's working set. **If two files hash the same, look at their LBAs
  before calling it a mistake.**

---

## 5. The executable is one or more OS-9/68000 modules

```
python tools/os9mod.py _work/files/<name>
```

Modules start with `$4AFC` and are self-validating, so you can find them by
scanning and confirm them by arithmetic:

```
0   $4AFC sync         18  type      1=Prgrm 2=Sbrtn 3=Multi 4=Data
2   M$SysRev                         $B=Trap $C=Systm $D=FlMgr $E=Drivr $F=Devic
4   M$Size              19  lang     1 = 68000 object
8   M$Owner             20  attr     bit7 re-entrant, bit5 sticky
12  M$Name (offset)     21  revision
16  M$Accs              22  edition
                        46  M$Parity = ~(XOR of the 24 header words)
48  M$Exec  52 M$Excpt  56 M$Mem  60 M$Stack
64  M$IData 68 M$IRefs  72 M$Init 76 M$Term
```

Module CRC is a 24-bit poly `$800063`, preset `$FFFFFF`, and running it over the
**whole** module including the stored CRC must give `$800FE3`. If it does, the
binary has not been patched since the link — worth stating.

**[all]** Two header details the classic OS-9/6809 documentation will get wrong
for you on 68000 modules: **`M$Name` is a four-byte offset at byte 12**, not
two, and names are **NUL-terminated**, not high-bit-terminated. Handle both
conventions.

**Not every module has `M$Init`/`M$Term`.** On Link the header ends at 72 and
the name string starts there, so reading offsets 72 and 76 as pointers yields
`0x6364695f` and `0x6c696e6b` — the ASCII of `cdi_link` itself. If those two
fields look like text, the header is 72 bytes, not 80.

The name may be followed by the **linker option string** in the same area.
Origami's main module reads `origami` NUL `-F` NUL.

Expect several modules concatenated with no padding — Origami's executable is
four (`Prgrm`, `Sbrtn`, `Prgrm`, `Trap`) whose sizes sum to the file size
exactly, and Link's second executable is two (`Prgrm` plus a 128-byte `Data`
module). A tiny module of type `$B` (Trap) next to the main program is normal:
it is the shared-library / trap-handler mechanism.

**Read the edition byte.** Link's game module is edition 1 and its bumper is
edition 7 — the logo player was reworked seven times and the game never was.

**[all]** **Look at the bytes immediately after the header, before any code.**
On Soccer they were a 29-glyph 1bpp 8×8 font — the alphabet the loader error
screens need before any file has been read. On Origami, inside the `cdi_bpsys`
module, they were nine names:

```
M.Armendariz,L.Barnes,W.Hunt,J.Kesselman,S.McClellan,R.Moore,T.Nutt,J.Piesing,J.Rotter
```

That is the author list of the **CD-i base program system** — the Philips /
OptImage support library that titles link against — not of the game.
J. Piesing is Jon Piesing, of Philips' CD-i and interactive-TV standards work.

**Grep every CD-i executable you meet for `Armendariz`.** It should appear in
every disc linking the same library revision, which makes it a free toolchain
fingerprint and, across enough titles, a way to date a build.

### Fingerprint the C runtime

Two binaries that share a toolchain share its tables. Link's two executables
have a **124-byte block byte-for-byte in common** — `cdi_bumper` at `0x2c60`,
`cdi_link` at `0x1df4d`:

```
01 01 01 01 01 01 11 11 01 11 11 01 ... 01
30  20*15  48*10  20*7  42*6  02*20  20*6  44*6  04*20  20*4
```

Read as a table indexed from ASCII 3, that is a C `ctype` array: `0x01`
control, `0x10` space, `0x20` punctuation, `0x02` upper, `0x04` lower, `0x08`
digit, `0x40` hex digit. Space is `0x30`, the digits `0x48`, `A`–`F` `0x42`,
`a`–`f` `0x44`. It is the Microware OS-9 C library's character-class table,
linked unchanged into both.

Finding it proves a shared toolchain and anchors the runtime's data area. The
runtime's panic strings are usually nearby, and their wording dates the build:
Link's game says `**** Can not install trap handler ****` and its bumper says
`**** Can't install trap handler ****` — the same message from two vintages of
the same library.

---

## 6. Symbols, strings, and the two tricks that pay best

### 6a. Scan for a symbol table before anything else

**Do this as step four, not step ten.** Link's `cdi_link` carries **325 C
function names** — NUL-terminated ASCII, laid down in link order between the
functions themselves. Neither Soccer nor Origami had them, so the technique was
never tried until the third disc, and on a disc that has them it is worth more
than everything else in this document combined.

```
python tools/cdisym.py list      # address, name
python tools/cdisym.py groups    # bucketed by prefix
```

The filter that works: 4–40 characters of `[a-z][a-z0-9_]*` followed by a NUL,
keeping only names that contain an underscore (plus a small allow-list of bare
words). That keeps 68000 opcode bytes out. It is title-independent and takes
two minutes to run.

If the names are there, **read them in address order.** The linker's order is
close to the compiler's order, which is the source file order, and the blocks
fall out as modules. Link's read as: boot and display setup, the two-plane
model, audio, combat, menus, saving, the playfield, the player, movement,
enemies, world loading, projectiles, items. That is the architecture of a game
whose source has never been public, recovered for free.

What to look for once you have the list:

- **Numbered families.** `init_type_1` … `init_type_8` with only
  `move_types_1_and_3`, `move_type_2`, `move_type_4`, `move_type_5_or_6_1_block`
  and `move_type_6` to move them: eight enemy types, five movement routines, and
  the names record exactly which types turned out to behave the same.
- **Paired verbs.** `add_bombs_to_buy` / `add_fake_bombs`,
  `add_ropes_to_buy` / `add_fake_ropes`, `add_oil_to_buy` / `add_fake_oil` —
  every purchasable item has a real version and a counterfeit, as separate
  functions rather than a flag.
- **Superseded code still linked in.** `old_get_sprite_block` sits beside
  `get_sprite_block`. `read_sprite_cycle_colors2` exists and
  `read_sprite_cycle_colors` does not.
- **Names that are an apology.** `fix_jump_stick`.
- **Misspellings.** `check_vulnerablility`, shipped.
- **Modes nobody documents.** `retrieve_mkt_demo` and `set_mkt_demo_off`, built
  with the same pair of verbs as `retrieve_help_state` / `set_help_off`, plus a
  `demo_playfield` beside `playfield`: a marketing attract loop for a shop
  kiosk, on the retail disc.
- **Functions that name a hardware model.** `plane_A_over_B`, `plane_B_over_A`,
  `set_fctA_rl7`, `set_fctA_clut7`, `set_fctB_rl7`, six `fade_*_plane_*` —
  fourteen functions that are the whole of one game's use of the MCD212.
- **CD-i system nouns.** `zero_data_cil` / `zero_video_cil` (Channel Interleave
  List), `set_pcb` / `set_pcl` / `reset_pcl` (Picture Control Block / List).
  These tell you which driver structures the title actually touches.

There will be no `main`, and no `printf` or `malloc`: the C library carries no
name strings, so what you get is exactly the title's own code.

### 6b. Cross-reference every path-shaped string, in both directions

68000 object code produces enormous amounts of accidental ASCII, so filter:
keep runs that are mostly letters and whose words are long enough to be
language. `cdistrings.py` does this and separates disc paths from prose.

Then:

> **Cross-reference every path-shaped string against the directory listing, in
> both directions.**

- Paths in the binary that resolve to nothing = features cut late, with their
  original names intact. Soccer gave `/segalogo_gfx`, `/comment_gfx`,
  `/ocean6a_gfx`, `/tmsplng_gfx`, `/kit1_rlspr` — the last of which *named an
  undocumented file format*.
- Files on the disc that the binary never names = dead weight, usually an
  earlier generation of assets.

The path strings usually sit in **load order**, which is free information about
program flow, and a missing name's neighbours tell you which screen it belonged
to.

**A clean cross-reference is also a result.** On Link nothing dangles in either
direction: every file is named by one of the two executables and every name is
on the disc. Whatever was cut from that game was cut before the final link.
Origami's is clean too. Say so — it dates the cut.

**Check which executable names what.** Link's game binary never mentions
`bumper.rtf`; the bumper program does, and the game names the bumper. The chain
`cdi_link` → `cdi_bumper` → `bumper.rtf` falls straight out of the two string
lists.

Other things to grep for by hand once the filter has run: profanity and
`debug`/`test`/`cheat` (Soccer had `CHEAT MODE ON` and an unprintable error
message), `TV`/`MONITOR`/`WINDOW` (development-host options — Link carries
`625` and `TV`, the strings that ask CD-RTOS for a 625-line PAL display, on a
disc that shipped to 525-line NTSC territory), and placeholder prose — look
specifically *between* legitimate string blocks, which is where Soccer hid a
copyright notice nobody replaced.

### 6c. String storage on a Microware build

Link's strings live in the module's initialised-data image — `M$IData` in the
header points straight at them — as:

```
<u8 length> <u16 address> <bytes>
```

with the addresses climbing monotonically. That means three useful things:

- The length byte lets you recover strings a printable-run filter would split.
- The order in the file is the order of declaration in the source.
- **Bytes that look like screen coordinates in front of a string are usually
  the 16-bit address, not a position.** Two bytes reading `0x28 0x28` in front
  of an item name are an address, and reading them as x and y will send you
  looking for a layout that is not there.

Two-line UI labels may be stored **bottom line first**. Link's item table reads
`Crystal`, `Ice`, `Diamond`, `Fire`, `Reflection`, `Crystal of`, … and only
makes sense in pairs read backwards: *Ice Crystal*, *Fire Diamond*, *Crystal of
Reflection*. Adjacent duplicates are states, not mistakes — `Canteen` appears
twice because the empty one and the full one are separate inventory entries.
And `(empty)` may itself be a string: the label an unused slot draws.

---

## 7a. Read the subheader coding byte before you guess anything

**[all]** Every video and audio sector states its own format in subheader
byte 3. This is free and it is authoritative — decode it before trying widths
or sample rates.

**Video coding byte:**

```
bits 3-0   0 CLUT4   1 CLUT7   2 CLUT8   3 RL3   4 RL7   5 DYUV
           6 RGB555 lower      7 RGB555 upper     15 MPEG
bits 6-4   0 normal resolution   1 double   3 high
```

**Audio coding byte:**

```
bits 1-0   00 mono         01 stereo
bits 3-2   00 37,800 Hz    01 18,900 Hz
bits 5-4   00 4 bits       01 8 bits
bit  6     emphasis
```

which gives, for the values actually seen:

```
0x00  Level B  mono    37,800 Hz  4-bit
0x01  Level B  stereo  37,800 Hz  4-bit
0x04  Level C  mono    18,900 Hz  4-bit
0x05  Level C  stereo  18,900 Hz  4-bit
0x10  Level A  mono    37,800 Hz  8-bit
0x11  Level A  stereo  37,800 Hz  8-bit
```

**Level A is the 8-bit one, so it is `0x10`/`0x11`, not `0x01`.** An earlier
revision of this document called `0x01` Level A; it is Level B stereo. Link uses
`0x01` in three places and a 4-bit Level B decoder reproduces all of them as
clean audio, which settles it. Getting this wrong costs you a factor of two in
every duration you compute.

A `bits 3-0` value of 15 means the title needs the **Digital Video cartridge**.
Neither 1993 disc has a single MPEG sector.

Normal resolution is **384 × 280 on PAL** and **384 × 240 on NTSC**. Both are
384 bytes to the line for the one-byte-per-pixel codings.

**[all]** **A change of coding byte inside one title marks a change of
purpose.** Origami is Level C for all five narration tracks and Level B for
exactly one channel in one file — which turns out to be the only music on the
disc. Link is Level C throughout `lmusic.rtr` except for the last record on the
last channel, which is Level B stereo, and Level C mono throughout the cutscene
soundtrack except for the final record, which is Level B stereo. **When you find
the odd coding byte out, you have found the thing that was worth twice the
bandwidth** — and on both discs it was the ending.

---

## 7b. DYUV

Soccer had none; Origami is nothing else; Link uses it for exactly one picture,
the publisher bumper. All three agree on the algorithm.

DYUV is one byte per pixel: a 4-bit luma delta for **every** pixel and a 4-bit
chroma delta for every **pair**, U on even bytes and V on odd. Each line
restarts from the picture start value, so errors cannot cross a line.

```
byte 2n     bits 7-4 = dU     bits 3-0 = dY(pixel 2n)
byte 2n+1   bits 7-4 = dV     bits 3-0 = dY(pixel 2n+1)
```

The delta table, indexed by the nibble and added modulo 256:

```
0, 1, 4, 9, 16, 27, 44, 79, 128, 177, 212, 229, 240, 247, 252, 255
```

**Check it is symmetric before you use it.** 1+255, 4+252, 9+247, 16+240,
27+229, 44+212 and 79+177 all sum to 256. A version ending
`227, 236, 243, 248, 251` is a corrupted copy that circulates, and it does not
fail loudly — it decodes to something with the right shapes in the right places,
streaked horizontally and saturating to white, which reads as a compression
format you have not identified yet. It cost the Origami pipeline an hour.

Starting each line from `Y=16, U=V=128` works. Link's bumper decodes at
384 × 240 from 40 sectors on the first attempt with those values.

Two more traps:

- **A flat area is a run of `0x00`**, because zero means "no change" in both
  nibbles. Do not read zero runs as absent data.
- **Judging a decode by "which plane looks smoother" is backwards.** For a
  desaturated photograph the *chroma* plane is the smooth one and the luma
  plane is the contrasty one. Render both and look; do not trust the gradient
  metric.

---

## 7c. Bitmaps, palettes, and proving a geometry

Assume raw and uncompressed until proved otherwise. On CD-i the MCD212 reads
CLUT planes straight out of memory, so a file is very often a framebuffer.

**Palettes** are flat RGB triplets. The size tells you the mode:

```
192 bytes = 64 entries  = 1 CLUT bank
384 bytes = 128 entries = 2 banks   (CLUT7)
768 bytes = 256 entries = 4 banks   (CLUT8)
```

**Entry 0 is the transparency key, but not always green.** Soccer used
`#00FF00` on almost everything; **Link uses `#FFFFFF` on all 71 of its
palettes**. The reliable test is not the colour, it is the usage: count how
often index 0 appears in the pixel data. On Link it appears in only 6 of 71
pictures, which is what a reserved index looks like.

Origami ships **no palettes at all** — a disc that streams everything may carry
its CLUTs inside the real-time data.

### Finding the width

Three methods, in increasing order of certainty.

**1. Take `max(pixel value)` first.** A ceiling of 127 means CLUT7, 63 or 31 a
smaller bank. And the negative form of this is a proof: across Link's 13.6 MB
of playfield pixels **not one byte has bit 7 set**. RL7 signals a run by setting
bit 7, so that data cannot be RL7, and a compressed format that never uses half
its byte values is not a compressed format. **"Not one byte has bit 7 set" is a
proof, not an impression.**

**2. Autocorrelate.** For candidate pitch *W*, count `x[i] == x[i+W]` over a
sample. Link's playfields peak sharply at 384 and at 1,152; Origami's real-time
video peaked at 384 with 0.88 agreement against a 0.667 random baseline. On
sparse data, autocorrelate the *zero mask* instead of the bytes.

Try widths in this order:

```
384   CD-i normal resolution
512   power-of-two line pitch -- see below
768, 1152   two and three screens wide, for scrolling playfields
280 / 240 as heights (PAL / NTSC full screen: 107,520 and 92,160 bytes)
320   NOT a CD-i size; if this is what renders, the art came from elsewhere
```

**3. Then prove it by arithmetic, to the byte.** This is the step that catches
the errors autocorrelation leaves. Link's 71 playfields are 24 at 384 × 240,
17 at 768 × 240 and 30 at 1,152 × 240; each starts on a sector boundary, so they
cost 40, 80 and 119 sectors:

```
24 x 40 + 17 x 80 + 30 x 119 = 5,890 sectors = 13,688,360 bytes
```

which is the video stream exactly. **If your model of the geometry does not
reproduce the stream length to the byte, it is wrong**, and the failure mode is
cruel: the first three pictures look perfect and the drift only becomes visible
around the twentieth.

**Pictures may be sector-aligned rather than packed.** Link's are — 92,160 bytes
of picture in 40 sectors of 2,324, with the 800-byte remainder left as whatever
was in the buffer, not zeroed. Look for that before concluding the data is
compressed. Related checks that work: find the last non-zero byte in one image
and see whether it lands on an exact multiple of the pitch (Origami:
74,112 = 384 × 193 exactly), and look for a constant sector count per image
(Origami's were 32 and 28; Link's 40, 80 and 119).

**The 512-byte line pitch caught Soccer out and will catch you out.** Two
122,880-byte files were 384 pixels of picture inside a 512-byte line, with 128
columns of filler on the right. Rendered at 384 they shear into diagonal noise
that looks like a compression format. If a file size is not a multiple of 384
but *is* a multiple of 512, try 512 before you start writing a decompressor.

**If a file renders at 320 × 224, stop and take it seriously.** That is a Mega
Drive PAL frame, not a CD-i one, and on Soccer it was one end of a chain
(orphan 320 × 224 art → a `/segalogo_gfx` reference → Cross Products in the
thanks list) pointing at a Sega-era toolchain and an earlier version of the
game. Other non-CD-i sizes deserve the same attention.

**Beware partial-band updates.** A stream may refresh a strip rather than a
frame. Origami's credit sequence is 88 runs of 43 lines — the height of one line
of sliding text. If your run heights are small and various, that is what you are
looking at.

---

## 7d. Run-length codings

**RL7**, the MCD212's own, and the one to expect for anything that moves:

```
byte & 0x80 == 0   one pixel of that index
byte & 0x80 != 0   run of colour (byte & 0x7F); the next byte is the length,
                   and a length of 0 means "to the end of the line"
```

**Validate the width by counting exact line ends.** Decode a few hundred lines
and count how many finish precisely on the boundary rather than overshooting.
Link's cutscenes give 479 of 480 at 384 pixels — a wrong width leaves runs
straddling line ends everywhere, and the count collapses.

Compression is good: Link's cels average 8,639 bytes against 92,160 raw, 10.7:1.

**`rlspr`**, a title-specific sprite format Soccer's binary named — a flat array
of fixed-size slots, each holding run-length-coded lines:

```
0x00..0x7F   one pixel of that index
0x80..0xFD   skip (n & 0x7F) transparent pixels
0xFF         end of line
0xFE         filler, pads the slot to its fixed size
```

Recognise it by pixel values above `0x80` in a file whose palette has 128
entries, and find the slot size by counting runs of the filler byte: Soccer's
bank had 416 filler runs in 53,248 bytes, so slots were 128 bytes. Do not assume
a `_sprts` suffix means coded — on that disc most `_sprts` files were plain
bitmaps.

### Look for fixed slots

**[2 of 3]** Both Soccer's sprites and Link's animation frames sit in
**fixed-size slots with the tail padded**, and in both cases decoding the stream
continuously produces something that looks nearly right and drifts.

Link's cutscene frames occupy **14,336 bytes — seven Form 1 sectors** — holding
240 lines of RL7 and then zero padding. Decoded as one continuous stream they
drift, because the padding decodes into nine to twelve blank scanlines of its
own. Cut into slots and decoded independently, every frame lands registered.

**Test for it by checking whether every record length shares a common divisor.**
Link's 61 animation records all divide by 14,336.

And note what the slot costs: **the slot size is the frame rate.** Seven sectors
a frame out of the 75 a 1x drive delivers is a hard ceiling of **10.7 fps**,
before the soundtrack takes its share. The padding is not waste, it is reserved
bandwidth — a frame that needed all 14,336 bytes would still arrive on time.

---

## 7e. Fonts

Soccer's appeared in three layouts on one disc, so test all of them:
glyph-major fixed cells, a **single strip N pixels tall** (glyph *n* is a
vertical slice — this is why 16 × 16 cells produce recognisable-but-sheared
letters), and 1bpp packed rows for anything linked into the executable.

Read the character set once you have it: Soccer's was IBM CP437 `0x20`–`0xAA`,
which dates the art pipeline to a PC even though the console is a 68000 running
OS-9.

---

## 8. Audio

Expect **Green Book ADPCM** everywhere. Whether it is wrapped depends on what
the disc is:

- **In files** (Soccer): wrapped in a container, and the container is where the
  provenance is. See below.
- **In real-time sectors** (Origami, Link): completely raw. **2,304 bytes of
  ADPCM per sector and 20 bytes of nothing**, no header of any kind. The format
  comes from the subheader coding byte and nowhere else, which is why section 7a
  matters.

The decoder is the same either way, and `cdiaudio.decode()` takes bare ADPCM
bytes.

The 128-byte sound group is 16 parameter bytes plus eight 28-sample units
(224 samples). Confirm the header layout rather than assuming it — the sixteen
bytes are eight parameters stored twice, and checking which halves match tells
you which is which:

```
bytes 0-3 == 4-7 and 8-11 == 12-15   -> units 0-3 from 0-3, units 4-7 from 8-11
```

A parameter byte is `filter << 4 | range`. Data byte `16 + t*4 + (u & 3)`, low
nibble for units 0–3 and high nibble for 4–7. Predictor coefficients
`f0 = 0, 60, 115, 98, 122`, `f1 = 0, 0, -52, -55, -60`, shifted right 6.

### Stereo alternates by sound unit, and there is no tell if you get it wrong

In a stereo group, **units 0, 2, 4, 6 are left and 1, 3, 5, 7 are right, and
each side needs its own two-sample history.** Decode stereo with a single shared
history and you get a signal that is still smooth, still passes every
plausibility check, and is still wrong. There is no obvious artefact. Get it
right by construction.

### If the audio is wrapped

- Soccer's shipped as **AIFF-C** (`FORM`/`AIFF`) with the samples in a
  non-standard **`APCM`** chunk instead of `SSND`, with the same 8-byte
  offset/blocksize preamble. `COMM` is honest about channels, bits and rate.
- Sample rate is an 80-bit IEEE extended float. **18,900 Hz** = Level C,
  **37,800 Hz** = Level A or B; 4 bits/sample = Level B or C, 8 bits = Level A.
- **Read the extra chunks.** Three of Soccer's twelve files carried `MARK`,
  `INST` and a 424-byte `APPL` whose signature was `Sd2a` — Digidesign Sound
  Designer II — and their dates were three weeks later than the other nine.
  Authoring-tool fingerprints in an audio container are free provenance.

### Duration arithmetic, and use it as a check

One Level C sector holds 2,304 bytes of ADPCM — 18 sound groups × 224 samples
= 4,032 samples = **0.2133 s** mono at 18,900 Hz, or half that in stereo. The
drive delivers 75 sectors a second, so:

```
Level C mono      one sector in sixteen     -> up to 16 channels
Level C stereo    one sector in eight       -> up to 8 channels
Level B stereo    one sector in four        -> up to 4 channels
```

Link's `lmusic.rtr` runs **eight** Level C stereo channels, which is the
theoretical maximum, and Origami runs five Level C mono narration channels
alongside two video streams.

**If your decoded channel length does not match `sectors × 0.2133 s`, the coding
byte is not what you think.**

### Check whether the music actually ships

Soccer has a 21-entry sound test naming eight tunes and no file on the disc
holds one. Enumerate the sound-test strings and map them onto the audio files; a
shortfall is a real finding. Link goes the other way: 83 minutes of music in 72
records, and nothing in the binary names any of it.

### Some questions only ears can close

All three pipelines hit a point where the measurements ran out: identifying the
speaker in the head-region audio, and mapping five narration channels onto five
named languages. Long-term average spectra, envelope modulation and
syllable-rate estimates all pointed in plausible directions and none was
decisive. Decode the channels to WAV, say in the documentation exactly what is
measured and what is inferred, and leave the listening as an open question
rather than dressing an inference up as a result.

---

## 9. Real-time files

**[all]** Anything whose sectors have submode bit `0x40` set must be read **by
sector, not through the directory record**. The size in the directory entry is a
Form 1 fiction, and it is wrong in both directions:

```
                Form 1   Form 2    true payload    directory says
bumper.rtf           1      624       1,452,224       1,280,000
ldata.rtr        3,541   11,166      33,201,752      30,119,936
lmusic.rtr           0   95,005     220,791,620     194,570,240
lanim.rtr       40,391   62,184     227,236,384     210,073,600
```

**[all]** Sectors tagged neither VIDEO nor AUDIO nor DATA are **bit-rate padding
and carry nothing** — 19.5 % of Soccer's one real-time file, 26.7 % of the whole
of Origami, **49.6 % of the whole of Link**. Drop them; concatenate the rest at
2,324 bytes each.

### Half the disc being empty is the mechanism, not a defect

A real-time file is read at exactly 1x and never faster, and every channel it
carries must be present at a constant rate whether or not anyone is listening.
Unused bandwidth cannot be saved — it becomes empty sectors.

Link spends **294.7 MB on sectors that carry nothing**. The arithmetic is worth
reproducing, because it shows the padding is a consequence rather than a
mistake: eight Level C stereo channels need 65.6 of the 75 sectors a second, so
a fully packed block would be 12.5 % padding. The measured figure is 49 %,
because the records on the eight channels are different lengths — the block runs
until its longest track finishes, and every channel that has already ended
contributes padding until it does. The inter-block gaps add the rest.

**Report the padding share per file.** It measures the design, not waste.

### Census by channel first

```
python tools/cdicensus.py rtf     # every file, every channel, decoded
python tools/cdirtr.py map
```

The single most informative thing on a real-time disc is the
`(file, channel, submode & 0x0E, coding)` histogram, because **a real-time file
can hold several independent streams** and the channel numbers tell you how many
of what. It is five minutes' work and it tells you whether the disc is a game or
a presentation.

Origami's 37 model files all look like this:

```
ch 0  VIDEO coding 0x05     ch 4  VIDEO coding 0x05
ch 1  AUDIO coding 0x04     ch 2  AUDIO coding 0x04
ch 3  AUDIO coding 0x04     ch 5  AUDIO coding 0x04
ch 6  AUDIO coding 0x04     ch 0  (no type bits) = padding
```

Two video channels and five audio ones. **Do not read a gap in the audio channel
numbers as a missing stream.** The audio channels here are 1, 2, 3, 5 and 6
because channel 4 is taken by the second video stream; the program numbers its
languages 0–4 and steps over the occupied channel. There was no literal
`{1,2,3,5,6}` table in the executable to find — the mapping is arithmetic.

**One channel can carry two streams at once.** Link's `ldata.rtr` channel 4 has
5,890 VIDEO sectors that are one continuous pixel stream and 438 DATA sectors
that are 71 separate descriptor records for it. Same channel number, two
submodes, two independent streams that never interleave with each other.

### Work out what parallel streams are *for*

Two video channels can mean double buffering, two camera angles, or — as on
Origami — **the same content at two geometries, one per video standard**. Three
tests separate them:

1. **Count the frames.** Equal counts on both channels points at duplication.
2. **Correlate frame *k* of one channel against frames *k−1, k, k+1* of the
   other**, rescaled to a common size. A peak on the diagonal means same
   content. Origami: 0.88–0.89 diagonal against 0.57–0.75 off-diagonal.
3. **Check the heights as a fraction of their screen.** Origami's are 193 of 280
   and 167 of 240 — 0.689 and 0.696. The same letterboxed picture in both
   standards.

If a disc does this, say so loudly in the overview: it means a fifth of the
pressing is unreadable on any given player. Origami spends 21.7 % of the disc on
video a PAL machine never touches.

### Interleaved channels may be alternatives — a jukebox

Link's `lmusic.rtr` is the other pattern, and the tell is unmistakable:
**channel *n*'s first sector is sector *n***, a perfect round-robin from the
first byte of the file. Eight stereo music channels running simultaneously, of
which the game listens to one.

The point is that **switching costs no seek**. The sectors for the tune you want
are already going past. Look for `channel n starts at sector n` and you have
found a design that trades disc space for head movement.

### Runs of pure padding cut a file into blocks

Find them — a run of 100 or more empty sectors — and you have found the seek
points. Link's `lmusic.rtr` has nine blocks starting at sectors 0, 9,000,
18,000, 27,000 … 81,000, round numbers laid out by hand or by a script that was
told to, separated by gaps of 1.9 to 55.3 seconds. Each block holds one record
on every channel, so **a block is a set of alternatives reachable from a single
seek**.

Count the records per block and note the exceptions: Link's block 8 has seven
where every other block has eight.

### `EOR`/`EOF` give you the record list; `TRIGGER` does not

`EOR` (`0x01`) closes a record on its channel and `EOF` (`0x80`) closes the file.
Walk those per channel and you have the file's independently addressable items —
one tune, one effect, one animation.

`TRIGGER` (`0x10`) is different: it does not end anything. It asks the driver to
raise an event to the application when that sector is delivered. **It is a cue
point and it sits *near* structure, not on it.** Link's 71 video triggers are
33–40, 77–83 or 119–125 sectors apart where the real picture boundaries are
exactly 40, 80 and 119. Use `EOR` for structure and read triggers as timing.

Triggers are also the whole synchronisation mechanism on a streaming disc.
Link's cutscenes carry no timestamps anywhere; 48 audio sectors have the trigger
bit set, and because a real-time file is read at exactly 1x, **the sector is the
clock**.

### Frame geometry inside a channel

Reassemble one channel (concatenate its sectors' payloads, ignore burst
boundaries — frames do **not** start at burst boundaries) and then apply
section 7c: autocorrelate for the pitch, look for a constant sector count per
image, and check that the last non-zero byte lands on a multiple of the pitch.

### If it is Digital Video

If the payload starts `00 00 01 BA`, it is an MPEG-1 system stream and the title
needs the **Digital Video cartridge**. Confirm by looking for the refusal screen:
grep the executable for `CARTRIDGE` and look for a large root-level bitmap named
something like `nodv_gfx`. Sequence header (`00 00 01 B3`) gives resolution and
rate; expect **368 × 272** at 25 fps for PAL CD-i DV, which is *not* the
352 × 288 of Video CD.

---

## 9b. Tagged chunks, false positives, and code hiding in data

Link's data records use four-character tags with a 32-bit length, and this is
the first disc in the series to do so. Two warnings and one surprise.

**Scanning for `[A-Z]{4}` produces hundreds of false hits.** CLUT7 pixel data
lands in the uppercase-ASCII range constantly; a single 12 KB record produced
eighty apparent tags of which two were real. **Only tags that survive a
structural check are real** — validate by monotonicity, by bounds, and by
recurring at the same offset across every record you have:

```
IDAT  u32 picture size in bytes                        (at offset 388, all 71)
CPL0  u32 block size, u32 header size, then offsets    (at offset 2048, all 71)
PAC0  u32 total size, u32 header size (108), 24 offsets, monotone, all < total
MAC0  same first eight bytes, 144-byte header, table not monotone -- unread
```

`PAC0` archives chain: archive *n* + 1 begins at `total + header`. Walking the
chain and landing exactly on a known boundary is the confirmation.

**Data blocks may contain code.** All 24 used entries of every `CPL0` block on
Link's disc begin `48 e7` — 68000 `MOVEM.L` with a register mask, the prologue a
C compiler emits. Each of the 71 playfields ships with about two dozen compiled
subroutines alongside its picture and its palette: the level scripts are machine
code, streamed off the disc, which is how a 133 KB executable runs 71 levels
with no bytecode interpreter anywhere in its symbol table.

**Check the first two bytes of every table entry before assuming a blob is
data**: `48e7` (`MOVEM.L`), `4e56` (`LINK A6`), `4eb9` (`JSR abs`) and `4afc`
(a module header) are all cheap to test and all change what the thing is.

---

## 10. Baselines, so you can tell signal from noise

Three very different discs, for comparison:

| | Ultra CD-i Soccer (1997) | Origami (1993) | Link: The Faces of Evil (1993) |
|---|---|---|---|
| Track | 7,875 sectors (1:45), 2.4 % of a CD | 326,400 sectors (72:32), 98 % | 255,924 sectors (56:52), 77 % |
| Entries | 12 dirs, 144 files, 10,635,729 B | 5 dirs, 46 files, 631,506,598 B | **0 dirs, 14 files** |
| Pre-FS region | 2,269 sectors, 28.5 % of the image | 2,268 sectors, 0.7 % | 2,269 sectors, 0.9 % |
| Head audio | 7.41 s + 8.31 s mono, L = R exactly | **29.39 s, byte-identical to Link** | **29.39 s, byte-identical to Origami** |
| Tail padding | plain zeroes | plain zeroes, 15,770 sectors | plain zeroes + 4 sectors of `0x20` |
| Executable | 229,376 B, 2 modules | 82,720 B, 4 modules | 135,168 B, 1 module (+ a 12 KB bumper) |
| Symbols in the binary | none | none | **325 C function names** |
| Streaming | one MPEG file | 79 % of the disc | 99.7 % of the bytes |
| Padding | 19.5 % of the one RTF | 26.7 % of the disc | **49.6 % of the disc** |
| Compression | none, bar the run-length sprites | none, anywhere | RL7 for everything that moves, 10.7:1 |
| Graphics | raw CLUT bitmaps + palettes on disc | DYUV and CLUT7, real-time only | CLUT7 playfields, RL7 cels, one DYUV still |
| Palettes | 192/384/768 B, entry 0 `#00FF00` | **none on the disc at all** | 384 B inline, entry 0 `#FFFFFF` |
| Audio | 12 effects, 10.2 s, Level C in AIFF-C | 5 languages, 3 h 57 m, raw | **100.8 min**, Levels B and C, raw |
| Video | MPEG-1 368 × 272 | 40 files, 7 streams each, no MPEG | no MPEG; RL7, CLUT7, DYUV |
| All-zero files | 16, totalling 1,070,080 B | none | none |
| Dangling path references | 8 | none | none |
| Duplicated files | none | none | **the same 30 MB pressed 3× (11.5 %)** |
| Languages shipped | English only | five — one of them not on the box | English only |

The three discs bracket the format. Soccer is what a *game* looks like on CD-i
when the program owns its assets: small files, a big executable, everything
loaded. Origami is what a *presentation* looks like: a tiny executable that owns
nothing and streams every pixel, including every letter of every menu. Link is
the hybrid and the most extreme case — a game whose executable owns almost
nothing, whose levels arrive as code and pixels together, and which spends half
its surface on keeping the drive fed.

If your disc compresses something, has more than one directory level, ships
palettes for CLUT data, uses MPEG, or carries symbols, it is doing something at
least one of these did not. **Carry the method forward, not the numbers.**

### Count the languages yourself

**Do not trust the language list in the dump's filename.** Origami is dumped as
`(En,Fr,De,Nl)` and carries **five** narration channels plus a five-entry
language menu plus five localised error screens — the fifth is Japanese. The
cheapest check is the audio channel census in section 9; the confirmation is
usually a menu screen, and on a disc with no text files it will be a picture, so
you will have to render it.

---

## 11. Order of work that worked

1. Extract to a raw image; confirm one `MODE2_RAW` / `CDI/2352` track and note
   the capacity used. That number sets your expectations for everything else.
2. `cdihead.py map` — the pre-file-system region, before anything else. **Hash
   the descrambled region and compare it against the known 1993 recording
   (`a0ed87f2e98b43f91281d16390fb178b`) before analysing anything.**
3. Volume descriptor and path table; note the application identifier, the
   publisher and the data preparer.
4. `cdifs.py list` / `map` / `extract`; note which entries have file number 1;
   all-zero census; date histogram against the volume date; hash files against
   each other.
5. **`cdisym.py list` on every executable.** If the symbols are there,
   everything after this is easier — read them in address order.
6. `os9mod.py`: parity and CRC on every module; the bytes after each header;
   grep for `Armendariz`; look for the shared `ctype` table.
7. `cat` the copyright / abstract / bibliographic files — and anything else in
   the root that nothing references.
8. Filtered strings; then the two-way path cross-reference, per executable.
9. **Subheader census by channel** on every real-time file. This tells you
   whether the disc is a game or a presentation.
10. Record structure: `EOR`/`EOF` lists per channel, then padding runs, then
    triggers.
11. Audio: decode the coding byte, check the duration arithmetic, decode one
    channel per stream and *listen* — several questions only ears can close.
12. Graphics: palette sizes → pixel-value ceilings → autocorrelated pitch →
    **prove the total to the byte** → render everything.
13. Tagged chunks, validated structurally, and check whether any of them is code.

Write down what does *not* resolve. Half of what makes a disc interesting is the
list of things that are measurably odd and not yet explained.
