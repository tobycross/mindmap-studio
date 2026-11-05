# Version log

Mindmap Studio follows the Studio family's numbering. The version is set once, as `APP_VERSION` in [`mindmap-studio.html`](mindmap-studio.html):
- **New features** raise the minor number (1.0 → 1.1).
- **Fixes only** raise the last number (1.0 → 1.0.1).

Each release is tagged in git as `v<version>` (for example `v1.0`).

When making a release:
1. Raise `APP_VERSION`.
2. Update the version line at the top of the README.
3. Add an entry here.
4. Tag the commit.

## 1.0 (5 November 2026)

First release, built to the agreed [specification](SPEC.md).

- **Map:** a hand-drawn mind map that starts as one box on a blank canvas.
  - Main branches sit on both sides of the centre, balanced automatically and read clockwise.
  - Sketch and Outline views.
  - Drag to rearrange, with a right-click menu that mirrors the node toolbar.
- **Colour:** each main branch has its own colour across the whole branch. It can be changed from any node, and the toolbar switch turns colour on and off.
- **Tasks:** any node can be a task (Alt+T).
  - Each task has a status (to do, doing, done), the people it is delegated to and a due date.
  - Tasks are shaded light red, amber or green by status. Text is never struck through, and overdue dates show in red.
- **Tags:** tags on any node, with a tag library and a picker that suggests existing tags. It warns about near-duplicates and only creates a tag on request.
  - **Manage tags** renames, merges and deletes tags.
- **Simplify:** hides dates, people and tags on the map.
- **Filter:** by tags, people and status, shared by every step.
- **Tasks step:** a Workplan-style table grouped by main branch.
  - A summary shows each person's workload.
  - Everything is editable, and the map changes with it.
- **Board step:** fixed To do, Doing and Done columns. Drag a card to change its status.
- **Files:** saves as `.mindmap.json` (format 1).
  - Opens JSON, Markdown and indented lists, including Issue Tree Studio and WBS Studio files.
  - Includes a worked example, "Open the Leeds store".
- **Exports:** Markdown (the default), HTML, PNG and CSV, depending on the step. Each format notes its limits.
- **Checks:** Hints & checks for the map, tasks and tags.
- **Also:** undo and redo, several maps, autosave, sync to your Claude account when published as an artifact, and a dark theme.
- **Tests:** 34 automated tests in `tests/run.html`.
