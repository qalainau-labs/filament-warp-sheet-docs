# Changelog

All notable changes to `qalainau/filament-warp-sheet` will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.6.0] - 2026-11-14

### Added

- `visible()` and `hidden()` on columns, with a closure to decide per user: a hidden column's values are not sent to the browser, and saves, imports, bulk edits, exports, totals, history, search, sort, filters, grouping and saved views cannot use it. A computed column whose formula uses a hidden column is hidden too. See "Columns only some users may see" in the README.

### Fixed

- On PostgreSQL, searching a sheet whose `searchable()` names a number or date column no longer fails with a server error (`operator does not exist: integer ~~* text`).
- The bulk edit form no longer closes when you pick a column right after editing a cell or scrolling. While the sheet was still loading cell colors or rows in the background, a reply that arrived after the form had opened put the page back to its earlier state, so the next click closed the form. The same could close the record history and the "Save view" form.

## [1.5.4] - 2026-11-13

### Fixed

- A sheet in a relation manager (`SheetRelationManager`) can save large changes again. A save over about 600 KB, such as pasting thousands of rows, is sent in parts, and that failed with a server error (`canSaveWarpSheet does not exist`). Matching the rows of an imported file failed the same way.

## [1.5.3] - 2026-11-12

### Changed

- Now requires Filament 5.9 or later (was 5.0). Parts of the plugin did not work on older 5.x versions, which were never tested. The tests now also run on the lowest supported versions before each release.

## [1.5.2] - 2026-11-10

### Fixed

- Totals are no longer cut off (`Sum 23,024,…`). A column you have not resized grows to fit its totals. If you drag it narrower, pointing at the total shows it in full, and double-clicking the header edge fits the totals too.
- A sheet that fills the rest of the window (no `height()`) no longer shrinks to about six rows on a page with a long description or a tall toolbar. It stays at least 28rem tall, or 60% of a short window, and the page scrolls instead.

## [1.5.1] - 2026-11-07

### Documentation

- "Testing with tenancy" now calls `Filament::bootCurrentPanel()` in the example. A Livewire test does not go through the panel's middleware, so without it Filament adds no tenant scope to the resource's model and puts no team on new records; the example as it was listed every team's rows. The AI guidelines and the skill say the same.
- A new "Feature tests (PHP)" section lists the methods a feature test calls — `warpSheetKeys()`, `warpSheetRows()`, `warpSheetSave()` and `warpSheetOptions()` (the matching options of a select column, prefix matches first, at most 50) — with examples.
- The AI guidelines' first example notes that `Rule::unique()` needs the team on a panel with tenancy.

## [1.5.0] - 2026-11-06

### Added

- A JavaScript API for browser tests: `cellPoint()` finds a cell on the canvas (scrolling it into view), `cellValue()` reads what a cell holds including unsaved edits and its error, and `whenIdle()` waits until loading and saving are done. See "Browser tests (JavaScript)" in the README.

## [1.4.2] - 2026-11-06

### Fixed

- The docblock of `Column::color()` said colors come only from saved values and are not given to cells being
  edited. Since 1.3.0 a closure runs again on the unsaved values of a changed row, as the README says; the docblock now
  says so too.

## [1.4.1] - 2026-11-06

### Fixed

- Rules that name another column of the record (`gte:cost`, `after:starts_on`) refused good values, because only the
  changed cells were given to the validator, and changing only the other column (`cost`) never checked them. They now
  see the record's saved values together with the changed ones, and a rule that names a changed column is checked
  again (its message is shown on its own cell).

## [1.4.0] - 2026-11-06

### Added

