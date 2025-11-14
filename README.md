# AI Engineer MCP Server

This project tests your ability to build MCP (Model Context Protocol) servers that enhance Claude Desktop's capabilities for marketing professionals.

## Overview

You will implement **one tool** that loads and organizes marketing data for LLM analysis. The tool should retrieve structured data that Claude can then analyze to provide insights to marketers.

## Requirements

### Tool: `get_email_campaign_data`

**Purpose**: Retrieve and organize email campaign data for LLM analysis.

**Parameters**:
- `query_type` (str): Type of data to retrieve ("all", "performance", "subjects", "metrics")
- `campaign_name` (str, optional): Specific campaign to analyze

**Expected Behavior**:
- Load and parse `mock_data.json`
- Extract email campaign data and organize by query type
- Return structured data for LLM analysis
- Handle different query types:
  - "performance": conversion rates, open rates, revenue data
  - "subjects": subject lines and their performance metrics
  - "metrics": comprehensive campaign metrics
  - "all": complete campaign data

## Technical Requirements

1. **Data Loading**: Implement JSON file loading with error handling
2. **Data Processing**: Parse and organize marketing data structures
3. **Query Handling**: Filter and format data based on parameters
4. **Error Handling**: Graceful handling of missing data, invalid inputs
5. **Structured Output**: Return data in a format suitable for LLM analysis

## Example Usage

When a marketer asks Claude: *"What's our best performing email campaign?"*

1. Claude calls `get_email_campaign_data(query_type="performance")`
2. Your tool returns structured data about campaign performance
3. Claude analyzes the data and provides insights to the user

## Evaluation Criteria

- **Data Loading**: Proper JSON parsing and error handling
- **Data Organization**: Logical structure and filtering capabilities
- **Code Quality**: Clean, readable, well-documented code
- **Error Handling**: Robust handling of edge cases
- **Marketing Relevance**: Data structure useful for marketing analysis

## Getting Started

1. **Clone and Setup**:
   - Clone this repository to your local machine
   - Navigate to the `ai-engineer/` directory
   - Install dependencies: `pip install -r requirements.txt`

2. **Implementation**:
   - Implement the `get_email_campaign_data` function in `server.py`
   - Test your implementation using the MCP Inspector
   - Ensure the server runs with: `python server.py`

3. **Submission**:
   - Create your own GitHub repository with your implementation
   - Add `thogg@hubspot.com` as a collaborator to your repository
   - Include a comprehensive README in your repo (see Submission Requirements below)

## Files

- `server.py`: Your implementation (TODO sections to complete)
- `mock_data.json`: Marketing data for testing
- `requirements.txt`: Python dependencies
- `README.md`: This file

## Time Limit

Complete this project in **2 hours or less**.

## Interview Preparation

**Important**: After completing your implementation, be prepared to:

1. **Walk through your code**: Explain your implementation approach and design decisions
2. **Discuss trade-offs**: Explain why you chose certain approaches over alternatives
3. **Handle edge cases**: Demonstrate how your solution handles various scenarios
4. **Explain data processing**: Walk through how you parse and organize the marketing data
5. **Discuss improvements**: Suggest how you would enhance the solution for production use
6. **Integration knowledge**: Explain how MCP servers integrate with Claude Desktop

During the interview, you may be asked to:
- Modify your implementation live
- Add new features or query types
- Optimize performance or add error handling
- Explain how the tool would work in real marketing scenarios

## Testing Your Implementation

Use the [MCP Inspector](https://modelcontextprotocol.io/docs/tools/inspector) to test your server implementation:

```bash
# Test your server with the MCP Inspector
npx @modelcontextprotocol/inspector python server.py
```

The Inspector provides:
- **Tools tab**: Test your `get_email_campaign_data` tool with different parameters
- **Server connection**: Verify your server starts correctly with stdio transport
- **Notifications pane**: Monitor server logs and error messages

Example test scenarios:
- Test with `query_type="performance"` to verify performance data retrieval
- Test with `campaign_name="Q1 Product Launch"` to verify filtering
- Test with invalid parameters to verify error handling

**For Interview**: Be prepared to provide the correct Claude Desktop configuration for your implementation so the interviewer can test it with actual Claude Desktop.

## Submission Requirements

When you create your own GitHub repository, include a comprehensive README.md that contains:

### 1. MCP Server Breakdown
Provide a clear explanation of your implementation including:
- **Architecture Overview**: How your MCP server is structured
- **Tool Implementation**: Detailed explanation of the `get_email_campaign_data` function
- **Data Processing Logic**: How you parse and organize the mock_data.json
- **Query Type Handling**: How you handle different query_type parameters ("all", "performance", "subjects", "metrics")
- **Error Handling**: Your approach to handling edge cases and invalid inputs
- **Design Decisions**: Why you chose certain implementation approaches

### 2. Claude Desktop Configuration
Your implementation uses **stdio transport**. You'll need to:

- Research and determine the correct Claude Desktop configuration for stdio-based MCP servers
- Figure out the proper structure for the `claude_desktop_config.json` file
- Include the working configuration in your README that allows Claude Desktop to connect to your server

**Challenge**: Part of this exercise is determining the correct Claude Desktop configuration for your stdio implementation. Your README should include the exact configuration that works with your server.

**Hint**: stdio-based servers are launched as processes that Claude Desktop manages directly.

### 3. Installation and Usage Instructions
- Step-by-step setup instructions
- How to install dependencies
- How to run the server
- Example usage scenarios with Claude Desktop

### 4. Testing and Validation
- How to test your implementation
- Expected behavior for different query types
- Sample outputs for each query_type parameter

### Repository Setup
1. Create a new **private** GitHub repository
2. Copy your implementation files to the repository
3. Add `thogg@hubspot.com` as a collaborator
4. Ensure your README.md contains all the above requirements
5. Test that the Claude Desktop configuration works before submission

### Final Submission
1. **GitHub Repository**: Ensure your repository is set up as described above
2. **Zip File**: Create a zip file containing your complete solution and share it with your recruiter
3. **Confirmation**: Send the GitHub repository link and zip file to your recruiter before the deadline 