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

The Next Actions page renders a horizontal column per area (`renderTasks`). Each column shows tasks whose `area` field matches, with an inline "Add action" button that calls `openTaskModalForArea(areaId)` → `openTaskModal(null, null, areaId)` to pre-fill the Area field. Tasks with no area or an unknown area ID appear in an "Other" column (only rendered when such tasks exist).

### Navigation

`navigate(view)` is the single entry point for switching pages. It:
1. Removes `.active` from all `.view` divs and `.nav-item` elements
2. Shows `#view-{view}` and highlights `#nav-{view}`
3. Calls the matching render function: `renderDashboard`, `renderCalendar`, `renderTasks`, `renderIdeaView`, `renderBucketView`, or `renderProjectsView`
4. Sets `state.currentView`

Sidebar order: Dashboard → Collect → Next Actions → Projects → Ideas → Calendar.

`navigateProject(id)` is a special case that reuses `view-tasks` with a project filter applied. When `state.currentView` is `'project-{id}'`, `renderTasks()` calls `_getProjectAndDescendantIds(id)` and filters tasks to that project and all its descendants. It is called from project cards in the Projects view and Dashboard.

`refreshView()` re-renders whichever view is currently active — call this after any state mutation that happens outside a full navigate (e.g. toggling a task done from a modal). Handles all five views including `bucket`.

### Render Pattern

Each view has a `render*()` function that rebuilds its DOM from scratch using `innerHTML`. There is no virtual DOM or reactive framework. When data changes, the relevant render function is called to redraw the entire view.

### Data Entities

| Entity | Key fields | Notes |
|---|---|---|
| Task | `id, name, area, project, priority, due, time, notes, waiting, done, partial, scheduledSlots, completedAt, created` | `area` is an ID reference to `state.areas`; `partial` is a boolean for in-progress state; `waiting` is a string for what the action is blocked on; `scheduledSlots` is an array of `{due, time}` for additional calendar slots beyond the primary `due`/`time`; `completedAt` is ISO timestamp |
| Project | `id, name, desc, area, color, start, end, completed, parentId` | `parentId` is null for root-level projects or an ID of the parent project; `color` is a hex string; `completed` is a boolean toggled by `toggleProjectComplete()` |
| Area | `id, name` | Stored in `state.areas`; defaults are Work, Life, University; user can add custom areas via the "＋ New area…" option in any Area dropdown |
| Meeting | `id, name, date, startTime, endTime, location, area, project, recur, recurEnd, attendees, type:'meeting'` | `area` is an ID reference to `state.areas`; shares calendar rendering with tasks via `isMeeting` flag |
| Bucket item | `id, text, created, completed, processed, processedAs, area?, project?` | `processedAs` is `task/project/meeting/idea` (internal values unchanged); `area` and `project` are optional IDs set when marking an item Done via the Done modal |
| Idea item | `id, text, created, developed, processed, processedAs, folderId?` | Mirror of bucket; `developed` ≡ bucket's `completed`; `folderId` optional reference to `state.ideaFolders` |
| Idea folder | `id, name, parentId` | Forms a tree structure; `parentId` is null for root-level folders |

### Recurring Events

`expandTasksRange(from, to)` expands all tasks and meetings into individual instances for a date range by walking recurrence rules via `nextOccurrence()`. This is the function called by every calendar view. It returns a flat array of instance objects with added `instanceDate` and `_slotIndex` fields. The base task/meeting objects in `state` are never duplicated — recurrence is always computed on the fly.

Each task instance gets `_slotIndex: -1` for the primary `due`/`time` slot. Additional entries in `task.scheduledSlots` produce separate instances with `_slotIndex: 0, 1, 2…`. The `_calUnscheduleBtn` and `unscheduleAction` functions use this index to remove only the correct slot.

### Date Utilities

`dateStr(d)` — converts a Date object to a `YYYY-MM-DD` string using **local** date components (`getFullYear`, `getMonth`, `getDate`). Never use `toISOString().slice(0,10)` — that produces UTC dates and causes off-by-one errors for users in positive UTC offsets.

`_stepDay(ds, n)` — timezone-safe day stepping from a date string; uses local arithmetic to avoid UTC conversion bugs.

`addDays(d, n)` — adds n days to a Date using `setDate`/`getDate` (local time safe).

### Pages / Views

| View ID | Nav ID | Render function |
|---|---|---|
| `view-bucket` | `nav-bucket` | `renderBucketView()` + `renderBucketList()` |
| `view-ideas` | `nav-ideas` | `renderIdeaView()` — dispatches to `renderIdeaList()` (Unfiled tab) or `renderIdeaFolderContent()` (File Explorer tab) based on `_ideaTab` |
| `view-dashboard` | `nav-dashboard` | `renderDashboard()` |
| `view-projects` | `nav-projects` | `renderProjectsView()` — projects grouped into area rows (grid layout); each area section only shows root projects (`isRoot(p)` — parentId is null or not found); sub-projects appear inside the parent card's expanded section under a "Sub-projects" heading; every card has a chevron that expands via `_expandedProjects` Set + `toggleProjectExpand(id, e)`; a **+** button on each card opens `openTaskModal` pre-filled with that project |
| `view-calendar` | `nav-calendar` | `renderCalendar()` → `renderMonthView/WeekView/DayView()` |
| `view-tasks` | `nav-tasks` | `renderTasks()` — the "Next Actions" page; sidebar badge shows incomplete (not done) count |

### Project Nesting

Projects support a `parentId` field forming a tree. Key rules:

- `isRoot(p)` — returns true when `!p.parentId || !projectIds.has(p.parentId)`. Only root projects appear as top-level cards in the Projects view area sections.
- `_getProjectAndDescendantIds(id)` — BFS returning a Set of the given project's ID plus all descendants. Used in `navigateProject` so clicking a parent project shows its tasks AND all sub-project tasks.
- `_populateProjectParentSelect(el, editId, areaId)` — populates the Parent Project dropdown, excluding the project being edited and its descendants (prevents circular references), filtered to the selected area.
- `_filterProjectParent()` — called when the Area dropdown changes in the project modal to re-filter the Parent Project options.
- Sub-projects appear inside the expanded section of the parent card (after a "Sub-projects" label), rendered recursively via `cardHtml`. Clicks on sub-project cards are wrapped with `event.stopPropagation()` so they don't bubble to the parent's navigate handler.
- A "N sub-projects" badge is shown in the project meta row when children exist.

### Action Scheduling

The **Schedule Action** button on the Calendar toolbar opens `#schedule-action-modal` with filter/sort controls. `scheduleActionNow(id)` sets the action's `due` and `time` to the current date/time (rounded to 5 minutes).

**Multiple schedule slots**: if the action already has a `due` date, a second call to `scheduleActionNow` pushes `{due, time}` into `task.scheduledSlots` instead of overwriting. Each slot produces a separate calendar pill with its own × unschedule button. `unscheduleAction(id, slotIndex)` removes only the targeted slot (`slotIndex === -1` removes the primary `due`/`time`; `slotIndex >= 0` splices from `scheduledSlots`).

**Undo**: `_schedUndoState` stores either `{id, prevDue, prevTime}` (first schedule) or `{id, addedSlot: true}` (additional slot). `_undoScheduleAction()` pops the last slot or restores the previous due/time accordingly.

**Status filter**: the "To Do" filter in the schedule modal includes both pending and partial (in-progress) tasks — `!t.done`. The "In Progress" filter shows only partial tasks.

### Task State Cycling

`cycleTaskState(id)` cycles: none → partial → done (with follow-up modal) → none. The `partial` field is a boolean on the task object. Calendar pills show a small circle button via `_calCompleteBtn(t)` that calls `cycleTaskState`.

`toggleTask(id)` (used on Next Actions cards) also triggers the follow-up modal when marking done.

### Waiting On

Tasks have an optional `waiting` string field. The action modal shows "Is this action waiting on anything?" checkbox; checking it reveals a text input for what the action is blocked on. When set, an amber `⏳ [text]` badge appears on the task card in the meta row.

### Collect Done with Area / Project

Clicking **Done** on a bucket item opens `#bucket-done-modal` via `openBucketDoneModal(id)`. The modal shows the item text (read-only) plus optional Area and Project dropdowns. The Project dropdown is filtered by the selected area via `_filterBucketDoneProjects()` — the same pattern as the task modal. On confirm, `saveBucketDone()` writes `area` and `project` onto the bucket item, sets `item.completed = true`, and calls `saveToStorage()` + `renderBucketView()`. Completed items with an area/project assigned display those as colour-coded badges in their meta row.

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

