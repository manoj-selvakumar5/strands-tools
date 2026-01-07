# Exa + Strands Blog Post Strategy

**Target Publication**: AWS Machine Learning Blog
**Date Prepared**: 2026-01-06
**Collaboration**: AWS Team + Exa Team

---

## Executive Summary

This blog post tells the story of neural search for AI agents through a problem-solution narrative. It emphasizes Exa's unique embeddings-based search capabilities and Strands' model/cloud agnostic framework, showing developers how to build smarter agents that truly understand the web.

**Key Differentiator**: Neural search that understands *meaning*, not just keywords.

---

## Blog Post Structure

**Title**: "Beyond Keyword Search: How Neural Search Unlocks Intelligent AI Agents"

**Length**: ~3,300 words (8 sections)

**Style**: Problem-Solution narrative with practical code examples

**Target Audience**:
- Developers building AI agents
- AI researchers and innovators
- Technical teams evaluating search solutions

---

## Section-by-Section Breakdown

### Section 1: The Keyword Search Problem (400 words)
**Owner**: AWS Team (with Exa input)

**Opening Hook**: "If you've built an AI agent that searches the web, you've probably hit this wall."

**Content**:

1. **Real Scenario 1**: Finding similar companies
   - Goal: Build agent to find companies similar to Anthropic
   - Keyword search returns: News articles about Anthropic, job posts, literal mentions
   - What you need: Semantically similar companies (OpenAI, Cohere, Mistral)
   - The gap: Keyword search can't understand *meaning*

2. **Real Scenario 2**: Style similarity
   - Goal: "Find blog posts with similar writing style to Paul Graham"
   - Keyword search: Returns articles *about* Paul Graham
   - What you need: Articles written in similar style/tone
   - The limitation: String matching vs understanding

3. **Why This Matters**:
   - Agents hallucinate when given irrelevant context
   - Wasted tokens processing bad search results
   - User trust breaks when agents give wrong answers
   - Cost implications of inefficient search

**Key Message**: "Keyword search matches strings. AI agents need to match *understanding*."

---

### Section 2: The Neural Search Breakthrough (500 words)
**Owner**: Exa Team (with AWS review)

**Content**:

1. **What is Neural Search?**
   - Traditional search: match strings (e.g., "Anthropic" in text)
   - Neural search: match *meaning* in vector space
   - Powered by embeddings (concepts as vectors)
   - Similarity measured by semantic closeness, not word overlap

2. **How Exa's Neural Search Works**:
   - Embeddings represent concepts as high-dimensional vectors
   - Semantic similarity computation in vector space
   - Finds results based on what you *mean*, not what you *say*
   - Example: "companies like Anthropic" → finds OpenAI, Cohere (no keyword match needed)

3. **The Auto Mode Innovation**:
   - Intelligently chooses: neural, keyword, or hybrid
   - API decides best approach for your query
   - Factual queries → keyword search
   - Conceptual queries → neural search
   - No manual decision needed

4. **Visual: Vector Space Diagram**
   - Show "companies similar to Anthropic" in embedding space
   - Closer vectors = more semantically similar
   - Visual clustering: OpenAI, Cohere, Mistral group together
   - Despite no keyword overlap with "Anthropic"

**Key Message**: "Neural search doesn't match words. It matches *understanding*. This changes what's possible for AI agents."

**Deliverable**: Vector space visualization diagram (Owner: Exa team)

---

### Section 3: Strands Agents SDK - The Model-Agnostic Foundation (400 words)
**Owner**: AWS Team

**Critical Message**: Strands is model and cloud agnostic (not AWS-locked)

**Content**:

1. **What is Strands Agents SDK?**
   - Open source, model-driven framework for building AI agents
   - **Model agnostic**: Works with any LLM
     - Claude (Anthropic direct or Bedrock)
     - GPT (OpenAI or Azure OpenAI)
     - Llama, Mistral, Command R, etc.
   - **Cloud agnostic**: Deploy anywhere
     - AWS (Bedrock, SageMaker, EC2)
     - GCP, Azure, on-premises
   - Code-first, lightweight, production-ready

