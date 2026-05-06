# URL Shortener

A powerful, production-ready URL shortener application built with Python, Flask, and MySQL. Includes AI-powered malicious URL detection for enhanced security.

## ✨ Features

- 🔗 **URL Shortening** - Convert long URLs to compact, shareable short links
- 🎯 **Custom Aliases** - Create memorable custom short codes
- 📊 **Analytics** - Track clicks, access times, and usage statistics
- 🛡️ **AI Malicious Detection** - Detect phishing and suspicious URLs in real-time
- ⏰ **URL Expiration** - Set expiration dates for temporary links
- 🔍 **QR Code Support** - Generate QR codes for shortened URLs
- 📱 **RESTful API** - Clean, well-documented API endpoints
- 🧪 **Unit Tests** - Comprehensive test coverage
- 🐳 **Docker Support** - Easy deployment with Docker
- 🚀 **Production Ready** - Logging, error handling, and security best practices

## 🛠️ Tech Stack

- **Backend**: Python 3.8+
- **Framework**: Flask 2.0+
- **Database**: MySQL 8.0+
- **ORM**: SQLAlchemy
- **Testing**: Pytest
- **Containerization**: Docker & Docker Compose
- **API Docs**: Swagger/OpenAPI

## 📦 Project Structure

```
URL-shortner/
├── app.py                    # Main Flask application
├── config.py                 # Configuration settings
├── requirements.txt          # Python dependencies
├── .env.example              # Environment variables template
├── .gitignore                # Git ignore patterns
├── Dockerfile                # Docker configuration
├── docker-compose.yml        # Multi-container setup
├── README.md                 # This file
├── SETUP.md                  # Detailed setup guide
│
├── database/
│   ├── __init__.py
│   └── db.py                 # Database initialization
│
├── models/
│   ├── __init__.py
│   └── url.py                # SQLAlchemy URL model
│
├── routes/
│   ├── __init__.py
│   ├── shorten.py            # URL shortening endpoints
│   ├── redirect.py           # Redirect endpoints
│   └── analytics.py          # Analytics endpoints
│
├── utils/
│   ├── __init__.py
│   ├── shortener.py          # URL shortening logic
│   ├── validators.py         # Input validation
│   ├── malicious_detector.py # AI-powered threat detection
│   └── qr_generator.py       # QR code generation
│
├── tests/
│   ├── __init__.py
│   ├── test_routes.py        # Route tests
│   ├── test_shortener.py     # Shortener logic tests
│   └── test_malicious.py     # Malicious URL detection tests
│
└── static/
    ├── index.html            # Web UI
    ├── style.css             # Styling
    └── script.js             # Frontend logic
```

## 🚀 Quick Start

### Option 1: Docker (Recommended)

```bash
# Clone the repository
git clone https://github.com/hemu-08/URL-shortner.git
cd URL-shortner

# Start with Docker Compose
docker-compose up -d

# Application runs at http://localhost:5000
```

### Option 2: Manual Setup

```bash
# Clone the repository
git clone https://github.com/hemu-08/URL-shortner.git
cd URL-shortner

# Create virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt

# Configure environment
cp .env.example .env
# Edit .env with your MySQL credentials

# Initialize database
python -c "from app import app, db; app.app_context().push(); db.create_all()"

# Run the application
python app.py
```

## 💡 API Endpoints

### 1. Create Shortened URL
**POST** `/api/shorten`

```bash
curl -X POST http://localhost:5000/api/shorten \
  -H "Content-Type: application/json" \
  -d '{
    "original_url": "https://github.com/hemu-08",
    "custom_alias": "mylink",
    "expiration_days": 30
  }'
```

**Response**:
```json
{
  "success": true,
  "short_url": "http://localhost:5000/abc123",
  "short_code": "abc123",
  "original_url": "https://github.com/hemu-08",
  "created_at": "2026-05-06T10:30:00Z",
  "malicious_score": 0.02
}
```

### 2. Redirect to Original URL
**GET** `/<short_code>`

Automatically redirects to the original URL and tracks analytics.

```bash
curl -L http://localhost:5000/abc123
```

### 3. Get URL Analytics
**GET** `/api/analytics/<short_code>`