### Ideas Page: Unfiled and File Explorer Tabs

The Ideas page has two tabs controlled by `_ideaTab` (either `'unfiled'` or `'fileexplorer'`):

**Unfiled tab** (`renderIdeaList()`) — displays only ideas with no folder (`!i.folderId`). Ideas are edited inline with full action buttons (Developed toggle, Collect button, Move to folder, Discard). Newlines in idea text are preserved via `white-space: pre-wrap` CSS.

**File Explorer tab** (`renderIdeaFolderContent()`) — split layout with a collapsible folder sidebar on the left and a main content pane on the right:

- **Sidebar** (`#idea-folder-sidebar`, `.idea-folder-sidebar`) — renders `#idea-folder-tree` via `renderIdeaFolderTree()`. Shows an "All Ideas" entry at the top, followed by the root/subfolder tree. Hovering a folder reveals action buttons: **+** (new subfolder), **✎** (rename), **✕** (delete). A thin toggle strip (`#sidebar-pin-btn`, `.idea-sidebar-toggle`) sits on the divider between sidebar and content; clicking it collapses or expands the sidebar by toggling `.collapsed` on `#idea-folder-sidebar` via `toggleIdeaSidebarPin()`.
- **Content pane** (`#idea-folder-content-pane`) — renders items for the selected folder via `renderIdeaFolderItems()`. Items are sorted oldest-to-newest by `created`. Each item is fully editable with Developed toggle, Unfile, Move to folder, and Discard buttons.

`switchIdeaTab(tab)` toggles between tabs. When switching to File Explorer, if `_selectedIdeaFolderForView` is null it defaults to `'all'` (All Ideas), then calls `renderIdeaFolderContent()`.

### Idea Folders

`state.ideaFolders` is an array of `{id, name, parentId}` objects. Folders form a tree structure via `parentId` (null means root level).

- `_selectedIdeaFolderForView` — drives the content pane: `null`=empty prompt, `'all'`=All Ideas, `'unfiled'`=no folder, else a folderId
- `_ideaSidebarPinned` — boolean; false collapses the sidebar via CSS `width:0` (not `display:none`) so the transition animates
- `_expandedIdeaFolders` — Set tracking which folders are expanded in the tree

**Render functions:**
- `renderIdeaFolderContent()` — calls `renderIdeaFolderTree()` then `renderIdeaFolderItems()`
- `renderIdeaFolderTree()` — rebuilds `#idea-folder-tree` from `state.ideaFolders`; renders All Ideas entry then recursive `renderTreeNode(null)`. The child filter uses `(f.parentId || null) === parentId` — the `|| null` normalises `undefined` parentId values that may exist in older saved data.
- `renderIdeaFolderItems()` — rebuilds `#idea-folder-items` based on `_selectedIdeaFolderForView`
- `selectIdeaFolderForView(id)` — sets `_selectedIdeaFolderForView` and re-renders both panes
- `toggleIdeaSidebarPin()` — toggles `_ideaSidebarPinned` and `.collapsed` class on sidebar

**Folder operations:**
- `createIdeaFolder(parentId)` — prompts for name, creates folder as child of parentId (or root if null); "New" button in sidebar header always passes null; hover "+" button on a folder passes that folder's id
- `renameIdeaFolder(id)` — prompts for new name
- `deleteIdeaFolder(id)` — deletes folder and all descendants; ideas in deleted folders become unfiled; resets `_selectedIdeaFolderForView` if the deleted folder was selected
- `openMoveIdeaFolderModal(folderId)` — opens modal to move folder to a different parent; prevents moving a folder into itself or its descendants via the `cannotMove` Set
- `confirmMoveFolderToParent()` — updates folder's parentId and re-renders

**Idea-folder association:**
- `unfileIdea(id)` — sets `idea.folderId = null`
- `openIdeaFolderMoveModal(id)` — opens modal to assign idea to a folder
- `confirmMoveIdeaToFolder()` — updates idea's folderId

When capturing ideas in File Explorer tab, new ideas are assigned to `_selectedIdeaFolderForView` if it is a folderId; in Unfiled tab, new ideas have no folder.

### Collect → Task / Project / Meeting (deferred processing)

When a bucket item is processed as `'task'`, `'project'`, or `'meeting'`, the item is **not** marked processed immediately. Instead:

1. `_pendingBucketId` is set to the bucket item's ID
2. The relevant modal opens pre-filled with the item's text
3. On **save** (`saveTask` / `saveProject` / `saveMeeting`), the bucket item is marked `processed: true` with the correct `processedAs` type, then `_pendingBucketId` is cleared before `saveToStorage()` is called
4. On **cancel** (Cancel button, Escape, or backdrop click), `closeModal()` clears `_pendingBucketId` and the bucket item is left untouched

`closeModal(id)` always clears `_pendingBucketId`. The Escape key and backdrop click handlers call `closeModal(m.id)` rather than removing the class directly, so the clear is guaranteed on all cancel paths.

### Modals

All create/edit forms are full-page overlays (`.modal-overlay`) toggled with `.open`. They are opened via:
- `openTaskModal(editId?, prefillName?, prefillArea?, prefillProject?)` — `prefillProject` pre-selects the Project dropdown on new items
- `openProjectModal(editId?, prefillName?)` — also populates the Parent Project dropdown via `_populateProjectParentSelect`
- `openMeetingModal(editId?, prefillDate?, prefillTime?, prefillName?, prefillArea?)`

Prefill parameters are only applied when `editId` is null/undefined (new items). Pressing Escape or clicking the backdrop closes any open modal.

### Task completion follow-up

`toggleTask(id)` shows `#followup-modal` whenever a task is marked done. The modal asks if completing the action created something new — options are **New Action**, **New Meeting**, **Turn into Project**, or **Nothing**. `_followupTask` holds the completed task object. `followupAction(type)` closes the modal and opens the relevant create modal: `openTaskModal(null,'',task.area)`, `openMeetingModal(null,null,null,task.name,task.area)`, or `openProjectModal(null,task.name)`.

### Theming

Light/dark mode uses CSS custom properties on the `<html>` element (`data-theme="light|dark"`). The full palette of `--color-*` variables is defined in `:root` (light) and `[data-theme="dark"]`. Always use these variables — never hardcode colours in new CSS or inline styles.

### Badges

The sidebar badge on Next Actions shows the count of **incomplete** (not done) actions. Badges on Collect and Ideas show the count of unprocessed/undeveloped items. They are updated in `refreshView()`, `renderBucketView()`, and `renderIdeaView()`. Always call `refreshView()` after any mutation that could change these counts.

### Startup Screen

On every page load a full-screen overlay (`#startup-screen`, `z-index:400`) is shown before the sidebar and main layout. It provides a distraction-free capture surface that feeds directly into `state.bucket`.

- `startupCapture()` — creates a bucket item and calls `renderStartupRecents()` to update the live list; does not call `renderBucketView()` (that happens after the screen is dismissed)
- `closeStartup()` — adds the `.exit` class (CSS fade+slide), waits 270 ms, hides the element, then calls `navigate('bucket')`
- `renderStartupRecents()` — renders the last 7 bucket items below the textarea; called on init and after each capture
- Keyboard: `Enter` captures, `Shift+Enter` newline, `Esc` dismisses

The overlay is dismissed for the rest of the session via `closeStartup()` (sets `display:none`). `showStartup()` reverses this — it restores `display:flex`, forces a reflow, removes the `.exit` class so the CSS transition replays, calls `renderStartupRecents()`, and re-focuses the textarea. It is triggered by the **Quick Capture** button in the sidebar footer.

### Task age tint

`_ageTint(createdISO, done)` returns an inline `background: hsla(...)` style for incomplete actions older than one week. Hue shifts from yellow-orange (week 1) toward red (week 4+); alpha increases by 0.06 per week, capped at 0.28. Applied as an inline style on the `.task-item` div in `renderTaskItem`. Done actions receive no tint. Each action card also shows an "Added [date]" label from `t.created`.

### renderTasks sorting and layout

The Next Actions page header displays two sections: left side shows KPI stats (**Incomplete Actions**, **Completed**), right side shows filters (**All Areas**, **All Status**, and **Sort**).

`renderTasks()` updates the action stats at the start, then renders the Next Actions page as a horizontal column per area (`task-columns` / `task-column`), each with an inline "Add action" button. An "Other" column collects actions with no recognised area.