2. **Why Strands + Exa?**
   - Strands: Agent orchestration and tool management
   - Exa: Intelligent web understanding via neural search
   - Together: Build agents that truly understand the web

3. **Simple Integration Example**:

```python
from strands import Agent
from strands_tools import exa

# Works with any model
agent = Agent(
    name="Research Assistant",
    tools=[exa],
    model="anthropic.claude-3-5-sonnet-20241022-v2:0"  # Or any other model
)
```

4. **First Search**:

```python
# Your first neural search
result = agent.tool.exa_search(
    query="AI safety research organizations",
    type="neural",  # Use semantic search
    num_results=5
)
```

**Key Message**: "Strands works with any model, any cloud. Exa brings neural search to any agent you build."

---

### Section 4: Use Case 1 - Market Intelligence Agent (600 words)
**Owner**: Joint (Exa leads code examples, AWS provides context)

**Scenario**: Find and analyze companies similar to a target company

**The Challenge**:
- Keyword search only finds companies that mention target by name
- Need: Semantic similarity in business model, technology, market
- Traditional APIs fail at conceptual matching

**The Solution with Neural Search**:

```python
from strands import Agent
from strands_tools import exa

agent = Agent(
    name="Market Intelligence Agent",
    tools=[exa],
    model="your-model-of-choice"
)

# Find semantically similar companies
result = agent.tool.exa_search(
    query="AI infrastructure startups similar to Anthropic",
    category="company",  # Optimize for company websites
    type="neural",       # Semantic similarity
    num_results=10,
    summary={
        "query": "Company overview, funding stage, and tech focus",
        "schema": {
            "type": "object",
            "properties": {
                "company_name": {"type": "string"},
                "funding_stage": {"type": "string"},
                "primary_technology": {"type": "string"},
                "market_focus": {"type": "string"}
            }
        }
    }
)
```

**What Makes This Powerful**:

1. **Neural Search**
   - Finds similar companies (OpenAI, Cohere, Mistral)
   - No keyword "Anthropic" needed in results
   - Understands semantic similarity in business models

2. **Structured Summaries with Custom Schemas**
   - Agent gets typed, structured data (not raw text)
   - Define exact fields you need
   - JSON schema ensures consistency

3. **Category Optimization**
   - `category="company"` optimizes for company websites
   - Better quality results for business intelligence

4. **One API Call**
   - Search + content extraction + structuring
   - No multi-step pipeline needed

**Results Preview**:

```json
{
  "company_name": "Cohere",
  "funding_stage": "Series C",
  "primary_technology": "Large Language Models",
  "market_focus": "Enterprise AI Platform"
}
```

**Key Message**: "Neural search finds connections keyword search misses. Structured output gives your agent clean, typed data ready for processing."

**Deliverable**: Screenshot of structured summary output (Owner: Exa team)

---

### Section 5: Use Case 2 - Code Documentation Agent (500 words)
**Owner**: Joint (AWS leads, Exa provides code examples)

**Scenario**: Find relevant documentation and code examples for technical concepts

**The Challenge**:
- Searching "async patterns in Python" with keywords
- Results: Random blog posts, Stack Overflow, inconsistent quality
- Need: Authoritative docs, modern patterns, working examples
- Keyword search can't understand *conceptual relevance*

**The Solution**:

```python
# Build a technical documentation agent
doc_agent = Agent(
    name="Documentation Assistant",
    tools=[exa],
    model="your-model-of-choice"
)

# Semantic code search
result = doc_agent.tool.exa_search(
    query="Python async/await best practices and common patterns",
    category="github",  # Focus on code repos and docs
    include_domains=["github.com", "docs.python.org", "realpython.com"],
    type="auto",  # Let Exa choose best approach
    text={"maxCharacters": 2000},  # Get substantial content
    summary={"query": "Key patterns, pitfalls, and code examples"}
)
```

**What Makes This Different**:

