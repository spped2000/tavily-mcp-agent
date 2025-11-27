# Tavily MCP Agent for Google ADK

A powerful multi-tool research agent built with Google ADK (Agent Development Kit) and Tavily's MCP (Model Context Protocol) integration. This agent uses **multiple Tavily tools strategically** to provide the most accurate and comprehensive information possible, with special optimization for news extraction from specific websites.

## ✨ Key Features

### 🔍 Multi-Tool Intelligence
- **tavily-search**: Find relevant articles across the web
- **tavily-extract**: Get full content from specific URLs (not just snippets)
- **tavily-map**: Discover all available pages on a website
- **tavily-crawl**: Deep exploration and comprehensive data collection

### 🎯 Advanced Capabilities
- **Parallel Execution**: Search multiple domains simultaneously for faster results
- **Comprehensive Extraction**: Uses search + extract combination for maximum accuracy
- **Complete Content**: Gets full articles, not just snippets or summaries
- **Cross-Domain Analysis**: Aggregates and cross-references information from multiple sources
- **Smart Tool Selection**: Automatically chooses the right tools for each task

### 🌏 Language & Domain Support
- **Multi-language Support**: Optimized for both English and Thai language queries
- **Domain-Specific Search**: Extract news from specific websites (e.g., thairath.co.th, bbc.com/thai)
- **News-Optimized**: Special handling for news queries with time-based filtering
- **Source Attribution**: Clear attribution for every piece of information

## Prerequisites

Before you begin, ensure you have the following installed:

- Python 3.10 or higher
- Node.js and npm (for Tavily MCP server)
- Git (for version control)

## Installation

### 1. Clone the Repository

```bash
git clone <your-repository-url>
cd tavily
```

### 2. Create a Virtual Environment

```bash
# On Windows
python -m venv .venv
.venv\Scripts\activate

# On macOS/Linux
python3 -m venv .venv
source .venv/bin/activate
```

### 3. Install Dependencies

```bash
pip install google-adk
```

### 4. Set Up Environment Variables

Create a `.env` file in the root directory:

```env
TAVILY_API_KEY=your_tavily_api_key_here
```

