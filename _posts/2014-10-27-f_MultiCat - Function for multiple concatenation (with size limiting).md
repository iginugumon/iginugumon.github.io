---
tags:
  - "#Article"
  - "#Powerbuilder"
  - "#Programming"
  - "#SQL"
  - Legacy-Coder
  - Blog
Date Created: 2014-10-27
---
# `f_MultiCat` - Function for multiple concatenation (with size limiting)

One of the features of our application involves generation of a text file with format specified by the entity that our client is supposed to send it to. The values on the files generated are not character delimited. There is a fixed length of characters in a row. And the length of columns are properly designated.

In example, let us assume that it only contain 3 columns:

```
column1 - 10 characters - 1288 MARIGOLD STREET, MANHATTAN, NEW YORK
column2 - 5 characters - 24
column3 - 5 characters - A-246
```

So, if we are to produce a text in the specified format, we should be getting this:

```
1288 MARIG 24A-246
```

The current implementation is, each value is retrieved and then appended in the text:

```
ls_column1 = ....

ls_text += ls_column1

ls_column2 = ....

ls_text += ls_column2

ls_column3 = ....

ls_text += ls_column3
```


The problem with the current design is it is harder for the programmer to understand the code. And when you revise parts of the code and then got an error, it will be frustrating to find which section caused it because the whole output is affected. So the OC me tried a more organized approach. I have separated the logic of the concatenation of the columns from the retrieval of values. First, I'll be retrieving all the values of each column. Then, at the end, I'll be appending their formatted strings.

But I needed to create a new function to do that: `f_MultiCat()`.

The function takes 2 parameters: an array of strings, and an array of lengths. The function will then append all the strings in the array with sequence according to their element number. At the same time, the strings to be appended will be limited by their specified lengths.

First, I needed to determine the number of elements of both parameters:

```
ll_NoOfTexts = UpperBound(as_Text)
ll_NoOfLengths = UpperBound(ai_Length)
```


Then, we need to access each text element for processing:

```
FOR ll_Index = 1 TO ll_NoOfTexts
....
NEXT
```


If no length is available for the text use its actual length, otherwise, use the specified length:

```
ll_ActualLength = Len(as_Text[ll_Index])

IF ll_Index > ll_NoOfLengths THEN
ll_TextLength = ll_ActualLength
ELSE
ll_TextLength = ai_Length[ll_Index]
END IF
```


If the length to be used is less than or equal to the string's actual length, drop the characters after the limit. Else, fill the remaining characters with SPACE.

```
IF ll_ActualLength >= ll_TextLength THEN
ls_ResultText = f_NVL(ls_ResultText,'') + Left(as_Text[ll_Index], ll_TextLength)
ELSE
ls_ResultText = f_NVL(ls_ResultText,'') + as_Text[ll_Index] + Space(ll_TextLength - ll_ActualLength)
END IF
```

