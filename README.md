# Hanzi stroke data

The stroke-order data of **Hanzi: Write Chinese**, an iPad app for learning to
write Chinese by hand: the strokes of 9,574 characters (Simplified and
Traditional), in writing order, as the app draws, animates and scores them.

It is a modified version of [Make Me a Hanzi](https://github.com/skishore/makemeahanzi)'s
stroke data, which is derived from fonts by Arphic Technology and licensed under
the **Arphic Public License** ([ARPHICPL.TXT](ARPHICPL.TXT)). As that licence
asks, the modified data is published here, freely, under the same licence.

## Files

| File | What it is |
|---|---|
| [`data/hanzivg.xml`](data/hanzivg.xml) | Every character's strokes as SVG paths, in [KanjiVG](https://kanjivg.tagaini.net)'s XML format. The readable form: use this one. |
| [`data/strokes.kana`](data/strokes.kana) | The same strokes exactly as the app ships them (its binary format, below). |
| [`data/strokes-index.tsv`](data/strokes-index.tsv) | Where each character's strokes are in `strokes.kana`. |
| [`ARPHICPL.TXT`](ARPHICPL.TXT) | The Arphic Public License, unaltered. |

## What was changed, and when

Source: Make Me a Hanzi's strokes as packaged by
[hanzi-writer-data](https://github.com/chanind/hanzi-writer-data) 2.0.1 (which
fixes some cut-off strokes). Make Me a Hanzi gives each stroke an outline and a
median (its centreline: a few points in a 1024-unit box, y up).

Changed in October 2026 for Hanzi:

- Each stroke's median was turned into cubic Bézier curves through its points
  (smooth, but a sharp turn, such as a hook, stays a corner), in KanjiVG's
  109-unit box with y down. The outlines are not used.
- Each character was written as a KanjiVG `<kanji>` element: one `<path>` per
  stroke, in writing order (`-s1`, `-s2`, ...).
- Its components were added as KanjiVG group elements (`kvg:element`), from
  [BabelStone's IDS](https://www.babelstone.co.uk/CJK/IDS.HTML), which anyone may
  use for any purpose. These groups are not part of Make Me a Hanzi's data.
- For the app, the curves were stored in `strokes.kana` as 16-bit fixed point.

Both data files say so at their start (an XML comment; the `NOTE` chunk).

## `strokes.kana`

Little-endian throughout.

- Header (12 bytes): `"KANA"`, `u32` version (1), `u32` kind (`"STRK"`).
- Then chunks, each a `u32` tag, a `u32` size and that many bytes:
  - `NOTE`: a NUL-terminated UTF-8 text saying what this data is, how and when it
    was changed, and its licence.
  - `GEOM`: the strokes, character after character, stroke after stroke. Each
    stroke is a `u8` segment count, `u8` type, `u8` type variant, `u8`
    alternative type (these three are 0 here), `i16` start x, `i16` start y,
    then per segment six `i16`: the two control points and the end point of a
    cubic Bézier (`c1x c1y c2x c2y x y`). Coordinates are KanjiVG's 109-unit box,
    y down, times 64.

`strokes-index.tsv` gives each character's code point, the offset of its first
stroke in `GEOM`'s payload, and its stroke count.

## Licence

The data in this repository is licensed under the Arphic Public License
([ARPHICPL.TXT](ARPHICPL.TXT)), whatever the terms of the app that uses it.

Credits: Make Me a Hanzi by Shaunak Kishore; hanzi-writer-data by David Chanin;
the original fonts (AR PL KaitiM GB and AR PL UKai) by Arphic Technology Co.,
Ltd.; components from BabelStone's IDS by Andrew West.
