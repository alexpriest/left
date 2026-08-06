# left

How much time is left.

From the 2026-08-05 voice memo: the `Left` app's death countdown landed hard —
*"almost halfway. Which is rude. And a little bit of a wake-up call."* — and
Alex asked for a daily version of it. This is that number, in two places.

```
$ left
48y 187d left · 2,531 Saturdays · 43.6% spent

$ left --statusline
48y 187d · 43.6%

$ left --long
48 years, 187 days left (to 2075-02-09, at 86).
   17,719 days · 2,531 weeks · 48 more summers.
   43.6% of the whole thing is behind you. 28.7% of your adult life.
```

## Config

`~/.config/left/config.json`

```json
{"birthdate": "1989-02-09", "life_expectancy": 86, "adulthood_starts": 18}
```

Birthdate verified 2026-08-06 against three macOS Contacts sources (all agree:
1989-02-09).

**`life_expectancy` is 86, set 2026-08-06 after a full actuarial workup.** It
shipped at 81 — the `Left` app's number — and Alex asked for a real estimate.
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
