# Mindmap Studio

Version 1.0. The version shows after the app's name in the toolbar and on exported pages. It is set once, as `APP_VERSION` near the top of the script: raise the minor number for new features (1.0 → 1.1) and the last number for fixes only (1.0 → 1.0.1).

A single-page app for thinking a project through as a mind map and then running it, in three steps: branch out ideas on a hand-drawn map, flag any node as a task, then track those tasks in a table and on a board.

It is a companion to [Issue Tree Studio](https://github.com/tobycross/issue-tree-studio), [WBS Studio](https://github.com/tobycross/wbs-studio) and [AHP Studio](https://github.com/tobycross/ahp-studio), and shares their look, controls and file approach. The full functional specification is in [`SPEC.md`](SPEC.md).

The whole app is one self-contained HTML file: [`mindmap-studio.html`](mindmap-studio.html). Changes between versions are listed in [`CHANGELOG.md`](CHANGELOG.md).

## Features

- **Three steps.** The step bar at the top runs Map › Tasks › Board. A new map opens on a blank canvas with one box in the middle, ready to type.
- **Hand-drawn mind map.** Main branches sit on both sides of the centre topic and are balanced automatically, so the map reads clockwise from the top right. Boxes and lines are inked by hand with Rough.js. Lines are thicker near the centre and finer further out.
- **Type directly on the map.** Click a node to select it, click again (or press Enter) to edit it. Enter adds a sibling and Tab adds a child.
- **Drag to rearrange.** Drop on the top or bottom of a node to place before or after it, or on its middle to make it a child. Drag a main branch across the centre, or press Alt+← / Alt+→, to move it to the other side.
- **Right-click menu.** It mirrors the node toolbar: add, make task, status, task details, tags, colour, side, fold and delete.
- **Colour.** Each main branch has its own colour, used for the outline and lines of everything in it. Change it from the right-click menu on any node in the branch. The Colour switch in the toolbar turns colour off for the plain ink look, and on again with every colour as it was.
- **Tasks.** Any node can be a task (Alt+T). A task has a status (to do, doing, done), the people it is delegated to and a due date. It is shaded light red, amber or green by status, and its text is never struck through. Overdue dates show in red.
- **Tags.** Any node can carry tags. The picker suggests existing tags first, with how often each is used, warns when a new name is close to an existing one, and only creates a tag when you choose "Create". Manage tags renames (everywhere at once), merges and deletes.
- **Simplify.** One switch hides dates, people and tags so only the shape of the map shows.
- **Filter.** Filter by tags, people and status. The same filter applies to the map (where other nodes fade), the task table and the board.
- **Tasks step.** Every task in a table grouped by main branch, as in Issue Tree Studio's Workplan. A summary shows totals and each person's open and overdue tasks, and clicking a name filters to them. Everything is editable here, and the map changes with it.
- **Board step.** To do, Doing and Done columns. Drag a card to change its status.
- **Outline view.** The map as an indented document with live Markdown alongside.
- **Hints & checks.** Checks for the map, tasks and tags. Many run automatically and jump to what needs attention.
- **Save and open.** Save writes a `.mindmap.json` file that opens again exactly as you left it. Open also reads Markdown and any indented list, including Issue Tree Studio and WBS Studio files. Drop a file anywhere on the page to open it.
- **Export.** Every step has an Export menu for that page's content. Markdown is first and recommended, and each format notes what it can't do:

  | Step | Formats |
  | --- | --- |
  | 1 Map | Markdown, HTML (a picture of the map), PNG, CSV (one row per node) |
  | 2 Tasks | Markdown, HTML, CSV |
  | 3 Board | Markdown, HTML, CSV |

  Task and board exports hold what is shown, so a filter narrows them. CSV files are UTF-8 with a byte-order mark, and typed text that starts with `=`, `+`, `-` or `@` gets a leading `'` so a spreadsheet never runs it as a formula.
- **Undo and redo**, several maps, and autosave. Deleting a map can be undone.

## Keyboard shortcuts

| Key | Sketch view | Outline view |
| --- | --- | --- |
| Enter | New sibling (on the centre topic: new main branch) | New sibling |
| Shift+Enter | Line break | Line break |
| Tab | New child | Indent |
| Shift+Tab | Move one level towards the centre | Outdent |
| Alt+↑ / Alt+↓ | Reorder among siblings | Reorder among siblings |
| Backspace on an empty node | Delete it | Delete it |
| Alt+T | Make task / remove task | Make task / remove task |
| ⌘Z / ⇧⌘Z | Undo / redo | Undo / redo |

When a node is selected but not being edited:
- The arrow keys move by position on the screen: outwards to a child, inwards to the parent, and up and down through siblings.
- Space ticks or unticks a task.
- Alt+← / Alt+→ moves a main branch to the other side.
- Delete removes the node.
- Shift+F10 opens the right-click menu.

On the Board, Space ticks the selected card and Alt+← / Alt+→ moves it a column.

## File format

Saved maps are JSON (`format: 1`). See [SPEC.md section 13](SPEC.md#13-save-and-open) for the full layout. Each node holds its text, children, tag ids and, for tasks, `{ status, people, due }`. Main branches also hold their side and colour. Unknown fields are kept when a file is opened and saved again.

The Markdown export is a readable outline that opens again with tasks and tags:

```markdown
# Open the Leeds store

## Premises

- [x] Sign the lease · Sam · due 2026-10-20 · #legal
- Fit-out
  - [ ] Choose a fit-out contractor _(doing)_ · Alex · due 2026-11-12 · #suppliers
```

## Running it

The app is meant to be published as a Claude artifact, where maps are saved to your Claude account and downloads go through the artifact's download prompt. It also opens directly in a browser as a plain HTML file, in which case maps are saved in the browser's local storage.

It loads [Rough.js](https://roughjs.com/) from jsDelivr and fonts from Google Fonts, so it needs an internet connection.

## Tests

The automated tests live in [`tests/run.html`](tests/run.html). They cover:
- the worked example's figures and layout
- every structural edit with undo
- colour, tasks, tags (including the near-duplicate rules and the picker) and the filter
- the Tasks and Board steps
- the JSON and Markdown round trips, and importing other Studios' files
- the exports, checks, right-click menu and typing.

Today is frozen at 2026-11-05. Serve the folder and open the test page:

```bash
python3 -m http.server 8766
```

Then visit `http://localhost:8766/tests/run.html`. Test runs use their own storage, separate from your real maps.
