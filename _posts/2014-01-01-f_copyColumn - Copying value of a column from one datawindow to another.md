---
tags:
  - "#Article"
  - "#Powerbuilder"
  - "#Programming"
  - "#Datawindow"
  - Blog
---
# `f_copyColumn` - Copying value of a column from one datawindow to another

I have discussed about copying the values of all matching columns from one datawindow to another on my previous post. The function `f_copyColumns()` calls another function, `f_copyColumn()`, which copies a specific column value from the source datawindow (`dw_source`) to the destination datawindow (`dw_destination`).

The implementation of the function simply uses the native `GetItem<type>()` and `SetItem()` functions:

`dw_destination.SetItem(row, columnName, dw_source.GetItem<Type>(row, columnName)`

The first part is just checking the data types and if column exists:

```
// Check datatypes
ls_sourceColType = apo_source.Dynamic Describe(as_columnName + '.ColType')
ls_destColType = apo_destination.Dynamic Describe(as_columnName + '.ColType')

// Exit function if not matching or if column does not exists on datawindow
IF NOT (Pos(ls_sourceColType, "char") > 0 AND Pos(ls_sourceColType, "char") > 0) AND &
NOT ( (f_In(ls_sourceColType, {"double", "integer", "long", "real", "number"}) OR Pos(ls_sourceColType, "decimal") > 0) AND (f_In(ls_destColType, {"double", "integer", "long", "real", "number"}) OR Pos(ls_destColType, "decimal") > 0)) AND &
NOT (ls_sourceColType = "datetime" AND f_In(ls_destColType, {"datetime", "date", "time"}) ) &
THEN RETURN 100
```

The main part uses conditions to choose which function to use, depending on the column data type:

```
// Copy
IF Pos(ls_destColType, "char") > 0 THEN
apo_destination.Dynamic SetItem(al_row, as_columnName, apo_source.Dynamic GetItemString(al_row, as_columnName))
ELSEIF Pos(ls_destColType, "decimal") > 0 THEN
apo_destination.Dynamic SetItem(al_row, as_columnName, DEC(apo_source.Dynamic GetItemNumber(al_row, as_columnName)))
ELSEIF ls_destColType = "double" THEN
apo_destination.Dynamic SetItem(al_row, as_columnName, DOUBLE(apo_source.Dynamic GetItemNumber(al_row, as_columnName)))
ELSEIF ls_destColType = "integer" THEN
apo_destination.Dynamic SetItem(al_row, as_columnName, INTEGER(apo_source.Dynamic GetItemNumber(al_row, as_columnName)))
ELSEIF ls_destColType = "long" THEN
apo_destination.Dynamic SetItem(al_row, as_columnName, LONG(apo_source.Dynamic GetItemNumber(al_row, as_columnName)))
ELSEIF ls_destColType = "real" THEN
apo_destination.Dynamic SetItem(al_row, as_columnName, REAL(apo_source.Dynamic GetItemNumber(al_row, as_columnName)))
ELSEIF ls_destColType = "number" THEN
apo_destination.Dynamic SetItem(al_row, as_columnName, DEC(apo_source.Dynamic GetItemNumber(al_row, as_columnName)))
ELSEIF ls_destColType = "datetime" THEN
apo_destination.Dynamic SetItem(al_row, as_columnName, apo_source.Dynamic GetItemDatetime(al_row, as_columnName))
ELSEIF ls_destColType = "date" THEN
apo_destination.Dynamic SetItem(al_row, as_columnName, DATE(apo_source.Dynamic GetItemDatetime(al_row, as_columnName)))
ELSEIF ls_destColType = "time" THEN
apo_destination.Dynamic SetItem(al_row, as_columnName, TIME(apo_source.Dynamic GetItemDatetime(al_row, as_columnName)))
END IF
```

So if I want to copy the values of the column "remarks" from `dw_entry` to `dw_report`, I will just have to use this code without worrying which `GetItem<Type>` function to use:

`f_copyColumn(dw_entry, dw_report, 1, "remarks")`

\*Updated source codes:
