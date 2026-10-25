# Changelog

All notable changes to `qalainau/filament-warp-table` will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

## [1.1.7] - 2026-10-25

### Added

- README: a "Multi-tenancy" section. Warp Table works with Filament's tenancy (`Panel::tenant()`) as it is: rows,
  search, summaries, inline editing, bulk actions and select options only reach the records of the current tenant.
  Tests with a real tenant panel.

### Fixed

- After a search, a filter or another update that came before the first click in the table, saving a cell, sorting
  from the column headers and running row actions sent nothing to the server. The table now always talks to the
  Livewire component of the current page.

## [1.1.6] - 2026-10-20

### Fixed

- After saving a cell, the table always redraws from the server's response. Before, the saved value was also written
  into the table in the browser, so a save that a model event cancelled (`saving` / `updating` returning `false`)
  could keep showing the new value when the response was otherwise unchanged.
- Clicking a toggle or checkbox again (or pressing Space) while it is still saving no longer sends the same value a
  second time. The extra clicks are ignored until the save has finished.
- The keyboard focus now follows its record and column when the table updates. Before, after a save, a re-sort or a
  filter, the focus stayed at the same position, so Space or Enter could toggle or run an action on a different
  record, or throw an error when the row was gone. When the record leaves the page, the focus is cleared.
- The keyboard focus ring and Space / Enter keep working after a Livewire update. Before, the focus ring disappeared
  after every update (for example after toggling with Space or sorting with Enter) and the next key press did nothing
  until you pressed Tab.
- A cell range selected with `warpCellSelection()` stays on its columns when another column is hidden or shown.
  Before, it moved to the neighbouring columns, so Ctrl/Cmd+C copied the wrong columns.
- When you open another cell while a save is still running, a failed save no longer closes the cell you are typing
  in, and Enter no longer moves you away from it. After a re-sort, a failed cell reopens on its own record.
- Validation errors stay on their cell across other updates of the table (for example `->poll()`), and a cell
  reopened by an update keeps its cursor position. Esc clears the cell's error.
- The validation error of a `native(false)` / searchable `SelectColumn` is shown on its record when the rows moved
  while the dropdown was open.

## [1.1.5] - 2026-10-20

### Changed

- Support moves to https://warpui.com/support?plugin=table: questions before and after buying, bug reports (also
  private ones) and an email when the fix is released. The README, the license agreement and the `support` links in
  `composer.json` point there, and `composer.json` links the public docs instead of the private repository.

## [1.1.4] - 2026-10-20

### Changed

- The license agreement is now one agreement shared by every Warp plugin.
- The license agreement no longer lists a Lifetime tier. The tiers are Single Project and Unlimited Projects, each
  with one year of updates.
- The license agreement adds a refund policy (section 5): a full refund within seven days of purchase for a defect
  that cannot be fixed or worked around.

### Security

- Links and page changes made by Warp Table accept only http, https, mailto and tel URLs and relative URLs. Other
  schemes, such as `file:` or `search-ms:`, are refused.

## [1.1.3] - 2026-10-14

### Fixed

- On a slow first visit, when Filament's font finished loading after the table was first drawn, column widths and
  truncated text kept the fallback font's measurements (Inter is 2-3% wider), so text could run past its cell. The
  table now measures again and redraws once the font has loaded.
- The loading indicator no longer ends while another request to the same component is still running.
- Image cells redraw when an image fails to load, and SVG images without a width and height are drawn at the size of
  their box, as an `<img>` does.

## [1.1.2] - 2026-10-09

### Fixed

- Fast page scrolling no longer shows an empty band at the top or bottom edge of the table for a moment. The canvas
  is now drawn 400px beyond the visible area above and below, so the rows are already there when the browser scrolls
  ahead of the next redraw.

## [1.1.1] - 2026-10-09

### Fixed

