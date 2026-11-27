# Changelog

All notable changes to `qalainau/filament-warp-table` will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.7.3] - 2026-11-28

### Fixed

- **Columns that your panel's CSS hides no longer take space or squeeze other columns**: when a column's header is hidden by CSS, as Warp Mobile's `swipeActions()` column is on wide screens, the table showed it as an empty column and stopped measuring column widths the way Filament does. Short columns such as `->date('n/j')` came out a little too narrow and were cut ("11…"). Such columns are now left out, like in Filament's own table, and the widths match it again. Tables with a fixed `rowHeight()` also keep the wider padding of the first and last cells, so their text is no longer cut by it.
- **Wrapped descriptions get their full height**: in a column with `->description(...)->wrap()`, a description that wraps onto two or more lines was drawn in a row only one line taller, so its last line was cut off at the bottom. Rows are now as tall as in Filament's own table.
- **No faint lines next to columns frozen with `warpFrozenEndColumns()`**: badges or text scrolled underneath the columns frozen on the right could show through as a thin line between two frozen columns or at the table's right edge. The frozen area is now painted as one block.

## [1.7.2] - 2026-11-26

### Changed

- **Date header filters can use the column's index**: a date range in a column heading is now compared as `column >= first day` and `column < the day after the last day`, instead of wrapping the column in a date function. On MySQL and other databases an index on the date column now speeds up the filter. The rows that match are the same, including the whole last day for datetime columns shown with `->date()`.
- **The AI filter's "can't be used now" sentence is readable on phones**: when a `warp.ai` gateway says the AI filter can't be used now, its sentence now also appears as a small, muted line under the disabled button (it was only in the tooltip, which phones can't show). Screen readers read it as the button's description. Nothing changes while the gateway allows the feature, without a gateway, or with your own `using()` resolver.

### Fixed

- **The AI filter works on tables with six or more filter columns**: through a `warp.ai` gateway, a real AI model refused the request ("too many parameters with union types") as soon as six columns had header filters, so the button showed the generic failure. The answer format sent to the gateway no longer uses `null` types: an empty list, `false` or an empty string now means “no suggestion”, and range bounds come back as numbers written as text. The filters that get filled in are unchanged. Your own `using()` resolvers and the deprecated `anthropic()`/`openAi()` calls are unchanged.
- **Number header filters with decimals work on PostgreSQL**: on PostgreSQL, a number range with a decimal such as `10.5` (or `20.0`) on an integer column, typed in the heading or suggested by the AI filter, stopped the table with a database error ("invalid input syntax for type integer"). Whole numbers are now compared as integers, so an index on the column is still used, and other numbers are compared as decimals. MySQL, MariaDB and SQLite already worked and are unchanged.

## [1.7.1] - 2026-11-26

### Fixed

- **Column header filters show with `warpHeight()`**: on a table with a fixed height, the filter buttons in the column headings were all hidden. They now show in the headings and stay with them while the table or the page scrolls.

## [1.7.0] - 2026-11-25

### Added
- **AI header filters through a shared AI gateway**: `WarpAiFilterAction::make()` uses `warp.ai` when no explicit resolver is configured. The gateway receives a structured filter schema, chooses the model and owns usage limits. Name a meter with `aiFeature('product_search')`. Unavailable features are disabled with the gateway's sentence as a tooltip, and safe refusal messages appear as notifications. Explicit resolvers, HTTP request bodies and the interface without a gateway are unchanged. See [Which AI service is used](README.md#which-ai-service-is-used).

### Deprecated
- `WarpAiFilterAction::anthropic()` and `openAi()` keep working in 1.x and will be removed in 2.0. Bind an AI gateway to `warp.ai` and remove the explicit adapter call instead.

### Docs
- The frozen columns section now says that the two-thirds limit for `warpFrozenColumns()` and `warpFrozenEndColumns()` is measured on the width the table is shown in (a Split View pane, a modal, a narrow grid column), not the browser window, that it is measured again when that width changes, and which end column is dropped first. It also shows how to keep text row actions from filling a narrow pane. Behavior is unchanged.

