# GetSheetDone
Helper library to pull data from google spreadsheets.   
Live demo: https://giladaya.github.io/get-sheet-done/

## Caveats
This is a simple library intended to provide a quick-and-dirty way to only **read** data from a Google spreadsheet.  
The document you read from must be publicly published for this to work and the entire sheet data is fetched.  
If you need something more sophisticated, take a look at  the [Google Sheets API](https://developers.google.com/sheets/api/)

## Usage 
### Preparation
- In the spreadsheet, `file -> publish to web`  
- Note the document key in the URL  

### In browser
See `/example/src` for a simple browser-based demo.  
Make sure to include the the dependecy `fetch-jsonp` in the page.

### With a package loader
Install from npm:  
```
npm install --save get-sheet-done
```

Use in code:  
```javascript
import GetSheetDone from 'get-sheet-done';

GetSheetDone.raw(DOC_KEY).then(sheet => {
    console.log(sheet)
})
```


## Api
There are three functions that return a promise and fetch the data from a published spreadsheet.  
Each function gets as arguments the document key (from the document url) and optinally the sheet index (first sheet has index 1).  
The functions differ in the way they parse and return the data:

### Raw
```javascript
GetSheetDone.raw(id, sheetNum = 1)
```
Get the data as a raw array of arrays (rows).
Suitable for spreadsheets that only contain values.

For example, this sheet:  

|     |     |     |     |     |
|-----|-----|-----|-----|-----|
|     | *A* | *B* | *C* | *D* |
| *1* | 22  | 24  | 26  | 20  |
| *2* | 31  | 32  | 37  | 36  |
| *3* | 11  | 14  | 19  | 12  |

Will result in this data:
```javascript
{
  title: "Sheet1",
  updated: "2017-07-30T07:11:40.056Z",
  data: [
    ["22", "24", "26", "20"],
    ["31", "32", "37", "36"],
    ["11", "14", "19", "12"],
  ]
}
```

### Labeled Columns
```javascript
GetSheetDone.labeledCols(id, sheetNum = 1)
```
Get the data as an array of objects, each representing a row. The keys are taken from the first row in the spreadsheet which is assumed to contain column headers.  
Note that all labels are converted to lower case and the only non-alphanumeric characters that are kept are periods and hyphens, so if the original label was `Lorem-ipsum dolor.sit:amet_blah`, the result labels would be: `lorem-ipsumdolor.sitametblah`.

For example, this sheet:  

|     |     |     |     |     |
|-----|-----|-----|-----|-----|
|     | *A* | *B* | *C* | *D* |
| *1* | **Q1** | **Q2** | **Q3** | **Q4** |
| *2* | 22  | 24  | 26  | 20  |
| *3* | 31  | 32  | 37  | 36  |
| *4* | 11  | 14  | 19  | 12  |
 
Will result in this data: 
```javascript
{
  title: "Sheet1",
  updated: "2017-07-30T07:11:40.056Z",
  data: [{
    Q1: "22",
    Q2: "24",
    Q3: "26",
    Q4: "20"
  },{
    Q1: "31",
    Q2: "32",
    Q3: "37",
    Q4: "36"
  },{
    Q1: "11",
    Q2: "14",
    Q3: "19",
    Q4: "12"
  }]
}
``` 

### Labeled Columns and rows
```javascript
GetSheetDone.labeledColsRows(id, sheetNum = 1)
```
Get the data as an object of objects, each representing a row. The first column in the spreadsheet is assumed to contain row headers, and the first row in the spreadsheet is assumed to contain column headers.  
Note that all labels are converted to lower case and the only non-alphanumeric characters that are kept are periods and hyphens, so if the original label was `Lorem-ipsum dolor.sit:amet_blah`, the result labels would be: `lorem-ipsumdolor.sitametblah`.

So this sheet:  

|     |        |        |        |        |        |
|-----|--------|--------|--------|--------|--------|
|     | *A*    | *B*    | *C*    | *D*    | *E*    |
| *1* |        | **Q1** | **Q2** | **Q3** | **Q4** |
| *2* | **UK** | 22     | 24     | 26     | 20     |
| *3* | **US** | 31     | 32     | 37     | 36     |
| *4* | **AU** | 11     | 14     | 19     | 12     |

Will result in this data: 
```javascript
{
  title: "Sheet1",
  updated: "2017-07-30T07:11:40.056Z",
  data: {
    UK: {
      Q1: "22",
      Q2: "24",
      Q3: "26",
      Q4: "20"
    },
    US: {
      Q1: "31",
      Q2: "32",
      Q3: "37",
      Q4: "36"
    },
    AU: {
      Q1: "11",
      Q2: "14",
      Q3: "19",
      Q4: "12"
    }
  }
}
``` 

## Development
1. Clone this repo
2. `npm install`
3. `npm start`
4. Go to `localhost:3000`
5. See `package.json` for other available scripts
