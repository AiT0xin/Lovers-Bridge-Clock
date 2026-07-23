# Lovers' Bridge — retrograde clock

A live SVG wristwatch dial with a **double retrograde** complication, synced to
atomic time. Two figures walk toward each other along the arch of a bridge: the
lady reads the hours, the gentleman the minutes. They meet at the crest at noon
and midnight, kiss, and both fly back to the far ends of the span.

Single self-contained file — `index.html`. No build step, no dependencies, no
network assets. Open it directly or serve the folder.

## How the complication works

| Time | Lady (hours) | Gentleman (minutes) |
|---|---|---|
| 00:00:00 | far left | far right |
| 06:30:00 | mid sweep | mid sweep |
| any :57 → :60 | drawn to the crest | drawn to the crest — **they kiss** |
| 11:59:59 | at the crest | at the crest |
| 12:00:01 | flies back to far left | flies back to far right |
| 12:59:59 | still climbing | at the crest |
| 13:00:01 | unchanged | flies back alone |

So the minute hand retrogrades every hour on the hour; the hour hand retrogrades
only at noon and midnight, and that is the one moment both jump together.

Both flybacks are drawn as a real spring snap (~420 ms, eased), not a glide
backwards — a retrograde hand never travels its scale in reverse.

**The kiss** runs over the last three minutes of the hour. It fades in from
`:57`, is fully held by `:58.2`, and stays until the retrograde fires at `:60`.
Pressed by hand it lasts exactly 5 s (0.7 s in, 5 s held, 0.7 s back).

## Geometry

Everything on the dial is derived from three fitted curves, so the parts stay in
proportion at any size:

- **Deck arc** — circle centred `(970, 1411)`, radius `531` in the 1920² viewBox.
  The figures' feet ride this; it is also the bridge's top surface.
- **Numeral curve** — a flatter parabola, `y = 658 + 118·t²`, `x = 970 ± 242·t`.
  It is deliberately *not* concentric with the deck, which is what gives the
  lovers increasing headroom as they climb toward the crest.
- **Cant** — figures tilt at `0.22×` the arch normal. A full cant is
  mechanically honest but swings the umbrella and the raised top hat off the
  dial at the retrograde extremes.

`t` runs `1` at the outer end of a scale to `0.10` where the two runs almost
meet under the moon. Both the numerals and the figure that points at them are
driven from the same `t`, so the indication can never drift from its scale.

## Atomic time

Three independent sources are sampled and voted on, best round-trip wins:

1. `worldtimeapi.org`
2. the origin's own HTTP `Date` header, refined by watching for the second
   rollover across rapid HEADs (sub-second precision from a second-accurate
   source)
3. `timeapi.io`

A source is accepted only if a second source agrees within 1.5 s; otherwise the
origin header is preferred. Re-syncs every 10 minutes, with exponential backoff
on failure. If every source fails it falls back to the device clock and says so.

The tab title carries the state — `20:41:03 — atomic · origin http`,
`— device clock`, `— syncing…`, or `— winding` — so the dial itself stays clean.

## Controls

- **Touch the bridge** — at any time at all, the two snap together and kiss for
  5 s, then drift slowly back to wherever the time has moved on to (snappy in,
  gentle 2.4 s return). The clock never stops, skips, or runs fast: only the
  figures are borrowed, for ~7.7 s. The crown is decorative and not interactive.
- **Triple-click anywhere** — toggles the dark backing, same gesture and
  persistence as the other clocks. Notion embeds this in an iframe with no way
  to pass its theme in, and its sandbox can silently block `localStorage`, so
  the setting is written to `localStorage` *and* a cookie, each wrapped so a
  throw in one doesn't stop the other.
- `?at=2026-07-23T11:59:57` — freeze the dial at an arbitrary instant.

Dial only — no strap, no lugs, no caption. The SVG viewBox is cropped to the
case plus the crown, so it drops straight into an embed.

## Note on branding

The dial carries no maker's name or model signature. The complication and the
scene are reproduced; the trademarks are not.