## [1.6.2] - 2026-11-24

### Changed
- `warpPaste()` toggle and checkbox cells also read yes / no in all ten table languages (`ja` / `nein`, `oui` / `non`, `sí` / `no`, `sim` / `não`, `예` / `아니요`, `是` / `否` and more) and the TRUE / FALSE of German, French, Spanish, Italian and Portuguese Excel (`WAHR` / `FALSCH`, `VRAI` / `FAUX`, `VERDADERO` / `FALSO`, `VERO` / `FALSO`, `VERDADEIRO` / `FALSO`), regardless of letter case. Values that were read before are read the same way.
- The AI filter disclosure in Japanese, Korean and Simplified and Traditional Chinese names the destination after a colon, without spaces around it.

## [1.6.1] - 2026-11-23

### Fixed
- The `warp_table_preferences` table (column widths and pinned rows) now has the same two tenant columns as the other Warp plugins, `tenant_type` and `tenant_id`, so tenant data tools can tell which tenant a row belongs to, and two tenant models with the same key no longer share saved widths and pins. Publish and run the new migration `add_tenant_type_to_warp_table_preferences_table` (`php artisan vendor:publish --tag=filament-warp-table-migrations` and `php artisan migrate`). It renames `tenant_key` to `tenant_id` and keeps every saved row. Until it runs, Warp Table keeps using the 1.6.0 columns.

## [1.6.0] - 2026-11-23

### Added
- Add opt-in `warpInfiniteScroll()` with geometric page growth, loading progress, manual loading beyond the automatic limit, and server-side size validation.


- Opt-in `warpPaste()` for Excel/Google Sheets pasting and Ctrl/Cmd+D fill down into editable cells, using existing inline validation and tenant scoping, with cell limits and a single result notification.

- Opt-in `warpRowDetail()` for lazy, tenant-scoped read-only detail bands with accessible toggles, measured heights, browser caching and expansion preserved across redraws.

- Add opt-in `WarpExportAction` for Excel and CSV downloads of visible table columns, groups and summaries, with scope selection, row limits and tenant-scoped queries.

- Opt-in `WarpAiFilterAction` for natural-language header filtering, with custom resolvers or Laravel HTTP adapters for Anthropic and OpenAI, validated values, tenant-scoped lists, provider disclosure and per-user rate limits.

- Opt-in Excel-style header filters with `Column::warpHeaderFilter()` and `warpHeaderFilters()`: searchable value lists, blank selection, numeric ranges and date periods, with Filament indicators, clearing, session persistence and tenant-scoped lazy options.

- Opt-in `warpPinnedRows()` with per-user, per-table and per-tenant pins, automatic `WarpPinAction`, and non-selectable pinned copies on later pages.

- Opt-in end-frozen columns with `warpFrozenEndColumns()`, including trailing row actions, sticky headers, summaries and input rows.

- Opt-in `warpResizableColumns()` for dragging column widths and double-clicking to restore automatic sizing, with per-user and per-tenant database preferences or browser storage for guests. Includes the optional preferences migration, `warpPreferencesKey()`, hidden action registration and feature JavaScript loaded only by tables that enable it.

### Fixed

- **Inline editing and cell selection with Warp Context Menu**: in a panel with Warp Context Menu, clicking an editable cell (text input, select, toggle, checkbox) did nothing and dragging did not select cells (and after a Shift+click that extended the range, Cmd/Ctrl+A and Escape did nothing), because the transparent cell mark that carries `data-warp-*` attributes took the click. Left clicks and drags now reach the table; right-clicks and delegated listeners (Quick Look, Split View) still see the attributes.
- **Summaries on Filament 5.12**: tables with page or all-records summaries ran each `sum()` query twice (and the grouped summary query even when no group ends on the page). Warp Table now runs the same summary queries as the standard Filament table on 5.10, 5.11 and 5.12.

## [1.5.1] - 2026-11-19

### Fixed

