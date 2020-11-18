# Basic Excel Terminology

| Term                    | Explanation                                                  |
| ----------------------- | ------------------------------------------------------------ |
| Spreadsheet or Workbook | A **Spreadsheet** is the main file you are creating or working with. |
| Worksheet or Sheet      | A **Sheet** is used to split different kinds of contents within the same spreadsheet. A **Spreadsheet** can have one or more **Sheets**. |
| Column                  | A **Column** is a vertical line, and it's represented by an uppercase letter: *A*. |
| Row                     | A **Row** is a horizontal line, and it's represented by a number: *1* |
| Cell                    | A **Cell** is a combination of **Column** and **Row**, represented by both an uppercase letter and a number: *A1*. |

# Reading Excel Spreadsheets With openpyxl

## Load workbook: load_workbook()

```python
from openpyxl import load_workbook
workbook = load_workbook(filename="sample.xlsx")
workbook.sheetnames

sheet = workbook.active
# sheet.title

sheet["A1"]    # Return the main Cell object
sheet["A1"].value

sheet.cell(row=10, column=6).value
```

`load_workbook()` opens a spreadsheet.

`workbook.sheetnames` returns all the sheets you have available to work with.    

`workbook.active` selects the first available sheet.    

The method `.cell()` is used to retrieve a cell using index notation.    

Python uses zero-indexed notation, spreadsheets use one-indexed notation.    

### Additional Reading Options

+ **read_only** loads a spreadsheet in read-only mode allowing you to open very large Excel files.
+ **data_only** ignores loading formulas and instead loads only the resulting values.

## Importing Data From a Spreadsheet

### Iterating Through the Data

The result from all iterations comes in the form of **tuples**.

+ Slice the data with a combination of columns and rows, `sheet["A1:C2"]`
+ Get ranges of rows or columns
  + `sheet["A"]`, get all cells from column A
  + `sheet["A:B"]`, get all cells for a range of columns
  + `sheet[5]`, get all cells from row 5
+ Using *generators* to go through data:
  + `.iter_rows(min_row, max_row, min_col, max_col, values_only=True)`
  + `.iter_cols(min_row, max_row, min_col, max_col, values_only=True)`

+ Iterate through the whole dataset
  + `.rows`
  + `.columns`

## Manipulate Spreadsheet Data

### Using Python's Default Data Structures

```python
import json
from openpyxl import load_workbook

workbook = load_workbook(filename="sample.xlsx")
sheet = workbook.active

products = {}

# Using the values_only because you want to return the cells' values
for row in sheet.iter_rows(min_row=2,
                           min_col=4,
                           max_col=7,
                           values_only=True):
    product_id = row[0]
    product = {
        "parent": row[1],
        "title": row[2],
        "category": row[3]
    }
    products[product_id] = product

# Using json here to be able to format the output for displaying later
print(json.dumps(products))
```

## Convert Data Into Python Class

[Spreadsheet download link](https://raw.githubusercontent.com/realpython/materials/master/openpyxl-excel-spreadsheets-python/reviews-sample.xlsx)

### Object-Oriented Analysis

There are two significant elements you can extract from the data available:

1. Products
2. Reviews

A **Product** has:

+ ID
+ Title
+ Parent
+ Category

The **Review** has a few more fields:

+ ID
+ Customer ID
+ Stars
+ Headline
+ Body
+ Date