1. **Semantic Understanding**
   - Finds conceptually relevant docs, not just keyword matches
   - Understands relationships between async, concurrency, coroutines
   - Discovers related concepts automatically

2. **Category Search (github)**
   - Optimizes specifically for code repositories
   - Better relevance for technical content
   - Finds real-world implementations

3. **Domain Filtering**
   - Focus on trusted, authoritative sources
   - Combine official docs with community best practices
   - Filter noise from low-quality sources

4. **Custom Summaries**
   - Automatically extract key patterns
   - Identify common pitfalls
   - Surface actionable insights

**Results You Get**:
- Official Python documentation on async/await
- Real-world patterns from popular repositories
- Common mistakes and how to avoid them
- Working code examples ready to use

**Key Message**: "Code search that understands what you're trying to learn, not just what keywords you used."

---

### Section 6: Use Case 3 - Research Literature Discovery (400 words)
**Owner**: Exa Team

**Scenario**: Find relevant research papers on a topic

**The Challenge**:
- Academic keyword search misses semantically related work
- Different terminology for same concepts across papers
- Examples: RAG vs grounding vs factuality (all about reducing hallucinations)
- Need: Conceptual similarity, not keyword overlap

**The Solution**:

```python
# Build a research discovery agent
research_agent = Agent(
    name="Research Assistant",
    tools=[exa],
    model="your-model-of-choice"
)

# Academic research search
result = research_agent.tool.exa_search(
    query="Techniques for reducing hallucinations in large language models",
    category="pdf",  # Focus on PDFs (research papers)
    type="neural",   # Semantic similarity crucial for research
    start_published_date="2023-01-01T00:00:00.000Z",  # Recent work only
    num_results=15,
    summary={"query": "Main approach, key findings, and evaluation metrics"}
)
```

**What Makes This Valuable**:

1. **Semantic Paper Discovery**
   - Finds papers using different terminology
   - "hallucinations" = "factuality" = "grounding" = "RAG"
   - Discovers related approaches across naming conventions

2. **Conceptual Similarity**
   - Understands research connections
   - Links related techniques and methods
   - Discovers papers you wouldn't find with keywords

3. **Date Filtering**
   - Focus on recent advances
   - Filter to specific time periods
   - Track evolution of research

4. **Structured Summaries**
   - Extract main approaches automatically
   - Identify key findings
   - Surface evaluation metrics

**Key Message**: "Discover research connections that keyword search can't see. Neural search understands concepts, not just terms."

---

### Section 7: Advanced Capabilities (500 words)
**Owner**: Joint (Exa technical details, AWS integration points)

**7.1 Subpage Discovery** (150 words)

Sometimes you need to explore related pages automatically.

```python
# Find and crawl related subpages
result = agent.tool.exa_search(
    query="Stripe API documentation",
    subpages=3,  # Crawl 3 related pages per result
    subpage_target=["authentication", "webhooks", "examples"]
)
```

**Benefits**:
- Automatically discovers relevant subpages
- Targeted crawling with keywords
- Deep research in a single call
- Comprehensive documentation coverage

---

**7.2 Live Crawl Strategies** (150 words)

Handle dynamic content and edge cases with flexible crawling.

```python
# Smart crawling with fallback
result = agent.tool.exa_get_contents(
    urls=["https://example.com/docs"],
    livecrawl="preferred",  # Try live crawl, fallback to cache
    text={"maxCharacters": 5000}
)
```

**Four Strategies**:
- `never`: Only use cached content (fastest)
- `fallback`: Cache first, crawl if unavailable (recommended)
- `always`: Always perform live crawl (freshest)
- `preferred`: Try live, fall back to cache (balanced)

**Benefits**:
- Handles sites that block crawlers
- Balance freshness vs reliability
- Graceful degradation

---

**7.3 Context Formatting for LLMs** (100 words)

Get results pre-formatted for optimal LLM consumption.

```python
result = agent.tool.exa_search(
    query="...",
    context=True  # Pre-format for LLM context
)
```

**Benefits**:
- Results optimized for LLM understanding
- Reduced token usage
- Ready to feed directly into prompts
- No manual formatting needed

