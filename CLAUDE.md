# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Running the App

There are no build steps or dependencies. Open `getting-things-done.html` directly in a browser, or use the VS Code **Live Server** extension for auto-reload on save.

There are no tests, no linter, and no package manager.

## Architecture

The entire application is a **single self-contained HTML file** (`getting-things-done.html`). All CSS, JavaScript, and HTML live in this one file in order: `<style>` block → `<body>` HTML → `<script>` block.

### State

A single `state` object is the source of truth for all data:

```js
state = {
  projects: [...],
  tasks:    [...],
  meetings: [...],
  bucket:   [...],  // Collect page items
  ideas:    [...],  // Ideas page items
  calView, calYear, calMonth, calWeekStart, calDay,
  currentView       // active page name string
}
```

State is persisted entirely to `localStorage` under the key `taskcal_data` via `saveToStorage()` / `loadFromStorage()`. Every mutation must call `saveToStorage()` followed by the appropriate render function.

### Areas

`state.areas` is an array of `{id, name}` objects. The defaults (Work, Life, University) are seeded from `DEFAULT_STATE.areas` and persisted. Users can add custom areas at any time via the "＋ New area…" option in any Area dropdown — `handleAreaSelect(el)` detects this sentinel value, prompts for a name, pushes to `state.areas`, saves, and rebuilds the dropdown. `_populateAreaSelect(el)` populates any `<select>` with the current areas list plus the add-new option.

The Tasks page renders a horizontal column per area (`renderTasks`). Each column shows tasks whose `area` field matches, with an inline "Add task" button that calls `openTaskModalForArea(areaId)` → `openTaskModal(null, null, areaId)` to pre-fill the Area field. Tasks with no area or an unknown area ID appear in an "Other" column (only rendered when such tasks exist).

### Navigation

`navigate(view)` is the single entry point for switching pages. It:
1. Removes `.active` from all `.view` divs and `.nav-item` elements
2. Shows `#view-{view}` and highlights `#nav-{view}`
3. Calls the matching render function: `renderDashboard`, `renderCalendar`, `renderTasks`, `renderIdeaView`, or `renderBucketView`
4. Sets `state.currentView`

Sidebar order: Dashboard → Collect → All Tasks → Ideas → Calendar → Projects.

`navigateProject(id)` is a special case that reuses `view-tasks` with a project filter applied.

`refreshView()` re-renders whichever view is currently active — call this after any state mutation that happens outside a full navigate (e.g. toggling a task done from a modal). Handles all five views including `bucket`.

### Render Pattern

Each view has a `render*()` function that rebuilds its DOM from scratch using `innerHTML`. There is no virtual DOM or reactive framework. When data changes, the relevant render function is called to redraw the entire view.

### Data Entities

| Entity | Key fields | Notes |
|---|---|---|
| Task | `id, name, area, project, priority, due, time, notes, done, completedAt, created` | `area` is an ID reference to `state.areas`; recurrence fields (`recur`, `recurEnd`) are hardcoded to `'none'`/`''` (removed from modal); `completedAt` is ISO timestamp set/cleared by `toggleTask()` |
| Project | `id, name, desc, area, color, start, end, completed` | `area` is an ID reference to `state.areas`; `color` is a hex string; `completed` is a boolean toggled by `toggleProjectComplete()` |
| Area | `id, name` | Stored in `state.areas`; defaults are Work, Life, University; user can add custom areas via the "＋ New area…" option in any Area dropdown |
| Meeting | `id, name, date, startTime, endTime, location, project, recur, recurEnd, attendees, type:'meeting'` | Shares calendar rendering with tasks via `isMeeting` flag |
| Bucket item | `id, text, created, completed, processed, processedAs` | `processedAs` is `task/project/meeting/idea` |
| Idea item | `id, text, created, developed, processed, processedAs` | Mirror of bucket; `developed` ≡ bucket's `completed` |

### Recurring Events

`expandTasksRange(from, to)` expands all tasks and meetings into individual instances for a date range by walking recurrence rules via `nextOccurrence()`. This is the function called by every calendar view. It returns a flat array of instance objects with an added `instanceDate` field. The base task/meeting objects in `state` are never duplicated — recurrence is always computed on the fly.

