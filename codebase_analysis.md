# Changedetection.io Codebase Analysis

## Overview

**Changedetection.io** is a comprehensive web application designed for monitoring website changes and sending notifications when updates are detected. The application supports multiple content fetching methods, various notification channels, and advanced filtering capabilities.

## Architecture & Technology Stack

### Core Framework
- **Flask 2.3+**: Main web framework providing REST API and web interface
- **Python 3.10+**: Target Python version with modern language features
- **Flask-SocketIO**: Real-time communication for live updates
- **Flask-RESTful**: REST API implementation
- **Flask-WTF**: Form handling and CSRF protection

### Content Fetching
The application supports multiple content fetching backends:
- **Requests**: Fast HTTP client for simple web pages
- **Playwright**: Browser automation for JavaScript-heavy sites
- **Puppeteer**: Alternative browser automation
- **Selenium WebDriver**: Additional browser automation option

### Data Processing & Filtering
- **BeautifulSoup4 + lxml**: HTML parsing and CSS/XPath filtering
- **jsonpath-ng**: JSON data extraction
- **jq**: Advanced JSON processing (Linux/macOS)
- **regex**: Text pattern matching and extraction

### Notifications
- **Apprise 1.9.3**: Comprehensive notification library supporting 80+ services
- Supports Discord, Slack, Email, Telegram, Webhooks, and many more

### Storage & Persistence
- **JSON-based storage**: Main datastore in `url-watches.json`
- **Brotli compression**: Snapshot compression for storage efficiency
- **Threading**: Concurrent processing with proper synchronization

## Directory Structure Analysis

### Core Application (`changedetectionio/`)
- **`__init__.py`**: Main application entry point and version management
- **`flask_app.py`**: Flask application configuration and route definitions
- **`store.py`**: Data persistence and watch management
- **`worker_handler.py`**: Background processing coordination
- **`async_update_worker.py`**: Asynchronous update processing

### Models (`changedetectionio/model/`)
- **`App.py`**: Application-level configuration and settings
- **`Watch.py`**: Individual watch configuration and history management
- **`Tag.py`**: Tag system for organizing watches
- **`__init__.py`**: Base watch model with common properties

### Content Fetchers (`changedetectionio/content_fetchers/`)
- **`requests.py`**: HTTP-based content fetching
- **`playwright.py`**: Browser automation with Playwright
- **`puppeteer.py`**: Browser automation with Puppeteer
- **`webdriver_selenium.py`**: Selenium WebDriver integration
- **`screenshot_handler.py`**: Screenshot capture functionality

### Processors (`changedetectionio/processors/`)
- **`text_json_diff/`**: Standard text and JSON change detection
- **`restock_diff/`**: Specialized processor for product restock monitoring
- **`exceptions.py`**: Processor-specific error handling

### API Layer (`changedetectionio/api/`)
- **`Watch.py`**: CRUD operations for watches
- **`Tags.py`**: Tag management endpoints
- **`Notifications.py`**: Notification configuration
- **`SystemInfo.py`**: System status and information
- **`Search.py`**: Search functionality across watches

### Web Interface (`changedetectionio/blueprint/`)
- **`ui/`**: Main user interface components
- **`settings/`**: Configuration management
- **`watchlist/`**: Watch list display and management
- **`browser_steps/`**: Browser automation step configuration
- **`imports/`**: Data import functionality
- **`backups/`**: Backup and restore operations

### Notification System (`changedetectionio/notification/`)
- **`handler.py`**: Core notification processing
- **`apprise_plugin/`**: Apprise integration and custom handlers

## Key Features & Capabilities

### Change Detection
1. **Text-based comparison**: MD5 checksum comparison of content
2. **Visual diff**: Character-by-character change visualization
3. **JSON monitoring**: Structured data change detection
4. **PDF monitoring**: Text extraction from PDF files
5. **Conditional triggers**: Custom logic for when to trigger notifications

### Advanced Filtering
- **CSS Selectors**: Target specific page elements
- **XPath**: Complex XML/HTML navigation
- **JSONPath/jq**: JSON data extraction and filtering
- **Regex extraction**: Pattern-based text extraction
- **Include/Exclude filters**: Content filtering rules

### Browser Automation
- **Browser Steps**: Automated interaction sequences
- **Visual Selector**: Point-and-click element selection
- **Screenshot capture**: Visual change documentation
- **JavaScript execution**: Custom client-side processing

### Scheduling & Timing
- **Flexible intervals**: Customizable check frequencies
- **Timezone support**: Location-aware scheduling
- **Business hours**: Time-based restrictions
- **Jitter**: Randomized timing to avoid detection

## Data Model Analysis

### Watch Object Structure
Each watch contains extensive configuration:

```python
{
    'url': '',  # Target URL to monitor
    'uuid': str(uuid.uuid4()),  # Unique identifier
    'title': None,  # Human-readable name
    'tags': [],  # Associated tag UUIDs
    'processor': 'text_json_diff',  # Change detection processor
    'fetch_backend': 'system',  # Content fetching method
    'time_between_check': {...},  # Check frequency configuration
    'notification_urls': [],  # Where to send notifications
    'include_filters': [],  # Content filtering rules
    'browser_steps': [],  # Automated browser interactions
    'conditions': {},  # Conditional trigger logic
    # ... 50+ additional configuration options
}
```

### Storage Architecture
- **JSON datastore**: Main configuration in `url-watches.json`
- **Per-watch directories**: Individual watch data storage
- **History snapshots**: Compressed text snapshots with timestamps
- **Screenshot storage**: PNG images for visual comparison
- **Metadata tracking**: Comprehensive change history

## API Design

