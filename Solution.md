# AI Engineer MCP Server Project Submission

This document contains the complete, working solution for the `server.py` implementation, fulfilling all project requirements, along with the full setup and testing walkthrough.

-----

## 1\. Project Requirements and File Overview

The solution implements the logic for the `get_email_campaign_data` function within `server.py`.

| File | Role | Modification Required |
| :--- | :--- | :--- |
| `server.py` | Main Python script with data logic and MCP tool definition. | **YES** (Implementation provided below) |
| `mock_data.json` | Contains the input marketing data to be processed. | NO |
| `requirements.txt` | Lists necessary Python libraries (`mcp`, `fastmcp`). | NO |

-----

## 2\. Complete `server.py` Solution

Replace the contents of your existing `server.py` file with the complete, working Python code below.

```python
#!/usr/bin/env python3
"""
MCP Server for AI Engineer Role
Transport: stdio
Tools: 1 custom tool
"""

import json
import logging
import re
import sys
from pathlib import Path

from mcp.server.fastmcp import FastMCP

# Configure logging
logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)

# Define the path to the mock data file
MOCK_DATA_PATH = Path(__file__).parent / "mock_data.json"

def load_campaign_data():
    """Load and parse the mock_data.json file."""
    try:
        with open(MOCK_DATA_PATH, 'r') as f:
            data = json.load(f)
            return data.get("campaign_data", {}).get("campaigns", [])
    except FileNotFoundError:
        logger.error(f"Error: mock_data.json not found at {MOCK_DATA_PATH}")
        return None
    except json.JSONDecodeError:
        logger.error(f"Error: Could not decode JSON from {MOCK_DATA_PATH}")
        return None
    except Exception as e:
        logger.error(f"An unexpected error occurred during data loading: {e}")
        return None

def calculate_performance_metrics(campaign):
    """Calculates open rate, click-through rate, and conversion rate for a campaign."""
    metrics = campaign.get("metrics", {})
    sent = metrics.get("sent", 0)
    opened = metrics.get("opened", 0)
    clicked = metrics.get("clicked", 0)
    converted = metrics.get("converted", 0)
    
    # Calculate rates, avoiding division by zero
    open_rate = (opened / sent) * 100 if sent > 0 else 0
    click_rate = (clicked / opened) * 100 if opened > 0 else 0
    conversion_rate = (converted / clicked) * 100 if clicked > 0 else 0

    return {
        "open_rate": f"{open_rate:.2f}%",
        "click_through_rate": f"{click_rate:.2f}%",
        "conversion_rate": f"{conversion_rate:.2f}%",
        "revenue": metrics.get("revenue", 0)
    }

# Create an MCP server
mcp = FastMCP("ai-engineer-mcp-server")

@mcp.tool()
def get_email_campaign_data(query_type: str = "all", campaign_name: str = None) -> str:
    """
    Retrieve email campaign data from a marketing database for analysis.
    Provides access to campaign performance metrics, audience data, and conversion rates.

    Args:
        query_type: Type of data to retrieve ("all", "performance", "subjects", "metrics")
        campaign_name: Specific campaign to analyze (optional)

    Returns:
        Structured email campaign data with metrics, audience information, and performance indicators, 
        formatted as a JSON string for Claude analysis.
    """
    logger.info(f"Tool called with query_type: {query_type}, campaign_name: {campaign_name}")
    
    # 1. Load and parse the mock_data.json file
    campaigns = load_campaign_data()
    if campaigns is None:
        return json.dumps({"error": "Failed to load campaign data."})

    # 7. Handle campaign_name filtering if provided
    if campaign_name:
        campaigns = [
            c for c in campaigns 
            if c.get("name", "").lower() == campaign_name.lower()
        ]
        if not campaigns:
            return json.dumps({"error": f"No campaign found with name: {campaign_name}"})

    results = []
    query_type = query_type.lower()
    
    # 4. For "performance" - return conversion rates, open rates, revenue data
    if query_type == "performance":
        for campaign in campaigns:
            performance_data = {
                "campaign_name": campaign.get("name"),
                "status": campaign.get("status"),
                **calculate_performance_metrics(campaign)
            }
            results.append(performance_data)
            
    # 6. For "metrics" - return comprehensive campaign metrics
    elif query_type == "metrics":
        for campaign in campaigns:
            metrics_data = {
                "campaign_name": campaign.get("name"),
                "status": campaign.get("status"),
                "duration_days": campaign.get("duration_days"),
                "audience": campaign.get("audience"),
                "metrics": campaign.get("metrics")
            }
            results.append(metrics_data)

    # 5. For "subjects" - return subject lines and their performance metrics (with a note)
    elif query_type == "subjects":
        # The current mock_data.json does not contain subject line data.
        for campaign in campaigns:
            results.append({
                "campaign_name": campaign.get("name"),
                "note": "Subject line data is not available in the current mock data structure.",
                "performance_summary": calculate_performance_metrics(campaign)
            })

    # "all" or invalid query_type defaults to "all"
    elif query_type == "all":
        results = campaigns
    
    else:
        return json.dumps({
            "error": f"Invalid query_type: {query_type}. Supported types are 'all', 'performance', 'subjects', 'metrics'."
        })

    # 8. Format the response as structured text for Claude analysis (JSON string)
    return json.dumps(results, indent=2)

if __name__ == "__main__":
    try:
        logger.info("Starting AI Engineer MCP Server...")
        # Run the server with stdio transport
        mcp.run(transport="stdio")
    except KeyboardInterrupt:
        logger.info("Server stopped by user")
    except Exception as e:
        logger.error(f"Server error: {e}")
        sys.exit(1)
```

