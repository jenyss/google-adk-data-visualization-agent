# google-adk-data-visualization-agent

This project is a lightweight AI agent built with Google ADK that helps you analyze and visualize data from Excel files. It supports natural language queries like "Show me a bar chart of revenue by product" and handles everything from data preview to SQL querying and Plotly visualizations.

If you have any questions or would like to collaborate, feel free to reach out to me on [LinkedIn](https://www.linkedin.com/in/jenya-stoeva-60477249/). You're more than welcome!

## How It Works
* Loads Excel data and previews the structure.
* Converts your natural language questions into DuckDB SQL queries.
* Generates interactive Plotly visualizations (bar, line, pie, scatter, sankey, etc.).
* Powered by GPT-4o or Gemini or Claude, with multi-model support via LiteLLM.
* Ideal for exploratory data analysis and fast insights.

## How-To
To run the agent, follow these steps: 

1. *Install dependencies* (at the top of the Notebook)
2. *Add your API keys* to a ```.env``` file:
```
OPENAI_API_KEY=your_openai_key
GOOGLE_API_KEY=your_google_key
```

3. *Setup the session and runner*:

```
session_service = InMemorySessionService()
app_name = "viz_app"
user_id = "jeny"
session_id = "session_viz_001"
session = session_service.create_session(app_name=app_name, user_id=user_id, session_id=session_id)

runner = Runner(agent=root_agent, app_name=app_name, session_service=session_service)
```

4. *Create the user message and run the agent*:
```
user_message = Content(role="user", parts=[Part(text="""
Use file: data_export.xlsx to
    1. Create and display a visualization which shows how the total Forcast flows through the unique groups in Channel and subchannel`
    2. You must follow this sankey flow: Source (Total forecast) -> target (Channel); Source (Channel) -> target (sub-channel)
    3. Visualization name: Total Forecast flow through Channel, Sub Channel

""")])

# Run and display the final response
for event in runner.run(user_id=user_id, session_id=session.id, new_message=user_message):
    if event.is_final_response():
        if event.content and event.content.parts:
            print(event.content.parts[0].text)
        else:
            print("No final text response was returned by the agent.")
```
5. *Choose the LLM you want to use* — configuration happens during agent initialization:
```
llm = LiteLlm(
    model="openai/gpt-4o",
    temperature=0.0
)

root_agent = Agent(
    name="data_agent",
    model=llm, # You can pass a LiteLLM-wrapped model here
    # Example options:
    # model="gemini-2.0-flash-exp"
    # model="gemini-2.5-pro-preview-03-25"
    # model="gemini-2.5-pro-exp-03-25"
    description=(
        "Agent to answer data questions and create visualizations."
    ),
    instruction=(
        """
        You will be given a task to perform. You must follow these exact steps in order: ....
        """
    ),
    tools=[preview_excel_structure, complex_duckdb_query, create_visualization],
    output_key="last_agent_response",
)
```