### REST Endpoints
- **Watches**: `/api/v1/watch/<uuid>` - CRUD operations
- **History**: `/api/v1/watch/<uuid>/history` - Snapshot access
- **Tags**: `/api/v1/tag/<uuid>` - Tag management
- **Search**: `/api/v1/search` - Content search
- **System**: `/api/v1/system/info` - System information

### Authentication
- **Token-based**: API key authentication
- **Session management**: Web interface authentication
- **CSRF protection**: Form security

## Testing Infrastructure

### Test Organization
- **Unit tests**: Individual component testing
- **Integration tests**: Full application testing
- **API tests**: REST endpoint validation
- **Browser tests**: Frontend functionality
- **Fetcher tests**: Content retrieval validation

### Test Categories
- **Authentication & authorization**
- **Change detection accuracy**
- **Notification delivery**
- **Import/export functionality**
- **Browser automation**
- **Performance and memory usage**

## Development Practices

### Code Quality
- **Ruff**: Python linting and formatting
- **Type hints**: Type annotations for better IDE support
- **Docstrings**: Comprehensive documentation
- **Error handling**: Robust exception management

### Security Considerations
- **Input validation**: Comprehensive request validation
- **Safe URL handling**: Protocol and content restrictions
- **CSRF protection**: Cross-site request forgery prevention
- **Secure headers**: Appropriate security headers

### Performance Optimizations
- **Compression**: Brotli compression for storage
- **Threading**: Concurrent processing architecture
- **Queue management**: Prioritized task processing
- **Memory management**: Garbage collection and cleanup

## Deployment & Operations

### Container Support
- **Docker**: Official container images
- **Docker Compose**: Multi-service deployment
- **Raspberry Pi**: ARM architecture support

### Configuration
- **Environment variables**: Runtime configuration
- **JSON configuration**: Persistent settings
- **Proxy support**: External proxy integration
- **SSL/TLS**: HTTPS support

## Strengths

1. **Comprehensive feature set**: Supports wide variety of monitoring scenarios
2. **Flexible architecture**: Multiple fetchers and processors
3. **Extensive API**: Full programmatic access
4. **Rich notification support**: 80+ notification services
5. **Active development**: Regular updates and community support
6. **Good documentation**: Comprehensive README and wiki

## Areas for Consideration

1. **Complexity**: Large codebase with many interdependent components
2. **Resource usage**: Browser automation can be resource-intensive
3. **Learning curve**: Extensive configuration options
4. **Platform dependencies**: Some features require specific platforms

## Data Storage Architecture Deep Dive

### Snapshot Storage Logic

The application maintains **two distinct types of compressed snapshots** with different retention policies and purposes:

#### Text Snapshots (`.txt.br` files)
- **Content**: Processed/filtered text content used for change detection and diffing
- **Storage**: Every snapshot is kept indefinitely in the watch's history
- **Naming Convention**: Uses MD5 hash of previous content as filename (`{snapshot_id}.txt.br`)
- **Compression**: Brotli compression with MODE_TEXT for optimal text compression
- **Purpose**: Core change detection, diff visualization, and notification content
- **Retention**: Unlimited - full history preserved

#### HTML Snapshots (`.html.br` files)
- **Content**: Raw HTML content fetched from the web page
- **Storage**: Only the most recent 2 snapshots are retained (automatic pruning)
- **Naming Convention**: Uses Unix timestamp as filename (`{timestamp}.html.br`)
- **Compression**: Brotli compression for HTML content
- **Purpose**: API access for raw HTML content and potential future UI features
- **Retention**: Limited to 2 most recent snapshots

#### Storage Conditions
Both snapshot types are saved under identical conditions:
```python
if changed_detected or not watch.history_n:  # First check OR change detected
    watch.save_history_text(contents=processed_text, ...)
    watch.save_last_fetched_html(contents=raw_html, ...)
```

#### Pruning Logic
HTML snapshots are automatically pruned to conserve disk space:
```python
def _prune_last_fetched_html_snapshots(self):
    dates = list(self.history.keys())
    dates.reverse()
    for index, timestamp in enumerate(dates):
        if index > 1:  # Keep only first 2
            os.remove(f"{timestamp}.html.br")
```

#### File Structure Example
```
datastore/{uuid}/
├── history.txt                    # Index of all snapshots
├── {timestamp1}.html.br          # Raw HTML (kept: 2 max)
├── {timestamp2}.html.br          # Raw HTML (kept: 2 max)
├── {md5_hash1}.txt.br            # Processed text (kept: all)
├── {md5_hash2}.txt.br            # Processed text (kept: all)
├── {md5_hash3}.txt.br            # Processed text (kept: all)
├── elements.deflate              # UI element data (zlib compressed)
├── favicon.png                   # Website favicon
└── last-screenshot.png           # Latest screenshot
```

#### Why Dual Snapshot System?
1. **Text snapshots**: Essential for change detection algorithms, smaller size, need full history for accurate diffing
2. **HTML snapshots**: Larger files, primarily for API access and potential UI features, only recent ones needed

#### Usage Patterns
- **Text content**: Powers diff views, change notifications, and history comparison
- **HTML content**: Available via API (`/api/v1/watch/{uuid}/history/{timestamp}?html=true`) for external tools

This design balances storage efficiency with comprehensive monitoring capabilities, ensuring that change detection remains fast and accurate while providing access to raw content when needed.

## Conclusion

Changedetection.io represents a sophisticated, feature-rich solution for website change monitoring. Its modular architecture, comprehensive API, and extensive customization options make it suitable for a wide range of monitoring scenarios from simple website tracking to complex automated workflows. The codebase demonstrates good software engineering practices with proper separation of concerns, comprehensive testing, and extensive documentation.