- Instructions for AI coding assistants (Claude Code, Cursor, Codex) through [Laravel Boost](https://laravel.com/docs/boost):
  a guideline that Boost adds to `CLAUDE.md` and `AGENTS.md`, and the `warp-sheet-development` skill for building
  sheets and testing them. Select `qalainau/filament-warp-sheet` in `php artisan boost:install`, or add it to
  `"packages"` in `boost.json`. See [AI coding assistants (Laravel Boost)](README.md#ai-coding-assistants-laravel-boost).
- `php artisan warp:assign-tenant "App\Models\Team" 1`: for apps that add Filament's tenancy after saving views. Saved
  views without a tenant are hidden from every tenant; the command gives them (`warp_sheet_views`) to one tenant, together with
  the rows without a tenant of every other installed Warp plugin, in one transaction. `--dry-run` only counts them.
  See "Adding tenancy to an app that already has saved views" in the README.
- README: "Testing with tenancy". Another tenant's records and saved views are treated like ones that do not exist
  (no exception or 403), with a table of what each call returns and an example test of what a tenant sees.

## [1.3.1] - 2026-11-02

### Fixed

- A few translations read more naturally: Japanese, Korean, French and Brazilian Portuguese wording in the import, bulk
  edit, grouping and history messages.
- Numbers typed, pasted or imported into a `Number` column are read with the page language's separators: in German,
  French, Spanish, Italian and Brazilian Portuguese `12,50` is 12.5, `1.234,5` is 1234.5 and `1.250` is 1250 (before,
  every comma was dropped, so `12,50` became 1250 and `1.234,5` became 1.2345). When the other separator appears once
  and is not followed by exactly three digits it is a decimal point, so `12.5` in German and `12,50` in English are
  12.5. Number cells of Excel files keep their value in every language.

## [1.3.0] - 2026-10-31

### Added

- Columns from related records: name a column after a relationship and an attribute with a dot
  (`Columns\Text::make('product.sku')`, also nested like `product.category.name`). The column is read-only and never
  saved, its relationships are eager-loaded with the rows, and it is empty for rows without a related record. It can be
  sorted, searched, filtered, totalled, exported and used as the import key (`importable(key: 'product.sku')`).
- Cell colors decided by a `color()` closure now follow unsaved edits: the sheet asks the server for the colors of the
  edited rows on screen (nothing is saved), so a computed column is colored as soon as a value it uses is typed.

### Changed

- A total without any value shows `0` for `sum` and `count` and `—` for `avg`, `min` and `max`, instead of the bare
  label ("Sum").
- A fixed color (`->color('info')`) also stays on cells the user has changed.

## [1.2.0] - 2026-10-31

### Added

- German, French, Spanish, Italian, Brazilian Portuguese, Korean and Chinese (simplified and traditional) translations.
- Importing Excel's "Unicode Text" (UTF-16, tab-separated) files.

### Fixed

- When an imported file could not be read (too large, not a CSV or xlsx file, an old .xls file, and so on), the reason
  was always shown in English. It now follows the app's language.
- Images, PDFs and other files that are not tables were read as rows of garbage; they are now refused. An old Excel
  97–2003 file (.xls) gets a message to save it as .xlsx or CSV.
- Rows with only spaces (left over in a spreadsheet) are skipped like empty rows.

## [1.1.1] - 2026-10-25

### Fixed

- After publishing the migrations (`php artisan vendor:publish --tag=filament-warp-sheet-migrations`, as the README
  says for editing them), `php artisan migrate` stopped with "table already exists": Warp Sheet ran its own copy as
  well. A published migration now replaces the bundled one; migrations you have not published still run as before.

## [1.1.0] - 2026-10-25

### Upgrading

- Run `php artisan migrate` after updating. The new `update_warp_sheet_keys_to_strings` migration turns
  `warp_sheet_views.owner_id`, `warp_sheet_views.tenant_id` and `warp_sheet_changes.user_id` into text columns and keeps
  the saved views and the history.

### Added

- A "Multi-tenancy" section in the README: the panel setup, what is kept per team, the columns it needs, how to turn the
  scoping off or use another relationship name, and what to watch.

### Fixed

- On a panel with tenancy, the column of the tenant's ownership relationship (`team_id`, a `morphTo`'s type and id, or a
  `MultiSelect` of a `belongsToMany` ownership) is read-only. Users could change it and move a record to another team.
- On a panel with tenancy, a sheet that shows the tenant key column can add rows again. New rows were refused because the
  column was required, although Filament fills it in when the record is created.
- Saved views and the history work for teams and users with UUID or ULID keys. `warp_sheet_views.owner_id`,
  `warp_sheet_views.tenant_id` and `warp_sheet_changes.user_id` were integer columns, so saving a view, or a save of a
  sheet with `auditLog()`, failed on MySQL, MariaDB and PostgreSQL. Run `php artisan migrate` after updating: the new
  migration `update_warp_sheet_keys_to_strings` turns them into text columns and keeps the saved views and the history.

## [1.0.6] - 2026-10-20

### Fixed

- A model event that cancels a save (`saving`, `updating`, `creating` or `deleting` returning `false`) no longer shows as
  saved. Nothing is saved and the record is marked with an error. A cancelled new row used to come back without a key.
- Saved rows show the values the database has, including columns an observer, a trigger or a generated column wrote.
- Bulk edit sets date times and times as typed. With a Filament timezone other than the app's, they were shifted by the
  difference between the two timezones.
- Grouping by a date column whose database column also stores times adds up the subtotals of the whole day. They
  used to show only one of its times.
- Grouping on a case-insensitive collation (MySQL's default) puts "Apple" and "apple" in one group. They used to
  alternate in many small groups, most of them without subtotals.
- A date time column's filter uses the days of the column's timezone (`->timezone()`), not the app's.
- The totals are worked out again after a save also when their columns are hidden, so showing the column again no
  longer shows the totals from before the save. A late answer no longer replaces the totals of a newer search.
- Grouping by another column opens every group. Groups with the same value in the new column used to stay collapsed.
- Fill down, fill right, the fill handle and copying write to and copy the rows that were selected, even when the sheet
  is sorted or filtered while their rows are still loading.
- "Review" fills in the rows and old values that were still loading when it was opened.
- Unsaved changes keep the version they were edited on when the sheet reloads its rows after a bulk edit or the record
  form, so someone else's change in between is still a conflict instead of being overwritten.
- A record changed in the same second as the sheet listed the records counts as a conflict for rows written without
  being loaded. `updated_at` is kept in whole seconds, so such a change used to be missed.
- A changed record that someone else deleted is taken out of the sheet with its changes, with a message. It used to
  stay as a conflict that every save reported again.
- Find (Ctrl/Cmd+F) looks for its matches again after sorting, filtering or saving, and no longer shows "…" after its
  text is cleared quickly.
- A sheet widget that reads the dashboard's page filters (`InteractsWithPageFilters`) lists its records again when they
  change.
- After a save, bulk edit or the record form, the other sheets on the page that show the same model reload their rows.

## [1.0.5] - 2026-10-20

### Changed

- Support moves to https://warpui.com/support?plugin=sheet: questions before and after buying, bug reports (also
  private ones) and an email when the fix is released. The README, the license agreement and the `support` links in
  `composer.json` point there, and `composer.json` links the public docs instead of the private repository.

## [1.0.4] - 2026-10-20

### Changed

- The license agreement no longer lists a Lifetime tier. The tiers are Single Project and Unlimited Projects, each
  with one year of updates.
- The license agreement adds a refund policy (section 5): a full refund within seven days of purchase for a defect
  that cannot be fixed or worked around.

## [1.0.3] - 2026-10-20

### Changed

- Sorting, filtering, searching, grouping and applying a view keep the cursor and the scroll position where they are,
  like a spreadsheet. The cursor used to follow its record to the new position, so a sort could jump the view to row
  46,000 of 100,000. After saving while grouped, the cursor still follows the edited record into its new group.

## [1.0.2] - 2026-10-19

### Security

- A relation manager's parent key column (and morph type) is always read-only, so rows cannot be moved to or created under another owner.
- Multi-select relationships accept only whole-number keys for integer keys (MySQL rounded "1.9" to another record), and select values must be integers or strings.
- Dates with times reject minutes and seconds over 59; PostgreSQL saves no longer cut long text silently; validation results are kept apart for new and saved rows.
- Malformed changes (keys that are arrays, values that are not lists) are ignored instead of failing with a server error; filters and imports skip non-numeric values for integer columns.
- Notification bodies built from validation and `SaveAborted` messages are escaped.
- Exported CSV neutralizes formulas behind spaces, line breaks, invisible characters and full-width signs; exported xlsx files stay valid with any text (U+FFFE/U+FFFF, broken UTF-8, cells over 32,767 characters, sheet names with brackets); export file names drop invisible formatting characters.
- Staged changes keep only deletes, updates and creates; guests stage per session; read-only relation managers and sheets that cannot save refuse staging and import matching.
- Saved views are limited in size and to 100 per user and sheet, and keep only the table's own filters. Exports are limited to 10 a minute per user.
- Search in the sheet and sorting by a select column use the relationship's narrowed query; opening a record that is not in the sheet no longer fails with a server error.
- Imports cap the number of cells, multi-select text is split in linear time, and long text skips the series and email checks in the browser.

## [1.0.1] - 2026-10-19

### Changed

- The commercial license agreement is now one text for every Warp plugin (the plugin you purchase is the "Software"); the terms are unchanged.

### Fixed

- The right-click menu items (add, delete, collapse, ungroup) could not be clicked.
- Pasting one value into a range that crosses group headings wrote to rows below the range, and pasting onto collapsed headings added rows.
- Pasting below the last row is one undo step, including the rows it adds; new rows are shown even when the group of new rows was collapsed; undone and redone new rows go back to the end.
- Clearing a boolean cell turns it off, and boolean cells no longer open a text editor (F2, Ctrl+Enter or an input method).
- Columns the database does not let be empty are checked before saving, and database errors are reported without a server error.
- Pressing Enter right after typing in a select searched on the server waits for the results instead of keeping the typed text.
- Redoing an edit back to the saved value no longer marks the cell as changed; reverting a row's changes clears its conflict mark.
- A cell's error is shown in the status bar before a short message from another cell; a save that stops on a conflict moves to the conflicting cell; Review says when a change is in a row the search or filters hide.
- The filter panel takes the keyboard every time it opens (typing went into the sheet when the previous panel was for a column of the same type), and stays open however long the funnel is held.
- Imported changes to records outside the current search or filters are listed in a group of their own while grouping; Review's row numbers follow collapsing and expanding; Ctrl+H in the find box opens replace instead of deleting a character.
- With several sheets on one page, one of them could stay empty: each sheet now keeps its own requests and listeners.
- Rows opened in a widget's `recordForm()` could not be saved.
- Filament's filters dropdown was cut off by the sheet's container.
- Leaving the page through the panel's SPA navigation asks first when the sheet has unsaved changes.
- The status bar sums only number columns (select values such as foreign keys are counted, not added), and the cell editor follows its row when the rows are reloaded.
- Scrolling quickly no longer leaves the rows on screen empty while the rows scrolled past load first: the rows on screen are loaded first, chunks scrolled past are dropped, and up to three chunks load at once.

## [1.0.0] - 2026-10-16

### Added

- `SheetPage` to edit the records of a Filament resource as a canvas-rendered spreadsheet.
- Scrolling through the whole query result without pagination: the sheet lists the sorted and filtered primary keys first and loads the rows on screen in chunks, so the scrollbar is exact and the cursor stays on the same record after sorting.
- Sorting on the server (select columns by their labels), search that treats `%` and `_` as plain characters, and Filament table filters.
- Spreadsheet editing: a cell cursor, typing to replace, F2 to edit, range selection, copy and paste with Excel and Google Sheets, fill down, undo and redo, frozen columns, and the count, sum and average of the selection.
- A fill handle to extend the selection with series of numbers, dates and numbered texts, Ctrl/Cmd+R to fill right, and Ctrl+; / Ctrl+Shift+; for today's date and the current time.
- Column resizing by dragging the header edge (double-click to fit), and a "Columns" menu to hide and reorder columns, remembered in the browser.
- Saved views of the column arrangement, sort, search and filters, private or shared, with a default per user (`savedViews()`, `shareableViews()`, `viewKey()`), stored in the `warp_sheet_views` table.
- Grouping rows by a column from its header menu, with counts and subtotals per group worked out in the database, collapsible groups, and the grouping saved in views (`groupable()`).
- A row of totals for every record matching the search and filters (`summarize('sum')`, `avg`, `min`, `max`, `count`), worked out in the database.
- Computed columns (`Columns\Computed::make()->formula('price * stock')`) recalculated in the browser as other cells change, sortable in the database and exported.
- Image columns (thumbnails from a URL, a disk path or a closure) and link columns opened with Ctrl/Cmd+click or the right-click menu, never for `javascript:` URLs.
- Columns: `Text`, `Textarea`, `MultiSelect` (a `belongsToMany` relationship synced on save, or an array attribute), `Number` (with currencies, prefixes and suffixes), `Select` (static options, a backed enum, or a `belongsTo` relationship searched on the server when it has many options), `Date`, `DateTime` (with a display timezone), `Time` and `Boolean`, with labels, widths, rules and read-only columns.
- Read-only cells decided record by record (`readOnly(fn (?Model $record) => ...)`) and cell colors from the panel's palette (`color()`).
- An audit log of every save from the sheet, with old and new values, user and time per record, shown from the right-click menu (`auditLog()`), stored in the `warp_sheet_changes` table.
- Find and replace in the sheet (Ctrl/Cmd+F, Ctrl+H) across every matching record, with highlights, match case, and Replace all as one undo step.
- Column filters from a button in every header — contains, ranges, periods and lists of values — run in the database and applied to totals, exports, bulk edit and views.
- Opening a record in the resource's form in a modal (double-click a row number, or the right-click menu), read-only when the user may not edit it (`recordModal()`).
- `SheetWidget` and `SheetRelationManager` to put a sheet on a dashboard or in a relation manager (`hasMany`, `morphMany`), with `recordForm()` and `recordTitleAttribute()` for opening rows; several sheets can share a page.
- A right-click menu (also Shift+F10 and the menu key) to open, add and delete rows.
- "Review": a panel listing the unsaved changes with their old and new values, to jump to them or revert a cell or a record.
- Simple column rules (`required`, `numeric`, `min`, `max`, `email`, date formats…) checked in the browser as soon as a cell changes, with Laravel's messages.
- Saving every change in one go, all or nothing: policies, validation, conflicts with changes made by others (merged when they touched other columns), and errors and conflicts marked on the cells. Adding and deleting rows are part of the same save.
- `beforeSave()` and `afterSave()` hooks that run inside the save's transaction, with the keys of the created, updated and deleted records. Throwing `SaveAborted` cancels the save with a message.
- Bulk edit on the server of the selected rows or every row matching the search and filters (`bulkEditable()`): set, clear, find and replace, add, change by a percentage, all or nothing.
- Excel and CSV export of the saved records with the current search, filters and sort (`exportable()`).
- CSV, TSV and Excel import matched by a key column (`importable(key: ...)`), read in the browser, that turns only the differing cells into changes to review and save.
- Fast large saves: changes are sent in parts, checked and written in chunks of 500 rows with prepared `UPDATE` statements (on MySQL, MariaDB and PostgreSQL, 100 rows per statement), with progress in the toolbar. `maxChangesPerSave()` limits a save (100,000 records by default) and `modelEvents()` saves through Eloquent when the model has listeners.
- The sheet fills the rest of the window by default, so the status bar stays on screen whatever is above or below it. `height()` sets a fixed height.
- Dark mode, the panel's colors and English and Japanese translations.
- Typing with an input method (Japanese, Chinese, Korean) from the first character of a cell, and full-width digits read as numbers and dates.
- A sheet page reads and writes the records of its resource's `getEloquentQuery()` unless it is given another query.
- Select columns check values with the relationship's query (constraints, global scopes, tenancy) instead of `exists`, and `relationship()` takes a closure to narrow it.
- Saves can only be staged on sheets that can save, in parts of at most 5,000 rows and a few saves per user at a time.
- Infinite numbers are refused, and formulas that overflow are left empty.
- Imported xlsx files are read in linear time, with limits on the file size, the inflated size and the number of columns.
- Links and images only open `http`, `https`, `mailto` and `tel` URLs.
- A multi-select cell takes at most 500 values; search and "contains" filters are cut at 255 characters; impossible dates in filters are ignored.
- Records cannot be opened from the browser when the sheet does not open them, and every view method checks access to the sheet.
