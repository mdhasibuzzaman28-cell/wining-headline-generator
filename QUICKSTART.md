# Quick Start Guide

## 🚀 Two Scraper Options

### Option 1: Puppeteer Version (scraper.js)
- **Pros**: More robust, handles JavaScript-heavy sites
- **Cons**: Heavier, slower, requires Chrome/Chromium
- **Use when**: Site heavily relies on JavaScript

### Option 2: Cheerio Version (scraper-light.js) ⭐ RECOMMENDED
- **Pros**: Faster, lighter, uses less memory
- **Cons**: Doesn't handle JavaScript
- **Use when**: Site content is in HTML (like Creator Hooks)

## Setup in 5 Minutes

### 1. Install Dependencies
```bash
npm install
```

### 2. Get Google Sheets Credentials

**Quick Steps:**
1. Go to https://console.cloud.google.com/
2. Create/select project
3. Enable "Google Sheets API"
4. Create Service Account
5. Download JSON key as `credentials.json`
6. Place in project folder

### 3. Prepare Your Google Sheet

1. Create a new Google Sheet
2. Copy the ID from URL: 
   ```
   https://docs.google.com/spreadsheets/d/{THIS_IS_THE_ID}/edit
   ```
3. Open `credentials.json` and find `client_email`
4. Share your sheet with that email (Editor access)

### 4. Update Configuration

Edit `scraper-light.js` (or `scraper.js`):
```javascript
const SPREADSHEET_ID = 'paste_your_id_here';
```

### 5. Run the Scraper

**Recommended (faster):**
```bash
npm run start:light
```

**Or full browser version:**
```bash
npm start
```

## Expected Output

The scraper will:
1. Visit all newsletter archive pages
2. Extract each post URL
3. Visit each post
4. Extract hook data (Title, Framework, Hook Score, Why This Works)
5. Save everything to your Google Sheet

### Sample Output:
```
Scraping listing page 1: https://creatorhooks.com/past-creator-hooks-newsletters/
Found 10 posts on page 1

Processing post 1/10
Scraping post: https://creatorhooks.com/...

Total hooks collected: 47
✓ Successfully saved 47 hooks to Google Sheet!
```

## Troubleshooting

### "Cannot find credentials.json"
- Make sure the file is in the same folder as scraper.js
- Check filename is exactly `credentials.json`

### "Permission denied" on Google Sheet
- Share the sheet with the service account email
- Grant Editor permissions

### No data appearing
- Check console for error messages
- Try the Puppeteer version if Cheerio fails
- Website might be blocking requests (add delays)

### Still stuck?
Check the full README.md for detailed troubleshooting.

## Tips

- Start with a small test: Comment out the page limit to scrape just 1-2 pages first
- Check your Google Sheet after a few posts to verify data format
- The scraper auto-saves to CSV if Google Sheets fails
