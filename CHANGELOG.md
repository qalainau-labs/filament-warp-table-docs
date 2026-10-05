# Changelog

All notable changes to `qalainau/filament-warp-table` will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Changed

- Installing needs only your email and license key. The fingerprint suffix on the Composer password and the activation limit of the Single Project license are gone; a Single Project license covers all environments of its one project.

## [1.1.0] - 2026-10-05

### Added

- `->warpStickyHeader()` keeps the header row below the panel's topbar while the page scrolls, until the last
  record scrolls past. Inside a modal or slide-over it sticks to the top of the modal's scrolling area, below a
  sticky modal header. Keyboard focus scrolls so the focused record is not hidden behind the header.

### Changed

- The canvas is no longer redrawn on every animation frame while the page is idle. Scrolling draws a frame about
  three times faster, and the select-all checkbox state is computed once per selection change instead of once
  per frame.

### Fixed

- Every Livewire update (sorting, searching, saving a cell) rebuilt the table. The open inline editor was
  closed, so Enter no longer moved to the next record and validation errors were not shown, and the page
  could jump back to the top. The table now carries the canvas, the editor, its focus and the scroll position
  over to the updated markup.
- Action group dropdowns near the bottom of the screen opened downwards off screen when the top of the table
  was still visible.
- Rows were drawn over a modal's sticky footer.
- Column `extraAttributes()`, `extraCellAttributes()`, `extraInputAttributes()` and `extraImgAttributes()` are
  now honored like in the native table. Their classes and styles are used when measuring column widths
  (for example `['style' => 'min-width: 6rem']`), and their effect on the cell is drawn on the canvas:
  background, padding, text color, weight, italics, text decoration, alignment and opacity. Input attributes
  such as `maxlength` are applied to the inline editor.
- Text color, weight and opacity that application CSS assigns to `->recordClasses()` are drawn, not only the
  background.
- Images that are neither circular nor square are drawn without rounded corners, like the native table.

## [1.0.0] - 2026-10-03

### Added

- `->warp()` for Filament 5 tables: the records area is drawn on a `<canvas>` while the header, toolbar,
  filters, pagination, bulk actions and modals stay Filament's own.
- Canvas rendering for `TextColumn`, `IconColumn`, `ImageColumn`, `ColorColumn`, `TextInputColumn`,
  `SelectColumn`, `ToggleColumn` and `CheckboxColumn`; other columns are rendered as DOM overlays for
  the rows on screen.
- Inline editing through Filament's `updateTableColumnState`, with validation errors.
- Grouping: collapsible groups, HTML group titles, descriptions and group selection.
- Summaries: group subtotals, page summary and table summary. Summary rows hidden by application CSS
  are hidden in the canvas too.
- Multi-level rows (`->warpMultiLevel()`): each record spans several lines on a grid, with a
  multi-level header (`HeaderCell`). Columns are placed with `->warpCell()`.

### Fixed

- Closer match with the native table, found with side-by-side demos:
  - Action colors default to `primary` (`gray` in dropdowns), button and badge actions use the `sm` size,
    and rows with button actions are as tall as in the native table.
  - The first and last cells get the native edge padding (`ps-6` / `pe-6` for action cells), and the
    actions header follows the actions alignment.
  - Text inputs draw `prefix()` / `suffix()` as separate sections, and disabled or saving inputs use the
    native disabled colors. Long values are clipped like an `<input>`.
  - Badges use the native letter spacing, limited lists show the translated "and N more", bulleted lists
    use the native marker indent, and icons are separated from the text by a space and sized by the text size.
  - Wrapped text and headers no longer break early because of sub-pixel differences or trailing spaces,
    and line clamping puts the ellipsis at the end of the last line.
  - Image columns use the native default sizes (2.5rem, 2rem when stacked, natural aspect ratio when
    neither circular nor square).
  - HTML, Markdown and custom view columns are rendered with the native cell markup, and all of them are
    measured with that markup, so column widths match the native table.
  - Column widths also consider the widest rows beyond the measurement sample.
- `->recordClasses()`: background colors assigned to the classes by application CSS are drawn.
- Native-table behavior parity:
  - Page scrolling by default; `->warpHeight()` for a fixed-height scroll area with a sticky header.
  - Row and URL-action links are real links (new tab with Cmd/Ctrl-click, middle-click, context menu).
  - Shift-click range selection.
  - Keyboard focus in the native Tab order with native-looking focus rings; Enter / Space activation.
  - Loading states (disabled checkboxes, sort spinner) while Livewire is busy.
  - Column widths measured by the browser from a hidden table with Filament's markup.
  - Browser find (Cmd/Ctrl+F) across all rows, including rows that are off screen.
- Automatic fallback to the native table for column layouts, reorder mode, groups-only tables and
  empty results.
- Asset URLs include a hash of the file contents, so browsers never keep a stale build.
