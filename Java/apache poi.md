## Apache POI User Guide Manual

### 1. New Workbook

#### Creating Workbooks

To create a new workbook, use either `HSSFWorkbook` for `.xls` format or `XSSFWorkbook` for `.xlsx` format.

```java
Workbook wb = new HSSFWorkbook(); // or new XSSFWorkbook();
```

#### Saving Workbooks

Save the workbook to a file using `wb.write(fileOut)` where `fileOut` is an `OutputStream`.

```java
try (OutputStream fileOut = new FileOutputStream("workbook.xls")) {
    wb.write(fileOut);
}
```

### 2. New Sheet

#### Creating Sheets

To create a new sheet in a workbook:

```java
Sheet sheet1 = wb.createSheet("new sheet");
Sheet sheet2 = wb.createSheet("second sheet");
```

Ensure sheet names are valid (max 31 characters, specific character restrictions). Use `WorkbookUtil.createSafeSheetName(nameProposal)` to create valid names.

### 3. Creating Cells

#### Working with Cells

To create cells and set their values:

```java
Row row = sheet.createRow(0);
Cell cell1 = row.createCell(0);
cell1.setCellValue(1);

row.createCell(1).setCellValue(1.2);

Cell cell3 = row.createCell(2);
cell3.setCellValue("This is a string");

Cell cell4 = row.createCell(3);
cell4.setCellValue(true);
```

### 4. Creating Date Cells

#### Handling Date Values

To work with date values:

```java
Cell cell1 = row.createCell(0);
cell1.setCellValue(new Date());

CreationHelper createHelper = wb.getCreationHelper();
CellStyle cellStyle = wb.createCellStyle();
cellStyle.setDataFormat(
    createHelper.createDataFormat().getFormat("m/d/yy h:mm"));

Cell cell2 = row.createCell(1);
cell2.setCellValue(new Date());
cell2.setCellStyle(cellStyle);

Cell cell3 = row.createCell(2);
cell3.setCellValue(Calendar.getInstance());
cell3.setCellStyle(cellStyle);
```

### 5. Working with Different Types of Cells

#### Handling Different Types

Cells can contain various types of data:

```java
row.createCell(0).setCellValue(1.1);
row.createCell(1).setCellValue(new Date());
row.createCell(2).setCellValue(Calendar.getInstance());
row.createCell(3).setCellValue("a string");
row.createCell(4).setCellValue(true);
row.createCell(5).setCellType(CellType.ERROR);
```

### 6. Demonstrating Alignment Options

#### Cell Alignment

Adjust cell alignment using `setAlignment` and `setVerticalAlignment`:

```java
row.setHeightInPoints(30);
createCell(wb, row, 0, HorizontalAlignment.CENTER, VerticalAlignment.BOTTOM);
createCell(wb, row, 1, HorizontalAlignment.CENTER_SELECTION, VerticalAlignment.BOTTOM);
// Add more cells with different alignments
```

### 7. Working with Borders

#### Adding Borders

Style cells with borders:

```java
CellStyle style = wb.createCellStyle();
style.setBorderBottom(BorderStyle.THIN);
style.setBottomBorderColor(IndexedColors.BLACK.getIndex());
style.setBorderLeft(BorderStyle.THIN);
style.setLeftBorderColor(IndexedColors.GREEN.getIndex());
// Add more border styles as needed

Cell cell = row.createCell(1);
cell.setCellValue(4);
cell.setCellStyle(style);
```

### 8. Iterating over Rows and Cells

#### Iterating through Data

Iterate over rows and cells in a sheet:

```java
for (Sheet sheet : wb) {
    for (Row row : sheet) {
        for (Cell cell : row) {
            // Process each cell here
        }
    }
}
```

### 9. Merging Cells

#### Cell Merging

Merge cells using `sheet.addMergedRegion(CellRangeAddress)`:

```java
Cell cell = row.createCell(1);
cell.setCellValue("This is a test of merging");

sheet.addMergedRegion(new CellRangeAddress(
        1, // first row (0-based)
        1, // last row  (0-based)
        1, // first column (0-based)
        2  // last column  (0-based)
));
```

### 10. Working with Fonts

#### Customizing Fonts

Customize cell fonts:

```java
Font font = wb.createFont();
font.setFontHeightInPoints((short)24);
font.setFontName("Courier New");
font.setItalic(true);
font.setStrikeout(true);

CellStyle style = wb.createCellStyle();
style.setFont(font);

Cell cell = row.createCell(1);
cell.setCellValue("This is a test of fonts");
cell.setCellStyle(style);
```

---
Here is the continuation of the Apache POI user guide manual based on additional functionalities like using newlines in cells, data formats, fitting sheets to one page, setting print areas, headers and footers, and other advanced features.

---

### 11. Using Newlines in Cells

#### Creating Newlines

To insert newlines in a cell and enable word wrap:

```java
Workbook wb = new XSSFWorkbook(); // or new HSSFWorkbook();
Sheet sheet = wb.createSheet();
Row row = sheet.createRow(2);
Cell cell = row.createCell(2);
cell.setCellValue("Use \n with word wrap on to create a new line");

CellStyle cs = wb.createCellStyle();
cs.setWrapText(true);
cell.setCellStyle(cs);

row.setHeightInPoints((2 * sheet.getDefaultRowHeightInPoints()));
sheet.autoSizeColumn(2);

try (OutputStream fileOut = new FileOutputStream("ooxml-newlines.xlsx")) {
    wb.write(fileOut);
}
wb.close();
```

### 12. Data Formats

#### Formatting Data

Apply specific data formats to cells:

```java
Workbook wb = new HSSFWorkbook();
Sheet sheet = wb.createSheet("format sheet");
DataFormat format = wb.createDataFormat();

Row row1 = sheet.createRow(0);
Cell cell1 = row1.createCell(0);
cell1.setCellValue(11111.25);
CellStyle style1 = wb.createCellStyle();
style1.setDataFormat(format.getFormat("0.0"));
cell1.setCellStyle(style1);

Row row2 = sheet.createRow(1);
Cell cell2 = row2.createCell(0);
cell2.setCellValue(11111.25);
CellStyle style2 = wb.createCellStyle();
style2.setDataFormat(format.getFormat("#,##0.0000"));
cell2.setCellStyle(style2);

try (OutputStream fileOut = new FileOutputStream("workbook.xls")) {
    wb.write(fileOut);
}
wb.close();
```

### 13. Fit Sheet to One Page

#### Adjusting Sheet for Printing

Fit sheet content to one page:

```java
Workbook wb = new HSSFWorkbook();
Sheet sheet = wb.createSheet("format sheet");
PrintSetup ps = sheet.getPrintSetup();
sheet.setAutobreaks(true);
ps.setFitHeight((short) 1);
ps.setFitWidth((short) 1);

// Create cells and rows for spreadsheet content

try (OutputStream fileOut = new FileOutputStream("workbook.xls")) {
    wb.write(fileOut);
}
wb.close();
```

### 14. Set Print Area

#### Defining Print Area

Set a specific print area for a sheet:

```java
Workbook wb = new HSSFWorkbook();
Sheet sheet = wb.createSheet("Sheet1");

// Option 1: Set print area by cell references
wb.setPrintArea(0, "$A$1:$C$2");

// Option 2: Set print area by indices
wb.setPrintArea(0, 0, 1, 0, 0);

try (OutputStream fileOut = new FileOutputStream("workbook.xls")) {
    wb.write(fileOut);
}
wb.close();
```

### 15. Set Page Numbers on Footer

#### Adding Page Numbers

Include page numbers in the footer:

```java
Workbook wb = new HSSFWorkbook(); // or new XSSFWorkbook();
Sheet sheet = wb.createSheet("format sheet");
Footer footer = sheet.getFooter();
footer.setRight("Page " + HeaderFooter.page() + " of " + HeaderFooter.numPages());

// Create cells and rows for spreadsheet content

try (OutputStream fileOut = new FileOutputStream("workbook.xls")) {
    wb.write(fileOut);
}
wb.close();
```

### 16. Using the Convenience Functions

#### Simplified Styling and Formatting

Utilize convenience functions for styling and formatting:

```java
Workbook wb = new HSSFWorkbook(); // or new XSSFWorkbook()
Sheet sheet1 = wb.createSheet("new sheet");

// Create a merged region
Row row = sheet1.createRow(1);
Cell cell = row.createCell(1);
cell.setCellValue("This is a test of merging");
CellRangeAddress region = CellRangeAddress.valueOf("B2:E5");
sheet1.addMergedRegion(region);

// Set borders around merged region
RegionUtil.setBorderBottom(BorderStyle.MEDIUM_DASHED, region, sheet1, wb);
RegionUtil.setBorderTop(BorderStyle.MEDIUM_DASHED, region, sheet1, wb);
RegionUtil.setBorderLeft(BorderStyle.MEDIUM_DASHED, region, sheet1, wb);
RegionUtil.setBorderRight(BorderStyle.MEDIUM_DASHED, region, sheet1, wb);
RegionUtil.setBottomBorderColor(IndexedColors.AQUA.getIndex(), region, sheet1, wb);
RegionUtil.setTopBorderColor(IndexedColors.AQUA.getIndex(), region, sheet1, wb);
RegionUtil.setLeftBorderColor(IndexedColors.AQUA.getIndex(), region, sheet1, wb);
RegionUtil.setRightBorderColor(IndexedColors.AQUA.getIndex(), region, sheet1, wb);

// Set cell alignment and create cell with specific style
CellStyle style = wb.createCellStyle();
style.setIndention((short) 4);
CellUtil.createCell(row, 8, "This is the value of the cell", style);

// Write out the workbook
try (OutputStream fileOut = new FileOutputStream("workbook.xls")) {
    wb.write(fileOut);
}
wb.close();
```

### 17. Shift Rows up or down on a Sheet

#### Adjusting Row Positions

Shift rows on a sheet:

```java
Workbook wb = new HSSFWorkbook();
Sheet sheet = wb.createSheet("row sheet");

// Shift rows 6 - 10 up by 5 rows
sheet.shiftRows(5, 10, -5);

try (OutputStream fileOut = new FileOutputStream("workbook.xls")) {
    wb.write(fileOut);
}
wb.close();
```

### 18. Set a Sheet as Selected

#### Marking a Sheet as Selected

Set a sheet as selected:

```java
Workbook wb = new HSSFWorkbook();
Sheet sheet = wb.createSheet("row sheet");
sheet.setSelected(true);
```

### 19. Set the Zoom Magnification

#### Adjusting Sheet Zoom

Set the zoom level (e.g., 75%):

```java
Workbook wb = new HSSFWorkbook();
Sheet sheet1 = wb.createSheet("new sheet");
sheet1.setZoom(75); // 75 percent magnification
```

### 20. Headers and Footers

#### Adding Headers and Footers

Set headers and footers for a sheet:

```java
Workbook wb = new HSSFWorkbook();
Sheet sheet = wb.createSheet("new sheet");
Header header = sheet.getHeader();
header.setCenter("Center Header");
header.setLeft("Left Header");
header.setRight("Right Header");

try (OutputStream fileOut = new FileOutputStream("workbook.xls")) {
    wb.write(fileOut);
}
wb.close();
```

### 21. Outlining

#### Grouping and Outlining

Group rows and columns to create outlines:

```java
Workbook wb = new HSSFWorkbook();
Sheet sheet1 = wb.createSheet("new sheet");

sheet1.groupRow(5, 14);
sheet1.groupRow(7, 14);
sheet1.groupRow(16, 19);
sheet1.groupColumn(4, 7);
sheet1.groupColumn(9, 12);
sheet1.groupColumn(10, 11);

try (OutputStream fileOut = new FileOutputStream("workbook.xls")) {
    wb.write(fileOut);
}
wb.close();
```





## 22. Creating Named Range / Named Cell
Named Range is a way to refer to a group of cells by a name. Named Cell refers to a single cell. You can create and refer to cells in a workbook by their named range. When working with Named Ranges, the classes `org.apache.poi.ss.util.CellReference` and `org.apache.poi.ss.util.AreaReference` are used.
### create named range for a single cell using AreaReference
```java
// setup code
String sname = "TestSheet", cname = "TestName", cvalue = "TestVal";
Workbook wb = new HSSFWorkbook();
Sheet sheet = wb.createSheet(sname);
sheet.createRow(0).createCell(0).setCellValue(cvalue);

Name namedCell = wb.createName();
namedCell.setNameName(cname + "1");
String reference = sname + "!$A$1:$A$1"; // area reference
namedCell.setRefersToFormula(reference);
```

### create named range for a single cell using CellReference
```java
Name namedCel2 = wb.createName();
namedCel2.setNameName(cname + "2");
reference = sname + "!$A$1"; // cell reference
namedCel2.setRefersToFormula(reference);
```

###  create named range for an area using AreaReference
```java
Name namedCel3 = wb.createName();
namedCel3.setNameName(cname + "3");
reference = sname + "!$A$1:$C$5"; // area reference
namedCel3.setRefersToFormula(reference);
```

###  create named formula
```java
Name namedCel4 = wb.createName();
namedCel4.setNameName("my_sum");
namedCel4.setRefersToFormula("SUM(" + sname + "!$I$2:$I$6)");
```

## 23. Reading from Named Range / Named Cell

```java
// setup code
String cname = "TestName";
Workbook wb = getMyWorkbook(); // retrieve workbook

int namedCellIdx = wb.getNameIndex(cellName);
Name aNamedCell = wb.getNameAt(namedCellIdx);

AreaReference aref = new AreaReference(aNamedCell.getRefersToFormula());
CellReference[] crefs = aref.getAllReferencedCells();

for (int i=0; i<crefs.length; i++) {
    Sheet s = wb.getSheet(crefs[i].getSheetName());
    Row r = sheet.getRow(crefs[i].getRow());
    Cell c = r.getCell(crefs[i].getCol());
    // extract the cell contents based on cell type etc.
}
```

### Reading from non-contiguous Named Ranges

```java
// Setup code
String cname = "TestName";
Workbook wb = getMyWorkbook(); // retrieve workbook

int namedCellIdx = wb.getNameIndex(cellName);
Name aNamedCell = wb.getNameAt(namedCellIdx);

AreaReference[] arefs = AreaReference.generateContiguous(aNamedCell.getRefersToFormula());

for (int i=0; i<arefs.length; i++) {
    CellReference[] crefs = arefs[i].getCells();
    for (int j=0; j<crefs.length; j++) {
        Sheet s = wb.getSheet(crefs[j].getSheetName());
        Row r = s.getRow(crefs[j].getRow());
        Cell c = r.getCell(crefs[j].getCol());
        // Do something with this corner cell
    }
}
```

When a cell is deleted, Excel does not delete the attached named range. Therefore, the workbook can contain named ranges that point to cells that no longer exist. Check the validity of a reference before constructing `AreaReference`.

```java
if(name.isDeleted()){
  // named range points to a deleted cell.
} else {
  AreaReference ref = new AreaReference(name.getRefersToFormula());
}
```

## 24. Adjust column width to fit the contents

