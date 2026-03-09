# Vue3 Excel Editor - Source Code Summary

## 📋 Quick Overview

**Vue3 Excel Editor** is a powerful Vue 3 plugin that provides Excel-like spreadsheet functionality for displaying and editing array-of-objects. It offers a rich set of features including filtering, sorting, pagination, export/import, and real-time data binding.

**Version:** 1.0.60  
**Author:** Kenneth Cheng  
**License:** MIT

---

## 🏗️ Architecture & Component Structure

```
src/
├── main.js                    # Entry point & plugin registration
├── VueExcelEditor.vue        # Main component (3000+ lines)
├── VueExcelColumn.vue        # Column definition component
├── VueExcelFilter.vue        # Filter row component
├── PanelFilter.vue           # Filter dialog panel
├── PanelSetting.vue          # Settings/Export panel
└── PanelFind.vue             # Find dialog panel
```

### Component Hierarchy

```
VueExcelEditor (Parent)
├── VueExcelFilter (Filter row cells)
├── PanelFilter (Modal dialog)
├── PanelSetting (Modal dialog)
├── PanelFind (Modal dialog)
└── DatePicker (Date picker)
```

---

## 🎯 Key Features

1. **Excel-like UI** - Table with fixed headers, sticky columns, custom scrollbars
2. **Real 2-way Data Binding** - v-model with reactive updates
3. **Column Filtering** - Regex, wildcards, operators (>, <, =, >=, <=, <>)
4. **Column Sorting** - Ascending/descending with custom sort functions
5. **Export/Import** - Excel (.xlsx) and CSV support via xlsx library
6. **Pagination** - Auto-calculated page size or manual
7. **Row Selection** - Multi-select with Ctrl/Cmd and Shift keys
8. **Mass Update** - Update all selected rows simultaneously
9. **Keyboard Navigation** - Arrow keys, Tab, Enter, Esc, Page Up/Down
10. **Hot Keys** - Ctrl+A (select all), Ctrl+C (copy), Ctrl+V (paste), Ctrl+Z (undo), Ctrl+F (find), Ctrl+G (find next), Ctrl+L (autocomplete)
    10.1 **Multi-line Editing** - Shift+Enter inserts newlines in cells, with dynamic height adjustment
11. **Validation** - Column-level and row-level validation
12. **Autocomplete** - Dynamic suggestions from existing data
13. **Date Picker** - For date/datetime fields
14. **Column Customization** - Width, visibility, sequence, sticky
15. **Summary Row** - Sum, avg, max, min, count calculations
16. **Undo/Redo** - Transaction history for updates
17. **Localization** - Customizable labels

---

## 📦 Core Components

### 1. VueExcelEditor.vue (Main Component)

**Purpose:** The central spreadsheet component managing all data, state, and interactions.

**Key Data Properties:**

- `table` - Filtered/sorted array of records
- `filteredValue` - Raw filtered results
- `modelValue` - Original data source (v-model)
- `fields` - Column definitions array
- `selected` - Hash of selected row IDs
- `selectedCount` - Number of selected rows
- `columnFilter` - Hash of column filter strings
- `pageTop` - Current page starting index
- `pageSize` - Number of rows per page
- `currentRecord` - Currently focused row data
- `currentField` - Currently focused column definition
- `focused` - Editor focus state
- `inputBox` - Textarea DOM reference
- `errmsg` - Validation error messages (hash)
- `rowerr` - Row-level validation errors
- `redo` - Undo transaction buffer
- `sortPos` - Currently sorted column position
- `sortDir` - Sort direction (0=none, 1=asc, -1=desc)

**Computed Properties:**

- `pagingTable` - Current page records slice
- `pageBottom` - Current page ending index
- `columnFilterString` - JSON string of filters (for watching)
- `token` - Unique component instance identifier
- `selectedCount` - Get/set with v-model support

**Major Methods Categories:**

#### Data Management

- `refresh()` - Recalculate table from modelValue
- `calTable()` - Apply filters, sorting, grouping
- `reset()` - Clear filters, selection, sort
- `registerColumn(field)` - Add column definition
- `autoRegisterAllColumns(rows)` - Auto-create columns from data
- `getKeys(rec)` - Get key field values
- `getFieldByName(name)` - Get field by name
- `getFieldByLabel(label)` - Get field by label

#### Filtering & Sorting

- `setFilter(name, text)` - Set column filter
- `clearFilter(name)` - Clear column filter
- `columnSuppress()` - Hide empty columns
- `columnAutoWidth(name)` - Auto-calculate column width
- `columnFillWidth()` - Fill remaining width
- `sort(n, pos)` - Sort by column
- `filterGrouping(rec, i, table)` - Apply grouping logic

#### CRUD Operations

- `newRecord(rec, selectAfterDone, noLastPage, isUndo)` - Add new row
- `deleteRecord(valueRowPos, isUndo)` - Delete row
- `deleteSelectedRecords()` - Delete all selected
- `updateCell(row, field, newVal, isUndo)` - Update single cell
- `updateSelectedRows(field, setText)` - Mass update

#### Selection

- `selectRecord(rowPos)` - Select row
- `unSelectRecord(rowPos)` - Deselect row
- `selectRecordByKeys(keys)` - Select by key fields
- `selectRecordById(id)` - Select by $id
- `toggleSelectAllRecords()` - Toggle all rows
- `clearAllSelected()` - Deselect all
- `getSelectedRecords()` - Get selected row objects
- `reviseSelectedAfterTableChange()` - Update selection after data change

#### Navigation & Focus

- `moveTo(row, col)` - Move cursor to position
- `moveNorth()`, `moveSouth()`, `moveWest()`, `moveEast()` - Arrow navigation
- `moveInputSquare(rowPos, colPos)` - Position editor overlay
- `inputBoxBlur()`, `inputBoxComplete()` - Handle focus loss

#### Import/Export

- `exportTable(format, selectedOnly, filename)` - Export to xlsx/csv
- `importTable(cb, errCb)` - Import from xlsx/csv
- `doImport(e)` - Process file upload

#### Validation

- `setFieldError(error, row, field)` - Set column error
- `setRowError(error, row)` - Set row error
- `validateAll()` - Validate all cells
- `calSummary(name)` - Calculate column summaries

#### Autocomplete

- `calAutocompleteList(force)` - Build suggestion list
- `inputAutocompleteText(text, e)` - Apply suggestion

#### Search

- `doFind(s)` - Search for text
- `doFindNext()` - Continue search

#### Paging

- `firstPage()`, `lastPage()`, `prevPage()`, `nextPage()` - Page navigation
- `refreshPageSize()` - Recalculate page size

#### UI Helpers

- `calStickyLeft()` - Calculate sticky column positions
- `calVScroll()` - Calculate vertical scrollbar
- `showDatePickerDiv()` - Show date picker popup
- `lazy(fn, delay)` - Debounced execution
- `hashCode(s)` - Generate hash code

#### Event Handlers

- `winKeydown(e)` - Global keyboard events
- `winPaste(e)` - Paste handling
- `mousewheel(e)` - Scroll handling
- `mouseDown(e)` - Cell click handling
- `headerClick(e, colPos)` - Column header click
- `colSepMouseDown(e)` - Column resize start

---

### 2. VueExcelColumn.vue (Column Definition)

**Purpose:** Defines column properties and registers with parent editor.

**Key Props:**

- `field` - Field name (mandatory)
- `label` - Header label
- `type` - Data type (string, number, date, select, map, check10, checkYN, checkTF, action, badge, password, etc.)
- `width` - Column width
- `readonly` - Read-only flag
- `validate` - Validation function
- `change` - Change handler function
- `options` - Options for select/map types
- `keyField` - Primary key flag
- `sticky` - Sticky column
- `invisible` - Hidden column
- `summary` - Summary type (sum, avg, max, min, count)
- `sort` - Custom sort function
- `link` - Link click handler
- `isLink` - Link visibility checker
- `toText(val)` - Value to display text converter
- `toValue(text)` - Display text to value converter
- `autocomplete` - Enable autocomplete
- `allowKeys` - Allowed input characters
- `mandatory` - Mandatory field error message
- `lengthLimit` - Max length
- `textTransform` - Uppercase/lowercase
- `textAlign` - Text alignment
- `initStyle` - Initial CSS style
- `color`, `bgcolor` - Color functions
- `placeholder` - Placeholder text
- `grouping` - Grouping behavior (collapse/expand)
- `hideDuplicate` - Hide duplicate values
- `cellClick` - Cell click handler
- `listByClick` - Show options on click
- `format` - Output format (text/html)

**Lifecycle:**

- `init()` called on created to configure field and register with parent

