# Profile: Japanese + Korean + Chinese/Mandarin Usenet

A SEL-driven filtering and sorting profile for [AIOStreams](https://github.com/Viren070/AIOStreams).
It keeps **only Usenet releases** with **Japanese, Korean, Chinese or Mandarin** audio,
requires **English or German subtitles**, and prefers **HDR** over SDR,
excluding **AV1** encodes.

## Requirements

| Rule | Behaviour |
| --- | --- |
| Audio | Streams with **Japanese, Korean, Chinese or Mandarin** audio |
| Subtitles | **English or German** subtitles required |
| Stream type | **Usenet only** |
| Resolution | Only **2160p, 1080p, 720p** |
| 720p gating | 720p shown **only when fewer than 5 x 1080p** streams exist |
| HDR | Allowed and **preferred over SDR** |
| Encode | **AV1 excluded** (unsupported by clients) |

## Priority order

Streams are tiered in the following order (highest first). Each stream matches only the
**first** Preferred Stream Expression it satisfies.

1. **CJK + HDR**
2. **CJK + SDR**

## How it works

### Excluded Stream Expressions (hard filters)

| Expression | Purpose |
| --- | --- |
| `negate(type(streams, 'usenet', 'stremio-usenet'), streams)` | Remove every non-Usenet stream |
| `negate(resolution(streams, '2160p', '1080p', '720p'), streams)` | Remove every resolution except 2160p / 1080p / 720p |
| `negate(merge(language(streams, 'Japanese'), language(streams, 'Korean'), language(streams, 'Chinese'), language(streams, 'Mandarin')), streams)` | Remove streams with none of the CJK audio languages |
| `negate(merge(subtitle(streams, 'English'), subtitle(streams, 'German')), streams)` | Remove streams with neither English nor German subtitles |
| `encode(streams, 'AV1')` | Remove AV1 encodes (unsupported by clients) |
| `count(resolution(streams, '1080p')) >= 5 ? resolution(streams, '720p') : []` | Remove 720p results when there are already 5+ x 1080p results |

### Preferred Stream Expressions (priority tiers)

- **CJK audio** is expressed by merging the four language filters:
  `merge(language(streams, 'Japanese'), language(streams, 'Korean'), language(streams, 'Chinese'), language(streams, 'Mandarin'))`.
  Both `'Chinese'` and `'Mandarin'` are matched because different indexers label
  Chinese audio tracks differently.
- **HDR** is any of `HDR`, `DV`, `HDR10`, `HDR10+`, `HLG`; **SDR** is its complement.
- A stream with an unknown/missing visual tag is treated as SDR.

### Sort

`sortCriteria.global` sorts by `streamExpressionMatched` (the priority tier), then
`resolution` (2160p → 1080p → 720p), then `quality`.

## Files

- `../../templates/japanese-korean-chinese-usenet.json` — full importable template (metadata + config)
- `../../sel/japanese-korean-chinese-usenet/excluded-stream-expressions.json` — ESE array (synced-URL format)
- `../../sel/japanese-korean-chinese-usenet/preferred-stream-expressions.json` — PSE array (synced-URL format)
- `../../sel/japanese-korean-chinese-usenet/ranked-stream-expressions.json` — RSE array (empty)

## Customising

- **HDR tags**: add or remove tags from the `visualTag(...)` lists in the PSE expressions
  (e.g. remove `'DV'` if your device has no Dolby Vision support).
- **720p threshold**: change `5` in the last ESE expression.
- **AV1 exclusion**: remove the `encode(streams, 'AV1')` ESE
  if your clients gain AV1 support.
- **Subtitle languages**: extend the subtitle ESE merge with additional languages,
  e.g. `subtitle(streams, 'French')`, or drop the subtitle ESE entirely if subtitles
  should be optional.
- **Single-language variant**: replace the four-way audio merge with a single
  `language(streams, 'Japanese')` (or `'Korean'` / `'Chinese'`) filter to focus on
  one language only.
- **Prefer 1080p over 2160p**: reorder the `resolution` sort direction or adjust the sort keys.
