# ✈️ TripMate AI — Multi-Agent Travel Planner with LangGraph & MCP

TripMate AI is an open-source, AI-powered multi-agent travel planning system that transforms natural-language travel requests into practical and structured travel plans.

The project combines **LangGraph, LangChain, MCP (Model Context Protocol), Groq, FastAPI, PostgreSQL, and external travel APIs** to create a safe and reviewable travel-planning pipeline.

It demonstrates how **Multi-Agent Systems, Supervisor Agents, Input Guardrails, MCP Tools, and Human-in-the-Loop (HITL)** can work together in a real-world AI application.

---

## 🌍 Overview

Planning a trip usually requires switching between multiple websites, flight-search platforms, hotel websites, weather applications, and spreadsheets.

TripMate AI brings these tasks together into a single AI-powered workflow.

A user can provide a request such as:

```text
Plan a 5-day trip to Tokyo from Dhaka.

My budget is $2000.
Include flights, hotels, weather, and a day-by-day itinerary.


The system processes the request through a multi-agent workflow:

User Request
     │
     ▼
Input Guardrails
     │
     ▼
Supervisor Agent
     │
     ├──► Flight Agent ──► AviationStack MCP
     │
     ├──► Hotel Agent ───► Tavily MCP
     │
     ├──► Weather Agent ─► Weather MCP
     │
     ▼
Itinerary Agent
     │
     ▼
Draft Travel Plan
     │
     ▼
Human-in-the-Loop Review
     │
     ├──► Approve ──► Final Response
     │
     └──► Revise ──► Agent Workflow

✨ Features
✈️ Flight research using AviationStack
🏨 Hotel suggestions using Tavily Search
🌤️ Weather lookup using a custom MCP server
🧠 Multi-agent orchestration using LangGraph
👨‍💼 Supervisor agent for workflow coordination
🛡️ Input guardrails for validating user requests
👤 Human-in-the-Loop (HITL) approval workflow
📝 Structured day-by-day itinerary generation
🌐 FastAPI backend
💻 HTML/CSS/JavaScript frontend
💾 PostgreSQL conversation/state persistence
⚡ LLM-powered responses using Groq
🔌 MCP-based external tool integrations
🧩 Modular architecture for adding new agents and tools
🧠 Multi-Agent System

TripMate AI uses multiple specialized agents instead of relying on a single agent.

✈️ Flight Agent

Responsible for researching flight information using the AviationStack MCP integration.

🏨 Hotel Agent

Uses Tavily MCP to search for hotel and accommodation recommendations.

🌤️ Weather Agent

Uses the custom weather MCP server to retrieve weather information from OpenWeather.

🗺️ Itinerary Agent

Combines information from the different agents and generates a practical day-by-day itinerary.

🧠 Final Response Agent

Combines the generated results into a structured final travel response.

👨‍💼 Supervisor Agent

The Supervisor Agent coordinates the different agents and manages the overall workflow.

It determines:

Which agent needs to be executed
What information is required
When enough information has been collected
When the itinerary should be generated
When the workflow is ready for human approval

Architecture:

                 ┌──────────────────┐
                 │    Supervisor    │
                 │      Agent       │
                 └────────┬─────────┘
                          │
          ┌───────────────┼───────────────┐
          │               │               │
          ▼               ▼               ▼
      ✈️ Flight       🏨 Hotel        🌤️ Weather
       Agent           Agent            Agent
          │               │               │
          ▼               ▼               ▼
    AviationStack     Tavily MCP      Weather MCP
          │               │               │
          └───────────────┼───────────────┘
                          │
                          ▼
                  🗺️ Itinerary Agent
                          │
                          ▼
                   Final Response

🛡️ Input Guardrails

TripMate AI uses input guardrails to validate requests before they enter the main agent workflow.

The guardrail layer can verify whether:

The request is travel-related
The request contains valid information
Required details are present
The request is supported by the application
The workflow should continue or reject the request

Example:

User Prompt
     │
     ▼
Input Guardrails
     │
     ├── Valid ──────► Supervisor
     │
     └── Invalid ────► Reject Request


This provides an additional safety layer before executing agents and external tools.

👤 Human-in-the-Loop (HITL)

TripMate AI supports Human-in-the-Loop approval for generated travel plans.

Instead of automatically treating every generated plan as final, the workflow can pause and wait for human approval.

The user can:

✅ Approve the generated plan
✏️ Request changes
💬 Provide feedback

Example:

Draft Travel Plan
       │
       ▼
Human Review
       │
   ┌───┴────┐
   │        │
Approve   Revise
   │        │
   ▼        ▼
Final    Feedback
Plan       │
           ▼
      Agent Workflow


This makes the system more controllable and reviewable.

🔌 Model Context Protocol (MCP)

TripMate AI uses the Model Context Protocol (MCP) to connect AI agents with external tools and services.

MCP is used in multiple parts of the project.

🔎 Tavily MCP

Tavily search is accessed through a remote MCP endpoint:

https://mcp.tavily.com/mcp/


It is primarily used for:

Hotel research
Web search
Destination research
Travel information
✈️ AviationStack MCP

Flight information is accessed through a local stdio MCP command:

uvx aviationstack-mcp


The AviationStack API key is configured through the environment variables.

🌤️ Custom Weather MCP Server

The project includes a custom MCP server:

custom_weather_mcp_server.py


The server demonstrates how custom domain-specific tools can be exposed through MCP.

The weather server communicates with OpenWeather and provides weather-related functionality to the Weather Agent.

Run the server with:

python custom_weather_mcp_server.py

🔄 MCP Client

The MCP client is implemented in:

mcp_client.py


It provides asynchronous helper functions for communicating with MCP servers.

Available helper functions include:

tavily_mcp_search
aviation_mcp_call
weather_mcp_search
forecast_mcp_search
extract_destination


The main workflow in backend.py uses these helpers to communicate with the different MCP tools.

🏗️ Architecture

The complete TripMate AI architecture:

                         ┌──────────────────────┐
                         │        USER          │
                         │ Natural Language     │
                         │   Travel Request     │
                         └──────────┬───────────┘
                                    │
                                    ▼
                         ┌──────────────────────┐
                         │    FastAPI App       │
                         │       app.py         │
                         └──────────┬───────────┘
                                    │
                                    ▼
                         ┌──────────────────────┐
                         │   INPUT GUARDRAILS   │
                         │ Validation & Safety  │
                         └──────────┬───────────┘
                                    │
                                    ▼
                         ┌──────────────────────┐
                         │   SUPERVISOR AGENT   │
                         │      LangGraph       │
                         └──────────┬───────────┘
                                    │
              ┌─────────────────────┼─────────────────────┐
              │                     │                     │
              ▼                     ▼                     ▼
     ┌────────────────┐    ┌────────────────┐    ┌────────────────┐
     │  FLIGHT AGENT  │    │   HOTEL AGENT   │    │ WEATHER AGENT  │
     └───────┬────────┘    └───────┬────────┘    └───────┬────────┘
             │                     │                     │
             ▼                     ▼                     ▼
     ┌────────────────┐    ┌────────────────┐    ┌────────────────┐
     │ AviationStack  │    │   Tavily MCP   │    │  Weather MCP   │
     │      MCP       │    │                │    │     Server     │
     └────────────────┘    └────────────────┘    └────────────────┘
             │                     │                     │
             └─────────────────────┼─────────────────────┘
                                   │
                                   ▼
                         ┌──────────────────────┐
                         │  ITINERARY AGENT     │
                         │ Day-by-Day Planning  │
                         └──────────┬───────────┘
                                    │
                                    ▼
                         ┌──────────────────────┐
                         │     DRAFT PLAN       │
                         └──────────┬───────────┘
                                    │
                                    ▼
                         ┌──────────────────────┐
                         │    HUMAN REVIEW      │
                         │        HITL          │
                         └──────────┬───────────┘
                                    │
                          ┌─────────┴─────────┐
                          │                   │
                       APPROVE              REVISE
                          │                   │
                          ▼                   ▼
                   ┌─────────────┐     ┌─────────────┐
                   │ FINAL PLAN  │     │   FEEDBACK  │
                   └─────────────┘     └──────┬──────┘
                                               │
                                               ▼
                                        Updated Workflow

🧰 Tech Stack
Technology	Purpose
Python 3.10+	Application development
FastAPI	Backend API
Jinja2	HTML template rendering
HTML/CSS/JavaScript	Frontend
LangGraph	Multi-agent workflow orchestration
LangChain	LLM and agent integration
Groq	LLM inference
MCP	Agent-to-tool communication
langchain-mcp-adapters	LangChain ↔ MCP integration
PostgreSQL	Conversation/state persistence
Tavily	Web and hotel research
AviationStack	Flight information
OpenWeather	Weather information
uvx	Running local MCP tools
📁 Project Structure
trip-mate/
│
├── app.py
│   └── FastAPI application entry point
│
├── backend.py
│   └── Core LangGraph agent orchestration
│
├── mcp_client.py
│   └── MCP client and tool integrations
│
├── custom_weather_mcp_server.py
│   └── Custom weather MCP server
│
├── requirements.txt
│   └── Python dependencies
│
├── templates/
│   └── HTML/Jinja2 frontend templates
│
├── static/
│   └── CSS, JavaScript and frontend assets
│
├── tools/
│   └── Flight and web search integrations
│
├── .env
│   └── Environment variables
│
├── .gitignore
│   └── Git ignored files
│
└── LICENSE
    └── Project license

📋 Prerequisites

Before running TripMate AI locally, make sure you have:

Python 3.10 or newer
Git
PostgreSQL
uvx
A virtual environment tool such as venv

You will also need API keys for:

Groq
Tavily
AviationStack
OpenWeather
🚀 Installation
1. Clone the Repository
git clone https://github.com/your-username/trip-mate.git

cd trip-mate


Replace the repository URL with your actual GitHub repository.

2. Create a Virtual Environment
Windows
python -m venv .venv

Linux / macOS
python3 -m venv .venv

3. Activate the Virtual Environment
Windows PowerShell
.venv\Scripts\Activate.ps1

Windows CMD
.venv\Scripts\activate.bat

Linux / macOS
source .venv/bin/activate

4. Install Dependencies
pip install -r requirements.txt

🔐 Environment Variables

Create a .env file in the project root:

DATABASE_URL=postgresql://user:password@localhost:5432/travel_db

GROQ_API_KEY=your_groq_api_key

AVIATIONSTACK_API_KEY=your_aviationstack_api_key

TAVILY_API_KEY=your_tavily_api_key

OPENWEATHER_API_KEY=your_openweather_api_key

DEFAULT_ORIGIN_IATA=DAC

Environment Variables
Variable	Description
DATABASE_URL	PostgreSQL database connection
GROQ_API_KEY	Groq API key
AVIATIONSTACK_API_KEY	AviationStack API key
TAVILY_API_KEY	Tavily API key
OPENWEATHER_API_KEY	OpenWeather API key
DEFAULT_ORIGIN_IATA	Default departure airport IATA code

Important: Never commit your .env file or API keys to GitHub.

Add the following to .gitignore:

.env
.venv/
__pycache__/
*.pyc

🗄️ PostgreSQL Setup

Make sure PostgreSQL is running.

Create the database specified in your DATABASE_URL.

For example:

travel_db


Example connection string:

postgresql://user:password@localhost:5432/travel_db

▶️ Running the Application
Option A — Run with Python
python app.py

Option B — Run with Uvicorn
uvicorn app:app --reload --host 127.0.0.1 --port 8000


Once the server starts, open:

http://127.0.0.1:8000

🔌 Running the MCP Server

The repository includes a custom weather MCP server.

Run it separately if you want to experiment with the custom weather MCP functionality:

python custom_weather_mcp_server.py


For AviationStack MCP, verify that uvx is available:

uvx --version


The AviationStack MCP command is:

uvx aviationstack-mcp

🌐 API Endpoints
Health Check
GET /health


Returns basic application health information.

Example:

curl http://127.0.0.1:8000/health

Travel Planning
POST /api/travel


Creates or resumes a travel planning thread.

Request
{
  "message": "Plan a 3-day trip to Tokyo with a budget of $1200",
  "thread_id": "optional-thread-id"
}

Example
curl -X POST http://127.0.0.1:8000/api/travel \
  -H "Content-Type: application/json" \
  -d "{\"message\":\"Plan a 3-day trip to Tokyo with a budget of $1200\"}"

Approve or Revise Travel Plan
POST /api/travel/approve


Used for the Human-in-the-Loop approval stage.

Approve a Plan
{
  "thread_id": "your-thread-id",
  "approved": true,
  "feedback": ""
}

Request Changes
{
  "thread_id": "your-thread-id",
  "approved": false,
  "feedback": "Reduce hotel costs and add more cultural attractions."
}

🔄 Workflow

The complete workflow is:

Step 1 — User Request

The user submits a natural-language travel request.

Plan a 3-day trip to Tokyo with a budget of $1200.

Step 2 — Input Guardrails

The request is validated.

User Request
     │
     ▼
Input Guardrails
     │
     ├── Valid ────► Continue
     │
     └── Invalid ──► Reject

Step 3 — Supervisor

The Supervisor Agent decides how the request should be handled.

Step 4 — Flight Research

The Flight Agent uses AviationStack MCP.

Flight Agent
     │
     ▼
AviationStack MCP
     │
     ▼
Flight Information

Step 5 — Hotel Research

The Hotel Agent uses Tavily MCP.

Hotel Agent
     │
     ▼
Tavily MCP
     │
     ▼
Hotel Information

Step 6 — Weather Research

The Weather Agent uses the custom weather MCP server.

Weather Agent
     │
     ▼
Weather MCP
     │
     ▼
OpenWeather
     │
     ▼
Weather Information

Step 7 — Itinerary Generation

The Itinerary Agent combines the available information.

Flights
   +
Hotels
   +
Weather
   +
Destination Information
   │
   ▼
Itinerary Agent
   │
   ▼
Day-by-Day Plan

Step 8 — Human Approval

The generated plan is presented for review.

Draft Plan
    │
    ▼
Human Review
    │
    ├── Approved ──► Final Plan
    │
    └── Revision ──► Feedback ──► Workflow

💬 Example Prompts
Budget Trip
Plan a 4-day trip to Bangkok with a budget of $800.

Include:
- Flights
- Hotels
- Weather
- Daily itinerary

Family Trip
Plan a 5-day family trip to Singapore.

We are traveling with two children.
Include family-friendly hotels and activities.

Luxury Trip
Plan a luxury 7-day trip to Paris.

Include premium hotels, flight suggestions,
weather information and high-end activities.

Revision Request
Reduce the hotel budget and add more
cultural attractions to the itinerary.

🧪 Development Notes

The project uses an asynchronous FastAPI server while maintaining synchronous convenience wrappers in backend.py.

nest_asyncio is applied in app.py to allow synchronous helpers to call asynchronous MCP helpers within the application environment.

Tests are currently not included.

You can experiment with the project by:

Using the web interface
Calling the REST API
Testing individual MCP tools
Trying different travel prompts
Testing the HITL approval flow
Adding new agents
Adding new MCP tools
🔮 Future Improvements

Potential improvements include:

💰 Intelligent budget optimization
✈️ Real-time flight tracking
🏨 Real-time hotel availability
🚆 Train and public transportation search
🍽️ Restaurant recommendation agent
🎟️ Attraction and activity booking
🗺️ Interactive maps
💱 Currency conversion
🧳 AI-generated packing lists
🌍 Multi-destination trip planning
📅 Calendar integration
📱 Mobile-friendly interface
🔐 Authentication and user accounts
📊 Detailed trip cost breakdown
🧠 Personalized travel preferences
🤖 Additional MCP agents
🛡️ Advanced input/output guardrails
👤 More advanced HITL workflows
🧪 Automated testing
🤝 Contributing

Contributions are welcome!

To contribute:

Fork the repository.
Create a feature branch.
git checkout -b feature/my-new-feature

Make your changes.
Test your changes.
Commit your changes.
git add .
git commit -m "Add new travel feature"

Push the branch.
git push origin feature/my-new-feature

Open a Pull Request.

You can contribute by:

Fixing bugs
Improving documentation
Adding new agents
Adding MCP tools
Improving guardrails
Improving HITL workflows
Adding tests
Improving the frontend
Adding new travel APIs
🐛 Issues & Feature Requests

If you find a bug or have an idea for a new feature, please open an issue in the repository.

When reporting an issue, include:

Operating system
Python version
Error message
Relevant logs
Steps to reproduce the issue

Do not include API keys, passwords, database credentials, or other secrets in issues.

🙏 Acknowledgements

This project is built using modern AI and application-development technologies:

LangGraph
LangChain
Model Context Protocol
Groq
FastAPI
PostgreSQL
Tavily
AviationStack
OpenWeather

The project demonstrates how Multi-Agent AI, LangGraph, MCP, Supervisor Agents, Guardrails, and Human-in-the-Loop workflows can be combined to build a practical and controllable AI application.

📄 License

This repository follows the license included in the LICENSE file.

⭐ Support

If you find this project useful or interesting:

⭐ Star the repository
🍴 Fork the repository
🐛 Report bugs
💡 Suggest features
🤝 Submit pull requests
📢 Share the project
✈️ TripMate AI

Plan smarter. Research faster. Keep humans in control.

Multi-Agent AI + LangGraph + MCP + Supervisor + Guardrails + Human-in-the-Loop




```text
