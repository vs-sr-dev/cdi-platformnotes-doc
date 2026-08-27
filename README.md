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
| 1 | Opening the image raw, sector layout, submode bits, capacity |
| 2 | The pre-file-system region — the highest-yield first move |
| 3 | Green Book volume descriptor, path table, directory records |
| 4 | Censusing the disc before reading any of it |
| 5 | OS-9/68000 modules, parity, CRC, toolchain fingerprints |
| 6 | Symbol tables — `.stb` files and loose name strings — string cross-referencing, Microware string storage |
| 7 | Coding bytes, DYUV, bitmaps and proving a geometry, run-length codings, fonts |
| 8 | Green Book ADPCM, wrapped and raw, and the duration arithmetic |
| 9 | Real-time files: channels, records, triggers, and why discs are half empty |
| 9b | Tagged chunks, false positives, code hiding in data, and validating a container by chaining it |
| 10 | Baselines for four discs, side by side |
| 11 | The order of work that worked |

Findings confirmed on every disc so far are marked **[all]**; those confirmed on
fewer are marked **[N of 4]**. Everything else is named after the disc it came
from, and is the kind of thing to test rather than assume.

## Discs it is drawn from

| Disc | Year | What it is |
|---|---|---|
| [Ultra CD-i Soccer](https://github.com/vs-sr-dev/cdi-ultracdisoccer-doc) | 1997 | Krisalis / Philips, UK — a game: 144 small files, raw CLUT bitmaps, one MPEG-1 intro, 2.4 % of a CD used |
| [Origami](https://github.com/vs-sr-dev/cdi-origami-doc) | 1993 | EagleVision, Netherlands — a presentation: 46 files, 98 % of a CD, five narration languages, no bitmaps at all |
| [Link: The Faces of Evil](https://github.com/vs-sr-dev/cdi-linkthefacesofevil-doc) | 1993 | Animation Magic / Philips Interactive Media of America, USA — a streaming game: 14 files in a flat root, 77 % of a CD, half of it deliberately empty |
| [Merlin's Apprentice](https://github.com/vs-sr-dev/cdi-merlinsapprentice-doc) | 1995 | Philips Interactive Media of America, USA — a puzzle game on a third-party runtime: 18 files in a flat root, 39.5 % of a CD, and the linker's symbol file left in the root |

The four bracket the format: one disc where the program owns its assets, one
where it owns nothing and streams every pixel, one hybrid that streams its level
code as well as its pictures, and one that owns everything but keeps it inside a
single 8 MB container it did not write itself.

## The result that made this repository worth splitting out

Sectors in front of the file system belong to no file, and on all four discs
they descramble to real recorded audio. On **three** of them — different
developers, different publishers, different continents, and now two years apart
— that audio is **byte-identical**:

```
Link     1993   sectors 19-2268   5,229,000 B   md5 a0ed87f2e98b43f91281d16390fb178b
Origami  1993   sectors 18-2267   5,229,000 B   md5 a0ed87f2e98b43f91281d16390fb178b
Merlin   1995   sectors 19-2268   5,229,000 B   md5 a0ed87f2e98b43f91281d16390fb178b
```

29.6 seconds at 44,100 Hz, two takes, a fundamental drifting around 170 Hz. It
is an artefact of the CD-i authoring chain, not title content, and it was still
being written onto masters in January 1995. The 1997 disc carries a different
recording in the same place.

No pipeline could have found that alone. **Hashing the head region of a new disc
against that MD5 is a thirty-second check**, it has paid off three times, and it
is exactly the kind of thing that has to live in one place to be useful.

Underneath it, the mechanism: the authoring system wrote sector headers over a
CD-DA stream, destroying **seven stereo frames — 28 bytes — per sector**
(sync + header + subheader, plus four bytes zeroed at the EDC position). Section
2 has the measurement.

## Contributing from a pipeline

When a new title turns up something about the *format* rather than the title:

1. Add it to the relevant section here rather than to the title's repository.
2. Mark it **[all]** or **[2 of N]** only if you have actually checked the other
   discs. Otherwise name the disc it came from.
3. If it contradicts what is already written, **correct the text and say so in
   place** — there are several such corrections in sections 2, 5 and 7a, and all
   of them are more useful with the history attached than without.
4. Update the baseline table in section 10 and the order of work in section 11.

State what is measured and what is inferred, and keep the measurements in the
document. Half of what makes a disc interesting is the list of things that are
measurably odd and not yet explained.
