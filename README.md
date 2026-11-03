# widget-vault

Seventeen widgets, each in its own self-contained HTML file with no GSAP. Open any of them in a browser — there's no build step, and the only dependency is Google Fonts (Outfit, Inter).

All seventeen share the same design tokens (honey, night, ink and the warm card surface), animate with CSS transitions and the Web Animations API, and respect the reduced-motion setting.

| File                   | Widget          |
| ---------------------- | --------------- |
| `time-tracker.html`    | Time tracker    |
| `time-tracker-tape.html` | Time tracker (tape) |
| `office-hours-tape.html` | Office hours (tape) |
| `office-hours-type.html` | Office hours (type) |
| `progress-card.html`   | Weekly progress |
| `onboarding-card.html` | Onboarding      |
| `calendar-card.html`   | Week calendar   |
| `month-tracker.html`   | Month tracker   |
| `status-tracker.html`  | Status tracker  |
| `focus-ring.html`      | Focus ring      |
| `activity-heatmap.html`| Activity heatmap|
| `command-palette.html` | Command palette |
| `timezone-planner.html`| Time-zone planner |
| `sky-clock.html`       | Sky clock         |
| `weather-sky.html`     | Weather sky       |
| `weather-mini.html`    | Weather mini      |
| `weather-type.html`    | Weather type      |

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

## Time tracker (tape) — `time-tracker-tape.html`

A second take on the time tracker with a different interface: a dark card where a horizontal tape ruler slides past a fixed needle, time is tagged to projects, and today's sessions are laid out on a timeline.

### Behaviour

- **Readout:** HH:MM in large rolling digits, with the seconds smaller beside them. The colon blinks while the timer runs.
- **Tape:** a ruler of tracked time, with a tick every 10 seconds and a label every minute, glides past the honey needle while you're tracking and stops when you pause. A coloured band along the bottom shows which project each stretch of time went to. It slides into place on load and rewinds to zero when you clear the day.
- **Projects:** Design, Development, Meetings and Admin, each with its own colour. Switching project while tracking ends the current session and starts a new one. In the first 5 seconds of a session it just relabels the session instead.
- **Start / Pause, Split:** Start begins a session on the selected project and Pause ends it. Split closes the current session and starts a fresh one on the same project, like a lap.
- **Today:** a wall-clock timeline of when each session happened, coloured by project, with a "now" marker. The running session is striped. Hovering a block shows its project, times and length. Above it, a goal line fills against an 8-hour day.
- **Sessions:** a list with the newest first, showing each session's project, start and end times and length. The running session counts live. Delete a session with its × button, or clear the whole day with ↺. Both offer **Undo** for 6 seconds.
- **Keyboard:** Space starts and pauses, S splits, and 1–4 pick a project.
- **Midnight:** each day starts clean. A session still running at midnight carries on into the new day from 00:00.
- **Timekeeping:** times are stored as timestamps, so the timer stays accurate in background tabs, carries on across reloads and stays in sync between open tabs.

### Settings and storage

| Attribute         | Default | What it does                                                                         |
| ----------------- | ------- | ------------------------------------------------------------------------------------ |
| `data-goal-hours` | `8`     | The working day the goal line fills against.                                         |
| `data-seed`       | on      | On first run, fills today with three sample sessions (2h 35m). Remove it to start empty. |
| `data-projects`   | —       | Optional JSON list replacing the default projects, up to 9: `[{"id":"client","name":"Client work","color":"#f6c343"}]`. |

Today's sessions are saved under `time-tracker-tape:state`. This tracker is separate from `time-tracker.html` and doesn't feed the month tracker.

---

## Office hours (tape) — `office-hours-tape.html`

The tape tracker rebuilt for presence. Instead of tagging time to projects, you clock in and out, and while you're clocked in you're always in one of four statuses: **Active**, **Focus**, **Meeting** or **Away**.

### Behaviour

