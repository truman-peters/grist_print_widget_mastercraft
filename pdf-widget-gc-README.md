# General Contractor PDF Printer (simplified)

Simplified version of the print widget: searches **General_Contractors_Data**
directly and fills only the GC name + address on the info sheet — nothing
else on the PDF gets touched.

## Why the old one didn't work

The original widget's `fieldMap` was built from placeholder guesses
(`'GC'`, `'GCPhone'`, etc.) before I'd seen your actual schema. Your real
columns (from the `.grist` file) are `General_Contractor_Name`, `Address`,
`Phone_Number`, etc. — none of those guessed names existed, so every field
resolved to empty and nothing showed up on the printed PDF. This version
uses your real column names directly.

## Files

- `pdf-widget.html` — the widget.
- `project_info_sheet_gc.pdf` — the template, **modified** from the
  original: I added an "Address:" line under the GC "Contact:" line (there
  wasn't an address field on the original sheet). Keep both files in the
  same folder — the widget fetches the PDF at print time.

## How it works

1. Search box filters `General_Contractors_Data` by
   `General_Contractor_Name` / `Also_Known_As`.
2. Click a result — preview shows the GC name and address that'll go on
   the sheet.
3. **Print PDF** fills `project_info_sheet_gc.pdf` in the browser and
   downloads it. Long names wrap across the GC box's two lines; a long
   address shrinks its font slightly rather than running off the page.

Verified against your actual converted data — "ABA Services" prints
correctly with its address, and a stress test with the longest GC name in
your dataset ("Warren Flynn Construction Co., Inc., General Corp.") wraps
cleanly across two lines.

## Customizing

```js
const CONFIG = {
  table: 'General_Contractors_Data',
  titleField: 'General_Contractor_Name',
  searchFields: ['General_Contractor_Name', 'Also_Known_As'],
  fieldMap: {
    gcName:  'General_Contractor_Name',
    address: 'Address',
  },
  templateUrl: './project_info_sheet_gc.pdf',
};
```

To add another field (e.g. `Phone_Number`, or append `Secondary_Address`
onto a second line):

1. Add a key to `fieldMap` pointing at the column.
2. Add a matching `x`/`y` entry to `PDF_LAYOUT` (measured the same way as
   `address` was — `pdfY(distanceFromTopOfPage)`).
3. Add one `draw(...)` or `drawWrapped(...)` call for it in `fillPdf()`.

If you want the rest of the original sheet's fields back (Project, ARCH,
Description, etc.), the full version is still available — just needs its
`fieldMap` corrected the same way this one was, against your real
`Projects` table columns (`Project_Name`, `General_Contractor` (a
Reference), `Architect` (a Reference), `Notes`, etc.) instead of the
placeholder names.
