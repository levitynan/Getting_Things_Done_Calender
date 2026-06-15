# TaskCal — README

**TaskCal** is a self-contained, single-file HTML application for managing tasks, projects, meetings, and recurring events with a built-in calendar. No installation, no server, no internet connection required — open the file in any modern browser and start working.

---

## Getting Started

1. Save `getting-things-done.html` anywhere on your computer.
2. Open it in a browser (Chrome, Edge, Firefox, Safari).
3. Optionally, use the **Live Server** extension in VS Code for a smoother development experience.

> All data is saved automatically to your browser's `localStorage`. Clearing your browser data will erase saved tasks and projects, so use the **Export .ics** feature regularly as a backup.

---

## Navigation

The left sidebar contains six main sections:

| Section | Purpose |
|---|---|
| **Dashboard** | Overview of projects, KPIs, and upcoming actions |
| **🪣 Collect** | Rapid-capture inbox for any thought, action, or to-do |
| **Next Actions** | Action list organised into area columns (Work, Life, University, custom) |
| **💡 Ideas** | Capture ideas and develop them into actions, projects, or meetings |
| **Calendar** | Monthly, weekly, and daily calendar views |
| **Projects** | All projects grouped by area |

The top bar contains two utility buttons alongside **New ▾**:
- **⊞ (grid icon)** — opens the Import / Export CSV modal
- **↓ (download icon)** — exports a `.ics` calendar file

Click **New ▾** to create an Action, Project, or Scheduled Meeting from anywhere in the app.

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
- Each captured item can be processed into an **Action**, **Project**, **Meeting**, or **Idea** using the action buttons on the card.
- Clicking **Done** opens a small modal where you can optionally assign an **Area** and **Project** before marking the item complete. On confirm, a completed action is automatically created and assigned to the selected project, so the item appears in the project's action list marked as done. Completed items in Collect also display the assigned area and project as colour-coded badges.
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
- **Collect** — move the idea back to the Collect inbox, where it can then be converted into an Action, Project, or Meeting.
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

Actions and projects can be assigned to an **area**: **Work**, **Life**, or **University**. You can also create custom areas at any time by choosing **＋ New area…** from any Area dropdown.

### Next Actions Page

The Next Actions page displays a separate column for each area. Actions with no area (or an unrecognised area) appear in an **Other** column, which is only shown when such actions exist. Click **Add action** inside any column to open the action form with that area pre-filled.

The page header displays:
- **Left side:** KPI stats showing **Incomplete Actions** and **Completed** count
- **Right side:** Filters for **All Areas**, **All Status**, and **Sort** options (Due Date, Priority, Duration, Oldest First, Newest First, Name)

The sidebar badge for Next Actions shows the count of **incomplete** (not done) actions.

---

## Actions

### Creating an Action

Click **New ▾ → Action**, or click any day on the calendar to open the action form pre-filled with that date.

| Field | Notes |
|---|---|
| **Title** | Required |
| **Waiting on** | Toggle "Is this action waiting on anything?" — reveals a text field to describe what you're blocked on |
| **Area** | Work / Life / University / custom |
| **Project** | Optional — assign to an existing project |
| **Priority** | Low / Medium / High |
| **Time Required** | Under 10 mins / Under 30 mins / Under an hour / Should be turned into a project |
| **Due Date** | Date picker |
| **Due Time** | Optional time |
| **Notes** | Free-text notes field |

When a "waiting on" value is set, an amber **⏳** badge appears on the action card showing what the action is blocked on. The badge contains an inline **✓** button — clicking it marks the waiting condition as fulfilled without marking the action done. The badge turns green to show the blocker is resolved. A **Time Required** badge appears in the matching colour (green / blue / amber / red) when a duration is set.

The modal footer has three buttons: **Cancel**, **Convert to Project** (carries name, area, and notes into a new project form), and **Save Action**.

### Managing Actions

- **Check** the circle on any action card to mark it done (strikethrough applied).
- **Star** (⭐) — hover an action card to reveal a star button. Click it to add the action to the **This Week** focus block at the top of its area column. Click again to remove it.
- **Convert to project** — hover an action card to reveal a folder icon. Click it to delete the action and open a new project form pre-filled with the action's name and area.
- **Edit** (pencil icon) to reopen the action form.
- **Delete** (bin icon) to remove the action permanently.
- Completed actions remain visible but styled as done and sorted below uncompleted actions in each column.

### This Week

Mark any action as "This Week" using the star button in the action card's hover controls. Starred incomplete actions appear in an amber-highlighted block at the top of their area column, separated from the rest by a divider. The block shows the count of starred actions. Click the star again to remove an action from the block.

### Action States

Actions support three states, cycled via the circle button on calendar pills:

| State | Appearance |
|---|---|
| **Pending** | Empty circle |
| **In Progress** | Amber fill with `~` |
| **Done** | Filled circle with `✓` |

### Action Complete modal

When an action is marked done, an **Action complete** modal appears. It provides:

- A **Notes** field pre-filled with the action's existing notes. Any changes are saved back to the action when the modal is dismissed (via any button, Escape, or clicking outside).
- Four next-step buttons: **New Action**, **New Meeting**, **Turn into Project**, or **Nothing, I'm done**. Choosing one closes the modal and opens the relevant creation form, pre-filled with the same area and project as the completed action.

---

## Projects

Projects group related actions together and appear as coloured cards in the Projects view and Dashboard.

### Creating a Project

Click **New ▾ → Project**.

| Field | Notes |
|---|---|
| **Name** | Required |
| **Outcome** | Required description of the desired result |
| **Area** | Work / Life / University / custom |
| **Parent Project** | Optional — nest this project inside another project (filtered to the selected area) |
| **Colour** | Pick from 8 colour swatches |
| **Start / End Date** | Optional date range |

### Project Nesting

Projects can be organised hierarchically. Assign a **Parent Project** when creating or editing a project to make it a sub-project. In the Projects view:

- Only top-level (root) projects appear as standalone cards in the grid.
- Expand a parent project card to reveal a **Sub-projects** section, which lists child project cards (themselves expandable).
- A **"N sub-projects"** badge on the card shows the count of direct children.
- Clicking a parent project's name shows its actions **and all descendant sub-project actions** together.

### Projects Page

The Projects page displays all root projects grouped by area in a card grid. The page header shows three KPI cards — **Incomplete**, **Ongoing**, and **Completed** counts — on the left, with a **New Project** button on the right.

Each project card has:
- **+ button** — create a new action, meeting, or sub-project pre-assigned to that project
- **Chevron** — expand to show sub-projects, actions (with move up/down, schedule, convert to project, edit, delete buttons), and meetings inline
- **Complete toggle** — mark the project done/active (hidden for Ongoing projects)
- **Edit / Delete** buttons — revealed on hover below the card header

### Linked Ideas Folder

Every project card has a lightbulb icon button that links the project to an idea folder:

- **No folder yet** — clicking the button automatically creates a new root idea folder named after the project and links the two together. The folder is immediately visible in the Ideas > File Explorer tab.
- **Already linked** — clicking the button navigates directly to the Ideas > File Explorer tab with that folder selected.

From the folder side, a linked folder shows a gold lightbulb indicator in the folder tree. A blue folder button next to it navigates back to the Projects page.

Links are bidirectional but are **not preserved** when exporting and re-importing via XLSX or CSV — they must be recreated manually after a full data round-trip.

### Ongoing Projects

Mark a project as **Ongoing** when it is a continuous process with no defined end date (e.g. a recurring area of responsibility). In the project modal, check **Ongoing** to:
- Disable and clear the End Date field
- Hide the complete-toggle button on the card
- Show an **∞ Ongoing** badge in the card meta row

Ongoing projects count separately from Incomplete and Completed in the header KPIs.

### Project Templates

Templates let you pre-define a list of initial actions that are automatically created whenever a new project is started from that template.

**Managing templates** — open the template manager from either the **Manage Templates** button on the Projects page header, or the **Manage** button next to the Template dropdown in the New Project form. The template manager is a two-panel modal:
- **Left panel** — lists all saved templates. Click one to select it.
- **Right panel** — edit the template name and its action list. Each action has a name, optional priority, and optional duration. Use the **↑ / ↓** buttons on the left of each row to reorder actions. Use **+ Add action** to append a row and **×** to remove one. Changes save immediately.
- Use **+ New Template** to create a blank template, and **Delete template** to remove the selected one.

**Save existing project as template** — when editing a project, a **Save as Template** button appears in the modal footer. Clicking it creates a new template named after the project, pre-populated with all of the project's non-done actions (preserving their priority and duration).

**Using a template** — the Template dropdown appears in the New Project form (not shown when editing an existing project). Select a template to see a preview of its actions. On save, each named action in the template is created as a pending action assigned to the new project, in the order defined in the template.

### Project View

Click a project card to switch to its filtered action list, showing actions belonging to that project and all its sub-projects.

### Completing a Project

Click the **circle icon** on any project card to mark the project complete. Completed projects are greyed out. Click the icon again to reactivate.

---

## Meetings

Meetings are scheduled events distinct from actions — they support additional fields suited to collaborative events.

### Creating a Meeting

Click **New ▾ → Scheduled Meeting**.

| Field | Notes |
|---|---|
| **Title** | Required |
| **Date / Start Time** | Required |
| **Duration** | In minutes |
| **Area** | Work / Life / University / custom |
| **Location / Link** | Room name or URL (e.g. Google Meet link) |
| **Recurrence** | None / Daily / Weekly / etc. |
| **Attendees** | Type a name and press Enter or click Add — shown as chips |
| **Agenda / Notes** | Free-text |

Meetings appear on the Calendar in purple and in the Dashboard upcoming panel.

---

## Calendar

### Views

Switch between **Month**, **Week**, and **Day** using the buttons in the top-right of the calendar toolbar.

