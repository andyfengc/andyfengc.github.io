---
layout: post
title: Excel functions - vlookup
author: Andy Feng
---

# if()
IF() function runs a logical test and returns one value for a TRUE result, and another for a FALSE result.

e.g. 
For example, =IF(A1>70,"Pass","Fail"). 如果分数超过70返回 "pass" 

# vlookup()
VLOOKUP(lookup_value, table_array, col_index_num, [range_lookup])

即*VLOOKUP(查找值，查找范围，往后查找列数(含第1列)，精确匹配或者近似匹配）The VLOOKUP function always looks up a value in the leftmost column of a table and returns the corresponding value from a column to the right in the same row. 

vlookup就是竖直查找，即列查找。通俗的讲，根据查找值参数，在查找范围的第一列搜索查找值，如果第一列中找到该值后，则返回值为：以第一列为准，当前行中，往后推列数查找对应的值

![](/images/posts/20210520-excel-2.jpg)

## demo
例如，基于一个复杂的产品数据表，使用vlookup()根据产品名查找总价

![](/images/posts/20210520-excel-1.png)

Steps:

1. 查找值 lookup_value

	![](/images/posts/20210520-excel-3.jpg)

2. 查找范围 table_array, 选取需要取值的表格范围

	![](/images/posts/20210520-excel-4.jpg)

3. 查找列数 col_index_num

	![](/images/posts/20210520-excel-5.jpg)

	通过我们所要查找的数值和条件数列的距离列数，确定数字。这里有点要注意的，要把我们查询的条件数列在查找范围中放在第一列，这样可以更好地统计后面的数值。

4. 匹配条件 range_lookup 为一逻辑值，指明函数 VLOOKUP 查找时是精确匹配，还是近似匹配。如果为false或0 ，则返回精确匹配，如果找不到，则返回错误值 #N/A。如果 range_lookup 为TRUE或1，函数 VLOOKUP 将查找近似匹配值，也就是说，如果找不到精确匹配值，则返回小于 lookup_value 的最大数值。default is false即精确匹配

	![](/images/posts/20210520-excel-6.jpg)

# hlookup()
HLOOKUP in Excel stands for 'Horizontal Lookup'. It is a function that makes Excel search for a certain value in a row (the so called 'table array'), in order to return a value from a different row in the same column.

# CONCATENATE(), CONCAT()
join two or more text strings into one string

`CONCATENATE(text1, [text2], ...)`

e.g.

=CONCATENATE("Stream population for ", A2, " ", A3, " is ", A4, "/mile.")

=CONCATENATE(B2, " ",C2)

# COUNTBLANK()
count the number of empty cells in a range of cells. Cells that contain text, numbers, errors, etc. are not counted. Formulas that return empty text are counted.

e.g.

![](/images/posts/20210520-excel-7.png)

# Reference
[IF function](https://support.microsoft.com/en-us/office/if-function-69aed7c9-4e8a-4755-a9bc-aa8bbff73be2)

[Excel VLOOKUP Function](https://exceljet.net/excel-functions/excel-vlookup-function)

[VLOOKUP function](https://support.microsoft.com/en-us/office/vlookup-function-0bbc8083-26fe-4963-8ab8-93a18ad188a1)

[Excel简易教程之vlookup函数的使用](https://www.jianshu.com/p/c62c1b2eefb6)

[CONCATENATE function](https://support.microsoft.com/en-us/office/concatenate-function-8f8ae884-2ca8-4f7a-b093-75d702bea31d)

[COUNTBLANK function](https://support.microsoft.com/en-us/office/countblank-function-6a92d772-675c-4bee-b346-24af6bd3ac22)