---

**7.4 Model & Cloud Agnostic Integration** (100 words)

Deploy anywhere with any model.

```python
from strands import Agent
from strands_tools import exa

# Works with any LLM
agent = Agent(
    tools=[exa],
    model="anthropic.claude-3-5-sonnet-20241022-v2:0"  # Bedrock
    # OR: "gpt-4o"  # OpenAI/Azure
    # OR: "claude-3-5-sonnet-20241022"  # Anthropic Direct
    # OR: any other model
)
```

**Deployment Options**:
- AWS: Bedrock, SageMaker, EC2
- Azure: Azure OpenAI Service
- GCP: Vertex AI
- On-premises: Your infrastructure
- Local: Open source models

**Key Message**: "Production-ready features for real-world agent deployments across any cloud, any model."

**Deliverable**: Architecture diagram showing Strands + Exa + cloud options (Owner: AWS team)

---

### Section 8: Getting Started & Next Steps (400 words)
**Owner**: Joint

**8.1 Quick Start** (200 words)

Get up and running in 3 steps:

**Step 1: Install Strands Agents SDK**
```bash
pip install strands-agents strands-tools
```

**Step 2: Get Exa API Key**
- Visit: https://dashboard.exa.ai/api-keys
- Create free account and generate API key
- Set environment variable:
```bash
export EXA_API_KEY=your_key_here
```

**Step 3: Build Your First Agent**
```python
from strands import Agent
from strands_tools import exa

agent = Agent(
    name="My Research Agent",
    tools=[exa],
    model="your-model-of-choice"  # Any LLM
)

# Run your first neural search
result = agent.tool.exa_search(
    query="Your semantic search query",
    type="auto"  # Let Exa choose the best approach
)

print(result)
```

---

**8.2 Production Considerations** (100 words)

**Enterprise Features**:
- SOC2 Type II certified
- Zero data retention options available
- DPAs for enterprise customers

**Scale**:
- 5-2000 queries per second throughput
- Up to 10,000 results per search
- Global availability

**Cost Transparency**:
- Detailed cost breakdown by operation type
- See exactly what you're paying for (search, content, summaries)
- Predictable pricing model

**Support**:
- Community Discord and forums
- Enterprise support available
- Comprehensive documentation

---

**8.3 Resources** (100 words)

**Documentation**:
- Exa API Docs: https://docs.exa.ai
- Strands SDK Docs: https://strandsagents.com
- Strands Tools GitHub: https://github.com/strands-agents/tools

**Examples & Community**:
- Example Code Repository: [Link to be created]
- Community Discord: [Link]
- Blog posts and tutorials: [Link]

**Get Your API Key**:
- Exa Dashboard: https://dashboard.exa.ai/api-keys

**Deliverable**: Screenshot of Exa dashboard/API key setup (Owner: Exa team)

---

**8.4 What to Build Next**

Ideas for your neural search agents:

**Business Intelligence**:
- Competitive intelligence agents
- Market analysis and trend detection
- Company research and profiling

**Developer Tools**:
- Technical documentation assistants
- Code example discovery
- API integration helpers

**Research & Learning**:
- Academic literature discovery
- Learning path creators
- Research synthesis agents

**Content & Media**:
- Content recommendation engines
- Semantic content discovery
- Topic exploration tools

**Key Message**: "Start building smarter agents today. Neural search is just an API call away."

---

## Content Ownership Matrix

| Section | Primary Owner | Support | Words | Key Deliverables |
|---------|---------------|---------|-------|------------------|
| 1. The Problem | AWS | Exa | 400 | Real failure scenarios, compelling problem statement |
| 2. Neural Search | Exa | AWS | 500 | Embeddings explanation, auto mode, vector space diagram |
| 3. Strands Intro | AWS | - | 400 | Model/cloud agnostic messaging, integration code |
| 4. Market Intel | Exa | AWS | 600 | Code walkthrough, structured summaries, results screenshot |
| 5. Code Docs | AWS | Exa | 500 | Code search example, developer-focused messaging |
| 6. Research | Exa | AWS | 400 | Academic search, PDF category demonstration |
| 7. Advanced | Joint | Joint | 500 | Subpages, live crawl, architecture diagram |
| 8. Getting Started | Joint | Joint | 400 | Setup guide, resources, call-to-action |

**Total**: ~3,700 words

---

## Technical Assets Checklist

### Code Examples to Develop & Test:

- [ ] Basic setup and first search (Section 3)
- [ ] Market intelligence with structured summaries (Section 4)
- [ ] Code documentation search with categories (Section 5)
- [ ] Academic research search with PDF category (Section 6)
- [ ] Subpage discovery example (Section 7.1)
- [ ] Live crawl strategies demonstration (Section 7.2)
- [ ] Context formatting example (Section 7.3)
- [ ] Multi-cloud deployment examples (Section 7.4)
- [ ] Complete getting started snippet (Section 8.1)

**Owner**: Joint effort, code review by both teams

---

### Diagrams & Visuals:

**1. Vector Space Visualization** (Section 2)
- **Type**: Diagram
- **Content**: 2D projection of embedding space
- **Shows**: Semantic clustering of similar companies
- **Labels**: Anthropic, OpenAI, Cohere, Mistral positioned by similarity
- **Owner**: Exa team
- **Format**: PNG/SVG, optimized for blog

**2. Architecture Diagram** (Section 7)
- **Type**: Architecture diagram
- **Content**: Strands + Exa + Cloud deployment options
- **Shows**:
  - Strands Agent orchestration
  - Exa API integration
  - Multiple LLM options (Claude, GPT, Llama, etc.)
  - Multiple cloud deployment paths (AWS, GCP, Azure, on-prem)
- **Owner**: AWS team
- **Format**: PNG/SVG, optimized for blog

**3. Search Results Comparison** (Section 1)
- **Type**: Screenshot/visual
- **Content**: Side-by-side keyword vs neural search results
- **Shows**: Same query, different result quality
- **Owner**: Exa team
- **Format**: Screenshot, annotated

**4. Structured Summary Output** (Section 4)
- **Type**: Screenshot/code output
- **Content**: Example JSON output from structured summary
- **Shows**: Clean, typed data ready for agent processing
- **Owner**: Exa team
- **Format**: Code snippet with syntax highlighting

**5. Exa Dashboard** (Section 8)
- **Type**: Screenshot
- **Content**: API key generation page
- **Shows**: How to get started quickly
- **Owner**: Exa team
- **Format**: Screenshot, annotated

---

## Key Messaging Framework

### Primary Messages (Priority Order):

**1. Neural Search Revolution**
- "Neural search understands *meaning*, not just keywords"
- Semantic understanding unlocks capabilities keyword search can't provide
- Real examples showing the difference
- Auto mode makes it effortless

**2. Strands is Model & Cloud Agnostic**
- Works with ANY LLM (Claude, GPT, Llama, Mistral, etc.)
- Deploy ANYWHERE (AWS, GCP, Azure, on-prem)
- NOT locked into AWS ecosystem
- True portability and flexibility

**3. Structured Intelligence**
- Custom JSON schemas for agent consumption
- One API call: search + extract + structure
- Typed data, not raw text
- Production-ready output

**4. Production Enterprise Ready**
- SOC2 certified, enterprise security
- 5-2000 QPS scale
- Zero data retention options
- Simple integration, powerful capabilities

---

### Differentiation from Tavily Blog

**Tavily Positioned As**:
- "Modular web intelligence tools"
- 4 specialized tools (search, extract, crawl, map)
- Composable operations
- Speed and relevance optimization

**Exa Positioned As**:
- "Neural search revolution"
- Semantic understanding via embeddings
- Structured intelligence with custom schemas
- Deep research and code-aware search

**Different Angles**:
- **Tavily**: Breadth of tools and operations
- **Exa**: Depth of understanding and intelligence

**No Overlap**: Both are valuable, serve different needs, can be used together

