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

The left sidebar contains six main sections:

| Section | Purpose |
|---|---|
| **Dashboard** | Overview of projects, KPIs, and upcoming tasks |
| **🪣 Collect** | Rapid-capture inbox for any thought, task, or to-do |
| **All Tasks** | Task list organised into area columns (Work, Life, University, custom) |
| **💡 Ideas** | Capture ideas and develop them into tasks, projects, or meetings |
| **Calendar** | Monthly, weekly, and daily calendar views |
| **Projects** | One nav item per project (listed below Calendar) |

The top bar contains two utility buttons alongside **New ▾**:
- **⊞ (grid icon)** — opens the Import / Export CSV modal
- **↓ (download icon)** — exports a `.ics` calendar file

Click **New ▾** to create a Task, Project, or Scheduled Meeting from anywhere in the app.

---

## Startup Screen

When the app loads, a full-screen minimalist capture screen appears before anything else. It is designed to get thoughts out of your head immediately, with no distractions.

- Type anything and press **Enter** to capture it — the item goes straight into Collect.
- **Shift+Enter** adds a new line within the same item.
- Captured items appear in a "Just captured" list below the input as you add them.
- Press **Esc** or click **Open full app →** to dismiss the screen and go to the Collect page.

The startup screen appears on every page load and is dismissed for the rest of that session once closed. You can return to it at any time using the **Quick Capture** button at the bottom of the sidebar.

---

## Collect (Inbox)

The Collect page is a frictionless capture inbox — dump anything here without worrying about where it belongs yet.

- Type in the text area and press **Enter** to capture. Use **Shift+Enter** for a new line.
- Each captured item can be processed into a **Task**, **Project**, **Meeting**, or **Idea** using the action buttons on the card.
- Clicking **Done** opens a small modal where you can optionally assign an **Area** and **Project** before marking the item complete. Completed items display the assigned area and project as colour-coded badges.
- Sending an item to **Idea** transfers it silently to the Ideas page — no form opens, and the card is marked "→ Ideas".
- Converted items show an **Undo** button — clicking it reverses the conversion. For items sent to Ideas, the corresponding idea is also removed.
- Filter the list by: **All · To Process · Completed · Converted**
- Use the **Empty Bucket** button to step through all unprocessed items one by one in a focused review modal.
- Items marked as completed stay visible under the Completed filter.
- Processed (converted) items are shown faded under the Converted filter and can be cleared in bulk.

---

## Ideas

The Ideas page is a dedicated space for capturing sparks of inspiration and organizing them into a folder structure. It has two tabs:

### Unfiled Tab

Shows only ideas with no assigned folder. This is the default view for capturing new ideas.

- Type in the text area and press **Enter** to capture. Use **Shift+Enter** for a new line.
- Ideas can also arrive here from the Collect page via the **Idea** action button on any captured item.
- Each idea card is fully editable — click the text to modify it.
- **Developed** — mark an idea as thought through without acting on it yet.
- **Collect** — move the idea back to the Collect inbox, where it can then be converted into a Task, Project, or Meeting.
- **Move** — assign the idea to a folder.
- **Discard** — delete the idea permanently.
- Filter the list by: **All · To Develop · Developed · Converted**

### File Explorer Tab

Organise ideas into a hierarchical folder structure using a two-pane layout:

**Folder sidebar (left):**
- **All Ideas** — shows every idea regardless of folder.
- Folder tree — root folders and subfolders listed below. Click any folder to show its ideas in the content pane.
- Expand/collapse arrows (⊳) — click to navigate subfolders.
- Item counts — shown in parentheses next to each folder name.
- Hover a folder to reveal action buttons: **+** (new subfolder), **✎** (rename), **✕** (delete).
- **New** button at the top of the sidebar — creates a root-level folder.
- **Collapse/expand toggle** — the thin strip on the right edge of the sidebar collapses it to give more room for the content pane; click again to restore it.

**Content pane (right):**
- Displays the ideas belonging to the selected folder, sorted oldest to newest.
- Each idea is fully editable with **Developed**, **Unfile**, **Move**, and **Discard** action buttons.
- Selecting **All Ideas** shows every idea across all folders.

### General

- Ideas moved to Collect are marked "→ Collect" and show an **Undo** button — clicking it removes the corresponding Collect entry and restores the idea.
- The sidebar badge shows the count of ideas still waiting to be developed.
- Newlines in idea text are preserved (use **Shift+Enter** to add new lines within an idea).

---

## Areas

Tasks and projects can be assigned to an **area**: **Work**, **Life**, or **University**. You can also create custom areas at any time by choosing **＋ New area…** from any Area dropdown.

### All Tasks Page