- **Clock in / out:** the main button clocks you in on the last status you used. Picking a status while you're off the clock clocks you in on that status.
- **Statuses:** switching status ends the current entry and starts a new one. In the first 5 seconds of an entry it just relabels it. Away still counts as clocked-in time but not as online time, and it's drawn striped wherever it appears.
- **Readout and tape:** the readout shows total time clocked in today. The tape slides past the needle while you're clocked in, with a band coloured by status. The status pill and the card's glow take the current status's colour.
- **Today:** a stacked bar and a legend give each status's time and share of the day, with **Online** (Active + Focus + Meeting) and **Away** totals above.
- **Timeline:** the clock-in entries laid out against the time of day. Office hours are outlined, with a "now" marker. The header line says how long until opening or closing, or that the office has closed, and adds any time clocked in outside office hours. During those hours the readout shows an "Outside office hours" tag.
- **Status log:** every change, newest first, with its times and length. The current entry counts live. Delete an entry with ×, or clear the day with ↺. Both offer **Undo** for 6 seconds.
- **Keyboard:** Space clocks in and out, and 1–4 pick Active, Focus, Meeting or Away.
- **Midnight and storage:** as in the tape tracker, each day starts clean, a clock-in still running at midnight carries on from 00:00, and open tabs stay in sync.

### Settings and storage

| Attribute           | Default | What it does                                                                                  |
| ------------------- | ------- | --------------------------------------------------------------------------------------------- |
| `data-office-start` | `9`     | Office opens, in hours (`9.5` = 09:30).                                                       |
| `data-office-end`   | `18`    | Office closes.                                                                                |
| `data-seed`         | on      | On first run, fills today with a sample morning, 2h 35m back to back, including 12m away. Remove it to start empty. |

Today's entries are saved under `office-hours:state`. It's separate from `status-tracker.html`, so the focus ring and heatmap don't read it.

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

---

## Sky clock — `sky-clock.html`

An ambient clock that shows the real sky for where you are. The sun follows its actual path for today, the sky shifts from dawn colours through day to night, stars come out after dusk, and the moon shows its current phase.

### Behaviour

- **Sun path:** a dashed curve traces the sun's height across the full 24 hours, with midnight at the left edge and midnight at the right. The part above the horizon is drawn brighter; the part below is faint. The sun's glow follows the curve, and once it sets a small ring keeps marking where it is below the horizon.
- **Sky:** the gradient is worked out from the sun's height: deep navy at night, violet and rose through twilight, orange at the horizon around sunrise and sunset, then pale blue by day. A warm haze gathers on the horizon near the sun at sunrise and sunset, and the hills take their colour from the sky. Text switches between dark and light to stay readable.
- **Night:** stars fade in as the sun drops more than 4° below the horizon, twinkle, and now and then a shooting star crosses. The moon appears with its current phase. Its position in the sky is decorative, not astronomical.
- **Preview:** drag across the sky to see any time of day. The sky, sun, badge and clock follow your pointer, and the line below says how far away that time is. When you let go, everything eases back to now. With the keyboard, ← / → move by 15 minutes, PgUp / PgDn by an hour, Home / End jump to midnight, and Escape or Enter returns to now; it also returns on its own after 4 seconds.
- **Facts:** a countdown to the next sunrise or sunset, then sunrise and sunset with first and last light (civil twilight), daylight length with the change since yesterday, and evening golden hour with the end of the morning one. A moon row gives the phase name, how much is lit, and the days until the next full or new moon. Polar day and night are handled too ("Midnight sun", "Polar night").
- **Location:** to start with, the sky uses a city matched to your browser's time zone (around 25 zones; for any other zone it estimates the location from your UTC offset). **Use my location** asks the browser for your position and saves it. Tapping the button again returns to the time-zone city.
- **Load-in:** the sun rises along its path from four hours ago to now.
- **Reduced motion:** the stars don't twinkle, there are no shooting stars, and the sky changes without easing.

### Notes

- The sun maths follows Vladimir Agafonkin's SunCalc (BSD licence) and is accurate to about a minute. The moon phase is counted from a known new moon using the average lunar month, which is accurate to within a few hours.
- Everything is calculated in the browser. The only data stored is a location you choose, under `sky-clock:location`.

---

## Weather sky — `weather-sky.html`

The sky clock's living sky, driven by the real forecast. The scene paints the current weather, the hourly strip lets you see the sky at any hour of the next day, and the panel below gives the details and the week ahead.

### Behaviour