- **`warpTable is not defined` in the console** when a Warp Table list appeared on a page whose layout re-renders with Livewire before the table's script has arrived (seen in a panel with Warp Mobile, Warp Offline and Warp Shift, when moving from another page to a list; the list worked after a moment). Filament's `x-load` holds back the table's Alpine component until its script is downloaded, but a Livewire re-render inside another Alpine component (such as Filament's table or Warp Mobile's swipe table) evaluates `warpTable()` earlier. Warp Table now registers an empty `warpTable` in the panel's scripts, which the real one replaces when it arrives.
- **Copying cells**: a cell whose text contains a lone carriage return (`\r`, e.g. text that came from an old Mac file) is now quoted when copied, so pasting it into Excel or Google Sheets no longer splits it into extra rows.

## [1.5.0] - 2026-11-17

### Added

- **`warpNativeBelow()`**: the screen width (`'sm'`, `'md'`, `'lg'`, `'xl'` or `'2xl'`) below which the table is shown as Filament's own table, for layouts on narrow screens that need Filament's HTML table. Without it, `->stackedOnMobile()` tables still switch below `'sm'` as before. Warp Mobile uses it so that `->mobileCards()` show as cards up to its own breakpoint (768px by default). See “Automatic fallback” in the README.

### Fixed

- **Columns whose view was replaced** with `->view()` (a `TextColumn`, `IconColumn` or other built-in column) were drawn on the canvas as plain text, without their own HTML. They are now shown with their HTML over the canvas, like `ViewColumn`. With Warp Tree, the first column of a `->warpTree()` table had no indent and no arrow, so rows under the top level could not be opened.
- **Tables in a table widget** (`TableWidget`) could lose their canvas after a Livewire update, such as opening a row or changing the number of rows per page: the rows were gone until the page was reloaded. This came with 1.4.0, which added hidden marks for other plugins next to the canvas; they are now inside the canvas area, which Livewire leaves alone.
- With Warp Mobile, a `->mobileCards()` table on screens between 640px and Warp Mobile's breakpoint (768px by default) was a canvas table squeezed by the card CSS. It now shows the cards.

## [1.4.0] - 2026-11-16

### Added

- **Working with other Warp plugins**: `->quickLook()` columns of Warp Quick Look (dotted underline, hover card, details on click or Enter, previous and next, focus back on the viewed cell) and `HasSplitView` of Warp Split View (a click opens the row in the pane, ↑↓ and j/k step through the rows, the open row is highlighted) now work on the canvas table. Before, the canvas had no marks for Quick Look and a click always went to the record page. See “Working with other Warp plugins” in the README.
- **JavaScript integration** for other plugins: the `data-warp-*` attributes of a column's `extraAttributes()` per record, `data-warp-table-decoration="dotted-underline"`, the cancelable `warp-table:open-row` event, `warp-table:rows-changed` and `warp-table:focus-row`, and `element.warpTable` (`rowKeys()`, `rowUrl()`, `markRow()`, `focusRow()`). The colours of the marked row are the CSS variables `--warp-table-marked-row-bg` and `--warp-table-marked-row-edge`.

### Fixed

- **Static analysis**: Larastan no longer reports “Call to an undefined method” for `->warp()`, `->warpStickyHeader()`, `->warpCell()` and the other methods this plugin adds to tables and columns. The plugin ships `extension.neon`, a PHPStan extension that reads Filament's macros (loaded automatically with `phpstan/extension-installer`; otherwise add it to `includes`). See “Static analysis” in the README.

## [1.3.0] - 2026-11-13

### Added

- **Entry row**: `->warpEntryRow()` puts an empty row under the header. Type, press Tab to move to the next field and Enter to create the record; the row is then emptied for the next one. The record is created by Filament's own `CreateAction`, so the resource's form, validation, `create` permission, tenancy and notification apply as with the "New" button. A wrong value is shown under its field and nothing is saved. When a required field of the form is not in the table, Enter opens the create modal with the typed values. Only the fields shown in the row are taken from the browser. Off by default; see "Entry row" in the README.
- **`SparklineColumn`**: a small line, area, bar or win/loss chart in each row, with optional marks on the highest, lowest and latest values, colors that can depend on the values, and dark mode. **`ProgressColumn`**: a progress bar with a percentage, against a maximum you choose per record. Both make their shapes on the server as SVG, so the native table shows them too. The README shows how to load the values of the whole page in one query.

