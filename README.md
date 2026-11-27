# React Expandable Table

An expandable, multi-select data table built with React 18, Vite, plain CSS and
the Fetch API. No UI libraries and no TypeScript.

## Features

- Loads hierarchical records from `public/data.json` with `fetch()` inside `useEffect`
- Loading indicator, error state (with a **Try again** button) and empty state
- Every parent row has an expand/collapse button; several parents can be open at once
- Parent and child rows can be selected independently with a click (or Enter / Space)
- Clicking a selected row deselects only that row
- Selection is kept when a parent is collapsed and expanded again
- Expand/collapse button never changes selection
- Selected rows keep a highlighted background, including while hovered
- Live "Selected rows" count and a "Clear selection" button

## Requirements

- Node.js 18 or newer (Node 20+ recommended)
- npm

## Setup and run

```bash
npm install
npm run dev
```

Open the URL printed in the terminal (normally http://localhost:5173).

### Production build

```bash
npm run build     # outputs to dist/
npm run preview   # serves the built app locally
```

## Project structure

```text
react-expandable-table/
├── public/
│   └── data.json                 # sample hierarchical data (6 departments)
├── src/
│   ├── components/
│   │   ├── ExpandableTable.jsx   # table, states, and wiring of expand/select state
│   │   ├── ExpandableTable.css   # toolbar, table, loading/error/empty styles
│   │   ├── TableRow.jsx          # one parent or child row
│   │   └── TableRow.css          # row, hover, selected, toggle button styles
│   ├── hooks/
│   │   ├── useRecords.js         # fetches /data.json (loading / error / retry)
│   │   └── useToggleSet.js       # Set-based state used for expansion and selection
│   ├── App.jsx
│   ├── App.css
│   ├── index.css
│   └── main.jsx
├── index.html
├── vite.config.js
├── package.json
└── README.md
```

The structure follows the suggested layout. The only additions are the two
small hooks in `src/hooks/` and `ExpandableTable.css`.

## Design notes

- **Expansion and selection are separate.** `ExpandableTable` holds two
  independent `Set`s (one per `useToggleSet()` call): `expanded` and `selected`.
  Collapsing a parent only changes `expanded`, so child selections survive.
- **Unique ids.** Rows use namespaced ids: `parent-1`, `child-101`. A parent and a
  child can never collide, and selecting a parent never selects its children.
- **New `Set` on every update.** React compares state by reference, so the hook
  copies the Set before changing it.
- **Expand button vs. row click.** The button calls `event.stopPropagation()`, so
  it can't trigger the row's selection handler.
- **Hover vs. selected.** Hover styling applies only to `:not(.row--selected)`
  rows, and selected rows have their own (slightly darker) hover colour. A
  selected row always stays visibly selected. A left accent bar adds a second
  cue that doesn't rely on colour alone.

## Data format

`public/data.json` is an array of parents. Each parent has `id`, `name`,
`description` and a `children` array of `{ id, name, role }`. Ids must be
unique within parents and within children.

## Testing

Manual checklist (all verified, see the delivery notes):

1. Data loads and the heading/table headers appear
2. Expanding shows children directly under the parent; collapsing hides them
3. Several parents can be expanded at once
4. Several rows (parents and children) can be selected; clicking again deselects only that row
5. Selection persists across collapse/expand
6. Expand button does not change selection
7. Count matches the number of highlighted rows
