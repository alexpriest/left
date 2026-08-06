# left

How much time is left.

From the 2026-08-05 voice memo: the `Left` app's death countdown landed hard —
*"almost halfway. Which is rude. And a little bit of a wake-up call."* — and
Alex asked for a daily version of it. This is that number, in two places.

```
$ left
47y 187d left · 2,479 Saturdays · 44.1% spent

$ left --statusline
⧗ 47y 187d · 44.1%

$ left --long
47 years, 187 days left (to 2074-02-09, at 85).
   17,354 days · 2,479 weeks · 47 more summers.
   44.1% of the whole thing is behind you. 29.1% of your adult life.
```

## The bar

No emoji, anywhere. **`--statusline` carries a progress bar; nothing else does.**

```
  0%  ░░░░░        44.1%  ██▏░░        75%  ███▊░       100%  █████
```

Five cells with a partial eighth-block for the fractional cell — **41 distinct
states across a life**, so it visibly moves about once a year. Built entirely from
Block Elements (U+2580–259F), **all 32 of which JetBrains Mono maps**: zero
fallback, guaranteed metric-native.

**The daily line stays bare** — it reaches iMessage, where none of the font
reasoning below holds.

### The three-step lesson, kept because I got it wrong twice

**1. Existence is not the test — metrics are.** This first shipped `⧗` (U+29D7
BLACK HOURGLASS) on a cmap scan showing it present on this Mac: 3 fonts, Apple
Symbols / STIXGeneral / STIXTwoMath. It rendered, and sat visibly misaligned,
because all three are **proportional** and the terminal is monospace. Rescanning
monospace only: `⧗` is in **0 of 42**. No hourglass exists in any monospace font.

**2. I then over-generalised in the other direction.** I built a `○ ◔ ◑ ◕ ●` dial
on the theory that a fallback *can* be metric-correct, since Menlo and Andale Mono
carry the two glyphs JetBrains Mono lacks. **Tested, and it failed** — `◑` rendered
misaligned exactly like `⧗`. The rule is stricter than I kept trying to make it:

> **A fallback glyph is a misaligned glyph. Only use codepoints the terminal's own
> font actually maps.**

**3. ⚠️ I recorded a prediction as a verified result.** This file previously said
*"Two of the five dial glyphs are fallbacks and the row renders even — confirmed
visually."* Alex had said *"let's try B and see how it looks."* That is an
experiment, not a confirmation, and writing it up as one is the failure mode in
`feedback_critiquing_alex`. **Never log an untested expectation as a finding.**

The font fact itself held up under two independent checks (hand-rolled cmap parser,
then fontTools): JetBrains Mono Regular maps only **1,182 codepoints** and ships
**39 of the 96 Geometric Shapes** — every triangle and square, plus `◆ ◇ ◊ ◎ ● ◔ ◕
◯`, but **not one of the eight half-filled circles** (`◐ ◑ ◒ ◓ ◖ ◗` all absent).

### Alternative bars, all fully native

```
████░░░░           8-cell, whole blocks
███▌░░░░           8-cell, eighth precision  (64 states)
━━━━──────         10-cell heavy/light line, lower visual weight
▓▓▓▓░░░░           8-cell shade, softer contrast
[███░░░]           bracketed
```

Change `BAR_CELLS`, or swap `█`/`░` in `bar()`. Verify any new glyph is in
JetBrains Mono first — `~/.config/ghostty/config` names the font.

## Config

`~/.config/left/config.json`

```json
{"birthdate": "1989-02-09", "life_expectancy": 85, "adulthood_starts": 18}
```

Birthdate verified 2026-08-06 against three macOS Contacts sources (all agree:
1989-02-09).

**`life_expectancy` is 85.** The actuarial workup (2026-08-06) put the honest
point estimate at **86**, band 84–89 — but Alex set 85 deliberately, because
**that is the closest the `Left` app's own toggles can get and he wants the two
to agree.** Consistency across the two surfaces beat a one-year gain in accuracy,
which is the right call for an instrument whose job is to be looked at daily.
Do not "fix" it back to 86.

Full brief with sources: `Claude/Output/Ad Hoc/2026-08-06 Life Expectancy —
Actuarial Brief.md`. Two things worth carrying:

- **81 was not wrong, it was unpersonalised.** SSA's *cohort* table for the 1989
  male birth cohort gives 81.49 — the app is using a correct population baseline.
  The widely-quoted "78" is a *period* table, which assumes mortality freezes at
  2023 rates; that's a 3.25-year understatement at age 37 before anything else.
- **The personal adjustment is +5, not +10.** Income and fitness overlap by ~72%
  (Whitehall II) — stacking them naively double-counts one advantage.

Honest band is 84–89. Do not chase precision here; treat 86 as "mid-to-high 80s".

**The mean is not the interesting number.** Median is 89.5, p10 is 66, p90 is
102 — a 36-year spread. The countdown shows a mean because a countdown needs a
single number, not because a single number is true. An odds line (74% chance of
seeing 80, 48% of seeing 90) was proposed and **declined 2026-08-06 as too much
text for a status bar** — don't re-add it.

## Where it shows up

1. **Claude Code status bar** — `~/.claude/scripts/statusline.sh`. claude-hud
   (0.0.7) has no custom-segment support, so that script wraps it instead of
   patching it: stdin forwarded verbatim, hud output printed unchanged, `left`
   appended on its own line so nothing depends on the hud's layout mode.
   Registered at `statusLine` in `~/.claude/settings.json`.
2. **Daily Briefing** — first line of the 5am file and text. Wired into
   `Claude/System/Scheduled Tasks/Daily Briefing.md` under Output Format and
   Delivery. The prompt says to print it verbatim and never caption it.

## Per-machine install

`~/Code` syncs via Syncthing but `~/.local/bin` does not, so the symlink is
per-machine:

```sh
ln -sf ~/Code/tools/left/left ~/.local/bin/left
mkdir -p ~/.config/left && cp config.example.json ~/.config/left/config.json
```

`~/.config` is not synced either — the config has to be created on each machine,
or `left` falls back to the defaults baked into the script (which are Alex's, so
it works either way; the file exists to make the number editable without
touching code).

## Notes

- Pure stdlib, no dependencies. Runs on the system `python3`.
- Leap years are handled by counting real days between real dates rather than
  multiplying by 365.25. A Feb 29 birthdate would roll to Mar 1 in common years.
