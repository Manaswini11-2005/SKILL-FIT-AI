# SHL Assessment Recommendation System — Complete Walkthrough

This README takes you from setup to deployment. Follow the steps in order.

## 0. What's in this folder

```text
shl-agent/
├── app/
│   ├── main.py        # FastAPI app: /health and /chat
│   ├── agent.py       # Conversational AI logic for assessment recommendations
│   └── retrieval.py   # TF-IDF retrieval over the SHL assessment catalog
├── scraper/
│   └── scrape_shl.py  # Scrapes the SHL catalog -> data/catalog.json
├── data/
│   └── catalog.json   # Seed catalog (20 sample assessments)
├── requirements.txt
└── render.yaml        # Deployment configuration for Render
```

## 1. Project Overview

The SHL Assessment Recommendation System is a conversational AI application that helps users identify suitable SHL assessments based on their hiring requirements.

Users can describe a job role, required skills, experience level, and other relevant details through a chat interface. The system understands the requirements, asks follow-up questions when necessary, retrieves relevant assessments from the SHL catalog, and provides recommendations.

### Key Features

- Conversational interaction for collecting hiring requirements.
- Clarification questions when information is incomplete.
- AI-powered assessment recommendations based on job requirements.
- Retrieval of relevant assessments using TF-IDF.
- Refinement of recommendations based on additional user input.
- Comparison of recommended assessments.
- Handling of unsupported or irrelevant requests.

## 2. Get a Free Groq API Key

1. Visit https://console.groq.com and create an account.
2. Generate an API key.
3. Keep the key secure. It will be configured as an environment variable rather than hard-coded into the application.

Groq provides fast LLM inference, which helps the application respond within the assessment's API time limits.

## 3. Build the SHL Assessment Catalog

The existing `data/catalog.json` file contains approximately 20 sample assessments.

Before submitting, replace the sample catalog with actual SHL assessment data.

Run:

```bash
cd scraper
pip install requests beautifulsoup4
python scrape_shl.py
```

The scraper should generate the catalog at:

```text
data/catalog.json
```

After scraping:

1. Check whether assessment names and URLs are valid.
2. Verify that the assessment URLs open correctly.
3. Ensure the catalog contains Individual Test Solutions rather than Job Solutions.
4. Check whether the descriptions and other assessment details were extracted correctly.

If the scraper returns zero or incorrect results, inspect the SHL catalog page and update the CSS selectors in `parse_listing_page()` and `fetch_detail()` inside `scrape_shl.py`.

## 4. Run the Application Locally

Install the dependencies:

```bash
pip install -r requirements.txt
```

Configure your Groq API key.

Windows PowerShell:

```powershell
$env:GROQ_API_KEY="your-key-here"
```

Linux/macOS:

```bash
export GROQ_API_KEY="your-key-here"
```

Start the FastAPI application:

```bash
uvicorn app.main:app --reload --port 8000
```

### Test the Health Endpoint

```bash
curl http://localhost:8000/health
```

### Test the Recommendation Endpoint

Send a hiring requirement:

```bash
curl -X POST http://localhost:8000/chat \
  -H "Content-Type: application/json" \
  -d '{"messages":[{"role":"user","content":"Hiring a Java developer who works with stakeholders"}]}'
```

The system may ask a clarification question about the required experience level.

Send the next message with the conversation history:

```bash
curl -X POST http://localhost:8000/chat \
  -H "Content-Type: application/json" \
  -d '{"messages":[
        {"role":"user","content":"Hiring a Java developer who works with stakeholders"},
        {"role":"assistant","content":"Got it — what seniority level are you targeting?"},
        {"role":"user","content":"Mid-level, around 4 years"}
      ]}'
```

The system uses the additional information to generate relevant assessment recommendations.

## 5. Test the Recommendation System

Use the 10 conversation traces provided with the assignment.

1. Download the 10-trace ZIP file from Tania Goyal's email.
2. Extract the ZIP file.
3. Review each conversation trace, including its persona and expected assessment shortlist.
4. Replay the user messages against the `/chat` endpoint in the correct order.
5. Include the complete conversation history with each request.
6. Compare the final recommendations with the expected assessment shortlist.

Calculate Recall@10 to evaluate how many relevant assessments appear in the top 10 recommendations.

## 6. Deploy the Application Using Render

### Step 1: Upload to GitHub

Push the project folder to a new GitHub repository.

### Step 2: Create a Render Web Service

1. Visit https://render.com.
2. Select **New → Web Service**.
3. Connect your GitHub repository.
4. Select the repository containing the project.

### Step 3: Configure Deployment

Use these commands if Render does not automatically detect the configuration.

**Build Command:**

```bash
pip install -r requirements.txt
```

**Start Command:**

```bash
uvicorn app.main:app --host 0.0.0.0 --port $PORT
```

### Step 4: Configure Environment Variables

Add the following environment variable in the Render dashboard:

```text
GROQ_API_KEY = your-groq-api-key
```

### Step 5: Deploy and Test

After deployment, test the public application URL:

```bash
curl https://your-app.onrender.com/health
```

Also test the `/chat` endpoint to verify that conversational assessment recommendations are working correctly.

Render's free tier may experience cold starts after inactivity, so allow time for the service to start.

## 7. Expected Project Workflow

1. User enters hiring requirements through the chat interface.
2. The AI agent understands the job role and required skills.
3. The system asks clarification questions if important details are missing.
4. The retrieval component searches the SHL assessment catalog.
5. The system recommends relevant assessments.
6. Users can refine or compare recommendations through further conversation.

**Project Goal:** Build a conversational AI-powered recommendation system that helps hiring teams find suitable SHL assessments based on job roles, skills, and candidate requirements.