- **Scene:** the sky colour comes from the sun's height, as in the sky clock, then turns grey under cloud, darker in rain and darker still in a storm. Clouds drift with the wind and grow and darken as cover thickens. Rain falls slanted by the wind direction, snow drifts and sways, and fog banks gather over the hills. In a thunderstorm, lightning flashes and forks across the sky. At night, stars and the current moon phase show through gaps in the cloud.
- **Big readout:** the temperature, the conditions (a clear sky after dark reads "Clear night"), feels-like, and today's high and low.
- **Next 24 hours:** a temperature curve over hourly columns showing each hour's icon, temperature and chance of rain. Hovering an hour, or tapping it on touch screens, plays that hour's sky in the scene, with the sun where it will be. Tapping pins the hour; tapping it again, the badge, or Escape returns to now. The arrow keys move between hours, and the dot on the curve follows.
- **Details:** feels like (and why), humidity, wind with a direction arrow, today's chance of rain, UV index, and sunrise and sunset.
- **7 days:** each day's icon, chance of rain, and low and high on a bar placed against the week's full range. The colour runs from cool blue to warm orange, and a dot on today's bar marks the current temperature.
- **Location:** opens a search across cities worldwide using Open-Meteo's geocoding. It also offers **Use my location**, and a way back to your time zone's city. The choice is saved.
- **Units:** °C with km/h, or °F with mph. Browsers set to US English start in °F. The choice is saved.
- **Refreshing:** the forecast is cached for 15 minutes and refreshes on its own, or when you press ↻. If it can't be reached, the last forecast stays up with a note. If nothing has ever loaded, a clearly labelled sample forecast keeps the widget usable.
- **Load-in:** the weather rolls in over a clear sky, the temperature curve draws itself, and the rows rise into place.
- **Reduced motion:** clouds, rain and snow hold still, there's no lightning, and scenes change without easing.

### Notes

