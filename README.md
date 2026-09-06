# Grabar Archive open data

Two datasets from [tsavdtanem.org](https://tsavdtanem.org), a reader over machine
working drafts of public-domain Armenian print.

1. **Bedrossian 1875-79**, 34,557 headwords, each tied to the scan page it came
   from and a URL that opens that page.
2. **An OCR agreement benchmark** over 2,053 pages of eleven Armenian imprints,
   1668-1905, against Calfa's open `hye-open-ocr` recognizer.

Both are offered for correction and reuse. If something here is wrong, open an
issue. Every row carries the page image that settles it.

Everything is derived from public-domain sources. Nothing here is a citation:
these are machine drafts, and the page image is the authority.

---

Three files, under `data/`. Method and known limits below.

## 1. `bedrossian_page_anchors.tsv` (34,557 entries)

Every headword of Bedrossian, *New Dictionary Armenian-English* (Venice 1875-79),
machine-digitized and image-reconciled, with the scan page it came from and a
resolvable URL for that page.

| column | meaning |
|---|---|
| `headword_as_printed` | the headword exactly as set on the page, abbreviations intact |
| `expanded_form` | the word the abbreviation stands for, where one applies (661 rows) |
| `glosses` | Bedrossian's English, semicolon-separated |
| `source_page` | our scan-page id |
| `image_verified` | 1 where two independent readings agreed or an adjudication confirmed it (34,145 of 34,557) |
| `scan_url` | the Internet Archive page image, openable |

The point of this file is provenance. It is one lexicon, kept separable, with every
entry traceable to a page you can open.

**Two abbreviation families** are expanded in `expanded_form`:

- `-ութ` + `ի` carrying the overbar abbreviation stroke -> `-ութիւն` (523 headwords)
- `Ած-` -> `Աստուած-` (135 headwords)

`Ած-` is ambiguous: `Ածական` "adjective" and `Աստուածական` "divine" are both real
words and Bedrossian prints both under `Ածական`. Expansion there is per entry and
only on evidence (external reference, or the entry's own gloss). 2 rows were left
unexpanded on purpose.

## 2. `calfa_candidate_gaps.tsv` (4,329 candidates)

Headwords in Bedrossian that we could not find in the published Calfa databases
(`definitions/en`, `definitions_proper/en`, `synonyms`, 51,697 normalized forms).
Restricted to image-verified rows, continuation and suffix stubs dropped.

**These are candidates, not confirmed gaps.** We sampled 110 of them against the
live dictionary at calfa.fr: **59 were absent (53.6%, 95% CI 44-63%)**. So the
file should contain roughly 1,900-2,700 real additions, mixed with entries the
live database already holds but the published dumps do not.

Measured separately, the rate is the same in the geographic/proper-name appendix
(19/35) as in the dictionary body (20/35), so the misses are not concentrated in
one section.

Probe controls: 10/10 words we predicted present came back present, 3/3 nonsense
strings came back absent.

Separately, we checked that the candidates are **real printed entries** rather
than digitization artefacts, by testing whether each one's English gloss appears
in the Internet Archive's own OCR of the page we say it came from. 193 of 200
confirmed (96%), against a coincidence baseline of 8-10% when the same test is
run against a page 30 or 60 leaves away. The 7 that failed are words that OCR
mangles (`affghanistan`, `themistocles`, `scenography`), not absent entries.

So of the 4,329 candidates, about 96% are genuine Bedrossian headwords and about
54% of those are missing from Calfa: roughly 2,200 real additions.

## 3. `ocr_agreement_by_page.csv` (2,053 pages)

`hye-calfa-n` (Calfa's open Tesseract 5 model, CPU, no layout stage) run over every
transcribed page of eleven Armenian imprints, 1668-1905, and compared against this
archive's own dual-model vision transcription.

**Neither side is ground truth.** This is agreement, not accuracy. It locates
disagreement. It does not grade either system. Comparison is on Armenian letters
only, NFC-normalized with `և` folded to `եւ`, so punctuation and reading-order
conventions do not pollute the figure. 66 pages were dropped for having under 40
characters on our side.

Median CER by book:

| book | year | pages | median CER | median length ratio |
|---|---|---|---|---|
| The Fox Book | 1668 | 300 | 0.066 | 0.98 |
| Songbook (Yergaran) | 1737 | 219 | 0.208 | 1.09 |
| Selected Fables of Vardan | 1825 | 50 | 0.362 | 0.68 |
| A Training in Manners | 1829 | 142 | 0.076 | 0.98 |
| Book of Hours (Žamagirkʿ) | 1848 | 733 | 0.050 | 1.00 |
| Almanac for 1853 | 1852 | 91 | 0.855 | 0.16 |
| Refutation of Nonsense-Talk | 1861 | 81 | 0.235 | 0.85 |
| Armenian National Constitution | 1863 | 58 | 0.546 | 0.54 |
| The Home Doctor | 1863 | 143 | 0.188 | 0.93 |
| A Thousand and One Games I | 1904 | 69 | 0.279 | 0.77 |
| A Thousand and One Games II | 1905 | 101 | 0.201 | 0.84 |

Corpus median 0.105. 48% of pages under 0.10, 29% under 0.05.

The length ratio separates two different failures. Where it sits near 1.00 the
recognizer read the page and the residual is character-level (1668 at 0.066 over
300 pages, 1848 at 0.050 over 733). Where it falls well below 1 the recognizer
returned only part of the page, which is layout, not recognition, the 1852
almanac (0.16) is calendar tables, the 1863 constitution (0.54) is two-column
bilingual. We ran no layout stage, so those rows are a floor, not a verdict.

The one genuinely hard case is the 1737 Songbook: ratio 1.09 with CER 0.208, so
the page was read and over-generated. That typography looks like the real gap.

Alignment is page-level, not line-level. As it stands this is an evaluation set,
not training data.

---

## Provenance and limits

- Bedrossian's headwords and glosses are public domain (Venice, 1875-79). The
  digitization is machine work, reconciled against the page image, and carries
  the error rate any such pipeline does. `image_verified` marks the 34,145 rows
  where two independent readings agreed or an adjudication settled it.
- The OCR benchmark measures **agreement between two machine systems**, neither
  of which is ground truth. Do not read it as an accuracy score for either.
- Alignment is page-level, not line-level.

## License

The data in `data/` is released under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).
Use it for anything, commercial included. Attribution: Grabar Archive, Steps Ventures,
tsavdtanem.org.

Bedrossian's own text (Venice 1875-79) is public domain. The license covers this
digitization of it.

## Credits

`hye-calfa-n` and `hye-open-ocr` are by [Calfa](https://calfa.fr) (CC BY-NC 4.0)
and are used here only to measure agreement. The Calfa lexical databases
(CC BY-NC 4.0) were used as an external reference for checking abbreviation
expansions and for the gap list. Page images of the eleven imprints come from the
British Library's Endangered Archives Programme, collection EAP180. Bedrossian
page images come from the Internet Archive.

## Contact

mike@stepsventures.com