**To get your Tavily API key:**
1. Visit [https://tavily.com](https://tavily.com)
2. Sign up for a free account
3. Navigate to your dashboard to get your API key

## Project Structure

```
tavily/
├── my_agent/
│   ├── __init__.py
│   ├── agent.py          # Main agent configuration
│   └── .env              # Agent-specific environment variables
├── .env                  # Root environment variables
├── .gitignore           # Git ignore rules
├── tavily_test.ipynb    # Jupyter notebook for testing
└── README.md            # This file
```

## 🚀 How It Works: Multi-Tool Strategy

The agent is configured in `my_agent/agent.py` with an intelligent multi-tool strategy for maximum accuracy and completeness.

### 📋 Execution Strategy for Comprehensive News Extraction

When you ask for news from specific websites, the agent follows this systematic approach:

#### **STEP 1: Parallel Search** ⚡
- Runs **separate searches for EACH domain simultaneously**
- Example: Searching thairath.co.th AND bbc.com/thai at the same time
- Much faster than sequential searches
- Each search uses optimal parameters:
  - `include_answer: true` - AI-generated summaries
  - `include_raw_content: true` - Full source content
  - `max_results: 10` - Comprehensive results
  - `search_depth: "advanced"` - Detailed information
  - `topic: "news"` - For news queries
  - `time_range: "week"` - Recent news

#### **STEP 2: Extract Full Content** 📄
- Identifies top 3-5 articles from each domain
- Uses `tavily-extract` to get **complete article text**
- No more relying on just snippets!
- Ensures maximum accuracy and context

#### **STEP 3: Map Exploration** 🗺️ (Optional)
- For comprehensive coverage requests
- Uses `tavily-map` to discover all news pages
- Explores site structure intelligently

#### **STEP 4: Deep Crawl** 🕷️ (Optional)
- For "all information" requests
- Uses `tavily-crawl` for systematic collection
- Intelligent discovery of relevant content

### 🎯 Domain-Specific Searches

Simply mention websites in your query:

```
สรุปข่าวน้ำท่วมประเทศไทยล่าสุด จาก https://www.thairath.co.th/ และ https://www.bbc.com/thai
```

The agent will:
1. ✅ Extract domains: `["thairath.co.th", "bbc.com"]`
2. ✅ Search both domains in parallel
3. ✅ Extract full content from top articles
4. ✅ Cross-reference and synthesize information
5. ✅ Present organized results by source

### 🌍 Thai Language Optimization

For Thai queries, automatic optimizations:
- `topic: "news"` for news-related searches
- `time_range: "week"` for recent news
- Advanced search depth for comprehensive coverage
- Handles `/thai` or `/th` paths for international sites

## Usage

### Running the Agent with Google ADK

1. **Start the ADK Web UI:**

```bash
cd my_agent
adk web
```

2. **Open your browser** to the displayed URL (typically `http://localhost:8000`)

3. **Start chatting** with the agent using natural language queries

### Example Queries

#### **Basic Search:**
```
Search for the latest news about artificial intelligence
```

#### **Thai Language:**
```
สรุปข่าวน้ำท่วมประเทศไทยล่าสุด
```

#### **Multi-Domain Search (Recommended for Best Results):**
```
สรุปข่าวน้ำท่วมประเทศไทยล่าสุด จาก https://www.thairath.co.th/ และ https://www.bbc.com/thai
```

**What happens:** The agent will:
1. Search thairath.co.th and bbc.com/thai in parallel
2. Extract full content from top articles
3. Cross-reference information
4. Provide comprehensive summary organized by source

#### **Comprehensive Analysis:**
```
Find ALL information about climate change from bbc.com
```

**What happens:** The agent may use:
- `tavily-search` to find relevant articles
- `tavily-map` to discover all climate-related pages
- `tavily-extract` to get full content
- `tavily-crawl` for deep exploration (if needed)

### Response Format

The agent provides **comprehensive, well-organized responses**:

```
[Comprehensive Summary - synthesizes ALL findings from ALL sources]

## 📊 ข้อมูลจากแหล่งต่างๆ / Information by Source

### 🔍 จาก thairath.co.th:
1. **[Article Title]**
   - URL: https://thairath.co.th/article/...
   - วันที่: [Date]
   - สาระสำคัญ: [Key points from article]
   - รายละเอียด: [Full details extracted]

2. **[Article Title 2]**
   - ...

### 🔍 จาก bbc.com/thai:
1. **[Article Title]**
   - URL: https://bbc.com/thai/article/...
   - วันที่: [Date]
   - สาระสำคัญ: [Key points]
   - รายละเอียด: [Full details]

## 🎯 สรุปรวม / Overall Summary
[Cross-referenced analysis, patterns, key findings]

## 🔗 ลิงก์ทั้งหมด / All Links
[All URLs organized by domain]
```

## 🛠️ Available Tavily Tools & Parameters

The agent has access to **4 powerful Tavily tools** via MCP:

### 1️⃣ tavily-search
Find relevant articles and content across the web.

**Key Parameters (auto-configured):**
| Parameter | Type | Description | Agent Default |
|-----------|------|-------------|---------------|
| `query` | string | Search query (required) | User's query |
| `search_depth` | string | "basic" or "advanced" | "advanced" |
| `topic` | string | "general" or "news" | "news" (for news) |
| `include_answer` | boolean | AI-generated summary | true |
| `include_raw_content` | boolean | Full source content | true |
| `max_results` | number | Max results (5-20) | 10 |
| `include_domains` | array | Whitelist specific domains | Auto-extracted |
| `time_range` | string | "day", "week", "month", "year" | "week" (for news) |

### 2️⃣ tavily-extract
Get complete content from specific URLs (not just snippets).

**Parameters:**
- `urls`: Array of URLs to extract from
- `extract_text`: true (get full text)
- `extract_links`: true (get all links)
- `extract_images`: true (get images)

**When used:** After search to get full article content from top URLs.

### 3️⃣ tavily-map
Discover all available pages and structure of a website.

**Parameters:**
- `url`: Base URL to map
- `max_depth`: How deep to explore (default: 2)
- `max_pages`: Maximum pages to discover (default: 50)

**When used:** For comprehensive site exploration requests.

### 4️⃣ tavily-crawl
Deep exploration with extraction and intelligent discovery.

**Parameters:**
- `url`: Base URL to crawl
- `max_depth`: Crawl depth (default: 2)
- `max_pages`: Maximum pages (default: 30)
- `extract`: true (extract content while crawling)
- `intelligent_discovery`: true (AI-powered page discovery)

**When used:** For "all information" or complete analysis requests.

## Customization

### Modifying the Agent

To customize the agent behavior, edit `my_agent/agent.py`:

```python
root_agent = Agent(
    model="gemini-3-pro-preview",  # Change model here
    name="tavily_agent",
    instruction="""...""",  # Modify instructions here
    tools=[...]
)
```

### Changing the Model

Available Google ADK models:
- `gemini-3-pro-preview` (current)
- `gemini-2.5-pro`
- `gemini-2.0-flash`

### Adjusting Search Behavior

Modify the instruction guidelines in `agent.py` to change:
- Default search parameters
- Response formatting
- Language-specific behavior
- Domain filtering rules

## Troubleshooting

### Common Issues

**1. "TAVILY_API_KEY not found"**
- Ensure your `.env` file exists and contains a valid API key
- Check that the virtual environment is activated

**2. "npx command not found"**
- Install Node.js from [https://nodejs.org](https://nodejs.org)
- Ensure npm is in your system PATH

**3. "Connection timeout"**
- Check your internet connection
- Verify your Tavily API key is valid
- Try increasing the timeout in `agent.py` (currently 30 seconds)

**4. Agent not showing references**
- Restart the ADK web server
- Verify the agent.py instructions haven't been modified
- Check the console for any error messages

### Debug Mode

To enable debug logging, modify the agent startup:

```bash
adk web --debug
```

## Development

### Testing with Jupyter Notebook

Use the included `tavily_test.ipynb` notebook to test Tavily API calls directly:

```bash
jupyter notebook tavily_test.ipynb
```

### Code Structure

- **agent.py**: Main agent configuration with instructions and tool setup
- **MCPToolset**: Handles the MCP connection to Tavily server
- **StdioConnectionParams**: Configures the stdio connection for MCP

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

### Development Setup

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## Resources

- [Google ADK Documentation](https://cloud.google.com/vertex-ai/docs/adk)
- [Tavily API Documentation](https://docs.tavily.com)
- [Tavily MCP Server](https://github.com/apappascs/tavily-search-mcp-server)
- [Model Context Protocol](https://modelcontextprotocol.io)

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Acknowledgments

- Google ADK team for the Agent Development Kit
- Tavily for providing the search API
- MCP (Model Context Protocol) community

## Support

For issues and questions:
- Open an issue in this repository
- Check the [Google ADK Documentation](https://cloud.google.com/vertex-ai/docs/adk)
- Visit [Tavily Support](https://docs.tavily.com)

---

**Note**: This agent requires valid API keys for both Google Cloud (for Gemini models) and Tavily (for search functionality). Ensure you have proper API quotas and billing set up for production use.
