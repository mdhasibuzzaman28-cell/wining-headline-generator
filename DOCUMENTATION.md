# Creator Hooks Scraper - Complete Documentation

## 📁 Project Structure

```
creator-hooks-scraper/
├── scraper.js              # Full Puppeteer-based scraper
├── scraper-light.js        # Lightweight Cheerio-based scraper (RECOMMENDED)
├── test-scraper.js         # Test script for single post
├── package.json            # Dependencies and scripts
├── .gitignore             # Git ignore rules
├── .env.example           # Environment variable template
├── README.md              # Full documentation
├── QUICKSTART.md          # Quick setup guide
└── credentials.json       # Google service account key (you create this)
```

## 🎯 What This Scraper Does

Extracts data from Creator Hooks newsletters and saves to Google Sheets:

**Data Extracted:**
1. **Post URL** - Link to the newsletter
2. **Section Title** - The creative section heading
3. **Framework** - The title framework/template
4. **Hook Score** - The numerical score
5. **Why This Works** - Explanation of effectiveness

## 🚀 Getting Started

### Prerequisites
- Node.js v16 or higher
- Google account with access to Google Sheets
- Google Cloud project (free tier is fine)

### Installation

1. **Clone/Download the project**

2. **Install dependencies:**
```bash
npm install
```

3. **Set up Google Sheets API** (detailed steps in QUICKSTART.md)
   - Create Google Cloud project
   - Enable Google Sheets API
   - Create service account
   - Download credentials.json
   - Share your sheet with service account email

4. **Configure the scraper:**
```javascript
// In scraper-light.js or scraper.js
const SPREADSHEET_ID = 'your_actual_spreadsheet_id';
```

5. **Run:**
```bash
npm run start:light  # Recommended: Fast Cheerio version
# OR
npm start           # Full browser version with Puppeteer
```

## 📊 Output Format

### Google Sheets
Data is written to Sheet1 with these columns:

| Post URL | Section Title | Framework | Hook Score | Why This Works |
|----------|--------------|-----------|------------|----------------|
| https://... | Dun Dun Dun | Something's Going Seriously Wrong in (Entity) | +320 | Negativity – Drama like this... |

### CSV Fallback
If Google Sheets fails, data auto-saves to `creator-hooks-data.csv`

## 🔧 Scripts

```bash
npm run start:light  # Run lightweight Cheerio scraper (recommended)
npm start           # Run full Puppeteer scraper
npm test            # Test scraping on single post
```

## 🎛️ Configuration Options

### Change Maximum Pages
```javascript
// In scraper-light.js or scraper.js
while (hasMorePages && currentPage <= 19) {
  // Change 19 to desired maximum
}
```

### Adjust Rate Limiting
```javascript
// Delay between pages
await this.delay(1000);  // Change milliseconds

// Delay between posts
await this.delay(1500);  // Change milliseconds
```

### Change Sheet Range
```javascript
// In saveToGoogleSheet() method
range: 'Sheet1!A:E',  // Modify as needed
```

## 🐛 Troubleshooting

### Common Issues

**1. "Cannot find module 'puppeteer'"**
```bash
npm install  # Run this in project directory
```

**2. "Credentials not found"**
- Ensure `credentials.json` is in project root
- Check JSON format is valid
- Verify file permissions

**3. "Permission denied" on Google Sheet**
- Open credentials.json
- Find "client_email" field
- Share your Google Sheet with this email
- Grant Editor permissions

**4. "Request failed with status 403"**
- Website might be blocking requests
- Increase delays between requests
- Check User-Agent header

**5. No data extracted**
- Run test script: `npm test`
- Check console for specific errors
- Website structure may have changed

**6. Timeout errors**
- Increase timeout in page.goto() calls
- Check internet connection
- Try during off-peak hours

### Debug Mode

Add logging to see what's happening:
```javascript
console.log('Current page:', currentPage);
console.log('Found hooks:', hooks);
```

## 📝 Code Examples

### Extract Just One Page
```javascript
// Modify scrapeAllPages() method
async scrapeAllPages() {
  const { postUrls } = await this.scrapeListingPage(1); // Just page 1
  
  for (const url of postUrls) {
    const hooks = await this.scrapePost(url);
    // ... rest of code
  }
}
```

### Add Custom Fields
```javascript
// In scrapePost() method, add:
if (text.startsWith('Custom Field:')) {
  customField = text.replace('Custom Field:', '').trim();
}

// Then include in return object:
hooks.push({
  sectionTitle,
  framework,
  hookScore,
  whyThisWorks,
  customField  // New field
});
```

### Save to Multiple Sheets
```javascript
await sheets.spreadsheets.values.update({
  spreadsheetId: SPREADSHEET_ID,
  range: 'Hooks!A1',  // Different sheet name
  valueInputOption: 'RAW',
  resource: { values: [headers, ...rows] },
});
```

## 🔒 Security Best Practices

1. **Never commit credentials.json** - It's in .gitignore
2. **Restrict service account permissions** - Only Sheets access needed
3. **Use environment variables** for production:
```javascript
const SPREADSHEET_ID = process.env.SPREADSHEET_ID;
```
4. **Rate limit requests** - Respect website servers
5. **Handle errors gracefully** - Don't expose sensitive info in logs

## 🚀 Performance Tips

1. **Use scraper-light.js** - 5-10x faster than Puppeteer
2. **Limit concurrent requests** - Avoid overwhelming servers
3. **Cache results** - Don't re-scrape same data
4. **Use CSV fallback** - Faster than Google Sheets API
5. **Process in batches** - Split large jobs

## 📈 Scaling Up

### For Large Datasets (1000+ posts)

1. **Batch Processing:**
```javascript
const BATCH_SIZE = 50;
for (let i = 0; i < postUrls.length; i += BATCH_SIZE) {
  const batch = postUrls.slice(i, i + BATCH_SIZE);
  // Process batch
  await saveBatchToSheet(batch);
}
```

2. **Resume Capability:**
```javascript
// Save progress to file
await fs.writeFile('progress.json', JSON.stringify({ lastPage }));
```

3. **Parallel Processing:**
```javascript
const chunks = chunkArray(postUrls, 5);
await Promise.all(chunks.map(chunk => procesChunk(chunk)));
```

## 🤝 Contributing

To modify the scraper:
1. Test on single post first: `npm test`
2. Update both scraper versions if needed
3. Document changes in README
4. Test with various edge cases

## 📜 License

MIT License - Feel free to modify and use for your projects

## ⚠️ Legal Disclaimer

This tool is for educational purposes. Always:
- Check website Terms of Service
- Respect robots.txt
- Use reasonable rate limiting
- Don't overload servers
- Obtain permission if commercial use

## 🆘 Support

If you encounter issues:
1. Check this documentation
2. Run test script: `npm test`
3. Review console logs
4. Check website hasn't changed structure
5. Verify Google Sheets setup

## 🔄 Updates

When Creator Hooks changes their site structure:
1. Run test script to identify broken selectors
2. Update jQuery/Cheerio selectors in scrapePost()
3. Test on single post before full run
4. Update documentation

## 💡 Tips for Success

- **Start small** - Test with 1-2 pages first
- **Check output early** - Verify data after first few posts
- **Use test script** - Debug issues quickly
- **Save credentials safely** - Never share or commit
- **Respect rate limits** - Be a good internet citizen
- **Backup data** - CSV fallback is your friend
