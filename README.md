# Tavily MCP Agent for Google ADK

A powerful web search agent built with Google ADK (Agent Development Kit) and Tavily's MCP (Model Context Protocol) integration. This agent enables intelligent web searches with comprehensive source references, optimized for both English and Thai language queries.

## Features

- **Advanced Web Search**: Uses Tavily's search API with MCP integration
- **Comprehensive Source References**: Every search includes detailed source citations
- **Multi-language Support**: Optimized for both English and Thai language searches
- **Domain-Specific Search**: Ability to search within specific websites
- **News-Optimized**: Special handling for news queries with time-based filtering
- **Rich Content Extraction**: Includes raw content and AI-generated summaries

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

## Configuration

The agent is configured in `my_agent/agent.py` with the following key features:

### Search Parameters

The agent automatically uses these optimal parameters for every search:

- **include_answer**: `true` - Provides AI-generated summaries
- **include_raw_content**: `true` - Includes full source content
- **max_results**: `10` - Returns up to 10 results
- **search_depth**: `"advanced"` - Uses advanced search for comprehensive results

### Domain-Specific Searches

You can search within specific domains by mentioning them in your query:

```
สรุปข่าวน้ำท่วมประเทศไทยล่าสุด จาก https://www.thairath.co.th/ และ https://www.bbc.com/thai
```

The agent will automatically extract domains and use the `include_domains` parameter.

### Thai Language Support

For Thai language queries, the agent automatically:
- Sets `topic: "news"` for news-related searches
- Uses `time_range: "week"` for recent news
- Applies advanced search depth for comprehensive coverage

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

**English:**
```
Search for the latest news about artificial intelligence
```

**Thai:**
```
สรุปข่าวน้ำท่วมประเทศไทยล่าสุด
```

**Domain-Specific:**
```
Find recent articles about climate change from bbc.com and theguardian.com
```

### Response Format

The agent will provide responses in this format:

```
[Comprehensive summary of the search results]

## แหล่งที่มา / Sources:

1. **[Article Title]**
   - URL: https://example.com/article
   - สาระสำคัญ: [Key excerpt from the article]

2. **[Article Title]**
   - URL: https://example.com/article2
   - สาระสำคัญ: [Key excerpt from the article]

...
```

## Available Tavily Search Parameters

The Tavily MCP server supports these parameters (automatically configured by the agent):

| Parameter | Type | Description | Default |
|-----------|------|-------------|---------|
| `query` | string | Search query (required) | - |
| `search_depth` | string | "basic" or "advanced" | "advanced" |
| `topic` | string | "general" or "news" | Context-based |
| `include_answer` | boolean | Include AI summary | true |
| `include_raw_content` | boolean | Include raw HTML | true |
| `max_results` | number | Max results (5-20) | 10 |
| `include_domains` | array | Whitelist domains | Auto-detected |
| `time_range` | string | "day", "week", "month", "year" | Context-based |

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
