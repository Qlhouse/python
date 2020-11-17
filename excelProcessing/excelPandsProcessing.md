# read

The `read_excel()` method can read Excel 2003 (.xls) files using the `xlrd` Python module. Excel 2007+ (.xlsx) files can be read using either `xlrd` or `openyxl`. Binary Excel (.xlsb) files can be read using `pyxlsb`. The `to_excel` instance method is used for saving a `DataFrame` to Excel.     

## read_excel()

`read_excel` takes a path to an Excel file, and the `sheet_name` indicating which sheet to parse.    

```python
# Return a DataFrame
pd.read_excel('path_to_file.xls', sheet_name='Sheet1')
```

+ The arguments `sheet_name` allows specifying the sheet or sheets to read
+ The default value for `sheet_name` is 0, indicating to read the first sheet
+ Pass a string to refer to the name of a particular sheet in the workbook
+ Pass an integer to refer to the index of a sheet. Indices follow Python convention, beginning at 0.
+ Pass a list of either strings or integers, to return a dictionary of specified sheets.
+ Pass a `None` to return a dictionary of all available sheets

## ExcelFile class

To facilitate working with multiple sheets from the same file, the `ExcelFile` class can be used to wrap the file and can be passed into `read_excel`.    

```python
data = {}
# For when Sheet1's format differs from Sheet2
with pd.ExcelFile('path_to_file.xls') as xls:
    data['Sheet1'] = pd.read_excel(xls, 'Sheet1', index_col=None,
                                   na_values=['NA'])
    data['Sheet2'] = pd.read_excel(xls, 'Sheet2', index_col=1)
```

Note that if the same parsing parameters are used for all sheets, a list of sheet names can simply be passed to `read_excel` with no loss in performance.    

```python
# using the ExcelFile class
data = {}
with pd.ExcelFile('path_to_file.xls') as xls:
    data['Sheet1'] = pd.read_excel(xls, 'Sheet1', index_col=None,
                                   na_values=['NA'])
    data['Sheet2'] = pd.read_excel(xls, 'Sheet2', index_col=None,
                                   na_values=['NA'])

# equivalent using the read_excel function
data = pd.read_excel('path_to_file.xls', ['Sheet1', 'Sheet2'],
                     index_col=None, na_values=['NA'])
```

## Reading a MultiIndex

`read_excel` can read a `MultiIndex` index, by passing a list of columns to `index_col` and a `MultiIndex` column by passing a list of rows to header. 