`_sortTasks(tasks, sortBy)` handles all sorting — done tasks always sink to the bottom, then within each group sorts by: `'due'` (ascending, no-date last), `'priority'` (High→Low), `'created'` (oldest first), `'created-desc'` (newest first), or `'name'` (A-Z). The sort is chosen via `#task-sort-filter` select on the right side of the header.

When an area filter is active (`af` is non-empty — set by the **All Areas** dropdown), only that area's column is shown. All area columns are shown when the filter is empty.

### Collect list sorting

`renderBucketList()` when `bucketFilter === 'all'` sorts so processed/completed items appear below unprocessed: `items.sort((a,b) => (!!a.processed||!!a.completed) - (!!b.processed||!!b.completed))`.

### Project completion

`toggleProjectComplete(id)` flips `p.completed`, saves, and calls `refreshView()`. Completed projects are greyed out (`.project-card.completed { opacity:.45; filter:grayscale(.4) }`) on the Dashboard and Projects view. The `completed` flag is preserved through edits in `saveProject()`.

### Projects page header

The Projects page header displays a KPI card showing the **Projects** count on the left side, with a **New Project** button on the right side. `renderProjectsView()` updates the project count at the start of the function.

### Excel (.xlsx) Import / Export

`exportXLSX()` — uses SheetJS (loaded from jsDelivr CDN) to build a workbook and triggers download as `taskcal-data.xlsx`.

`importXLSX(input)` — reads the selected `.xlsx` file as an ArrayBuffer, parses it with `XLSX.read()`, then processes whichever sheets are present, upserting rows by ID into `state`. Calls `saveToStorage()`, `refreshView()`, `renderSidebar()`, re-opens the modal, and shows a toast with added/updated counts.

Sheet names and columns:

| Sheet | Columns |
|---|---|
| `Actions` | `id, name, area_id, area_name, project_id, project_name, priority, due, time, recur, recurEnd, notes, waiting, scheduledSlots, done, partial, completedAt, created` |
| `Projects` | `id, name, desc, area_id, area_name, color, start, end, completed, parentId` |
| `Meetings` | `id, name, date, startTime, endTime, location, area_id, area_name, project_id, project_name, recur, recurEnd, attendees, notes` |
| `Collect` | `id, text, created, completed, processed, processedAs, area_id, project_id` |
| `Ideas` | `id, text, created, developed, processed, processedAs, folderId` |
| `Areas` | `id, name` |
| `IdeaFolders` | `id, name, parentId` |

`area_name` and `project_name` columns are derived at export time and ignored on import — the `area_id` / `project_id` columns drive the join. `scheduledSlots` is serialised as a JSON string. Both export and import guard against `XLSX` being undefined (CDN not loaded) and alert the user.

### CSV Import / Export

`openDataModal()` renders the Import / Export modal dynamically from current `state` counts and attaches it to `#data-modal`. The modal is opened via the spreadsheet-grid icon in the topbar. The modal shows the Excel section first, then individual CSV rows below.

`exportCSV(type)` — builds a CSV string via `_toCSV()` and triggers a download. Supported types and their column sets:

| Type | Columns |
|---|---|
| `tasks` | `id, name, area_id, area_name, project_id, project_name, priority, due, time, recur, recurEnd, notes, waiting, done, partial, completedAt, created` |
| `projects` | `id, name, desc, area_id, area_name, color, start, end, completed, parentId` |
| `meetings` | `id, name, date, startTime, endTime, location, area_id, area_name, project_id, project_name, recur, recurEnd, attendees, notes` |
| `bucket` | `id, text, created, completed, processed, processedAs, area_id, project_id` |
| `ideas` | `id, text, created, developed, processed, processedAs, folderId` |
| `areas` | `id, name` |
| `ideaFolders` | `id, name, parentId` |

`attendees` is serialised as a pipe-separated string (`Alice|Bob|Carol`) in both CSV and xlsx; on import it is split back into an array.

`importCSV(type, input)` — reads the selected file with `FileReader`, parses it via `_parseCSV()`, and upserts rows into `state` by ID (existing ID → update in place; unknown ID → append). After merging it calls `saveToStorage()`, `refreshView()`, `renderSidebar()`, and re-opens the modal to refresh counts. Import feedback is shown via the save toast.

Private helpers: `_csvEscape()`, `_toCSV()`, `_downloadCSV()`, `_parseCSV()` — prefixed with `_` to distinguish them from public app functions.
