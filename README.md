# cdi-platformnotes-doc

**The canonical CD-i platform checklist**, carried from one documentation
pipeline to the next and added to by each.

→ **[cdi-platform-notes.md](cdi-platform-notes.md)**

Each Philips CD-i title I document produces two things: a repository about that
disc, and whatever it taught me about the *format*. The second kind of finding
does not belong to any one title, and keeping a copy of it in every pipeline is
a recipe for three copies that disagree. This repository is the single copy.
Pipelines link here rather than fork it.

## What it covers

A working checklist for opening an unfamiliar CD-i disc: what to look at first,
what the numbers should be, which traps cost real time, and what is measured
versus what is inferred. It is written to be read in order the first time and
grepped afterwards.

| Section | |
|---|---|
| 1 | Opening the image raw, sector layout, submode bits, capacity, **CD-i Ready** |
| 2 | The pre-file-system region — the highest-yield first move |
| 3 | Green Book volume descriptor, path table, directory records |
| 4 | Censusing the disc before reading any of it |
| 5 | OS-9/68000 modules, parity, CRC, toolchain fingerprints |
| 5b | The publisher bumper as a shared, byte-identical asset |
| 6 | Symbol tables — `.stb` files and loose name strings — string cross-referencing, Microware string storage |
| 7 | Coding bytes, DYUV, bitmaps and proving a geometry, run-length codings, fonts |
| 8 | Green Book ADPCM, wrapped and raw, and the duration arithmetic |
| 9 | Real-time files: channels, records, triggers, and why discs are half empty |
| 9b | Tagged chunks, false positives, code hiding in data, and validating a container by chaining it |
| 10 | Baselines for eight discs, side by side |
| 11 | The order of work that worked |
| 12 | The eight `sha1-all.txt` lists, the eight `streams.txt` lists, and what a file-level hash cannot see |

Findings confirmed on every disc so far are marked **[all]**; those confirmed on
fewer are marked **[N of 8]**. Every mark now reads `of 8`: the eighth pipeline
re-derived the ones its disc exercises and **re-declared in place**, with a note
saying so, the ones it cannot. **Four claims did not survive that pass** and are
corrected where they stand — §2's argument from a computed EDC, §5b's "Merlin
ships no bumper", §9's reading of the `TRIGGER` bit, and §9's low-padding rule. Everything else is named after the disc it came
from, and is the kind of thing to test rather than assume.

## Discs it is drawn from

