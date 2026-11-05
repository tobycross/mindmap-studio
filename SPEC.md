# Mindmap Studio: functional specification

Status: agreed draft, 5 November 2026. Target release: version 1.0.

Mindmap Studio is a single-page app for thinking a project through as a mind map and then running it. You start with a blank canvas and one box in the middle, branch out ideas on both sides, flag any node as a task, and track those tasks on a summary table and a board.

It is the fourth app in the Studio family, alongside [Issue Tree Studio](../issue-tree-studio), [WBS Studio](../wbs-studio) and [AHP Studio](../ahp-studio). It shares their look, toolbar, menus, keyboard shortcuts, exports, checklist and storage, so anyone who has used one of them should find this one familiar straight away. Where this spec says "as in Issue Tree Studio", that app's current behaviour (version 1.6) is the reference.

## Contents

1. [Principles](#1-principles)
2. [Screens and toolbar](#2-screens-and-toolbar)
3. [The map](#3-the-map)
4. [Colour](#4-colour)
5. [Tasks](#5-tasks)
6. [Tags](#6-tags)
7. [Simplify](#7-simplify)
8. [Filter](#8-filter)
9. [Step 2: Tasks](#9-step-2-tasks)
10. [Step 3: Board](#10-step-3-board)
11. [One project, three views](#11-one-project-three-views)
12. [Hints and checks](#12-hints-and-checks)
13. [Save and open](#13-save-and-open)
14. [Export](#14-export)
15. [Keyboard and mouse](#15-keyboard-and-mouse)
16. [Storage, versions and running it](#16-storage-versions-and-running-it)
17. [Not in version 1](#17-not-in-version-1)
18. [Worked example and acceptance test](#18-worked-example-and-acceptance-test)

---

## 1. Principles

- **Lean and clean.** Every control earns its place. If a feature needs explaining, it is probably too complicated for version 1.
- **Familiar.** Reuse the Studio patterns before inventing new ones: the same toolbar, step bar, buttons, menus, dialogs, drawer, fonts, colours and shortcuts. New keys are added only where the Studios have nothing equivalent (section 15).
- **Readable at a glance.** Text is never struck through or hidden by status. Colour helps you find things; it never carries meaning on its own.
- **One project, many views.** The map, the task table and the board are three views of the same data. There is nothing to sync or refresh.
- **One self-contained file.** The app is one HTML file. The only outside pieces are Google Fonts and Rough.js, as in the other Studios.

## 2. Screens and toolbar

### 2.1 Steps

The step bar runs **1 Map › 2 Tasks › 3 Board**. A new project opens on step 1. The step you were on is remembered for each project.

There is no worksheet step. Ideation starts on a blank canvas.

### 2.2 Toolbar

Left to right, using the existing Studio components (`.bar`, `.seg`, `.btn`, `.menu`):

| Group | Controls | Shown on |
| --- | --- | --- |
| Brand | "Mindmap **Studio**" with the version number after it | All steps |
| Project | Project picker (a select listing your maps), New, Delete | All steps |
| Step | 1 Map › 2 Tasks › 3 Board | All steps |
| View | Sketch / Outline | Map |
| Colour | On / Off | Map |
| Simplify | On / Off | Map |
| (spacer) | | |
| Actions | Undo, Redo, Filter, Hints & checks, Open, Save, Copy Markdown, Export ▾ | All steps (Copy Markdown on Map only) |

- Colour and Simplify are two-option segmented controls styled like Sketch / Outline. Each has a small uppercase label ("COLOUR", "SIMPLIFY") like the existing "VIEW" and "STEP" labels.
- **Filter** opens and closes the filter bar (section 8). It shows a count when a filter is on, for example "Filter · 2".
- **Export** stays the accent (primary) button, as in the other Studios.
- On narrow screens the toolbar wraps, as it does today.

### 2.3 Footer

The status strip at the foot shows, from left to right:
- counts: "23 nodes · 9 tasks · 2 done · 1 overdue"
- a context hint, for example "Tab adds a child · Alt+T makes it a task"
- the save state: "Saved in this browser" or "Saved to your account".

### 2.4 Projects

One map is one project. The picker, New and Delete work as in Issue Tree Studio: several projects, autosave, and Delete can be undone (a toast offers Undo, and deletions sync to your other devices).

## 3. The map

### 3.1 A new map

A new map is a blank canvas with one box in the middle, the **centre topic**, showing the placeholder "What's on your mind?". It is already in editing mode, so you can start typing straight away. The project's title is the centre topic's text. There is nothing else on the canvas.

### 3.2 Layout

- **Both sides.** Main branches (the children of the centre topic) sit on the right and left of the centre. Everything below a main branch grows outwards on that branch's side.
- **Automatic balance.** By default the first half of the main branches (rounded up) goes on the right, top to bottom, and the rest on the left, bottom to top, so the map reads clockwise from the top right. Adding or removing a main branch rebalances the automatic ones.
- **Choosing a side.** Dragging a main branch across the centre, or pressing Alt+← / Alt+→ on it, fixes it to that side. A fixed side is saved. "Automatic side" in the right-click menu of a main branch puts it back under automatic balance.
- **Spacing.** Sub-branches are laid out as a tidy tree on their side, with no overlaps. Positions are worked out from the structure every time it is drawn; there is no free placement.
- **Motion.** Nodes glide to their new place when the map reflows (as cards do in Issue Tree Studio).

### 3.3 Nodes

- **Centre topic:** hand lettering in small capitals (Patrick Hand SC), larger, in a hand-inked rounded box.
- **Every other node:** hand lettering (Patrick Hand) in a hand-inked rounded box. Main branches are a step larger than deeper nodes. The text wraps at about 212 px, as Issue Tree Studio's cards do.
- **Connectors:** curved, hand-inked lines from parent to child, drawn with Rough.js. They are thicker near the centre and taper to finer lines further out.
- **Details line:** under a node's text, a small line in lighter ink shows its due date, the people it is delegated to and its tag chips, in that order. Only the parts that are set are shown. Simplify hides it (section 7).
- Line breaks are allowed (Shift+Enter) and kept.

### 3.4 Editing

All of this is as in Issue Tree Studio unless stated:
- Click a node to select it. Click again, or press Enter on a selected node, to edit its text. Escape stops editing.
- **Node toolbar.** A small toolbar appears above the selected node: **+ Child**, **+ Sibling**, **Task**, **Tag**, **Fold**, **Delete**. On the centre topic it shows **+ Branch** only. Task shows **Details** instead once the node is a task (section 5.4).
- **Add button.** A round "+" appears beside a node on hover, on its outer side, and adds a child.
- **Drag.** Drag a node to reorder it among its siblings, to give it a new parent, or (for a main branch) to move it to the other side. Its sub-branches travel with it. The map reflows live while you drag, with the same drop markers and ghost card as Issue Tree Studio. Escape cancels. Every drop is one undo step.
- **Fold.** Folding a node hides everything below it. A dashed pill beside it shows how many nodes are hidden, for example "+5", and "5 · 2 tasks" when tasks are among them. Clicking the pill unfolds it.
- **Pan and zoom.** Drag on empty canvas to pan, pinch or ⌘ + scroll to zoom. The zoom controls at the bottom right are −, Fit and +, as in the other Studios.

### 3.5 Right-click menu

This is new to the Studio family, so it mirrors the node toolbar and adds nothing that can't be reached another way. It uses the existing `.menu` styling. Right-click a node, long-press it on a touch screen, or press Shift+F10 or the Menu key on a selected node to open it.

| Section | Items (shortcut shown on the right) |
| --- | --- |
| Add | Add child (Tab) · Add sibling (Enter) |
| Task | Make task / Remove task (Alt+T) · Status ▸ To do, Doing, Done · Task details… |
| Tags | Add tag… · Manage tags… |
| Branch | Colour ▸ (palette, plus "Automatic") · Automatic side (main branches with a fixed side only) · Fold / Unfold |
| | Delete |

- On the centre topic the menu shows Add branch, Manage tags and Fold all / Unfold all.
- Status ▸ appears only on tasks.
- Escape or a click elsewhere closes the menu. The arrow keys move through it and Enter chooses.

### 3.6 Outline view

The same map as an indented document, as in Issue Tree Studio's Outline view, with the live Markdown export alongside and a Copy button. It has no numbering.

- Each task row shows its checkbox, its status shading (section 5.3) and its details line.
- The keys are the Outline keys in section 15.
- Main branches show their colour as a thin bar on the left when Colour is on.

## 4. Colour

**Why:** different colours make each branch easy to pick out.

- **Colour applies to a whole main branch.** The main branch and everything below it share one colour. The colour is used for the outline of each node and the connectors in that branch.
- **Automatic colours.** A new main branch takes the next colour in the palette that isn't already used by another branch. Once all eight are used, the palette repeats.
- **Changing it.** Right-click any node in a branch, then Colour ▸ and a swatch. This changes the colour of its whole main branch, even when you right-click a node deep inside it. "Automatic" goes back to the automatic colour. The centre topic is always ink. A colour change is one undo step.
- **Palette.** Eight colours: Teal, Blue, Violet, Plum, Ochre, Moss, Coral and Slate. Each has a light-theme and a dark-theme value, defined as tokens on `:root` like the other Studio colours, and each must read clearly on both the paper and the task shades (section 5.3). The palette avoids the pure red, amber and green kept for task status.
- **Toggle.** Colour On/Off in the toolbar. Off draws every outline and connector in plain ink, the classic Studio look. It changes the view only, never the saved colours, so turning it back on restores them. Task shading stays in both cases, because it carries meaning.
- **Elsewhere.** When Colour is on, branch colours also mark the group headings on the Tasks step and the left edge of cards on the Board. The toggle on the Map controls all three.

## 5. Tasks

### 5.1 Flagging a task

Any node except the centre topic can be a task. Use **Task** on the node toolbar, **Make task** in the right-click menu, or **Alt+T**. Doing it again turns the task back into a plain node. Its task details are kept for that session, so turning it back into a task straight away restores them, and undo works as usual.

A node with children can be a task. A task's sub-nodes can be tasks too.

### 5.2 Task fields (lean)

| Field | Values | Default |
| --- | --- | --- |
| Status | To do · Doing · Done | To do |
| Delegated to | Any number of people | None |
| Due | A date | None |

Tags belong to every node, not just tasks (section 6). Nothing else is stored on a task in version 1.

### 5.3 How a task looks

- **Checkbox.** A small hand-drawn checkbox sits before the text. It is empty for To do, dashed for Doing and ticked for Done. Clicking it, or pressing Space on a selected task, ticks the task (To do or Doing becomes Done) or unticks it (Done becomes To do).
- **Shading.** Each task's box is filled with a soft tint for its status:
  - **To do:** light red (rose)
  - **Doing:** light amber
  - **Done:** light green (sage)

  The tints are pale enough that text stays fully readable. Each has a deeper version for the dark theme, defined as tokens (`--task-todo`, `--task-doing`, `--task-done`). Shading is never used on plain nodes.
- **No strike-through.** Text is always shown in full, whatever the status.
- **Overdue.** A task that isn't done, with a due date before today, shows its date in the warning red as "Overdue · 1 Nov". The fill does not change.
- **Dates** show in short form ("14 Nov"), with the year added when it isn't the current year.

### 5.4 Task details

Clicking a task's details line, choosing **Details** on the node toolbar, or choosing **Task details…** in the right-click menu opens a small popover beside the node:

- **Status:** a To do / Doing / Done segmented control.
- **Delegated to:** a list of name chips. Type a name and press Enter. Names used anywhere in your projects are suggested, as in Issue Tree Studio's Workplan. × on a chip removes that person.
- **Due:** a date field with a Clear button.
- **Tags:** the tag picker (section 6.2).

Changes apply as you make them, and each one is an undo step. Escape or a click outside closes the popover.

## 6. Tags

### 6.1 Tags on nodes

Any node can have any number of tags, including the centre topic, plain nodes and tasks. Tags show as small chips on the node's details line, are hidden by Simplify, and are saved in the file. Clicking a chip filters by that tag (section 8).

### 6.2 The tag picker: one name for one thing

The aim is to stop near-identical tags being made for the same thing.

- **Opening it.** Open the picker from **Tag** on the node toolbar, **Add tag…** in the right-click menu, or the Tags field in Task details.
- **Existing tags come first.** Typing filters the project's tags as you go, each one shown with how many times it is used. ↑ / ↓ move through the list and Enter chooses. With nothing typed, the list shows every tag, most used first.
- **Suggestions from other projects.** Tags used in your other maps appear under the project's own tags, marked "from other maps".
- **Matching rules.** Tag names are trimmed and repeated spaces collapsed. Matching ignores case, so `Marketing` and `marketing` are the same tag. A tag keeps the capitalisation it was first created with.
- **Near-duplicate warning.** If what you type is close to an existing tag, a warning line names it: "Close to an existing tag: marketing". Close means any one of these:
  - the same after removing spaces, hyphens and underscores
  - one is the other with "s" or "es" added
  - two edits or fewer apart, for names of five letters or more.
- **New tags are deliberate.** A new tag is only made by choosing the last row, **+ Create new tag "…"**. Pressing Enter picks the highlighted existing tag whenever the list has one. An exact match (ignoring case) never offers to create a new tag.
- Tags already on the node are left out of the list.

### 6.3 Tag library

Each project keeps a tag library, saved in its file. It lists every tag, including tags not currently used by any node.

**Manage tags…** (in the right-click menu and the filter bar) opens a small panel listing each tag with its use count. From there you can:
- **Rename** a tag. It updates on every node, because nodes refer to tags by ID. Renaming to the name of another tag offers to merge the two instead.
- **Merge** one tag into another. Every node gets the remaining tag, without doubles.
- **Delete** a tag. A tag in use asks first: "Remove 'legal' from 2 nodes?".

Each of these is one undo step.

## 7. Simplify

The **Simplify** toggle on the Map hides the extra detail so you see only the shape of your thinking:

- **Hidden:** the details line (due date, delegated to, tags).
- **Kept:** node text, the task checkbox and shading, branch colours, and the fold pills.

It is a view setting. It never changes data and is not an undo step. It is saved with the project, and the Outline view follows it too. The Tasks and Board steps are not affected.

## 8. Filter

**Filter** in the toolbar opens a slim filter bar under the toolbar. It holds:

- three dropdowns, **Tags**, **People** and **Status** (To do, Doing, Done, Overdue), each a checklist
- a summary: "Showing 7 of 23 nodes · 4 of 9 tasks"
- a **Clear** link
- a **Manage tags…** link.

How it works:

- **Combining choices.** Within one dropdown, a node matches if it matches any choice. Across dropdowns, it must match all of them. For example, Tags = research or ops, *and* People = Priya. People and Status match tasks only. Tags match any node.
- **One filter for the project.** The filter applies on all three steps and stays as you move between them. It is remembered per project in this browser only. It is not saved in the file and is not an undo step.
- **Map:** nodes that don't match fade to about 25% strength. Their parents stay full strength, so you can see where each match sits. Nothing moves. A folded node with matches inside it shows its pill in the accent colour.
- **Tasks and Board:** only matching tasks are listed.
- **Quick filters.** Clicking a tag chip anywhere, or a person's name in the Tasks summary, adds that choice to the filter and opens the bar.
- **When the bar is closed** while a filter is on, the Filter button keeps its count so the filter is never invisible.

## 9. Step 2: Tasks

A table of every task, modelled on Issue Tree Studio's Workplan step.

### 9.1 Summary strip

- **Totals:** To do · Doing · Done · Overdue.
- **People:** one chip per person showing their open tasks (To do plus Doing) and, if any, their overdue tasks, for example "Priya 3 · 1 overdue". Clicking a name filters to that person, as in Workplan.

### 9.2 Table

- **Groups.** Rows are grouped under their main branch, in map order (right side top to bottom, then left side, the clockwise reading order). Each group heading shows the branch name, its colour bar when Colour is on, and "2 of 4 done". Within a group, rows follow map order.
- **Columns:** ☐ (checkbox) · Task · Where (the path between the main branch and the task, for example "Fit-out ›") · Status · Delegated to · Due · Tags.
- **Row shading.** Each row carries the same status tint as on the map, as a soft band. Overdue dates show in red.
- **Sort.** "Sort: Map order / Due date" sits above the table. Due date lists all tasks in one list, earliest first and undated last, without groups.
- **Editing in place.**
  - Task text edits the node's text directly.
  - Status cycles To do › Doing › Done on click.
  - Delegated to uses the same name-chip input as Task details.
  - Due is a date field.
  - Tags use the tag picker.
- **Adding a task.** Each group ends with "+ Add task", which adds a new task node as the last child of that main branch and puts the cursor in it. With no main branches yet, the table shows "Add branches on the map first" and a button that goes to the Map.
- **Removing a task.** The × at the end of a row turns the task back into a plain node. Its tooltip reads "Remove from tasks (the node stays on the map)". Nodes are deleted only on the Map.
- **Going to the map.** Clicking the Where cell selects that node on the Map and brings it into view.
- **Keys:** Tab moves between cells, Enter finishes a cell, Shift+Enter adds a line break in task text, and Escape stops editing, as in Workplan.

When there are no tasks, the step shows "Flag any node as a task with Alt+T or the Task button" and a button that goes to the Map.

## 10. Step 3: Board

Three fixed columns: **To do**, **Doing** and **Done**. Each heading shows a count. The column backgrounds carry a faint version of the matching status tint.

- **Cards** show the task text, its main branch's colour on the left edge (when Colour is on), its people, its due date (red if overdue) and its tags. The text is hand-lettered, as on the Map.
- **Order.** Cards within a column follow map order. There is no separate board order to keep in step.
- **Moving a card.** Dragging a card to another column changes its status, as one undo step. Keyboard users can select a card and press Alt+← / Alt+→ to move it one column. Space ticks it (to Done) or unticks it (back to To do), as on the Map.
- **Editing.** Clicking a card opens Task details, the same popover as on the Map. Double-clicking the text edits it. Clicking the card's branch name goes to the node on the Map.
- **Filter.** Only matching cards are shown.

## 11. One project, three views

- The Map, the Tasks table and the Board read from and write to one project. Any edit on one shows on the others straight away.
- Every change is one undo step, wherever it was made. Undo and redo work on every step, and undoing a change made on another step leaves you on the step you're on.
- Deleting a node on the Map removes its row and card, and undo restores them.
- View settings are not undo steps. These are the step, Sketch or Outline, Colour, Simplify, the filter, the sort and zoom.

## 12. Hints and checks

The **Hints & checks** drawer works as in the other Studios: a checklist with tick boxes, a progress bar, automatic checks marked "Looks fine" or "Check", and "Check" items that jump to the node or row. Version 1 checks:

**Map**
- The centre topic has text. *(automatic)*
- There are at least two main branches. *(automatic)*
- No node has more than seven direct children. *(automatic, jumps to the node)*
- No nodes are empty. *(automatic, jumps to the node)*
- Each main branch says one idea in a few words. *(manual tick)*

**Tasks**
- Every main branch has at least one task, or is clearly for reference only. *(automatic, jumps to the branch)*
- Every open task is delegated to someone. *(automatic)*
- Every open task has a due date. *(automatic)*
- Nothing is overdue. *(automatic)*
- Task names start with a verb, for example "Book", "Draft" or "Agree". *(manual tick, with a hint listing tasks whose first word is in a short list of common non-verbs)*
- No one has more than five open tasks. *(automatic)*

**Tags**
- No two tags are near-duplicates (using the rules in section 6.2). *(automatic, opens Manage tags)*
- No tags are unused. *(automatic, opens Manage tags)*

## 13. Save and open

### 13.1 Save

**Save** downloads the whole project as `<title>.mindmap.json`. It holds everything, so opening it brings the project back exactly as you left it.

We chose JSON over Markdown for saving because a map holds a lot that isn't text: colours, sides, folds, tags and the tag library, task fields, and view settings. JSON keeps all of it without escaping rules, and new fields can be added later without breaking older files. Markdown remains the default export, for reading and sharing (section 14).

### 13.2 File format (format 1)

```json
{
  "app": "Mindmap Studio",
  "format": 1,
  "appVersion": "1.0",
  "id": "m_8f3k2",
  "title": "Open the Leeds store",
  "updated": "2026-11-05T09:30:00Z",
  "view": { "colour": true, "simplify": false, "mode": "sketch", "step": "map" },
  "tags": [
    { "id": "t1", "name": "legal" },
    { "id": "t2", "name": "suppliers" }
  ],
  "root": {
    "id": "n0",
    "text": "Open the Leeds store",
    "tags": [],
    "children": [
      {
        "id": "n1",
        "text": "Premises",
        "side": "auto",
        "colour": "teal",
        "collapsed": false,
        "tags": [],
        "children": [
          {
            "id": "n2",
            "text": "Sign the lease",
            "tags": ["t1"],
            "task": { "status": "done", "people": ["Sam"], "due": "2026-10-20" },
            "children": []
          }
        ]
      }
    ]
  }
}
```

- **`side`** (`"auto"`, `"left"` or `"right"`) and **`colour`** (a palette name, or `"auto"`) appear on main branches only.
- **`task`** is present only on tasks. `status` is `"todo"`, `"doing"` or `"done"`. `due` is an ISO date, or it is left out.
- **`tags`** on a node lists tag IDs from the library. Unknown IDs are dropped on open.
- **`collapsed`** may appear on any node. Missing optional fields take their defaults.
- **Unknown fields** are kept when a file is opened and saved again, so a newer file opened in an older app loses nothing.
- **A newer format** (a higher `format` number) opens with a notice: "This file was saved by a newer Mindmap Studio. Some details may not show."

### 13.3 Open

The **Open** dialog works as in the other Studios. It has Choose a file…, drop a file anywhere on the page, or paste text, then Cancel, Replace this map, or Import as new map. It accepts:

| Input | Result |
| --- | --- |
| `.mindmap.json` (or any `.json` from this app) | The exact project |
| Markdown exported from Mindmap Studio | The structure, tasks (status, people, due date) and tags. Colours, sides and folds use the defaults. |
| Any other Markdown or indented list, including Issue Tree Studio and WBS Studio exports | The structure. The first heading or top line becomes the centre topic. Codes and numbers at the start of lines are dropped. `- [ ]` and `- [x]` lines become tasks. |

The dialog also has a **Load the example** link that opens the worked example (section 18) as a new map.

## 14. Export

Every step has an **Export** menu holding that step's content only. **Markdown is the default.** It is listed first, marked "Recommended", and chosen if you press Enter as soon as the menu opens. Each item has a one-line note saying what the format can and can't do, so you know before you choose.

| Step | Format | What you get | The note on the menu item |
| --- | --- | --- | --- |
| Map | **Markdown** | The whole map as an indented outline with tasks and tags | "Opens again in Mindmap Studio with tasks and tags. Colours, sides and folds aren't kept." |
| Map | HTML | A web page holding a picture of the map | "For viewing and printing. It can't be opened again in Mindmap Studio." |
| Map | PNG | A high-resolution image of the map, cropped with a small margin | "An image for slides and documents. It can't be edited." |
| Map | CSV | One row per node: path, level, text, tags and task fields | "For spreadsheets. Shows structure as columns, not a picture." |
| Tasks | **Markdown** | The task table, grouped as on screen | "Readable in documents, GitHub or Notion. Holds the tasks shown." |
| Tasks | HTML | A formatted, self-contained page to share or print | "For viewing and printing. It can't be opened again in Mindmap Studio." |
| Tasks | CSV | One row per task | "Imports into Excel, Todoist or Asana. Tasks only: the map isn't included." |
| Board | **Markdown** | Three lists, one per column | "Readable anywhere. Holds the cards shown." |
| Board | HTML | A snapshot of the board | "For viewing and printing. Cards can't be moved in the copy." |
| Board | CSV | The same as Tasks, with a Column field | "Imports into Excel, Todoist or Asana. Tasks only: the map isn't included." |

- **Filter.** When a filter is on, Tasks and Board exports hold only what is shown, and the menu says so at the top: "Filtered: 4 of 9 tasks". Map exports always hold the whole map, with no fading.
- **Colour toggle.** Map pictures (HTML and PNG) follow the Colour and Simplify toggles. They are always drawn in the light theme, as Issue Tree Studio's PNG is.
- **CSV files** follow the Studio rules: UTF-8 with a byte-order mark, and typed text that starts with `=`, `+`, `-` or `@` gets a leading `'`. The task CSV columns are `Task, Branch, Path, Status, Delegated to, Due, Tags`, with several people or tags separated by `; `.
- **Copy Markdown** on the Map step copies the Map Markdown export.
- **HTML pages** carry the app name and version, as in the other Studios.

### 14.1 Markdown export format

```markdown
---
app: Mindmap Studio
format: 1
---

# Open the Leeds store

## Premises

- [x] Sign the lease · Sam · due 2026-10-20 · #legal
- Fit-out
  - [ ] Choose a fit-out contractor _(doing)_ · Alex · due 2026-11-12 · #suppliers
  - [ ] Order the coffee machine · Alex · due 2026-11-28 · #suppliers #equipment

## Launch marketing

- Local press
  - [ ] Write the press release · Jo · due 2026-12-05 · #marketing
```

- Each main branch is a `##` heading, in clockwise reading order. Below it, each level is indented two spaces.
- Tasks use GitHub task-list syntax: `[ ]` for to do and doing, `[x]` for done. Doing adds `_(doing)_`.
- Details follow the text, separated by ` · `. People are separated by commas. Tags are written as `#tag`, with spaces in a tag written as hyphens.
- Characters that would be misread when the file is opened again (a leading `#`, `[`, `·`, `\`, `<` and `&`) are escaped, as in Issue Tree Studio's format 6.

## 15. Keyboard and mouse

### 15.1 The same as the other Studios

| Key | Sketch view | Outline view |
| --- | --- | --- |
| Enter | New sibling (on the centre topic: new main branch) | New sibling |
| Shift+Enter | Line break | Line break |
| Tab | New child | Indent |
| Shift+Tab | Move one level towards the centre | Outdent |
| Alt+↑ / Alt+↓ | Reorder among siblings | Reorder among siblings |
| Backspace on an empty node | Delete it | Delete it |
| ⌘Z / ⇧⌘Z | Undo / redo | Undo / redo |
| Escape | Stop editing; close a menu, popover or drag | Stop editing |

When a node is selected but not being edited, the arrow keys move around the map **by position on the screen**:
- **On the right side:** → goes to the first child and ← goes to the parent.
- **On the left side:** ← goes to the first child and → goes to the parent.
- **↑ / ↓** go to the sibling above or below.
- **From the centre topic:** → goes to the first right-hand branch and ← to the first left-hand branch.

Enter edits the selected node, as in Issue Tree Studio.

### 15.2 New in Mindmap Studio

These keys are kept to a minimum.

| Key | Action | Where |
| --- | --- | --- |
| Alt+T | Make task / remove task | Map (selected or editing), Outline |
| Space | Tick or untick a task (when selected, not editing) | Map, Board |
| Alt+← / Alt+→ | Move a main branch to the left or right side | Map, on a main branch |
| Alt+← / Alt+→ | Move a card one column | Board |
| Shift+F10 or the Menu key | Open the right-click menu | Map |

- **Alt+T** is used because ⌘T opens a new browser tab and can't be taken over. Alt shortcuts are matched on the physical key (`KeyT`), because on a Mac Alt+T types "†".
- **Space** on a plain node does nothing, and the hint line suggests Alt+T.

### 15.3 Mouse and touch

- Click to select, click again to edit, drag to move, right-click (or long-press) for the menu.
- Drag on empty canvas to pan. Pinch or ⌘ + scroll to zoom.
- Click a checkbox to tick or untick it, and click a tag chip or a name to filter.

## 16. Storage, versions and running it

These follow the other Studios:
- **Autosave.** Projects are saved 700 ms after the last change, to the browser's local storage, and also to your Claude account when the app runs as a published artifact. Deletions sync.
- **Version number.** `APP_VERSION` is set once, near the top of the script. Raise the minor number for new features and the last number for fixes only. It shows after the app's name and on exported pages.
- **One file.** The app is `mindmap-studio.html`. It loads Rough.js from jsDelivr and fonts from Google Fonts, so it needs an internet connection.
- **Dark theme.** The theme follows the system. All new colours (the branch palette and the task tints) are defined as tokens with light and dark values, using the same `:root` pattern as the other Studios.
- **Downloads.** Downloads go through the artifact's download prompt when published, or a normal download when the file is opened directly in a browser.

## 17. Not in version 1

These are deliberately left out to keep version 1 lean. The order is a rough guide to what might come next.

1. Colouring nodes by tag (the tag library is ready for it).
2. A cross-project "All tasks" view.
3. Notes on nodes.
4. Priority and effort fields.
5. Links between branches, free-floating topics, and free placement of nodes.
6. Dependencies, Gantt and timeline views.
7. Recurring tasks, reminders and notifications.
8. Images, icons and emoji on nodes.
9. Real-time collaboration.
10. Direct connections to Todoist, Asana or other tools (CSV covers this for now).

---

## 18. Worked example and acceptance test

### 18.1 The example project: "Open the Leeds store"

This ships with the app (Open › Load the example) and is the test fixture. It continues the fictional Northfield Coffee used in Issue Tree Studio.

For testing, **today is fixed at 2026-11-05**. The test page sets the app's clock hook (`window.__msToday`) so that overdue results don't change with the real date.

| Side | Main branch (colour) | Node | Task | Status | Delegated to | Due | Tags |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Right | Premises (Teal) | Sign the lease | Yes | Done | Sam | 2026-10-20 | legal |
| | | Fit-out | | | | | |
| | | › Choose a fit-out contractor | Yes | Doing | Alex | 2026-11-12 | suppliers |
| | | › Order the coffee machine | Yes | To do | Alex | 2026-11-28 | suppliers, equipment |
| | | Licences | | | | | |
| | | › Food hygiene registration | Yes | To do | Priya | 2026-11-01 | legal |
| Right | People (Blue) | Hire a store manager | Yes | Doing | Priya, Sam | 2026-11-20 | hiring |
| | | Baristas | | | | | |
| | | › Advertise four barista roles | Yes | To do | Priya | 2026-12-01 | hiring |
| | | Training plan | | | | | |
| Left | Launch marketing (Violet) | Local press | | | | | |
| | | › Write the press release | Yes | To do | Jo | 2026-12-05 | marketing |
| | | Opening-week offer | Yes | To do | | | marketing |
| | | Social media ideas *(folded)* | | | | | |
| | | › Behind the scenes of the fit-out | | | | | |
| | | › Meet the team | | | | | |
| Left | Money (Coral) | Agree the opening budget | Yes | Done | Sam | 2026-10-15 | finance |
| | | Cash flow for the first three months | | | | | |

Figures the tests rely on:

- **Nodes:** 23 (the centre topic plus 22). **Main branches:** 4, two on each side under automatic balance.
- **Tasks:** 9. To do 5, Doing 2, Done 2, Overdue 1 (Food hygiene registration).
- **Open tasks by person:** Priya 3 (1 overdue), Alex 2, Sam 1, Jo 1.
- **Tag library:** legal 2, suppliers 2, hiring 2, marketing 2, equipment 1, finance 1.
- **Checks that should show "Check":** "Every open task is delegated" and "Every open task has a due date" (both Opening-week offer), and "Nothing is overdue" (Food hygiene registration). Every other automatic check looks fine.

### 18.2 Acceptance script

Run these in order in a fresh browser profile. Each step states what must be true afterwards. All of them must pass for version 1.0.

**Start and map**

| # | Do | Expect |
| --- | --- | --- |
| AT-01 | Open the app for the first time. | One blank map on step 1 Map: a single box in the middle reading "What's on your mind?", ready for typing. No other nodes or worksheet. The footer reads "1 node · 0 tasks". |
| AT-02 | Type "Open the Leeds store" and press Enter. | The centre topic shows the text, and a new main branch appears on the right in editing mode. The project picker shows "Open the Leeds store". |
| AT-03 | Type "Premises", Enter, "People", Enter, "Launch marketing", Enter, "Money". | Four main branches: Premises and People on the right (top to bottom), Launch marketing and Money on the left (Money above Launch marketing, reading clockwise). Each has a different automatic colour. |
| AT-04 | Select Premises and press Tab, then type "Sign the lease". | A child appears to the right of Premises, outlined in Premises' colour. |
| AT-05 | Select Launch marketing and press Tab. | The new child appears to the **left** of Launch marketing. |
| AT-06 | Select a node on the left side and press ←, then →. | ← moves to its first child (further left). → moves back to the parent. |
| AT-07 | Press Alt+→ on Money. | Money moves to the right side and stays there after a reload. "Automatic side" in its right-click menu moves it back. |
| AT-08 | Drag "Sign the lease" onto People, then press ⌘Z. | It becomes People's child with People's colour, then undo puts it back under Premises. |
| AT-09 | Press Backspace in an empty new node. | The node is deleted, and selection moves to its parent. |

*For the remaining steps, load the example (Open › Load the example). Each group below starts from a freshly loaded example, so the figures in 18.1 hold at the start of every group. Within a group, steps build on each other.*

**Colour**

| # | Do | Expect |
| --- | --- | --- |
| AT-10 | Right-click "Order the coffee machine", then Colour ▸ Plum. | The whole Premises branch (Premises, Sign the lease, Fit-out and everything below, Licences and below) turns Plum. Other branches don't change. ⌘Z restores Teal. |
| AT-11 | Set Colour to Off. | All outlines and connectors are ink. Task shading stays. On Tasks, the group colour bars are gone. On Board, the card edges are gone. |
| AT-12 | Set Colour back to On. | Every branch colour comes back exactly as it was. |

**Tasks on the map**

| # | Do | Expect |
| --- | --- | --- |
| AT-13 | Look at the map. | Nine nodes are shaded: five rose (to do), two amber (doing), two sage (done). No text is struck through anywhere. Food hygiene registration shows "Overdue · 1 Nov" in red. |
| AT-14 | Select "Training plan" and press Alt+T. | It becomes a task: rose shading and an empty checkbox. The footer reads "10 tasks". It appears on Tasks under People, and on Board in To do. |
| AT-15 | Press Space. | It turns sage with a ticked box and moves to Done on the Board. Space again returns it to To do. |
| AT-16 | Press Alt+T again, then ⌘Z twice. | It becomes a plain node, then the first undo makes it a task again and the second undo makes it plain, as it started. |
| AT-17 | Open Task details on "Opening-week offer", add Jo and due date 2026-11-30. | Its details line shows "30 Nov · Jo · marketing". The "delegated" and "due date" checks now look fine. |

**Tags**

| # | Do | Expect |
| --- | --- | --- |
| AT-18 | Open the tag picker on "Meet the team" (unfold Social media ideas first) and type "Marketting". | The list shows "marketing (2)" highlighted, a warning reads "Close to an existing tag: marketing", and the last row is "+ Create new tag "Marketting"". |
| AT-19 | Press Enter. | "marketing" is added (not a new tag). The library still has six tags, and marketing now shows 3. |
| AT-20 | Type "MARKETING" in the picker on another node. | No "Create" row is offered (exact match ignoring case). |
| AT-21 | Type "hirings". | A warning names "hiring". |
| AT-22 | In Manage tags, rename "equipment" to "kit". | "Order the coffee machine" shows "kit". Undo restores "equipment". |
| AT-23 | In Manage tags, rename "kit" to "suppliers". | The panel offers to merge. After merging, "Order the coffee machine" has "suppliers" once, and the library has five tags. |

**Simplify**

| # | Do | Expect |
| --- | --- | --- |
| AT-24 | Set Simplify to On. | Every details line (dates, people, tag chips) disappears. The checkboxes, shading, colours and fold pills stay. Nothing on Tasks or Board changes. |
| AT-25 | Reload the page. | Simplify is still on, and the data is unchanged. |

**Filter**

| # | Do | Expect |
| --- | --- | --- |
| AT-26 | Open Filter and choose Tags = hiring. | On the map, only the two hiring tasks and their parents (People, Baristas and the centre) are at full strength, and everything else fades. The summary reads "Showing 2 of 23 nodes · 2 of 9 tasks". |
| AT-27 | Also choose People = Sam. | Only "Hire a store manager" matches (hiring *and* Sam). |
| AT-28 | Go to Tasks, then Board. | Both show only "Hire a store manager". The Filter button shows "Filter · 2". |
| AT-29 | Clear the filter and click Priya's name in the Tasks summary. | The filter becomes People = Priya, showing three tasks. |

**Tasks step and two-way editing**

| # | Do | Expect |
| --- | --- | --- |
| AT-30 | Clear the filter and open Tasks. | Four groups in clockwise order: Premises (1 of 4 done), People (0 of 2), Launch marketing (0 of 2), Money (1 of 1). The summary shows To do 5 · Doing 2 · Done 2 · Overdue 1, and Priya 3 · 1 overdue, Alex 2, Sam 1, Jo 1. |
| AT-31 | Edit the text "Advertise four barista roles" to "Advertise five barista roles". | On the Map, the node reads "Advertise five barista roles". |
| AT-32 | Click the status of "Order the coffee machine" until it reads Doing. | On the Map it turns amber, and on the Board it sits in Doing. |
| AT-33 | Click "+ Add task" under Money and type "Open a business account". | On the Map, a new rose task appears as the last child of Money. |
| AT-34 | Click × on that row. | The row goes. On the Map the node remains as a plain node. |
| AT-35 | On the Map, delete "Licences" (with its child), then go to Tasks. | Food hygiene registration is gone from the table. ⌘Z (on the Tasks step) brings back the node and the row, and you stay on Tasks. |
| AT-36 | Choose Sort: Due date. | One list, earliest first: Agree the opening budget (15 Oct), Sign the lease (20 Oct), Food hygiene registration (1 Nov), …, with Opening-week offer last (no date). |

**Board**

| # | Do | Expect |
| --- | --- | --- |
| AT-37 | Open Board. | To do 5, Doing 2, Done 2. Cards in each column are in map order. Each card's left edge has its branch colour. |
| AT-38 | Drag "Write the press release" to Done. | It moves to Done, and on the Map it turns sage and is ticked. ⌘Z moves it back. |
| AT-39 | Select a card and press Alt+→. | It moves one column right. |

**Save, open and export**

| # | Do | Expect |
| --- | --- | --- |
| AT-40 | Save. | `Open the Leeds store.mindmap.json` downloads. |
| AT-41 | Open that file with Import as new map. | A second project that matches the first exactly: every node, side, colour, fold, tag, task field, and the Colour and Simplify settings. |
| AT-42 | On Map, open Export. | Markdown is first, marked Recommended, and each item shows its limitation note. Pressing Enter exports Markdown. |
| AT-43 | Open that Markdown export with Import as new map. | The same nodes, tasks (status, people, due date) and tags. Colours are automatic and nothing is folded. |
| AT-44 | Export Tasks as CSV and open it in a spreadsheet. | Nine rows with the columns Task, Branch, Path, Status, Delegated to, Due and Tags. "Hire a store manager" shows "Priya; Sam". Accents are correct. |
| AT-45 | With Tags = marketing on, open Export on Tasks. | The menu reads "Filtered: 2 of 9 tasks", and the export holds two rows. |
| AT-46 | Export the Map as PNG with Colour on, then off. | Two light-theme images cropped to the map, one coloured and one ink only. |
| AT-47 | Paste an Issue Tree Studio Markdown export into Open. | A map with its key question as the centre topic and its branches as main branches, with no numbers in the text. |

**Checks, undo and storage**

| # | Do | Expect |
| --- | --- | --- |
| AT-48 | Open Hints & checks on the untouched example. | Exactly three automatic items show "Check": delegated, due date and overdue. Clicking "Nothing is overdue" selects Food hygiene registration on the Map. |
| AT-49 | Delete the project, then press Undo in the toast. | The project comes back complete. |
| AT-50 | Switch the system to the dark theme. | The paper, ink, branch palette and task tints all switch to their dark values. All text stays readable on every tint. |

### 18.3 Automated tests

`tests/run.html` follows the other Studios: serve the folder (`python3 -m http.server 8766`) and open `http://localhost:8766/tests/run.html`. Tests use their own storage and freeze today at 2026-11-05. It covers:

- **Model:** layout sides and automatic balance; clockwise ordering; every structural edit with undo (add, delete, indent, outdent, reorder, move, change side, fold).
- **Colour:** inheritance across a branch, automatic assignment, the toggle leaving data unchanged.
- **Tasks:** flag and unflag, status cycle, Space toggle, overdue against the frozen date.
- **Tags:** normalisation, the near-duplicate rules (one case each), rename, merge and delete, ID references surviving a rename.
- **Filter:** any-of within a dropdown, all-of across dropdowns, ancestors kept, counts.
- **Two-way editing:** changes on Tasks and Board reflected on the Map and the reverse, cross-step undo.
- **File:** the JSON round trip (deep equality), unknown fields kept, a newer-format notice; the Markdown export round trip; importing indented lists, Issue Tree Studio exports and WBS Studio exports.
- **Exports:** which formats show on which step; the filtered export; the CSV byte-order mark and formula escaping.
- **Example:** every figure in section 18.1.
- **Page:** the Sketch, Outline, right-click menu, popover and drag behaviour, as far as the test page can drive them.
