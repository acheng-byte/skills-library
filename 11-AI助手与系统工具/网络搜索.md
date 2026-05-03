---
name: web-search
description: Search the web with AI-powered summaries. Use when you need current information, recent news, documentation references, or to find specific websites and resources.
tools: Bash, Read, Write
---

# Web Search Skill

Search the web to find current information, documentation, news, and online resources. This skill uses Brave Search API to provide AI-generated summaries along with relevant web pages.

## When to Use

- **Current Information**: Get up-to-date facts, statistics, or news
- **Documentation**: Find official documentation, API references, or guides
- **Research**: Look up technical topics, best practices, or comparisons
- **Recent Events**: Check latest news, releases, or announcements
- **Website Discovery**: Find specific tools, libraries, or services
- **Fact Checking**: Verify information or get multiple perspectives

## How It Works

This skill includes a `scripts/web_search.py` script that calls the Worker API `/api/tool/web-search` endpoint, which integrates with Brave Search.

### Basic Usage

```bash
python scripts/web_search.py "your search query"
```

### Examples

**Simple search:**
```bash
python scripts/web_search.py "React 19 new features"
```

**More results:**
```bash
python scripts/web_search.py "TypeScript best practices" --count 15
```

**Without AI summary (faster):**
```bash
python scripts/web_search.py "Vite configuration" --no-summary
```

**Get raw JSON output:**
```bash
python scripts/web_search.py "Cloudflare Workers" --json
```

## Parameters

- `query` (required): The search query string
- `--count`, `-c`: Number of results (1-20, default: 10)
- `--no-summary`: Disable AI-generated summary
- `--no-entity-info`: Disable entity information
- `--json`: Output raw JSON response

## Response Format

The script returns:

1. **Key Findings**: AI-generated summary of search results (if enabled)
2. **Entity Information**: Additional context about the main entity (if available)
3. **Search Results**: List of web pages with:
   - Title
   - URL
   - Description
   - Age (when applicable)

## Usage Patterns

### Pattern 1: Quick Information Lookup

```
User: What are the new features in Next.js 15?
Assistant: Let me search for that information.

[Runs: python scripts/web_search.py "Next.js 15 new features"]

Based on the search results, Next.js 15 introduces...
```

### Pattern 2: Documentation Search

```
User: How do I configure Vite for React TypeScript?
Assistant: I'll search for Vite configuration documentation.

[Runs: python scripts/web_search.py "Vite React TypeScript configuration" --count 5]

Here's what I found about Vite configuration...
[Can follow up with Read tool to fetch specific URLs if needed]
```

### Pattern 3: Comparison Research

```
User: Compare Redux vs Zustand for state management
Assistant: Let me gather information on both.

[Runs: python scripts/web_search.py "Redux vs Zustand comparison 2024" --count 10]

Based on recent articles and discussions...
```

### Pattern 4: Troubleshooting

```
User: I'm getting "Cannot find module" error in TypeScript
Assistant: I'll search for solutions to this error.

[Runs: python scripts/web_search.py "TypeScript cannot find module error fix"]

Here are the common causes and solutions...
```

## Best Practices

### Effective Query Construction

**Good queries:**
- Specific and focused: "React Server Components performance"
- Include context: "TypeScript strict mode configuration"
- Use technical terms: "Cloudflare Workers Durable Objects"
- Include year for current info: "best practices 2024"

**Avoid:**
- Too vague: "programming"
- Too broad: "how to code"
- Ambiguous: "best framework" (for what?)

### Combining with Other Tools

1. **Search first** to get overview and find URLs
2. **Use Read or Bash (curl)** to fetch full content from specific URLs
3. **Use Write** to save important findings

### Handling No Results

If search returns nothing useful:
- Rephrase with different keywords
- Make query more specific or more general
- Try alternative terminology
- Search in English even if original query was in another language

## Environment Variables

The script requires these environment variables (automatically set in sandbox):

- `AGENT_WORKER_BASE_URL`: Worker API URL
- `AGENT_WORKER_SECRET`: Authentication secret
- `FLY_APP_NAME`: Sandbox ID (optional)

## Error Handling

The script handles these error cases:
- **Missing environment variables**: Exits with error message
- **HTTP errors**: Displays error code and message
- **Network errors**: Shows connection error details
- **Invalid response**: Catches and reports JSON parsing errors

## Tips

- Be specific in your queries for better results
- Use `--count` to get more options when initial results aren't helpful
- Disable summary (`--no-summary`) for faster results when you only need URLs
- Use `--json` to parse results programmatically if needed
- Cross-check important information across multiple searches