## [1.2.4] - 2026-11-12

### Changed

- Now requires Filament 5.10.2 or later (was 5.0). Parts of the plugin did not work on older 5.x versions, which were never tested. The tests now also run on the lowest supported versions before each release.

## [1.2.3] - 2026-11-10

### Fixed

- A table wider than the page now shades the edge where more columns are hidden: the right edge until you reach the last column, and the left edge once you have scrolled (with frozen columns, their own edge shadow does that). With the scrollbar hidden, as macOS and trackpads do until you scroll, the last visible column looked cut off and nothing showed the table could scroll. It works with a sticky header, `warpHeight()` and in dark mode.

## [1.2.2] - 2026-11-07

### Fixed

- When a table falls back to the native table because of a column layout (`Split`, `Stack`, `Panel`), Filament's bar
  with the "select all" checkbox and the sort menu is shown again. Until 1.2.1 Warp Table's CSS hid it in every state,
  so these tables had no way to select the whole page or to sort.
- A table with Warp Table turned off (`->warp(false)` or a closure that returns `false`) no longer gets Warp Table's
  class on its wrapper, so it is exactly Filament's own table.

### Changed

- README, guideline and skill: use `stackedOnMobile()` for cards on phones (a column layout turns Warp Table off on
  every screen), `warpHeight()` for a box of fixed height (not CSS `max-height`), give users of screen readers the
  native table with `->warp(fn () => ...)`, and check `isWarp()` in tests instead of the HTML.

## [1.2.1] - 2026-11-06

### Changed

- README: a "Testing inline edits" section with a full example of a test that edits cells through
  `updateTableColumnState()`: a saved value, a value refused by `->rules()`, a toggle, an unknown select option, and
  the other cases that return `['error' => ...]` without saving.

## [1.2.0] - 2026-11-06

### Added