```java
Sheet sheet = workbook.getSheetAt(0);
sheet.autoSizeColumn(0); // adjust width of the first column
sheet.autoSizeColumn(1); // adjust width of the second column
```

For `SXSSFWorkbooks`, ensure columns are tracked for auto-sizing prior to flushing rows.

```java
SXSSFWorkbook workbook = new SXSSFWorkbook();
SXSSFSheet sheet = workbook.createSheet();
sheet.trackColumnForAutoSizing(0);
sheet.trackColumnForAutoSizing(1);
// create cells
for (int r=0; r < 10; r++) {
    Row row = sheet.createRow(r);
    for (int c; c < 10; c++) {
        Cell cell = row.createCell(c);
        cell.setCellValue("Cell " + c.getAddress().formatAsString());
    }
}
sheet.autoSizeColumn(0);
sheet.autoSizeColumn(1);
```

**Note:** `Sheet#autoSizeColumn()` does not evaluate formula cells; their width is based on the cached formula result.

## 25. Conditional Formatting

```java
Workbook workbook = new HSSFWorkbook(); // or new XSSFWorkbook();
Sheet sheet = workbook.createSheet();
SheetConditionalFormatting sheetCF = sheet.getSheetConditionalFormatting();

ConditionalFormattingRule rule1 = sheetCF.createConditionalFormattingRule(ComparisonOperator.EQUAL, "0");
FontFormatting fontFmt = rule1.createFontFormatting();
fontFmt.setFontStyle(true, false);
fontFmt.setFontColorIndex(IndexedColors.DARK_RED.index);
BorderFormatting bordFmt = rule1.createBorderFormatting();
bordFmt.setBorderBottom(BorderStyle.THIN);
bordFmt.setBorderTop(BorderStyle.THICK);
bordFmt.setBorderLeft(BorderStyle.DASHED);
bordFmt.setBorderRight(BorderStyle.DOTTED);
PatternFormatting patternFmt = rule1.createPatternFormatting();
patternFmt.setFillBackgroundColor(IndexedColors.YELLOW.index);

ConditionalFormattingRule rule2 = sheetCF.createConditionalFormattingRule(ComparisonOperator.BETWEEN, "-10", "10");

ConditionalFormattingRule [] cfRules = { rule1, rule2 };
CellRangeAddress[] regions = { CellRangeAddress.valueOf("A3:A5") };
sheetCF.addConditionalFormatting(regions, cfRules);
```

## 26. Hiding and Un-Hiding Rows

```java
Workbook workbook = new XSSFWorkbook();  // OR new HSSFWorkbook()
Sheet sheet = workbook.createSheet(0);
Row row = workbook.createRow(0);
row.setZeroHeight();
```

To unhide previously hidden rows:

```java
Workbook workbook = WorkbookFactory.create(new File(.......));
Sheet sheet = workbook.getSheetAt(0);
Iterator<Row> rowIter = sheet.iterator();

while(rowIter.hasNext()) {
    Row row = rowIter.next();
    if(row.getZeroHeight()) {
        row.setZeroHeight(false);
    }
}
```

## 27.Setting Cell Properties

```java
Workbook workbook = new XSSFWorkbook();  // OR new HSSFWorkbook()
Sheet sheet = workbook.createSheet("Sheet1");

Map<String, Object> properties = new HashMap<String, Object>();
// border around a cell
properties.put(CellUtil.BORDER_TOP, BorderStyle.MEDIUM);
properties.put(CellUtil.BORDER_BOTTOM, BorderStyle.MEDIUM);
properties.put(CellUtil.BORDER_LEFT, BorderStyle.MEDIUM);
properties.put(CellUtil.BORDER_RIGHT, BorderStyle.MEDIUM);

// Give it a color (RED)
properties.put(CellUtil.TOP_BORDER_COLOR, IndexedColors.RED.getIndex());
properties.put(CellUtil.BOTTOM_BORDER_COLOR, IndexedColors.RED.getIndex());
properties.put(CellUtil.LEFT_BORDER_COLOR, IndexedColors.RED.getIndex());
properties.put(CellUtil.RIGHT_BORDER_COLOR, IndexedColors.RED.getIndex());

// Apply the borders to the cell at B2
Row row = sheet.createRow(1);
Cell cell = row.createCell(1);
CellUtil.setCellStyleProperties(cell, properties);

// Apply the borders to a 3x3 region starting at D4
for (int ix=3; ix <= 5; ix++) {
  row = sheet.createRow(ix);
  for (int iy = 3; iy <= 5; iy++) {
    cell = row.createCell(iy);
    CellUtil.setCellStyleProperties(cell, properties);
  }
}
```

