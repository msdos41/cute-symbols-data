# Cute symbols data

Open data behind [CopyCute](https://copycute.com), a free website of cute symbols, kaomoji and fancy Unicode text to copy and paste.

It has every item listed on the site, which common fonts contain each character, which emoji Windows 10 can't draw, and the bio length limits of a few social apps, each with its source.

I collected this while building the site, because I kept needing the same answers: does this character exist in common fonts, will this emoji show as an empty box on Windows 10, how long can a TikTok bio be. If you're building a picker, a bot or a small text tool, it might save you some time.

## Files

| File | Rows | What's in it |
|---|---|---|
| `data/items.csv` | 2,811 | Every item you can copy on copycute.com: the text, its code points, the page and section it's listed under, and any emoji in it that Windows 10 can't draw |
| `data/characters.csv` | 1,738 | Every distinct non-ASCII character used on the site, with its Unicode name and which of 8 reference fonts has a glyph for it |
| `data/windows10-missing-emoji.csv` | 1,204 | Emoji sequences that Windows 10 can't draw: Emoji 12.1 and later, plus all 262 flags. Built from Unicode `emoji-test.txt` 18.0 |
| `data/platform-limits.json` | 5 | Bio character limits for Instagram, TikTok, X and Discord, with the official help page and the date it was checked |
| `data/font-styles.csv` | 24 | The 24 text styles of the [cute font generator](https://copycute.com/cute-font-generator/), with a sample and its code points |

Some items in `items.csv` span several lines (dot art, bio layouts). They're quoted, so any CSV reader handles them.

## How it was checked

**Font coverage (`characters.csv`).** A script reads each font's character map with fontTools and checks every character against DejaVu Sans, Noto Sans, Noto Sans Symbols, Noto Sans CJK JP, Liberation Sans, Noto Sans Math, Sarabun and Noto Color Emoji. In the latest run, all 1,738 characters had a glyph in at least one of them.

That only shows a character exists in a common font. It doesn't prove that a given phone, app or game draws it. I haven't tested every device, so treat this as a first filter.

**Windows 10 emoji (`windows10-missing-emoji.csv`).** Windows 10's last emoji update was the May 2019 Update (version 1903), which added Emoji 12.0. Later emoji only reached Windows 11 ([Emojipedia: Microsoft](https://emojipedia.org/microsoft)). One exception: the Emoji 12.1 skin-tone versions of "people holding hands" did reach Windows 10, so they're left out. Windows has no flag emoji at all and shows two letters instead, so every flag is listed.

**Bio limits (`platform-limits.json`).** Each limit links to the platform's own help page or announcement, with the date I last opened it. The 160-character TikTok bio is reported for some accounts, but I couldn't find it in TikTok's help pages, so it has no official source.

## Source pages

The data comes from these pages on [CopyCute](https://copycute.com): [kaomoji](https://copycute.com/kaomoji/), [cute symbols](https://copycute.com/cute-symbols/heart/), [aesthetic symbols](https://copycute.com/aesthetic-symbols/), [cute font generator](https://copycute.com/cute-font-generator/) and [aesthetic bio maker](https://copycute.com/aesthetic-bio-maker/).

## Example

```python
import csv

with open("data/items.csv", encoding="utf-8") as f:
    hearts = [r["text"] for r in csv.DictReader(f) if "heart" in r["page"].lower()]
print(len(hearts), hearts[:10])
```

```python
import csv

# Which items contain an emoji that Windows 10 shows as a box?
with open("data/items.csv", encoding="utf-8") as f:
    risky = [r for r in csv.DictReader(f) if r["win10_missing_emoji"]]
print(len(risky), sorted({r["win10_missing_emoji"] for r in risky}))
```

## License

The data is released under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/) (full text in [LICENSE](LICENSE)). You can use, share and adapt it, including commercially, as long as you give credit.

Attribution: **Data from CopyCute (https://copycute.com)**

Code points, character names and emoji versions come from the Unicode Character Database and `emoji-test.txt` ([Unicode License](https://www.unicode.org/license.txt)). The characters themselves are standard Unicode; what this repo adds is the selection, grouping and checks.

## Corrections and updates

Found a wrong limit, or a symbol that shows as a box on your device? Open an issue, or email hello@copycute.com with the details.

The files are exported from the site's source. Last export: 2026-10-27.

Made by [msdos41](https://x.com/msdos41_).