- Instructions for AI coding assistants (Claude Code, Cursor, Codex) through [Laravel Boost](https://laravel.com/docs/boost):
  a guideline that Boost adds to `CLAUDE.md` and `AGENTS.md`, and the `warp-table-development` skill for adding Warp
  Table to a table, using its options and testing it. Select `qalainau/filament-warp-table` in
  `php artisan boost:install`, or add it to `"packages"` in `boost.json`. See
  [AI coding assistants (Laravel Boost)](README.md#ai-coding-assistants-laravel-boost).

- Screen readers and browser tests can read the cells on screen. The text of the visible rows and columns is kept in a
  hidden `role="table"` next to the canvas (rows carry `data-record`, cells `data-column`), so `getByText()`,
  `getByRole('cell')` and `innerText` find the values. It holds only what is on screen and follows scrolling, sorting,
  search, filters, saved cells and refreshes. Toggles and checkboxes read "Yes" / "No" in the panel's language.
- `cellPoint({ record, column })` / `cellPoint({ row, column })` on the table's Alpine component returns where a cell is
  on the screen (center and box in viewport pixels), scrolling it into view first, so browser tests and screen
  recordings can click a cell without hard-coded coordinates. `whenIdle()` waits until requests, cell saves and the
  redraw are done. See "Browser tests and screen recordings" in the README.

### Fixed

- Filament's `assertCanSeeTableRecords()` and `assertCanNotSeeTableRecords()` did not work with Warp Table: they look
  for the row markers of the native table in the HTML, and the rows are drawn on a canvas. Warp Table now puts the same
  markers into the page, so both assertions (also `inOrder: true`) check the records the table shows, including with
  tenancy. The README's "Testing" section said to use `getTableRecords()` instead; it now shows both, plus a "Testing
  with tenancy" example.

## [1.1.15] - 2026-10-28

### Fixed

- A grouped table with summaries ran each summary query twice: the group summaries and the summary of all records (and
  the page summary) were calculated once by Filament's table view, which also renders a summary table that Warp hides,
  and again by Warp, which draws the summary rows itself. On large tables this could double the time to open the page.
  Warp now leaves the summaries to itself, so each one runs once, like the native table (the queries are the same as
  the native table's).

## [1.1.14] - 2026-10-28

### Fixed

- After a group was expanded or collapsed, the table area (`.fi-warp-tbl-stage`) of a table that fits on the screen
  could stay 15px taller than its content, leaving a gap under the table. The height made room for a horizontal
  scrollbar whenever one was there at the moment of measuring, even if it was only there for a moment. A table that
  fits on the screen has no horizontal scrollbar, so its height is now always the height of its content.
- After showing a column that comes before the visible ones (for example a column hidden by default), its search field
  was added after the others in the page, so Tab moved through the column search fields out of the column order
  (back to the left). The fields are now always in the column order, and a field you are typing in keeps its focus
  and caret when they are reordered.

## [1.1.13] - 2026-10-28

### Added

- Columns with `searchable(isIndividual: true)` now have their search field, as in the native table: a row under the
  column headers with Filament's own search field for each of these columns (`wire:model.live.debounce` on
  `tableColumnSearches.<column>`, with the table's debounce, or on blur with `searchOnBlur()`). The column widths include
  the fields, so the table is as wide as the native one. The fields keep their focus and text while the table
  updates, scroll sideways with their columns, follow the columns that are shown or hidden, and stay under the header
  with `warpStickyHeader()` and inside `warpHeight()`. Until now the row was missing, so these columns could only be
  searched from code, and the columns were narrower than in the native table.

## [1.1.12] - 2026-10-28

### Fixed

- The header row was 1px lower than in the native table, so every row border below it was 1px higher. The native
  table uses `border-collapse: collapse`, which draws the 1px line under the header outside the header cells: the
  header background is 48px high with the line below it. The header now has the same height.
- Text was drawn about 1px higher than in the native table (in the header, in plain text cells and in every other
  cell). Text is now placed on the same baseline as the browser's: the font's ascent and descent (rounded) sit in the
  line box with the half-leading rounded down, and the baseline is snapped to the device pixel.

## [1.1.11] - 2026-10-28

### Fixed

- A table whose record actions were all hidden (`hidden()` / `visible(false)` for every record) still had a 36px
  empty actions column, so in a table that fits on the screen the other columns were narrower and further left than
  in the native table (up to 22px at the right). As in the native table, the actions column is now only there when at
  least one record has a visible action.
- When the width of a table that fits on the screen shrank (for example when the page's vertical scrollbar appeared
  just after loading), the table's content stayed at the old width for a frame, until the columns were measured
  again: the scroll area (`.fi-warp-tbl-stage`) was briefly wider than its visible width, so it could be scrolled
  sideways and scripts that compare `scrollWidth` with `clientWidth` saw an overflow. The canvas, and in a table that
  fits also the content, the overlays, the summary rows and the group headers, now follow the visible width in the
  same layout as the browser, so the content is never wider than the table.
- An empty date input in Japanese (and Chinese) showed `年/月/日` starting 4px too far left. As in the browser's
  date input, the year field is wider than `年` and the text is centred in it (`年 /月/日`, 18px from the left edge of
  the input).

## [1.1.10] - 2026-10-28

### Fixed

- A badge whose label is empty (for example `formatStateUsing()` returning `''` for some rows) was drawn as a small
  square as tall as a badge with text. It is now drawn as in the native table: the label takes no height, so the badge
  is a thin bar made of its padding (20×4px for `sm`, 24×8px for `md` and `lg`), sitting just below the text baseline,
  with the same color as the native badge. This also holds when empty and labeled badges share a cell, in lists with
  line breaks and for other badge sizes. Badges with text are drawn as before; in lists of badges over several lines,
  each line is now placed at the measured line height, as the native table does (1px lower per line than before).
- Spaces in cell text were drawn as they are, while the native table shows text with the browser's normal white-space
  handling. A run of spaces made a wide gap (`0484571␣␣␣␣NTT…`), text could end in "…" earlier than in the native
  table, and a leading space moved the text 4px to the right. Text, badges, descriptions and placeholders are now drawn
  and measured (column widths, "…" truncation, `wrap()` and `lineClamp()`) as the browser shows them: runs of spaces,
  tabs and line breaks become one space, and spaces at the start and the end are not shown. Ideographic spaces
  (`　`, U+3000) and no-break spaces are kept, as in the browser. Cells styled with `white-space: pre` (or `pre-wrap`,
  `break-spaces`) through `extraAttributes()` keep their spaces. Only the display changes: the values sent to the
  browser, copied cells and inline editing keep the original text.

- A date input (`TextInputColumn::make()->type('date')`) was drawn as an empty box when it had no value, and its value
  as plain text (`2026-09-15`) without the calendar icon. It is now drawn as the browser's date input in the native
  table: the date in the browser's format (`09/15/2026`, `2026/09/15` in Japanese), the field markers when it is empty
  (`mm/dd/yyyy`, `年/月/日`) and the calendar icon on the right. `time` (with the clock icon), `datetime-local`, `month`
  and `week` inputs are drawn the same way.
- Picking a date in a date input saved nothing until the input lost focus. Inputs of type `date`, `time`,
  `datetime-local`, `month` and `week` are now saved on `change`, as in the native table (`x-model.lazy`), and stay open
  so the next field can be typed; a value picked while the previous one is still saving is saved after it, and Enter or
  leaving the input does not send the same value again. Text and number inputs are saved as before.
- A placeholder of an empty cell (`TextColumn::placeholder()`, and the same for icon, color and image columns) could be
  cut with "…" (`未…`), because the column width was measured without the padding the native table puts around it. The
  column is now as wide as in the native table, which also makes the whole table as wide as the native one.
- In a `SelectColumn` with `searchableOptions()` or `native(false)`, a long selected name was cut with "…" on one line.
  It now wraps inside the select as in the native table (`canOptionLabelsWrap()`, on by default), and the select and
  its row are as tall as the wrapped name. With `canOptionLabelsWrap(false)` it stays on one line with "…", as before.

## [1.1.9] - 2026-10-28

### Fixed

- Wrapped text with a line limit (`wrap()` + `lineClamp()`) could end in "…" earlier than the native table. Japanese,
  Chinese and Korean text now breaks between any two characters, as the browser does, so a part after a space
  (including the ideographic space `　`) fills the room left on the line before. Runs of Latin letters and digits
  (`D25101601`, URLs) still stay whole and are only split when they are wider than the column. Line breaks follow the
  browser's rules: no break before closing brackets and punctuation (`）」、。`) or after opening brackets, a break
  after the hyphen in `Type-R`, none at a no-break space, and a closing bracket at the end of a line is narrowed to
  half its width when it would not fit otherwise. Text is also measured with the same kerning as the page (Japanese
  fonts were measured about 1.4% wider), and a line that is wider than the column by more than 0.1px now wraps, as
  in the browser (it was 1px).
- The right side of a group header (the collapse button and anything else on the right) was 12px further right than
  in the native table. At the edges of the row, group headers now have the native padding (`sm:ps-6` / `sm:pe-6`),
  on the left too when there is no checkbox column.

## [1.1.8] - 2026-10-25

### Fixed

- With Warp Repeater in the same app, a Warp Repeater showed only its first row. Both plugins' CSS used the same class
  names (`.fi-warp-stage`, `.fi-warp-canvas` and others) and is loaded on every page. Warp Table's classes and CSS
  variables now start with `fi-warp-tbl` (`.fi-warp-tbl-stage`, `--fi-warp-tbl-height`), so they no longer reach other
  plugins. If your own CSS targets a Warp Table class, add `tbl-` after `fi-warp-` (`.fi-warp` becomes `.fi-warp-tbl`,
  `.fi-warp-ta` becomes `.fi-warp-tbl-ta`).

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
