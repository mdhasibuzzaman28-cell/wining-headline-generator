# Creator Hooks Newsletter Scraper

A Node.js web scraper that extracts data from Creator Hooks newsletters and saves it to Google Sheets.

## Features

- Scrapes all paginated newsletter posts
- Extracts: Title, Framework, Hook Score, Why This Works
- Automatically saves to Google Sheets
- Falls back to CSV if Google Sheets fails
- Rate limiting to be respectful to the server
- Progress logging

## Prerequisites

- Node.js (v16 or higher)
- A Google Cloud project with Sheets API enabled
- Service account credentials

## Setup Instructions

### 1. Install Dependencies

```bash
npm install
```

### 2. Set Up Google Sheets API

#### Step 1: Create a Google Cloud Project
1. Go to [Google Cloud Console](https://console.cloud.google.com/)
2. Create a new project or select an existing one
3. Enable the Google Sheets API:
   - Go to "APIs & Services" > "Library"
   - Search for "Google Sheets API"
   - Click "Enable"

#### Step 2: Create Service Account
1. Go to "APIs & Services" > "Credentials"
2. Click "Create Credentials" > "Service Account"
3. Fill in the details and click "Create"
4. Skip optional steps and click "Done"

#### Step 3: Create and Download Key
1. Click on the service account you just created
2. Go to "Keys" tab
3. Click "Add Key" > "Create New Key"
4. Choose JSON format
5. Download the file and rename it to `credentials.json`
6. Place it in the same directory as `scraper.js`

#### Step 4: Create Google Sheet
1. Create a new Google Sheet
2. Copy the Spreadsheet ID from the URL:
   - URL format: `https://docs.google.com/spreadsheets/d/{SPREADSHEET_ID}/edit`
3. Share the sheet with your service account email:
   - Find the email in `credentials.json` (client_email field)
   - Share the sheet with this email as an Editor

#### Step 5: Update Configuration
Open `scraper.js` and update:
```javascript
const SPREADSHEET_ID = 'YOUR_SPREADSHEET_ID'; // Replace with your actual ID
```

### 3. Run the Scraper

```bash
npm start
```

## Output

### Google Sheets Format
The scraper will create the following columns:
- **Post URL**: Link to the newsletter post
- **Section Title**: The section heading (e.g., "Dun Dun Dun")
- **Framework**: The title framework used
- **Hook Score**: The hook score value
- **Why This Works**: Explanation of why the hook is effective

### CSV Fallback
If Google Sheets integration fails, data will be saved to `creator-hooks-data.csv`

## How It Works

1. **Scrape Listing Pages**: Navigates through all paginated newsletter pages
2. **Extract Post URLs**: Collects all individual post links
3. **Visit Each Post**: Opens each post and extracts hook data
4. **Parse Content**: Extracts Title, Framework, Hook Score, and Why This Works sections
5. **Save to Google Sheets**: Writes all data to the configured spreadsheet

## Rate Limiting

The scraper includes built-in delays:
- 1 second between listing pages
- 1.5 seconds between individual posts

This ensures we're respectful to the server.

## Customization

### Change Maximum Pages
Edit the page limit in `scraper.js`:
```javascript
while (hasMorePages && currentPage <= 19) { // Change 19 to your desired max
```

### Change Output Range
Modify the Google Sheets range:
```javascript
range: 'Sheet1!A:E', // Change to your desired range
```

### Add More Fields
To extract additional data, modify the `scrapePost` method in `scraper.js`.

## Troubleshooting

### "Credentials not found" error
- Ensure `credentials.json` is in the correct location
- Check the file has proper JSON format

### "Permission denied" error
- Make sure you've shared the Google Sheet with the service account email
- Verify the service account has Editor permissions

### No data scraped
- Check your internet connection
- The website structure may have changed
- Check console logs for specific errors

### Timeout errors
- Increase timeout values in `page.goto()` options
- Your internet connection may be slow

## Notes

- The scraper skips "Flop of the Week" sections
- Advertisement sections are automatically filtered out
- Some posts may have multiple hooks - each is saved as a separate row

## License

MIT

## Disclaimer

This tool is for educational purposes. Always respect website terms of service and robots.txt files. Use responsibly and avoid overloading servers.
