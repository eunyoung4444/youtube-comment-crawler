# YouTube Comment Crawler & Preprocessor

A simple tool to crawl YouTube comments and preprocess them for analysis. This project consists of two main components: a comment crawler and a data preprocessor.

## Project Structure

```
youtube-comment-crawler/
├── comment_crawler.ipynb          # Main crawler notebook
├── preprocessing.ipynb            # Data preprocessing notebook
├── comments/                      # Output directory for crawled data
│   ├── {BATCH_NAME}/             # Raw crawled comments
│   └── {BATCH_NAME}_sampled/     # Processed and sampled data
├── crawling_progress_{BATCH_NAME}.csv  # Progress tracking file
└── README.md                     # This file
```

## Quick Start

### Prerequisites

1. **Python 3.7+** installed on your system
2. **Jupyter Notebook** or **JupyterLab**
3. **YouTube Data API v3** key

### Installation

1. Clone or download this repository
2. Install required packages:
   ```bash
   pip install pandas google-api-python-client langdetect
   ```

3. Get a YouTube Data API key:
   - Go to [Google Cloud Console](https://console.cloud.google.com/)
   - Create a new project or select existing one
   - Enable YouTube Data API v3
   - Create credentials (API Key)
   - Copy your API key

## Usage Guide

### Step 1: Crawl YouTube Comments

1. Open `comment_crawler.ipynb` in Jupyter Notebook
2. **Configure your settings** in the first cell:
   ```python
   # ===== Configuration Variables =====
   API_KEY = 'YOUR-API-KEY'  # Replace with your actual API key
   BATCH_NAME = 'my_batch'   # Name for this crawling session
   VIDEO_IDS = [             # List of YouTube video IDs to crawl
       'dQw4w9WgXcQ',        # Example: Rick Roll
       'jNQXAC9IVRw',        # Example: Me at the zoo
       # Add more video IDs here
   ]
   ```

3. **Run the crawler**:
   - Execute all cells in the notebook
   - The crawler will automatically:
     - Create output directories
     - Save progress to resume later if interrupted
     - Handle API quota limits gracefully

4. **Monitor progress**:
   - Check the progress file: `crawling_progress_{BATCH_NAME}.csv`
   - View real-time progress in the notebook output

### Step 2: Preprocess the Data

1. Open `preprocessing.ipynb` in Jupyter Notebook
2. **Configure preprocessing settings**:
   ```python
   # ===== Configuration Variables =====
   BATCH_NAME = 'my_batch'        # Same batch name from crawler
   MIN_REPLY_COUNT = 5           # Minimum replies to consider "popular"
   SAMPLE_LENGTH = 50            # Number of popular comments to sample
   ```

3. **Run the preprocessor**:
   - Execute all cells in the notebook
   - The preprocessor will automatically:
     - Process all CSV files in the batch
     - Filter English comments
     - Identify popular comments (with many replies)
     - Create sampled datasets

## Output Files

### Crawler Output
- **Raw comments**: `comments/{BATCH_NAME}/{video_title}_{video_id}.csv`
- **Progress tracking**: `crawling_progress_{BATCH_NAME}.csv`

### Preprocessor Output
- **Popular comments**: `comments/{BATCH_NAME}_sampled/{video_title}_popular.csv`
- **Sampled dataset**: `comments/{BATCH_NAME}_sampled/{video_title}_sampled{N}.csv`

## Configuration Options

### Crawler Settings
- `API_KEY`: Your YouTube Data API v3 key
- `BATCH_NAME`: Unique identifier for this crawling session
- `VIDEO_IDS`: List of YouTube video IDs to crawl

### Preprocessor Settings
- `BATCH_NAME`: Must match the crawler batch name
- `MIN_REPLY_COUNT`: Minimum number of replies to consider a comment "popular" (default: 5)
- `SAMPLE_LENGTH`: Number of popular comments to include in final dataset (default: 50)

## Advanced Usage

### Resuming Interrupted Crawling
The crawler automatically saves progress and can resume from where it left off:
- If interrupted by API quota limits, simply run the crawler again
- It will automatically continue from the last saved position

### Processing Multiple Batches
You can process multiple batches simultaneously:
1. Change `BATCH_NAME` to create separate batches
2. Each batch maintains its own progress and output files
3. Example: `january_2024`, `february_2024`, `test_batch`

### Customizing Preprocessing
Modify the preprocessing parameters based on your needs:
- **Lower `MIN_REPLY_COUNT`** (e.g., 3) for more comments
- **Higher `MIN_REPLY_COUNT`** (e.g., 10) for only very popular comments
- **Adjust `SAMPLE_LENGTH`** based on your analysis requirements

## Data Format

### Raw Comment Data
Each CSV file contains the following columns:
- `ID`: Unique comment identifier
- `Author`: Comment author's display name
- `AuthorID`: Comment author's channel ID
- `Comment`: Comment text content
- `LikeCount`: Number of likes
- `PublishedAt`: Publication timestamp
- `Depth`: Comment depth (0 = parent, 1+ = replies)
- `ParentID`: ID of parent comment (for replies)

### Processed Data
- **Popular comments**: All comments from threads with many replies
- **Sampled data**: Random sample of popular comment threads

## Important Notes

### API Quota Limits
- YouTube Data API has daily quota limits
- The crawler handles quota exceeded errors gracefully
- Large-scale crawling may require multiple days

### File Management
- Each batch creates its own directory structure
- Progress files help track crawling status
- Empty or corrupted files are automatically skipped

### Language Detection
- Only English comments are processed by default
- Uses `langdetect` library for language identification
- Non-English comments are filtered out during preprocessing

## Troubleshooting

### Common Issues

1. **"No CSV files found"**
   - Check that `BATCH_NAME` matches between crawler and preprocessor
   - Ensure the crawler has completed successfully

2. **"API quota exceeded"**
   - Wait 24 hours for quota reset
   - The crawler will automatically resume when run again

3. **"Missing required columns"**
   - Ensure the crawler completed successfully
   - Check that CSV files are not corrupted

4. **Empty files**
   - Some videos may have no comments or comments disabled
   - The preprocessor will skip these files automatically

### Getting Help
- Check the notebook output for detailed error messages
- Ensure all required packages are installed
- Verify your API key has proper permissions

## Example Workflow

1. **Setup**: Configure API key and video IDs in crawler
2. **Crawl**: Run crawler to collect comments (may take time due to API limits)
3. **Preprocess**: Run preprocessor to filter and sample data
4. **Analyze**: Use the processed data for your analysis

## Batch Processing Example

```python
# Crawler configuration
BATCH_NAME = 'vaccine_videos'
VIDEO_IDS = [
    'dQw4w9WgXcQ',
    'jNQXAC9IVRw',
    'oHg5SJYRHA0'
]

# Preprocessor configuration (same batch)
BATCH_NAME = 'vaccine_videos'
MIN_REPLY_COUNT = 5
SAMPLE_LENGTH = 100
```

This will create:
- `comments/vaccine_videos/` - Raw crawled data
- `comments/vaccine_videos_sampled/` - Processed data
- `crawling_progress_vaccine_videos.csv` - Progress tracking

## License

This project is open source. Please check the LICENSE file for details.