-----

## 3\. Implementation Logic Breakdown

| Logic Block | What it does | Why it's needed (Requirements) |
| :--- | :--- | :--- |
| `MOCK_DATA_PATH` | Defines the correct file path using `pathlib.Path` for reliable file finding. | **Requirement 1** (Locate `mock_data.json`) |
| `load_campaign_data()` | Opens `mock_data.json`, parses it, and returns a clean list of campaigns, including robust error handling. | **Requirement 1** (Load data) |
| `calculate_performance_metrics()` | Calculates Open Rate, Click-Through Rate, and Conversion Rate percentages, with checks to **prevent division by zero**. | **Requirement 4** (Return conversion/open rates) |
| Data Load & Error Check | Calls `load_campaign_data` and returns a JSON error message if the data fails to load. | **Requirement 1 & 3** (Return structured data, even on error) |
| Filtering by Name | Filters the campaigns where the name matches the `campaign_name` argument (case-insensitively). | **Requirement 7** (Handle `campaign_name` filtering) |
| `query_type` Logic | Uses an `if/elif/else` block to handle the different data return paths. | **Requirement 2** (Organize by `query_type`) |
| `if query_type == "performance"` | Returns rates, revenue, and status for analysis. | **Requirement 4** (Performance data) |
| `elif query_type == "metrics"` | Returns raw metrics object along with campaign details. | **Requirement 6** (Comprehensive metrics) |
| `elif query_type == "subjects"` | Returns a graceful note about missing subject lines along with the performance summary. | **Requirement 5** (Subject lines—handled gracefully) |
| Final Return | Converts the results list to a formatted JSON string using `json.dumps(results, indent=2)`. | **Requirement 3 & 8** (Format as structured text) |

-----

## 4\. Setup and Testing Walkthrough

### Step 4.1: Project Preparation and Dependencies

1.  **Project Setup:** Ensure `server.py`, `mock_data.json`, and `requirements.txt` are in a folder named `ai-engineer-mcp-server`.

2.  **Open Terminal:** Open your computer's terminal and navigate to your project folder:

    ```bash
    # Replace 'path/to/your/folder' with the actual path
    cd path/to/your/ai-engineer-mcp-server
    ```

3.  **Install Python Dependencies:**

    ```bash
    pip install -r requirements.txt
    ```

### Step 4.2: Local Testing with MCP Inspector

1.  **Install MCP Inspector (Requires Node.js/npm):**
    ```bash
    npm install -g @modelcontextprotocol/inspector
    ```
2.  **Test the Server:** Run your server using the Inspector:
    ```bash
    npx @modelcontextprotocol/inspector python server.py
    ```
    *Inside the Inspector, go to the **Tools** tab and test:*
      * **Tool:** `get_email_campaign_data`
      * **Parameter 1:** `query_type = performance` (to verify calculated rates)
      * **Parameter 2:** `campaign_name = Q1 Product Launch` (to verify filtering)

-----

## 5\. Connect to Claude Desktop

### Step 5.1: Find the Absolute Path

Identify the absolute path to your project folder (`ai-engineer-mcp-server`):

  * **Windows:** `C:\Users\YourName\Documents\ai-engineer-mcp-server`
  * **macOS/Linux:** `/Users/YourName/Documents/ai-engineer-mcp-server`
    **Copy this path.**

### Step 5.2: Edit the Claude Desktop Configuration

1.  Open **Claude Desktop** and access **Settings** -\> **Developer** tab.
2.  Click the **Edit Config** button to open `claude_desktop_config.json`.
3.  Paste the following JSON structure, **replacing the `working_directory` placeholder** with the absolute path you copied in Step 5.1.

<!-- end list -->

```json
{
  "tools": [
    {
      "type": "mcp",
      "id": "ai-engineer-mcp-server",
      "name": "Marketing Data Tool",
      "description": "Retrieves structured email campaign data (performance, metrics, subjects) for LLM analysis.",
      "transport": {
        "type": "stdio",
        "command": [
          "python",
          "server.py"
        ],
        "working_directory": "/path/to/your/ai-engineer-mcp-server/directory" 
        // 🚨 REPLACE THIS PATH 🚨
      }
    }
  ]
}
```

### Step 5.3: Restart and Use

1.  Completely quit and **Restart Claude Desktop**.
2.  If successful, the tool will load and be ready for use by the AI model.

-----

## 6\. Example Usage in Claude

| Marketer Prompt | Expected Claude Action |
| :--- | :--- |
| "What's our best performing email campaign in terms of conversion rate?" | Claude calls `get_email_campaign_data(query_type="performance")`, analyzes the results, and provides the answer. |
| "Can you give me the raw metrics and audience data for the Mid-Market Webinar campaign?" | Claude calls `get_email_campaign_data(query_type="metrics", campaign_name="Mid-Market Webinar")`, then summarizes the data. |

```
```