**Type-Specific Behaviors:**

- `number` - Right-aligned, numeric input only
- `date/datetime/datetimesec` - Date picker, specific format
- `datetick/datetimetick/datetimesectick` - Unix timestamp conversion
- `select` - Dropdown options from array
- `map` - Dropdown options from object hash
- `check10/checkYN/checkTF` - Boolean checkboxes
- `password` - Masked input with masking character
- `action` - Action buttons with options list
- `badge` - Badge styling with bgcolor

---

### 3. VueExcelFilter.vue (Filter Row Cell)

**Purpose:** Editable filter input for each column.

**Key Features:**

- Contenteditable input
- Filter condition dropdown (click near right edge)
- Keyboard navigation (left/right arrows)
- Enter to apply filter
- Delete to clear filter
- Focus management

**Methods:**

- `updateValue(e)` - Emit value change
- `onFocus()` - Highlight column header
- `onBlur(e)` - Remove highlight, update value
- `keyWest(e)`, `keyEast(e)` - Navigate to adjacent filters
- `keyEnter(e)` - Select all, update value
- `keyDelete(e)` - Clear on empty
- `mouseDown(e)` - Show filter panel (click near right edge)

---

### 4. PanelFilter.vue (Filter Dialog)

**Purpose:** Modal dialog for advanced filtering and sorting.

**Features:**

- Sort ascending/descending buttons
- Filter condition selector (=, <>, >, <, >=, <=, ~, ≒)
- Custom filter input
- List of unique values (up to nFilterCount)
- Click value to set exact match

**Methods:**

- `showPanel(ref)` - Open dialog, populate unique values
- `hidePanel()` - Close dialog
- `sort(direction)` - Sort column
- `setFilterCondition(choice)` - Set condition operator
- `doFilter()` - Apply filter
- `filterPanelSelect(opt)` - Select from value list

**Data:**

- `inputFilter` - Current filter text
- `inputFilterCondition` - Current operator
- `sortedUniqueValueList` - Unique column values
- `filteredSortedUniqueValueList` - Filtered values list

---

### 5. PanelSetting.vue (Settings Panel)

**Purpose:** Modal dialog for table settings, column management, export/import.

**Features:**

- Export to Excel button
- Import from Excel button
- Draggable column list (using vuedraggable)
- Column visibility toggles
- Reset to default button

**Methods:**

- `showPanel()` - Open dialog
- `hidePanel()` - Close dialog
- `reset()` - Reset columns
- `exportTable(format)` - Export data
- `importTable()` - Import data
- `columnLabelClick(e, item)` - Toggle visibility

---

### 6. PanelFind.vue (Find Dialog)

**Purpose:** Simple search dialog.

**Features:**

- Text input for search term
- Search button
- Enter to search

**Methods:**

- `showPanel()` - Open dialog, focus input
- `hidePanel()` - Close dialog
- `doFind(s, e)` - Trigger search in parent

---

## 🔄 Data Flow

### Initialization Flow

```
1. VueExcelEditor mounted()
2. Read vue-excel-column children
3. registerColumn() called for each column
4. fields[] populated with column definitions
5. refresh() called
6. calTable() processes modelValue
7. Filters, sorting, grouping applied
8. table[] populated with filtered results
9. pagingTable computed for current page
10. DOM rendered
```

### Cell Edit Flow

```
1. User clicks cell
2. mouseDown() sets currentRecord, currentField, currentCell
3. moveInputSquare() positions inputSquare overlay
4. inputBox (textarea) shown/focused
5. User types/edits
6. inputBoxChanged = true
7. Tab/Enter/blur triggers inputBoxBlur()
8. inputBoxComplete() called
9. inputCellWrite() processes value
10. toValue() converts text to data value
11. updateCell() called
12. validate() if defined
13. change() callback if defined
14. Record updated
15. redo[] buffer updated
16. @update event emitted
17. calSummary() if summary column
18. DOM refreshed
```

### Filter Flow

```
1. User types in filter cell or opens PanelFilter
2. columnFilter[colPos] updated
3. columnFilterString computed changes
4. Watch triggers
5. processing = true
6. setTimeout(0)
7. pageTop reset to 0
8. refresh() called
9. calTable() filters records
10. table[] updated
11. processing = false
12. DOM re-rendered
```

### Sort Flow

```
1. User clicks column header
2. headerClick() called
3. sort() method invoked
4. If same column, toggle direction
5. modelValue.sort() executed
6. refresh() called
7. calTable() processes sorted data
8. table[] updated
9. DOM re-rendered
10. sortPos, sortDir updated
```

---

## 🔧 Key Algorithms

### Filter Parsing (calTable)

```javascript
Filters support:
- '' (empty) - contains
- '=value' - exact match
- '<>value' - not equal
- '>value' - greater than
- '>=value' - greater/equal
- '<value' - less than
- '<=value' - less/equal
- '~regex' - regex pattern
- 'value*' - starts with
- '*value' - ends with
- '*value*' - contains (explicit)
- Wildcards: * (any chars), ? (single char)
```

### Pagination

```javascript
pageSize calculated from:
- If page prop provided: use that
- Else: floor((containerHeight - header - footer) / rowHeight)
pageTop maintained across table changes
```

### Undo/Redo

```javascript
redo[] is a stack of transaction arrays
Each transaction = [{ type, $id, field, oldVal, newVal, keys }]
undoTransaction() pops and reverses changes
redo cleared on table change
```

### Autocomplete

```javascript
1. Get unique values from modelValue (limit: autocompleteCount)
2. Filter by input value (case-insensitive startsWith)
3. Sort alphabetically
4. Show dropdown
5. Arrow keys navigate
6. Enter selects
```

### Summary Calculation

```javascript
sum: reduce((a,b) => a + Number(b[i]||0), 0)
avg: sum / table.length
max: reduce((a,b) => a > b[i] ? a : b[i])
min: reduce((a,b) => a < b[i] ? a : b[i])
count: special logic per type (number>0, string non-empty, check=Y/1/T, date>=now)
```

---

## 🎨 Styling & CSS

### Key CSS Classes

- `.vue-excel-editor` - Main container
- `.systable` - Table element (fixed layout)
- `.table-content` - Scrollable wrapper
- `.sticky-column` - Fixed position columns
- `.first-col` - Row number column (sticky)
- `.select` - Selected rows
- `.focus` - Focused cell/column/row
- `.error` - Validation error cells
- `.readonly` - Read-only cells
- `.hideDuplicate` - Hidden duplicate values
- `.grouping` - Grouped cells
- `.link` - Clickable links
- `.badge` - Badge-styled cells

### Scrollbar Styling

- Custom vertical scrollbar (`.v-scroll`)
- Custom horizontal scrollbar (footer-based)
- No native scrollbars (width: 0, height: 0)

### Z-Index Layers

- 0-5: Normal table elements
- 10-15: Input square, autocomplete
- 50: Tooltips
- 999: Modal panels
- 1000: Processing overlay

---

## 📦 Dependencies

### Runtime Dependencies

```json
{
  "@vuepic/vue-datepicker": "^3.3.0", // Date picker component
  "vuedraggable": "^4.1.0", // Drag and drop for column reordering
  "xlsx": "^0.18.5" // Excel import/export
}
```

### Key Dependencies Usage

- **@vuepic/vue-datepicker**: Inline date picker for date/datetime fields
- **vuedraggable**: Draggable list in PanelSetting for column reordering
- **xlsx**: SheetJS for reading/writing Excel files

---

## 🎯 Props Reference (VueExcelEditor)

### Main Props

- `v-model` (Array) - Data source (mandatory)
- `page` (Number) - Page size (default: auto)
- `no-paging` (Boolean) - Disable pagination
- `no-num-col` (Boolean) - Hide row numbers
- `filter-row` (Boolean) - Show filter row
- `no-footer` (Boolean) - Hide footer
- `readonly` (Boolean) - All columns read-only
- `height` (String) - Fixed height
- `width` (String) - Max width (default: '100%')

### Selection Props

- `free-select` (Boolean) - Select without Ctrl/Cmd
- `v-model:selected-count` (Number) - Selected count binding

### Interaction Props

- `autocomplete` (Boolean) - Enable autocomplete
- `autocomplete-count` (Number) - Max suggestions (default: 50)
- `spellcheck` (Boolean) - Enable spellcheck
- `new-if-bottom` (Boolean) - Auto-add row at bottom
- `enter-to-south` (Boolean) - Enter moves down (default: right)
- `multi-line` (Boolean) - Multi-line cells (default: true)

### Feature Props

