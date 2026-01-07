# Exa x Strands Agents SDK Blog Post - Meeting Briefing

**Meeting Purpose**: Collaborate with Exa team on a joint blog post similar to the Tavily-Strands integration blog

**Date Prepared**: 2026-01-06

---

## Executive Summary

This briefing provides comprehensive information about both Exa and Tavily integrations with Strands Agents SDK, key differences between the two search platforms, and a strategic approach for creating a compelling joint blog post.

---

## 1. Understanding the Integrations

### Tavily Integration with Strands Agents SDK

**Integration Status**: Production-ready with official AWS blog post

**Available Tools** (4 tools in `strands-tools`):
- `tavily_search` - Real-time web search optimized for AI agents
- `tavily_extract` - Clean content extraction from URLs
- `tavily_crawl` - Graph-based website traversal with parallel exploration
- `tavily_map` - Website structure mapping and discovery

**Key Integration Details**:
```python
from strands import Agent
from strands_tools import tavily

agent = Agent(tools=[tavily])
result = agent.tool.tavily_search(query="What is artificial intelligence?")
```

**Setup**: Requires `TAVILY_API_KEY` environment variable

### Exa Integration with Strands Agents SDK

**Integration Status**: Production-ready, documented in `strands-tools`

**Available Tools** (2 tools in `strands-tools`):
- `exa_search` - Intelligent web search with neural and keyword modes
- `exa_get_contents` - Advanced URL content extraction with AI summaries

**Key Integration Details**:
```python
from strands import Agent
from strands_tools import exa

agent = Agent(tools=[exa])
result = agent.tool.exa_search(query="Best project management tools", text=True)
```

**Setup**: Requires `EXA_API_KEY` environment variable

**Source Code**: `src/strands_tools/exa.py`

---

## 2. Tavily vs Exa: Capabilities Comparison

### Search Technology

**Tavily**:
- AI-optimized search specifically designed for LLMs/RAG
- Search depths: `basic`, `advanced`, `fast`, `ultra-fast`
- Reviews multiple sources for most relevant content
- Includes automatic LLM-generated answers
- Focus on speed and relevance balance

**Exa**:
- **Unique**: First meaning-based search powered by embeddings
- Search modes: `auto` (recommended), `neural`, `keyword`, `fast`
- **Auto mode**: Intelligently combines neural + keyword search
- **Neural search**: Semantic understanding via embeddings
- Category-specific search (company, news, PDF, GitHub, etc.)
- Deep search with agentic multi-query capability

### Content Extraction

**Tavily**:
- Clean content extraction (markdown/text)
- Extract depth: `basic`, `advanced` (with tables/embedded content)
- Parallel graph-based crawling
- Natural language instructions for targeted crawling
- Category filtering (Careers, Blog, Documentation, etc.)

**Exa**:
- Full page text with customizable character limits
- **Unique**: AI-generated summaries with custom queries
- **Unique**: Structured output with JSON schemas
- Subpage crawling and discovery
- Live crawling with multiple fallback strategies
- Rich metadata extraction

### Advanced Capabilities

**Tavily Only**:
- Website mapping (discover all URLs without content)
- Multi-depth/breadth graph traversal
- Image search with descriptions
- Auto-parameter optimization

**Exa Only**:
- Neural embeddings-based semantic search
- Structured summaries with custom schemas
- Answer API for direct answers
- Websets API for monitoring the web semantically
- Agentic deep research mode
- Code search capabilities
- Company research crawling
- MCP server with advanced tools

### Performance & Scale

**Tavily**:
- 600K+ developers
- Search depths optimize for latency vs relevance tradeoff
- 1,000 free requests on new accounts

**Exa**:
- 5-2000 queries per second throughput
- Up to 10,000 results per search
- SOC2 certified with zero data retention option
- Enterprise DPAs available

---

## 3. Tavily AWS Blog Post Analysis

**Reference**: [Build dynamic web research agents with the Strands Agents SDK and Tavily](https://aws.amazon.com/blogs/machine-learning/build-dynamic-web-research-agents-with-the-strands-agents-sdk-and-tavily/)

### Key Insights from Tavily Blog:

1. **AWS Endorsement**: "AWS chose Tavily as the premier tool for real-time search integration within the Strands Agents SDK"

2. **Production Usage**: Strands is actively used by multiple AWS teams (Kiro, Amazon Q, AWS Glue)

3. **Integration Highlights**:
   - API-first web intelligence layer designed for LLM agents
   - Real-time search, content extraction, structured crawling
   - Built for precision, speed, and modularity
   - Native integration with Amazon Bedrock AgentCore Gateway
   - Available on AWS Marketplace

4. **Blog Structure** (inferred):
   - Introduction to Strands Agents SDK
   - Why web search matters for AI agents
   - Tavily's unique value proposition
   - Technical integration details
   - Use case demonstrations
   - Code examples
   - Enterprise considerations

---

## 4. Exa's Unique Value Propositions

Based on the comparison, here are Exa's key differentiators for the blog:

### 1. **Neural Search Innovation**
- First meaning-based search powered by embeddings
- True semantic understanding beyond keyword matching
- Auto mode intelligently combines approaches

### 2. **Structured Intelligence**
- Custom JSON schemas for structured summaries
- Programmable output formats
- Perfect for agents that need structured data

### 3. **Advanced Research Capabilities**
- Agentic deep research mode
- Deep search with smart query expansion
- Code search and documentation discovery
- Company research with website crawling

### 4. **Flexibility & Control**
- Multiple search modes (auto, neural, keyword, fast)
- Live crawling strategies (never, fallback, always, preferred)
- Custom summary queries
- Subpage targeting and discovery

### 5. **Enterprise Ready**
- SOC2 certified
- Zero data retention options
- High throughput (5-2000 QPS)
- Massive scale (up to 10K results)

---

## 5. Blog Post Strategy & Content Ideas

### Proposed Blog Title Options:

1. "Build Intelligent Research Agents with Neural Search: Strands Agents SDK and Exa Integration"
2. "Semantic Web Search for AI Agents: Integrating Exa's Neural Search with Strands Agents SDK"
3. "Beyond Keyword Search: Building Meaning-Based AI Research Agents with Strands and Exa"
4. "Build Production-Ready Research Agents with Exa's Neural Search and Strands Agents SDK"

### Blog Structure (Following Tavily Template):

#### Section 1: Introduction (AWS Team)
- Brief intro to Strands Agents SDK
- Why web search is critical for AI agents
- The evolution from keyword to neural search
- Introduction to Exa partnership

#### Section 2: What Makes Exa Different (Exa Team Lead)
- Neural search powered by embeddings
- The problem with traditional keyword search
- How Exa's auto mode works
- Real-world examples of semantic vs keyword search

#### Section 3: Integration Deep Dive (Joint - AWS Lead)
- How Exa integrates with Strands
- Code examples showing integration
- Available tools: `exa_search` and `exa_get_contents`
- Configuration and setup

#### Section 4: Use Case 1 - Research Agent (Exa Team Lead)
**Proposed Use Case**: Building a Market Research Agent
- Uses neural search to find similar companies
- Structured summaries with custom schemas
- Code search for competitive technology analysis
- Example code walkthrough

#### Section 5: Use Case 2 - Documentation Assistant (AWS Team Lead)
**Proposed Use Case**: Building a Technical Documentation Assistant
- Semantic code search across GitHub repositories
- Finding up-to-date API documentation
- Structured extraction of code examples
- Integration with Amazon Bedrock

#### Section 6: Advanced Capabilities (Joint)
- Deep search for comprehensive research
- Structured output with JSON schemas
- Live crawling strategies
- Subpage discovery

#### Section 7: Production Considerations (AWS Team)
- Enterprise features (SOC2, data retention)
- Performance characteristics
- Best practices for agent design
- Integration with AWS services

#### Section 8: Getting Started (Joint)
- Setup instructions
- Sample code repository
- Links to documentation
- Call to action

### Content Responsibility Matrix:

| Section | Lead | Support | Content Type |
|---------|------|---------|--------------|
| 1. Introduction | AWS | Exa | Context setting |
| 2. What Makes Exa Different | Exa | AWS | Product education |
| 3. Integration Deep Dive | AWS | Exa | Technical tutorial |
| 4. Use Case 1 - Market Research | Exa | AWS | Demo/walkthrough |
| 5. Use Case 2 - Documentation | AWS | Exa | Demo/walkthrough |
| 6. Advanced Capabilities | Exa | AWS | Feature showcase |
| 7. Production Considerations | AWS | Exa | Enterprise guidance |
| 8. Getting Started | Joint | Joint | Onboarding |

---

## 6. Recommended Use Cases for Blog

### Use Case 1: Market Intelligence Research Agent
**Why This Works**:
- Showcases neural search finding semantically similar companies
- Demonstrates structured summaries with custom schemas
- Highlights category-specific search (company, financial reports)
- Real business value is clear

**Agent Capabilities**:
1. Search for companies in a specific industry
2. Extract structured data (revenue, employee count, tech stack)
3. Find similar competitors using neural search
4. Generate comparative analysis reports

**Code Example**:
```python
from strands import Agent
from strands_tools import exa

# Create market research agent
agent = Agent(
    name="Market Research Agent",
    tools=[exa],
    model="anthropic.claude-3-5-sonnet-20241022-v2:0"
)

# Find companies and extract structured data
result = agent.tool.exa_search(
    query="AI infrastructure startups similar to Anthropic",
    category="company",
    num_results=10,
    summary={
        "query": "Company overview, funding, and technology stack",
        "schema": {
            "type": "object",
            "properties": {
                "company_name": {"type": "string"},
                "funding_stage": {"type": "string"},
                "technology_stack": {"type": "array"},
                "employee_count": {"type": "string"}
            }
        }
    }
)
```

### Use Case 2: Technical Documentation Assistant
**Why This Works**:
- Demonstrates code search capabilities
- Shows value of semantic search for developers
- Highlights integration with Amazon Bedrock
- Practical developer use case

**Agent Capabilities**:
1. Search GitHub repos for code examples
2. Find up-to-date API documentation
3. Extract specific code patterns
4. Generate usage examples

**Code Example**:
```python
from strands import Agent
from strands_tools import exa

# Create documentation assistant
doc_agent = Agent(
    name="Documentation Assistant",
    tools=[exa],
    model="anthropic.claude-3-5-sonnet-20241022-v2:0"
)

# Search for code examples and documentation
result = doc_agent.tool.exa_search(
    query="Python async/await best practices and patterns",
    category="github",
    include_domains=["github.com", "docs.python.org"],
    text={"maxCharacters": 2000},
    summary={"query": "Key patterns and common pitfalls"}
)
```

### Use Case 3: Academic Research Assistant (Alternative)
**Why This Works**:
- Showcases PDF search capability
- Demonstrates deep research mode
- Appeals to academic/research audience
- Shows value of semantic search for papers

---

## 7. Key Messages to Emphasize

### For AWS Audience:
1. **Neural search unlocks new capabilities** beyond keyword matching
2. **Structured output** makes agent development easier
3. **Enterprise-ready** with SOC2 certification and scale
4. **Native integration** with Strands Agents SDK and Amazon Bedrock
5. **Production-proven** with high throughput and reliability

### For Developer Audience:
1. **Simple integration** with just a few lines of code
2. **Flexible search modes** for different use cases
3. **Rich content extraction** with customizable limits
4. **Code search** for finding documentation and examples
5. **Cost-effective** with transparent pricing

### For Technical Decision Makers:
1. **Semantic understanding** reduces hallucinations
2. **Scalable architecture** supports high-volume applications
3. **Security and compliance** with SOC2 and data retention controls
4. **AWS ecosystem integration** with Bedrock and other services
5. **Open source foundation** with Strands Agents SDK

---

## 8. Differentiation from Tavily Blog

To avoid duplication and provide unique value:

### Tavily Blog Focus:
- Real-time search optimization for RAG
- Multi-tool ecosystem (search, extract, crawl, map)
- Speed and relevance balance
- General web research use cases

### Exa Blog Should Focus On:
- **Neural/semantic search** as primary differentiator
- **Structured intelligence** with JSON schemas
- **Code and technical documentation** search
- **Deep research mode** for comprehensive analysis
- **Meaning-based discovery** vs keyword matching

### Unique Angles for Exa:
1. **The Neural Search Revolution**: How embeddings change web search for AI
2. **From Data to Intelligence**: Structured summaries and custom schemas
3. **Code-Aware Search**: Finding technical documentation that keyword search misses
4. **Agentic Research**: Deep search mode that thinks like a researcher

---

## 9. Technical Assets Needed

### Code Examples:
- [ ] Basic search integration example
- [ ] Structured summary with custom schema
- [ ] Code search for GitHub/documentation
- [ ] Deep research agent implementation
- [ ] Complete use case walkthrough (Jupyter notebook?)

### Diagrams:
- [ ] Strands + Exa architecture diagram
- [ ] Neural vs keyword search comparison
- [ ] Agent workflow with Exa tools
- [ ] Integration with Amazon Bedrock flow

### Screenshots/Visuals:
- [ ] Exa API response examples
- [ ] Comparison of neural vs keyword results
- [ ] Structured summary output examples
- [ ] Agent execution traces

---

## 10. Action Items for Meeting

### Questions to Clarify:

1. **Timeline**: What's the target publication date?
2. **Use Cases**: Which use case(s) should we prioritize?
3. **Code Repository**: Should we create a companion GitHub repo with examples?
4. **AWS Services**: Which AWS services should we highlight integration with?
5. **Technical Depth**: How technical should we go? (Developer vs executive audience)
6. **Length**: Target word count? (Tavily blog as reference)
7. **Review Process**: How many review cycles? Who needs to approve?
8. **Promotion**: Cross-promotion plans? (AWS blog, Exa blog, social media)

### Division of Labor:

**AWS Team Should Own**:
- Strands Agents SDK introduction and context
- Integration architecture and setup
- AWS service integration examples
- Blog hosting and publication
- Initial outline and structure

**Exa Team Should Own**:
- Neural search technology explanation
- Exa-specific capabilities deep dive
- Use case demonstrations (with AWS input)
- API examples and best practices
- Technical accuracy review

**Joint Responsibilities**:
- Use case selection and validation
- Code examples and testing
- Technical review and editing
- Diagram creation
- Getting started guide

### Next Steps:
1. Agree on use cases and blog structure
2. Define ownership for each section
3. Create timeline with milestones
4. Set up shared document for collaboration
5. Identify technical reviewers from both teams
6. Plan code repository creation
7. Schedule follow-up sync

---

## 11. Reference Materials

### Existing Documentation:
- Exa integration code: `src/strands_tools/exa.py`
- Exa tool documentation: `docs/exa_tool.md`
- Tavily integration code: `src/strands_tools/tavily.py`
- Tavily AWS blog: [Link](https://aws.amazon.com/blogs/machine-learning/build-dynamic-web-research-agents-with-the-strands-agents-sdk-and-tavily/)

### External Resources:
- Exa API Documentation: https://docs.exa.ai
- Exa Dashboard: https://dashboard.exa.ai
- Strands Agents Documentation: https://strandsagents.com
- Strands Tools GitHub: https://github.com/strands-agents/tools

### Key Differences Summary:

| Feature | Tavily | Exa |
|---------|--------|-----|
| **Search Technology** | AI-optimized keyword + RAG | Neural embeddings + keyword |
| **Primary Strength** | Speed & relevance balance | Semantic understanding |
| **Search Modes** | 4 depth levels | 4 search types (auto/neural/keyword/fast) |
| **Tools in Strands** | 4 tools | 2 tools |
| **Unique Features** | Website mapping, graph crawl | Structured schemas, code search, deep research |
| **Content Extraction** | Markdown/text with tables | Custom summaries with JSON schemas |
| **Category Search** | Crawl categories | Content categories (company, news, PDF, GitHub) |
| **Best For** | Fast RAG retrieval | Semantic discovery & structured intelligence |

---

## Sources

- [Exa API Documentation](https://docs.exa.ai/reference/search)
- [Exa AI Features](https://exa.ai/exa-api)
- [Exa Demos](https://exa.ai/demos)
- [Tavily Documentation](https://docs.tavily.com/)
- [Tavily Search API](https://docs.tavily.com/documentation/api-reference/endpoint/search)
- [AWS Blog: Build dynamic web research agents with Strands Agents SDK and Tavily](https://aws.amazon.com/blogs/machine-learning/build-dynamic-web-research-agents-with-the-strands-agents-sdk-and-tavily/)
- [AWS Blog: Introducing Strands Agents](https://aws.amazon.com/blogs/opensource/introducing-strands-agents-an-open-source-ai-agents-sdk/)
- [Strands Agents Documentation](https://strandsagents.com/latest/)

---

**Prepared by**: AWS Team
**For**: Exa Partnership Meeting
**Status**: Draft for Discussion
