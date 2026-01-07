# Exa Integration

The `strands-tools` package includes comprehensive Exa integration with two main tools for intelligent web search and content extraction.

## Overview

Exa provides advanced web search optimized for LLMs and AI agents, offering neural search capabilities that go beyond traditional keyword-based search engines.

## Features

### 1. **`exa_search`** - Intelligent Web Search

- **Auto mode** (default): Intelligently combines neural embeddings-based search with traditional keyword search
- Search types: `auto`, `neural`, `keyword`, `fast`
- Advanced filtering by domains, dates, and content
- Category-specific search: company, news, PDF, GitHub, personal sites, LinkedIn profiles, financial reports
- Live crawling with fallback options
- Content extraction with summaries and structured output

### 2. **`exa_get_contents`** - URL Content Extraction

- Extract full page content from specific URLs
- AI-generated summaries with custom queries
- Subpage crawling and discovery
- Structured output with JSON schemas
- Cached results with live crawling fallback

## Setup

You'll need to set the `EXA_API_KEY` environment variable:

```bash
export EXA_API_KEY=your_api_key_here
```

Get your API key at: https://dashboard.exa.ai/api-keys

## Usage Example

```python
from strands import Agent
from strands_tools import exa

agent = Agent(tools=[exa])

# Basic search (auto mode is default and recommended)
result = agent.tool.exa_search(
    query="Best project management tools",
    text=True
)

# Company-specific search
result = agent.tool.exa_search(
    query="Anthropic AI safety research",
    category="company",
    text=True
)

# Search with domain filtering and content options
result = agent.tool.exa_search(
    query="JavaScript frameworks comparison",
    include_domains=["github.com", "stackoverflow.com"],
    num_results=5,
    text={"maxCharacters": 500},
    summary={"query": "Key features and differences"}
)

# Get contents from specific URLs
result = agent.tool.exa_get_contents(
    urls=["https://strandsagents.com/"],
    text=True
)

# Advanced content extraction with summary
result = agent.tool.exa_get_contents(
    urls=["https://en.wikipedia.org/wiki/Artificial_intelligence"],
    text={"maxCharacters": 5000, "includeHtmlTags": False},
    summary={"query": "key points and conclusions"},
    subpages=2,
    extras={"links": 5, "imageLinks": 3}
)

# Structured content analysis
result = agent.tool.exa_get_contents(
    urls=["https://arxiv.org/abs/2303.08774"],
    summary={
        "query": "main findings and recommendations",
        "schema": {
            "type": "object",
            "properties": {
                "main_findings": {"type": "string"},
                "recommendations": {"type": "string"},
                "conclusion": {"type": "string"}
            }
        }
    }
)
```

## Search Types

- **auto**: Intelligently combines neural and keyword approaches (recommended default)
- **neural**: Uses embeddings-based model for semantic search
- **keyword**: Google-like SERP search for exact matches
- **fast**: Streamlined versions of neural and keyword models

## Categories

Use categories sparingly as general search works best. Available categories:

- `company`: Focus on company websites and information
- `news`: News articles and journalism
- `pdf`: PDF documents
- `github`: GitHub repositories and code
- `personal site`: Personal websites and blogs
- `linkedin profile`: LinkedIn profiles
- `financial report`: Financial and earnings reports

## Live Crawling Options

- `never`: Only use cached content
- `fallback`: Use cache first, crawl if not available (default)
- `always`: Always perform live crawl
- `preferred`: Try live crawl, fall back to cache if it fails

## Source Code

The integration is located at `src/strands_tools/exa.py`.
