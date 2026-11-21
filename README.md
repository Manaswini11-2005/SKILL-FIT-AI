# SkillFit AI – RAG-Based Assessment Recommendation System

SkillFit AI is an AI-powered conversational recommendation system that helps users identify suitable assessments based on job roles, required skills, experience levels, and hiring requirements.

The application uses a conversational AI agent and retrieval-based recommendation to understand user requirements, ask clarification questions, retrieve relevant assessments, and generate personalized recommendations.

This README covers project setup, configuration, testing, and deployment.

## 1. Project Structure

```text
SkillFit-AI/
├── app/
│   ├── main.py              # FastAPI application: /health and /chat
│   ├── agent.py             # AI agent: clarification, recommendation, refinement and comparison
│   └── retrieval.py         # Retrieval system for the assessment catalog
├── scraper/
│   └── scrape_catalog.py    # Scrapes assessment catalog data
├── data/
│   └── catalog.json         # Assessment catalog
├── requirements.txt
└── render.yaml              # Render deployment configuration
```

**Note:** Rename the existing `scrape_shl.py` file to `scrape_catalog.py` and update any imports or references if necessary.

## 2. Key Features

- **Conversational AI:** Understands hiring requirements through natural-language conversations.
- **Requirement Clarification:** Asks follow-up questions when important information is missing.
- **RAG-Based Recommendations:** Uses retrieved assessment information to generate relevant recommendations.
- **Assessment Retrieval:** Finds suitable assessments based on job roles, skills, and experience requirements.
- **Recommendation Refinement:** Updates recommendations based on additional user input.
- **Assessment Comparison:** Helps users compare recommended assessments.
- **Intelligent Responses:** Handles incomplete or unsupported requests appropriately.

## 3. Technologies Used

- Python
- FastAPI
- Large Language Models (LLMs)
- Retrieval-Augmented Generation (RAG)
- Groq API
- TF-IDF Retrieval
- Natural Language Processing (NLP)
- Requests and BeautifulSoup
- Render

## 4. Configure the Groq API Key

1. Visit https://console.groq.com and create an account.
2. Generate an API key.
3. Store the key securely as an environment variable.
4. Never hard-code the API key in the application.

Groq provides fast LLM inference, helping the application generate responses efficiently.

## 5. Build the Assessment Catalog

The `data/catalog.json` file contains the assessment information used by the recommendation system.

The initial catalog contains approximately 20 sample assessments. Replace the sample data with the actual assessment catalog before final submission.

Run the scraper:

```bash
cd scraper
pip install requests beautifulsoup4
python scrape_catalog.py
```

After scraping, verify:

1. Assessment names are accurate.
2. Assessment URLs are valid and accessible.
3. Assessment descriptions are correctly extracted.
4. The catalog contains the required assessment categories.
5. The generated JSON structure matches the format expected by the retrieval component.

If the scraper returns incomplete or incorrect results, inspect the source website and update the relevant CSS selectors in the scraping functions.

## 6. Run the Application Locally

Install the dependencies:

```bash
pip install -r requirements.txt
```

Configure the API key.

**Windows PowerShell:**

```powershell
$env:GROQ_API_KEY="your-key-here"
```

**Linux/macOS:**

```bash
export GROQ_API_KEY="your-key-here"
```

Start the FastAPI server:

```bash
uvicorn app.main:app --reload --port 8000
```

### Check Application Health

```bash
curl http://localhost:8000/health
```

### Test the Recommendation Endpoint

```bash
curl -X POST http://localhost:8000/chat \
  -H "Content-Type: application/json" \
  -d '{"messages":[{"role":"user","content":"I am hiring a Java developer who works with stakeholders"}]}'
```

If the requirements are incomplete, the conversational agent may ask a clarification question.

For example, it may ask about the required experience level.

Continue the conversation by including the previous messages:

```bash
curl -X POST http://localhost:8000/chat \
  -H "Content-Type: application/json" \
  -d '{"messages":[
        {"role":"user","content":"I am hiring a Java developer who works with stakeholders"},
        {"role":"assistant","content":"What seniority level are you targeting?"},
        {"role":"user","content":"Mid-level, around 4 years"}
      ]}'
```

The system processes the conversation and generates relevant assessment recommendations.

## 7. Evaluate the Recommendation System

Test the application using the provided conversation traces.

1. Download and extract the test conversation ZIP file.
2. Review each conversation and its expected assessment shortlist.
3. Replay the user messages against the `/chat` endpoint.
4. Include the complete conversation history with each request.
5. Compare the generated recommendations with the expected results.
6. Calculate Recall@10 to evaluate recommendation performance.

Recall@10 measures how many relevant assessments are included in the top 10 recommendations.

## 8. Deploy Using Render

### Step 1: Push the Project to GitHub

Create a GitHub repository and push the complete project.

### Step 2: Create a Render Web Service

1. Visit https://render.com.
2. Select **New → Web Service**.
3. Connect your GitHub repository.
4. Select the project repository.

### Step 3: Configure Build and Start Commands

**Build Command:**

```bash
pip install -r requirements.txt
```

**Start Command:**

```bash
uvicorn app.main:app --host 0.0.0.0 --port $PORT
```

### Step 4: Add Environment Variables

In the Render dashboard, add:

```text
GROQ_API_KEY = your-groq-api-key
```

### Step 5: Deploy and Test

Deploy the application and check the health endpoint:

```bash
curl https://your-app.onrender.com/health
```

Test the `/chat` endpoint to verify that the conversational recommendation workflow is functioning correctly.

Note: Free hosting services may experience cold starts after periods of inactivity.

## 9. Application Workflow

1. The user enters hiring requirements through the chat interface.
2. The AI agent understands the job role, skills, and experience requirements.
3. The agent asks clarification questions if additional information is needed.
4. The retrieval component searches the assessment catalog.
5. Relevant assessment information is used to generate recommendations.
6. The user can refine or compare recommendations through further conversation.

## 10. Project Objective

The objective of SkillFit AI is to simplify assessment selection through conversational AI and retrieval-based recommendations.

It helps users identify relevant assessments efficiently by understanding hiring requirements and generating context-aware recommendations.
