# Changelog

## Unreleased

### What's new

- fix(profiles): correct inverted AV1 exclusion (was keeping only AV1 instead of excluding it).
- docs(profiles): clarify German Usenet profile keeps streams containing German audio, other languages allowed.

## 1.4.0 (2026-09-29)

### What's new

- All profiles now exclude **AV1** encodes (`negate(encode(streams, 'AV1'), streams)`)
  as none of the clients support AV1.

## 1.3.0 (2026-09-29)

### What's new

- Added the **Japanese + Korean + Chinese/Mandarin Usenet** profile.
- Japanese, Korean, Chinese or Mandarin audio (both `Chinese` and `Mandarin` tags matched), English or German subtitles required, HDR over SDR.
- Resolution limited to 2160p / 1080p / 720p (720p only when fewer than 5 x 1080p results).

### What's new

- Added the **Russian + Ukrainian Usenet** profile.
- Russian or Ukrainian audio, HDR over SDR.
- Resolution limited to 2160p / 1080p / 720p (720p only when fewer than 5 x 1080p results).

## 1.1.0 (2026-08-14)

### What's new

- Added the **German Usenet** profile.
- German-only audio, HDR over SDR.
- Resolution limited to 2160p / 1080p / 720p (720p only when fewer than 5 x 1080p results).

## 1.0.0 (2026-08-14)

### What's new

- Initial release with the **German + English Usenet (Dual Audio)** profile.
- SEL-driven filtering keeps only Usenet releases with German and/or English audio.
- Priority order: Dual Audio + HDR > Dual Audio + SDR > Single Audio + HDR > Single Audio + SDR.
- Resolution limited to 2160p / 1080p / 720p (720p only when fewer than 5 x 1080p results).
