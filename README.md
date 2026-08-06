# left

How much time is left.

From the 2026-08-05 voice memo: the `Left` app's death countdown landed hard —
*"almost halfway. Which is rude. And a little bit of a wake-up call."* — and
Alex asked for a daily version of it. This is that number, in two places.

```
$ left
⏳ 43y 187d left · 2,270 Saturdays · 46.3% spent

$ left --statusline
⏳ 43y 187d · 46.3%

$ left --long
⏳ 43 years, 187 days left (to 2070-02-09, at 81).
   15,893 days · 2,270 weeks · 43 more summers.
   46.3% of the whole thing is behind you. 30.9% of your adult life.
```

## Config

`~/.config/left/config.json`

```json
{"birthdate": "1989-02-09", "life_expectancy": 81, "adulthood_starts": 18}
```

Birthdate verified 2026-08-06 against three macOS Contacts sources (all agree:
1989-02-09). **`life_expectancy` is 81 because that is the number the `Left` app
used and the one Alex already reacted to** — it reproduces his "just over 43
years left" exactly. It is not an actuarial estimate and should not be quietly
"corrected" into one; changing it changes the number he anchored on, so ask
first.

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