Every disc below has its own repository, and the full write-up for each one lives in
the family index: **[cdi-gamelist-doc](https://github.com/vs-sr-dev/cdi-gamelist-doc)**. The table here stays
the short form; the index is where the prose is.

| Disc | Year | What it is |
|---|---|---|
| [Ultra CD-i Soccer](https://github.com/vs-sr-dev/cdi-ultracdisoccer-doc) | 1997 | Krisalis / Philips, UK — a game: 144 small files, raw CLUT bitmaps, one MPEG-1 intro, 2.4 % of a CD used |
| [Origami](https://github.com/vs-sr-dev/cdi-origami-doc) | 1993 | EagleVision, Netherlands — a presentation: 46 files, 98 % of a CD, five narration languages, no bitmaps at all |
| [Link: The Faces of Evil](https://github.com/vs-sr-dev/cdi-linkthefacesofevil-doc) | 1993 | Animation Magic / Philips Interactive Media of America, USA — a streaming game: 14 files in a flat root, 77 % of a CD, half of it deliberately empty |
| [Merlin's Apprentice](https://github.com/vs-sr-dev/cdi-merlinsapprentice-doc) | 1995 | Philips Interactive Media of America, USA — a puzzle game on a third-party runtime: 18 files in a flat root, 39.5 % of a CD, and the linker's symbol file left in the root |
| [The Apprentice](https://github.com/vs-sr-dev/cdi-theapprentice-doc) | 1994 | The Vision Factory, Netherlands — a platform game on a **CD-i Ready** disc: the whole volume hides in track 1's pregap, 74 % of the pressing is Red Book audio, and the soundtrack ships a second time as ADPCM |
| [Laser Lords](https://github.com/vs-sr-dev/cdi-laserlords-doc) | **1992** | Spinnaker Software / American Interactive Media, published by Philips Interactive Media of America, USA — 33 files in a flat root, 87 % of a CD, and **ten jukebox streams carrying 6 h 34 m of speech on sixteen parallel channels** |
| [Burn:Cycle](https://github.com/vs-sr-dev/cdi-burncycle-doc) | **1995-05** | Trip Media / Philips Media, UK — an interactive film: **7 directory entries**, 94.5 % of a CD, **99.23 % of the volume in one real-time file**, a 69-minute RL7 picture at 384 × 240 and 12.5 fps, 77:49 of ADPCM the pressing tags as *video*, 353 compiled 68000 script objects interleaved with the film, **0.68 % padding**, and the only Italian-language disc here |
| [A Visit to Sesame Street: Letters](https://github.com/vs-sr-dev/cdi-avisittosesamestreetletters-doc) | **1991** | American Interactive Media, USA — the oldest disc here by eleven and a half months: 14 files in a flat root, 86 % of a CD, **no text file of any kind**, four painted 640-pixel rooms with a hot-spot table over them, and **13,138 DYUV frames of alphabet cartoons decoded at 172 × 108** |

The eight bracket the format: one disc where the program owns its assets, one
where it owns nothing and streams every pixel, one hybrid that streams its level
code as well as its pictures, one that owns everything but keeps it inside a
single 8 MB container it did not write itself, one that is not a data disc at
all, one from a year before any of them that had already finished the streaming
idiom the others use, one from a year before *that* which is not a game —
four rooms that sit still until a three-year-old points at something — and one
that is not software with content in it at all, but a film with a program
inside it.

## The result that made this repository worth splitting out

Sectors in front of the file system belong to no file, and on all eight discs
they resolve to real recorded audio. On **five** of them — different
developers, different publishers, different continents, three and a half years
apart — that audio is **byte-identical**:

```
Letters      1991   sectors 19-2268   5,229,000 B   md5 a0ed87f2e98b43f91281d16390fb178b
Laser Lords  1992   sectors 19-2268   5,229,000 B   md5 a0ed87f2e98b43f91281d16390fb178b
Link         1993   sectors 19-2268   5,229,000 B   md5 a0ed87f2e98b43f91281d16390fb178b
Origami      1993   sectors 18-2267   5,229,000 B   md5 a0ed87f2e98b43f91281d16390fb178b
Merlin       1995   sectors 19-2268   5,229,000 B   md5 a0ed87f2e98b43f91281d16390fb178b
```

29.6 seconds at 44,100 Hz, two takes, a fundamental drifting around 170 Hz. It
is an artefact of the CD-i authoring chain, not title content, and the window it
covers is **1991-09-13 to 1995-01-31**. The 1997 disc carries a different
recording in the same place, **The Apprentice, in 1994, carries a third**
(`4e61f608e1f1455d9ad5b2a0615dbbd3`) and **Burn:Cycle, in May 1995, a fourth**
(`b80d0c314bd303bb9c21495fcdf41975`) — so date does not predict which one you
get, and the two 1995 discs four months apart carry different ones. The
Apprentice writes its block **twice**, head and tail.

On Laser Lords the identity does not stop at the region: **2,269 of the first
2,270 sectors are byte-identical to Link's, and the one that differs is the
volume descriptor.**

No pipeline could have found that alone. **Hashing the head region of a new disc
against those MD5s is a thirty-second check**, it has now answered the question
outright seven times, and it is exactly the kind of thing that has to live in one
place to be useful. One warning that cost the sixth pipeline's briefing its
headline: **those MD5s are of the descrambled audio, not of the bytes in the
image.** Compare like with like, or the answer is always "a new recording".

A second check of the same shape has since turned up, and the seventh pipeline
doubled it: the **publisher's bumper** that opens a licensed disc is a finished
asset handed out as a file, and it is pressed verbatim. There are **two
generations**, one on two discs and one on **three**. 714,240 bytes of the 1993 one — RL7 video,
Level B stereo audio and one 384 × 240 DYUV picture — are byte-identical
between a 1993 disc made in the USA and a 1994 disc made in the Netherlands.
The eighth pipeline's stream-level hash list then found the same
audio track a third time, inside **Merlin's** `anims.rtf`, which five sessions of
file-level comparison could not see. And **2,246,756 bytes of the 1991–92 one — eight streams, CLUT4 video on two
channels, Level A audio and a palette sector — are byte-identical between a
September 1991 disc and a September 1992 one**, invisible to every file-level
hash list because the two files differ: the older one adds a channel carrying a
384 × 240 licensor's card. Section 5b has all sixteen hashes.

Underneath it, the mechanism: the authoring system wrote sector headers over a
CD-DA stream, destroying **six stereo frames — 24 bytes — per sector** on the
four discs carrying the 1991–95 recording (sync + header + subheader) and
**seven frames — 28 bytes — on the two that carry their own**. *Three revisions
of this document said seven for all of them; the sixth pipeline settled the
first group with a control the document had never run — and the eighth found
that Burn:Cycle loses seven frames while carrying 2,250 correctly computed EDCs,
which kills the argument that a computed EDC proves those four bytes were never
audio. The loss tracks the recording; the EDC setting tracks nothing.* Section 2
has both measurements and both controls.

And a third check of the same shape, in section 12: **every disc repository
publishes `notes/sha1-all.txt`** — **345 records over eight discs** — **and, from
the eighth pipeline, `notes/streams.txt` beside it: 1,088 records, one per
`(file, channel, type, coding)` run.** The file-level list finds two crossings,
one of which is a ten-byte path table on five discs. The stream-level list finds
**fourteen**, including the Merlin bumper audio nobody had found, and it drops
the shared-and-invisible total from 7,475,756 bytes to 5,703,096 — of which
5,229,000 is the filler recording, which belongs to no file on any disc. On this
platform the unit of sharing is the *stream* and the *pre-file-system region*,
not the directory entry.

## Contributing from a pipeline

When a new title turns up something about the *format* rather than the title:

1. Add it to the relevant section here rather than to the title's repository.
2. Mark it **[all]** or **[N of 8]** only if you have actually checked the other
   discs. Otherwise name the disc it came from. **And move every existing mark
   onto the new denominator** — re-derive the ones your disc exercises and
   re-declare the rest in place with a note. Two sessions of postponing a mark
   turn it into furniture.
3. If it contradicts what is already written, **correct the text and say so in
   place** — there are several such corrections in sections 2, 5 and 7a, and all
   of them are more useful with the history attached than without.
4. Update the baseline table in section 10, the order of work in section 11,
   and publish `notes/sha1-all.txt` for your disc (section 12).

State what is measured and what is inferred, and keep the measurements in the
document. Half of what makes a disc interesting is the list of things that are
measurably odd and not yet explained.
