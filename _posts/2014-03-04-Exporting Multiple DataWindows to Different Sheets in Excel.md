---
tags:
  - "#Article"
  - "#Powerbuilder"
  - "#Programming"
  - "#Datawindow"
  - "#Excel"
  - Blog
---
# Exporting Multiple DataWindows to Different Sheets in Excel

I was tasked to create a new version of an existing report. On top of the new report, I have to include new templates of a review module.

Before proceeding with the development, I checked the original report's design so I can come up with a design that will not be in conflict with the existing one. Upon checking, I have found out that the report is a composite DataWindow with nested reports. And one of the problems I have encountered during development is the exporting of data.

The report allows exporting to 2 formats: PDF and Excel. I have just reused the codes in PDF exporting as it only creates a PDF file of the printout.

But I found out that the Excel format merely joins the data of all the nested reports and used the SaveAs() facility of the DataWindow. The result is a repeating value of every header column for every row in the detail table. That is why I decided to create another exporting facility which has a separation of each nested report in sheets: `f_exportToExcel()`. The function takes in an array of DataWindows and a string for the filename as parameters.

I have created four OLE objects for the Excel application, the main workbook, the temporary workbook, and the current sheet:


```
OLEOBJECT lole_Excel
OLEOBJECT lole_MainWorkBook, lole_TempWorkBook
OLEOBJECT lole_sheet

lole_Excel = CREATE OLEOBJECT
lole_MainWorkBook = CREATE OLEOBJECT
lole_TempWorkBook = CREATE OLEOBJECT
lole_sheet = CREATE OLEOBJECT
```


Then I connected to the application and added a new workbook or created a new file:


```
ll_OLEReturn = lole_Excel.ConnectToNewObject("excel.application")
IF ll_OLEReturn < 0 THEN
... // Insert error here
RETURN -1
END IF

lole_MainWorkBook = lole_excel.WorkBooks.Add()
```


Then I synchronised the number of DataWindows passed to the number of sheets on the main workbook:

```
ll_last = lole_MainWorkBook.Worksheets.count

DO WHILE ll_last < ll_UpperBound
lole_MainWorkBook.Worksheets.Add
ll_last = lole_MainWorkBook.Worksheets.count
LOOP
```

And then for every DataWindow, I have exported the content using SaveAsFormattedText() function to retain its presentation. Then I copied it to a sheet on the Main Workbook. I have also added few formatting.

```
FOR ll_ctr = 1 TO ll_UpperBound
// Save As
ls_sheetName = f_If(adw\[ll_ctr\].Title = "", adw\[ll_ctr\].DataObject, adw\[ll_ctr\].Title)
ls_sheetName = f_global_replace(ls_sheetName, ' ', '_')
ls_fileName = f_getostempdir() + ls_sheetName + ".txt"
adw\[ll_ctr\].SaveAsFormattedText(ls_fileName, EncodingANSI!, "~t", "", "~r~n", TRUE)
// adw\[ll_ctr\].SaveAs(as_filename, EXCEL8!, TRUE)
// Open in Excel
lole_BufferWorkBook = lole_excel.WorkBooks.Open(ls_fileName)
lole_BufferWorkBook.Worksheets(1).Cells.Select
lole_Excel.CutCopyMode = False
lole_BufferWorkBook.Worksheets(1).Cells.Copy
// Paste to sheet
lole_MainWorkBook.Activate
lole_sheet = lole_MainWorkBook.WorkSheets(ll_ctr)
lole_sheet.Name = ls_sheetName
lole_sheet.Paste
lole_sheet.Cells.EntireColumn.AutoFit
// clear from clipboard
lole_Excel.CutCopyMode = False
// Close
lole_BufferWorkBook.CLOSE(FALSE)
// Delete temporary file
// FileDelete ( ls_fileName )
NEXT
```

I then showed the application to the user and used the `SaveAs()` function for the file name parameter.

```
lole_Excel.Visible = TRUE
lole_MainWorkBook.saveAs(as_filename)
```

Finally, clean up!

```
lole_Excel.DisconnectObject()
lole_MainWorkBook.DisconnectObject()
lole_BufferWorkBook.DisconnectObject()
lole_sheet.DisconnectObject()

DESTROY lole_Excel
DESTROY lole_MainWorkBook
DESTROY lole_BufferWorkBook
DESTROY lole_sheet
```

\*Updated code:
