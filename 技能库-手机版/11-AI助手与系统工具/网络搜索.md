---
name: web-search
description: "使用Brave Search API搜索网络，获取最新信息、文档、新闻和在线资源，支持实时信息查询和事实核查。当用户提到搜索网络、查一下、最新信息、网络搜索、search时触发。"
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

### Basic Usage

```

### Examples

**Simple search:**
```

**More results:**
```

**Without AI summary (faster):**
```

**Get raw JSON output:**
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

Based on the search results, Next.js 15 introduces...
```

### Pattern 2: Documentation Search

```
User: How do I configure Vite for React TypeScript?
Assistant: I'll search for Vite configuration documentation.

Here's what I found about Vite configuration...
[Can follow up with Read tool to fetch specific URLs if needed]
```

### Pattern 3: Comparison Research

```
User: Compare Redux vs Zustand for state management
Assistant: Let me gather information on both.

Based on recent articles and discussions...
```

### Pattern 4: Troubleshooting

```
User: I'm getting "Cannot find module" error in TypeScript
Assistant: I'll search for solutions to this error.

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