- `no-finding` (Boolean) - Disable Ctrl+F
- `no-finding-next` (Boolean) - Disable Ctrl+G
- `no-sorting` (Boolean) - Disable sorting
- `no-mass-update` (Boolean) - Disable mass update
- `no-mouse-scroll` (Boolean) - Disable wheel scroll
- `no-header-edit` (Boolean) - Disable header editing

### Customization Props

- `row-style` (Function) - Conditional row styling
- `cell-style` (Function) - Conditional cell styling
- `header-label` (Function) - Custom header text
- `record-label` (Function) - Custom row number text
- `validate` (Function) - Row-level validation

### Localization Props

- `localized-label` (Object) - Custom labels object
- `remember` (Boolean) - Save settings to localStorage

### Panel Props

- `disable-panel-setting` (Boolean) - Hide settings panel
- `disable-panel-filter` (Boolean) - Hide filter panel

---

## 🔔 Events Reference (VueExcelEditor)

### Data Events

- `@update(updateItemArray)` - Cell updated
  - `updateItemArray`: Array of update objects
- `@delete(deleteItemArray)` - Records deleted
  - `deleteItemArray`: Array of deleted records

### Selection Events

- `@select(selectIdArray, direction)` - Selection changed
  - `selectIdArray`: Array of selected row positions
  - `direction`: true=selected, false=unselected

### Cell Events

- `@cell-click(rowPos, colPos)` - Cell clicked (before focus)
- `@cell-focus({rowPos, colPos, cell, rec})` - Cell focused
- `@cell-blur({rowPos, colPos, cell, rec})` - Cell blurred

### Navigation Events

- `@page-changed(pageTopPos, pageBottomPos)` - Page changed

### Configuration Events

- `@setting(setting)` - Settings changed (column width, visibility)
  - `setting`: { colHash, fields: [{name, invisible, width, label}] }
- `@validate-error(error, row, field)` - Validation error

---

## 🛠️ Public Methods Reference (VueExcelEditor)

### Navigation

```javascript
firstPage(); // Go to first page
lastPage(); // Go to last page
prevPage(); // Go to previous page
nextPage(); // Go to next page
moveNorth(); // Move cursor up
moveSouth(); // Move cursor down
moveWest(); // Move cursor left
moveEast(); // Move cursor right
moveTo(row, col); // Move cursor to position
moveToNorthWest(); // Move to first row, first column
moveToNorthEast(); // Move to first row, last column
moveToSouthWest(); // Move to last row, first column
moveToSouthEast(); // Move to last row, last column
```

### CRUD

```javascript
newRecord(rec); // Add new record
deleteRecord(rowpos); // Delete record at position
deleteSelectedRecords(); // Delete all selected
selectRecord(row); // Select row
unSelectRecord(row); // Deselect row
clearAllSelected(); // Deselect all
getSelectedRecords(); // Get selected row objects
```

### Search & Sort

```javascript
doFind(text); // Search and locate text
doFindNext(); // Continue last search
sort(direction, pos); // Sort column (direction: 1=asc, -1=desc)
```

### Filter

```javascript
setFilter(name, text); // Set column filter
clearFilter(name); // Clear column filter (name optional)
columnSuppress(); // Hide empty columns
```

### Import/Export

```javascript
exportTable(format, selectedOnly, filename); // Export (format: 'xlsx'|'csv')
importTable(callback, errorCallback); // Import
```

### Validation

```javascript
validateAll(); // Validate all cells
setFieldError(err, row, field); // Set field error
setRowError(err, row); // Set row error
```

### Utility

```javascript
getFieldByName(name); // Get field definition by name
getFieldByLabel(label); // Get field definition by label
undoTransaction(); // Undo last update
calSummary(); // Recalculate summaries
```

---

## 💡 Internal Variables (Read-Only)

```javascript
processing; // Boolean - Component busy state
pageTop; // Number - Current page start
pageSize; // Number - Rows per page
fields; // Array - Column definitions
filterColumn; // Object - Current filters
table; // Array - Filtered/sorted records
selected; // Object - Selected rows {rowPos: $id}
selectedCount; // Number - Selected count (v-model supported)
errmsg; // Object - Validation errors {cellId: error}
redo; // Array - Undo buffer
```

---

## 🎯 Column Types Reference

### Text Types

- `string` - Text input, left-aligned
- `password` - Masked input, customizable masking character

### Numeric Types

