# AI News Aggregator

An intelligent news aggregation system that collects, processes, and delivers personalized AI-related content from multiple sources including YouTube channels, OpenAI blog, and Anthropic blog. The system uses AI agents to curate and summarize content, then sends personalized email digests based on user profiles.

## Description

The AI News Aggregator is an automated pipeline that:
- **Scrapes** content from multiple sources (YouTube channels, OpenAI blog RSS feeds, Anthropic blog)
- **Processes** raw content (transcripts, markdown) for analysis
- **Generates** AI-powered summaries and digests using OpenAI's GPT models
- **Curates** content based on personalized user profiles
- **Delivers** formatted email digests with ranked, relevant articles

The system is designed to help AI professionals stay up-to-date with the latest developments in the field by automatically collecting and filtering content based on their specific interests and expertise level.

## Project Structure

```
ai-news-aggregator/
├── app/
│   ├── agent/              # AI agents for content processing
│   │   ├── curator_agent.py    # Ranks articles based on user profile
│   │   ├── digest_agent.py     # Generates article summaries
│   │   └── email_agent.py      # Creates email digest content
│   ├── config.py           # Configuration (YouTube channels, etc.)
│   ├── daily_runner.py     # Main pipeline orchestrator
│   ├── runner.py           # Scraper orchestration
│   ├── database/           # Database layer
│   │   ├── connection.py   # PostgreSQL connection setup
│   │   ├── create_tables.py # Database schema creation
│   │   ├── models.py       # SQLAlchemy ORM models
│   │   └── repository.py   # Data access layer
│   ├── profiles/           # User profile management
│   │   └── user_profile.py # User profile definitions
│   ├── scrapers/           # Content scrapers
│   │   ├── youtube.py      # YouTube video scraper
│   │   ├── openai.py       # OpenAI blog RSS scraper
│   │   └── anthropic.py    # Anthropic blog scraper
│   └── services/           # Business logic services
│       ├── email.py        # Email sending functionality
│       ├── process_anthropic.py # Anthropic article processing
│       ├── process_curator.py  # Content curation service
│       ├── process_digest.py   # Digest generation service
│       ├── process_email.py    # Email digest service
│       └── process_youtube.py  # YouTube transcript processing
├── docker/
│   └── docker-compose.yml  # PostgreSQL database setup
├── main.py                 # Entry point
├── pyproject.toml          # Project dependencies
└── requirements.txt        # Python package requirements
```

## Implementation Overview

### 1. Content Scraping (`app/scrapers/`)

The system scrapes content from three main sources:

- **YouTube Scraper** (`youtube.py`): 
  - Fetches videos from configured YouTube channels via RSS feeds
  - Extracts video metadata (title, description, publish date)
  - Retrieves transcripts using YouTube Transcript API
  - Supports proxy configuration for transcript access

- **OpenAI Scraper** (`openai.py`):
  - Parses OpenAI blog RSS feed
  - Extracts article metadata and descriptions
  - Categorizes articles by topic

- **Anthropic Scraper** (`anthropic.py`):
  - Scrapes Anthropic blog articles
  - Downloads and processes markdown content
  - Extracts full article text for analysis

### 2. Data Storage (`app/database/`)

- **Models**: SQLAlchemy ORM models for:
  - `YouTubeVideo`: Video metadata and transcripts
  - `OpenAIArticle`: OpenAI blog articles
  - `AnthropicArticle`: Anthropic blog articles with markdown content
  - `Digest`: Generated summaries and digests

- **Repository**: Data access layer providing bulk operations for efficient database writes

- **Connection**: PostgreSQL database connection management using SQLAlchemy

### 3. Content Processing (`app/services/`)

- **Process YouTube**: Extracts and stores video transcripts
- **Process Anthropic**: Downloads and stores article markdown content
- **Process Digest**: Generates AI-powered summaries for articles using digest agents
- **Process Curator**: Ranks articles based on user profile relevance
- **Process Email**: Assembles and sends formatted email digests

### 4. AI Agents (`app/agent/`)

- **Curator Agent**: Uses OpenAI GPT-4 to rank articles by relevance to user profile
  - Analyzes user interests, background, and expertise level
  - Assigns relevance scores (0.0-10.0) and ranks articles
  - Provides reasoning for rankings

- **Digest Agent**: Generates concise summaries of articles
  - Creates structured summaries with key points
  - Formats content for email delivery

- **Email Agent**: Composes personalized email digests
  - Generates greeting and introduction
  - Formats articles with summaries and links
  - Creates HTML email templates

### 5. Pipeline Execution (`app/daily_runner.py`)

The main pipeline orchestrates the entire process:

1. **Scraping Phase**: Collects content from all sources
2. **Processing Phase**: Processes Anthropic markdown and YouTube transcripts
3. **Digest Phase**: Generates summaries for all articles
4. **Email Phase**: Curates top articles and sends personalized digest

## Setup

### Prerequisites

- Python 3.12+
- PostgreSQL database (can use Docker Compose)
- OpenAI API key
- Gmail account with app password (for email sending)

### Installation

1. Clone the repository:
```bash
git clone <repository-url>
cd ai-news-aggregator
```

2. Install dependencies:
```bash
pip install -r requirements.txt
```

3. Set up PostgreSQL database:
```bash
cd docker
docker-compose up -d
```

4. Create environment file:
```bash
cp app/example.env .env
```

5. Configure `.env` file with your credentials:
```
OPENAI_API_KEY=your_openai_api_key
MY_EMAIL=your_email@gmail.com
APP_PASSWORD=your_gmail_app_password

POSTGRES_USER=postgres
POSTGRES_PASSWORD=postgres
POSTGRES_DB=ai_news_aggregator
POSTGRES_HOST=localhost
POSTGRES_PORT=5432
```

6. Initialize database:
```bash
python -m app.database.create_tables
```

### Usage

Run the daily pipeline:
```bash
python main.py [hours] [top_n]
```

Arguments:
- `hours`: Time window for scraping content (default: 24)
- `top_n`: Number of top articles to include in digest (default: 10)

Example:
```bash
python main.py 48 15  # Scrape last 48 hours, send top 15 articles
```

## Configuration

### YouTube Channels

Edit `app/config.py` to add/remove YouTube channels:
```python
YOUTUBE_CHANNELS = [
    "UCawZsQWqfGSbCI5yjkdVkTA",  # Matthew Berman
    # Add more channel IDs here
]
```

### User Profile

Configure your user profile in `app/profiles/user_profile.py` to personalize content curation.

## Dependencies

Key dependencies:
- **openai**: OpenAI API client for GPT models
- **sqlalchemy**: ORM for database operations
- **psycopg2-binary**: PostgreSQL adapter
- **feedparser**: RSS feed parsing
- **youtube-transcript-api**: YouTube transcript extraction
- **beautifulsoup4**: HTML parsing
- **docling**: Document processing
- **python-dotenv**: Environment variable management
- **pydantic**: Data validation
- **markdown**: Markdown processing
- **requests**: HTTP requests

See `requirements.txt` for complete list with versions.

## License

[Add your license here]