| View | Description |
|---|---|
| **Month** | Full month grid. Actions and meetings shown as colour-coded pills. Click a day to see a detail panel below. |
| **Week** | 7-column time grid (48px per hour). All-day events shown above the grid. A red line marks the current time. |
| **Day** | Single-day time grid with full event details. |

### Navigation

- **← →** arrows step backward/forward by month, week, or day depending on the active view.
- **Today** button jumps back to the current date.
- Click any **day number** in week view to jump to that day in day view.
- Click any **calendar event pill** to open its edit form.

### Schedule Action

Click **Schedule Action** in the calendar toolbar to open a searchable list of your Next Actions. Select any action and click **Schedule Now** to place it on the calendar at the current time (rounded to 5 minutes).

- **Scheduling again**: scheduling an already-scheduled action adds a new time slot — the original slot remains. Both appear as separate calendar pills.
- **Undo**: an undo toast appears for 5 seconds after scheduling, letting you reverse the last schedule operation.
- **Unschedule**: click the **×** on any calendar pill to remove that specific time slot from the calendar.
- **Partial actions**: in-progress (partially complete) actions appear in the "To Do" filter and can be scheduled.

---

## Dashboard

The Dashboard shows:

- **KPI cards** — Total projects, total actions, completed actions, overdue actions, actions due in the next 7 days.
- **Project cards** — Each project with a progress bar (completed / total actions), description, and date range.
- **Upcoming Actions** — A panel listing all actions due in the next 14 days, grouped by date, with project colour coding.

---

## Exporting to a Calendar

Click the **↓ download icon** in the top bar to export a `.ics` file covering the current month ±1–3 months. The file includes all actions and meetings with their recurrence rules.

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
| **Actions** | All actions — area, project, priority, duration, recurrence, notes, waiting-on, waiting-fulfilled, additional schedule slots, sort order, this-week flag, completion state, timestamps |
| **Projects** | All projects with area, colour, dates, description, ongoing flag, completion state, and parent project |
| **Meetings** | All meetings — date, times, area, location, attendees, recurrence, notes |
| **Collect** | All Collect items with processing state, and any area/project assigned via Done modal |
| **Ideas** | All Ideas items with development state and folder assignment |
| **Areas** | All areas (including custom user-created areas) |
| **IdeaFolders** | All idea folder definitions with parent–child relationships |

The exported file (`taskcal-data.xlsx`) can be opened and edited directly in Excel or Google Sheets. Re-importing merges by ID — existing rows are updated, new rows are appended.

> **Note:** The Excel feature requires an internet connection on first load to fetch the SheetJS library from a CDN. Subsequent loads use the browser cache.

### CSV (individual files)

Each data type can also be exported and imported independently as a `.csv` file:

| Export file | Contents |
|---|---|
| `taskcal-tasks.csv` | Actions (with area, project, priority, duration, waiting-on, this-week flag, partial state) |
| `taskcal-projects.csv` | Projects (with area, ongoing flag, completion state, parent project) |
| `taskcal-meetings.csv` | Meetings (with area, attendees as pipe-separated values) |
| `taskcal-collect.csv` | Collect items (with area/project if set via Done modal) |
| `taskcal-ideas.csv` | Ideas (with folder assignment) |
| `taskcal-areas.csv` | Areas (including custom areas) |
| `taskcal-idea-folders.csv` | Idea folders (with parent–child structure) |

**Importing** merges by ID — rows whose ID already exists are updated in place; new IDs are appended. Re-importing an export will not create duplicates. A toast notification confirms how many rows were added and updated.

CSV files can be opened and edited in Excel, Google Sheets, or any text editor before re-importing.

---

## Light / Dark Mode

Click **Toggle Theme** at the bottom of the sidebar to switch between light and dark mode. The app also respects your system preference by default.

---

## Resetting Data

Click **Reset All Data** at the bottom of the sidebar to wipe all actions, projects, meetings, and collected items and restore the built-in sample data. **This operation cannot be undone.**

---

## Data Storage

All data is stored in your browser's `localStorage` under the key `taskcal_data`. Because this is browser-local storage:

- Data does not sync across devices or browsers.
- Clearing browser site data will delete everything.
- The app works fully offline.

**Recommended backup:** Export CSV files via the ⊞ grid icon, or export a `.ics` file via the ↓ download icon. CSV exports capture all data including Collect and Ideas items that `.ics` does not include.

---

## Technical Notes

- Single `.html` file — no build tools, no package manager.
- Fonts loaded from Google Fonts CDN; SheetJS loaded from jsDelivr CDN. Both require internet on first load and are cached by the browser thereafter. All other features (actions, calendar, CSV) work fully offline.
- Compatible with Chrome 90+, Edge 90+, Firefox 88+, Safari 14+.
- Tested at desktop (1280px+) and mobile (375px). Fully responsive.
- Light and dark mode via CSS custom properties (`data-theme` attribute on `<html>`).