- `number` - Numeric input, right-aligned, allows: 0-9, ., -

### Boolean Types

- `check10` - Checkbox, values: 0 or 1
- `checkYN` - Checkbox, values: Y or N
- `checkTF` - Checkbox, values: T or F

### Date/Time Types

- `date` - YYYY-MM-DD format, date picker
- `datetime` - YYYY-MM-DD HH:mm format, date picker
- `datetimesec` - YYYY-MM-DD HH:mm:ss format, date picker
- `datetick` - Unix timestamp, displays as date
- `datetimetick` - Unix timestamp, displays as datetime
- `datetimesectick` - Unix timestamp, displays as datetimesec

### Selection Types

- `select` - Dropdown from array options
- `map` - Dropdown from object hash (display:text, value:key)

### Special Types

- `action` - Action buttons from options array, triggers change callback
- `badge` - Badge-styled display with bgcolor

---

## 🔐 Validation System

### Column Validation

```javascript
validate: function(content, oldContent, record, field) {
  if (content === '') return 'Mandatory field'
  if (!/pattern/.test(content)) return 'Invalid format'
  return ''  // No error
}
```

### Row Validation

```javascript
validate: function(content, oldContent, record, field) {
  if (record.field1 !== record.field2) return 'Fields must match'
  return ''
}
```

### Error Display

- Cell errors: Red background icon (top-right corner), tooltip on hover
- Row errors: Displayed in number column

---

## 📊 Summary System

### Summary Types

- `sum` - Sum of numeric values
- `avg` - Average of numeric values
- `max` - Maximum value
- `min` - Minimum value
- `count` - Count of non-empty/true/future values

### Calculation Timing

- Triggered on: New record, delete, filter change
- NOT triggered on: Cell edit (manual calSummary() needed)
- Calculated for: Visible/filtered rows only

---

## 🌐 Localization

### Required Labels

```javascript
{
  footerLeft: (top, bottom, total) => string,
  first, previous, next, last,
  footerRight: { selected, filtered, loaded },
  processing,
  tableSetting, exportExcel, importExcel,
  back, reset,
  sortingAndFiltering,
  sortAscending, sortDescending,
  near, exactMatch, notMatch,
  greaterThan, greaterThanOrEqualTo,
  lessThan, lessThanOrEqualTo,
  regularExpression,
  customFilter,
  listFirstNValuesOnly: n => string,
  apply,
  noRecordIndicator,
  noRecordIsRead,
  readonlyColumnDetected,
  columnHasValidationError: (name, err) => string,
  rowHasValidationError: (row, name, err) => string,
  noMatchedColumnName,
  invalidInputValue,
  missingKeyColumn
}
```

---

## 🎨 Custom Styling Hooks

### Row Styling

```javascript
rowStyle: function(record) {
  return {
    backgroundColor: record.active ? 'lightgreen' : 'white',
    fontWeight: record.important ? 'bold' : 'normal'
  }
}
```

### Cell Styling

```javascript
cellStyle: function(record, field) {
  return {
    color: field.name === 'amount' && record.amount < 0 ? 'red' : 'black',
    fontStyle: record[field.name] ? 'normal' : 'italic'
  }
}
```

### Column Initial Style

```javascript
initStyle: {
  textAlign: 'center',
  backgroundColor: '#f0f0f0'
}
// or function
initStyle: function(record, field) {
  return { color: record[field.name] > 100 ? 'red' : 'black' }
}
```

---

## 🔗 Link System

### Definition

```javascript
link: function(content, record, rowPos, colPos, field, editor) {
  router.push(`/detail/${record.id}`)
}
```

### Visibility Check

```javascript
isLink: function(record) {
  return record.type === 'detail'  // Only show link for certain rows
}
```

### Usage

- Hold Alt key to see clickable links
- Click to trigger link function
- Links are automatically read-only

---

## 📥 Import System

### Supported Formats

- `.xlsx` - Excel 2007+
- `.xls` - Excel 97-2003
- `.xlsm` - Macro-enabled Excel
- `.csv` - Comma-separated values

### Import Process

1. User clicks Import or calls `importTable()`
2. File picker opens
3. File read as binary string
4. xlsx library parses workbook
5. First sheet converted to row objects
6. Key fields matched
7. Validation applied
8. Existing records updated or new records added
9. `@update` and `@delete` events emitted
10. Callback with stats: `{inserted, updated, recordAffected}`

### Error Handling

- `noRecordIsRead` - Empty file
- `missingKeyColumn` - Key field not found
- `readonlyColumnDetected` - Trying to update readonly column
- `columnHasValidationError` - Validation failed
- `rowHasValidationError` - Row validation failed

---

## 📤 Export System

### Supported Formats

- `xlsx` - Excel with compression (recommended)
- `xls` - Excel without compression
- `csv` - Comma-separated values

### Export Process

1. `exportTable(format, selectedOnly, filename)` called
2. Processing overlay shown
3. Data prepared (filtered/selected only)
4. Column labels extracted
5. Workbook created with SheetJS
6. File downloaded with specified filename
7. Processing overlay hidden

### Column Widths

- Exported widths match visible column widths
- Calculated from rendered width / 6.5

---

## 🎯 Performance Optimizations

### Pagination

- Only render visible page rows
- Auto-calculate optimal page size
- Virtual scrolling-like behavior

### Debouncing

- `lazy()` function for delayed execution
- Autocomplete: 700ms delay
- Window resize: 500ms delay
- Filter application: 0ms (async)

### Selective Updates

- Only refresh changed cells
- Vue's reactivity for efficient DOM updates
- Lazy buffer for batch operations

### Memory

- redo buffer cleared on data change
- Filtered results recalculated on demand
- Autocomplete limited to autocompleteCount

---

## 🐛 Common Patterns

### Parent Component Access

```javascript
this.$parent.registerColumn(field);
this.$parent.calTable();
this.$parent.refresh();
```

### Event Emission

```javascript
this.$emit('update:modelValue', value);
this.$emit('select', rows, direction);
this.$emit('update', transaction);
```

### DOM References

```javascript
this.$refs.editor; // Main container
this.$refs.tableContent; // Scrollable area
this.$refs.systable; // Table element
this.$refs.inputBox; // Textarea editor
```

### Force Update

```javascript
const instance = getCurrentInstance();
instance?.proxy?.$forceUpdate();
```

---

## 📝 Usage Patterns

### Basic Usage

```html
<vue-excel-editor v-model="data">
  <vue-excel-column
    field="name"
    label="Name"
    type="string"
  />
  <vue-excel-column
    field="age"
    label="Age"
    type="number"
  />
</vue-excel-editor>
```

### With Features

```html
<vue-excel-editor
  v-model="data"
  filter-row
  autocomplete
>
  <vue-excel-column
    field="name"
    label="Name"
    type="string"
    width="150px"
  />
  <vue-excel-column
    field="email"
    label="Email"
    type="string"
    :validate="validateEmail"
  />
</vue-excel-editor>
```

### Method Access

```html
<vue-excel-editor
  ref="editor"
  v-model="data"
>
</vue-excel-editor>

<script>
  this.$refs.editor.newRecord({ name: 'New' });
  this.$refs.editor.exportTable('xlsx', false, 'data');
</script>
```

---

## 🎓 Key Concepts for AI Understanding

### Unique IDs

- Every record gets a `$id` property (timestamp-index format)
- Used for tracking, selection, undo/redo
- Temp keys start with `§` prefix

### Column Registration

- `VueExcelColumn` components don't render
- They call `registerColumn()` on parent during `created()`
- Parent builds `fields[]` array
- Used for rendering, filtering, sorting, validation

### Two-Way Binding

- `v-model` binds to modelValue prop
- Changes emitted via `@update` and `@delete` events
- Parent should handle these for persistence

### Filter Logic

- Filters stored as hash: `{colPos: filterString}`
- Watched via `columnFilterString` (JSON)
- Applied in `calTable()` to filter modelValue
- Results in `filteredValue` then `table`

### Paging Logic

- `table` contains all filtered/sorted records
- `pagingTable` = `table.slice(pageTop, pageTop + pageSize)`
- Only `pagingTable` is rendered in DOM
- Virtual scrolling effect with manual scrollbar

### Undo System

- `redo[] is a stack of transaction arrays
- Each transaction contains change details
- `undoTransaction()` reverses last transaction
- Cleared on data changes to prevent inconsistencies

### Validation Flow

- Column-level: `field.validate()`
- Row-level: `editor.validate()` prop
- Called on every cell update
- Errors stored in `errmsg` and `rowerr`
- Displayed via CSS classes and tooltips

### Sticky Columns

- `position: sticky` CSS property
- `left` position calculated by `calStickyLeft()`
- Higher z-index for proper layering
- Independent of horizontal scroll

### Mass Update

- When editing cell with multiple rows selected
- All selected rows' same column updated
- Disabled by `no-mass-update` prop
- Emits multiple `@update` events

---

## 🚀 Extension Points

### Custom Column Types

- Add type handling in `VueExcelColumn.init()`
- Define `toText()` and `toValue()` converters
- Add input restrictions via `allowKeys`

### Custom Validators

- Return error message string, empty for valid
- Access to: value, oldValue, record, field
- Can be async (return Promise)

### Custom Formatters

- `toText(val, record, field, pos)` - Value to display
- `toValue(text, record, field)` - Display to value
- Used for all conversions

### Custom Actions

- `action` type with `options` array
- `change` callback triggered on selection
- Good for Edit/Delete actions

### Custom Links

- Define `link` function
- Optionally define `isLink` function
- Alt+click to trigger
- Automatic readonly

---

## ⚡ Performance Considerations

### Large Datasets

- Use pagination (avoid `no-paging`)
- Limit filter results with `n-filter-count`
- Disable autocomplete if not needed
- Use virtual scrolling concepts

### Complex Validation

- Debounce validation in change handlers
- Use Promise-based async validation
- Show processing indicator

### Many Columns

- Use `invisible` for rarely used columns
- Use `columnSuppress()` to hide empty columns
- Limit sticky columns

### Frequent Updates

- Batch updates where possible
- Use `processing` prop to show indicator
- Consider debouncing external changes

---

## 📚 Resources

### Dependencies

- Vue 3: https://vuejs.org/
- @vuepic/vue-datepicker: https://vue3datepicker.com/
- vuedraggable: https://github.com/SortableJS/vue.draggable.next
- SheetJS (xlsx): https://sheetjs.com/

### Repository

- GitHub: https://github.com/cscan/vue3-excel-editor
- Issues: https://github.com/cscan/vue3-excel-editor/issues

### Documentation

- Full README included in project root
- API documentation in README
- Example usage in README

---

## 🎯 Quick Start for AI Development

When working with this codebase:

1. **Understand the data flow**: modelValue → calTable() → table → pagingTable → DOM
2. **Key component is VueExcelEditor.vue** - All core logic lives here
3. **Columns are registered, not rendered** - VueExcelColumn just calls registerColumn()
4. **Filters are reactive** - columnFilterString watcher triggers recalculations
5. **Selection is hash-based** - selected[rowPos] = $id
6. **All mutations go through updateCell()** - Handles validation, events, undo
7. **Use lazy() for debouncing** - Prevents excessive recalculations
8. **References are critical** - $refs for DOM, getCurrentInstance() for Vue instance
9. **Two-way binding via events** - @update and @delete, not automatic v-model
10. **Styling is scoped** - CSS classes control most visual aspects

---

## 🔍 Code Navigation Tips

### Finding Feature Implementation

- Search for method name in VueExcelEditor.vue
- Check data() for related properties
- Look for computed properties for derived values
- Check event handlers for user interactions

### Adding New Column Type

- Edit VueExcelColumn.vue init() method
- Add type case with appropriate:
  - Input restrictions (allowKeys)
  - Default styles (textAlign, etc.)
  - Validators if needed
  - Converters if needed

### Modifying Validation

- updateCell() in VueExcelEditor.vue
- setFieldError() and setRowError() methods
- Error display in CSS classes

### Changing Data Flow

- calTable() - filtering/sorting logic
- refresh() - recalculates table from modelValue
- updateCell() - handles all cell updates

---

## 📊 Summary

This is a **mature, feature-rich Excel-like editor** for Vue 3 with:

- **3000+ line main component** handling all spreadsheet logic
- **6 supporting components** for specialized UI elements
- **Excel-level features**: filtering, sorting, paging, validation, import/export
- **Clean separation**: Column definitions separate from editor logic
- **Event-driven**: Updates communicated via @update and @delete events
- **Performance optimized**: Pagination, debouncing, selective rendering
- **Highly customizable**: Props, slots, callbacks, styling hooks

The codebase follows Vue 3 Composition API patterns (defineComponent), uses vanilla JavaScript for most logic, and integrates well with external libraries for date picking, drag-and-drop, and Excel I/O.

---

_Generated for AI code understanding and quick reference_
