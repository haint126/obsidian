# From pdf table to excel table
- Copy pdf table to excel
	- All field will be in one column
- To transform 1 column to multiple columns, use this formula, replace `num_col` with the number of columns
	- [Source](https://www.extendoffice.com/documents/excel/681-excel-change-columns-to-rows.html)

```
=OFFSET($A$1;COLUMNS($A1:A1)-1+(ROWS($1:1)-1)*num_col;0)
```

![[99_Others/Obsidian/Copy table from pdf or excel/Example.jpeg]]
- To transform 1 column to multiple rows, use this formula, replace `row_num` with number of rows
	- [Source](https://www.extendoffice.com/documents/excel/681-excel-change-columns-to-rows.html)

```
=INDEX($A$1:$A$20;ROW(C1)+(row_num*(COLUMNS($C$1:C$1)-1)))
```

# Copy table from excel
- Go to [tableconvert.com](https://tableconvert.com/excel-to-markdown)
- Copy excel table then paste into the website
- Copy the markdown table

#
---
- Status: #done
- Tags:
- References:
- Related:
