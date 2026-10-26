# widget-vault

Ten widgets, each in its own self-contained HTML file with no GSAP. Open any of them in a browser — there's no build step, and the only dependency is Google Fonts (Outfit, Inter).

All ten share the same design tokens (honey, night, ink and the warm card surface), animate with CSS transitions and the Web Animations API, and respect the reduced-motion setting.

| File                   | Widget          |
| ---------------------- | --------------- |
| `time-tracker.html`    | Time tracker    |
| `progress-card.html`   | Weekly progress |
| `onboarding-card.html` | Onboarding      |
| `calendar-card.html`   | Week calendar   |
| `month-tracker.html`   | Month tracker   |
| `status-tracker.html`  | Status tracker  |
| `focus-ring.html`      | Focus ring      |
| `activity-heatmap.html`| Activity heatmap|
| `command-palette.html` | Command palette |
| `timezone-planner.html`| Time-zone planner |

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

---

## Month tracker — `month-tracker.html`

The Payroll view's month card, rebuilt as a working month-to-month activity log. Every part of the original card is reused: the hours / amount summary, the month pill, the Work hours / Overtime / Leave rail and the day grid with its hour, overtime and leave chips.

### Behaviour

- **Months:** the month pill is now a navigator. ‹ and › page through months with a slide; clicking the month name jumps back to the current month. The grid lays each month out on its real weekdays, Monday first, and outlines today.
- **Logging:** clicking a day opens an editor. Step the hours in half-hour steps, pick 4 h / 8 h / 9.5 h, book Leave or Clear the day. Changes apply immediately and are saved in the browser.
- **Chips:** honey for hours, dark once a day runs past a standard day (overtime), grey for leave.
- **Summary:** the total hours and pay count to their new values on every change. Pay is hours × rate plus a 0.5× premium on overtime, as in the original.
- **Rail:** the three segments resize to the month's split of regular hours, overtime and leave, and refill when the month changes.
- **Future days:** you can book leave ahead, but hours can only be logged once the day arrives.
- **Time tracker link:** when `time-tracker.html` has been used in the same browser, today's hours follow it, with a green dot that pulses while the tracker runs. Logging today by hand overrides the tracker.
- **Keyboard:** arrow keys move between days, crossing into the next or previous month at the edges; Enter opens the editor and Escape closes it.
- **Phones:** the editor becomes a bottom sheet and the grid tightens up.

### Settings

Set these on the card element (`<section class="mt" data-month-tracker …>`):

| Attribute        | Default | What it does                                                                               |
| ---------------- | ------- | ------------------------------------------------------------------------------------------ |
| `data-rate`      | `38`    | Hourly rate used for the pay total.                                                        |
| `data-day-hours` | `8`     | A standard day. Hours above it count as overtime; a leave day counts as this many hours.   |
| `data-seed`      | on      | Fills past weekdays from the last 12 months with sample hours. Remove it for a clean log.  |

What you log is saved under the localStorage key `month-tracker:log`, keyed by date, and always takes priority over the sample data and the tracker. The time tracker doesn't reset at midnight, so the "today" figure it supplies is whatever it currently holds.

---

## Status tracker — `status-tracker.html`

A second month tracker, built on the same card as `month-tracker.html`, that tracks presence instead of pay: how each day's time splits across **Active**, **Focus**, **In a meeting**, **Away** and **Offline**.

### Behaviour

- **Live status:** the "Now" switcher sets your current status. Time counts into it from the moment you pick it, with a running clock beside the switcher, and a dark pill slides to the active status. Choosing **Offline** pauses tracking.
- **Today:** today's cell fills itself as you work. Its chip shows hours online with a pulsing green dot while a status is running, and its bar grows to match. Today's cell and the month totals refresh every 30 seconds.
- **Day cells:** each past day shows its main status as a dot, a chip with its hours online, and a thin stacked bar of the day's status mix. Days spent fully away or offline say so on the chip instead of showing "0.0h". Future days can't be edited.
- **Editing a day:** clicking a past day (or today) opens an editor with a half-hour stepper per status, the day's online and total hours, and Clear day. A day can't exceed 24 hours. Editing today first folds in the live time, so you adjust the real figure; the running status keeps counting afterwards.
- **Summary:** "hrs online" is Active + Focus + In a meeting for the month; "% focus" is Focus's share of that.
- **Rail:** five segments, one per status, sized to the month's split, and refilled when the month changes.
- **Months and keyboard:** as in the month tracker — ‹ / › to page, click the month to jump back to this month, arrow keys to move between days and across months, ← / → to move along the switcher, Escape to close the editor.
- **Overnight:** a status left running past midnight is split across the days at midnight when the page next opens.
- **Phones:** the switcher scrolls sideways, the rail becomes a 3 + 2 grid and the editor becomes a bottom sheet.

### Settings and storage

| Attribute   | Default | What it does                                                                                     |
| ----------- | ------- | ------------------------------------------------------------------------------------------------ |
| `data-seed` | on      | Fills the last 12 months with a sample status mix per day. Remove it for a clean log.           |

- `status-tracker:log` holds the hours per status for each date you've logged or tracked. It takes priority over the sample data.
- `status-tracker:live` holds the current status and when it started, so tracking carries on across reloads and syncs between open tabs.
- Status colours are the `--st-active`, `--st-focus`, `--st-meeting`, `--st-away` and `--st-offline` tokens at the top of the stylesheet. Statuses themselves are the `STATUSES` array in the script.

---

## Shared status data

`status-tracker.html`, `focus-ring.html` and `activity-heatmap.html` read the same browser storage, so they agree with each other:

- `status-tracker:log` — hours per status for each date.
- `status-tracker:live` — the current status and when it started.

All three use the same sample data (`data-seed`) for days with nothing logged, and update when another open tab changes the data. Open them from the same origin (the same folder or site) so they share storage.

---

## Focus ring — `focus-ring.html`

A daily focus goal, built on the status tracker's data.

### Behaviour

- **Ring:** fills with today's Focus time against your goal, 3 h by default. Past the goal it turns honey and a second honey lap shows the overshoot.
- **Live session:** **Start focus** sets your status to Focus, the same as choosing it in the status tracker. The ring and the session clock tick every second. **Pause focus** sets your status back to Active.
- **Goal:** the − / + stepper in the header changes the goal in half-hour steps, from 30 minutes to 8 hours, and remembers your choice.
- **Goal reached:** crossing the goal during a session pulses the ring, bursts dots around it and shows a toast. Changing the goal or reloading doesn't trigger it.
- **Streak:** consecutive workdays at your goal. Weekends are skipped, and today only counts once you hit the goal, so an unfinished day doesn't break the streak. **Best** is the longest such run in the last year.
- **Last 7 days:** a mini ring per day, honey with a tick on days that hit the goal. Weekends have dashed tracks.
- **Midnight:** the ring rolls over to the new day on its own.

### Settings and storage

| Attribute         | Default | What it does                                                         |
| ----------------- | ------- | -------------------------------------------------------------------- |
| `data-goal-hours` | `3`     | Starting daily goal. Once you use the stepper, your choice is saved. |
| `data-seed`       | on      | Use the status tracker's sample data. Keep it the same in all three. |

The chosen goal is saved under `focus-ring:goal`.

---

## Activity heatmap — `activity-heatmap.html`

A GitHub-style grid of every day, built on the status tracker's data.

### Behaviour

