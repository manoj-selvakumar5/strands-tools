# Exa Integration: Technical Architecture & Integration Guide

## Table of Contents
- [Overview](#overview)
- [Architecture Deep Dive](#architecture-deep-dive)
- [Data Flow Documentation](#data-flow-documentation)
- [Integration Guide](#integration-guide)
- [Tool Reference](#tool-reference)
- [API Integration Details](#api-integration-details)
- [Development Guide](#development-guide)

---

## Overview

### What is the Exa Integration?

The Exa integration provides intelligent web search and content retrieval capabilities for Strands agents. It wraps the [Exa API](https://exa.ai) with two powerful tools that enable AI agents to search the web using neural embeddings and extract rich content from URLs.

### Purpose and Use Cases

**Primary Use Cases:**
- **Research & Information Gathering**: Find relevant information across the web using semantic search
- **Company Intelligence**: Retrieve company information, news, and financial reports
- **Content Extraction**: Get full-text content and summaries from specific URLs
- **Document Discovery**: Find PDFs, GitHub repositories, and technical documentation
- **News Monitoring**: Search for recent news articles with date filtering

**Key Differentiators:**
- Neural embeddings-based search optimized for AI agents
- Auto mode that intelligently combines neural and keyword approaches
- Rich content extraction with AI-generated summaries
- Live crawling with smart fallback strategies
- Structured output support with JSON schemas

### Key Features at a Glance

| Feature | Description |
|---------|-------------|
| **Auto Mode** | Intelligent hybrid search combining neural + keyword approaches |
| **Neural Search** | Embeddings-based semantic understanding |
| **Keyword Search** | Traditional Google-like SERP search |
| **Content Extraction** | Full-text retrieval with customizable limits |
| **AI Summaries** | Automatic summarization with custom queries |
| **Category Filtering** | Focus on companies, news, PDFs, GitHub, etc. |
| **Date Filtering** | Filter by crawl date or publish date |
| **Domain Control** | Include/exclude specific domains |
| **Live Crawling** | Fresh content with fallback strategies |
| **Subpage Discovery** | Crawl related pages automatically |
| **Structured Output** | JSON schema support for summaries |
| **Rich Display** | Beautiful terminal output with Rich library |

---

## Architecture Deep Dive

### File Structure

The Exa integration is organized across several files:

```
strands-tools/
├── src/strands_tools/
│   └── exa.py                           # Main implementation (571 lines)
├── tests/
│   └── test_exa.py                      # Unit tests (311 lines)
├── docs/
│   ├── exa_tool.md                      # User-facing documentation
│   ├── exa-blog-briefing.md             # Comparative analysis
│   ├── exa-blog-strategy.md             # Marketing content
│   └── exa-integration-architecture.md  # This document
├── README.md                             # Usage examples
└── .github/ISSUE_TEMPLATE/
    └── feature_request.yml               # Issue template references
```

**Single Source Design**: All implementation logic is contained in `exa.py` for simplicity and maintainability.

### Component Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                        User/Agent                           │
└───────────────────────┬─────────────────────────────────────┘
                        │
                        ▼
┌─────────────────────────────────────────────────────────────┐
│                   Strands SDK (@tool)                       │
│  • Auto-discovery of tools                                  │
│  • Parameter validation and routing                         │
└───────────────────────┬─────────────────────────────────────┘
                        │
                        ▼
┌─────────────────────────────────────────────────────────────┐
│                  Async Tool Functions                       │
│  ┌──────────────────┐        ┌───────────────────┐         │
│  │  exa_search()    │        │ exa_get_contents()│         │
│  │  • Validation    │        │ • Validation      │         │
│  │  • Payload build │        │ • Payload build   │         │
│  │  • API call      │        │ • API call        │         │
│  └──────────────────┘        └───────────────────┘         │
└───────────────────────┬─────────────────────────────────────┘
                        │
                        ▼
┌─────────────────────────────────────────────────────────────┐
│                   HTTP Layer (aiohttp)                      │
│  • Async request handling                                   │
│  • Connection pooling                                       │
│  • JSON serialization                                       │
└───────────────────────┬─────────────────────────────────────┘
                        │
                        ▼
┌─────────────────────────────────────────────────────────────┐
│                      Exa API                                │
│  • https://api.exa.ai/search                               │
│  • https://api.exa.ai/contents                             │
└───────────────────────┬─────────────────────────────────────┘
                        │
                        ▼
┌─────────────────────────────────────────────────────────────┐
│                  Response Processing                        │
│  ┌──────────────────┐        ┌───────────────────┐         │
│  │ format_search_   │        │ format_contents_  │         │
│  │   response()     │        │   response()      │         │
│  │ • Rich Panel     │        │ • Rich Panel      │         │
│  │ • Console output │        │ • Console output  │         │
│  └──────────────────┘        └───────────────────┘         │
└───────────────────────┬─────────────────────────────────────┘
                        │
                        ▼
┌─────────────────────────────────────────────────────────────┐
│              Structured Response to Agent                   │
│  {"status": "success", "content": [...]}                   │
└─────────────────────────────────────────────────────────────┘
```

### Component Breakdown

#### 1. Strands SDK Integration

**Location**: `exa.py:52`

```python
from strands import tool

@tool
async def exa_search(...):
    """Search the web intelligently using Exa's neural and keyword search."""
    # Implementation
```

**How it Works**:
- The `@tool` decorator registers functions with the Strands framework
- Decorated functions become auto-discoverable by any Strands Agent
- Agents can invoke tools through natural language
- The framework handles parameter mapping and validation

**Benefits**:
- Zero-config tool registration
- Standardized interface
- Automatic documentation generation
- Type checking and validation

#### 2. API Communication Layer

**Location**: `exa.py:56-59, 396-410, 540-555`

**Configuration Constants**:
```python
EXA_API_BASE_URL = "https://api.exa.ai"
EXA_SEARCH_ENDPOINT = "/search"
EXA_CONTENTS_ENDPOINT = "/contents"
```

**HTTP Client**: Uses `aiohttp` for async HTTP operations

```python
async with aiohttp.ClientSession() as session:
    async with session.post(url, json=payload, headers=headers) as response:
        data = await response.json()
```

**Why aiohttp?**
- Non-blocking I/O for better performance
- Connection pooling out of the box
- Native async/await support
- Efficient resource management with context managers

**Authentication**:
```python
headers = {
    "x-api-key": api_key,
    "Content-Type": "application/json"
}
```

API keys are retrieved from environment variables via `_get_api_key()` helper.

#### 3. Async/Await Implementation

**All tool functions are async**:
```python
@tool
async def exa_search(...) -> Dict[str, Any]:
    # Async implementation
```

**Benefits**:
1. **Non-blocking**: Agents can make multiple concurrent tool calls
2. **Performance**: Better resource utilization during I/O operations
3. **Scalability**: Supports high-concurrency scenarios
4. **Compatibility**: Works seamlessly with async Strands agents

**Example Concurrent Usage**:
```python
# Multiple searches in parallel
results = await asyncio.gather(
    agent.tool.exa_search(query="AI safety"),
    agent.tool.exa_search(query="AI alignment"),
    agent.tool.exa_search(query="AI governance")
)
```

#### 4. Error Handling Strategy

**Location**: `exa.py:417-425, 562-570`

**Multi-layered Error Handling**:

```python
try:
    # Validation layer
    if not query or not query.strip():
        return {"status": "error", "content": [...]}

    # API call
    async with aiohttp.ClientSession() as session:
        async with session.post(...) as response:
            data = await response.json()

    return {"status": "success", "content": [...]}

except asyncio.TimeoutError:
    return {"status": "error", "content": [{"text": "Request timeout..."}]}
except aiohttp.ClientError:
    return {"status": "error", "content": [{"text": "Connection error..."}]}
except ValueError as e:
    return {"status": "error", "content": [{"text": str(e)}]}
except Exception as e:
    logger.error(f"Unexpected error: {str(e)}")
    return {"status": "error", "content": [{"text": f"Unexpected error: {str(e)}"}]}
```

**Error Categories**:
1. **Validation Errors**: Empty queries, invalid parameters, malformed dates
2. **Network Errors**: Timeouts, connection failures
3. **API Errors**: JSON parsing failures, invalid responses
4. **Generic Errors**: Catch-all with logging

**Error Response Format**:
```python
{
    "status": "error",
    "content": [{"text": "Human-readable error message"}]
}
```

#### 5. Rich Display Integration

**Location**: `exa.py:50-62, 411-413, 557-558`

**Purpose**: Provide beautiful terminal output for human users while maintaining structured data for agents.

**Implementation**:
```python
from rich.console import Console
from rich.panel import Panel

console = Console()

# Format and display
panel = format_search_response(data)
console.print(panel)
```

**Example Output**:
```
╭─ Exa Search Results ─────────────────────────────────────────╮
│ Request ID: abc123xyz                                        │
│ Search Type: auto (resolved: neural)                         │
│ Cost: $0.0050                                               │
│                                                              │
│ Results: 3 found                                            │
│ ──────────────────────────────────────────────────────────  │
│                                                              │
│ [1] Understanding AI Safety Research                         │
│ URL: https://example.com/ai-safety                          │
│ Author: Jane Researcher                                      │
│ Published: 2024-01-15                                       │
│ Summary: Comprehensive overview of current AI safety...     │
│ Content: AI safety research focuses on ensuring that...     │
│                                                              │
│ [2] AI Alignment Challenges                                  │
│ URL: https://example.com/alignment                          │
│ ...                                                          │
╰──────────────────────────────────────────────────────────────╯
```

**Dual Interface Benefits**:
- Human users get rich, formatted output
- Agents receive structured JSON data
- No performance penalty (formatting happens after data extraction)

### Design Decisions and Rationale

#### 1. Single Source File Design

**Decision**: Keep all implementation in one file (`exa.py`)

**Rationale**:
- **Simplicity**: Easy to navigate and understand
- **Cohesion**: All Exa-related logic in one place
- **Maintainability**: Changes to API integration are localized
- **Small Scope**: Only 571 lines with two main functions

**Trade-off**: If Exa integration grows significantly, may need to split into modules.

#### 2. Auto Mode as Default

**Decision**: Set `type="auto"` as the default search type

**Rationale**:
- **Best Results**: Auto mode combines neural and keyword for optimal relevance
- **User-Friendly**: Users don't need to understand search type differences
- **Flexibility**: Power users can still override for specific needs
- **Exa Recommendation**: Aligns with Exa's own best practices

#### 3. Async-First Architecture

**Decision**: Make all tool functions async

**Rationale**:
- **Performance**: Non-blocking I/O for better throughput
- **Scalability**: Supports concurrent agent operations
- **Future-Proof**: Aligns with async trends in Python
- **Strands Compatibility**: Works seamlessly with async agents

#### 4. Comprehensive Error Handling

**Decision**: Catch specific exceptions and return structured errors

**Rationale**:
- **Reliability**: Graceful degradation on failures
- **Debugging**: Clear error messages for troubleshooting
- **User Experience**: Users see helpful error messages, not stack traces
- **Agent Compatibility**: Structured error responses that agents can interpret

#### 5. Rich Display Integration

**Decision**: Use Rich library for terminal output

**Rationale**:
- **User Experience**: Beautiful, readable output for human users
- **Developer Experience**: Better debugging and development workflow
- **No Performance Impact**: Formatting happens after data processing
- **Optional**: Doesn't affect agent-to-agent communication

#### 6. Environment-Based Configuration

**Decision**: Use environment variables for API keys

**Rationale**:
- **Security**: Keeps secrets out of code
- **Flexibility**: Easy to change without code modifications
- **Standard Practice**: Follows 12-factor app methodology
- **CI/CD Friendly**: Works well in automated environments

---

## Data Flow Documentation

### Request/Response Lifecycle

#### exa_search() Flow

```
1. User/Agent Invocation
   ↓
2. Parameter Validation
   • Check query is not empty
   • Validate num_results range (1-100)
   • Validate date formats (ISO 8601)
   ↓
3. Authentication
   • Retrieve EXA_API_KEY from environment
   • Raise error if missing
   ↓
4. Payload Construction
   • Build base payload with query and search type
   • Add optional filtering parameters
   • Add contents options if specified
   • Remove None values
   ↓
5. HTTP Request
   • Create aiohttp ClientSession
   • POST to https://api.exa.ai/search
   • Include API key header
   • Send JSON payload
   ↓
6. Response Processing
   • Parse JSON response
   • Handle parsing errors
   ↓
7. Display Formatting
   • Format response with Rich Panel
   • Display to console (if terminal user)
   ↓
8. Return Structured Response
   • {"status": "success", "content": [{"text": str(data)}]}
```

#### exa_get_contents() Flow

```
1. User/Agent Invocation
   ↓
2. Parameter Validation
   • Check URLs list is not empty
   ↓
3. Authentication
   • Retrieve EXA_API_KEY from environment
   ↓
4. Payload Construction
   • Build payload with URLs
   • Add content options (text, summary, etc.)
   • Remove None values
   ↓
5. HTTP Request
   • Create aiohttp ClientSession
   • POST to https://api.exa.ai/contents
   • Include API key header
   • Send JSON payload
   ↓
6. Response Processing
   • Parse JSON response
   • Handle parsing errors
   ↓
7. Display Formatting
   • Format response with Rich Panel
   • Show success/failure counts
   ↓
8. Return Structured Response
   • {"status": "success", "content": [{"text": str(data)}]}
```

### Authentication Flow

```
┌─────────────────────────────────┐
│  Function Entry Point           │
│  (exa_search/exa_get_contents) │
└──────────────┬──────────────────┘
               │
               ▼
┌─────────────────────────────────┐
│  Call _get_api_key()            │
└──────────────┬──────────────────┘
               │
               ▼
┌─────────────────────────────────┐
│  os.getenv("EXA_API_KEY")      │
└──────────────┬──────────────────┘
               │
          ┌────┴────┐
          │         │
    None  │         │  Valid Key
          │         │
          ▼         ▼
   ┌──────────┐  ┌──────────────────┐
   │  Raise   │  │  Return API Key  │
   │ValueError│  └──────────────────┘
   └──────────┘           │
                          ▼
               ┌────────────────────┐
               │ Add to HTTP Header │
               │ "x-api-key": key   │
               └────────────────────┘
```

### Error Handling Paths

**Validation Errors** (Early Return):
```
User Input → Validation → Error Response → Return to Agent
```

**Network Errors** (Exception Handling):
```
API Request → Timeout/Connection Error → Exception Handler → Error Response → Return to Agent
```

**API Errors** (Response Processing):
```
API Response → JSON Parse Error → Exception Handler → Error Response → Return to Agent
```

### Example System Trace

**Scenario**: Agent searches for "AI safety research" with summaries

```python
# Agent invocation
result = await agent.tool.exa_search(
    query="AI safety research",
    num_results=3,
    text=True,
    summary={"query": "key findings"}
)
```

**Trace**:
```
1. [Agent] Calls exa_search with parameters
2. [exa.py:313] Validates query is not empty ✓
3. [exa.py:317] Validates num_results (3) is in range 1-100 ✓
4. [exa.py:354] Calls _get_api_key()
5. [exa.py:67] Retrieves EXA_API_KEY from environment ✓
6. [exa.py:357-394] Builds payload:
   {
     "query": "AI safety research",
     "type": "auto",
     "numResults": 3,
     "contents": {
       "text": true,
       "summary": {"query": "key findings"}
     }
   }
7. [exa.py:396] Sets headers with API key
8. [exa.py:404-409] Makes async POST to https://api.exa.ai/search
9. [Exa API] Processes request, returns results
10. [exa.py:407-409] Parses JSON response
11. [exa.py:412] Formats response with format_search_response()
12. [exa.py:413] Displays Rich Panel to console
13. [exa.py:415] Returns structured response to agent
14. [Agent] Processes results and continues
```

---

## Integration Guide

### Prerequisites

#### Required Dependencies

```bash
# Core dependencies (automatically installed with strands-tools)
pip install strands
pip install aiohttp
pip install rich
```

#### API Key Setup

1. **Get your Exa API key**: Visit https://dashboard.exa.ai/api-keys
2. **Set environment variable**:

```bash
# Linux/macOS
export EXA_API_KEY="your_exa_api_key_here"

# Windows (Command Prompt)
set EXA_API_KEY=your_exa_api_key_here

# Windows (PowerShell)
$env:EXA_API_KEY="your_exa_api_key_here"
```

3. **Persist in shell profile** (optional):

```bash
# Add to ~/.bashrc or ~/.zshrc
echo 'export EXA_API_KEY="your_exa_api_key_here"' >> ~/.bashrc
source ~/.bashrc
```

4. **Use .env file** (recommended for projects):

```bash
# .env file
EXA_API_KEY=your_exa_api_key_here
```

```python
# Load with python-dotenv
from dotenv import load_dotenv
load_dotenv()
```

### Environment Configuration

#### Verify Installation

```python
import strands_tools
from strands_tools import exa

print("Exa tools loaded successfully")
print(f"Available tools: {dir(exa)}")
```

#### Test API Connection

```python
import asyncio
from strands_tools.exa import exa_search

async def test_connection():
    result = await exa_search(query="test", num_results=1)
    if result["status"] == "success":
        print("✓ Exa API connection successful")
    else:
        print(f"✗ Connection failed: {result['content'][0]['text']}")

asyncio.run(test_connection())
```

### Basic Integration Examples

#### Example 1: Simple Agent with Exa Search

```python
from strands import Agent
from strands_tools import exa

# Create agent with Exa tools
agent = Agent(
    name="Research Assistant",
    tools=[exa],
    model="claude-3-5-sonnet-20241022"
)

# Use the agent
response = agent.run("Find the latest developments in quantum computing")
print(response)
```

#### Example 2: Direct Tool Usage

```python
import asyncio
from strands_tools.exa import exa_search, exa_get_contents

async def research_topic(topic):
    # Search for relevant pages
    search_results = await exa_search(
        query=topic,
        num_results=5,
        text=True,
        summary={"query": "main points and findings"}
    )

    print(f"Search Status: {search_results['status']}")

    # Get full content from specific URLs
    urls = ["https://example.com/article1", "https://example.com/article2"]
    content_results = await exa_get_contents(
        urls=urls,
        text={"maxCharacters": 3000},
        summary={"query": "key takeaways"}
    )

    return search_results, content_results

# Run
results = asyncio.run(research_topic("AI safety alignment"))
```

#### Example 3: Category-Specific Search

```python
async def find_company_info(company_name):
    """Search for company-specific information."""
    result = await exa_search(
        query=f"{company_name} company information",
        category="company",
        num_results=3,
        text=True,
        summary={"query": "company overview and recent news"}
    )
    return result

# Usage
company_data = asyncio.run(find_company_info("Anthropic"))
```

### Advanced Usage Patterns

#### Pattern 1: Multi-Source Research

```python
async def comprehensive_research(topic):
    """Research a topic across multiple sources and categories."""

    # Parallel searches across different categories
    searches = await asyncio.gather(
        # General web search
        exa_search(
            query=topic,
            num_results=5,
            text=True
        ),
        # News articles
        exa_search(
            query=topic,
            category="news",
            start_published_date="2024-01-01T00:00:00.000Z",
            num_results=5
        ),
        # GitHub repositories
        exa_search(
            query=topic,
            category="github",
            num_results=3
        ),
        # Academic papers (PDFs)
        exa_search(
            query=topic,
            category="pdf",
            num_results=3
        )
    )

    general, news, github, papers = searches

    return {
        "general": general,
        "news": news,
        "code": github,
        "research": papers
    }

# Usage
research = asyncio.run(comprehensive_research("large language models"))
```

#### Pattern 2: Structured Data Extraction

```python
async def extract_structured_info(url):
    """Extract structured information using JSON schema."""

    schema = {
        "type": "object",
        "properties": {
            "title": {"type": "string"},
            "main_points": {
                "type": "array",
                "items": {"type": "string"}
            },
            "conclusion": {"type": "string"},
            "author": {"type": "string"},
            "date": {"type": "string"}
        }
    }

    result = await exa_get_contents(
        urls=[url],
        summary={
            "query": "extract title, main points, conclusion, author, and date",
            "schema": schema
        }
    )

    return result

# Usage
structured_data = asyncio.run(extract_structured_info("https://example.com/article"))
```

#### Pattern 3: Domain-Specific Search

```python
async def search_trusted_sources(query, domains):
    """Search only within trusted domains."""

    result = await exa_search(
        query=query,
        include_domains=domains,
        num_results=10,
        text={"maxCharacters": 1000},
        summary={"query": "key findings"}
    )

    return result

# Usage
trusted_domains = [
    "arxiv.org",
    "nature.com",
    "sciencedirect.com",
    "ieee.org"
]

academic_results = asyncio.run(
    search_trusted_sources(
        "transformer architecture improvements",
        trusted_domains
    )
)
```

#### Pattern 4: Recent News Monitoring

```python
from datetime import datetime, timedelta

async def monitor_recent_news(topic, days_back=7):
    """Monitor recent news about a topic."""

    # Calculate date range
    end_date = datetime.now()
    start_date = end_date - timedelta(days=days_back)

    result = await exa_search(
        query=topic,
        category="news",
        start_published_date=start_date.isoformat() + "Z",
        end_published_date=end_date.isoformat() + "Z",
        num_results=10,
        text=True,
        summary={"query": "headline and key points"}
    )

    return result

# Usage
news = asyncio.run(monitor_recent_news("AI regulation", days_back=30))
```

#### Pattern 5: Deep Content Analysis with Subpages

```python
async def deep_dive_analysis(base_url):
    """Analyze a page and its related subpages."""

    result = await exa_get_contents(
        urls=[base_url],
        text={"maxCharacters": 5000},
        summary={
            "query": "comprehensive summary including main content and related topics"
        },
        subpages=5,  # Crawl 5 related subpages
        subpage_target="documentation tutorial guide",  # Look for specific subpages
        extras={
            "links": 10,
            "imageLinks": 5
        }
    )

    return result

# Usage
deep_analysis = asyncio.run(deep_dive_analysis("https://docs.example.com/overview"))
```

### Best Practices

#### 1. Use Auto Mode by Default

```python
# ✓ Good: Use auto mode (default)
result = await exa_search(query="AI safety")

# ✗ Avoid: Specifying search type unless you have a specific reason
result = await exa_search(query="AI safety", type="keyword")
```

**Why**: Auto mode intelligently combines neural and keyword search for best results.

#### 2. Set maxCharacters for Text Extraction

```python
# ✓ Good: Control text length
result = await exa_search(
    query="machine learning basics",
    text={"maxCharacters": 1000}
)

# ✗ Avoid: Relying on defaults if you need specific lengths
result = await exa_search(
    query="machine learning basics",
    text=True  # Unknown length
)
```

**Why**: Prevents token bloat and ensures predictable response sizes.

#### 3. Use Summaries for Large Content

```python
# ✓ Good: Request summary for large pages
result = await exa_get_contents(
    urls=["https://example.com/long-article"],
    summary={"query": "main findings and conclusions"}
)

# ✗ Avoid: Extracting full text when you only need key points
result = await exa_get_contents(
    urls=["https://example.com/long-article"],
    text=True  # Could be very large
)
```

**Why**: Summaries are more efficient and provide focused information.

#### 4. Handle Errors Gracefully

```python
# ✓ Good: Check status and handle errors
result = await exa_search(query="test")
if result["status"] == "error":
    error_message = result["content"][0]["text"]
    logger.error(f"Search failed: {error_message}")
    # Implement fallback logic
else:
    # Process results
    pass

# ✗ Avoid: Assuming success
result = await exa_search(query="test")
data = result["content"]  # Could fail if status is error
```

**Why**: Network issues, API errors, and rate limits can occur.

#### 5. Use Parallel Searches Efficiently

```python
# ✓ Good: Parallel independent searches
results = await asyncio.gather(
    exa_search(query="topic A"),
    exa_search(query="topic B"),
    exa_search(query="topic C")
)

# ✗ Avoid: Sequential searches when they can be parallel
result_a = await exa_search(query="topic A")
result_b = await exa_search(query="topic B")
result_c = await exa_search(query="topic C")
```

**Why**: Parallel execution is significantly faster for independent operations.

#### 6. Use Specific Categories When Appropriate

```python
# ✓ Good: Use category when you know what you want
company_info = await exa_search(
    query="Anthropic",
    category="company"  # Specifically looking for company info
)

# ✗ Avoid: Using category unnecessarily
general_search = await exa_search(
    query="best practices for Python",
    category="company"  # Doesn't make sense for this query
)
```

**Why**: Categories improve precision when searching for specific content types.

#### 7. Validate Date Formats

```python
from datetime import datetime

# ✓ Good: Use ISO 8601 format
date_str = datetime.now().isoformat() + "Z"
result = await exa_search(
    query="AI news",
    start_published_date=date_str
)

# ✗ Avoid: Non-standard date formats
result = await exa_search(
    query="AI news",
    start_published_date="2024/01/15"  # Wrong format
)
```

**Why**: API requires ISO 8601 format (YYYY-MM-DDTHH:MM:SS.sssZ).

---

## Tool Reference

### exa_search()

**Location**: `exa.py:191-426`

Intelligent web search using Exa's neural and keyword search capabilities.

#### Function Signature

```python
async def exa_search(
    query: str,
    type: Optional[Literal["keyword", "neural", "fast", "auto"]] = "auto",
    category: Optional[Literal["company", "news", "pdf", "github", "personal site", "linkedin profile", "financial report"]] = None,
    user_location: Optional[str] = None,
    num_results: Optional[int] = None,
    include_domains: Optional[List[str]] = None,
    exclude_domains: Optional[List[str]] = None,
    start_crawl_date: Optional[str] = None,
    end_crawl_date: Optional[str] = None,
    start_published_date: Optional[str] = None,
    end_published_date: Optional[str] = None,
    include_text: Optional[List[str]] = None,
    exclude_text: Optional[List[str]] = None,
    context: Optional[Union[bool, Dict[str, Any]]] = None,
    moderation: Optional[bool] = None,
    # Contents options
    text: Optional[Union[bool, Dict[str, Any]]] = None,
    summary: Optional[Dict[str, Any]] = None,
    livecrawl: Optional[Literal["never", "fallback", "always", "preferred"]] = None,
    livecrawl_timeout: Optional[int] = None,
    subpages: Optional[int] = None,
    subpage_target: Optional[Union[str, List[str]]] = None,
    extras: Optional[Dict[str, Any]] = None,
) -> Dict[str, Any]
```

#### Parameters

##### Core Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `query` | `str` | Yes | - | The search query string |
| `type` | `str` | No | `"auto"` | Search type: "auto", "neural", "keyword", or "fast" |
| `num_results` | `int` | No | 10 | Number of results (1-100) |

##### Filtering Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `category` | `str` | Focus on specific content type: "company", "news", "pdf", "github", "personal site", "linkedin profile", "financial report" |
| `user_location` | `str` | Two-letter ISO country code (e.g., "US", "UK") for geo-localized results |
| `include_domains` | `List[str]` | List of domains to include (e.g., `["github.com", "stackoverflow.com"]`) |
| `exclude_domains` | `List[str]` | List of domains to exclude |
| `start_crawl_date` | `str` | Include links crawled after this date (ISO 8601 format) |
| `end_crawl_date` | `str` | Include links crawled before this date (ISO 8601 format) |
| `start_published_date` | `str` | Include links published after this date (ISO 8601 format) |
| `end_published_date` | `str` | Include links published before this date (ISO 8601 format) |
| `include_text` | `List[str]` | Strings that must be present in webpage text (max 1 string, up to 5 words) |
| `exclude_text` | `List[str]` | Strings that must not be present in webpage text (max 1 string, up to 5 words) |

##### Content Extraction Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `text` | `bool` or `Dict` | Extract full text. Dict format: `{"maxCharacters": int, "includeHtmlTags": bool}` |
| `summary` | `Dict` | Generate summaries. Format: `{"query": str, "schema": dict}` |
| `context` | `bool` or `Dict` | Format results for LLM context. Dict format: `{"maxCharacters": int}` |
| `moderation` | `bool` | Enable content moderation to filter unsafe content |

##### Live Crawling Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `livecrawl` | `str` | Live crawling strategy: "never", "fallback", "always", "preferred" |
| `livecrawl_timeout` | `int` | Timeout for live crawling in milliseconds (default 10000) |
| `subpages` | `int` | Number of subpages to crawl from each result |
| `subpage_target` | `str` or `List[str]` | Keywords to find specific subpages |
| `extras` | `Dict` | Extra options: `{"links": int, "imageLinks": int}` |

#### Return Value

```python
{
    "status": "success" | "error",
    "content": [
        {
            "text": str  # String representation of response data
        }
    ]
}
```

The actual response data includes:
- `requestId`: Unique request identifier
- `searchType`: The search type used
- `resolvedSearchType`: The actual search type after auto resolution
- `results`: List of search results with title, URL, text, summary, etc.
- `costDollars`: Cost breakdown for the request
- `context`: Formatted context if requested

#### Usage Examples

**Basic Search**:
```python
result = await exa_search(
    query="machine learning best practices"
)
```

**Search with Text Extraction**:
```python
result = await exa_search(
    query="Python async programming",
    num_results=5,
    text={"maxCharacters": 1000}
)
```

**Company Search**:
```python
result = await exa_search(
    query="Anthropic AI safety",
    category="company",
    text=True,
    summary={"query": "company overview and recent developments"}
)
```

**News Search with Date Filtering**:
```python
result = await exa_search(
    query="climate change policy",
    category="news",
    start_published_date="2024-01-01T00:00:00.000Z",
    num_results=10
)
```

**Domain-Filtered Search**:
```python
result = await exa_search(
    query="React hooks tutorial",
    include_domains=["github.com", "reactjs.org"],
    exclude_domains=["medium.com"],
    num_results=5
)
```

**Structured Summary**:
```python
result = await exa_search(
    query="GPT-4 technical report",
    category="pdf",
    summary={
        "query": "extract key findings",
        "schema": {
            "type": "object",
            "properties": {
                "findings": {"type": "array", "items": {"type": "string"}},
                "methodology": {"type": "string"},
                "conclusions": {"type": "string"}
            }
        }
    }
)
```

### exa_get_contents()

**Location**: `exa.py:429-571`

Get full page contents, summaries, and metadata for a list of URLs.

#### Function Signature

```python
async def exa_get_contents(
    urls: List[str],
    text: Optional[Union[bool, Dict[str, Any]]] = None,
    summary: Optional[Dict[str, Any]] = None,
    livecrawl: Optional[Literal["never", "fallback", "always", "preferred"]] = None,
    livecrawl_timeout: Optional[int] = None,
    subpages: Optional[int] = None,
    subpage_target: Optional[Union[str, List[str]]] = None,
    extras: Optional[Dict[str, Any]] = None,
    context: Optional[Union[bool, Dict[str, Any]]] = None,
) -> Dict[str, Any]
```

#### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `urls` | `List[str]` | Yes | List of URLs to retrieve content from |
| `text` | `bool` or `Dict` | No | Extract full text. Dict: `{"maxCharacters": int, "includeHtmlTags": bool}` |
| `summary` | `Dict` | No | Generate summaries: `{"query": str, "schema": dict}` |
| `livecrawl` | `str` | No | Strategy: "never", "fallback" (default), "always", "preferred" |
| `livecrawl_timeout` | `int` | No | Timeout in milliseconds (default 10000) |
| `subpages` | `int` | No | Number of subpages to crawl |
| `subpage_target` | `str` or `List[str]` | No | Keywords for specific subpages |
| `extras` | `Dict` | No | Extra content: `{"links": int, "imageLinks": int}` |
| `context` | `bool` or `Dict` | No | Format for LLM context |

#### Return Value

```python
{
    "status": "success" | "error",
    "content": [
        {
            "text": str  # String representation of response data
        }
    ]
}
```

Response data includes:
- `requestId`: Unique request identifier
- `results`: List of content results with title, URL, text, summary, subpages
- `statuses`: Success/error status for each URL
- `costDollars`: Cost breakdown

#### Usage Examples

**Simple Content Retrieval**:
```python
result = await exa_get_contents(
    urls=["https://example.com/article"],
    text=True
)
```

**Content with Summary**:
```python
result = await exa_get_contents(
    urls=[
        "https://arxiv.org/abs/2303.08774",
        "https://arxiv.org/abs/2204.01691"
    ],
    text={"maxCharacters": 3000},
    summary={"query": "summarize key findings and methodology"}
)
```

**Deep Crawl with Subpages**:
```python
result = await exa_get_contents(
    urls=["https://docs.python.org/3/"],
    text=True,
    subpages=5,
    subpage_target="tutorial guide",
    extras={"links": 10}
)
```

**Structured Content Extraction**:
```python
result = await exa_get_contents(
    urls=["https://example.com/product"],
    summary={
        "query": "extract product details",
        "schema": {
            "type": "object",
            "properties": {
                "product_name": {"type": "string"},
                "price": {"type": "string"},
                "features": {"type": "array", "items": {"type": "string"}},
                "rating": {"type": "string"}
            }
        }
    }
)
```

### Common Parameter Combinations

#### Research Mode
```python
{
    "text": {"maxCharacters": 2000},
    "summary": {"query": "key findings and conclusions"},
    "livecrawl": "fallback"
}
```

#### Quick Overview
```python
{
    "summary": {"query": "brief overview"},
    "livecrawl": "never"  # Use cache for speed
}
```

#### Deep Analysis
```python
{
    "text": {"maxCharacters": 5000},
    "summary": {"query": "comprehensive analysis"},
    "subpages": 5,
    "subpage_target": "documentation examples",
    "extras": {"links": 10, "imageLinks": 5},
    "livecrawl": "preferred"
}
```

#### Structured Data Extraction
```python
{
    "summary": {
        "query": "extract structured information",
        "schema": {
            "type": "object",
            "properties": {
                "title": {"type": "string"},
                "main_points": {"type": "array", "items": {"type": "string"}},
                "conclusion": {"type": "string"}
            }
        }
    }
}
```

---

## API Integration Details

### Exa API Endpoints

#### Search Endpoint

**URL**: `POST https://api.exa.ai/search`

**Purpose**: Perform intelligent web search with neural and keyword capabilities.

**Request Format**:
```json
{
  "query": "search query",
  "type": "auto",
  "numResults": 10,
  "category": "company",
  "includeDomains": ["example.com"],
  "contents": {
    "text": true,
    "summary": {"query": "summarize"}
  }
}
```

**Response Format**:
```json
{
  "requestId": "abc123",
  "searchType": "auto",
  "resolvedSearchType": "neural",
  "results": [
    {
      "title": "Result Title",
      "url": "https://example.com",
      "author": "Author Name",
      "publishedDate": "2024-01-15",
      "text": "Page content...",
      "summary": "AI-generated summary..."
    }
  ],
  "costDollars": {
    "total": 0.005
  }
}
```

#### Contents Endpoint

**URL**: `POST https://api.exa.ai/contents`

**Purpose**: Extract full content and metadata from specific URLs.

**Request Format**:
```json
{
  "urls": ["https://example.com"],
  "text": {"maxCharacters": 1000},
  "summary": {"query": "key points"},
  "livecrawl": "fallback"
}
```

**Response Format**:
```json
{
  "requestId": "xyz789",
  "results": [
    {
      "url": "https://example.com",
      "title": "Page Title",
      "text": "Page content...",
      "summary": "Summary...",
      "subpages": []
    }
  ],
  "statuses": [
    {
      "id": "https://example.com",
      "status": "success"
    }
  ],
  "costDollars": {
    "total": 0.003
  }
}
```

### Authentication Mechanism

**Method**: API Key Header Authentication

**Header Format**:
```
x-api-key: your_exa_api_key_here
```

**Implementation**:
```python
headers = {
    "x-api-key": os.getenv("EXA_API_KEY"),
    "Content-Type": "application/json"
}
```

**Security Considerations**:
- API keys should never be hardcoded
- Use environment variables or secret management systems
- Rotate keys periodically
- Monitor usage for anomalies

### Rate Limiting and Costs

#### Cost Structure

Exa API charges based on:
1. **Search Operations**: Cost per search query
2. **Content Extraction**: Cost per URL and content type
3. **Live Crawling**: Additional cost for fresh content
4. **Summaries**: Cost per summary generation

**Cost Tracking**: Each response includes a `costDollars` object:
```json
{
  "costDollars": {
    "total": 0.005,
    "search": 0.002,
    "contents": 0.003
  }
}
```

#### Rate Limits

**Default Limits**:
- Requests per minute: Varies by plan
- Concurrent requests: Varies by plan
- Daily quotas: Varies by plan

**Handling Rate Limits**:
```python
import asyncio

async def rate_limited_search(query):
    try:
        result = await exa_search(query=query)
        return result
    except aiohttp.ClientResponseError as e:
        if e.status == 429:  # Too Many Requests
            # Implement exponential backoff
            await asyncio.sleep(5)
            return await rate_limited_search(query)
        raise
```

#### Best Practices for Cost Management

1. **Use maxCharacters**: Limit text extraction to what you need
2. **Cache results**: Store frequently accessed content locally
3. **Use summaries**: Often cheaper than full text extraction
4. **Batch operations**: Group related searches when possible
5. **Monitor costs**: Track `costDollars` in responses

---

## Development Guide

### Testing Approach

#### Test Structure

**File**: `tests/test_exa.py` (311 lines)

**Test Categories**:

1. **API Key Validation Tests**
   - Missing API key
   - Present API key

2. **Parameter Validation Tests**
   - Empty query validation
   - num_results bounds checking
   - Date format validation

3. **Response Formatting Tests**
   - format_search_response()
   - format_contents_response()

4. **Error Handling Tests**
   - Connection errors
   - Timeouts
   - JSON parsing errors

#### Running Tests

```bash
# Run all Exa tests
pytest tests/test_exa.py

# Run with coverage
pytest tests/test_exa.py --cov=strands_tools.exa

# Run specific test
pytest tests/test_exa.py::test_empty_query_validation

# Run with verbose output
pytest tests/test_exa.py -v
```

#### Writing New Tests

**Example Test**:
```python
import pytest
from strands_tools.exa import exa_search

@pytest.mark.asyncio
async def test_search_with_category():
    """Test search with category filter."""
    # Mock environment variable
    import os
    os.environ["EXA_API_KEY"] = "test_key"

    # This would require mocking aiohttp
    # Actual implementation depends on test setup

    result = await exa_search(
        query="test",
        category="company"
    )

    assert result["status"] == "success"
```

#### Test Fixtures

**Mock Search Response**:
```python
@pytest.fixture
def mock_search_response():
    return {
        "requestId": "test123",
        "searchType": "auto",
        "resolvedSearchType": "neural",
        "results": [
            {
                "title": "Test Result",
                "url": "https://example.com",
                "author": "Test Author",
                "publishedDate": "2024-01-15",
                "text": "Test content",
                "summary": "Test summary"
            }
        ],
        "costDollars": {"total": 0.005}
    }
```

### Adding New Features

#### Process

1. **Plan the Feature**
   - Define requirements
   - Check Exa API documentation
   - Consider backward compatibility

2. **Implement**
   - Add parameters to function signatures
   - Update payload construction
   - Add validation logic

3. **Test**
   - Write unit tests
   - Test edge cases
   - Verify error handling

4. **Document**
   - Update docstrings
   - Add examples
   - Update this documentation

#### Example: Adding a New Parameter

**1. Update Function Signature**:
```python
@tool
async def exa_search(
    query: str,
    # ... existing parameters ...
    new_parameter: Optional[str] = None,  # Add new parameter
) -> Dict[str, Any]:
```

**2. Update Docstring**:
```python
"""
Args:
    ...
    new_parameter: Description of new parameter
    ...
"""
```

**3. Add to Payload**:
```python
payload = {
    "query": query,
    # ... existing fields ...
    "newParameter": new_parameter,
}
```

**4. Add Validation** (if needed):
```python
if new_parameter is not None:
    # Validate new_parameter
    if not validate_new_parameter(new_parameter):
        return {
            "status": "error",
            "content": [{"text": "Invalid new_parameter value"}]
        }
```

**5. Write Tests**:
```python
@pytest.mark.asyncio
async def test_new_parameter():
    result = await exa_search(
        query="test",
        new_parameter="valid_value"
    )
    assert result["status"] == "success"
```

### Contributing Guidelines

#### Code Style

- Follow PEP 8
- Use type hints
- Write descriptive docstrings
- Add inline comments for complex logic

**Example**:
```python
async def exa_search(
    query: str,
    type: Optional[Literal["keyword", "neural", "fast", "auto"]] = "auto",
) -> Dict[str, Any]:
    """
    Search the web intelligently using Exa's neural and keyword search.

    Args:
        query: The search query string
        type: Search type - "auto" (default), "neural", "keyword", or "fast"

    Returns:
        Dict containing search results with status and content

    Raises:
        ValueError: If API key is not configured
    """
    # Implementation
```

#### Pull Request Process

1. **Fork and Branch**
   ```bash
   git checkout -b feature/new-exa-feature
   ```

2. **Make Changes**
   - Write code
   - Add tests
   - Update documentation

3. **Test Locally**
   ```bash
   pytest tests/test_exa.py
   ```

4. **Commit**
   ```bash
   git commit -m "Add new feature for Exa integration"
   ```

5. **Push and Create PR**
   ```bash
   git push origin feature/new-exa-feature
   ```

#### Documentation Updates

When adding features, update:
- Function docstrings (`exa.py`)
- User documentation (`docs/exa_tool.md`)
- This architecture guide (`docs/exa-integration-architecture.md`)
- README examples (`README.md`)

### Debugging Tips

#### Enable Debug Logging

```python
import logging

logging.basicConfig(level=logging.DEBUG)
logger = logging.getLogger("strands_tools.exa")
logger.setLevel(logging.DEBUG)
```

#### Inspect Raw Responses

```python
async def debug_search(query):
    result = await exa_search(query=query)

    # Parse the response string
    import ast
    data = ast.literal_eval(result["content"][0]["text"])

    print(f"Request ID: {data.get('requestId')}")
    print(f"Search Type: {data.get('searchType')}")
    print(f"Results: {len(data.get('results', []))}")
    print(f"Cost: ${data.get('costDollars', {}).get('total', 0)}")

    return data

# Usage
data = asyncio.run(debug_search("test query"))
```

#### Test API Connection

```python
import aiohttp
import os

async def test_api_connection():
    api_key = os.getenv("EXA_API_KEY")

    headers = {
        "x-api-key": api_key,
        "Content-Type": "application/json"
    }

    payload = {
        "query": "test",
        "type": "auto",
        "numResults": 1
    }

    async with aiohttp.ClientSession() as session:
        async with session.post(
            "https://api.exa.ai/search",
            json=payload,
            headers=headers
        ) as response:
            print(f"Status: {response.status}")
            data = await response.json()
            print(f"Response: {data}")

asyncio.run(test_api_connection())
```

---

## Appendix

### Glossary

| Term | Definition |
|------|------------|
| **Neural Search** | Embeddings-based semantic search using machine learning |
| **Auto Mode** | Intelligent hybrid combining neural and keyword search |
| **Live Crawling** | Real-time fetching of fresh web content |
| **Subpage** | Related page crawled from a primary result |
| **JSON Schema** | Structure definition for validating summary output |
| **Rich Panel** | Formatted terminal output using Rich library |

### Related Documentation

- [Exa Tool User Guide](./exa_tool.md)
- [Strands Tools README](../README.md)
- [Exa API Documentation](https://docs.exa.ai)
- [Strands SDK Documentation](https://github.com/strands-agents/sdk-python)

### Version History

- **v1.0.0** (2024-01): Initial Exa integration
- Current implementation: 571 lines, 2 main tools, comprehensive error handling

---

**Document Information**:
- **Created**: 2024
- **Last Updated**: 2024
- **Status**: Current
- **Maintainers**: Strands Tools Team
