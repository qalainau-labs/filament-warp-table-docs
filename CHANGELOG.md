# Changelog

All notable changes to `qalainau/filament-warp-table` will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

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
