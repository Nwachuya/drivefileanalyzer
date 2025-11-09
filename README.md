# Drive File Analyzer Library Documentation

## What is This Library?

This library helps you understand what's in your Google Drive folders without changing anything. It analyzes your files and provides reports about file types, duplicates, and organization recommendations.

## How to Use This Library

### Step 1: Add the Library to Your Apps Script Project

1. Open your Google Apps Script project
2. Click the "Services" icon (plus sign) in the left sidebar
3. Click "Libraries"
4. Enter the library ID: `1pvRGM-KhqH3qJG3Dxt7o8RTp2j6yXW_dJlvbPrY2nB9L7chG2lrFoQGK`
5. Click "Add"
6. The identifier should be set to `DriveFileAnalyzer`
7. Click "Save"

### Step 2: Get Your Folder ID

1. Go to drive.google.com
2. Open the folder you want to analyze
3. Look at the URL in your browser
4. The folder ID is the long string after `/folders/`
5. Copy this ID for use in your code

Example URL: `https://drive.google.com/drive/u/0/folders/ABC123xyz456def789`
Folder ID: `ABC123xyz456def789`

### Step 3: Basic Usage Code

Copy and paste this code into your Apps Script file:

```javascript
const FOLDER_ID = "YOUR_FOLDER_ID_HERE";
const SHEET_NAME = "Log";

function runAnalysis() {
  const results = DriveFileAnalyzer.analyzeFolder(FOLDER_ID);
  
  logAnalysisResult(results);
}

function logAnalysisResult(results) {
  const ss = SpreadsheetApp.getActiveSpreadsheet();
  let sheet = ss.getSheetByName(SHEET_NAME);
  
  if (!sheet) {
    sheet = ss.insertSheet(SHEET_NAME);
    sheet.getRange(1, 1, 1, 6).setValues([[
      "Date", "Files Analyzed", "File Types", "Duplicates Found", 
      "Recommendations", "Breakdown"
    ]]);
  }
  
  const row = [
    new Date(),
    results.filesAnalyzed,
    Object.keys(results.fileTypeBreakdown).length,
    results.potentialDuplicates.length,
    results.recommendations.length,
    JSON.stringify(results.fileTypeBreakdown)
  ];
  
  const lastRow = sheet.getLastRow();
  sheet.getRange(lastRow + 1, 1, 1, 6).setValues([row]);
  
  console.log("Analysis complete and logged to sheet");
}
```

Replace `YOUR_FOLDER_ID_HERE` with your actual folder ID.

**Important**: The script will automatically create the "Log" sheet if it doesn't exist. You don't need to create it manually.

### Step 4: Run the Analysis

1. Save your script
2. Select the `runAnalysis` function from the dropdown
3. Click the Run button
4. Authorize the script when prompted
5. Check your spreadsheet for the results in the "Log" sheet (the script will create this sheet if needed)

## Library Functions

### analyzeFolder(folderId, config)
Analyzes all files in a folder and returns detailed information.

Parameters:
- `folderId`: String - The ID of the folder to analyze
- `config`: Object - Optional configuration settings

Returns:
- `filesAnalyzed`: Number of files found
- `fileTypeBreakdown`: Object showing count of each file type
- `fileAgeDistribution`: Object showing files by age
- `potentialDuplicates`: Array of duplicate file pairs
- `organizationInsights`: Array of insights about the files
- `recommendations`: Array of organization suggestions

### findFiles(folderId, criteria)
Searches for files matching specific criteria without moving them.

Parameters:
- `folderId`: String - The ID of the folder to search
- `criteria`: Object - Search criteria with properties:
  - `fileTypes`: Array of file extensions to find
  - `namePatterns`: Array of text patterns to match in filenames
  - `sizeRange`: Object with min and max size values

Returns:
- Array of file objects matching the criteria

### Example: Find All PDF Files

```javascript
function findPDFs() {
  const criteria = {
    fileTypes: [".pdf"]
  };
  
  const pdfFiles = DriveFileAnalyzer.findFiles(FOLDER_ID, criteria);
  
  console.log("Found " + pdfFiles.length + " PDF files");
  
  for (let i = 0; i < pdfFiles.length; i++) {
    console.log("- " + pdfFiles[i].name + " (" + pdfFiles[i].size + " bytes)");
  }
}
```

### Example: Find Files with Specific Terms

```javascript
function findInvoices() {
  const criteria = {
    fileTypes: [".pdf", ".doc", ".docx"],
    namePatterns: ["invoice", "bill", "payment"]
  };
  
  const invoiceFiles = DriveFileAnalyzer.findFiles(FOLDER_ID, criteria);
  
  console.log("Found " + invoiceFiles.length + " invoice-related files");
}
```

## Understanding the Results

The analysis provides several types of information:

###FileTypeBreakdown
Shows how many files of each type exist in your folder. For example:
- `.pdf`: 15 files
- `.jpg`: 42 files
- `.xlsx`: 8 files

### FileAgeDistribution
Shows how old your files are:
- "over 1 year": Files created more than a year ago
- "6-12 months": Files created 6-12 months ago
- "31-90 days": Files created 1-3 months ago
- "0-30 days": Recently created files

### PotentialDuplicates
Lists files that may be duplicates based on name and size. This helps identify files you might want to remove.

### Recommendations
Provides suggestions for improving organization based on the analysis.

## Important Notes

- This library does not move, delete, or modify any files
- All analysis is read-only
- The library respects your Google Drive permissions
- Large folders may take longer to analyze
- Always backup important data before using any tool
- The library will automatically create the "Log" sheet when you run it. You don't need to create it manually.

## Troubleshooting

If you encounter errors:
1. Verify your folder ID is correct
2. Ensure you have access to the folder
3. Check that you've properly added the library
4. Make sure you've authorized the script to access your Drive and Sheets

The library is designed to provide insights about your files without making any changes to your Google Drive. The script will automatically create the "Log" sheet if it doesn't exist when you run it.