---

## Timeline & Process

### Phase 1: Validation & Planning (Week 1)
- [ ] Review this outline with Exa team
- [ ] Assign specific writers to sections
- [ ] Set up shared editing environment
- [ ] Create GitHub repo for code examples
- [ ] Schedule weekly sync meetings

### Phase 2: Initial Drafting (Weeks 2-3)
- [ ] Each owner drafts their sections
- [ ] Test all code examples
- [ ] Create diagrams and visuals
- [ ] Share drafts for early feedback

### Phase 3: Integration & Review (Week 4)
- [ ] Integrate all sections into single document
- [ ] Cross-team review for consistency
- [ ] Technical accuracy review
- [ ] Ensure smooth narrative flow

### Phase 4: Editing & Polish (Week 5)
- [ ] Professional editing pass
- [ ] Verify all links and resources
- [ ] Optimize for SEO
- [ ] Final technical review

### Phase 5: Approval & Publication (Week 6)
- [ ] Legal/compliance review
- [ ] Final approvals from both teams
- [ ] Schedule publication date
- [ ] Prepare promotion plan (social, email, etc.)

**Total Timeline**: ~6 weeks from kickoff to publication

---

## Success Metrics

**Engagement Targets**:
- Blog views: 10,000+ in first month
- Code example repository stars: 100+ in first quarter
- Exa API signups from blog: Track conversion rate
- Social shares and discussion

**Quality Indicators**:
- Developer feedback and comments
- Questions in community channels
- Follow-up blog requests
- Integration examples shared by community

**Business Impact**:
- Exa API adoption among Strands users
- AWS blog traffic and engagement
- Joint case studies and success stories
- Future collaboration opportunities

---

## Questions for Meeting Discussion

### Content Strategy:
1. Does this narrative resonate with Exa's brand positioning?
2. Are the three use cases the right choices?
3. Should we add/remove any technical capabilities?
4. Is the tone appropriate for both audiences?

### Process & Logistics:
5. Who are the specific writers from each team?
6. What's the review/approval process for each team?
7. When can we realistically publish?
8. What's the promotion plan post-publication?

### Technical Assets:
9. Can Exa provide the vector space visualization?
10. Should we create a companion GitHub repo with full examples?
11. Do we need video content or just written blog?
12. Are there existing diagrams we can leverage?

### Messaging:
13. Is "model/cloud agnostic" messaging prominent enough?
14. Should we mention pricing/costs in more detail?
15. How do we position alongside (not against) Tavily?
16. What's the call-to-action priority?

---

## Next Steps After Meeting

1. **Immediate** (Day 1-3):
   - Finalize section assignments
   - Set up shared editing environment
   - Create code example repository structure
   - Schedule weekly syncs

2. **Short-term** (Week 1):
   - Begin drafting assigned sections
   - Start developing code examples
   - Commission diagram creation
   - Set up tracking/metrics

3. **Medium-term** (Weeks 2-4):
   - Complete first drafts
   - Cross-review and integrate
   - Finalize visuals
   - Technical accuracy review

4. **Long-term** (Weeks 5-6):
   - Final editing and polish
   - Approvals and compliance
   - Publication and promotion
   - Track engagement and iterate

---

## Reference Materials

**Key Files**:
- Exa integration code: `src/strands_tools/exa.py`
- Tavily integration code: `src/strands_tools/tavily.py`
- Exa tests: `tests/test_exa.py`
- Original briefing: `docs/exa-blog-briefing.md`
- Planning document: `.claude/plans/fuzzy-hatching-pony.md`

**External Resources**:
- Exa API Docs: https://docs.exa.ai
- Strands SDK Docs: https://strandsagents.com
- Tavily AWS Blog: https://aws.amazon.com/blogs/machine-learning/build-dynamic-web-research-agents-with-the-strands-agents-sdk-and-tavily/
- Strands GitHub: https://github.com/strands-agents

---

**Document Version**: 1.0
**Last Updated**: 2026-01-06
**Status**: Ready for Team Review
