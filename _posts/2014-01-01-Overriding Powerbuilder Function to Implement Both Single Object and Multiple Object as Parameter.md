---
tags:
  - "#Article"
  - "#Powerbuilder"
  - "#Programming"
  - "#Function"
  - Blog
URL: https://wordpress.com/post/thelegacycoder.wordpress.com/25
---
# Overriding Powerbuilder Function to Implement Both Single Object and Multiple Object as Parameter

One of my project involves exporting data from a datawindow to file. Powerbuilder has a lot of facilities for that:

```
SaveAs()
SaveAsAscii()
SaveAsFormattedText()
Export()
```


But in my experience as a programmer, we always tend to not only use the native functions because the users request "nice-to-haves" like exporting to Excel with nice formatting. We often use OLE to interface with the needed application.

One of the things that I did when exporting to Excel is creating a single workbook with data from different datawindows on different sheets. So I came up with a basic function call similar to this:

```
f_exportToExcel({dw1, dw2})
```


And then by looping through all the elements, I apply the processing:

```
ll_Max = UpperBound(dw)

IF ll_Max <= 0 THEN RETURN

FOR ll = 1 TO ll_Max
// export
dw[ll].SaveAs(...)
... // other processing like inserting to the main workbook
NEXT
```


What about single datawindow parameter? I can always use this:

```
f_exportToExcel({dw})
```

But since some programmers who doesn't know the implementation might call by not using an array, I've decided to override the function for the single object implementation containing the same code as above and passing the object as single-element array:

```
f_exportToExcel({adw})
```


Now I can don't have to worry about forgetting the curly braces because I can now use this:

```
f_exportToExcel(dw)
```

