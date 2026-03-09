# RT Recording Viewer

A popup window tool for analyzing recorded GTFS-RT trip performance data.

## Features

### Data Sources
1. **Current Recording (Local Memory)** - View data currently being recorded in the browser
2. **Load from GitHub** - Fetch previously exported recordings from `recordedRTData/` folder
3. **Upload JSON File** - Load any manually saved recording file

### Analysis Capabilities

#### On-Time Performance Tab
- **Top 10 Routes by Average Delay**: Shows routes with the highest average maximum delay per trip
- **Top 20 Stops by Average Delay**: Shows stops with the highest average delay across all trips

### Filtering
- Multi-select route filter with checkboxes
- All routes selected by default
- Use "Select All" / "Deselect All" for quick filtering

## Data Processing

### Delay Calculation
- **Delta** = Actual arrival time - Scheduled arrival time
- **Positive** (+) = Late (bus arrived after scheduled time)
- **Negative** (-) = Early (bus arrived before scheduled time)
- Format: `+HH:MM:SS` or `-HH:MM:SS`

### Aggregation Methods
- **Route delays**: Average of each trip's maximum delay
- **Stop delays**: Average of all stop delays at that location
- **Excluded**: Stops without both RT and scheduled data (delta = null)

### Performance
- Typical dataset: 3,000 trips × 20 stops = 60,000 records
- Processing time: ~1 second
- Memory overhead: ~1-2 MB additional

## GitHub File Scanning

The viewer automatically scans `recordedRTData/` folder for available recordings:
- Checks past 60 days (today backwards)
- Batches of 10 days at a time
- Stops if entire batch is empty (optimization)
- Uses HEAD requests to check file existence (no download until selected)

## Charts

Both charts use:
- Horizontal bar layout (easier to read labels)
- Gradient coloring (red = worst, green = best)
- Tooltips with detailed information:
  - Route chart: Average delay, trip count
  - Stop chart: Average delay, record count, stop name

## File Format

Expected JSON structure:
```json
{
  "recordedData": {
    "tripId": {
      "rid": "501",
      "vid": "1234",
      "stops": {
        "1": {
          "sid": "9516",
          "seq": 1,
          "arr": 1709923980,
          "sch_arr": "17:02:36",
          "sch_dep": null
        }
      }
    }
  },
  "scheduledTimesCache": { ... },
  "exportedAt": 1709923980000
}
```

## Usage Tips

1. **First time use**: Start with "Current Recording" to test with live data
2. **Historical analysis**: Use "Load from GitHub" for day-to-day comparisons
3. **Filter smartly**: Deselect subway routes if only interested in surface routes
4. **Watch tooltips**: Hover over bars for detailed statistics

## Troubleshooting

### "No recordings found"
- No files exist in `recordedRTData/` folder yet
- Wait for automated daily export (8 AM UTC) or run manual GitHub Action

### "Recording data is empty"
- Start recording first in the main UI
- Wait for some trips to be captured

### "Invalid file format"
- Ensure uploaded JSON has required `recordedData` key
- Check file wasn't corrupted during download

### Charts not displaying
- All selected routes have no data matching criteria
- Try "Select All" routes or adjust date selection

## Technical Details

- **Timezone**: All times calculated in America/Toronto timezone
- **Stop names**: Loaded from `data/stops.json` (must be available)
- **Browser popup**: Opens in new window (allow popups if blocked)
- **Chart library**: Uses Chart.js (already loaded from main page)

## Keyboard Shortcuts

None currently implemented. Feature for future enhancement.

## Future Enhancements

Potential additions:
- Time-of-day distribution charts
- Route comparison overlay
- Export filtered results to CSV
- Click-through to trip details
- Historical trend graphs (multi-day comparison)
