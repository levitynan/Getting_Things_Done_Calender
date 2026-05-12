# TaskCal — README

**TaskCal** is a self-contained, single-file HTML application for managing tasks, projects, meetings, and recurring events with a built-in calendar. No installation, no server, no internet connection required — open the file in any modern browser and start working.

---

## Getting Started

1. Save `task-calendar-9.html` anywhere on your computer.
2. Open it in a browser (Chrome, Edge, Firefox, Safari).
3. Optionally, use the **Live Server** extension in VS Code for a smoother development experience.

> All data is saved automatically to your browser's `localStorage`. Clearing your browser data will erase saved tasks and projects, so use the **Export .ics** feature regularly as a backup.

---

## Navigation

The left sidebar contains five main sections:

| Section | Purpose |
|---|---|
| **🪣 Collect** | Rapid-capture inbox for any thought, task, or to-do |
| **Dashboard** | Overview of projects, KPIs, and upcoming tasks |
| **Calendar** | Monthly, weekly, and daily calendar views |
| **All Tasks** | Full filterable task list grouped by date |
| **Projects** | One nav item per project (listed below All Tasks) |

Click the **New ▾** button in the top bar to create a Task, Project, or Scheduled Meeting from anywhere in the app.

---

## Collect (Inbox)

The Collect page is a frictionless capture inbox — dump anything here without worrying about where it belongs yet.

- Type in the text area and press **Enter** to capture. Use **Shift+Enter** for a new line.
- Each captured item can be processed into a **Task**, **Project**, or **Meeting** using the action buttons on the card.
- Filter the list by: **All · To Process · Completed · Converted**
- Use the **Empty Bucket** button to step through all unprocessed items one by one in a focused review modal.
- Items marked as completed stay visible under the Completed filter.
- Processed (converted) items are shown faded under the Converted filter and can be cleared in bulk.

---

## Tasks

### Creating a Task

Click **New ▾ → Task**, or click any day on the calendar to open the task form pre-filled with that date.

| Field | Notes |
|---|---|
| **Title** | Required |
| **Project** | Optional — assign to an existing project |
| **Priority** | Low / Medium / High |
| **Due Date** | Date picker |
| **Due Time** | Optional time |
| **Recurrence** | None / Daily / Weekdays / Weekly / Bi-weekly / Monthly |
| **Recur Until** | Optional end date for recurring tasks |
| **Notes** | Free-text notes field |

### Recurring Tasks

Choosing any recurrence option other than **None** generates recurring instances automatically across the calendar and All Tasks views. Each instance appears independently on its due date. Set a **Recur Until** date to stop the series at a specific point; leave it blank for an indefinite series.

### Managing Tasks

- **Check** the circle on any task card to mark it done (strikethrough applied).
- **Edit** (pencil icon) to reopen the task form.
- **Delete** (bin icon) to remove the task permanently.
- Completed tasks remain visible but styled as done — they are not hidden.

---

## Projects

Projects group related tasks together and appear as coloured entries in the sidebar, on task cards, and in the Dashboard.

### Creating a Project

Click **New ▾ → Project**.

| Field | Notes |
|---|---|
| **Name** | Required |
| **Description** | Optional summary |
| **Start / End Date** | Optional date range |
| **Colour** | Pick from 8 colour swatches |

### Project View

Click a project name in the sidebar to switch to its filtered task list, showing only tasks belonging to that project. The Dashboard shows a card for each project with a progress bar, task count, and date range.

---

## Meetings

Meetings are scheduled events distinct from tasks — they support additional fields suited to collaborative events.

### Creating a Meeting

Click **New ▾ → Scheduled Meeting**.

| Field | Notes |
|---|---|
| **Title** | Required |
| **Date / Start Time** | Required |
| **Duration** | In minutes |
| **Location / Link** | Room name or URL (e.g. Google Meet link) |
| **Recurrence** | Same options as tasks |
| **Attendees** | Type a name and press Enter or click Add — shown as chips |
| **Agenda / Notes** | Free-text |

Meetings appear on the Calendar in purple and in the Dashboard upcoming panel.

---

## Calendar

### Views

Switch between **Month**, **Week**, and **Day** using the buttons in the top-right of the calendar toolbar.

| View | Description |
|---|---|
| **Month** | Full month grid. Tasks and meetings shown as colour-coded pills. Click a day to see a detail panel below. |
| **Week** | 7-column time grid (48px per hour). All-day events shown above the grid. A red line marks the current time. |
| **Day** | Single-day time grid with full event details. |

### Navigation

- **← →** arrows step backward/forward by month, week, or day depending on the active view.
- **Today** button jumps back to the current date.
- Click any **day number** in week view to jump to that day in day view.
- Click any **calendar event pill** to open its edit form.

---

## Dashboard

The Dashboard shows:

- **KPI cards** — Total projects, total tasks, completed tasks, overdue tasks, tasks due in the next 7 days.
- **Project cards** — Each project with a progress bar (completed / total tasks), description, and date range.
- **Upcoming Tasks** — A panel listing all tasks due in the next 14 days, grouped by date, with project colour coding.

---

## Exporting to a Calendar

Click the **download icon** (↓) in the top bar to export a `.ics` file covering the current month ±1–3 months. The file includes all tasks and meetings with their recurrence rules.

Import the `.ics` file into:
- **Google Calendar** — Settings → Import
- **Apple Calendar** — File → Import
- **Outlook** — File → Open & Export → Import/Export

---

## Light / Dark Mode

Click **Toggle Theme** at the bottom of the sidebar to switch between light and dark mode. The app also respects your system preference by default.

---

## Resetting Data

Click **Reset All Data** at the bottom of the sidebar to wipe all tasks, projects, meetings, and collected items and restore the built-in sample data. **This action cannot be undone.**

---

## Data Storage

All data is stored in your browser's `localStorage` under the key `taskcal-v1`. Because this is browser-local storage:

- Data does not sync across devices or browsers.
- Clearing browser site data will delete everything.
- The app works fully offline.

**Recommended backup:** Export a `.ics` file regularly via the download button.

---

## Technical Notes

- Single `.html` file — no build tools, no dependencies, no network requests.
- Fonts loaded from Google Fonts CDN (requires internet on first load; cached thereafter).
- Compatible with Chrome 90+, Edge 90+, Firefox 88+, Safari 14+.
- Tested at desktop (1280px+) and mobile (375px). Fully responsive.
- Light and dark mode via CSS custom properties (`data-theme` attribute on `<html>`).