## 28.Drawing Borders

```java
PropertyTemplate pt = new PropertyTemplate();

// #1) these borders will all be medium in default color
pt.drawBorders(new CellRangeAddress(1, 3, 1, 3), BorderStyle.MEDIUM, BorderExtent.ALL);

// #2) these cells will have medium outside borders and thin inside borders
pt.drawBorders(new CellRangeAddress(5, 7, 1, 3), BorderStyle.MEDIUM, BorderExtent.OUTSIDE);
pt.drawBorders(new CellRangeAddress(5, 7, 1, 3), BorderStyle.THIN, BorderExtent.INSIDE);

// #3) these cells will all be medium weight with different colors for the
//     outside, inside horizontal, and inside vertical borders. The center
//     cell will have no borders.
pt.drawBorders(new CellRangeAddress(9, 11, 1, 3), BorderStyle.MEDIUM, IndexedColors.RED.getIndex(), BorderExtent.OUTSIDE);
pt.drawBorders(new CellRangeAddress(9, 11, 1, 3), BorderStyle.MEDIUM, IndexedColors.BLUE.getIndex(), BorderExtent.INSIDE_VERTICAL);
pt.drawBorders(new CellRangeAddress(9, 11, 1, 3), BorderStyle.MEDIUM, IndexedColors.GREEN.getIndex(), BorderExtent.INSIDE_HORIZONTAL);
pt.drawBorders(new CellRangeAddress(10, 10, 2, 2), BorderStyle.NONE, BorderExtent.ALL);

// apply borders to sheet
Workbook wb = new XSSFWorkbook();
Sheet sh = wb.createSheet("Sheet1");
pt.applyBorders(sh);
```

## 29.Creating a Pivot Table

```java
XSSFWorkbook wb = new XSSFWorkbook();
XSS

FSheet sheet = wb.createSheet();

// Create some data to build the pivot table on
setCellData(sheet);

XSSFPivotTable pivotTable = sheet.createPivotTable(new AreaReference("A1:D4"), new CellReference("H5"));

// Configure the pivot table
// Use first column as row label
pivotTable.addRowLabel(0);
// Sum up the second column
pivotTable.addColumnLabel(DataConsolidateFunction.SUM, 1);
// Set the third column as filter
pivotTable.addColumnLabel(DataConsolidateFunction.AVERAGE, 2);
// Add filter on fourth column
pivotTable.addReportFilter(3);
```

## 29.  Cells with multiple styles (Rich Text Strings)

To apply different formatting to different parts of a cell, use `RichTextString`.

### HSSF Example
```java
HSSFCell hssfCell = row.createCell(idx);
HSSFRichTextString richString = new HSSFRichTextString("Hello, World!");
richString.applyFont(0, 6, font1);
richString.applyFont(6, 13, font2);
hssfCell.setCellValue(richString);
```

### XSSF Example
```java
XSSFCell cell = row.createCell(1);
XSSFRichTextString rt = new XSSFRichTextString("The quick brown fox");
rt.applyFont(0, 10, font1);
rt.applyFont(10, 19, font2);
rt.append(" Jumped over the lazy dog", font3);
cell.setCellValue(rt);
```
 