```bash
curl http://localhost:5000/api/analytics/abc123
```

**Response**:
```json
{
  "short_code": "abc123",
  "original_url": "https://github.com/hemu-08",
  "clicks": 42,
  "created_at": "2026-05-06T10:30:00Z",
  "last_accessed": "2026-05-06T15:45:00Z",
  "is_malicious": false,
  "malicious_score": 0.02
}
```

### 4. Delete URL
**DELETE** `/api/urls/<short_code>`

```bash
curl -X DELETE http://localhost:5000/api/urls/abc123
```

### 5. List All URLs
**GET** `/api/urls`

```bash
curl http://localhost:5000/api/urls
```

### 6. Generate QR Code
**GET** `/api/qr/<short_code>`

```bash
curl http://localhost:5000/api/qr/abc123 > qrcode.png
```

## 🔐 Malicious URL Detection

The application includes AI-powered threat detection that analyzes URLs for:

- ✅ Phishing patterns
- ✅ Suspicious keywords (bank, verify, confirm, etc.)
- ✅ IP-based URLs
- ✅ Shortened URL chains
- ✅ Unknown domains
- ✅ Special characters and encoding

**Risk Scoring**: Each URL receives a malicious score (0.0-1.0)
- **0.0-0.3**: Safe
- **0.3-0.7**: Suspicious
- **0.7-1.0**: Likely Malicious

## 📋 Database Schema

### URLs Table
```sql
CREATE TABLE urls (
    id INT PRIMARY KEY AUTO_INCREMENT,
    original_url VARCHAR(2000) NOT NULL,
    short_code VARCHAR(20) UNIQUE NOT NULL,
    custom_alias VARCHAR(50) UNIQUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    expires_at DATETIME,
    clicks INT DEFAULT 0,
    last_accessed DATETIME,
    is_active BOOLEAN DEFAULT TRUE,
    is_malicious BOOLEAN DEFAULT FALSE,
    malicious_score FLOAT DEFAULT 0.0,
    user_agent TEXT,
    referrer TEXT,
    ip_address VARCHAR(45),
    created_by VARCHAR(255),
    INDEX (short_code),
    INDEX (custom_alias),
    INDEX (created_at)
);
```

## ⚙️ Configuration

Edit `config.py` to customize:

```python
# Database
DB_USER = 'root'
DB_PASSWORD = 'password'
DB_HOST = 'localhost'
DB_NAME = 'url_shortener'

# Short URL Settings
SHORT_CODE_LENGTH = 6
CHAR_SET = 'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789'

# Flask
DEBUG = False
SECRET_KEY = 'your-secret-key'

# Security
MAX_URL_LENGTH = 2000
MAX_CUSTOM_ALIAS_LENGTH = 50
DEFAULT_EXPIRATION_DAYS = None
```

## 🧪 Testing

Run the comprehensive test suite:

```bash
# Install test dependencies
pip install pytest pytest-cov

# Run all tests
pytest

# Run with coverage report
pytest --cov=. --cov-report=html
```

## 📊 Monitoring & Logging

All requests and operations are logged to `logs/app.log`:

```
2026-05-06 10:30:00 - INFO - URL shortened: abc123 -> https://github.com/hemu-08
2026-05-06 10:31:15 - INFO - Redirect access: abc123 from 192.168.1.100
2026-05-06 10:32:45 - WARNING - Malicious URL detected: score=0.85
```

## 🚢 Deployment

### Using Gunicorn

```bash
pip install gunicorn
gunicorn -w 4 -b 0.0.0.0:5000 app:app
```

### Using Docker

```bash
# Build image
docker build -t url-shortener .

# Run container
docker run -d -p 5000:5000 \
  -e DB_HOST=mysql \
  -e DB_USER=root \
  -e DB_PASSWORD=password \
  url-shortener
```

### Using Docker Compose

```bash
docker-compose up -d
```

## 🔒 Security Features

✅ Input validation and sanitization
✅ SQL injection prevention (SQLAlchemy ORM)
✅ XSS protection
✅ CSRF tokens
✅ Rate limiting
✅ Malicious URL detection
✅ HTTPS support in production
✅ Secure password hashing


