# google-adk-data-visualization-agent

🧠 ADK Data Visualization Agent

This project is a lightweight AI agent built using Google ADK that helps you analyze and visualize data from Excel files. It supports natural language queries like "Show me a bar chart of revenue by product" and handles everything from data preview to SQL querying and Plotly visualizations.

✨ What it does
✅ Loads Excel data and previews the structure
📊 Converts your natural language questions into DuckDB SQL queries
📈 Generates interactive Plotly visualizations (bar, line, pie, scatter, sankey, etc.)
🧠 Powered by GPT-4o or Gemini, with multi-model support via LiteLLM
🧪 Ideal for exploratory data analysis, internal dashboards, and fast insights
🔧 How to run
Install dependencies:

pip install google-adk litellm pandas python-dotenv duckdb numpy plotly
Add your API keys to a .env file:

OPENAI_API_KEY=your_openai_key
GOOGLE_API_KEY=your_google_key
Run the agent (sample):

from google.adk.runners import Runner
from google.adk.sessions import InMemorySessionService
from google.genai.types import Content, Part

# Set up your agent and runner (see agent.py for full definition)
...

user_message = Content(role="user", parts=[Part(text="""
Use file: data_export.xlsx to
1. Create and display a visualization which shows how the total Forcast flows through the unique groups in Channel and subchannel
2. You must follow this sankey flow: Source (Total forecast) -> target (Channel); Source (Channel) -> target (sub-channel)
3. Visualization name: Total Forecast flow through Channel, Sub Channel
""")])

for event in runner.run(user_id="jeny", session_id="session_viz_001", new_message=user_message):
    if event.is_final_response():
        print(event.content.parts[0].text)
⚙️ Agent Flow
preview_excel_structure: Inspects and samples your data
complex_duckdb_query: Converts prompts to SQL and queries your data
create_visualization: Displays results in interactive Plotly charts
Stores state between tools via ToolContext
All logic follows clean ReAct-style planning
