# widget-vault

Four dashboard widgets, each in its own self-contained HTML file with no GSAP. Open any of them in a browser — there's no build step, and the only dependency is Google Fonts (Outfit, Inter).

All four share the same design tokens (honey, night, ink and the warm card surface), animate with CSS transitions and the Web Animations API, and respect the reduced-motion setting.

| File                   | Widget          |
| ---------------------- | --------------- |
| `time-tracker.html`    | Time tracker    |
| `progress-card.html`   | Weekly progress |
| `onboarding-card.html` | Onboarding      |
| `calendar-card.html`   | Week calendar   |

---

## Time tracker — `time-tracker.html`

### Behaviour

- **Rolling digits:** HH:MM and the seconds roll into place, starting from 02:35:12.
- **Ticks:** while it's running, the ticks light up through the minute. When paused, only the tick for the current second is lit.
- **Sweep dot:** it springs to each second. It always takes the short way round, so it doesn't spin backwards when the minute wraps.
- **Honey arc:** it fills against the working day, 8 h by default.
- **Reset:** it rewinds the counter and the dial to 00:00.
- **Alarm button:** it toggles to honey and sets a real break reminder one hour out.

### Changes from the original

- **Timekeeping:** the clock runs off timestamps rather than a frame counter, so it stays accurate in background tabs.
- **Persistence:** it remembers its state across reloads using localStorage.
- **Settings:** you can change the starting time, the goal hours and persistence with data attributes on the card.

### Settings

Set these on the card element (`<article class="tt" data-tracker …>`):

| Attribute         | Default | What it does                                                            |
| ----------------- | ------- | ----------------------------------------------------------------------- |
| `data-start`      | `9312`  | Seconds already tracked on first load (`9312` = 02:35:12).              |
| `data-goal-hours` | `8`     | Length of the working day the arc fills against.                        |
| `data-persist`    | on      | Keeps the stopwatch across reloads. Remove it to start fresh each time. |

```html
<article class="tt" data-tracker data-start="0" data-goal-hours="7" data-persist>
```

Saved state lives under the localStorage key `time-tracker:state`. Clear it (or press Reset) to go back to zero; once saved, the stored value takes priority over `data-start`.

---

## Weekly progress — `progress-card.html`

### Behaviour

- **Load-in:** the seven bars grow up from the baseline in a stagger, and the average counts up.
- **Average:** "Avg. workday" is calculated from the data — the mean of the weekdays, weekends excluded (6.6 h with the sample data).
- **Bars:** weekdays are dark, weekends grey, and today honey and slightly wider.
- **Tooltip:** a honey pill glides to whichever bar you hover or focus, showing the hours as `5h 42m`, and dims the other bars. It returns to today when you leave the chart.
- **Keyboard:** each day is focusable; ← and → move between days.

### Editing the data

Change the `DAYS` array in the script. Each entry is `{ d, name, h }`, plus `off: true` for a weekend or `today: true` for the highlighted day. `MAX_HOURS` (default `9`) sets what counts as a full-height bar.

---

## Onboarding — `onboarding-card.html`

### Behaviour

- **Tasks:** clicking a task ticks it off — the title strikes through, the tick pops and the icon wobbles, and a small ring of dots bursts from it. Clicking again unticks it.
- **Count:** the `3/8` counter rolls up or down to the new number.
- **Percentages:** the overall percentage and each phase's percentage count to their new values, and the phase bars (Paperwork, Setup, Team) fill to match.
- **Completion:** ticking the last task bursts dots from the counter and shows "Onboarding complete — welcome aboard" in a toast.
- **Load-in:** the tasks slide in one by one while the phase bars fill and the percentages count up from zero.
- **Scrolling:** the task list scrolls inside the dark card and fades out at the bottom edge.

### Editing the tasks

Tasks are plain `<button class="task">` elements in the HTML. Set `aria-pressed="true"` for a task that starts done, and `data-phase="0"`, `"1"` or `"2"` to assign it to Paperwork, Setup or Team. The totals and percentages are worked out from the markup, so adding or removing tasks needs no script changes.

---

## Week calendar — `calendar-card.html`

### Behaviour

- **Week view:** six days (Mon–Sat) from 8:00 to 12:00 in half-hour rows, with today's date in a dark circle and a honey "now" line.
- **Events:** each event shows a title, a subtitle and illustrated avatars of the people in it. On narrow events the avatars hide to keep the text readable.
- **Paging:** the prev and next buttons (or Alt + ← / →) slide the current week out and the new one in. The buttons show the neighbouring weeks' start dates and disable at either end.
- **Event details:** clicking an event pops it and shows its details in a toast.
- **Phones:** below 640 px the week scrolls sideways, snapping to days, with the time column pinned on the left.

### Editing the data

- `WEEKS` holds each week: a `title`, the six `days` numbers, optional `today` (day index) and `now` (rows from 8:00, e.g. `3.33` ≈ 9:40), and its `events`.
- Each event is `{ day, span, slot, len, title, sub, tone, who }` — `day` 0–5, `span` days wide, `slot` the half-hour row it starts on, `len` rows tall, `tone` `'dark'`, `'honey'` or `''`, and `who` indexes into `PEOPLE`.
- `PEOPLE` holds each person's name, avatar background and illustrated look.
- `EDGE_LABELS` sets the button labels shown at the first and last week.

The now line draws across events, as in the original.