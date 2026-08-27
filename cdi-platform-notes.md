# CD-i platform notes — a checklist for the next disc

A running checklist, carried from one CD-i documentation pipeline to the next
and added to by each. It now covers **five discs, four years and two
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
- **Merlin's Apprentice** (Philips Interactive Media of America, USA, 1995) — a
  puzzle game on a third-party runtime: 18 files in a flat root, 39.5 % of a CD,
  everything the program owns inside one 8 MB container pressed three times, and
  **the linker's symbol file left in the root**.
- **The Apprentice** (The Vision Factory, Netherlands, 1994) — a platform game
  on a **CD-i Ready** disc: the entire volume hides in the 69,150-sector pregap
  of track 1, 74 % of the pressing is Red Book audio, and the soundtrack is
  pressed twice — once as CD-DA and once as ADPCM, mapped one to one by a table
  in the executable. It too left the linker's symbol file behind.

They have almost nothing in common at the content level, which makes the things
they *do* share worth trusting. Those are marked **[all]** when all five agree
and **[N of 5]** when fewer do. Older marks reading **[N of 4]** predate the
fifth disc and have not all been rechecked against it. Findings from only one
disc are named, and are the ones to test rather than assume.

The tools referenced live in the pipeline repositories:

- [cdi-ultracdisoccer-doc](https://github.com/vs-sr-dev/cdi-ultracdisoccer-doc)
- [cdi-origami-doc](https://github.com/vs-sr-dev/cdi-origami-doc)
- [cdi-linkthefacesofevil-doc](https://github.com/vs-sr-dev/cdi-linkthefacesofevil-doc)
- [cdi-merlinsapprentice-doc](https://github.com/vs-sr-dev/cdi-merlinsapprentice-doc)
- [cdi-theapprentice-doc](https://github.com/vs-sr-dev/cdi-theapprentice-doc)

`cdilib.py`, `cdifs.py`, `cdihead.py`, `os9mod.py`, `cdistrings.py`,
`cdiaudio.py`, `cdidyuv.py`, `cdirta.py`, `cdipic.py`, `cdicensus.py`,
`cdisym.py`, `cdistb.py` and `cdiready.py` are platform-general and should work
unmodified on another disc. `cdigfx.py`, `cdispr.py`, `cditeams.py`, `cdipf.py`,
`cdianim.py`, `cdibolt.py`, `cditoc.py`, `cdirtf.py`, `cdidat.py`,
`cdimusic.py` and `cdinvr.py` carry title-specific tables and are worth reading
rather than running.

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

### If every track says AUDIO, the disc is CD-i Ready — do not stop there

**Read the cue sheet before you read the image.** The Apprentice extracts to
this:

```
TRACK 01 AUDIO
  INDEX 00 00:00:00
  INDEX 01 15:22:00      <- a 15:22 pregap = 69,150 sectors
TRACK 02 AUDIO
...
TRACK 22 AUDIO
```

Twenty-two audio tracks, no data track anywhere, and grepping the image for
`CD-RTOS` finds nothing. That is not a bad dump. **CD-i Ready** puts the entire
CD-i volume inside track 1's pregap, so that an ordinary audio CD player —
which begins at INDEX 01 — plays straight past the game and treats the disc as
an album. The CD-i player starts at LBA 0 and sees a normal volume.

The consequence for your dump is mechanical and total: the ripper followed the
TOC, saw an audio track, and returned **raw channel data**. Everything from
byte 12 of every sector is still **scrambled**. Sync survives, because the
scrambler starts at byte 12, and that is the tell:

```
LBA 0    stored     00 ff ff ff ff ff ff ff ff ff ff 00  01 82 00 62
         sync intact ------------------------------^     ^ nonsense
```

Confirm it with four bytes rather than by eye. A Mode 2 sector at LBA *n*
carries MSF `(n+150)` in BCD plus mode `02`; the ECMA-130 Annex B sequence
begins `01 80 00 60`:

```
  LBA    stored       want       scrambler   xor
     0   01820062     00020002   01800060    01820062   <-- match
    16   01821662     00021602   01800060    01821662   <-- match
  1000   01952562     00152502   01800060    01952562   <-- match
 60000   12a20062     13220002   01800060    12a20062   <-- match
```

Then XOR bytes 12.. of every sector in the pregap and you have an ordinary
Mode 2 image. `cdiready.py probe` prints the table above; `cdiready.py extract`
writes the track.

Two things to note once it opens:

- **The volume space size counts the audio.** The Apprentice's descriptor says
  279,300 blocks; the CD-i area is 69,150. Do not use that field as the size of
  the data area — use the pregap length from the TOC.
- **The audio tracks may be in the file system.** See section 3.

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
sectors. Soccer uses 7,875 of them, Merlin 131,610, Link 255,924, Origami
326,400. That one number sets your expectations for everything that follows: a
disc at 2 % of capacity will have dead files lying around, and a disc at 98 %
will not. The middle of the range is its own signal — Merlin, at 39.5 %, has no
dead files at all but spends 7.9 % of the pressing on two redundant copies of
one library, which is what having room and no reason to economise looks like.

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
3. **Does the XOR produce something structured?** On all four discs a large
   part of it became 16-bit little-endian PCM.

Signals that a region is real audio rather than noise:

- `mean|x[n] - x[n-1]| / mean|x[n]|` well below 1 — 0.12 on Soccer, 0.124 on
  Link. Noise sits near 1.
- Left channel equal to right on every frame, or nearly so.
- A harmonic series in the averaged FFT rather than a flat floor.

**`cdihead.py map` labels anything non-zero "scrambled PCM", and that label is
inherited, not earned.** Verify it on your disc before writing the word "audio"
down: compute the smoothness ratio, check L against R, and take an FFT.

**[all]** **Compare the head against the tail.** On all four discs the tail
padding is *plain* zeroes while the head padding is *scrambled*. Two filler
mechanisms in one image means two different tools touched it, and it has now
held on four unrelated pressings.

**Link has a third mechanism**: four sectors at 255,770–255,773, 2,324 bytes
each, **every byte `0x20`** — ASCII spaces. Not zero, not scrambled zero.
Classify padding by content, not by position.

**The audio does not have to start at sector 0.** It is aligned to the *end* of
the region, not the beginning:

```
Origami   0-15 zeroes, 16-17 volume descriptors, 18-2267 audio  (2,250 sectors)
Link      0-15 zeroes, 16-17 volume descriptors, 18 zero,
                                                19-2268 audio  (2,250 sectors)
Merlin    0-15 zeroes, 16-17 volume descriptors, 18 zero,
                                                19-2268 audio  (2,250 sectors)
Soccer    a mix: 1,064 sectors of scrambled zero, 1,203 of PCM
```

On three of the four discs the audio is **exactly 2,250 sectors ending
immediately before the path table**, and Merlin's layout is Link's to the
sector — two years later.

### The head-region audio is the same recording on three unrelated discs

This is the strongest result the pipelines have produced, and it was found by
comparing rather than by analysing.

*Link: The Faces of Evil* (USA, Animation Magic, Philips Interactive Media of
America) and *Origami* (Netherlands, EagleVision) share **no developer, no
publisher, no continent and no content**. Descramble both head regions and
they are **byte-identical**. *Merlin's Apprentice*, mastered **two years
later**, makes it three:

```
Link     sectors 19-2268    5,229,000 B    md5 a0ed87f2e98b43f91281d16390fb178b
Origami  sectors 18-2267    5,229,000 B    md5 a0ed87f2e98b43f91281d16390fb178b
Merlin   sectors 19-2268    5,229,000 B    md5 a0ed87f2e98b43f91281d16390fb178b
```

All 5,229,000 bytes. Same MD5. **The recording is not title content — it is an
artefact of the CD-i authoring chain**, laid down by whatever tool wrote the
pre-file-system region — and it was still being laid down in **January 1995**,
not only on the 1993 masters. Whatever wrote it stayed in service for at least
two years and across at least two development houses.

*Ultra CD-i Soccer*, in 1997, carries a **different** recording in the same
place — two mono clips of 7.41 s and 8.31 s, bit-identical left and right,
fundamentals near 151 and 161 Hz. No 512-byte run of one appears in the other.

*The Apprentice*, in **October 1994 — inside the window the trio brackets** —
carries a **third**:

```
Apprentice  1994  sectors 18-2267   5,229,000 B  md5 4e61f608e1f1455d9ad5b2a0615dbbd3
```

So the filler is not one artefact of one tool. Its structure is common across
all five discs and its content is not, and date does not predict which
recording you get: a 1994 disc has neither the 1993–95 one nor the 1997 one.

On the *structural* tests The Apprentice sits with Soccer rather than with the
trio — two separate clips rather than one continuous take, and left
**bit-identical** to right (100.00 % of frames) rather than merely correlated
at 0.9988. And its peak is exactly **16,383** = 2^15/2 − 1, which no other
disc's filler shows: either a 14-bit source or a deliberate 6 dB attenuation.

**Do the comparison properly.** Both clips contain long runs of digital
silence, so a naive substring search reports matches that are silence matching
silence. Sample only *non-trivial* windows — reject any window with two or
fewer distinct byte values — and search both directions. Against Soccer's two
clips that gives **zero matches in all four directions**.

So: **hash the descrambled head region of every new disc and compare it against
all three known recordings** before spending any time analysing it. That is a
thirty-second check, it has now answered the question outright four times, and
the window it covers is wider than the first two discs suggested.

The 1993–95 recording, measured:

```
29.64 s at 44,100 Hz stereo      1,307,250 frames   (2,324 B/sector)
mean |x|                               814.3
mean |x[k] - x[k-1]|, boundaries excluded   123.4
peak                                  23,345
L == R exactly                          1.40 % of frames
corr(L, R)                             0.9988
fundamental                         169-171 Hz, drifting
second harmonic                     339-341 Hz
one silence                          8.6-8.9 s, so two takes
```

*(Two corrections to an earlier revision of this block, both found by measuring
the identical bytes on Merlin. The duration was given as 29.39 s and
1,296,000 frames, which is what a **2,304**-byte-per-sector extraction yields;
the lag analysis below needs the full **2,324** bytes of Form 2 user data, which
gives 581 frames per sector and 29.64 s. And the "smoothness ratio 0.124" was
quoted without its denominator — 123.4 over neither mean |x| (814.3, ratio
0.152) nor RMS (1,736.2, ratio 0.071) reproduces it. **Quote the two means, not
the ratio.** The screening rule is unaffected: a signal is plausibly audio when
mean |x[k] - x[k-1]| is well below mean |x|, and noise when it is not.)*

A drifting ~170 Hz fundamental with strong harmonics is what a male speaking
voice looks like, and 29 seconds in two takes is what a slate or an
announcement looks like. Nothing identifies the speaker. This is a question
only ears can close.

### Why it is scrambled: the mechanism, and how many bytes are lost

Origami worked this out, and Link and Merlin confirm it on identical data.

**[3 of 4]** The head sectors have correct sync, a correct MSF header, a valid
subheader — and a **wrong EDC on almost every one**. The subheader is
`00 00 20 00` on all 2,250 sectors of Origami, Link and Merlin alike: Form 2
with none of the data, audio or video bits set, a sector declaring itself to be
of no type at all.

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
Link    EDC field values across 2,250 sectors:  {'00000000': 2250}
        descrambles to zero:                    0 / 2250
```

**Merlin has one exception, and it is the last sector.** 2,249 of its 2,250
carry a zeroed EDC field; sector 2,268 — the one immediately before the path
table — carries `33 34 48 aa`, and that is a **correctly computed Form 2 EDC**
over bytes 16–2347:

```
Merlin  EDC field values:  {'00000000': 2249, '333448aa': 1}
        sector 2,268 computed EDC  aa483433
        sector 2,268 stored, LE    aa483433   <== valid
```

So the region is closed off by one properly finished sector while the 2,249 in
front of it are not. Check the last sector of the run separately from the rest;
whatever wrote this region treated its final block differently, and on a disc
where that closing sector is missing or different you have a different tool.

Extending the extraction to 2,328 bytes per sector to try to recover those four
bytes makes the discontinuity far worse (8,839 against 455), which confirms they
were overwritten rather than merely discarded. **Seven frames per sector are
gone and cannot be recovered.**

Merlin reproduces Link's measurement on the identical bytes, to the tenth:

```
Merlin, left channel, 2,324 B/sector
  mean |x[k] - x[k-1]| away from boundaries    123.4
  mean |x[k] - x[k-1]| at a sector boundary    455.6
  lag 6 expectation                            405.5
  lag 7 expectation                            454.7   <== match
```

Two independent pipelines, the same four numbers. If your extraction does not
reproduce them on data with this MD5, the extraction width is wrong — it must be
**2,324 bytes per sector**, 581 stereo frames, not 2,304.

*(An earlier revision of this document, written from Origami alone, put the loss
at six frames / 24 bytes. That accounted for sync + header + subheader and
missed the zeroed EDC field. Link's per-channel measurement lands on lag 7 to
within 0.1 %, and Merlin's on the same bytes lands within 0.07 %.)*

Run this lag test on your disc. A boundary jump matching lag 7 is the same
mechanism.

One difference worth recording: Soccer's and The Apprentice's head clips are
**bit-identical** left and right; the 1993 recording correlates at 0.9988 but is
not identical — a mono source through a stereo converter rather than a
duplicated channel.

### The filler may be written twice, head *and* tail

**[1 of 5]** Link, Origami, Merlin and Soccer all pad the tail of the volume
with plain zeroes. The Apprentice does not: it writes the identical 2,250-sector
block a second time, immediately after the last directory sector.

```
head  18-2267      5,229,000 B  md5 4e61f608e1f1455d9ad5b2a0615dbbd3
tail  57868-60117  5,229,000 B  md5 4e61f608e1f1455d9ad5b2a0615dbbd3
```

Same length, same internal run structure — 623 sectors of PCM, 446 of zero,
1,119 of PCM, 62 of zero — same MD5. **Hash the sectors after your last file as
well as the ones before your first.** It costs one more call and it is a
different tool, or the same tool configured differently, whenever it hits.

Whether this is a CD-i Ready consequence — the data area has an audio track
behind it rather than a lead-out — is not decidable from one disc. The next
CD-i Ready title settles it.

### An audio-mode dump shows the same region in the clear

The model above says the authoring tool wrote the audio in the *post-scramble
channel domain*, which is why a data-mode rip — which descrambles — yields
audio XOR scrambler. **The Apprentice confirms that from the other side.** Its
dump was never descrambled (section 1), and the identical region reads as
**plain PCM with no XOR at all**:

```
raw bytes 24..2347   mean|x| 1750.7   mean|dx| 274.5   ratio 0.157   L==R 100.00 %
XOR scrambler        mean|x| 16432    mean|dx| 21102   ratio 1.284   L==R   0.00 %
```

Same object, two views, and the second one is the confirmation the first four
pipelines could only infer. The sector metadata is identical to the other
discs: subheader `00 00 20 00` and EDC field `00 00 00 00` on all 2,250 (and on
all 2,250 of the tail copy too).

### Quote the lag index with its convention

The boundary test reproduces on The Apprentice and gives the same 28 bytes, but
the *index* differs by one, and the reason is worth writing down so the next
pipeline does not think it has found a discrepancy.

If `D` frames are missing at a boundary, the two surviving frames either side of
it are `D + 1` apart in the original stream. So a boundary jump matching
`mean|x[i] - x[i-k]|` means **`D = k - 1` frames are gone, not `k`**.

```
Apprentice, left channel, 2,324 B/sector
  clip A  boundary 1275.2   matches lag 8 (1258.6)   ->  7 frames, 28 bytes
  clip B  boundary 1200.9   matches lag 8 (1225.5)   ->  7 frames, 28 bytes
```

which is sync (12) + header (4) + subheader (8) + EDC (4) = 28, the same answer
the Link and Merlin measurements reach. **State whether your lag number is the
separation or the loss.**

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
  On Origami, Link and Merlin all three agree, and the path has no directory
  component at all — the boot file sits in the root. Three of four discs
  therefore make this field trustworthy and the fourth makes it interesting;
  either way it is one read.
- **[all]** **`copyright`, `abstract` and `biblio`, if named, are plain text on
  the disc.** Always `cat` all three. Soccer's held marketing copy and the
  complete credits. Origami's held the credits, a placeholder abstract
  (`ORIGAMI is great!`) and three different spellings of the publisher's own
  name, one of them inside the copyright notice. **Link's `BIBLIOGRAPHY` is the
  complete credit roll** — producer, script, three programmers, graphic design,
  video, audio and music — and it is the only place several of those names
  appear. **Merlin names all three with a `.txt` extension**, and its
  `abstract.txt` is the only one so far that was actually written for the job:
  823 bytes of catalogue copy that states the game's puzzle and potion counts,
  both of which turned out to be checkable against the executable and both of
  which were right.
- **Grep the text files for `@(#)`.** All three of Merlin's open with an SCCS
  what-string:

  ```
  @(#)copyright.txt	1.1	2/25/94
  @(#)abstract.txt	1.1	2/25/94
  @(#)bibliography.txt	1.2	6/7/94
  ```

  That is the keyword SCCS expands on checkout and the `what` utility looks for.
  It dates each file, gives its revision number, tells you which files were
  revised and which were written once, and tells you the source tree was under
  SCCS. It costs one grep and it works on any shipped text, not just these
  three.
- **`cat` anything in the root that nothing references, too.** Origami has a
  `message.txt` that no file names and the executable never opens, holding the
  studio's address and the programmer's **private home address and telephone
  number**. If you turn up personal data belonging to a living private
  individual, record that it is there and what kind of thing it is, and think
  before giving it a second publication.
- **Read the publisher and data preparer fields as carefully as the application
  identifier.** The preparer field is usually blank. On Origami it is a person's
  name, and the same name is the sole programming credit. On Link **and on
  Merlin** it is `_ISG_CDI_TOOLS_1.6` — the disc naming its own authoring tool,
  at the same version number, on discs two years and two development houses
  apart. That makes the string a dating tool with a known-wide window rather
  than a fingerprint of one studio. And Link's
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
8 world-read, 10 world-exec, **14 CDDA**, 15 directory. Expect `0x0555` for
files and `0x8111` for directories; anything else is worth a look (Soccer's
flagged the path table exposed as a file, and so does Link's).

*(Earlier revisions of this document put CDDA at bit 12, which is what the
commonly circulated table says. It is bit 14. The Apprentice is the first disc
here to exercise it: its twenty-two `/CDDA/trackN.cda` entries are the Red Book
tracks exposed as files — their extents are past the end of the data area
entirely — and every one carries `0x4111`, while nothing else on the volume sets
bit 14. Four discs had passed through without touching the bit, so the error
was invisible.)*

**On a CD-i Ready disc, look for a `/CDDA/` directory.** The audio tracks may be
addressable as files, which gives you their LBAs and lengths from the directory
rather than from the TOC, and gives the program a way to name them. The
Apprentice's are `track2.cda` through `track23.cda` — **numbered by their
physical track, so the count starts at 2**, because track 1 is the CD-i area
itself. A CHD or cue will call those same tracks 1 through 22. Expect the
off-by-one and say which numbering you are using.

**And permissions may actually mean something.** Merlin marks every entry
`0x0555` whether it is code or not. The Apprentice gives `0x0555` only to the
seven OS-9 modules and `0x0111` to all 62 data entries, so on that disc the
execute bit is a reliable filter for "this is a program".

**[2 of 4] The file number byte in the system-use area is `1` on real-time
files and `0` on everything else.** On Link that byte is the only mechanical
difference between a 30 MB stream and an executable at the file-system level,
and it is what tells the driver to hand the file to the real-time reader. Check
it before you trust any directory size.

**And the byte can simply be wrong.** The Apprentice sets it to 1 on every
real-time file it built and to **0 on `/CMDS/philips.rtf`** — 625 sectors of
Form 2 with RL7 video, DYUV and Level B stereo audio across three channels,
which is unambiguously a stream. That is the one real-time file on the disc the
studio did not make: it came from the publisher (see section 5b). The program
opens it anyway. So the byte tells you what the *driver* will do, not what the
file *is*, and a file supplied from outside the build is where it is most
likely to disagree.

**And do not read the extension instead.** Merlin's eleven file-number-1
entries include three `.blt` files that are not streams at all — they are an
asset archive the program random-accesses — alongside eight `.rtf` ones that
are. All eleven are Form 2 at 2,324 bytes and all eleven have a directory size
that is `sectors x 2048`, which is wrong by 13.5 %. **The byte is authoritative;
the extension is a naming convention.** A file that is real-time in the
file-system sense need not be a stream in the content sense.

**A flat root is normal for a streaming disc.** Soccer has 12 directories and
144 files; Origami has 5 and 46; **Link has none and 14**; **Merlin has none and
18**. Directory count correlates with how much the program owns rather than
streams — and Merlin shows the limit case of that rule, because it owns almost
everything but keeps it inside one archive file rather than in a directory
tree.

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
  Origami had none and sits at 98 % of capacity; Link had none and sits at 77 %;
  Merlin had none and sits at 39.5 %. **A disc with no slack is a disc with no
  dead files** — but Merlin shows the converse does not hold. Slack buys dead
  files, it does not guarantee them, and a tidy build at 39.5 % is perfectly
  possible. Measure the capacity to set expectations, then check anyway.
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

  **The seconds either side of the volume descriptor are worth reading too.**
  Merlin's last three timestamps are consecutive — `13:57:18` on
  `cdi_merlin.stb`, `13:57:19` on the executable, `13:57:20` on the descriptor.
  Nothing postdates the descriptor, so the build is tidy; but the file one
  second *older* than the executable turned out to be the linker's symbol table,
  produced by the same link and mastered with it. **Sort the listing by
  timestamp and look at whatever sits immediately before the executable.**
- **Gaps in the sector map.** Anything owned by `<free>` inside the file area
  deserves a hexdump. On Link the gaps are plain zeroes and they are structural:
  two runs of 2,872 sectors sit immediately in front of the two files the game
  must start streaming without a hitch — 38 seconds of disc for the drive to
  settle.
- **[2 of 4] Hash every file's payload *and* its subheaders.** Link presses the
  same 30 MB file three times — `ldata.rtr`, `ldata1.rtr`, `ldata2.rtr`, byte-
  identical down to the channel and submode bytes — at LBA 2,992, 115,786 and
  235,940. That is 11.5 % of the disc spent so a single-speed drive is never far
  from the game's working set. **Merlin does exactly the same thing** with
  `boltlib0.blt`, `boltlib1.blt` and `boltlib2.blt` — 8 MB each, one MD5, one
  timestamp, 7.9 % of the disc, at LBA 2,340, 122,447 and 125,893. Two discs of
  four now trade space for head movement this way, so **if two files hash the
  same, look at their LBAs before calling it a mistake.**

  **But check whether the binary knows all the names.** Link's executable names
  all three of its copies. Merlin's contains only the string `boltlib0.blt` and
  computes the digit: `OpenNextFile` increments a copy index, compares a global
  the symbol table calls `ErrorRetry` against an immediate `#3`, and adds the
  index as a character into the name buffer. So on Merlin a plain string
  cross-reference reports two files that nothing references, and the correct
  conclusion is the opposite of the obvious one. **A filename ending in a digit,
  with siblings, is a filename that may be generated.**

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

**[2 of 4] Not every module has `M$Init`/`M$Term`.** On Link the header ends at
72 and the name string starts there, so reading offsets 72 and 76 as pointers
yields `0x6364695f` and `0x6c696e6b` — the ASCII of `cdi_link` itself. Merlin
does the same: `M$Name` reads `0x48`, the name starts there, and offsets 72 and
76 read as `0x6364695f` / `0x6d65726c`, the ASCII of `cdi_merl`. **If those two
fields look like text, the header is 72 bytes, not 80.**

### The `-F` after the module name is not a linker option — it is `_cstart`

Earlier revisions of this document recorded that Origami's and Merlin's main
modules read `<name>` NUL `-F` NUL, and read the `-F` as a linker option string
of unknown meaning. **It is neither a string nor an option.** It is the first
instruction of the Microware C entry sequence, rendered as ASCII:

```
2D46 8010    MOVE.L  D6,-$7FF0(A6)      "-F"
2D46 8014    MOVE.L  D6,-$7FEC(A6)      "-F"
3D43 8018    MOVE.W  D3,-$7FE8(A6)      "=C"
082B 0005    BTST    #5,$0005(A3)
```

The check that settles it is one field: `M$Exec`. On Merlin's `cdi_merlin` it
is `0x54`, the name ends at `0x52`, and the sixteen bytes at `0x54` are
`2d 46 80 10 2d 46 80 14 3d 43 80 18 08 2b 00 05`. The "option" is at the entry
point. The Apprentice's `cdi_philips`, `cdi_factory` and `cdi_invaders` open
with the identical sixteen bytes, and its `cdi_loader` appeared to carry a
different option, `=C`, only because its name is a different length and the
window lands on the third instruction instead of the first.

**Which makes it a better fingerprint than the thing it was mistaken for:**

```
2d 46 80 10 2d 46 80 14 3d 43 80 18 08 2b 00 05
```

Four modules on one disc and one on another, two studios and two years apart,
open with that run. Grep for it, and treat any module that does *not* have it as
worth a second look — The Apprentice's `cdi_app`, the one module whose symbol
file shipped, is the only one on its disc that starts differently
(`20 7c ff ff 9d d2`, `MOVEA.L #$FFFF9DD2,A0`).

Expect several modules concatenated with no padding — Origami's executable is
four (`Prgrm`, `Sbrtn`, `Prgrm`, `Trap`) whose sizes sum to the file size
exactly, and Link's second executable is two (`Prgrm` plus a 128-byte `Data`
module). A tiny module of type `$B` (Trap) next to the main program is normal:
it is the shared-library / trap-handler mechanism.

**Read the edition byte.** Link's game module is edition 1 and its bumper is
edition 7 — the logo player was reworked seven times and the game never was.
Merlin's single module is edition 7, so the same number can belong to the game
itself; read it as a revision count, not as a role.

**[3 of 4]** **Look at the bytes immediately after the header, before any
code.**
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

**A negative result is informative.** Merlin has nothing between its header and
`_cstart` but the module name — no font, no author list —
and no `Armendariz` and no `cdi_bpsys` anywhere in the binary. It does not link
the Philips base program system at all; it links a third-party runtime (BOLT)
whose graphics, audio, input and disc code is all first-party. So the absence of
that name is not a failed grep, it is a finding: **this title used somebody
else's engine.** Check what fills the gap before concluding the grep was
wasted.

**[2 of 5]** The Apprentice has no `Armendariz` and no `cdi_bpsys` either, and
what fills its gap is neither a Philips library nor a third-party one: a
9,410-byte `Sbrtn` module the studio wrote (`cdi_start`) plus direct OS-9 calls.
So the absence has now meant two different things on two discs, and the follow-up
question — *what is there instead* — is the one that pays.

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

**Merlin gives it its name and its full form: `_chcodes`, 129 bytes.** The
symbol file calls the global `_chcodes` and puts the next global 129 bytes
later; the initialiser sits at module offset `0x21092` and is the same table
with the leading guard slot Link's 124-byte window cuts off:

```
021092  00 01 01 01 01 01 01 01 01 01 11 11 01 11 11 01
0210a2  01 01 01 01 01 01 01 01 01 01 01 01 01 01 01 01
0210b2  01 30 20 20 20 20 20 20 20 20 20 20 20 20 20 20
0210c2  20 48 48 48 48 48 48 48 48 48 48 20 20 20 20 20
0210d2  20 20 42 42 42 42 42 42 02 02 02 02 02 02 02 02
0210e2  02 02 02 02 02 02 02 02 02 02 02 02 20 20 20 20
0210f2  20 20 44 44 44 44 44 44 04 04 04 04 04 04 04 04
021102  04 04 04 04 04 04 04 04 04 04 04 04 20 20 20 20
021112  01
```

Index 0 is the guard slot for `EOF`; index *n*+1 is ASCII *n*; tab, LF, FF and
CR are `0x11`. So the run to grep for is
`01 01 01 11 11 01 11 11`, which is present on both discs regardless of where
the window starts, and the symbol to look for in a symbol table is `_chcodes`.

Finding it proves a shared toolchain and anchors the runtime's data area. The
runtime's panic strings are usually nearby, and their wording dates the build:
Link's game says `**** Can not install trap handler ****` and its bumper says
`**** Can't install trap handler ****` — the same message from two vintages of
the same library. Merlin, in 1995, has the contracted form, and also carries
`**** Stack Overflow ****` and
`Unexpected signal number $%X - Application Terminating`.

**Look for a libgcc-to-Microware shim while you are in the runtime's data
area.** Merlin has five `BRA.W` instructions four bytes apart at `0x1d452`:

```
__mulsi3    -> _T$UMul / _T$LMul      __umodsi3  -> _T$UMod
__udivsi3   -> _T$UDiv                __modsi3   -> _T$LMod
__divsi3    -> _T$LDiv
```

Every branch target is the Microware routine its GCC name calls for, and
`_T$UMul` and `_T$LMul` are the same address. Something in that build was
compiled by a GCC-family compiler emitting calls to libgcc, and twenty bytes of
thunk land them on the Microware runtime instead. A row of equally spaced
`60 00 xx xx` near the arithmetic helpers is what it looks like without symbols.

---

## 5b. The publisher's bumper is a shared asset — hash it

**[2 of 5]** Discs published under the Philips banner may carry a bumper stream
that the publisher supplied as a finished file. It is not the studio's work, it
is **pressed verbatim**, and it is therefore another free identity check of
exactly the kind the head-region MD5 is.

*The Apprentice* (The Vision Factory, Netherlands, 1994) and *Link: The Faces of
Evil* (Animation Magic, USA, 1993) carry the same one. Not similar — the same
bytes:

| stream | channel | coding | sectors | payload | MD5 |
|---|---:|---|---:|---:|---|
| RL7 video | 15 | `0x04` | 121 | 278,784 B | `383be6befb8eed0330da9b0815cfc869` |
| Level B stereo audio | 15 | `0x01` | 149 | 343,296 B | `c8547a7e48868733bdfc90cd5b9ef79a` |
| DYUV picture | 16 | `0x05` | 40 | 92,160 B | `4f19073bf9f625c6e620a065ad43cb5f` |
| descriptor | 16 | data | 1 | 2,304 B | differs — 37 bytes, all past offset 2,048 |

**714,240 bytes identical across two studios, two continents and eighteen
months.** Same channel numbers, same codings, same sector counts. Only the
single descriptor sector differs, and its first 2,048 bytes match exactly.

The DYUV payload is 40 × 2,304 = 92,160 = **384 × 240**, which is the geometry
proved by arithmetic rather than guessed — worth knowing before you try to
decode it.

**The descriptor sector opens with a magic and a field dictionary:**

```
ba be fa ce  00 00 00 05  00 00 00 00  01 00 00 00 ...
... "cluts\0count\0filenum\0frame_sizes\0"
```

`0xBABEFACE`, with the tag names `cluts`, `count`, `filenum`, `frame_sizes`.
Grep a new disc for `babeface`; it costs nothing and it names the container.

Two practical consequences:

1. **Hash the DYUV payload of any bumper you meet** against
   `4f19073bf9f625c6e620a065ad43cb5f` before decoding it.
2. **A bumper's sector metadata may not match the rest of its disc** — see
   section 3 on the file-number byte, which is wrong on exactly this file.

Merlin's Apprentice, published by the same company, ships no bumper stream at
all, so this is not universal even within one publisher.

---

## 6. Symbols, strings, and the two tricks that pay best

### 6a. Look for symbols before anything else — in two different places

**Do this as step four, not step ten.** On a disc that has symbols they are
worth more than everything else in this document combined, and there are two
quite different ways they survive. Check both.

#### 6a-i. A `.stb` file in the directory listing

**Check the file listing for a `.stb` before you open a single binary.** It is
one glance at output you already have.

Merlin's root holds `cdi_merlin.stb`, 19,752 bytes, which nothing on the disc
references and the program never opens. It is the **Microware linker's global
symbol table**, wrapped as an OS-9 Data module so a debugger could `modload` it
beside the program, and it carries **887 symbols with their source-level
names** — the game, the title's runtime, the Microware C library and the OS-9
system-call library, all four.

```
python tools/cdistb.py stats
python tools/cdistb.py list
```

The record array follows the module name; each record is 16 bytes:

```
u16  hi         high word of the symbol's 32-bit value
u16  value      low word
u16  flags      0x0004 on code-section symbols, clear on globals;
                0x2000 and 0x1000 appear to be visibility, unconfirmed
u32  nameoff    offset of a NUL-terminated name, module-relative
```

The name offsets climb monotonically and every delta is the previous name's
length plus one, which is what identifies the layout. **The two 16-bit halves
are one 32-bit number**, `addr = (hi << 16) | value`, and `hi == 0xffff` means a
negative A6-relative offset — where an OS-9/68000 C program keeps its globals.

**Prove the address model before you trust a single name**, with checks against
the executable's own header:

```
btext    -> 0x00000000     the module's first byte
etext    -> 0x0002196a     exactly M$Size
_cstart  -> 0x00000054     exactly M$Exec
```

and then in bulk: of Merlin's 576 code symbols, **452 land on `4e55` (`LINK
A5`), `2f00` (`MOVE.L D0,-(SP)`) or `48e7` (`MOVEM.L`)** — C-compiler
prologues. If your model is wrong that number collapses.

A `.stb` also hands you things a name-string scan cannot: the **globals**, with
their offsets, so adjacent symbols bracket each structure. Merlin's
`R250_Buffer` is followed by the next global exactly 500 bytes later — 250
16-bit words, which names both the generator (R250, Kirkpatrick–Stoll) and its
state size. The same trick gives `_chcodes` its 129-byte length in section 5.

#### 6a-ii. Loose name strings inside the binary

Link's `cdi_link` carries **325 C function names** — NUL-terminated ASCII, laid
down in link order between the functions themselves, with no symbol file
anywhere. Neither Soccer nor Origami had either kind, so the technique was never
tried until the third disc.

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

**A `.stb` behaves the opposite way, and that is a feature.** Merlin's contains
`main`, `printf`, `malloc`, `strcmp`, `memcpy`, `open`, `read`, `lseek`,
`modload`, `os9fork` and the rest, because the linker records everything it
linked. That lets you separate the title from its libraries by section and by
address run, and it is how the libgcc thunk table in section 5 was found. It
also means the symbol count is not a measure of how big the game is: of Merlin's
887, only 83 are the game.

**What to look for is the same either way.** Merlin's list gave up eight
`*ChallEngine` functions, a `BOSS_*` state machine with paired
`Get`/`Set` accessors for every piece of game state — including a
`BOSS_GetCheatMode` / `BOSS_SetCheatMode` pair built with the same verbs as the
difficulty accessors — a `KludgeTwoControllers` global sitting in the input
block, and `TC_Set_Animation_Paramter` shipped misspelled beside a correctly
spelled `TC_Get_Animation_Parameter`. The apology, the misspelling and the
undocumented mode are all the same species as Link's `fix_jump_stick`,
`check_vulnerablility` and `retrieve_mkt_demo`.

**Intersect symbol addresses with string offsets.** This is the move that pays
on a disc with a symbol file, because you get address ranges for free. Merlin's
executable holds 30 identifiers ending in `DD`; bucketing them by which
`*ChallEngine`'s address range they fall inside splits them into 3 + 4 + 6 + 3 +
2 + 3 + 6 puzzles and 3 potions, which is **27 and 3** — exactly the counts the
disc's own `abstract.txt` claims. Neither list alone says that; the intersection
does.

**And read the abbreviations out of the error strings.** Forty of Merlin's
symbols carry a `TC_` prefix that nothing expands, until the diagnostics turn up
`Traffic Cop Could Not Open Animation:%d`. Prefixes are cheap to collect and
their expansions usually shipped.

### 6b. Cross-reference every path-shaped string, in both directions

68000 object code produces enormous amounts of accidental ASCII, so filter:
keep runs that are mostly letters and whose words are long enough to be
language. `cdistrings.py` does this and separates disc paths from prose.

**Strip the epilogue bytes off the front of every hit.** A string literal placed
immediately after a function picks up that function's last instructions, and
`4E5D 4E75` — `UNLK A5; RTS` — renders as `N]Nu`. On Merlin that prefix hid the
*first* name of every cluster: `N]NuPondDD` fails a name filter that `FlasksDD`
passes, so the filter silently drops one entry in every group of related
strings. Strip a leading `N]Nu`, `NuNu`, `N]` or `Nu` and advance the offset to
match. Also split concatenated names: the linker packs `PondDD`, `FlasksDD` and
`StalacDD` with no separator a printable-run filter can see.

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
Origami's and Merlin's are clean too. Say so — it dates the cut.

**[2 of 4] Names may live in a data file rather than in the binary.** Merlin's
executable contains six distinct paths and no more. The seven filenames its
animation system streams from appear **nowhere in the executable** — they are in
`tcanim.toc`, a 472-byte root file with a seven-slot name table at the end. So a
binary-only cross-reference reports seven unreferenced files and gets the disc
badly wrong.

Before concluding anything dangles, look for a small root file whose size is
suspiciously exact and read it as a table. Merlin's is fully accounted for:
`4 + 89 x 4 + 7 x 16 = 472`, and every one of its 89 entries resolves to a real
sector on the disc. **If a candidate index file exists, verify it against the
image rather than believing it** — that check is what turned an inference into a
result.

**Check which executable names what.** Link's game binary never mentions
`bumper.rtf`; the bumper program does, and the game names the bumper. The chain
`cdi_link` → `cdi_bumper` → `bumper.rtf` falls straight out of the two string
lists.

Other things to grep for by hand once the filter has run: profanity and
`debug`/`test`/`cheat` (Soccer had `CHEAT MODE ON` and an unprintable error
message), `TV`/`MONITOR`/`WINDOW` (development-host options — Link carries
`625` and `TV`, the strings that ask CD-RTOS for a 625-line PAL display, on a
disc that shipped to 525-line NTSC territory; Merlin carries both too, on a
European pressing where they are unremarkable), and placeholder prose — look
specifically *between* legitimate string blocks, which is where Soccer hid a
copyright notice nobody replaced.

**Collect the `printf`-shaped strings as a set.** Anything containing `%d`,
`%s` or `%X` is a developer diagnostic that was never compiled out, and read
together they describe subsystems the user never sees. Merlin ships fourteen,
including a state machine narrating itself (`BOSS: CurrentState = %d`), a bit
packer complaining about its own parameters, and one column-aligned for a
terminal that is not attached to a television:

```
BOSS_Init:  NumBitsPerVar = %d.  Should be 1,2,4, or 8.            -- using 8.
```

A `dprintf` in the symbol table with a dozen callers is the same finding as
Origami's video-plane hex dumper: a serial host on the other end of a cable,
still wired up on the pressing.

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
byte 3. This is free — decode it before trying widths or sample rates.

**It is authoritative for audio on all four discs. For video it is only
authoritative when the MCD212 decodes the stream itself.** Merlin tags all
3,446 sectors of a `.blt` file with video coding `0x02`, and the content is not
CLUT8 or any other picture format — it is an asset archive. It tags its
animation video channel `0x00`, nominally CLUT4 at normal resolution, and the
data does not autocorrelate at any CD-i line pitch and has bit 7 set on 43.6 %
of its bytes. Where a title expands its own frames in software before handing
them to the hardware, the coding byte records what the *display* will eventually
be given, or nothing meaningful at all. Trust it for audio; corroborate it for
video.

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
Neither 1993 disc has a single MPEG sector, and neither does Merlin; only Soccer,
in 1997, uses one.

Normal resolution is **384 × 280 on PAL** and **384 × 240 on NTSC**. Both are
384 bytes to the line for the one-byte-per-pixel codings.

**But not every bitmap on a CD-i disc is 384 wide.** The Apprentice's
autocorrelation comes back 384 on its full screens and an unambiguous **320** on
two of its chunks — a 320 × 21 status panel shared byte-for-byte by all six
level files, and a 320 × 191 chunk in the intro file. Those are sub-screen
graphics blitted into a 384-wide framebuffer, not display modes. Take the
measured pitch over the expected one, and let the odd pitch tell you the object
is not a full screen.

**[all]** **A change of coding byte inside one title marks a change of
purpose.** Origami is Level C for all five narration tracks and Level B for
exactly one channel in one file — which turns out to be the only music on the
disc. Link is Level C throughout `lmusic.rtr` except for the last record on the
last channel, which is Level B stereo, and Level C mono throughout the cutscene
soundtrack except for the final record, which is Level B stereo. Merlin is Level
B mono everywhere and Level B **stereo** for exactly 149 sectors — every fourth
sector of one 7.9-second animation, which contains no mono audio at all. Three
of four discs, three different base levels, and in each case one outlier.

**And a disc may have no outlier at all.** The Apprentice is Level C stereo
(`0x05`) on every one of the 26,064 audio sectors in its own three real-time
files — no exception anywhere. Its only other coding bytes are in the two
bumpers, and one of those is not its own work. So run the census, but do not
assume the outlier exists: a title that never varies its audio is telling you
something too, and here what it is telling you is that the music was authored
as one batch (section 8).

**When you find the odd coding byte out, you have found the thing that was worth
twice the bandwidth.** On both 1993 discs it was the ending. **On Merlin it is
not** — the stereo sequence is the thirteenth of the fifteen animations in the
file that holds the prologue and the interstitials, with two more after it. So
the rule about where to look holds and the guess about what you will find does
not. Locate the outlier, then play it; do not name it from its position.

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
palettes**; The Apprentice uses `#000000` on the one 768-byte palette shared by
all eighteen of its map files. Three discs, three colours. The reliable test is
not the colour, it is the usage: count how often index 0 appears in the pixel
data. On Link it appears in only 6 of 71
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

**[2 of 4]** Both Soccer's sprites and Link's animation frames sit in
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
Level B mono      one sector in nine (9.4)  -> up to 8 channels in practice
Level B stereo    one sector in four (4.7)  -> up to 4 channels
```

A Level B sector holds the same 4,032 samples but plays them at 37,800 Hz, so it
is **0.1067 s** mono — half a Level C sector's worth of time for the same bytes.

Link's `lmusic.rtr` runs **eight** Level C stereo channels, which is the
theoretical maximum; Origami runs five Level C mono narration channels alongside
two video streams; and Merlin's `help.rtf` runs **eight Level B mono** channels,
which needs 8/9.4 of the bandwidth and is why that file is only 8.9 % padding
where everything else on the disc is 42–64 %.

**If your decoded channel length does not match `sectors × 0.2133 s` (Level C)
or `sectors × 0.1067 s` (Level B), the coding byte is not what you think.**

*(Level C **stereo** is 0.1067 s per sector — the same 4,032 samples split
between two channels. It is easy to write the mono figure and be out by two on
a whole disc.)*

### On a CD-i Ready disc the music may ship twice — find the table

**[1 of 5]** The Apprentice carries its whole soundtrack in **both** formats:
22 Red Book CD-DA tracks (45.85 min, 73.9 % of the pressing) and 22 Level C
stereo ADPCM channels across three real-time files (46.0 min). One to one,
nothing exclusive to either.

The reason is architectural rather than indecisive: the symbol table has both
`cdda_play` and a 60-symbol software mixer with `mix_playcd` and a `mix_cdbusy`
flag. **The game uses the drive when the drive is free and its own ADPCM when it
is not.** A CD-i Ready disc has to have the CD-DA anyway, so the second copy is
nearly free.

**Look for the mapping table in the executable.** It is 22 records of six bytes
at a global the symbol file calls `music_table`:

```
u8   CD-DA track number
u8   channel inside the real-time file
u32  pointer to the file name
```

and the three distinct pointer values fall out without any relocation work,
because the filename globals' addresses differ by exactly `len(name) + 1`.

**Then check it, because the check is free and it closes.** CD-DA sectors ÷ 75
against ADPCM sectors × 0.1067:

```
21 of 22 agree to within 0.25 s
track 15:  CD-DA 66.61 s   ADPCM 93.44 s   +26.83 s
```

One outlier out of twenty-two, which is a real finding about that piece of
music rather than a mistake in the reading.

### The interleave may be timed to the drive, and that is a check too

**[1 of 5]** A Level C stereo channel gets one sector in eight at 0.1067 s
each, and the drive delivers 75 sectors a second — so a channel's **span in
sectors** is its duration in 75ths of a second, which is also its length as a
CD-DA track. On The Apprentice `music.rtf` channel 3 spans 21,449 sectors and
CD-DA track 7 is 21,449 sectors.

Walk the EOR markers, subtract, and compare against something you know the
duration of. If the numbers line up, the file was authored to play in real time
at exactly one disc revolution's worth of bandwidth per channel, and your
channel assignment is right.

### Check whether the music actually ships

Soccer has a 21-entry sound test naming eight tunes and no file on the disc
holds one. Enumerate the sound-test strings and map them onto the audio files; a
shortfall is a real finding. Link goes the other way: 83 minutes of music in 72
records, and nothing in the binary names any of it.

### Some questions only ears can close

Every pipeline so far has hit a point where the measurements ran out:
identifying the speaker in the head-region audio, mapping five narration
channels onto five named languages, working out what Merlin's eight help
channels select on and why one of its 89 animations is the only one in stereo. Long-term average spectra, envelope modulation and
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

On Merlin every real-time file is Form 2 throughout, so the fiction is a flat
13.5 % everywhere — `boltlib0.blt` is 3,446 x 2,324 = 8,008,504 bytes and the
directory says 3,446 x 2,048 = 7,057,408. The header inside the file gives its
own size as 8,007,558, which confirms the sector reading and leaves 946 bytes of
slack. **When a file carries its own size field, use it as the check.**

**[all]** Sectors tagged neither VIDEO nor AUDIO nor DATA are **bit-rate padding
and carry nothing** — 19.5 % of Soccer's one real-time file, 26.7 % of the whole
of Origami, 38.3 % of the whole of Merlin, **49.6 % of the whole of Link**. Drop
them; concatenate the rest at 2,324 bytes each.

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

**[2 of 4]** Link's `lmusic.rtr` is the other pattern, and the tell is
unmistakable: **channel *n*'s first sector is sector *n***, a perfect
round-robin from the first byte of the file. Eight stereo music channels running
simultaneously, of which the game listens to one.

Merlin's `help.rtf` is the same design **descending**, on eight channels again:

```
first sector of channel  8 ->  24      channel 12 -> 20
                         9 ->  23              13 -> 19
                        10 ->  22              14 -> 18
                        11 ->  21              15 -> 17
```

So do not test for `channel n starts at sector n` literally. **Test whether the
channels' first sectors form a contiguous run** — ascending or descending, and
starting wherever the file's own header ends. Two of four discs do this.

The point is that **switching costs no seek**: the sectors for the track you
want are already going past. It also shows in the padding figure — Merlin's
`help.rtf` is 8.9 % padding against 42–64 % for every other stream on the disc,
because all eight of its channels run at full rate the whole way through. **A
real-time file with anomalously low padding is a jukebox until proved
otherwise.**

Merlin names the design outright: `TC_Open_Jukebox`,
`TC_Play_Jukebox_Selection`, `TC_Is_Jukebox_Playing`, `TC_Stop_Jukebox_Play` and
a global called `jukebox`. On Link the identical mechanism had to be inferred
from sector arithmetic because nothing named it — which is the argument for
doing section 6a first.

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

**[2 of 5] Chunk 0 of an asset file is quite often 68000 code.** Link ships two
dozen compiled subroutines per playfield; The Apprentice opens the first chunk
of every level and screen container with a run of `60 00 xx xx` — `BRA.W`
instructions four bytes apart, at offset 4:

```
level1.dat  #0   00 00 00 00  60 00 28 76  60 00 28 4e  60 00 28 de ...
```

A jump table at the top of a loaded block is a **code overlay**: the loader puts
it somewhere and the engine calls entry *n* through slot *n*. It also explains a
suspiciously small executable — The Apprentice's game module is 23,236 bytes
because the per-level logic is not in it. Check for that `60 00` run in the
first chunk of anything before deciding an asset file is only assets.

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

### Validate a container by chaining it, and stop only when it lands exactly

A title may keep everything it owns inside one archive rather than in files.
Merlin's `boltlib0.blt` opens with the ASCII tag **`BOLT`** and holds 129 groups
of 7,703 members in 8 MB, and the whole of the game's art, palettes and tables
are inside it. The way to be sure you have read such a thing correctly is not to
render something and squint at it — it is arithmetic:

1. **Find the count from a header field, not by scanning.** A field reading
   2,096 with a record array starting at 32 gives `(2096 - 32) / 16 = 129`
   exactly. A field that divides cleanly is telling you the record size too.
2. **Chain every record.** Each member's offset should be the previous member's
   offset plus its length, allowing one byte of even alignment.
3. **Require the chain to land on the next group's offset, and the last one to
   land on the size in the header.** Merlin's does, on all 129, with **zero
   breaks**, ending on 8,007,558 — the value at header offset 12.

Getting this wrong is easy in a specific way: a group's member table lives
*inside* the group, at its start, so computing a group's end as
`offset + size` is short by exactly `count x 16` and the shortfall looks
convincingly like a gap between groups. **If your "gaps" are all exactly
`count x record_size`, they are not gaps.**

**[2 of 5] And when the chain does not land, suspect alignment before you
suspect the layout.** The Apprentice's 45 `.dat` files hold `u16 count` then one
`u32` size per chunk, and packing the chunks end-to-end from the end of that
table misses the file size by several kilobytes on every single one. The header
is padded to **2,048 bytes** and each chunk starts on the next 2,048-byte
boundary; with that, all 45 land **exactly**, from 106 KB to 776 KB, two to five
chunks each.

The tell was in the file itself: the first non-zero byte is at `0x800`, not just
after the size table. **Find the first non-zero byte after the header before you
choose an origin**, and try sector alignment before concluding the size fields
mean something other than sizes.

**A container of 45 files that all close is worth more than one that closes
once.** Repeat the check across every file of the same kind and report the count
— "45 of 45" is a different claim from "it worked on the one I looked at", and
`cdidat.py check` is what makes it a one-liner.

### Settle a compression flag by measurement, not by name

A member record's type byte is worth reading, but do not guess which value means
compressed. Compare each record's declared size against the distance to the next
record and let the distributions decide. On Merlin:

```
type  8 / 9 / 10   stored - declared is 0 or 1, never anything else
type  0 / 1 / 2    stored - declared is strictly negative, always
```

The `1` is a byte of even-alignment padding and appears nowhere else. So bit
`0x08` is "stored verbatim", the low bits are a sub-kind that survives both
ways, and the size field is always the size **after** expansion — which is what
a `DecompressSize` global is for. Two minutes of counting, and no assumption.

**Report the ratio even when it is unimpressive.** BOLT's compressor recovers
1.172 : 1 across 4.7 MB, which is 8 % of the library, on a disc using 39.5 % of a
CD. Link's RL7 managed 10.7 : 1 on comparable material. A weak compressor is a
finding about what the team was optimising for, and it is also a warning: do not
assume a container that has a `Decompress` entry point is doing anything you
need to reverse before you can read most of it.

---

## 10. Baselines, so you can tell signal from noise

Five very different discs, for comparison:

| | Ultra CD-i Soccer (1997) | Origami (1993) | Link: The Faces of Evil (1993) | Merlin's Apprentice (1995) | The Apprentice (1994) |
|---|---|---|---|---|---|
| Track | 7,875 sectors (1:45), 2.4 % of a CD | 326,400 sectors (72:32), 98 % | 255,924 sectors (56:52), 77 % | 131,610 sectors (29:14), 39.5 % | **no data track** — 69,150-sector pregap of track 1 (19.9 %), plus 22 CD-DA tracks (73.9 %) |
| Entries | 12 dirs, 144 files, 10,635,729 B | 5 dirs, 46 files, 631,506,598 B | **0 dirs, 14 files** | **0 dirs, 18 files** | 2 dirs, 91 files — 69 CD-i files of 113,851,360 B, plus 22 CD-DA entries |
| Pre-FS region | 2,269 sectors, 28.5 % of the image | 2,268 sectors, 0.7 % | 2,269 sectors, 0.9 % | 2,269 sectors, 1.7 % | 2,268 sectors, 0.8 % — **and an identical 2,250-sector copy after the last file** |
| Head audio | 7.41 s + 8.31 s mono, L = R exactly | **byte-identical to Link and Merlin** | **byte-identical to Origami and Merlin** | **byte-identical to both 1993 discs** | **a third recording**: 8.21 s + 14.74 s, L = R exactly, peak 16,383 |
| Tail padding | plain zeroes | plain zeroes, 15,770 sectors | plain zeroes + 4 sectors of `0x20` | plain zeroes, 2,258 sectors | the head block again, then 9,032 sectors of CD-DA silence |
| Executable | 229,376 B, 2 modules | 82,720 B, 4 modules | 135,168 B, 1 module (+ a 12 KB bumper) | 139,264 B, 1 module, edition 7 | 23,236 B game module; 9 modules over 6 files |
| Symbols | none | none | **325 C function names in the binary** | **887, in a `.stb` file in the root** | **521, in a `.stb` file in `/CMDS/`** |
| Streaming | one MPEG file | 79 % of the disc | 99.7 % of the bytes | 88.6 % of the disc | 69.5 % of the CD-i area, all of it music |
| Padding | 19.5 % of the one RTF | 26.7 % of the disc | **49.6 % of the disc** | 38.3 % of the disc | 23-68 % per RTF; **zero free sectors between the path table and the last file** |
| Compression | none, bar the run-length sprites | none, anywhere | RL7 for everything that moves, 10.7:1 | BOLT, on 54 % of the library, **1.17:1** | none, anywhere |
| Graphics | raw CLUT bitmaps + palettes on disc | DYUV and CLUT7, real-time only | CLUT7 playfields, RL7 cels, one DYUV still | inside the BOLT container; frame codec unidentified | CLUT7 and CLUT8 in `.dat` containers, pitches 384 **and 320** |
| Palettes | 192/384/768 B, entry 0 `#00FF00` | **none on the disc at all** | 384 B inline, entry 0 `#FFFFFF` | 390 B BOLT members, 6 + 128 × 3 | 384 B and 768 B, entry 0 `#000000` |
| Audio | 12 effects, 10.2 s, Level C in AIFF-C | 5 languages, 3 h 57 m, raw | **100.8 min**, Levels B and C, raw | 47 min, Level B mono, raw | 46 min ADPCM, **all Level C stereo**, plus the same 46 min as CD-DA |
| Video | MPEG-1 368 × 272 | 40 files, 7 streams each, no MPEG | no MPEG; RL7, CLUT7, DYUV | no MPEG; 89 animations in 7 files | no MPEG; **no video streams of its own at all** |
| All-zero files | 16, totalling 1,070,080 B | none | none | none | none |
| Dangling path references | 8 | none | none | none | none — 20 templates cover all 56 assets exactly |
| Duplicated files | none | none | **the same 30 MB pressed 3× (11.5 %)** | **the same 8 MB pressed 3× (7.9 %)** | none; the **filler block** is pressed twice |
| Languages shipped | English only | five — one of them not on the box | English only | English only | English only |

The five discs bracket the format. Soccer is what a *game* looks like on CD-i
when the program owns its assets: small files, a big executable, everything
loaded. Origami is what a *presentation* looks like: a tiny executable that owns
nothing and streams every pixel, including every letter of every menu. Link is
the hybrid and the most extreme case — a game whose executable owns almost
nothing, whose levels arrive as code and pixels together, and which spends half
its surface on keeping the drive fed.

**The Apprentice is the fifth shape, and the only one that is not a plain data
disc.** It owns everything, like Soccer and Merlin, in 45 containers of its own
format; but it is CD-i Ready, so three quarters of the pressing is Red Book
audio a CD-i player never touches, and the game carries a second ADPCM copy of
that same audio for when it needs the drive. Its executable is the smallest of
the five by a factor of six, because the per-level logic ships as 68000 overlays
inside the asset files.

**Merlin is the fourth shape: a game on somebody else's engine.** It owns
everything, like Soccer, but keeps it in one archive rather than 144 files, so
its root looks like a streaming disc's and its behaviour does not. It links no
Philips library at all. And it sits in the middle of the capacity range with no
dead files, which is the combination the first three discs would not have
predicted.

If your disc compresses something, has more than one directory level, ships
palettes for CLUT data, uses MPEG, carries symbols, keeps its assets in a
container with its own header, or hides its whole volume in a pregap, it is
doing something at least one of these did not. **Carry the method forward, not
the numbers.**

### Count the languages yourself

**Do not trust the language list in the dump's filename.** Origami is dumped as
`(En,Fr,De,Nl)` and carries **five** narration channels plus a five-entry
language menu plus five localised error screens — the fifth is Japanese. The
cheapest check is the audio channel census in section 9; the confirmation is
usually a menu screen, and on a disc with no text files it will be a picture, so
you will have to render it.

**And do not read a parallel-channel count as a language count either.**
Merlin's `help.rtf` carries eight audio channels and the disc ships in one
language; the eight are alternatives selected by content, not by locale. The
discriminator is whether the channels are the same length and start together
(languages, as on Origami) or differ in record count and interleave as a
round-robin (a jukebox, as on Link and Merlin).

---

## 11. Order of work that worked

1. **Read the cue sheet before the image.** One `MODE2_RAW` / `CDI/2352` track
   is the normal case. If every track says `AUDIO` and track 1 has a pregap of
   tens of thousands of sectors, the disc is **CD-i Ready**: the volume is in
   that pregap and your dump is scrambled. `cdiready.py probe` confirms it in
   four bytes and `cdiready.py extract` fixes it. Either way, note the capacity
   used — that number sets your expectations for everything else.
2. `cdihead.py map` — the pre-file-system region, before anything else. **Hash
   the region (2,324 bytes per sector, descrambled if your dump was data-mode)
   and compare it against the three known recordings before analysing
   anything** — `a0ed87f2e98b43f91281d16390fb178b` (1993–95),
   `4e61f608e1f1455d9ad5b2a0615dbbd3` (The Apprentice, 1994), and Soccer's
   1997 pair. Three of five discs match the first outright. **Hash the sectors
   after your last file too**: on one disc so far they are the same block again.
3. Volume descriptor and path table; note the application identifier, the
   publisher and the data preparer.
4. `cdifs.py list` / `map` / `extract`; note which entries have file number 1;
   all-zero census; date histogram against the volume date; hash files against
   each other. **Read the listing itself before opening anything** — look for a
   `.stb`, for sibling filenames differing only in a digit, and for whatever is
   timestamped one second before the executable.
5. **Symbols, both ways.** `cdistb.py` on any `.stb` in the root, and
   `cdisym.py list` on every executable. If the symbols are there, everything
   after this is easier — prove the address model against `M$Size` and `M$Exec`,
   then read them in address order.
6. `os9mod.py`: parity and CRC on every module; the bytes after each header —
   remembering that a `-F` there is `MOVE.L D6,-$7FF0(A6)`, not a linker option;
   grep for `Armendariz` and for the sixteen-byte `_cstart` prologue; look for
   the `_chcodes` table and for a row of equally spaced `60 00 xx xx` near the
   arithmetic helpers.
7. `cat` the copyright / abstract / bibliographic files — and anything else in
   the root that nothing references. Grep them for `@(#)`.
8. Filtered strings, with epilogue bytes stripped; then the two-way path
   cross-reference, per executable — and check any small root file that might be
   an index before calling anything unreferenced.
9. **Subheader census by channel** on every real-time file. This tells you
   whether the disc is a game or a presentation.
10. Record structure: `EOR`/`EOF` lists per channel, then padding runs, then
    triggers.
11. Audio: decode the coding byte, check the duration arithmetic, decode one
    channel per stream and *listen* — several questions only ears can close.
12. Graphics: palette sizes → pixel-value ceilings → autocorrelated pitch →
    **prove the total to the byte** → render everything.
13. Tagged chunks and containers, validated by chaining them until they land
    exactly on a header field or on the file size — try sector alignment before
    giving up on a layout — and check whether any of them is code. **Report how
    many files the check closed on, not that it closed.**
14. If the disc carries CD-DA, look for the table in the executable that maps
    tracks onto anything else, and cross-check the durations both ways.

Write down what does *not* resolve. Half of what makes a disc interesting is the
list of things that are measurably odd and not yet explained.
