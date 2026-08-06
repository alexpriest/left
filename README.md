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

## The dial

No emoji, anywhere. **`--statusline` carries a fill dial; nothing else does.**

```
○  0–12.5%      ◔  12.5–37.5%      ◑  37.5–62.5%      ◕  62.5–87.5%      ●  87.5%+
```

One character that fills as the life does — same fill language as the Week Shape
calendar. It makes the countdown something you *notice* rather than a number you
have to read. `◑` is the state he's in now, and the memo that started all of this
was about being almost halfway.

**The daily line stays bare** — it reaches iMessage, where none of the font
reasoning below holds.

### ⚠️ Existence is not the test. Metrics are.

This first shipped `⧗` (U+29D7 BLACK HOURGLASS) on the strength of a cmap scan
showing it present on this Mac — 3 fonts: Apple Symbols, STIXGeneral, STIXTwoMath.
It rendered, and sat visibly misaligned, because **all three are proportional and
the terminal is monospace.** Every draw was a foreign-width fallback. Rescanning
**monospace only**: `⧗` is in **0 of 42**. No hourglass exists in any monospace
font at all.

### ⚠️ And the correction to that correction: missing ≠ broken

Ghostty falls back. `⧗` was doomed because *no* monospace font had it — that was a
property of that glyph, not a general rule, and generalising it was an error.

`○` (U+25CB) and `◑` (U+25D1) are genuinely absent from JetBrains Mono — verified
twice, with a hand-rolled cmap parser and then independently with fontTools. The
font maps only **1,182 codepoints** and ships **39 of the 96 Geometric Shapes**:
every triangle and square, plus `◆ ◇ ◊ ◎ ● ◔ ◕ ◯`, but **not one of the eight
half-filled circles** (`◐ ◑ ◒ ◓ ◖ ◗` all absent). A curated subset, oddly shaped.

But they *are* in Menlo, Andale Mono and Courier New, so the fallback lands
metric-correct. **Two of the five dial glyphs are fallbacks and the row renders
even** — confirmed visually 2026-08-06. Do not "fix" them out on font-coverage
grounds alone; check how it actually looks first.

### If the dial ever needs replacing

Fully native in JetBrains Mono, no fallback: `◯ ◔ ◕ ● ◆ ▽ □ ◧ ■ ▫ •`. The square
family (`□ ◧ ■`) is a complete three-state dial with no fallback at all, at the
cost of the clock metaphor.

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