The All Tasks page displays a separate column for each area. Tasks with no area (or an unrecognised area) appear in an **Other** column, which is only shown when such tasks exist. Click **Add task** inside any column to open the task form with that area pre-filled.

The page header displays:
- **Left side:** KPI stats showing **Total tasks** and **Completed** count
- **Right side:** Filters for **All Areas**, **All Status**, and **Sort** options (Due Date, Priority, Oldest First, Newest First, Name)

---

## Tasks

### Creating a Task

Click **New ▾ → Task**, or click any day on the calendar to open the task form pre-filled with that date.

| Field | Notes |
|---|---|
| **Title** | Required |
| **Area** | Work / Life / University / custom |
| **Project** | Optional — assign to an existing project |
| **Priority** | Low / Medium / High |
| **Due Date** | Date picker |
| **Due Time** | Optional time |
| **Notes** | Free-text notes field |

### Managing Tasks

- **Check** the circle on any task card to mark it done (strikethrough applied).
- **Edit** (pencil icon) to reopen the task form.
- **Delete** (bin icon) to remove the task permanently.
- Completed tasks remain visible but styled as done and sorted below uncompleted tasks in each column.

---

## Projects

Projects group related tasks together and appear as coloured entries in the sidebar, on task cards, and in the Dashboard.

### Creating a Project

Click **New ▾ → Project**.

| Field | Notes |
|---|---|
| **Name** | Required |
| **Area** | Work / Life / University / custom |
| **Description** | Optional summary |
| **Start / End Date** | Optional date range |
| **Colour** | Pick from 8 colour swatches |

### Projects Page

The Projects page displays all projects grouped by area in expandable rows. The page header shows a **Projects** count KPI card on the left side, with a **New Project** button on the right. Click the chevron icon on any project card to expand an inline list of its tasks.

### Project View

Click a project name in the sidebar to switch to its filtered task list, showing only tasks belonging to that project. The Dashboard shows a card for each project with a progress bar, task count, and date range.

### Completing a Project

Click the **circle icon** in the top-right of any project card on the Dashboard to mark the project complete. Completed projects are greyed out on the Dashboard and shown with a strikethrough in the sidebar. Click the icon again to reactivate the project.

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

Click the **↓ download icon** in the top bar to export a `.ics` file covering the current month ±1–3 months. The file includes all tasks and meetings with their recurrence rules.

Import the `.ics` file into:
- **Google Calendar** — Settings → Import
- **Apple Calendar** — File → Import
- **Outlook** — File → Open & Export → Import/Export

---

## Import / Export

Click the **⊞ grid icon** in the top bar to open the Import / Export modal.

### Excel (.xlsx)

Export or import all data in a single `.xlsx` file. Each data type lives on its own sheet:

| Sheet | Contents |
|---|---|
| **Tasks** | All tasks — project ID and name, priority, recurrence, notes, completion state, timestamps |
| **Projects** | All projects with colour, dates, and description |
| **Meetings** | All meetings — date, times, location, attendees, recurrence, notes |
| **Collect** | All Collect items with processing state |
| **Ideas** | All Ideas items with development and processing state |

The exported file (`taskcal-data.xlsx`) can be opened and edited directly in Excel or Google Sheets. Re-importing merges by ID — existing rows are updated, new rows are appended.

> **Note:** The Excel feature requires an internet connection on first load to fetch the SheetJS library from a CDN. Subsequent loads use the browser cache.

### CSV (individual files)

Each data type can also be exported and imported independently as a `.csv` file:

| Export file | Contents |
|---|---|
| `taskcal-tasks.csv` | Tasks |
| `taskcal-projects.csv` | Projects |
| `taskcal-meetings.csv` | Meetings (attendees as pipe-separated values) |
| `taskcal-collect.csv` | Collect items |
| `taskcal-ideas.csv` | Ideas |

**Importing** merges by ID — rows whose ID already exists are updated in place; new IDs are appended. Re-importing an export will not create duplicates. A toast notification confirms how many rows were added and updated.

CSV files can be opened and edited in Excel, Google Sheets, or any text editor before re-importing.

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

**Recommended backup:** Export CSV files via the ⊞ grid icon, or export a `.ics` file via the ↓ download icon. CSV exports capture all data including Collect and Ideas items that `.ics` does not include.

---

## Technical Notes

- Single `.html` file — no build tools, no package manager.
- Fonts loaded from Google Fonts CDN; SheetJS loaded from jsDelivr CDN. Both require internet on first load and are cached by the browser thereafter. All other features (tasks, calendar, CSV) work fully offline.
- Compatible with Chrome 90+, Edge 90+, Firefox 88+, Safari 14+.
- Tested at desktop (1280px+) and mobile (375px). Fully responsive.
- Light and dark mode via CSS custom properties (`data-theme` attribute on `<html>`).

