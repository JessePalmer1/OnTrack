# OnTrack

A personal goal tracker: long-term goals, weekly goals and daily check-offs in one
phone-sized app. Published as a private Claude Artifact, so it installs on the iPhone
home screen with no app store and no server to run.

**Live app:** https://claude.ai/artifact/7hm3CKqHH7dCs4P9rRzdbE

## Put it on your phone

1. Open the link in **Safari** on your iPhone (sign in to claude.ai if asked).
2. Tap the **Share** button, then **Add to Home Screen**.
3. It gets a 🎯 icon and opens full-screen like a native app.

The same link on a laptop shows the same data — storage lives server-side, not in the
browser, so checking something off on the phone shows up everywhere.

## The four tabs

| Tab | What it holds |
| --- | --- |
| **Today** | Everything scheduled for one day: repeating daily goals plus one-off to-dos. Tap the circle to check off, tap the text to edit. `‹ ›` moves to other days so you can plan ahead or review yesterday. |
| **Week** | The 7-day ribbon (bar height = that day's completion, dot = something due), this week's goals, and anything unfinished carried over from last week. |
| **Goals** | Long-term goals with due-date countdowns and a progress bar driven by the weekly/daily goals linked to them. |
| **Upcoming** | Every due date, soonest first, bucketed Overdue → Today → Tomorrow → Next 7 days → Next 30 → Later. The red badge on the tab counts overdue items. |

## Beyond the original requirements

- **Repeat on chosen weekdays**, not just every day (gym Mon/Wed/Fri, review Tue/Thu).
- **Linking** — a daily or weekly goal can be "part of" a long-term goal, which is what
  fills that goal's progress bar. This is the bit that connects "two practice problems
  today" to "be ready to interview".
- **Streak** — consecutive days where every scheduled daily goal was completed. Today
  counts once it's clear, so an in-progress morning never shows a broken streak.
- **Roll-over** — unfinished one-off items from earlier days surface on Today with a
  `→ Today` button; unfinished weekly goals surface with `→ This week`.
- **Overdue badge** on the Upcoming tab so a missed due date is visible from any screen.
- Full dark mode, safe-area aware, and the day flips over automatically if you leave the
  app open overnight.

## Editing

Tap any item's text to open the editor sheet: name, notes, kind (daily / weekly /
long-term — you can promote a daily to-do into a real goal), due date, repeat schedule,
and which long-term goal it belongs to. Delete is a two-tap confirm, no modal dialogs.

## How it's built

`ontrack.html` is the whole app — one file, no build step, no dependencies beyond three
Google Fonts. State lives in the artifact's `db` capability (declared at publish time):

- `goals/<id>` — one document per goal.
  `type` is `long` | `week` | `day`; long/week carry `due` and `done`; week carries
  `weekStart` (Monday); day carries either `repeat` + `start` + `wd` (weekday mask, empty
  = every day) or a single `date`; any non-long goal may carry `parentId`.
- `days/<YYYY-MM-DD>` — one document per day: `{ date, done: { goalId: true }, total }`.
  Daily check-offs are per date, so repeating goals keep a real history without
  duplicating a document per goal per day.

Writes are serialized per document through a small queue, and the UI updates optimistically
before the write lands.

### Changing it

Edit `ontrack.html` and republish to the **same URL** — the data survives republishes:

```
Artifact publish  file_path: ontrack.html  url: https://claude.ai/artifact/7hm3CKqHH7dCs4P9rRzdbE
```

The seeded example goals (interview prep, semester project, and three daily goals) are
ordinary rows — edit or delete them from inside the app.
