---
tags:
  - "#Article"
  - "#Powerbuilder"
  - "#Programming"
  - "#Datawindow"
  - "#DataStore"
  - "#DataWindowChild"
  - Blog
---
# Passing DataWindow, DataStore or DataWindowChild as parameter on a single implementation of function

I was done developing these functions for copying matching columns from one `datawindow` to another: `f_copyColumns()`, `f_copyColumn()`. But when I finally went back to the main task, I realized that I will actually be dealing with `datawindowchilds` instead of `datawindows`. So here are my options:

Overloading
I will have to overload the function for every combination of `DataWindow`, `DataStore`, and `DataWindowChild`. Not a very good option, even when dealing with only 2 parameters because I will have to copy, paste, and then replace the source code for every combination:

```
DataWindow, DataWindow
DataWindow, DataStore
DataStore, DataWindow
DataStore, DataStore
DataStore, DataWindowChild
DataWindowChild, DataStore
DataWindowChild, DataWindowChild
DataWindowChild, DataWindow
DataWindow, DataWindowChild
```


Converting `Datawindow` <-> `DataStore` <-> `DataWindowChild`:

This one is probably the most difficult it might also the worst solution. Not to mention the performance issues.

Interfacing:

This one is actually the most appealing to me. If only I can create an interface of the function, or even a template for that matter, then maybe I could create just one set of codes and I'd just have to determine which datatype/object to use. But the article <http://rolandmuhlberger.ulitzer.com/node/42508/mobile> seems to complex to implement. Although I might dabble on it later when needed. But it also gave me an idea on what to do instead.

Use the ancestor object as parameters and then simply determine the object type passed:
<http://codeverge.com/sybase.powerbuilder.general/passing-datawindow-and-datastore/848878>

```
CHOOSE CASE apo.TypeOf()
CASE DataWindow!, DataStore!, DataWindowChild!
// insert process here
CASE ELSE
RETURN -1
END CHOOSE // apo.TypeOf()
```


And during processing, I used the dynamic calling of function:

```
ll_RowCount = apo.Dynamic RowCount()
```


I can now call my functions regardless of which object I've used:"

```
f_copyColumns(dw_source, dw_destination)
f_copyColumns(dw_source, ds_destination)
f_copyColumns(dwc_source, ds_destination)
...
```


\*Updated source codes:
