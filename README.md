# Constant Contact Google Sheets Unsubscriber

[![Python 3.8+](https://img.shields.io/badge/python-3.8+-blue.svg)](https://www.python.org/downloads/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

[![PyPI version](https://badge.fury.io/py/constant-contact-sheets-unsubscriber.svg)](https://badge.fury.io/py/constant-contact-sheets-unsubscriber)

A Python package for automatically unsubscribing emails from Constant Contact using Google Sheets as the data source. Perfect for automating email list management and compliance.

## ✨ Features

- 🔄 **Real-time monitoring** of Google Sheets for new emails
- 📊 **Smart tracking** - only processes new emails, ignores already processed ones
- 📝 **Comprehensive logging** with timestamps
- 🔧 **PM2 support** for production deployment
- 🛡️ **Rate limiting** to respect API limits
- ⚙️ **Configurable** via environment variables
- 🚀 **Easy installation** via pip

## 📦 Installation

### From PyPI (Recommended)

```bash
pip install constant-contact-sheets-unsubscriber
```

### From GitHub Releases

```bash
# Download the latest .whl file from releases and install
pip install https://github.com/juliosalvat/constant-contact-sheets-unsubscriber/releases/latest/download/constant_contact_sheets_unsubscriber-1.0.1-py3-none-any.whl
```

### From Source

```bash
git clone https://github.com/juliosalvat/constant-contact-sheets-unsubscriber.git
cd constant-contact-sheets-unsubscriber
pip install -e .
```

## 🔧 Quick Setup

### 1. Constant Contact API Setup

1. Create a developer account at [Constant Contact Developer Portal](https://developer.constantcontact.com/)
2. Create an API application to get your credentials
3. Generate access and refresh tokens

### 2. Google Sheets API Setup

1. Go to [Google Cloud Console](https://console.cloud.google.com/)
2. Create a new project or select existing one
3. Enable the Google Sheets API
4. Create a Service Account and download the credentials JSON file
5. Share your Google Spreadsheet with the service account email

### 3. Environment Configuration

Copy the example environment file and fill in your credentials:

```bash
cp .env.example .env
# Edit .env with your actual credentials
```

Required environment variables:

```bash
CONSTANT_CONTACT_API_KEY=your_api_key
CONSTANT_CONTACT_ACCESS_TOKEN=your_access_token
CONSTANT_CONTACT_REFRESH_TOKEN=your_refresh_token
CONSTANT_CONTACT_CLIENT_SECRET=your_client_secret
CONSTANT_CONTACT_REDIRECT_URI=https://localhost
GOOGLE_CREDENTIALS_FILE=credentials.json
```

## 🚀 Usage

### Command Line Interface

```bash
# Initialize - mark all existing emails as processed (first run only)
cc-unsubscriber --spreadsheet-id "your_sheet_id" --range "Sheet1!B:B" --initialize

# Process new emails
cc-unsubscriber --spreadsheet-id "your_sheet_id" --range "Sheet1!B:B"

# Start continuous monitoring
cc-monitor --spreadsheet-id "your_sheet_id" --range "Sheet1!B:B"
```

### Python API

```python
from constant_contact_unsubscriber import ConstantContactUnsubscriber, Config

# Initialize configuration
config = Config()

# Create unsubscriber instance
unsubscriber = ConstantContactUnsubscriber(config)

# Process emails from Google Sheets
unsubscriber.process_sheet(
    spreadsheet_id="your_spreadsheet_id",
    range_name="Sheet1!B:B",
    initialize_mode=False  # Set to True for first run
)
```

## 📊 Google Sheets Format

Your Google Spreadsheet should have emails in a single column:

| A    | B (Email Address) | C     |
| ---- | ----------------- | ----- |
| Date | user1@example.com | Notes |
| Date | user2@example.com | Notes |
| Date | user3@example.com | Notes |

- **Column B**: Email addresses to unsubscribe
- **Range**: Use `Sheet1!B:B` to read all emails from column B

## 🔄 Production Deployment

### With PM2 (Recommended)

```bash
# Install PM2
npm install -g pm2

# Start the monitor
pm2 start cc-monitor --name "unsubscribe-monitor" -- --spreadsheet-id "your_id" --range "Sheet1!B:B"

# Save PM2 configuration
pm2 save
pm2 startup
```

### With Docker

```dockerfile
FROM python:3.11-slim

WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt

COPY . .
CMD ["cc-monitor", "--spreadsheet-id", "your_id", "--range", "Sheet1!B:B"]
```

## 📈 Monitoring

The package creates several files for tracking and logging:

- `processed_emails.json` - Tracks which emails have been processed
- `unsubscribe_success_log.txt` - Log of successful unsubscribes with timestamps
- Application logs via Python logging module

### Monitoring Commands

```bash
# Check total unsubscribes
wc -l unsubscribe_success_log.txt

# View recent activity
tail -f unsubscribe_success_log.txt

# Check processed count
python -c "import json; print(f'Processed: {len(json.load(open(\"processed_emails.json\")))}')"
```

## ⚙️ Configuration Options

| Environment Variable | Default | Description |
| -------------------- | ------- | ----------- |

| `RATE_LIMIT_SECONDS`