- The sticky header (`->warpStickyHeader()`) no longer jitters while the page scrolls fast. While it is pinned, the
  header is drawn on its own `position: fixed` canvas, which the browser keeps in place during scrolling, instead of
  being moved after each scroll event. Inside modals and slide-overs it is drawn as before.
- Tab and Shift+Tab with the dropdown of a `native(false)` / searchable `SelectColumn` open close it and move to the
  next or previous field in the row, like the native table, instead of leaving the table.
- After Enter moves the inline editor to the next row, Tab continues from that row instead of the one above.

## [1.1.0] - 2026-10-08

### Added

- Frozen columns (`->warpFrozenColumns()`): the first columns stay at the left edge while a wide table scrolls
  sideways, with the header, group headers and summary rows aligned to them.
- Cell selection (`->warpCellSelection()`): drag across cells, extend with Shift+click or Shift+arrow keys, and copy the
  range with Ctrl/Cmd+C as tab-separated text and an HTML table for spreadsheets.

### Fixed

- Tab and Shift+Tab scroll a wide table sideways to the focused cell, like the native table.
- Keyboard focus on an inline text input or select shows only the field's own focus ring, without a second ring
  around the cell.
- Keyboard focus on checkboxes and toggles is drawn around the control itself, in the native colors (`primary-600`
  for unchecked checkboxes and toggles, lighter for checked checkboxes, `primary-500` in dark mode).
- `ToggleColumn` icons use the `offColor()` / `onColor()` icon color, like the native toggle (the "off" icon was gray).

## [1.0.0] - 2026-10-06

### Added

- `->warp()` for Filament 5 tables: the records area is drawn on a `<canvas>` while the header, toolbar,
  filters, pagination, bulk actions and modals stay Filament's own.
- Canvas rendering for `TextColumn`, `IconColumn`, `ImageColumn`, `ColorColumn`, `TextInputColumn`,
  `SelectColumn`, `ToggleColumn` and `CheckboxColumn`; other columns are rendered as DOM overlays for
  the rows on screen. HTML, Markdown and custom view columns use the native cell markup.
- Inline editing through Filament's `updateTableColumnState`, with validation errors. Text inputs support
  `prefix()` / `suffix()`, `prefixIcon()` / `suffixIcon()`, `inlinePrefix()` / `inlineSuffix()`, `mask()` and
  input attributes such as `maxlength`. `native(false)` and `searchableOptions()` select columns open
  Filament's own select with search, keyboard navigation and server-side results.
- Grouping (collapsible groups, HTML group titles, descriptions, group selection, `selectGroupsOnly()`),
  column groups (`ColumnGroup`) and summaries (group subtotals, page summary and table summary).
- Multi-level rows (`->warpMultiLevel()`): each record spans several lines on a grid, with a
  multi-level header (`HeaderCell`). Columns are placed with `->warpCell()`.
- Page scrolling by default, `->warpHeight()` for a fixed-height scroll area, and `->warpStickyHeader()` to keep
  the header row below the topbar (or the sticky modal header) while the page scrolls.
- `stackedOnMobile()` tables use Filament's native stacked layout below the `sm` breakpoint and switch back
  to Warp Table on wider screens without a reload.
- Native-table behavior: row, column `url()` and URL-action links are real links (new tab with Cmd/Ctrl-click,
  middle-click, context menu); column `action()` and `disabledClick()` follow the native click rules;
  Shift-click range selection; the native Tab order and focus rings with Enter / Space activation; loading
  states while Livewire is busy; browser find (Cmd/Ctrl+F) across all rows; scroll position, the open inline
  editor and its focus are kept across Livewire updates; the mouse wheel over the table scrolls the page.
- Column widths are measured by the browser from a hidden table with Filament's markup, including
  `extraAttributes()`, `extraCellAttributes()`, `extraInputAttributes()` and `extraImgAttributes()`, so they
  match the native table. Styles that application CSS assigns to `->recordClasses()` are drawn.
- Automatic fallback to the native table for column layouts, reorder mode, groups-only tables and
  empty results.
- Installation with your email and license key through a private Composer repository.