- **Grid:** one column per week, Monday to Sunday, with month labels. Today is outlined, and days still to come this week are shown as empty outlines.
- **Measures:** **Online** (Active + Focus + In a meeting), **Focus** or **Meetings**, each with its own colour ramp: honey, charcoal or terracotta. Switching measure recolours the grid in a sweep from left to right.
- **Ranges:** **3M**, **6M** or **1Y**. Changing the range rebuilds the grid with a diagonal sweep-in.
- **Stats:** total hours in the range, the average on days with activity, the best day and its date, and the longest workday streak with any activity (weekends neither count nor break it).
- **Tooltip:** hovering, tapping or focusing a day shows its date, the chosen measure, a mini bar of the day's status mix and the hours per status.
- **Keyboard:** ↑ / ↓ move a day, ← / → move a week, Home jumps to the first day, End to today, and Escape hides the tooltip.
- **Sizing:** cells grow to fill the card, between 11 and 24 px. When the range doesn't fit, the grid scrolls sideways and starts at today.
- **Live:** today's cell and the stats refresh every 30 seconds while a status is running.

### Levels

| Measure  | Level 1 | Level 2   | Level 3     | Level 4  |
| -------- | ------- | --------- | ----------- | -------- |
| Online   | < 6 h   | 6–8.5 h   | 8.5–9.5 h   | ≥ 9.5 h  |
| Focus    | < 1 h   | 1–2 h     | 2–3 h       | ≥ 3 h    |
| Meetings | < 1 h   | 1–2 h     | 2–3 h       | ≥ 3 h    |

Change them in the `METRICS` object's `bins`. The ramps are the `--l1`–`--l4` tokens on `.hm-card`.

### Settings and storage

| Attribute    | Default | What it does                                                         |
| ------------ | ------- | -------------------------------------------------------------------- |
| `data-range` | `6m`    | Starting range: `3m`, `6m` or `1y`.                                  |
| `data-seed`  | on      | Use the status tracker's sample data. Keep it the same in all three. |

The chosen measure and range are remembered under `activity-heatmap:view`.

---

# General-purpose widgets

These two aren't tied to the dashboard. They're built to drop into any future web app.

## Command palette — `command-palette.html`

A keyboard-first ⌘K / Ctrl K palette for searching and running commands, in the style of Linear, Raycast or VS Code.

### Behaviour

- **Opening:** ⌘K / Ctrl K toggles it from anywhere, and `/` opens it when you're not typing in a field. It opens as a native `<dialog>`, so focus is trapped and returns where it was on close.
- **Fuzzy search:** matches letters in order (`drk` finds **D**a**rk**). Word starts, runs of letters and prefixes rank higher, and matched letters are underlined in honey. Keywords match too, so "pomodoro" finds the focus timer.
- **Deep results:** searching from the top level also finds commands inside pages, shown with their path (*Change theme › Dark*).
- **Sections:** with an empty search you see **Recent** (your last five), then each section in order. Commands you use often rank higher in search.
- **Pages:** commands with children open a sub-page, shown as a chip in the search bar. Enter or Tab goes in; Backspace on an empty search or Escape goes back. Escape at the top level closes the palette.
- **Highlight:** a honey-edged highlight slides between rows. ↑ / ↓ move by one, PgUp / PgDn by six, and the mouse moves it too.
- **Shortcuts:** commands can declare a shortcut (`mod+shift+k`). It runs from anywhere and shows as key caps on the row: ⌘ ⇧ ⌥ on a Mac, Ctrl / Shift / Alt elsewhere.
- **Japanese input:** Enter and the arrow keys are ignored while an IME is composing, so choosing kanji candidates doesn't run a command.
- **Phones:** the palette sits at the top of the screen and the footer hints collapse.

### Using it in your app

Copy the `.cp` dialog markup, its styles and the first `<script>` block, then register your commands:

```js
CommandPalette.register([
  { id: 'go-home', title: 'Go to Home', section: 'Navigate', icon: 'home',
    keywords: ['dashboard'], shortcut: 'alt+1', run: () => router.push('/') },
  { id: 'theme', title: 'Change theme', section: 'Preferences', icon: 'palette',
    children: [
      { id: 'dark', title: 'Dark', checked: () => theme === 'dark', run: () => setTheme('dark') },
    ] },
]);
```

