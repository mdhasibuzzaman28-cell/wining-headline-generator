# Creator Hooks Scraper - Workflow Diagram

## How the Scraper Works

```
┌─────────────────────────────────────────────────────────────────┐
│                         START SCRAPER                            │
└────────────────────────────┬────────────────────────────────────┘
                             │
                             ▼
┌─────────────────────────────────────────────────────────────────┐
│  STEP 1: Initialize                                              │
│  • Load configuration (Spreadsheet ID, credentials)              │
│  • Set up HTTP client or browser                                 │
│  • Configure headers and rate limiting                           │
└────────────────────────────┬────────────────────────────────────┘
                             │
                             ▼
┌─────────────────────────────────────────────────────────────────┐
│  STEP 2: Scrape Listing Pages                                    │
│  • Start at page 1                                               │
│  • Extract all post URLs from current page                       │
│  • Check if "Next" button exists                                 │
│  • Repeat until page 19 or no more pages                         │
│                                                                   │
│  URLs collected: ~180-190 posts                                  │
└────────────────────────────┬────────────────────────────────────┘
                             │
                             ▼
┌─────────────────────────────────────────────────────────────────┐
│  STEP 3: Scrape Individual Posts (Loop through all URLs)         │
│                                                                   │
│  For each post URL:                                              │
│  ┌───────────────────────────────────────────────────────────┐  │
│  │ a) Visit the post page                                     │  │
│  │ b) Find .entry-content section                             │  │
│  │ c) Locate all H1/H2 headings (hook sections)               │  │
│  │ d) For each section:                                       │  │
│  │    • Extract Section Title (heading text)                  │  │
│  │    • Look for "Title:" → Framework                         │  │
│  │    • Look for "Framework:" → Framework                     │  │
│  │    • Look for "Hook score" → Extract number                │  │
│  │    • Look for "Why this works:" → Extract explanation      │  │
│  │ e) Skip ads and "Flop of the Week"                         │  │
│  │ f) Store all hook data in array                            │  │
│  └───────────────────────────────────────────────────────────┘  │
│                                                                   │
│  Wait 1.5 seconds between posts (rate limiting)                  │
└────────────────────────────┬────────────────────────────────────┘
                             │
                             ▼
┌─────────────────────────────────────────────────────────────────┐
│  STEP 4: Prepare Data                                            │
│  • Combine all hooks from all posts                              │
│  • Format into rows with columns:                                │
│    [Post URL | Section Title | Framework | Hook Score | Why]    │
│  • Add header row                                                │
│                                                                   │
│  Total hooks collected: ~400-500                                 │
└────────────────────────────┬────────────────────────────────────┘
                             │
                             ▼
┌─────────────────────────────────────────────────────────────────┐
│  STEP 5: Save to Google Sheets                                   │
│  • Authenticate with service account                             │
│  • Clear existing data in Sheet1                                 │
│  • Write headers + all rows                                      │
│  • Handle errors → fallback to CSV                               │
└────────────────────────────┬────────────────────────────────────┘
                             │
                             ▼
┌─────────────────────────────────────────────────────────────────┐
│                         COMPLETE! ✓                              │
│  Data available in Google Sheets or CSV file                     │
└─────────────────────────────────────────────────────────────────┘
```

## Data Flow Example

```
Newsletter Post
├── "Dun Dun Dun" (Section Title)
│   ├── Title: Something's Going Seriously Wrong in France
│   ├── Framework: Something's Going Seriously Wrong in (Entity)
│   ├── Hook Score: +320
│   └── Why this works: Negativity – Drama like this...
│
├── "New Year New Tools" (Section Title)
│   ├── Title: Graphic Design in 2026...
│   ├── Framework: (Niche) in (Current year)...
│   ├── Hook Score: +404
│   └── Why this works: Timeliness – Dropping in...
│
└── [More sections...]

                    ↓
                    
Google Sheet Row 1: [URL | Dun Dun Dun | Something's Going... | +320 | Negativity...]
Google Sheet Row 2: [URL | New Year... | (Niche) in... | +404 | Timeliness...]
```

## Component Comparison

### Puppeteer Version (scraper.js)
```
Browser Launch → Navigate → Execute JS → Parse DOM → Extract Data
     ⚠️ Heavy        ⚠️ Slow      ✓ Reliable    ✓ Robust
```

### Cheerio Version (scraper-light.js) ⭐ RECOMMENDED
```
HTTP Request → Parse HTML → Query Elements → Extract Data
   ✓ Fast       ✓ Light      ✓ Simple       ✓ Efficient
```

## Rate Limiting Strategy

```
Pages: [1] ──1s──> [2] ──1s──> [3] ──1s──> ...
                              
Posts: [P1] ──1.5s──> [P2] ──1.5s──> [P3] ──1.5s──> ...

Reason: Respectful to server, prevents blocking
```

## Error Handling Flow

```
Try Scrape Post
    │
    ├─ Success → Store data
    │
    └─ Error → Log error → Continue to next post
              (Don't stop entire scraper)

Try Save to Google Sheets
    │
    ├─ Success → Done! ✓
    │
    └─ Error → Fallback to CSV → Done! ✓
```

## Time Estimates

```
Small Test (2 pages):
  • ~20 posts
  • ~100 hooks
  • Time: ~1-2 minutes

Medium Run (10 pages):
  • ~100 posts
  • ~250 hooks
  • Time: ~5-8 minutes

Full Run (19 pages):
  • ~190 posts
  • ~500 hooks
  • Time: ~10-15 minutes
```

## System Requirements

```
Minimum:
• Node.js 16+
• 512MB RAM
• Internet connection

Recommended:
• Node.js 18+
• 1GB RAM
• Stable internet
• Linux/Mac/Windows

For Puppeteer:
• +500MB for Chromium
• +1GB RAM
```