- Data comes from [Open-Meteo](https://open-meteo.com/) (free, no API key, CC BY 4.0). Keep the attribution line in the footer.
- Add `?scene=` to the URL to force a scene while designing: `clear`, `partly`, `cloudy`, `fog`, `drizzle`, `rain`, `heavy`, `snow` or `storm`.
- Saved in the browser: `weather-sky:location`, `weather-sky:units` and `weather-sky:cache`.

---

## Weather mini — `weather-mini.html`

The weather sky reduced to a single card, about 640 × 330 with a 620 × 310 scene: the live sky, the current conditions, and the next few hours along the hills.

### Behaviour

- **Scene:** the same weather painting as `weather-sky.html`: a sky driven by the sun, drifting clouds, rain, snow, fog, lightning, stars and the moon.
- **Readout:** the temperature, the conditions, and the feels-like temperature with today's high and low. The text switches between dark and light to suit the sky.
- **Next hours:** "Now" plus the next five hours, each with an icon and temperature. Hovering an hour plays its sky, and moving the pointer away returns to now. Tapping pins an hour; tap it again, or tap the place pill, which reads "14:00 · Back to now" while pinned, to return. ← / → move between hours and Escape returns to now. On phones it shows five hours instead of six.
- **Two taps:** tap the temperature to switch between °C and °F. Tap the place pill to use your location; tap it again to go back to your time zone's city.
- **Sharing:** it shares its location, units and 15-minute forecast cache with `weather-sky.html`, and follows changes made in another open tab, so the two widgets always agree.
- **Offline:** if Open-Meteo can't be reached, the last forecast stays up. If nothing has loaded yet, a sample forecast is shown, and the credit line says so.

### Settings

| Attribute    | Default | What it does                                    |
| ------------ | ------- | ----------------------------------------------- |
| `data-hours` | `6`     | How many hours run along the bottom, from 3 to 8. |

`?scene=rain` and the other scene names work here too. Keep the "Weather data by Open-Meteo.com" credit in the corner.

---

## Weather type — `weather-type.html`

The weather as typography: no scene, just a large thin temperature, a hand-drawn line icon and a quiet row of hours. It comes in two styles, shown one above the other on the demo page. Each card is 620 × 310, so either can be used on its own.

| Style   | Markup                                                   | Look                                                                                  |
| ------- | -------------------------------------------------------- | ------------------------------------------------------------------------------------- |
| Tinted  | `<article class="wt" data-weather-type data-style="tint">`  | A soft two-tone wash that follows the weather and the hour. The ink and accent colours change with it. |
| Paper   | `<article class="wt" data-weather-type data-style="paper">` | Ink on light paper with a faint grain, and honey as the only accent.               |

### Layout

- **Top line:** the place and local time in small tracked capitals, with a °C / °F switch on the right.
- **Hero:** the temperature at about 130px in Outfit Thin with a light raised degree sign. A hairline separates it from the condition column: a 58px line icon, the condition in Outfit Light, and "Feels 14° · H 19° · L 10°" with the feels-like figure emphasised.
- **Hours:** "Now" plus five hours below a hairline, separated by hairlines. Each shows the hour in small tracked capitals, a small line icon and the temperature in thin figures.

### Behaviour

- **Icons:** hand-drawn at 48 × 48 in the text colour, with honey only in the sun's core. The hero icon has one slow motion: the sun's rays turn, clouds drift, rain and snow fall, fog lines slide, a storm bolt flickers, and a star twinkles beside the moon. The hour icons stay still. Anything behind a cloud is masked out, so lines never cross.
- **Tint palettes:** each kind of weather has a day, a dusk and a night palette, blended by the sun's height. Clear mornings are warm honey, rain is slate blue, storms are charcoal with light ink, and clear nights are deep navy. Colours glide between states over about 1.4 seconds.
- **Previews:** hovering an hour updates the hero. The number counts to the new temperature, the icon and words fade in, and a short accent line slides under that hour. The top line reads "15:00 forecast" with an accent underline. Tap an hour to pin it; tap it again, tap the place, or press Escape to return. ← / → step between hours.
- **Taps:** tap the temperature, or °C / °F, to switch units. Tap the place to use your location, and tap again to go back to your time zone's city.
- **Load-in:** each card's temperature counts up from zero while the lines and hours rise into place, with the second card slightly after the first.
- **Phones:** the icon moves beside the conditions under the temperature, and five hours show.
- **Shared data:** it uses the same live Open-Meteo data, cache, location and units as the other weather widgets, offline handling included. `?scene=` forces the conditions for design checks.

---

## Office hours (type) — `office-hours-type.html`

The office-hours tracker redrawn in the `weather-type.html` style: typography first, hairlines and line icons, in **Tinted** and **Paper** versions. Each card has a 620 × 310 face, and a **Day** section opens below it.

Both cards on the demo page are views of one tracker. Clock in on either and both update; keep whichever style you want.

### The face

- **Top line:** "OFFICE HOURS · 09:00–18:00" in tracked capitals. On the right: time until opening or closing, any time outside office hours, and the **Day ⌄** toggle.
- **Hero:** today's clocked-in time in thin Outfit numerals, with the seconds small and raised. A hairline separates it from the current status: a line icon, the status name, and how long you've been in it ("0:02 · since 21:45"). The **Clock in / Clock out** button sits on the right; it's filled to clock in and outlined to clock out.
- **Tape:** a hairline ruler of clocked-in time gliding past a thin accent needle, with tabular minute labels. The status band is drawn as thin coloured rules, dashed for Away.
- **Status row:** Active, Focus, Meeting and Away between hairlines, each showing its total for today. The current one gets an accent underline, and the shortcut key appears on hover. Tapping a status switches to it, or clocks you in on it.

### The Day section

It opens smoothly below the face and holds:

- **Summary:** Online and Away totals, plus any time outside office hours.
- **Timeline:** a hairline bar of the day, with office hours outlined in the accent colour, a thin rule for each entry and a "now" line.
- **Status log:** entries as hairline rows, newest first. The current one counts live; others can be deleted. **Clear today** is at the bottom. Both delete and clear offer **Undo** for 6 seconds.

### Styles

- **Tinted:** the wash takes the current status's colour: honey for Active, cool slate for Focus, terracotta for Meeting and warm grey for Away. Off the clock it's plain paper. The ink and accent colours change with it, and the transition takes about 1.2 seconds.
- **Paper:** ink on light paper with a faint grain. The status colours appear only as small dots, rules and the needle.

### Icons

Line icons at 48 × 48, each with one slow motion while you're in that status:

- **Active:** a spark that pulses.
- **Focus:** a target whose inner ring breathes.
- **Meeting:** two speech bubbles with typing dots.
- **Away:** a cup with rising steam.
- **Off the clock:** a power symbol.

### Settings

Settings go on `<body>`:

| Attribute           | Default | What it does                                               |
| ------------------- | ------- | ---------------------------------------------------------- |
| `data-office-start` | `9`     | Office opens (in hours; `9.5` = 09:30).                     |
| `data-office-end`   | `18`    | Office closes.                                              |
| `data-seed`         | on      | Set to `off` to start empty instead of with a sample morning. |

Behaviour otherwise matches the tape version: Space clocks in and out, 1–4 pick a status, a status left running carries over midnight, and open tabs stay in sync. State is saved under `office-hours-type:state`, separate from `office-hours-tape.html`.