| Field      | What it does                                                                                     |
| ---------- | ------------------------------------------------------------------------------------------------ |
| `id`       | Unique within its level. Recents and usage counts are stored by its path (`theme/dark`).         |
| `title`    | The row text, and what fuzzy search highlights.                                                  |
| `section`  | Group heading at the top level. Defaults to "Commands".                                          |
| `icon`     | The id of a sprite `<symbol>`, without the `i-` prefix.                                          |
| `keywords` | Extra words to match, without being shown.                                                       |
| `hint`     | Small grey text after the title.                                                                 |
| `shortcut` | A key combo such as `mod+k`, `alt+shift+c`, or a single key. `mod` is ⌘ on a Mac, Ctrl elsewhere. |
| `run(api)` | What it does. `api` has `close()`, `toast(message)` and `push(title, items)`.                    |
| `children` | An array, or a function returning one, that opens as a sub-page.                                 |
| `checked`  | A function; when it returns true the row shows a tick.                                           |
| `keepOpen` | Leaves the palette open after running.                                                           |

`CommandPalette` also exposes `open(initialQuery)`, `close()` and `toggle()`. Recents and usage counts are stored under `command-palette:recent` and `command-palette:freq`.

### The demo

The demo page's commands have real effects: light, dark or system theme (applied before first paint, so there's no flash), comfortable or compact density, English or 日本語, page navigation on Alt + 1–4, and copying the page link. Its settings are saved under `command-palette:demo`.

---

## Time-zone planner — `timezone-planner.html`

A strip that answers "when can we all meet?" for a team spread across time zones.

### Behaviour

- **Rows:** one per person, across your 24 hours. Each hour cell shows that person's local hour. Working hours are shaded honey, night hours (22:00–06:00) grey, and the first hour of a new local day is labelled with its weekday. Half-hour zones such as Bengaluru show their `:30`.
- **Everyone free:** hours when everyone is working get a green band, and a banner states the window ("Everyone's working 17:00–18:00 your time"). When no time works for everyone, it shows the best partial window as a dashed box and names who's missing. **Select it** jumps the selection there.
- **Picking a time:** click or drag on the timeline to place the meeting. It snaps to half-hours and centres on where you click. A dashed cursor follows the pointer, and the right-hand column shows each person's local time under it, with "working" or "off".
- **Length:** 30 min, 1 h, 1.5 h or 2 h.
- **Selected time:** a dark panel lists everyone's local times for the slot, with a green dot when it's inside their hours and "off" when it isn't. **Copy times** copies a ready-to-paste message:

  ```
  Meeting · Monday, October 26
  You (Tokyo): 17:00–18:00
  Priya (Bengaluru): 13:30–14:30
  Lena (London): 08:00–09:00
  ```

- **Dates:** ‹ / › change the day, and the date button returns to today. Offsets are worked out for the chosen date, so daylight-saving changes show up; New York moves from UTC−4 to UTC−5 after November 1. Today also shows a honey "now" line.
- **People:** **Add person** opens a form with a name, a city (around 45 cities, grouped by region) and working hours in half-hour steps. Overnight shifts work: set "until" earlier than "from". Click a name to edit or remove someone. Your own row uses your browser's time zone and can't be removed. The team is saved under `tz-planner:team`.
- **Keyboard:** the timeline is a slider. ← / → move the meeting by 30 minutes, PgUp / PgDn by 2 hours, Home / End jump to the ends, and Enter copies the times.
- **Phones:** the timeline scrolls sideways with the names pinned and starts on the selected time. The right-hand column is hidden, since the selected-time panel shows the same thing.

All time-zone maths uses the browser's built-in `Intl` API, so there's no time-zone library to ship. Add more cities to the `CITIES` array as `[city, IANA time zone]` pairs.