---
tags:
  - "#Article"
  - "#Powerbuilder"
  - "#Programming"
  - Legacy-Coder
  - Blog
Date Created: 2014-10-27
---
# `f_timeUnitConvert` - Unified conversion function for Day, Hour, Minute, Second

I was developing a module wherein I need to compare the time imported from a file to the one in the database. Both the file and database has 2 columns: hours and minutes. I thought it would be as easy as checking:

```Powerbuilder
IF database.Hours = file.Hours...
IF database.Mins = file.Mins...
```

But it is stated in the requirements that the comparison should be with the summation of hours and minutes. Which is why I needed a conversion function.

Powerbuilder has its date and time functions available. But I need to convert my data to `DateTime` datatype before processing. Only then that I can use those built-in functions -- which the coding can still be much more complex than creating my own function.

In designing the function, I decided to use a map. It is similar to a multiplication table:

```
SECOND MINUTE HOUR DAY
SECOND 1 1/60 1/3600 1/86400
MINUTE 60 1 1/60 1/1440
HOUR 3600 60 1 1/24
DAY 86400 1440 24 1
```

The values in the map are the factors to be multiplied to the given value to convert to the desired unit. In example, if we want to convert X SECONDS to the unit DAY, we need to use the item on the row of SECOND under the DAY column, 1/86400:
`# of DAYS = X SECONDS * 1/86400`

Let us proceed with the coding.
First, I created constants to designate which element number are for each of the units. Then I initialized the factor map using 2 dimensional array:

```
// Declare constants
CONSTANT INT lci_SECOND = 1
CONSTANT INT lci_MINUTE = 2
CONSTANT INT lci_HOUR = 3
CONSTANT INT lci_DAY = 4

// Initialize map
DOUBLE ldbl_factor[4,4] = {1, 60, 3600, 86400, 1/60, 1, 60, 1440, 1/3600, 1/60, 1, 24, 1/86400, 1/1440, 1/24, 1}
```

I determined which unit the input is and to which unit it should be converted and use the respective unit's constant:

```
// Get units' factor map indices
INT li_current, li_converted
CHOOSE CASE Lower(ac_currentUnit)
CASE 's'
li_current = lci_SECOND
CASE 'm'
li_current = lci_MINUTE
CASE 'h'
li_current = lci_HOUR
CASE 'd'
li_current = lci_DAY
CASE ELSE
RETURN -1
END CHOOSE
CHOOSE CASE Lower(ac_convertedUnit)
CASE 's'
li_converted = lci_SECOND
CASE 'm'
li_converted = lci_MINUTE
CASE 'h'
li_converted = lci_HOUR
CASE 'd'
li_converted = lci_DAY
CASE ELSE
RETURN -1
END CHOOSE
```

Conversion to the desired unit is basically multiplying the input value to the factor on the map:

```
// convert
RETURN (adbl_value * ldbl_factor[li_current, li_converted])
```
