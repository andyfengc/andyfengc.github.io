---
layout: post
title: Java .NET POI tutorial
author: Andy Feng
---

# Introduction #
[`Apache POI`](http://poi.apache.org/) is a popular API that allows programmers to create, modify, and display MS Office files using Java programs. It is an open source library developed and distributed by Apache Software Foundation to design or modify Microsoft Office files using Java program. It contains classes and methods to decode the user input data or a file into MS Office documents.

[`NPOI`](https://github.com/nissl-lab/npoi) is the C# implementation of Java POI, which can help you read/write XLS, DOC, PPT file extensions. It covers most of the features of Excel like styling, formatting, data formulas, extract images, etc. The good thing is that it does not require Microsoft Office on the server. 

The index of row and column (cells in a row) starts from 0.

# POI
POI includes a few APIs to manipulate Excel, Word and Powerpoint.
When it comes to Excel processing, POI has two APIs:

`HSSF` (Horrible Spreadsheet Format) : It is used to read and write xls format of MS-Excel files.

`XSSF` (XML Spreadsheet Format) : It is used for xlsx file format of MS-Excel.

## Preview ##
create an empty xls file through HSSF

## Steps ##
Create empty Java project
Add POI library

## Add POI library ##
The easiest way to add POI library is through Maven.

right click project > convert to maven project

open pom.xml, add dependency

	<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	...
		<dependencies>
			<!-- HSSF -->
			<dependency>
				<groupId>org.apache.poi</groupId>
				<artifactId>poi</artifactId>
				<version>3.13</version>
			</dependency>
			<!-- XSSF -->
			<dependency>
				<groupId>org.apache.poi</groupId>
				<artifactId>poi-ooxml</artifactId>
				<version>3.13</version>
			</dependency>
		</dependencies>
	</project>

That's it.

There is another way to add libary. You can add POI library manually through downloading binary disribution at [here](http://poi.apache.org/download.html). Then unzip and add jars to your project.

## Learn common APIs ##
There are two major APIs in the case of Excel processing in POI. 

- HSSF - implementations of the Excel ’97(-2007) file format.

- XSSF - implementations of the Excel 2007 OOXML (.xlsx) file format.

two major APIs in Word processing

- XWPF - word 2007 (docx)
- HWPF - word 2003 (doc)

### Workbook ###

Workbook is the fundamentals of POI and an interface under org.apache.poi.ss.usermodel package. It is directly related to Excel workbooks. There are two implementations in POI:

- **HSSFWorkbook:** under the org.apache.poi.hssf.usermodel package. It implements the Workbook interface and is used for Excel files in .xls format. This class has methods to read and write Microsoft Excel files in .xls format. It is compatible with MS-Office versions 97–2003.

- **XSSFWorkbook:** under the org.apache.xssf.usemodel package. This class has methods to read and write Microsoft Excel and OpenOffice xml files in .xls or .xlsx format. It is compatible with MS-Office versions 2007 or later.

### Sheet ###
Sheet is an interface under the org.apache.poi.ss.usermodel package. It represents a worksheet in Excel, which typically includes a grid of cells. Similarly, there are two implementations in POI: 

- **HSSFSheet:** under the org.apache.poi.hssf.usermodel package. It can create excel spreadsheets and it allows to format the sheet style and sheet data.
- **XSSFSheet:** under org.apache.poi.hssf.usermodel package. 

### Row ###
It is an interface under the org.apache.poi.ss.usermodel package. It is used for high-level representation of a row of a spreadsheet. There are two implementations:

- **XSSFRow:** under the org.apache.poi.xssf.usermodel package. It implements the Row interface, therefore it can create rows in a spreadsheet. 

### Cell ###
It is an interface under the org.apache.poi.ss.usermodel package. It represents a cell in one row of a spreadsheet.

Cells can take various attributes such as blank, numeric, date, error, etc. Cells should have their own numbers (0 based) before being added to a row.

There are two implementations:

- **XSSFCell:** under the org.apache.poi.xssf.usermodel package. There are following types of Cells.
-
	- CELL_TYPE_BLANK	Represents blank cell
	- CELL_TYPE_BOOLEAN	Represents Boolean cell (true or false)
	- CELL_TYPE_ERROR	Represents error value on a cell
	- CELL_TYPE_FORMULA	Represents formula result on a cell
	- CELL_TYPE_NUMERIC	Represents numeric data on a cell
	- CELL_TYPE_STRING	Represents string (text) on a cell

### Other classes ###
- **XSSFCellStyle:**  under the org.apache.poi.xssf.usermodel package. It will provide possible information regarding the format of the content in a cell of a spreadsheet. It also provides options for modifying that format. It implements the CellStyle interface.

- **HSSFColor:** under the org.apache.poi.hssf.util package. It provides different colors as nested classes. Usually these nested classes are represented by using their own indexes. It implements the Color interface.

- **XSSFFont:** under the org.apache.poi.xssf.usermodel package. It implements the Font interface and therefore it can handle different fonts in a workbook.

- **XSSFHyperlink:** under the org.apache.poi.xssf.usermodel package. It implements the Hyperlink interface. It is used to set a hyperlink to the cell contents of a spreadsheet.

- **XSSFCreationHelper: **under the org.apache.poi.xssf.usermodel package. It implements the CreationHelper interface. It is used as a support class for formula evaluation and setting up hyperlinks.

- **XSSFPrintSetup:** under the org.apache.poi.xssf.usermodel package. It implements the PrintSetup interface. It is used to set print page size, area, options, and settings.

# NPOI
## major APIs
- HSSF (Excel 2003)
- XSSF (Excel 2007) 
- XWPF (Word 2007)

Install lib

![](/images/posts/20201223-npoi-1.png)

Read from excel

	using System;
	using System.IO;
	using NPOI.HSSF.UserModel;
	using NPOI.SS.UserModel;
	
	private static void procExcel(string fileName, string schoolPicDir){
	    try
	    {
	        IWorkbook workbook;
	        FileStream fs = new FileStream(fileName, FileMode.Open, FileAccess.Read);
	        if (fileName.IndexOf(".xlsx") > 0) 
	        	workbook = new XSSFWorkbook(fs);
	        else if (fileName.IndexOf(".xls") > 0) 
	            workbook = new HSSFWorkbook(fs);
	    	//First sheet
	    	ISheet sheet = workbook.GetSheetAt(0);
	    	if (sheet != null)
	        { 
	    		int rowCount = sheet.LastRowNum; // This may not be valid row count.
	        	// If first row is table head, i starts from 1
	            for (int i = 1; i <= rowCount; i++)
	        	{
	                IRow curRow = sheet.GetRow(i);
	                // Works for consecutive data. Use continue otherwise 
	                if (curRow == null)
	            	{
	                    // Valid row count
	                	rowCount = i - 1;
	                	break;
	            	}
	                // Get data from the 4th column (4th cell of each row)
	            	var cellValue = curRow.GetCell(3).StringCellValue.Trim();
	                Console.WriteLine(cellValue);
	        	}
	        }
	    }
	    catch(Exception e)
	    {
	        Console.WriteLine(e.Message);
	    }
	}

Write to Excel

	public byte[] ToExcel(IEnumrable<Item> items){
		// generate excel
		using (var memoryStream = new MemoryStream())
		{
		    var workbook = new XSSFWorkbook();
		    var sheet = workbook.CreateSheet("Test dummy");
		    int rowIndex = 0;
		    int idIndex = 0;
		    int nameIndex = 1;
		    // add header
		    var row = sheet.CreateRow(rowIndex++);
	        row.CreateCell(idIndex).SetCellValue("ID");  
	        row.CreateCell(nameIndex).SetCellValue("Name");  
		    // add data rows
		    foreach (var item in items)
		    {
		        row = sheet.CreateRow(rowIndex++);
		        row.CreateCell(idIndex).SetCellValue(item.Id);
		        row.CreateCell(nameIndex).SetCellValue(item.Name);
		    }
			// add total row
			var detailSubtotalCellStyle = GetTotalStyle();
			row = sheet.CreateRow(rowIndex++);			
			cell = row.CreateCell(0);// Create the first cell – "Total" – and apply the style
			cell.SetCellValue("Total:");
			cell.CellStyle = detailSubtotalCellStyle;			
			cell = row.CreateCell(1);// Create the second cell  which is empty 
			cell.CellStyle = detailSubtotalCellStyle;
		    // format
		    for (int i = 0; i <= row.LastCellNum; i++) {
				sheet.AutoSizeColumn(i);
			}
		    // export
		    workbook.Write(memoryStream);
		    return memoryStream.ToArray();
		}
	}

	// set style
	public ICellStyle GetTotalStyle(){
		// Create the style object
		var detailSubtotalCellStyle = workbook.CreateCellStyle();
		// Define a thin border for the top and bottom of the cell
		detailSubtotalCellStyle.BorderTop = CellBorderType.THIN;
		detailSubtotalCellStyle.BorderBottom = CellBorderType.THIN;
		// Create a font object and make it bold
		var detailSubtotalFont = workbook.CreateFont();
		detailSubtotalFont.Boldweight = (short)FontBoldWeight.BOLD;
		detailSubtotalCellStyle.SetFont(detailSubtotalFont);
	}

download from .net core web api

	var excelBytes = ToExcel(items);
	string fileName = $"items.xlsx";
    return File(excelBytes, "application/vnd.openxmlformats-officedocument.spreadsheetml.sheet", fileName);

# Reference
[Getting Started with NPOI](https://github.com/nissl-lab/npoi/wiki/Getting-Started-with-NPOI)

[Read data from Excel using NPOI in C#](https://shengwenbai.github.io/2017/02/18/npoi/)

[How To Create Excel Spreadsheets Using NPOI](https://steemit.com/utopian-io/@haig/how-to-create-excel-spreadsheets-using-npoi)