---
tags:
  - "#Article"
  - "#Powerbuilder"
  - "#Programming"
  - "#Datawindow"
  - Blog
---
# `f_copyColumns` - Copying data values from one datawindow to another

So let's say I have 2 datawindows (`dw_source` and `dw_destination`). If I want their columns to have the same values, I just use `RowsCopy()` or `ShareData()`. But both those functions will not work if the number of columns of the datawindows are not equal.

So if dw_source has these:


```
column1
column2
column3
```

And `dw_destination` has these:

```
column2
column3
column4
```
If I want to copy the values of column2 from `dw_source` to `dw_destination`, I need to use the native `GetItem<type>()` and `SetItem()` functions:

`dw_destination.SetItem(row, "column1", dw_source.GetItem<type>(row, "column1"))`

Or I can create and use a function that copies matching (and compatible) columns, `f_copyColumn()`:

`f_CopyColumn(dw_source, dw_destination, row, "column1")`

But I will have to do that for every column. And for the task at hand, I'm dealing with lots of columns and I'm lazy to code that much.

So I will need to create a function for copying matching columns.

First, I have to check if the rows are match:

```
ll_sourceRowCount = adw_source.Dynamic RowCount()
ll_destinationRowCount = adw_destination.Dynamic RowCount()
IF ll_sourceRowCount <> ll_destinationRowCount THEN RETURN -1
IF (ll_sourceRowCount = 0) OR (ll_destinationRowCount = 0) THEN RETURN 100
```

Then I could loop through all rows and then have an inner loop for checking if the columns match. But for optimization's sake, I've switched the loops:

```
// Loop through all columns in source
li_columnCount = Integer(adw_source.Describe('DataWindow.Column.Count'))
FOR li_colCtr = 1 TO li_columnCount

// Loop through all rows
FOR ll_row = 1 TO ll_sourceRowCount
NEXT //ll_row = 1 TO ll_sourceRowCount
NEXT //li_colCtr = 1 TO li_columnCount
```

I can then move on to the next column if mismatch in name or in data type:


```
// Loop through all columns in source
li_columnCount = Integer(adw_source.Describe('DataWindow.Column.Count'))
FOR li_colCtr = 1 TO li_columnCount
ls_columnVariable = '#'+String(li_colCtr)+'.name'
ls_columnName = Lower(adw_source.Describe(ls_columnVariable))
ls_colType = adw_source.Describe('#' + STRING(li_colCtr) + '.ColType')
ls_destColType = adw_destination.Describe(ls_columnName + '.ColType')

// Skip copying if column does not exists or with different data type in the destination
IF NOT (Pos(ls_ColType, "char") > 0 AND Pos(ls_destColType, "char") > 0) AND &
NOT ( (f_In(ls_colType, {"double", "integer", "long", "real", "number"}) OR Pos(ls_ColType, "decimal") > 0) AND (f_In(ls_destColType, {"double", "integer", "long", "real", "number"}) OR Pos(ls_destColType, "decimal") > 0)) AND &
NOT (ls_colType = "datetime" AND f_In(ls_destColType, {"datetime", "date", "time"}) ) &
THEN CONTINUE
...
...
NEXT //li_colCtr = 1 TO li_columnCount
```
And for the inner loop for the number of rows, I just need the statement that copies the values of the columns:

```
// Loop through all rows
FOR ll_row = 1 TO ll_sourceRowCount
// Copy column value
lb_copied = (0 = f_CopyColumn(adw_source, adw_destination, ll_row, ls_columnName))
NEXT //ll_row = 1 TO ll_sourceRowCount
```

This function can now be used if you want to copy column2 and column3 of the above example. The rest of the columns will just be ignored:

```
f_CopyColumn(dw_source, dw_destination)
```