### Pages / Views

| View ID | Nav ID | Render function |
|---|---|---|
| `view-bucket` | `nav-bucket` | `renderBucketView()` + `renderBucketList()` |
| `view-ideas` | `nav-ideas` | `renderIdeaView()` + `renderIdeaList()` |
| `view-dashboard` | `nav-dashboard` | `renderDashboard()` |
| `view-calendar` | `nav-calendar` | `renderCalendar()` → `renderMonthView/WeekView/DayView()` |
| `view-tasks` | `nav-tasks` | `renderTasks()` |

### Collect → Ideas Transfer

When a bucket item is processed as `'idea'`, `processAs()` both marks the bucket item processed (`processedAs: 'idea'`) and pushes a new entry into `state.ideas`. No modal opens — it is a silent transfer.

### Undoing a Collect Conversion

Processed bucket items (`.bucket-item.processed`) render an **Undo** button instead of the normal action row. Clicking it calls `undoBucketProcess(id)`:

- For `processedAs === 'idea'`: finds the first matching unprocessed idea in `state.ideas` by text and removes it, then clears the bucket item's `processed` / `processedAs` flags.
- For `task / project / meeting`: clears the flags only — the created entity is kept (there is no reference back to it to remove).

The `.bucket-item.processed` CSS rule has no `pointer-events:none` so the Undo button remains clickable.

### Ideas → Collect Transfer

Ideas cannot be converted to Task / Project / Meeting directly. Instead, the **Collect** button on each idea card calls `moveIdeaToCollect(id)`:

- Marks the idea `processed: true, processedAs: 'collect'`
- Pushes a new bucket item with the same text into `state.bucket`
- No modal opens — it is a silent transfer

### Undoing an Ideas → Collect Transfer

Processed idea items render an **Undo** button. Clicking it calls `undoIdeaProcess(id)`:

- For `processedAs === 'collect'`: finds the first matching unprocessed bucket item by text and removes it, then clears the idea's `processed` / `processedAs` flags.
- The idea item's `developed` state is preserved through the undo.

### Collect → Task / Project / Meeting (deferred processing)

When a bucket item is processed as `'task'`, `'project'`, or `'meeting'`, the item is **not** marked processed immediately. Instead:

1. `_pendingBucketId` is set to the bucket item's ID
2. The relevant modal opens pre-filled with the item's text
3. On **save** (`saveTask` / `saveProject` / `saveMeeting`), the bucket item is marked `processed: true` with the correct `processedAs` type, then `_pendingBucketId` is cleared before `saveToStorage()` is called
4. On **cancel** (Cancel button, Escape, or backdrop click), `closeModal()` clears `_pendingBucketId` and the bucket item is left untouched

`closeModal(id)` always clears `_pendingBucketId`. The Escape key and backdrop click handlers call `closeModal(m.id)` rather than removing the class directly, so the clear is guaranteed on all cancel paths.

### Modals

All create/edit forms are full-page overlays (`.modal-overlay`) toggled with `.open`. They are opened via `openTaskModal(editId?, prefillName?, prefillArea?)`, `openProjectModal(editId?, prefillName?)`, `openMeetingModal(editId?, prefillDate?, prefillTime?, prefillName?)`. Prefill parameters are only applied when `editId` is null/undefined (new items). Pressing Escape or clicking the backdrop closes any open modal.

### Theming

Light/dark mode uses CSS custom properties on the `<html>` element (`data-theme="light|dark"`). The full palette of `--color-*` variables is defined in `:root` (light) and `[data-theme="dark"]`. Always use these variables — never hardcode colours in new CSS or inline styles.

### Badges

The sidebar badges on Collect and Ideas show the count of unprocessed/undeveloped items. They are updated in `refreshView()` and `renderBucketView()` / `renderIdeaView()`. Always call `refreshView()` after any mutation that could change these counts.

### Startup Screen

On every page load a full-screen overlay (`#startup-screen`, `z-index:400`) is shown before the sidebar and main layout. It provides a distraction-free capture surface that feeds directly into `state.bucket`.

