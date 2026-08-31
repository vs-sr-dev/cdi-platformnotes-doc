# CD-i platform notes — a checklist for the next disc

A running checklist, carried from one CD-i documentation pipeline to the next
and added to by each. It now covers **eight discs, six years and two continents
apart**:

- **A Visit to Sesame Street — Letters** (American Interactive Media, USA,
  **September 1991**) — a place rather than a game, and the oldest disc here by
  eleven and a half months: 14 entries in a flat root, 86 % of a CD, 98.4 % of
  its bytes in eight real-time files, four painted 640-pixel rooms with a
  hot-spot table over them, **13,138 DYUV frames of alphabet cartoons at
  172 × 108**, and no text file of any kind.

- **Laser Lords** (Spinnaker Software / American Interactive Media, published by
  Philips Interactive Media of America, USA, **1992**) — a streaming adventure
  and the oldest disc here by a full year: 33 files in a flat root, 87 % of a
  CD, 94 % of its bytes in 22 real-time files, **ten of which are jukeboxes**
  carrying 6 h 34 m of speech on fourteen to sixteen parallel channels.

- **Burn:Cycle** (Trip Media / Philips Media, UK, **May 1995**) — an interactive
  film, and the shape none of the other seven has: **7 directory entries**, 94.5 %
  of a CD, **99.23 % of the volume space in one real-time file**, a
  sixty-nine-minute RL7 picture at 384 × 240 and 12.5 fps, 77:49 of ADPCM that
  the pressing tags as video, 353 compiled 68000 script objects interleaved with
  the film they belong to, and **0.68 % padding** — thirty-five times lower than
  anything else here.

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
they *do* share worth trusting. Those are marked **[all]** when all eight agree
and **[N of 8]** when fewer do.

*Every mark in this document now reads `of 7`.* Earlier revisions carried marks
reading `[N of 4]` and `[N of 5]` that the document itself declared had not
been rechecked, and two sessions of postponing them had turned them into
furniture. The sixth pipeline went through all of them: those the sixth disc
exercises were re-derived against it, and those it cannot exercise — CD-DA
mapping, chained containers, chunked asset files — were **re-declared in
place**, with a bracketed note saying so and why the numerator did not move.
An unexercised claim keeps its numerator and takes the new denominator; it does
not keep an old one. Findings from only one disc are named, and are the ones to
test rather than assume.

**The seventh pipeline did the same pass**, and three marks moved for a reason
worth naming rather than by simple increment: the head-padding mark (§2) gained
a *second* exception for a *different* reason, the publisher's-bumper mark (§5b)
went from 2 of 6 to 4 of 7 because the 1992 bumper turned out not to be
unique to the disc it was found on, and the Level A mark (§7a) went from 1 of 6
to 2 of 7 and took a correction with it.

**The eighth pipeline did it again, and four claims did not survive contact with
its disc.** They are flagged in place and they are worth knowing before reading
any of the sections they live in:

- **§2's argument that a correctly computed EDC proves the four bytes at the EDC
  position were never audio.** Burn:Cycle has 2,250 correct EDCs *and* loses
  seven frames a sector, control run. The conclusion about the 1991–95 group
  stands; the general argument does not.
- **§5b's "Merlin's Apprentice ships no bumper stream at all."** It ships the
  Philips bumper's audio track, byte-identical, inside `anims.rtf`. The 1993/94
  bumper is on three discs.
- **§9's "`TRIGGER` sits *near* structure, not on it."** On Burn:Cycle the
  trigger bit is one per frame, on the sector where the frame closes, 53,794
  times.
- **§9's "a disc whose aggregate padding is low is a disc full of jukeboxes."**
  Burn:Cycle is at 0.68 % and is not a jukebox. Padding is unclaimed bandwidth;
  a jukebox is one way to claim it and a film is another.

And one whole section gained a caveat rather than a correction. **§7a says the
coding byte must be corroborated for video and is authoritative for audio.**
That presumes you know which sectors are audio, and on the eighth disc the
*type bits* are wrong: 21,888 sectors carry Green Book ADPCM with the VIDEO bit
set and the AUDIO bit clear, so a census reports **zero audio sectors** on a disc
with seventy-seven minutes of sound. The test that finds it is §8's sound-group
structure test, run over every stream rather than only over the ones the type
bits call audio.

The tools referenced live in the pipeline repositories:

- [cdi-ultracdisoccer-doc](https://github.com/vs-sr-dev/cdi-ultracdisoccer-doc)
- [cdi-origami-doc](https://github.com/vs-sr-dev/cdi-origami-doc)
- [cdi-linkthefacesofevil-doc](https://github.com/vs-sr-dev/cdi-linkthefacesofevil-doc)
- [cdi-merlinsapprentice-doc](https://github.com/vs-sr-dev/cdi-merlinsapprentice-doc)
- [cdi-theapprentice-doc](https://github.com/vs-sr-dev/cdi-theapprentice-doc)
- [cdi-laserlords-doc](https://github.com/vs-sr-dev/cdi-laserlords-doc)
- [cdi-avisittosesamestreetletters-doc](https://github.com/vs-sr-dev/cdi-avisittosesamestreetletters-doc)
- [cdi-burncycle-doc](https://github.com/vs-sr-dev/cdi-burncycle-doc)

`cdilib.py`, `cdifs.py`, `cdihead.py`, `os9mod.py`, `cdistrings.py`,
`cdiaudio.py`, `cdidyuv.py`, `cdirta.py`, `cdipic.py`, `cdicensus.py`,
`cdisym.py`, `cdistb.py` and `cdiready.py` are platform-general and should work
unmodified on another disc. `cdigfx.py`, `cdispr.py`, `cditeams.py`, `cdipf.py`,
`cdianim.py`, `cdibolt.py`, `cditoc.py`, `cdirtf.py`, `cdidat.py`,
`cdimusic.py` and `cdinvr.py` carry title-specific tables and are worth reading
rather than running.

The seventh pipeline adds two more: `cdifilm.py` (a real-time video stream cut
into frames, with the slot proved from the sector run rather than assumed) and
`cdiclut.py` (a CLUT7 picture and the palette that sits in its own stream).
**Both are title-specific and both fail silently on another disc** — the eighth
pipeline classified them *inherited and misleading here* and wrote a replacement
rather than parameterising them.

The eighth pipeline adds `cdirl7.py` (the MCD212's own run-length coding, with
the width proved by exact-line-end saturation and the frame boundary proved by
the trigger bit) and **`cdistreams.py`**, which writes the `notes/streams.txt`
§12 has been asking for: one hash per `(file, channel, type, coding)` run.
**1,088 stream records over eight discs**, and its first run found a crossing
five sessions of file-level comparison had missed (§5b).
`cdi-burncycle-doc/notes/sha1-all.txt` makes **eight** published hash lists,
345 records, and eight `notes/streams.txt` beside them.

The sixth pipeline adds five that have no ancestor:
`cdiscan.py` (one pass over every sector into a fixed-width cache, so nothing
else has to make one), `cdiscramble.py` (the ECMA-130 stream, and the reason
section 2's hashes cannot be compared against image bytes), `lagcheck.py` (the
control that settles section 2's byte count), `cdixref.py` (the two-way
reference graph with a name filter that cannot invent a filename) and
`sixdiscs.py` (all eight discs at once: hash lists, head regions, sector-prefix
comparison). **`cdi-laserlords-doc/notes/sha1-all.txt` and its six siblings
are the branch's published hash lists** — see section 12.

**This document is the canonical copy.** Pipelines should link to it rather
than fork it.

---

## 1. Open the image first, and open it raw

```
chdman extractcd -i "TITLE.chd" -o _work/disc.cue -ob _work/disc.bin
```

**The eighth disc is the first to arrive as a CHD, so here is the case worked
rather than described.** `Burn-Cycle (Italy) (Il Gioco).chd`, 456,230,345 bytes:

```
chdman info -i "Burn-Cycle (Italy) (Il Gioco).chd"

  Logical size: 770,444,352 bytes      Unit Size:    2,448 bytes
  Total Units:  314,724                Ratio:        59.2%
  Compression:  cdlz (CD LZMA), cdzl (CD Deflate), cdfl (CD FLAC)
  Metadata:  TRACK:1 TYPE:MODE2_RAW SUBTYPE:NONE FRAMES:314724 PREGAP:0
             PGTYPE:MODE1 PGSUB:NONE POSTGAP:0

chdman extractcd -i "...chd" -o _work/disc.cue -ob _work/disc.bin
  28.4 s, 740,230,848 bytes = 314,724 x 2,352, remainder 0
  a 70-byte cue: FILE "disc.bin" BINARY / TRACK 01 MODE2/2352 / INDEX 01 00:00:00
```

**Read `SUBTYPE` before you get excited about the unit size.** 2,448 is 2,352
plus 96, and 96 is the size of a CD's subcode block — but **2,448 is chdman's
fixed unit size for a CD whether or not there is subcode in it**, and when there
is none the tail is zero-filled. `SUBTYPE:NONE` says so in the metadata you have
already printed. Confirm it if you like, and confirm it the cheap way:

```
chdman extractraw -i "...chd" -o raw.img      # 314,724 x 2,448
  sectors with a non-zero subcode area : 0
  distinct 16-byte patterns there      : 1 (zero)
```

**No CD-i disc in this collection has yet handed over a byte of subchannel data**,
and the mechanism that loses it is the ripper rather than the container. A
2,448-byte unit is not evidence; `SUBTYPE` is.

Two more things worth doing once, on any CHD:

- **`chdman info -v` prints the hunks by codec.** Zero `cdfl` (CD FLAC) hunks
  means no audio track, which is a free cross-check on the track list.
- **Verify the extraction is lossless before treating its output as the object.**
  Compare bytes 0..2351 of every raw unit against the corresponding sector of the
  extracted `.bin`. On the eighth disc: 314,724 units, 0 sectors differing.

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
sectors. Soccer uses 7,875 of them, Merlin 131,610, Link 255,924, **Letters 286,330**, Laser Lords
290,955 and Origami 326,400. That one number sets your expectations for everything that follows: a
disc at 2 % of capacity will have dead files lying around, and a disc at 98 %
will not. The middle of the range is its own signal — Merlin, at 39.5 %, has no
dead files at all but spends 7.9 % of the pressing on two redundant copies of
one library, which is what having room and no reason to economise looks like.

---

## 2. Check the pre-file-system region — this is the highest-yield first move

**[all]** **Run this before anything else.** It turned up 5.2 MB on every disc
so far — eight of eight — and on none of them can the program reach it.

**And hash it in the right domain.** Everything below that quotes an MD5 quotes
it for the *descrambled audio*, not for the bytes in the image. A data-mode rip
holds the audio XOR the ECMA-130 stream (see "Why it is scrambled" below), so
an image-domain hash compared against these values never matches and always
looks like a new recording. The sixth pipeline's briefing made exactly that
mistake and reported a fourth recording that is not there. **State which domain
your hash is in, every time.**

```
python tools/cdihead.py map
python tools/cdihead.py check
python tools/cdihead.py wav OUT/
```

The path table sits a long way in — **LBA 2,269 on Soccer, Link, Merlin and
Laser Lords, 2,268 on Origami and The Apprentice** — and everything before it
belongs to nobody. That is also the rule for choosing the window: the region is
2,250 sectors ending immediately before the path table, so the path table's LBA
decides the alignment and no hashing of both candidates is needed. Do not assume it is
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

**[6 of 8]** **Compare the head against the tail.** The tail padding is *plain*
zeroes while the head padding is *scrambled*, on six of the eight, and there
are now **two exceptions for two different reasons**: The Apprentice writes the
head block again instead of padding (below), and *A Visit to Sesame Street:
Letters* writes its lead-in as **plain** zero rather than scrambled zero. On
that disc sectors 0–15 and 18 have a correct twelve-byte sync, a correct MSF
header and a valid `00 00 20 00` subheader, and 2,324 bytes of ordinary zero.
**Which kind of zero a lead-in holds is a per-disc setting, so measure it
rather than inheriting the label** — `cdiscramble.py selftest` now reports both
tests and names the case instead of asserting one of them.
Two filler mechanisms in one image means two different tools touched it, and it
has now held on five unrelated pressings.

**The "third mechanism" is not one, and it is not on the disc at all.** Earlier
revisions of this document recorded four sectors on Link at 255,770–255,773,
2,324 bytes each, every byte `0x20`, as a third kind of padding, and told the
next pipeline to classify padding by content rather than by position. Laser
Lords has two such sectors at 290,950–290,951 and the sixth pipeline checked
both discs properly:

```
laserlords  LBA 290950  whole sector 0x20: True   subheader 20 20 20 20
laserlords  LBA 290951  whole sector 0x20: True   subheader 20 20 20 20
link        LBA 255770  whole sector 0x20: True   subheader 20 20 20 20
link        LBA 255771  whole sector 0x20: True   subheader 20 20 20 20
link        LBA 255772  whole sector 0x20: True   subheader 20 20 20 20
link        LBA 255773  whole sector 0x20: True   subheader 20 20 20 20
origami / apprentice / merlin / soccer   0 sectors
```

**All six sectors, on both discs, are `0x20` from byte zero — sync field
included.** A pressed Mode 2 sector always carries the twelve-byte sync
pattern; the mastering writes it and it is not optional. These do not have it,
so they were never read from a disc: they are what a ripper wrote into its
buffer when a read returned nothing. The finding is about two **transcriptions**
and not about two pressings, and it belongs beside the other rip damage rather
than beside the filler mechanisms.

The advice survives the correction and is worth more than the finding was:
**classify by content, and check the sync field before calling anything
padding.** A sector with no sync is not padding of any kind.

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
later**, makes it three, and *Laser Lords*, mastered **nine months earlier than
any of them**, makes it four:

```
Letters      1991-09  sectors 19-2268  5,229,000 B  md5 a0ed87f2e98b43f91281d16390fb178b
Laser Lords  1992-08  sectors 19-2268  5,229,000 B  md5 a0ed87f2e98b43f91281d16390fb178b
Link         1993-06  sectors 19-2268  5,229,000 B  md5 a0ed87f2e98b43f91281d16390fb178b
Origami      1993-05  sectors 18-2267  5,229,000 B  md5 a0ed87f2e98b43f91281d16390fb178b
Merlin       1995-01  sectors 19-2268  5,229,000 B  md5 a0ed87f2e98b43f91281d16390fb178b
```

All 5,229,000 bytes. Same MD5. **The recording is not title content — it is an
artefact of the CD-i authoring chain**, laid down by whatever tool wrote the
pre-file-system region — and the window it covers is now **1991-09-13 to
1995-01-31**: three years, four and a half months, four development houses, two
continents. It has been called the 1993–95 recording and then the 1992–95 one;
**call it the 1991–95 recording**, and expect the left edge to move again.

**And on Laser Lords the identity does not stop at the region.** Compared
against Link sector by sector over everything in front of the file system:

```
laserlords vs link, sectors 0..2269, comparing bytes 16..2347
      0..15       16 sectors  IDENTICAL      (scrambled zero)
     16..16        1 sectors  differ         (the volume descriptor)
     17..2269   2253 sectors  IDENTICAL      (terminator, region, path table)
```

**2,269 of the first 2,270 sectors are byte-identical and the one that differs
is the descriptor.** Two discs a year apart from two different studios agree on
5,291,308 bytes of everything that precedes their first file. Run
`sixdiscs.py prefix A B` on any new pair; it is one command and it turns a
region hash into a boundary.

*Ultra CD-i Soccer*, in 1997, carries a **different** recording in the same
place — two mono clips of 7.41 s and 8.31 s, bit-identical left and right,
fundamentals near 151 and 161 Hz. No 512-byte run of one appears in the other.

*The Apprentice*, in **October 1994 — inside the window the other five bracket** —
carries a **third**:

```
Apprentice  1994  sectors 18-2267   5,229,000 B  md5 4e61f608e1f1455d9ad5b2a0615dbbd3
```

So the filler is not one artefact of one tool. Its structure is common across
all eight discs and its content is not, and date does not predict which
recording you get: a 1994 disc has neither the 1991–95 one nor the 1997 one.

On the *structural* tests The Apprentice sits with Soccer rather than with the
group — two separate clips rather than one continuous take, and left
**bit-identical** to right (100.00 % of frames) rather than merely correlated
at 0.9988. And its peak is exactly **16,383** = 2^15/2 − 1, which no other
disc's filler shows: either a 14-bit source or a deliberate 6 dB attenuation.

**Do the comparison properly.** Both clips contain long runs of digital
silence, so a naive substring search reports matches that are silence matching
silence. Sample only *non-trivial* windows — reject any window with two or
fewer distinct byte values — and search both directions. Against Soccer's two
clips that gives **zero matches in all four directions**.

So: **hash the descrambled head region of every new disc and compare it against
all four known recordings** before spending any time analysing it. That is a
thirty-second check, it has now answered the question outright seven times, and
the window it covers is wider than the first two discs suggested. Four
recordings over eight discs:

```
a0ed87f2e98b43f91281d16390fb178b   5 discs: Letters 1991, Laser Lords 1992,
                                            Link 1993, Origami 1993,
                                            Merlin 1995
4e61f608e1f1455d9ad5b2a0615dbbd3   1 disc:  The Apprentice 1994
d1bc6b7dbed8abfd30df0ff4c7cada48   1 disc:  Ultra CD-i Soccer 1997
b80d0c314bd303bb9c21495fcdf41975   1 disc:  Burn:Cycle 1995-05
```

**Four recordings over eight discs, 5/1/1/1, and the two 1995 discs carry
different ones.** Merlin, January 1995, has the shared 1991–95 take; Burn:Cycle,
four months later, has its own. Date does not predict which recording a disc
gets, and now two discs four months apart say so explicitly rather than merely
consistently.

The fourth recording, measured (`cdihead.py tests 19 2269`):

```
29.64 s at 44,100 Hz stereo      1,307,250 frames   (2,324 B/sector)
mean |x|                               L 1358.3   R 1382.4
mean |x[k] - x[k-1]|                   L  206.4   R  211.2
peak                                   L 32,084   R 32,767
digital silence          1,009 sectors, ONE CONTIGUOUS RUN, LBA 636-1644
two takes                617 sectors (8.13 s) and 624 sectors (8.22 s)
L == R, all frames                     46.64 %
L == R, silent sectors excluded         3.26 %
corr(L, R)                             1.0000
```

**Quote the L==R figure with the silence excluded, or quote the silence beside
it.** 1,009 / 2,250 is 44.8 % and the naive L==R figure is 46.64 %: almost all of
the agreement is silence agreeing with silence, and the honest number is 3.26 %,
which puts this recording structurally *with* the 1991–95 group (a mono source
through a stereo converter) rather than with Soccer and The Apprentice (channels
bit-identical on 100 % of frames). §2 already warns about silence-matching-silence
for substring searches; it applies to this statistic too.

**And every filler recording in the collection is two takes.** 1991–95: one
silence of 8.6–8.9 s in 29.64 s. The Apprentice: 8.21 s and 14.74 s. Burn:Cycle:
8.13 s and 8.22 s with 13.29 s between them. Four recordings, four voices, one
shape — a slate or a two-line announcement — and nothing measured identifies any
of the speakers.

`python tools/sixdiscs.py head` prints that table for all eight at once
(`cdi-burncycle-doc`, extended from the seven-disc version in
`cdi-avisittosesamestreetletters-doc`). **Every one of those hashes is of the descrambled
audio.**

The 1991–95 recording, measured:

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

**[7 of 8]** The head sectors have correct sync, a correct MSF header, a valid
subheader — and the subheader is `00 00 20 00` on all 2,250 sectors of Origami,
Link, Merlin, The Apprentice, Laser Lords and Letters alike: Form 2 with none of the
data, audio or video bits set, a sector declaring itself to be of no type at
all. **What is in the four-byte EDC field varies, and it is the one place these
four otherwise identical regions differ** — see below.

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

**Six stereo frames — 24 bytes — are lost per sector.** That is
sync (12) + header (4) + subheader (8), and the four-byte EDC field is **not**
part of it. *(This block said seven frames and 28 bytes for three revisions.
The correction is in "Quote the lag index with its convention" below, and it is
settled by a control rather than by argument.)* So the object
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

**And Laser Lords, in 1992, computes all 2,250.** Four discs whose payload is
byte-identical, four different answers:

```
                sectors 19..2268     EDC valid   wrong   zeroed
  Letters       1991-09                     0        0     2250
  Laser Lords   1992-08                  2250        0        0
  Link          1993-06                     0        0     2250
  Merlin        1995-01                     1        0     2249
  Burn:Cycle    1995-05                  2250        0        0
```

Since the payload is the same on all four, the *correct* EDC of each sector is
the same number on all four; only whether it was written differs.

**The seventh pipeline was asked to settle whether this is a setting or a
drift, and the answer is: a setting with no covariate.** Read the four discs
down every column that could explain them and none does.

```
  disc          year     preparer field              publisher field                  EDC
  Letters       1991-09  Katie Moriarty and Greg B.  American Interactive Media       zeroed
  Laser Lords   1992-08  _ISG_CDI_TOOLS_1.6          Philips Interactive Media of A.  computed
  Link          1993-06  _ISG_CDI_TOOLS_1.6          Philips Intractive Media of A.   zeroed
  Merlin        1995-01  _ISG_CDI_TOOLS_1.6          PHILIPS INTERACTIVE MEDIA OF A.  2249 zeroed
  Burn:Cycle    1995-05  Graham Deane                Trip Media                       computed
```

**The fifth sample was the one that could have broken a covariate and does not.**
Burn:Cycle carries **neither** the tool string nor the Philips publisher field —
it is the second disc outside that mastering line entirely — and it behaves like
Laser Lords, which carries both. Five samples, no covariate, and the negative has
now been tested from outside the operation as well as inside it.

The year does not predict it — the **oldest** disc zeroes and the second-oldest
computes. The preparer string does not predict it — three discs carry
`_ISG_CDI_TOOLS_1.6` character for character and give two and a half different
answers. The publisher does not predict it — the same operation produced both.
And the one disc that carries neither that tool string nor that publisher
behaves like two of the three that do. **Four samples, no covariate**, and the
negative is the result. Record both settings on every new disc anyway; a fifth
sample is the only thing that improves this.

Check the whole run and the last sector of it separately. **And do not use the
EDC field as evidence about what the filler overwrote** — see the next block.

*(That last sentence was wrong, and the correction is below: it is six frames
and 24 bytes, and the EDC field was never audio. The 2,328-byte test the
paragraph above rests on cannot distinguish "overwritten" from "never audio",
because on Link the four bytes are `00 00 00 00` and inserting a zero frame
every 582 frames blows up any discontinuity measure either way.)*

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

Run this lag test on your disc. A boundary jump matching separation 7 is the
same mechanism, and it means **six** frames, not seven.

One difference worth recording: Soccer's and The Apprentice's head clips are
**bit-identical** left and right; the 1993 recording correlates at 0.9988 but is
not identical — a mono source through a stereo converter rather than a
duplicated channel.

### The filler may be written twice, head *and* tail

**[1 of 8]** Link, Origami, Merlin, Soccer, Laser Lords, Letters and Burn:Cycle all pad the
tail of the volume with plain zeroes. The Apprentice does not: it writes the identical 2,250-sector
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

### Quote the lag index with its convention — and the convention settles the count

If `D` frames are missing at a boundary, the two surviving frames either side of
it are `D + 1` apart in the original stream. So a boundary jump matching
`mean|x[i] - x[i-k]|` means **`D = k - 1` frames are gone, not `k`**.

```
Apprentice, left channel, 2,324 B/sector
  clip A  boundary 1275.2   matches separation 8 (1258.6)   ->  7 frames, 28 bytes
  clip B  boundary 1200.9   matches separation 8 (1225.5)   ->  7 frames, 28 bytes

Link / Merlin / Laser Lords, left channel, 2,324 B/sector
          boundary  455.6   matches separation 7  (452.7)   ->  6 frames, 24 bytes
```

**Two different discs, two different answers, and the earlier revisions of this
document applied the convention to one of them and not the other.** The Link
and Merlin blocks read their separation 7 as seven frames; under the rule stated
two paragraphs up, separation 7 is six.

**The sixth pipeline settled it with a control instead of an argument.** Audio
*inside* one sector is continuous — 581 stereo frames with nothing removed — so
a cut of known size can be made in it and the estimator asked what it reports:

```
python tools/lagcheck.py control            (cdi-laserlords-doc)

  D   boundary  by mean |dx|
  0      243.6  k=1  -> 0 frames OK
  1      445.2  k=2  -> 1 frames OK
  2      589.6  k=3  -> 2 frames OK
  3      660.9  k=4  -> 3 frames OK
  4      741.3  k=5  -> 4 frames OK
  5      810.3  k=6  -> 5 frames OK
  6      916.0  k=7  -> 6 frames OK
  7      938.4  k=8  -> 7 frames OK
  8      967.8  k=8  -> 7 frames XX   (the profile has flattened)
```

The estimator returns `k - 1` for every D from 0 to 7. So on the three discs
carrying the 1991–95 recording the loss is **six frames, 24 bytes** — sync,
header and subheader, and **not** the EDC field. On The Apprentice, whose
filler is a different recording written by a different tool, it is seven frames
and 28 bytes.

Two independent corroborations that the EDC field was never audio on the
1991–95 group: Laser Lords carries **2,250 correctly computed EDCs** in that
field (a checksum cannot also be a sample), and the "extending to 2,328 bytes
makes it worse" test proves nothing either way, because Link's four bytes there
are zero.

**State whether your lag number is the separation or the loss, and run the
control before you convert one into the other.**

### The EDC field was audio on one of the four recordings, and the argument above has to go

The paragraph two blocks up reads:

> Two independent corroborations that the EDC field was never audio on the
> 1991–95 group: Laser Lords carries **2,250 correctly computed EDCs** in that
> field (a checksum cannot also be a sample) …

**The conclusion about the 1991–95 group stands** — six frames, measured three
times on byte-identical data. **The general form of the argument does not**, and
the eighth disc is the counter-example. Burn:Cycle carries 2,250 correctly
computed EDCs *and* loses **seven frames, 28 bytes**:

```
python tools/cdihead.py lag 19 2269      (cdi-burncycle-doc)

  boundary jump                                   771.5  (2,249 boundaries)
  separation 7   709.8      separation 8   770.7   <== match
  => 7 frames = 28 bytes: sync (12) + header (4) + subheader (8) + EDC (4)

python tools/lagcheck.py control
  the estimator returns k - 1 for D = 0,1,3,4,5,6,7 and misses at D = 2 and 8;
  it is EXACT at D = 7, which is the value being read
```

So the tool wrote a valid Form 2 checksum over four bytes that had been audio. A
correctly computed EDC in that field proves nothing about what was there before
it.

Read down all six discs that can be measured, the two settings are simply
independent:

```
  disc          recording      EDC field        frames lost
  Letters       1991-95        zeroed           6
  Laser Lords   1991-95        computed         6
  Link          1991-95        zeroed           6
  Merlin        1991-95        2249 zeroed      6
  Apprentice    its own        zeroed           7
  Burn:Cycle    its own        computed         7
```

**The loss tracks the recording; the EDC setting tracks nothing.** Four discs
sharing one payload lose six frames whatever their EDC setting; the two discs
carrying their own payloads lose seven, likewise whatever their setting. Two
axes, and this document has been reading them as one.

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
  component at all — the boot file sits in the root. On *Letters* the field
  says `cdi_ctw`, the root holds a file of that name, and the OS-9 module
  inside it is called `cdi_ctw` — but the **volume** id is `CTW Letters` and
  the box says *A Visit to Sesame Street: Letters*, so on that disc the
  application identifier agrees with the module and with nothing else. Five of
  discs make this field trustworthy and two make it interesting; either
  way it is one read.
- **[7 of 8]** **`copyright`, `abstract` and `biblio`, if named, are plain text
  on the disc.** Always `cat` all three. *(Re-declared rather than rechecked:
  A Visit to Sesame Street: Letters names none of the three — all three fields
  are 32 spaces — so it cannot exercise the claim. The denominator moves and
  the numerator does not. **And the absence is itself the finding on that
  disc**: with no text file, no symbol table and no `printf`, the only two
  human names anywhere on it are in the data-preparer field, and the only
  place its own abbreviation is expanded is a picture. Section 5b.)* Soccer's held marketing copy and the
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
  name, and the same name is the sole programming credit. On The Apprentice it
  is four first names. On Link, Merlin **and Laser Lords** it is
  `_ISG_CDI_TOOLS_1.6`, at the same version number, on discs **two and a half
  years and three development houses apart**:

  ```
  letters     1991  Katie Moriarty and Greg Brooks  American Interactive Media
  laserlords  1992  _ISG_CDI_TOOLS_1.6        Philips Interactive Media of America
  link        1993  _ISG_CDI_TOOLS_1.6        Philips Intractive Media of America, Inc.
  merlin      1995  _ISG_CDI_TOOLS_1.6        PHILIPS INTERACTIVE MEDIA OF AMERICA, 1994
  origami     1993  Paul Brand                EagleVision b.v.
  apprentice  1994  Tim, Luke, Luc and Arjen  The Vision Factory
  burncycle   1995  Graham Deane              Trip Media
  soccer      1997  (blank)                   Philips Interactive Media
  ```

  **Burn:Cycle is the first disc where the preparer field is corroborated by the
  disc's own credit roll.** `Graham Deane` appears twice in its
  `bibliographic.txt` — `Graham Deane — Runtime and Production Software` and
  `Graham Deane — Technical Director`. Origami's preparer is a person's name and
  the sole programming credit, which is close, but Origami has no separate roll
  to check it against. **If a disc names all three text files, read the preparer
  field against the bibliography before assuming the field is a tool.**

  **[3 of 8]**, and the three are exactly the three whose publisher field says
  *Philips Interactive Media of America*. **The oldest disc of the eight does
  not carry the tool string** — its preparer field is two people's names — so
  the window in which that mastering line was in service still opens in 1992-08
  and does not move left. On a disc eleven and a half months older than the
  earliest instance, that negative is worth as much as a positive would have
  been. No other disc carries the string and
  no disc carrying it has a different publisher, so on this evidence it is that
  operation's mastering line rather than a tool version in general circulation.
  A dating tool with a window of 1992-08 to 1995-01 and a version number that
  never moves. And Link's
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

**[5 of 8] The file number byte in the system-use area is `1` on real-time
files and `0` on everything else.** *(Letters makes it four: 1 on all eight
`.rtf` and 0 on all six others, including the two `.map` files that describe
bumper streams and the executable itself, with no exception.)* On Laser Lords it is 1 on all twenty-two
`.rtf` entries and 0 on all eleven others, **including both publisher-supplied
bumpers**, with no exception anywhere. On Link that byte is the only mechanical
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
144 files; Origami has 5 and 46; The Apprentice 2 and 91; **Link has none and
14**; **Merlin has none and 18**; **Laser Lords has none and 33**; **Letters
has none and 14**; **Burn:Cycle has none and 7**. Five of eight have no
directory at all, and the smallest root in the collection belongs to the disc
that uses the most of a CD.

**And one of those entries may be the file system describing itself.** Soccer,
Link, Merlin, Laser Lords, Letters and Burn:Cycle all expose the **path table as
a file** —
ten bytes on the four flat-rooted ones, and byte-identical across them
(sha1 `6fa9eb5c50bcb6e9e6b82b51128ad52649a0e186`), because a
Green Book path table for a single-directory volume with its root at LBA 2,270
is the same ten bytes whoever made the disc. Expect it in the listing, expect
it to be named by nothing, and do not count it among the title's files.
**And do not count it as a cross-disc crossing either**: it is now on five of
the eight and it is a coincidence of format, not a shared component. It is the
single most misleading line in `notes/sha1-all.txt` and §12 says so. Directory count correlates with how much the program owns rather than
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
- **[2 of 8] Hash every file's payload *and* its subheaders.** Link presses the
  same 30 MB file three times — `ldata.rtr`, `ldata1.rtr`, `ldata2.rtr`, byte-
  identical down to the channel and submode bytes — at LBA 2,992, 115,786 and
  235,940. That is 11.5 % of the disc spent so a single-speed drive is never far
  from the game's working set. **Merlin does exactly the same thing** with
  `boltlib0.blt`, `boltlib1.blt` and `boltlib2.blt` — 8 MB each, one MD5, one
  timestamp, 7.9 % of the disc, at LBA 2,340, 122,447 and 125,893. Two discs of
  four now trade space for head movement this way, so **if two files hash the
  same, look at their LBAs before calling it a mistake.**

  **Two discs now do it below the file level, and one of them is the cheapest
  instance of the pattern yet.** *Letters* presses the same 640 x 244 CLUT7
  picture of Sesame Street into all four of its location files — `street.rtf`,
  `nest.rtf`, `apt.rtf` and `cave.rtf`, md5 `6f6de076a4ac9668a657af3cfdf7f0cd`
  four times, 474,096 redundant bytes and **0.07 % of the disc** — so that
  walking back to the street from any room costs no seek. Link spends 11.5 %
  and Merlin 7.9 % of their surfaces on the same trade at the file level; that
  disc spends a rounding error on it at the picture level.

  **Laser Lords does it below the file level too.** No two of its thirty-three
  files hash the same, but all seven of Laser Lords' `world.rtf` streams carry the identical
  56-sector Level C clip on channel 7 — `2335288f50bf7313c7f57bb6d687d194`, 130,144 bytes
  — so 780,864 bytes are pressed six extra times to put an arrival sound at the
  head of every world. **A duplicate need not be a whole file**, and a file-level
  hash list cannot see one that is not.

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

**[5 of 8] Not every module has `M$Init`/`M$Term`.** *(Letters makes it four:
both of its programs read `0x6364695f` at offset 72 — the `cdi_` of
`cdi_ctw` and `cdi_bumperanim` — so both have 72-byte headers.)* Laser Lords
made it three:
all five of its programs read `0x6364695f` at offset 72 — the ASCII `cdi_` of
their own names — so all five have 72-byte headers. On Link the header ends at
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

**And the eighth disc makes the same mistake available in a second register.**
Burn:Cycle's module name field reads

```
0x48: 63 64 69 5f 42 75 72 6e 43 79 63 6c 65 00 4e 56 00 00 ...
      c  d  i  _  B  u  r  n  C  y  c  l  e  \0 N  V  \0 \0
```

— `cdi_BurnCycle` NUL then bytes rendering as `NV`, which is **`4E 56 00 00`,
`LINK A6,#$0000`**: the first instruction of `main`, which that disc's symbol
table puts at `0x56`, immediately after the name's NUL at `0x55`. Not `_cstart`,
because that linker put the title's objects before the C runtime's.

**So the general rule, and it answers three questions from one hexdump:** the
bytes after a module name are the first instruction of whatever the linker put
first; which instruction that is tells you whether the build uses **A5 or A6**
for its frame pointer; and that in turn tells you what its function epilogues
look like in ASCII, which §6b needs. `4E5D 4E75` (A5) renders `N]Nu`;
**`4E5E 4E75` (A6) renders `N^Nu`**, and a strip list that has only the first
will silently eat a filename. It did.

**Which makes it a better fingerprint than the thing it was mistaken for:**

```
2d 46 80 10 2d 46 80 14 3d 43 80 18 08 2b 00 05
```

Four modules on one disc and one on another, two studios and two years apart,
open with that run.

**But it identifies one particular Microware C startup, not Microware C.**
Burn:Cycle links Microware C — `printf`, `sprintf`, `strcpy`, `memchr`, `atoi`,
`clock`, `_iobinit` are all in its symbol table, and the 129-byte `_chcodes`
table is in its binary — and **the sixteen bytes are absent**. Its `M$Exec`
points at a symbol named `_cstart` whose first instruction is `48 e7 ff f8`,
`MOVEM.L D0-D7/A0-A4,-(SP)`. So a disc can link this runtime and not have this
prologue, and the grep's negative means *a different startup*, not *a different
library*.

**And on a disc with a symbol file, grep for the `_chcodes` table rather than
the name.** Burn:Cycle's binary contains the table (`01 01 01 11 11 01 11 11`)
and the string `_chcodes` nowhere at all, because its names live in the `.stb`.

Grep for the prologue, and treat any module that does *not* have it as
worth a second look — The Apprentice's `cdi_app`, the one module whose symbol
file shipped, is the only one on its disc that starts differently
(`20 7c ff ff 9d d2`, `MOVEA.L #$FFFF9DD2,A0`).

Expect several modules concatenated with no padding — Origami's executable is
four (`Prgrm`, `Sbrtn`, `Prgrm`, `Trap`) whose sizes sum to the file size
exactly, and Link's second executable is two (`Prgrm` plus a 128-byte `Data`
module). A tiny module of type `$B` (Trap) next to the main program is normal:
it is the shared-library / trap-handler mechanism.

**Read the edition byte at the right offset.** The table above, and the classic
documentation, put it at byte **22**. On all five modules of *A Visit to Sesame
Street: Letters* byte 22 is zero and byte 23 carries the value, so the field is
a **16-bit big-endian word at offset 22** and a single-byte read at 22 reports
every module on that disc as edition 0 — which would have reversed the
paragraph below. `os9mod.py` reads the word; the table is the thing that was
wrong.

**And do not read the edition byte as a date.** Link's game module is
edition 1 and its bumper is edition 7. Merlin's single module is edition 7, so
the same number can belong to the game itself. **Laser Lords settles what it is
not:** four of its five programs are edition 7, and their directory dates span
**1991-08-19 to 1992-09-09** — thirteen months inside one build — while the
fifth program and all four Data modules are edition 1. The same value 7 appears
on three unrelated discs across four years. A field that takes one value across
thirteen months inside a build and repeats across four years between builds is
a **revision count**, not a build date and not a role. *Letters* says it again
from a third angle: its three modules live inside one file with one directory
timestamp and are editions **7, 1 and 0**, and its `cdi_bumperanim` is edition
7 nineteen days away from its `cdi_ctw`, which is also edition 7 — and so is
Laser Lords' `cdi_bumperanim`, built a week earlier and 850 bytes larger.

**[3 of 8]** **Look at the bytes immediately after the header, before any
code.** *(Letters is a fifth negative: between each of its five headers and
`_cstart` there is the module name and nothing else. Laser Lords is a fourth
negative: between each header and `_cstart`
there is the module name and nothing else — no font, no author list. Which is
still worth the look, because it is one of the five measurements that
separates its two supplied modules from its four own ones.)*
On Soccer they were a 29-glyph 1bpp 8×8 font — the alphabet the loader error
screens need before any file has been read. On Origami, inside the `cdi_bpsys`
module, they were nine names:

```
M.Armendariz,L.Barnes,W.Hunt,J.Kesselman,S.McClellan,R.Moore,T.Nutt,J.Piesing,J.Rotter
```

That is the author list of the **CD-i base program system** — the Philips /
OptImage support library that titles link against — not of the game.
J. Piesing is Jon Piesing, of Philips' CD-i and interactive-TV standards work.

**Grep every CD-i executable you meet for `Armendariz`.** The expectation when
this was written was that it would appear in every disc linking the same
library revision. Six discs later it has appeared on **exactly one** — Origami,
1993 — so mark it **[1 of 8]** and treat the grep as a test whose interesting
outcome is the negative. **The seventh disc is the strongest negative of the
six**, because it is the oldest and an American Interactive Media title, which
is where the Philips base program system would have been least surprising. Two
better fingerprints, measured across all seven:

```
disc        Can not  Can't  Stack Overflow  Armendariz  cdi_bpsys  _cstart  _chcodes  @(#)
letters        -       4          4              -           -        4        4       -
laserlords     1       4          5              -           -        4        4       -
link           1       1          2              -           -        1        2       -
origami        -       1          1              1           1        1        1       -
apprentice     -       3          3              -           -        3        4       -
merlin         -       1          1              -           -        1        1       3
soccer         -       -          -              -           -        -        -       -
```

**`_cstart` and `_chcodes` are 6 of 7** — every disc with a Microware C build —
and `**** Stack Overflow ****` tracks them exactly. `@(#)` is **1 of 7**. The
sixteen-byte `_cstart` prologue's earliest instance moves from 1992-08-13 to
**1991-08-26**, and one of *Letters*' four copies of each of those four
patterns is in 41 sectors that belong to no file: the previous night's build of
its own executable, still on the pressing (`cdi-avisittosesamestreetletters-doc`
chapter 08). **Count the fingerprints over the image and not only over the
files** — the surplus copy is how that leftover was found.

**A negative result is informative.** Merlin has nothing between its header and
`_cstart` but the module name — no font, no author list —
and no `Armendariz` and no `cdi_bpsys` anywhere in the binary. It does not link
the Philips base program system at all; it links a third-party runtime (BOLT)
whose graphics, audio, input and disc code is all first-party. So the absence of
that name is not a failed grep, it is a finding: **this title used somebody
else's engine.** Check what fills the gap before concluding the grep was
wasted.

**[5 of 8]** Laser Lords has neither, on any of its seven module files, and what
fills its gap is a third answer again: **direct OS-9 and nothing else** — all
five of its programs name the `math` trap handler and no library beyond it.
*Letters* gives the same answer a year earlier: both its binaries name `math`
and this title's own 212-byte `cdi_cv_timer` trap module, and nothing else. So
two discs now answer the follow-up question the same way, which is the first
time that has happened, and they are the two oldest. The
follow-up question is still the one that pays. And The Apprentice has no
`Armendariz` and no `cdi_bpsys` either, and
what fills its gap is neither a Philips library nor a third-party one: a
9,410-byte `Sbrtn` module the studio wrote (`cdi_start`) plus direct OS-9 calls.
So the absence has now meant two different things on two discs, and the follow-up
question — *what is there instead* — is the one that pays.

**Burn:Cycle gives a fifth answer and it is the most complete: the studio wrote
the runtime and linked it into the same module as the game.** No `Armendariz`,
no `cdi_bpsys`, no third-party engine, no separate `Sbrtn` — and 633 symbols
naming a stream manager (`smgr_*`, 43 symbols), a video manager that writes the
MCD212's control tables by name (`vmgr_*`, 31), a hot-spot manager (`hmgr_*`,
21), an object system, and 164 `C_*` entry points behind a function called
`dispatch`. That is not a library the title links; it *is* the title, and it is
why its 92,880-byte module runs a two-hour game with none of the content in it.

**Five discs, five answers.** The follow-up question has now been worth asking
five times out of five, and the shape of the answer has never repeated.

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
`**** Can't install trap handler ****`. Merlin, in 1995, has the contracted
form, and also carries `**** Stack Overflow ****` and
`Unexpected signal number $%X - Application Terminating`.

**And there is a third wording, in a third case.** Burn:Cycle has
`**** stack overflow ****` — **lowercase**, twice — and no trap-handler message
of either wording at all. Its two `Can't` strings are the title's own
diagnostics (`Can't gotoview, async movie or audio in progress`), not the
runtime's. So the row for that disc is *lowercase, no trap handler string*, and
a grep for `Stack Overflow` returns zero on a disc that has it.
**Grep case-insensitively.**

**Earlier revisions read the two wordings as two vintages of one library. Twelve
modules across five discs do not support that:**

```
disc        file             date        wording
laserlords  cdi_bumperanim   1991-08-19  Can't        <- the oldest module of all
laserlords  cdi_launcher     1992-08-13  Can't
laserlords  cdi_space        1992-08-31  Can't
laserlords  cdi_tribes       1992-09-09  Can't
laserlords  cdi_nvrui        1991-12-09  Can not
link        cdi_bumper       1993-02-17  Can't
link        cdi_link         1993-06-24  Can not
origami     cdi_origami      1993-05-28  Can't
apprentice  cdi_appl01/02/04 1994        Can't
merlin      cdi_merlin       1995-01-31  Can't
```

The `Can't` set spans 1991-08 to 1995-01 and **brackets both `Can not` modules
on either side**, so the wording is not a clock. What the two long-form modules
have in common is that each is the odd one out on its own disc — Link's is the
game beside a bumper, Laser Lords' is a supplied NVRAM component beside four
title modules. Two cases is not a rule; read the wording as *which build a
module came out of*, not when.

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

**[5 of 8]** Discs published under the Philips banner may carry a bumper stream
that the publisher supplied as a finished file. It is not the studio's work, it
is **pressed verbatim**, and it is therefore another free identity check of
exactly the kind the head-region MD5 is.

**There are two generations of two discs each**, and the second pair was found
by the seventh pipeline running `cdirtf.py hash` on both discs and reading the
two outputs side by side. The 1993/94 pair is below; the 1991/92 pair is the
block after it.

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

*(Two conventions are mixed in that table and the eighth pipeline separated them.
The three payload figures are computed at **2,304** bytes a sector. That is right
for the audio row — §8's sound group is 2,304 bytes of ADPCM in a 2,324-byte
sector, with 20 bytes of nothing after it — and wrong for the two video rows,
because §7c says on Form 2 all 2,324 bytes are picture. At 2,324 the video rows
are 281,204 and 92,960, so the identical total is **720,440 bytes plus the
descriptor**, not 714,240. The conclusion is unaffected; the arithmetic should
say which convention each row uses.)*

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

**Merlin's Apprentice ships part of one, and five sessions of file-level
comparison missed it.** `cdi-merlinsapprentice-doc`'s `anims.rtf` carries, on
channel 15 with coding `0x01`, 149 sectors whose payload hashes
`0ed5f089b563d13c0f99afd7bfddbfa80b1d87b1` — **346,276 bytes byte-identical to
the Philips bumper's Level B stereo audio track on Link and The Apprentice**.

```
0ed5f089b563d13c0f99afd7bfddbfa80b1d87b1   346,276 B   ch15 audio 0x01
    link              /bumper.rtf
    apprentice        /CMDS/philips.rtf
    merlin            /anims.rtf          <== found by cdistreams.py, 2025
```

So **the 1993/94 bumper is on three discs, not two**, and its audio is inside a
fifteen-animation container on the third. Earlier revisions of this block said
Merlin ships no bumper at all; that was a file-level statement about two files
that do not resemble each other, and §12's stream-level list found it in one
command. **This is the single best argument for `notes/streams.txt` that exists.**

Whether Merlin's video and descriptor are there too is not settled: only the
audio stream hashes the same, and `anims.rtf`'s other channels do not match the
bumper's. The likely reading is that the studio was given the bumper's
soundtrack and re-authored the picture; that is a hypothesis and it is open.

### The 1991-92 bumper is a different object, it is pressed twice per disc, and it is on two discs

*Laser Lords* (Philips Interactive Media of America, 1992) carries **two**
bumpers, `/ntsc_bumper.rtf` and `/pal_bumper.rtf`, and neither is the one Link
and The Apprentice share. **And *A Visit to Sesame Street: Letters* (American
Interactive Media, September 1991) carries the same two, byte for byte:**

| stream | ch | coding | sectors | bytes | md5 |
|---|---:|---|---:|---:|---|
| NTSC descriptor | 15 | data | 1 | 2,048 | `314263f87cbd5b8701ddeadec6a9ff1d` |
| NTSC video | 15 | CLUT4 | 213 | 495,012 | `c509b0972e089186151f671668b5bdd0` |
| NTSC video | 16 | CLUT4 | 85 | 197,540 | `00512db8efb46fb7a38a0d70cf64a082` |
| NTSC audio | 15 | Level A mono | 192 | 446,208 | `a6e41a0f87987c3f89f76c0c306b06e4` |
| PAL descriptor | 15 | data | 1 | 2,048 | `8104f9d4fc7d1a425c87ca11aaec3f2f` |
| PAL video | 15 | CLUT4 | 186 | 432,264 | `1bf862696a45851062818e9f83063c36` |
| PAL video | 16 | CLUT4 | 97 | 225,428 | `ed2e4f02f289fc82a448f5f4cb8a1c41` |
| PAL audio | 15 | Level A mono | 192 | 446,208 | `a6e41a0f87987c3f89f76c0c306b06e4` |

**2,246,756 bytes identical across eleven months and eighteen days and two
development houses**, and the audio md5 is the same in the NTSC and PAL files
on both discs, so those 446,208 bytes are pressed four times across the pair.
**Not one of those eight streams appears in either disc's `sha1-all.txt`**,
because the two *files* differ: the 1991 disc adds a channel the 1992 one does
not have.

That addition is the interesting part. `Letters` carries, on **channel 17** and
in both bumper files identically, a 384-byte CLUT7 palette in a data sector and
40 sectors of CLUT7 video which render at **384 × 240** as a still reading
`CHILDREN'S TELEVISION WORKSHOP`. So the shape is: *the publisher's animation,
verbatim, plus a licensor's card on a channel of its own.* Expect that shape on
any co-branded title of the period, and expect the card to be the only place a
disc with no text files says who licensed it.

And the file lengths still balance the way section 5b says they must:

```
              ch15 vid  ch16 vid  ch17 vid  audio  data  padding   sum
   Letters NTSC   213       85        40      192     2     269     801
   Letters PAL    186       97        40      192     2     284     801
   Laser Lords NTSC 213      85         -      192     1     374     865
   Laser Lords PAL  186      97         -      192     1     389     865
```

The 1991 disc carries 40 more content sectors in 64 fewer, and the padding
absorbs all of it.

The comparison against the 1993/94 pair:

| | Link 1993 / Apprentice 1994 | Letters 1991 / Laser Lords 1992 |
|---|---|---|
| video, channel 15 | RL7, 121 sectors | **CLUT4** coding, 213 / 186 sectors |
| video, channel 16 | DYUV picture, 40 sectors | **CLUT4** coding, 85 / 97 sectors |
| video, channel 17 | — | CLUT7 licensor card, 40 sectors, **Letters only** |
| audio, channel 15 | Level B stereo, 149 sectors | **Level A mono**, 192 sectors, 10.24 s |
| descriptor sector | `0xBABEFACE` + `cluts count filenum frame_sizes` | a count, a palette, a frame table — **no magic, no tag names** |
| file length | 625 sectors | 865 (Laser Lords) / 801 (Letters) |
| versions pressed | one | **two**, NTSC and PAL |
| shared bytes | 714,240, and only by accident (§12) | **2,246,756**, invisible to every hash list |

So the publisher's bumper is a shared asset **within a generation and not
across the collection, and each generation is on two discs**: between 1992 and
1993 the logo animation, its codec, its audio level, its container format and
its length all changed. Grep for `babeface` on a new disc; if it is absent,
**hash the streams before concluding anything**, because the absence marks the
generation and not a dead end. The 1991/92 descriptor sector is the same 2,048
bytes on both discs of its pair, and it has no magic — while the 1991 disc's
*own* container, for its own files, does have one (`0x00001190`, next block).

### The magic-less descriptor is the publisher's, not the platform's

An earlier revision of this document asked whether the header-less 1992 bumper
descriptor is the ancestor of Link's `0xBABEFACE`. **It is not, and the seventh
disc answers it from underneath.** That descriptor is byte-identical on the
1991 and 1992 discs, so it is one object and not a lineage; and the 1991 disc's
own five real-time containers open with

```
0   u32   0x00001190       magic
4   u32   size             total bytes used, header included
8   u32   count            number of 16-byte records
12  count x { u16 kind, u16 flags, u32 sector, u32 value, u32 aux }
```

— a magic, a size and a count, two years before the one with tag names. So a
studio's container had a magic in September 1991 and the publisher's asset
descriptor did not. **Grep any new CD-i disc for `00 00 11 90` at a
sector-payload boundary**; it costs one pass, and on that disc it hits twelve
times: once in each of two 2,048-byte `.map` files, and **twice in each of five
real-time files — at the first sector and again about ten sectors before the
last**. A stream that can only be read forwards writes its own index at both
ends so that a program which has reached the end has it again without a seek.

And the `sector` field of those records is a **seek table**: monotone, always
less than the file's own sector count, and **186 of its 195 entries across five
files land within one sector of a record boundary walked from the `EOR` bits**.
The one file where it does not is the jukebox, which has nothing to seek to
because its alternatives are selected by channel.

**Two bumpers of identical length is a mechanism worth knowing.** Both Laser
Lords files are 865 Form 2 sectors and the padding absorbs the difference:

```
              video ch15   video ch16   video total   padding   audio   data   sum
   NTSC          213           85           298         374      192      1    865
   PAL           186           97           283         389      192      1    865
```

298 + 374 = 283 + 389 = 672. A real-time file is read at exactly 1x, so two
alternatives that occupy one slot must be the same length in sectors and the
only free variable is how much of it carries nothing. **And their audio streams
are byte-identical** — 192 sectors, 446,208 bytes, one md5 — because a video
standard changes the picture and not the soundtrack. That is a crossing no
file-level hash list can see, because the two files differ.

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

#### A `.stb` may have a header, and if it does, run its checks first

**Merlin's layout is not the only one.** Burn:Cycle's `/cdi_BurnCycle.stb` is
14,336 bytes and holds **three** OS-9 Data modules, one per program module,
concatenated with no padding — and each one carries a **fourteen-byte header**
after its (NUL-padded to even) module name:

```
u16  0x0100        layout version
u32  CRC-24 of the program module this table describes
u32  offset of the record array, module-relative
u32  number of records

records, ten bytes each:
    u32  value      the symbol's address, module-relative
    u16  flags      0x0004 on code symbols
    u32  nameoff    offset of a NUL-terminated name, module-relative

names, packed, from the end of the record array
```

Merlin's record is also ten bytes and its fields are in a **different order**
(`u16 hi, u16 value, u16 flags, u32 nameoff`), with `0xFFFF` in the high word for
A6-relative globals — which is what Merlin's parser scans for, and which does not
exist here. A parser written for one raises an exception on the other, and that
is the good case.

**Two checks, in this order, before trusting a name:**

1. **`recoff + 10 × count` must land on the first name offset.** On Burn:Cycle it
   does on all three tables, exactly. That is a stated field closing on an
   observed one, which is stronger than Merlin's monotone-name-offset test.
2. **The CRC-24 field must match the module's own stored CRC-24** — the same
   24-bit quantity `os9mod.py` reads from a module's last three bytes. On
   Burn:Cycle two of three match exactly; the third, a six-symbol table for a
   548-byte trap handler, declares `0xbe0c60` for a module carrying `0xd0b656`.

**Check two is the instrument this document has been missing.** §4 says to sort
the listing by timestamp and look at what sits immediately before the executable,
because on Merlin the `.stb` was one second older than the binary. A checksum in
the header does that job properly: it **proves** a table belongs to the binary
beside it, or proves it does not. On Burn:Cycle the timestamps are equal and
would have said nothing; the CRC field says two tables are current and one is
stale.

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

**And a `.stb` may hold several tables, so report the count per table.**
Burn:Cycle's 633 are 530 + 97 + 6, one per program module, and the 97 belong to a
*second process* the first one forks — `preseek`, `play`, `stop_fmv`, `ss_play`,
`ss_pos`, `fmv_arrived`, `fma_arrived`. A flat "633 symbols" hides an
architecture. And the title-versus-library ratio runs the other way from
Merlin's: **81 % of Burn:Cycle's names are the title's own**, because that build
links very little it did not write.

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

**Three ways a name filter eats a real filename, and all three are silent.**
§6b has documented two: the epilogue bytes in front (below), and a name too short
for a three-letter minimum (`tv.rtf` on *Letters*). The eighth disc adds a third
and it is the worst of them.

**Capitals inside the name.** `cdixref.py` walked leftwards from a string's
terminating NUL through a character class of `[a-z0-9_.]` and validated
extensions with `[a-z0-9_]+\.[a-z]{2,4}`. Burn:Cycle's one content file is
**`BurnCycle.rtr`**, so the walk stopped at the `C` and produced `ycle.rtr`,
which matches nothing on the disc — and the tool reported **the file holding
98.5 % of the volume as named by nothing**.

It is worse than the other two because the wreckage is not a near-miss. A
filter that returns `uluxor.rtf` looks wrong; one that returns `ycle.rtr` looks
like noise and gets dropped. **Allow capitals in the stem and require lowercase
only in the extension**, and check any conclusion of the form *"nothing names
this file"* against the file's own name by hand before writing it down.

**Strip the epilogue bytes off the front of every hit.** A string literal placed
immediately after a function picks up that function's last instructions, and
`4E5D 4E75` — `UNLK A5; RTS` — renders as `N]Nu`. On Merlin that prefix hid the
*first* name of every cluster: `N]NuPondDD` fails a name filter that `FlasksDD`
passes, so the filter silently drops one entry in every group of related
strings. Strip a leading `N]Nu`, `NuNu`, `N]` or `Nu` and advance the offset to
match — **and `N^Nu` and `N^`, because a build that uses A6 rather than A5 for
its frame pointer emits `4E5E 4E75` and renders `N^Nu`** (§5). Burn:Cycle is
such a build and the missing entry cost it the same filename the capitals cost
it. Also split concatenated names: the linker packs `PondDD`, `FlasksDD` and
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

**[2 of 8] Names may live in a data file rather than in the binary.** *(Letters
does the opposite too, and adds a warning: every one of its thirteen files is
named by one of its two binaries, seven of them in one contiguous fifty-six-byte
literal table in disc order — but `cdistrings.py`'s name filter needs three
consecutive letters and therefore **drops `tv.rtf`**, which is the largest file
on that disc, from the middle of that table. Section 6b already warns that a
filter eats the first name of a cluster; it eats short names too. Check any
two-letter stem by hand. Laser Lords does the opposite and it is worth one
line: all fourteen of its world
stream names sit as literals in one packed table in `cdi_tribes`, NUL-separated
and in world order, with no `%` conversion and no digit stencil anywhere. A
systematic sibling is a reason to check, not a reason to conclude.)* Merlin's
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

**And corroborate the type bits too, because they can be wrong.** Every
revision of this section until the eighth disc assumed the type bits were the
one thing you could trust. On Burn:Cycle they are not:

```
python tools/cdicensus.py       (cdi-burncycle-doc)

  audio-typed sectors : 0
```

Zero, on a disc carrying **77:49 of Green Book ADPCM**. 21,888 sectors — channel
17 with 21,784, plus 94 + 6 + 4 on channels 10 and 4 — have submode **`0x62`:
VIDEO + FORM2 + REALTIME**, with the audio bit clear, and their payload is
ADPCM. So the census never puts them in an audio row, the coding byte is never
consulted for audio at all, and the disc reads as silent.

**The test that finds it is §8's sound-group structure test, run over every
stream.** One ADPCM sector is eighteen 128-byte groups, and each group's sixteen
parameter bytes are eight values stored twice — `bytes 0-3 == 4-7` and
`8-11 == 12-15`, which is 64 bits of redundancy per group and cannot fire by
chance. Over Burn:Cycle's thirteen streams:

```
(ch, type, coding)     groups passing, mean of 18     sectors passing 18/18
( 1, video, 0x01)              0.12                     0 of 200
( 2, video, 0x05)              5.04                     1 of 200
( 3, video, 0x01)              5.81                     2 of 200
(16, data,  0x00)              6.96                     0 of 200
( 4, video, 0x05)             18.00                     4 of 4      <==
(10, video, 0x05)             18.00                    94 of 94     <==
(17, video, 0x05)             18.00                   200 of 200    <==
```

It costs one pass, it discriminates 21,888 sectors from 288,014, and on that
disc it is the only thing that found the soundtrack. **Guard it against all-zero
sectors**, which pass trivially. Then corroborate: decode as audio and check
§2's smoothness ratio (0.243 there, against ~1 for noise), and decode as the
video the coding byte claims and confirm it is garbage.

**And decode the coding byte *against the type bits*, never alone.** The coding byte is
meaningless without knowing whether the sector is audio, video, data or
padding, and on a disc with enough streams the same value means several things
at once. On *Letters*, coding `0x00` alone is 172,710 sectors — **60.3 % of the
pressing** — and it is read four ways: Level B speech, container headers,
bit-rate padding, and the publisher's CLUT4 logo. Laser Lords:

```
  type   code  sectors   means
  audio  0x04    98837   Level C mono   18900 4b
  pad    0x00    70211   no type bits -- bit-rate padding
  video  0x08    64629   undefined in the table below
  audio  0x01    28674   Level B stereo 37800 4b
  data   0x00    14136   data
  audio  0x00     9701   Level B mono   37800 4b
  video  0x05     3800   DYUV normal
  video  0x00      581   CLUT4 normal
  audio  0x10      384   Level A mono   37800 8b
  pad    0x20        2   two sectors that were never read at all
```

Coding `0x00` alone is 94,629 sectors — 32.5 % of that pressing — and it is
read **four different ways**. The key is
`(file, channel, submode & 0x0E, coding)`, and a one-dimensional histogram of
the coding byte is not a census.

**It is authoritative for audio on all four discs. For video it is only
authoritative when the MCD212 decodes the stream itself.** Merlin tags all
3,446 sectors of a `.blt` file with video coding `0x02`, and the content is not
CLUT8 or any other picture format — it is an asset archive. It tags its
animation video channel `0x00`, nominally CLUT4 at normal resolution, and the
data does not autocorrelate at any CD-i line pitch and has bit 7 set on 43.6 %
of its bytes. Where a title expands its own frames in software before handing
them to the hardware, the coding byte records what the *display* will eventually
be given, or nothing meaningful at all. Corroborate it for video, and corroborate
it for audio too now that the type bits have been caught lying.

**And the eighth disc names the register that does the overriding.** Burn:Cycle
tags 270,741 sectors coding `0x01`, CLUT7 — and 40.77 % of their bytes have bit 7
set, which a 7-bit index cannot. They are **RL7**. The mechanism is not software
expansion: that binary contains **no decoder at all** (grep its 633 symbols for
`dec`, `unpack`, `expand`, `compress`, `inflate` and the entire yield is three
run-length *drawing* primitives for sprites). The MCD212 decodes it, and what
tells the MCD212 how is the plane's **File Control Table**, which the program
writes at run time. That title's symbol table names the routines:

```
vmgr_swap_fctA   vmgr_swap_fctB   vmgr_swap_fctBoth
vmgr_setICF      vmgr_readICF     vmgr_icfA   vmgr_icfB
```

and its diagnostics confirm them — `Write PA FCT #%d: %d`, `Unrecognised FCT`,
`Bad ICF value %ld`, `Plane %ld is not a CLUT plane (code = %lx)`.

**So the correct statement is: the subheader coding byte is a claim by the
authoring tool, and the FCT is the fact.** Nothing forces them to agree. On a
disc that ships a symbol table you can find out which routine writes the FCT; on
one that does not, the byte is a hypothesis and the pixels are the test.

**Video coding byte:**

```
bits 3-0   0 CLUT4   1 CLUT7   2 CLUT8   3 RL3   4 RL7   5 DYUV
           6 RGB555 lower      7 RGB555 upper     15 MPEG
bits 6-4   0 normal resolution   1 double   3 high
```

**A value the table does not define can be real.** Laser Lords tags 64,629
video sectors — 22 % of its pressing and its single largest stream — with
coding `0x08`, which is bits 3-0 = 8 and sits past `RGB555 upper`. It is not a
misread: the subheader is stored twice in every sector and the two copies agree
on all 290,955. The records open with the four ASCII bytes `NS07` followed by
`10 80 80` = the DYUV start triple, and the format is not decoded. Record the
value, cut the records, and say you stopped.

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

**[2 of 8] Level A does get used, and only for ten seconds.** Five discs passed
through without a single `0x10` sector. Laser Lords has 384 of them — 192 in
each of its two bumpers — and *A Visit to Sesame Street: Letters* has the same
384, in the same two bumpers, and they are **the same bytes** (§5b). Neither
disc uses Level A anywhere else. The one place on either that spends four times
Level C's bandwidth is the publisher's logo.

**And the duration was wrong in this document for two revisions.** It said
11.70 s per bumper, which is 2,304 samples a sector — every byte of the user
data a sample, with no room for the sound-group headers. **A Level A sector is
eighteen 128-byte sound groups exactly as a Level B or C sector is**: 16
parameter bytes and 112 data bytes, and at 8 bits a data byte is one sample, so
**2,016 samples a sector and 0.053333 s**. Measured on *Letters*: the parameter
bytes of all eighteen groups of a Level A sector satisfy section 8's own test
(`bytes 0-3 == 4-7` and `8-11 == 12-15`) in 18 of 18. So each bumper is
**10.24 s** and the two of them are 20.48 s, not 23.4. It also cross-checks
against the bandwidth: Level A mono is a quarter of the CD's 176,400 B/s, which
is 18.75 sectors a second, and 37,800 / 2,016 = 18.75 exactly. At 2,304 it
would be 16.4, which is not a ratio anything uses. `cdicensus.py` in
`cdi-avisittosesamestreetletters-doc` has the corrected table.

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

**And the odd pitch can be bigger than the screen.** *A Visit to Sesame
Street: Letters* renders its four location backgrounds at **640 × 244** —
autocorrelation 0.6971 at 640 against a 0.074 baseline, with the harmonic at
1,280 — which is wider than CD-i normal resolution and narrower than double,
and it is a **panorama seen through a 384-pixel window**. Its run-length
animation cels are 640 wide too (3,999 of 4,000 exact line ends, against 3,000
of 4,000 at 384), and its hot-spot table works in a 1,280 × 480 space, which is
that picture doubled. **Three independent measurements landing on one width is
what a proved geometry looks like**, and none of the three is a display mode.

**Its film is not a display mode either.** 105,104 DYUV sectors cut into
eight-sector slots give 13,138 frames of **172 × 108** at 8 fps — 18,576 bytes
of picture in an 18,592-byte slot, with the sixteen spare bytes zero on every
frame. 172 is not a fraction of 384 and nothing in the container states where
on the screen it goes. **A CD-i title may pick any width it likes and letterbox
it in software**; the list of widths to try is a starting point and not a
constraint.

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

**When you find the odd coding byte out, ask which way the trade went.** It is
usually the thing that was worth twice the bandwidth — but on *Letters* it is
the opposite: that disc is Level B mono across 55,295 sectors in five files and
Level C mono in exactly one, `tv.rtf`, and Level C is **half** the sample rate.
The file where the soundtrack was halved is the one whose video needs 64 of
every 75 sectors. First disc of eight where the outlier is an economy rather
than an extravagance, and the rule about *where* to look survives it intact.

**And on Burn:Cycle the change of coding byte marks the largest change of purpose
there is.** That disc has exactly two video codings — `0x01` on 270,741 sectors
and `0x05` on 35,002 — and the boundary between them is not two kinds of picture:
`0x01` is the film, in RL7, and 21,888 of the `0x05` sectors are **the
soundtrack**, in ADPCM, wearing the video type bit. Same rule, and the thing on
the other side of it is a different medium. On both 1993 discs it was the ending. **On Merlin it is
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
was in the buffer, not zeroed. **Laser Lords' slideshow is the same
arrangement**: 3,800 DYUV sectors on one channel, 3,800 = 95 × 40, 40 × 2,324 =
92,960 against 92,160 of picture, 800 bytes of slack per picture, 95 pictures at
384 × 240 on a PAL pressing.

**And it carries a warning the sixth pipeline paid for.** 40 × **2,304** is also
92,160 exactly, so the arithmetic closes at both widths and only one renders.
At 2,304 the pictures are recognisable and shear twenty bytes further left in
every sector; raising the DYUV line start from 16 to 128 makes them look
*better* while leaving a starfield mid-grey and the highlights wrapped through
zero. Two errors cancelling, the second introduced to hide the first.
**Do not tune a constant to compensate for a geometry you have not proved** —
and on Form 2, all 2,324 bytes are picture. Look for that before concluding the data is
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

**But the count does not collapse on the high side, and that will fool you.**
The "run to the end of the line" byte absorbs any surplus, so **every width at or
above the true one reads 1.000**. Burn:Cycle:

```
python tools/cdirl7.py widths 400      (cdi-burncycle-doc)

 width     lines     exact   ratio
   352     34637     23703   0.684
   368     30383     23731   0.781
   376     26384     23521   0.891
   384     22664     22657   1.000   <== and it saturates from here
   400     22662     22657   1.000
   512     22660     22654   1.000
   768     22654     22652   1.000
```

**The number to quote is the smallest width at which the ratio saturates**, and
the step — 0.891 at 376 to 1.000 at 384 — is the proof. Quoting 512 because it
also reads 1.000 would be quoting an artefact.

**Two more ways to nail the height, both cheap and both independent of pixels.**
Vertical autocorrelation over decoded lines gives Burn:Cycle 239–240 against a
0.20 baseline, which is a real peak one line wide and no narrower. What settles
it is the **trigger bit**: per record, the number of sectors with submode `0x10`
set equals the number of times the running line count crosses a multiple of 240,
**exactly, on 857 of 1,025 records**. A height of 239 or 241 makes that agreement
zero. See §9.

**And an RL7 stream need not be cut into frames at all.** Burn:Cycle's has no
per-frame header in 617 MB, because the decoder writes lines into a *wrapping
plane* and the program points the Line Control Table at whichever 240 are the
current picture — `smgr_set_wrap_plane`, `smgr_set_wrap_level`, `get_wrap_size`,
`set_wrap_ptr`, and a panic string reading `Double wrap`. If you are looking for
frame headers in a run-length stream and not finding any, **check whether the
title manages a wrap pointer** before concluding the format is undocumented.

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

**[5 of 8]** *(Letters does it twice as well, and one of the two is the whole
of its main content: its film is **8 sectors per frame, 18,592 bytes, of which
172 × 108 = 18,576 are picture and the last 16 are zero on all 13,138 frames**;
and each of its four location screens occupies a 68-sector slot holding one
640 × 244 picture and 1,872 bytes of something else. The slot is proved
without decoding anything — the video channel's LBA run is interrupted 31
times and **all 31 interruptions fall between frames**. If a stream has gaps,
check what they are aligned to before you check anything else.)* Laser Lords
does it twice — its 95 DYUV stills occupy 40 sectors
each with 800 bytes of slack in the last one, and the sixteen filenames in
`cdi_space`'s missing-stream table sit in 20-byte slots NUL-padded. Both
Soccer's sprites and Link's animation frames sit in
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

**And the same trap runs the other way: decoding mono speech as stereo leaves it
recognisable and unintelligible.** Each ear gets every other 28-sample unit, in
order and at the right pitch, so the waveform passes every screening test in this
document and a listener reports "speech, but I cannot make it out". That is the
tell. On Burn:Cycle it was the only tell there was, because the two candidate
readings are byte-for-byte the same data at the same duration. **If a decode
sounds like speech and is not intelligible, try the other channel count before
you blame the recording.**

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

**[1 of 8]** *(Re-declared rather than rechecked, three times now: neither Laser
Lords nor A Visit to Sesame Street: Letters is CD-i Ready and neither carries
any CD-DA, so neither can exercise this. The denominator moves and the
numerator does not.)* The Apprentice carries its whole soundtrack in
**both** formats:
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

**[1 of 8]** *(Re-declared: no CD-DA on Laser Lords, Letters or Burn:Cycle, so no track
length to compare a channel span against. The duration arithmetic itself checks
out on both — Laser Lords' `luxor_v.rtf` channel 15 is 1,247 Level C mono
sectors, predicted 266.03 s and decoded 266.03 s; Letters' `misc.rtf` channel 8
is 2,660 Level B mono sectors, predicted 283.73 s and decoded 283.73 s, and its
`tv.rtf` channel 15 is 7,575 Level C mono sectors, predicted 1,616.00 s and
decoded 1,616.00 s.)* A Level C
stereo channel gets one sector in eight at 0.1067 s
each, and the drive delivers 75 sectors a second — so a channel's **span in
sectors** is its duration in 75ths of a second, which is also its length as a
CD-DA track. On The Apprentice `music.rtf` channel 3 spans 21,449 sectors and
CD-DA track 7 is 21,449 sectors.

Walk the EOR markers, subtract, and compare against something you know the
duration of. If the numbers line up, the file was authored to play in real time
at exactly one disc revolution's worth of bandwidth per channel, and your
channel assignment is right.

**The test has a precondition, and the eighth disc violates it.** It assumes the
audio is consumed at the rate it is delivered. Burn:Cycle's is not: its sounds
are **assets grabbed into memory and played later**, and the symbol table says so
(`grab_audio`, `snd_grab`, `snd_release`, `AssetToSound`, `C_KEEPINVSOUND`) and
so do its diagnostics (`Keeping audio`, `Releasing audio`,
`PANIC:Already kept audio`, `Sound already kept`).

On that disc the test looked decisive and was not:

```
  as Level C stereo / Level B mono : median decoded duration / span = 0.560
  as Level C mono                  : median decoded duration / span = 1.120
```

1.120 is impossible for a stream that plays as it arrives, and Level C mono is
what the disc actually is — settled by ear against three candidates, because
nothing in the bytes separates Level C stereo from Level B mono at all (same
4,032 samples a sector, same 0.10667 s).

**So: before using duration-against-span, check for a `grab`/`release` pair in
the symbol table or a `Keeping`/`Releasing` pair in the diagnostics. If the title
loads its sound rather than streaming it, the ratio carries no information.**

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

On *Letters* every one of the eight real-time files is Form 2 throughout except
for its interleaved Form 1 data sectors, so the fiction is a flat 13.477 %:
they declare 576,854,016 bytes across 281,667 sectors and those sectors hold
654,594,108, understating by **77,740,092 B on a 673 MB disc**.

On Laser Lords every one of the twenty-two real-time files is Form 2
throughout, so the fiction is a flat 13.48 % across all of them: they declare
586,166,272 bytes and their sectors hold 665,161,336. **The file system
understates its own streams by 78,995,064 bytes — 79 MB on a 684 MB disc.**
Quote that when anybody adds up a Green Book directory listing.

On Merlin every real-time file is Form 2 throughout, so the fiction is a flat
13.5 % everywhere — `boltlib0.blt` is 3,446 x 2,324 = 8,008,504 bytes and the
directory says 3,446 x 2,048 = 7,057,408. The header inside the file gives its
own size as 8,007,558, which confirms the sector reading and leaves 946 bytes of
slack. **When a file carries its own size field, use it as the check.**

**[all]** Sectors tagged neither VIDEO nor AUDIO nor DATA are **bit-rate padding
and carry nothing** — 19.5 % of Soccer's one real-time file, **24.1 % of the
whole of Laser Lords**, 26.7 % of the whole of Origami, **36.3 % of the whole
of Letters**, 38.3 % of the whole of Merlin, **49.6 % of the whole of Link**.
Drop them; concatenate the rest at 2,324 bytes each.

**And the eighth disc sets a new floor by a factor of thirty-five.**
Burn:Cycle is **0.68 %** — 2,109 untyped sectors in a 309,902-sector file — and
it is **not a jukebox**:

```
python tools/cdirtf.py starts BurnCycle.rtr

  channel 16  file offset 0        channel  1  file offset 110
  channel  2  file offset 19       channel 10  file offset 8,421
  channel  3  file offset 60       channel  8  file offset 9,034
  channel 17  file offset 62       channel  9  file offset 34,270
                                   channel  4  file offset 141,763
  => not a round robin
```

**So the rule below — *a disc whose aggregate padding is low is a disc full of
jukeboxes* — is a correlation drawn from four discs and not a mechanism.** The
mechanism is that **padding is bandwidth nobody claimed**. A jukebox claims it by
running N alternatives at once; a film claims it by running one thing that needs
almost all of it. Burn:Cycle's picture stream takes 85.8 % of the file's sectors
and its soundtrack another 7.0 %, which leaves nothing over.

**And low padding does not imply seek points either.** §9 says a run of 100 or
more empty sectors is a seek point. Burn:Cycle has **1,875 padding runs, the
longest 15 sectors, and none at all above 100** — the padding is an interleave
remainder, not a gap, and that file has no seek structure expressed as padding.

**And say which denominator you used.** A census that walks every sector in the
image counts the pre-file-system region, the tail and the inter-file gaps as
untyped too. On *Letters* that is 108,544 untyped sectors — 37.909 % of the
image — of which **4,593 belong to no file at all**, so the disc's own bit-rate
padding is 103,951 sectors: **36.305 % of the image and 36.90 % of its
real-time sectors**. The two figures answer different questions and only the
second compares against another disc's design.

Laser Lords is the lowest whole-disc figure of the five that are comparable,
and the reason is the jukeboxes: its ten round-robin files run at 19–28 %
padding where everything else on that disc runs at 41–63 %. **A disc whose
aggregate padding is low is a disc full of jukeboxes**, which is the same rule
as the per-file one at one level up.

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

**[4 of 8]** Link's `lmusic.rtr` is the other pattern, and the tell is
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
starting wherever the file's own header ends.

**And Letters makes it four**, with one jukebox in eight files: `misc.rtf`,
eight Level B mono channels descending 15 → 8, first sectors contiguous from
file offset 5 to 12, 4,798.9 s of speech and 37.6 % padding against 71–81 % on
the four files that are not jukeboxes. **Its first sectors do not start at file
offset 0**, because sectors 0–4 carry the file's own container header on
channel 0 — so `cdirtf.py starts` reported "not a round robin" until the test
was made to exclude the header channel. **Run section 9's contiguity test on
the channels that carry alternatives, not on every channel the file has.**

**Laser Lords does it ten times in one file system**, and it is the design of
the whole disc rather than one file in it: the seven `world_v.rtf` streams (16
channels, 15 on one), `messages.rtf` (16), `help.rtf` (14) and `themes.rtf`
(4), every one of them descending from channel 15 and starting at the file's
very first sector.

```
python tools/cdirtf.py starts luxor_v.rtf     (cdi-laserlords-doc)

  channel 15  LBA 147626  (file offset 0)
  channel 14  LBA 147627  (file offset 1)
  ...
  channel  0  LBA 147641  (file offset 15)
  => a jukebox: switching costs no seek
```

Four of eight discs use the pattern, and the earliest one uses it for
**6 h 34 m of speech across 401 individually addressable records in one file**.
The 1992 disc did it before either of the discs that were thought to have
invented it.

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
raise an event to the application when that sector is delivered.

**On Link it is a cue point that sits *near* structure**: its 71 video triggers
are 33–40, 77–83 or 119–125 sectors apart where the real picture boundaries are
exactly 40, 80 and 119.

**On Burn:Cycle it IS the structure, and it is the finest structure that file
has.** That disc sets the trigger bit on **one sector per frame**, on the sector
where the frame's last line completes:

```
record  sectors  lines  frames at 240  triggers  frame-end sectors  coincide
   300      207   8416             35        35                35     28 of 35
   700       88   3843             16        16                16     16 of 16
   420      243   9612             40        40                40     38 of 40

over all 1,025 records: trigger count == frame count on 857
                        53,794 triggers on the film channel, 17.3 % of the file
```

That proves the frame height without decoding a pixel: at 239 or 241 the
agreement is zero.

**So the instruction that survives both discs is neither *triggers are timing*
nor *triggers are structure*. It is: count the triggers, and compare the count
against something you derived another way.** On Link that comparison says
"near"; on Burn:Cycle it says "exactly", and either answer is worth having
before you go looking for frame headers that may not exist.

Triggers are also the whole synchronisation mechanism on a streaming disc.
Link's cutscenes carry no timestamps anywhere; 48 audio sectors have the trigger
bit set, and because a real-time file is read at exactly 1x, **the sector is the
clock**.

### A data channel may carry the game itself

**[1 of 8]** Burn:Cycle's `/BurnCycle.rtr` interleaves **353 named, compiled
68000 script objects** with the film they belong to, on channel 16 — 566 Form 1
sectors, 1,159,168 bytes, 0.16 % of the file. Each object begins:

```
0x00  name, NUL-terminated
      u16 0x0001
      a second name, NUL-terminated and NUL-padded
0x10  u32  total size
0x2a  u32 x4  four monotone section offsets, all < size
      then 0xFF runs (the cross-reference slots), a NUL-separated string pool,
      and 68000 code
```

**The second name is the view the object is installed at.** On 343 it is the
object's own name; on ten it is a different one, and five of those ten are the
same minigame installed at five viewpoints. The executable's symbol table names
the machinery — `LoadObject`, `InstallObject`, `LocateXRefs`, `LocateGlobals`,
`EnterObject`, `CallObject`, `sendMessage`, `objname`, `xrefname` — and its
diagnostics print `Installing object %s:%d @%lx`, which is the name-and-number
in the header.

The tell that this is code and not data is §9b's: **the most common printable run
in the whole data channel is `N^ _N`, 2,781 times**, which is `4E 5E 20 5F 4E` —
`UNLK A6; MOVEA.L (SP)+,A0`, a C function epilogue. Two thousand seven hundred
function endings in the data channel of a video stream.

**So on a streaming disc, run §9b's check on the DATA sectors of the stream, not
only on the files.** A title that ships its logic this way has an executable that
looks far too small for what it does — Burn:Cycle's is 92,880 bytes and runs a
two-hour game — for the same reason The Apprentice's is 23,236 bytes.

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

**[3 of 8]** *(Re-declared twice: neither Laser Lords nor Letters has a chunked
asset file to open. Letters has five OS-9 modules in two files, a hot-spot
table, two 2,048-byte descriptors and eight streams, and its containers are
inside the streams rather than in the file system.)* **Chunk 0 of an asset file is quite often 68000
code.** Link ships two
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

**[2 of 8] And when the chain does not land, suspect alignment before you
suspect the layout.** *(Re-declared: no chained container on Laser Lords or on
Letters — the latter's `0x00001190` header is a flat record array with a size
field that checks out on both files where the size is verifiable, and nothing
chains. The
nearest thing is the sixty-value frame table in its bumper descriptor, which is
monotone within groups and whose referent is not established — an open question
rather than a sample.)* The Apprentice's 45 `.dat` files hold `u16 count` then one
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

Seven very different discs, for comparison:

| | Ultra CD-i Soccer (1997) | Origami (1993) | Link: The Faces of Evil (1993) | Merlin's Apprentice (1995) | The Apprentice (1994) | **Laser Lords (1992)** | **A Visit to Sesame Street: Letters (1991)** | **Burn:Cycle (1995-05)** |
|---|---|---|---|---|---|---|---| --- |
| Track | 7,875 sectors (1:45), 2.4 % of a CD | 326,400 sectors (72:32), 98 % | 255,924 sectors (56:52), 77 % | 131,610 sectors (29:14), 39.5 % | **no data track** — 69,150-sector pregap of track 1 (19.9 %), plus 22 CD-DA tracks (73.9 %) | 290,955 sectors (64:39), **87.4 %** | 286,330 sectors (63:37), **86.0 %** | 314,724 sectors (69:56), **94.5 %** |
| Entries | 12 dirs, 144 files, 10,635,729 B | 5 dirs, 46 files, 631,506,598 B | **0 dirs, 14 files** | **0 dirs, 18 files** | 2 dirs, 91 files — 69 CD-i files of 113,851,360 B, plus 22 CD-DA entries | **0 dirs, 33 files** — one of them the path table; 586,463,563 B declared, 665,161,336 B actually carried | **0 dirs, 14 files** — one of them the path table; **no text file at all** | **0 dirs, 7 files** — one of them the path table; 634,807,819 B declared, **719,646,448 B** actually carried |
| Pre-FS region | 2,269 sectors, 28.5 % of the image | 2,268 sectors, 0.7 % | 2,269 sectors, 0.9 % | 2,269 sectors, 1.7 % | 2,268 sectors, 0.8 % — **and an identical 2,250-sector copy after the last file** | 2,269 sectors, 0.8 % | 2,269 sectors, 0.8 % — and the **lead-in is plain zero, not scrambled zero** | 2,269 sectors, 0.7 % |
| Head audio | 7.41 s + 8.31 s mono, L = R exactly | **byte-identical to Link and Merlin** | **byte-identical to Origami and Merlin** | **byte-identical to both 1993 discs** | **a third recording**: 8.21 s + 14.74 s, L = R exactly, peak 16,383 | **byte-identical to Link, Origami and Merlin — and nine months older than any of them** | **byte-identical to Laser Lords, Link, Origami and Merlin — and eleven and a half months older than any of them** | **a fourth recording**: 8.13 s + 8.22 s, 13.29 s of silence between, L==R 3.26 % once the silence is excluded |
| Tail padding | plain zeroes | plain zeroes, 15,770 sectors | plain zeroes; the 4 `0x20` sectors are rip damage, not padding | plain zeroes, 2,258 sectors | the head block again, then 9,032 sectors of CD-DA silence | plain zeroes, 2,255 sectors (5 of them rip damage, not pressing) | plain zeroes, 2,264 sectors (2,250 of them past the volume space) | plain zero, 2,421 sectors (4 of them rip damage) |
| Executable | 229,376 B, 2 modules | 82,720 B, 4 modules | 135,168 B, 1 module (+ a 12 KB bumper) | 139,264 B, 1 module, edition 7 | 23,236 B game module; 9 modules over 6 files | 20,528 B launcher + 133,234 B game; **9 modules over 7 files**, four of five edition 7 | 66,588 B in **3 modules**, plus a 12,288 B bumper player in 2; editions 7, 1, 0 | 108,544 B in **3 modules**, all edition 1; `M$Exec` at `0x782a` because `main` was linked first |
| Symbols | none | none | **325 C function names in the binary** | **887, in a `.stb` file in the root** | **521, in a `.stb` file in `/CMDS/`** | none — no `.stb`, and no loose names either | none — no `.stb`, no loose names | **633, in a three-table `.stb`** whose header binds each table to its module by CRC-24 |
| Streaming | one MPEG file | 79 % of the disc | 99.7 % of the bytes | 88.6 % of the disc | 69.5 % of the CD-i area, all of it music | 94 % of the disc, in 22 files | **98.4 % of the disc**, in 8 files | **98.5 % of the disc in one file**; 99.23 % of the volume space |
| Padding | 19.5 % of the one RTF | 26.7 % of the disc | **49.6 % of the disc** | 38.3 % of the disc | 23-68 % per RTF; **zero free sectors between the path table and the last file** | **24.1 % of the disc** — the lowest of the comparable four | **36.3 % of the disc**; 9.2 % in the film and 72–81 % in the four location files | **0.68 % of the disc** — 1,875 runs, longest 15 sectors, none over 100 |
| Compression | none, bar the run-length sprites | none, anywhere | RL7 for everything that moves, 10.7:1 | BOLT, on 54 % of the library, **1.17:1** | none, anywhere | unidentified — `NS07`, video coding `0x08`, 22 % of the disc, not decoded | RL7 for the animation cels, at width **640**; the publisher's CLUT4 logo undecoded | **RL7**, 6.6:1 on live action and CGI |
| Graphics | raw CLUT bitmaps + palettes on disc | DYUV and CLUT7, real-time only | CLUT7 playfields, RL7 cels, one DYUV still | inside the BOLT container; frame codec unidentified | CLUT7 and CLUT8 in `.dat` containers, pitches 384 **and 320** | 95 DYUV stills at 384 × 240, CLUT4 in the bumpers; **no bitmap file anywhere** | **13,138 DYUV frames at 172 × 108**, 5 CLUT7 pictures — four of them **640 × 244** | **RL7 at 384 × 240**, 55,368 frames at 12.5 fps; channel 2's 41-sector 384 × 240 slots undecoded |
| Palettes | 192/384/768 B, entry 0 `#00FF00` | **none on the disc at all** | 384 B inline, entry 0 `#FFFFFF` | 390 B BOLT members, 6 + 128 × 3 | 384 B and 768 B, entry 0 `#000000` | none on the disc; one 195-entry CLUT inside each bumper descriptor sector | 128-entry CLUT7 in the bumper streams' own data sectors; **none for the location screens** | **none on the disc**; a 128-entry CLUT7 arrives as command `0x0009` in the stream |
| Audio | 12 effects, 10.2 s, Level C in AIFF-C | 5 languages, 3 h 57 m, raw | **100.8 min**, Levels B and C, raw | 47 min, Level B mono, raw | 46 min ADPCM, **all Level C stereo**, plus the same 46 min as CD-DA | **6 h 34 m**, Level C mono throughout, **plus 11.7 s of Level A** in each bumper | **2 h 05 m**, Level B mono throughout, Level C for the film, 20.5 s of Level A in the logo | **77:49**, Level C mono — and **not one sector is typed as audio** |
| Video | MPEG-1 368 × 272 | 40 files, 7 streams each, no MPEG | no MPEG; RL7, CLUT7, DYUV | no MPEG; 89 animations in 7 files | no MPEG; **no video streams of its own at all** | no MPEG; 95 stills, 94 animations undecoded | no MPEG; **32 alphabet cartoons, 27 m 22 s at 8 fps** | no MPEG; one 69-minute film, 12.5 fps, in one file |
| All-zero files | 16, totalling 1,070,080 B | none | none | none | none | none | none | none |
| Dangling path references | 8 | none | none | none | none — 20 templates cover all 56 assets exactly | **26**, of which **16 are `.rtf` names never pressed** | none — 12 of 13 files named by a binary, the 13th by the descriptor | 2 — `vid_regs.prf` and `player_shell_settings.prf`, both development-host files |
| Duplicated files | none | none | **the same 30 MB pressed 3× (11.5 %)** | **the same 8 MB pressed 3× (7.9 %)** | none; the **filler block** is pressed twice | none; but **one 11.94 s clip pressed 7×** and the bumper pressed twice (NTSC + PAL) | none; but **one 640 × 244 screen pressed four times** and the bumper pressed twice | none at file level; **one minigame object pressed 5 times** and 4 view-variants of another |
| Languages shipped | English only | five — one of them not on the box | English only | English only | English only | English only | English only | **one, and it is Italian** — 22 strings and a dub |

The eight discs bracket the format. Soccer is what a *game* looks like on CD-i
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

**A Visit to Sesame Street: Letters is the seventh shape, and it is a place
rather than a game.** Its program owns nothing and it streams almost
everything, like Origami and Laser Lords — but what it streams is neither a
presentation nor a story. Four painted rooms 640 pixels wide sit still until a
child points at something, a 20 KB hot-spot table in half-pixel units says what
may be pointed at, and a television inside the picture plays 27 minutes of
alphabet cartoons at 172 × 108. **Four fifths of its four location files are
empty sectors**, which on any other disc reads as waste and here reads as
*waiting*: a real-time file is delivered at 1× whether or not a three-year-old
is doing anything. And the same principle appears three times at three scales —
eight speech channels permanently under the head, a container header written at
both ends of every stream, and the street screen pressed into all four location
files — so that no move the child can make costs a seek. Nothing else in the
collection spends 190 MB on standing still.

**Laser Lords is the sixth shape, and it is Link's shape a year early.** Its
program owns nothing, its 33 files are 22 streams and 9 modules, and 94 % of the
pressing is real-time. What is new is *why* it is interleaved: ten of its
twenty-two streams are jukeboxes of fourteen to sixteen parallel speech
channels, so six and a half hours of dialogue are all simultaneously in front of
the head and switching between speakers costs nothing. That is the lowest
whole-disc padding figure of the four comparable discs and the largest audio
inventory in the collection, on the oldest disc in it. Whatever this platform's
streaming idiom was, it was not invented between 1993 and 1995; it was already
finished in 1992.

**Burn:Cycle is the eighth shape, and it is a film with a program inside it.**
The other seven are software with content in them; this one is 98.5 % of a CD in
a single real-time file that carries a sixty-nine-minute picture, its soundtrack
and the game's own logic all at once, past the head at 1×. Its executable owns
nothing — not even the room scripts, which arrive as 353 compiled 68000 objects
interleaved with the film they belong to — and its 0.68 % padding is not thrift,
it is the absence of anywhere to put anything. Link spends half its surface
keeping the drive fed; this disc spends two thirds of one per cent, because the
picture never stops needing the bandwidth. It is also the only disc here that
ships in one language and that language is not English.

If your disc compresses something, has more than one directory level, ships
palettes for CLUT data, uses MPEG, carries symbols, keeps its assets in a
container with its own header, hides its whole volume in a pregap, spells
something out in a picture because it has no text at all, or puts its scripts in
the stream, it is doing something at least one of these did not. **Carry the method forward, not
the numbers.**

### Count the languages yourself

**Do not trust the language list in the dump's filename.** Origami is dumped as
`(En,Fr,De,Nl)` and carries **five** narration channels plus a five-entry
language menu plus five localised error screens — the fifth is Japanese. The
cheapest check is the audio channel census in section 9; the confirmation is
usually a menu screen, and on a disc with no text files it will be a picture, so
you will have to render it.

**And a disc may ship exactly one language that is not English.** Burn:Cycle is
an Italian pressing of a British title and its Italian is **22 distinct strings
in 34 places** — four in the executable (a dirty-disc panic screen, stored in
reverse display order, §6c) and eighteen in the string pools of five script
objects inside the stream — plus a re-recorded dub.

**Two lessons, and the second is the one that costs sessions.**

The Italian strings are **Latin-1** while that disc's `abstract.txt` is
**Mac OS Roman** (`0xD5` right quote, `0xC9` ellipsis, `0xAA` trademark, CR line
endings). **Two encodings on one disc, from two desks**, and a single-encoding
assumption reads one of them as noise.

And: **a localised product does not contain the name of the language it is
localised into.** Grepping a CD-i disc for `Italian`, `italiano`, `Deutsch`,
`Fran` or `lingua` will find nothing on a disc that is fully localised. Grep for
**function words and accented letters** — and grep the whole image, not the
executable, because on a streaming disc the executable is a rounding error.
On Burn:Cycle it is 122,880 bytes of 740,230,848, and all but four of the
Italian strings are in the other 99.98 %.

*(And use a lookahead for the string terminator, not a consuming match. A packed
string pool puts one NUL between neighbours, so `\x00(...)\x00` consumed by
`finditer` skips every second string. That mistake reported eleven Italian
strings where there are eighteen.)*

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
2. `cdihead.py map` — the pre-file-system region, before anything else. The
   window is 2,250 sectors ending immediately before the path table, so the
   path table's LBA fixes the alignment. **Descramble it — always — and then
   hash it against the three known recordings before analysing anything**:
   `a0ed87f2e98b43f91281d16390fb178b` (1991–95, five discs),
   `4e61f608e1f1455d9ad5b2a0615dbbd3` (The Apprentice, 1994),
   `d1bc6b7dbed8abfd30df0ff4c7cada48` (Soccer, 1997) and
   `b80d0c314bd303bb9c21495fcdf41975` (Burn:Cycle, 1995-05). Those hashes are of
   the **audio**; a hash of the image bytes will never match one of them and
   will look like a new recording. Five of eight discs match the first
   outright.
   **Hash the sectors after your last file too**: on one disc so far they are
   the same block again. `sixdiscs.py head` does all seven in one command.
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
11. Audio: decode the coding byte, check the duration arithmetic **unless the
    title grabs its sound into memory rather than streaming it**, decode one
    channel per stream and *listen* — several questions only ears can close.
12. Graphics: palette sizes → pixel-value ceilings → autocorrelated pitch →
    **prove the total to the byte** → render everything.
13. Tagged chunks and containers, validated by chaining them until they land
    exactly on a header field or on the file size — try sector alignment before
    giving up on a layout — and check whether any of them is code. **Report how
    many files the check closed on, not that it closed.**
14. If the disc carries CD-DA, look for the table in the executable that maps
    tracks onto anything else, and cross-check the durations both ways.
15. **Hash every stream, not every file, and compare against one sibling.**
    `cdirtf.py hash` on your disc and on the nearest disc by date, read side by
    side. On the seventh pipeline that was two commands and it found 2,246,756
    bytes of publisher's bumper identical across eleven and a half months —
    something no file-level list can see, on two discs whose files differ.
16. **Run §8's sound-group test over every stream, not only over the ones the
    type bits call audio.** It is one pass and 64 bits of redundancy per group,
    and on the eighth disc it is the only thing that found 77 minutes of
    soundtrack on a disc whose census said zero audio sectors.
17. **Count the TRIGGER bits per record and compare the count against something
    you derived another way.** On one disc that says "near a boundary"; on
    another it says "exactly one per frame" and proves a geometry without
    decoding a pixel.
18. **On a streaming disc, search the whole image for the things you would
    normally grep the executable for** — languages, filenames, magic numbers.
    When 98.5 % of the volume is one file, "I searched the binaries" is a
    statement about a rounding error.
19. **If a video stream has gaps in its channel's LBA run, check what they are
    aligned to before you check anything else.** On *Letters* all thirty-one
    gaps in the film's sector run fall between frames, which proves the frame
    size without decoding a pixel — and the same gaps turn out to be the cuts
    between its thirty-two segments.

Write down what does *not* resolve. Half of what makes a disc interesting is the
list of things that are measurably odd and not yet explained.

**And on a disc with no text, read the pictures for words.** *A Visit to Sesame
Street: Letters* has no copyright file, no abstract, no bibliography, no symbol
table and no `printf`; the only two human names on it are in the data-preparer
field, and the only place its own abbreviation is expanded is a 384 × 240 CLUT7
still inside the publisher's bumper reading `CHILDREN'S TELEVISION WORKSHOP`.
Typos live in the fields that were typed; on a disc for people who cannot read,
so does everything else.

---

## 12. The hash lists, and what they cannot see

**[all]** Every disc repository now publishes `notes/sha1-all.txt`: one line per
file, `sha1  size  path`, generated for all eight at once by
`cdi-burncycle-doc/tools/sixdiscs.py write`. **345 records over eight discs.**
Before the sixth pipeline there were none, and the cross-disc comparison chapter
of five documents could only say it had not been attempted.

Comparing all 345 finds **two** crossings, and the eighth disc adds one line
to one of them and nothing else:

```
6fa9eb5c50bcb6e9e6b82b51128ad52649a0e186   10 B      Letters, Laser Lords,
                                                     Link, Merlin, Burn:Cycle
                                                     /path_tbl
fb36018b660928ced83f5ae22d3bb2b7dced89bc   1,280,000 B  Link /bumper.rtf,
                                                        Apprentice /CMDS/philips.rtf
```

The first is ten bytes of path table, now on five discs — **and it is not a
crossing in any useful sense.** A Green Book path table for a single-directory
volume with its root at LBA 2,270 is the same ten bytes whoever pressed the disc
(§3). It is a coincidence of format counted as a shared component, and it is the
most misleading line these lists contain. The second is the shared Philips bumper —
and it matches **only because the directory size is a fiction**: 1,280,000 bytes
read at 2,324 per Form 2 sector stops at sector 551 of 625, before the
descriptor sector where the two files differ. A correct read of both files would
not match.

Meanwhile, invisible to every one of those 324 records:

| shared thing | size | in `sha1-all.txt`? | in `streams.txt`? |
|---|---:|---|---|
| the 1991–95 head recording, **5 discs** | 5,229,000 B | no — belongs to no file | **no** — belongs to no file |
| the 1991–92 bumper's eight streams, 2 discs | 2,246,756 B | no — the files differ in length | **yes** |
| the Philips bumper streams, **3 discs** | 720,440 B | by accident, and on only 2 | **yes, on 3** |
| Laser Lords' two bumpers' audio | 446,208 B | no | **yes** |
| Letters' street screen, 4 files on 1 disc | 474,096 B | no | **no** — a picture inside one channel |
| the path table, **5 discs** | 10 B | yes | yes |

**Before `streams.txt` existed, 7,475,756 bytes were shared across discs and
invisible to all 338 file records, while ten bytes were shared and visible.**
The second list sees 2,525,084 of them (and Merlin's 346,276 that nobody had
found), leaving **5,703,096 invisible** — of which 5,229,000 is the filler
recording, which belongs to no file on any disc and never will be visible to a
list of files or of streams. The ratio got a third worse the day the seventh
disc was measured, and it got worse because of the single largest cross-disc
result the branch has produced.

**A file-level hash list is structurally blind to both kinds of sharing this
platform actually does.** One is *below* the file system and one is *inside*
containers of different lengths.

So the collection's crossing rule — *two objects share files only where they
share a third-party component* — is restated for this branch:

> **Two objects share bytes where they share a third-party component, and the
> component need not be a file.** On CD-i the unit of sharing is the *stream*
> and the *pre-file-system region*, not the directory entry.

And, after eight discs, there are **three** levels rather than two:

> The **file** is visible to `sha1-all.txt`. The **stream** — a `(file, channel,
> type, coding)` run — is visible to `streams.txt`, and that is where the
> publisher's assets live. The **object inside a stream** — one picture, one
> clip, one script — is visible to neither, and finding one still takes a
> hypothesis.

and the instrument that follows is a hash per
`(file, channel, submode, coding)` run, not a hash per file.

### The second list now exists, and it found something five sessions had missed

`cdi-burncycle-doc/tools/cdistreams.py write` writes `notes/streams.txt` for
every disc in the table: `sha1  sectors  payload-bytes  file  channel  type
coding`, with the payload Form-sized rather than the directory's Form 1 fiction.

```
letters     65   laserlords 256   link       128   origami    323
apprentice  97   merlin      54   soccer     146   burncycle   19
                                                   total    1,088
```

**1,088 stream records over eight discs, against 345 file records, and fourteen
cross-disc crossings against two.**

```
4,620,112 B   laserlords /themes.rtf ch0 pad  ==  origami .../pinguin.rtf ch0 pad
  729,736 B   link /bumper.rtf ch0 pad        ==  apprentice /CMDS/philips.rtf ch0 pad
  495,012 B   letters + laserlords  ntsc_bumper ch15 video 0x00
  446,208 B   letters + laserlords  ntsc AND pal ch15 audio 0x10  (4 copies)
  432,264 B   letters + laserlords  pal_bumper  ch15 video 0x00
  346,276 B   link + apprentice + MERLIN       ch15 audio 0x01   <== new
  281,204 B   link + apprentice                ch15 video 0x04
  225,428 B   letters + laserlords  pal_bumper  ch16 video 0x00
  197,540 B   letters + laserlords  ntsc_bumper ch16 video 0x00
   92,960 B   link + apprentice                ch16 video 0x05
    2,048 B   x4  two bumper descriptors, one bumper data sector, path_tbl
```

Three things about that, and they are the argument for the list.

**It reproduces the seventh session's headline result without being told to.**
The 1991/92 bumper pair — eight streams across Letters and Laser Lords — was
found by hand, two `cdirtf.py hash` outputs read side by side. Here it falls out
of one command over eight discs.

**Two of the fourteen are padding and must be filtered before any total is
quoted.** `themes.rtf` and `pinguin.rtf` share 4,620,112 bytes because both are
1,988 sectors of zero. Of the 7,874,932 bytes the list reports as shared,
**5,349,848 are zeroes agreeing with zeroes** and the real figure is 2,525,084.

**And it found the crossing in §5b** — the Philips bumper's audio inside Merlin's
`anims.rtf` — which five sessions of file-level comparison could not see, because
the two files it lives in do not resemble each other at all.

**Publish both lists.** They answer different questions and the second one
answers the question this platform actually poses.

**One caveat when comparing across branches.** A CD-i record's size field is
the directory's declared size, which on Form 2 understates the payload by
13.48 %. A PC branch record's size is the file's length. Two records with the
same sha1 *and* the same size mean the same thing on both branches; a size on
its own does not.