- `startupCapture()` — creates a bucket item and calls `renderStartupRecents()` to update the live list; does not call `renderBucketView()` (that happens after the screen is dismissed)
- `closeStartup()` — adds the `.exit` class (CSS fade+slide), waits 270 ms, hides the element, then calls `navigate('bucket')`
- `renderStartupRecents()` — renders the last 7 bucket items below the textarea; called on init and after each capture
- Keyboard: `Enter` captures, `Shift+Enter` newline, `Esc` dismisses

The overlay is dismissed for the rest of the session via `closeStartup()` (sets `display:none`). `showStartup()` reverses this — it restores `display:flex`, forces a reflow, removes the `.exit` class so the CSS transition replays, calls `renderStartupRecents()`, and re-focuses the textarea. It is triggered by the **Quick Capture** button in the sidebar footer.

### renderTasks sorting

`renderTasks()` sorts each area column so completed tasks appear below uncompleted: `tasks.sort((a,b)=>a.done===b.done?(a.due||'').localeCompare(b.due||''):a.done?1:-1)`.

### Collect list sorting

`renderBucketList()` when `bucketFilter === 'all'` sorts so processed/completed items appear below unprocessed: `items.sort((a,b) => (!!a.processed||!!a.completed) - (!!b.processed||!!b.completed))`.

### Project completion

`toggleProjectComplete(id)` flips `p.completed`, saves, calls `refreshView()` and `renderSidebar()`. Completed projects are greyed out (`.project-card.completed { opacity:.45; filter:grayscale(.4) }`) and shown with strikethrough in the sidebar. The `completed` flag is preserved through edits in `saveProject()`.

### Excel (.xlsx) Import / Export

`exportXLSX()` — uses SheetJS (loaded from jsDelivr CDN) to build a workbook with five sheets and triggers download as `taskcal-data.xlsx`.

`importXLSX(input)` — reads the selected `.xlsx` file as an ArrayBuffer, parses it with `XLSX.read()`, then processes whichever of the four named sheets are present, upserting rows by ID into `state`. Calls `saveToStorage()`, `refreshView()`, `renderSidebar()`, re-opens the modal, and shows a toast with added/updated counts.

Sheet names and columns:

| Sheet | Columns |
|---|---|
| `Tasks` | `id, name, project_id, project_name, priority, due, time, recur, recurEnd, notes, done, completedAt, created` |
| `Projects` | `id, name, desc, color, start, end` |
| `Meetings` | `id, name, date, startTime, endTime, location, project_id, project_name, recur, recurEnd, attendees, notes` |
| `Collect` | `id, text, created, completed, processed, processedAs` |
| `Ideas` | `id, text, created, developed, processed, processedAs` |

`project_name` in the Tasks sheet is derived at export time from `state.projects`; on import it is ignored (the `project_id` column drives the join). Both export and import guard against `XLSX` being undefined (CDN not loaded) and alert the user.

### CSV Import / Export

`openDataModal()` renders the Import / Export modal dynamically from current `state` counts and attaches it to `#data-modal`. The modal is opened via the spreadsheet-grid icon in the topbar. The modal shows the Excel section first, then individual CSV rows below.

`exportCSV(type)` — builds a CSV string via `_toCSV()` and triggers a download. Supported types and their column sets:

| Type | Columns |
|---|---|
| `tasks` | `id, name, project_id, project_name, priority, due, time, recur, recurEnd, notes, done, completedAt, created` |
| `projects` | `id, name, desc, color, start, end` |
| `meetings` | `id, name, date, startTime, endTime, location, project_id, project_name, recur, recurEnd, attendees, notes` |
| `bucket` | `id, text, created, completed, processed, processedAs` |
| `ideas` | `id, text, created, developed, processed, processedAs` |

`attendees` is serialised as a pipe-separated string (`Alice|Bob|Carol`) in both CSV and xlsx; on import it is split back into an array.

`importCSV(type, input)` — reads the selected file with `FileReader`, parses it via `_parseCSV()`, and upserts rows into `state` by ID (existing ID → update in place; unknown ID → append). After merging it calls `saveToStorage()`, `refreshView()`, `renderSidebar()`, and re-opens the modal to refresh counts. Import feedback is shown via the save toast.

Private helpers: `_csvEscape()`, `_toCSV()`, `_downloadCSV()`, `_parseCSV()` — prefixed with `_` to distinguish them from public app functions.
