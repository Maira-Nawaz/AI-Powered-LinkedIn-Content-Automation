# AI News to LinkedIn Automation with n8n

An AI-powered, human-in-the-loop workflow that automatically discovers relevant industry news, evaluates its relevance, generates a LinkedIn post, sends it for human approval, and publishes the approved content to LinkedIn.

> **Automate the repetitive work while keeping the human in control.**

## Overview

Creating content consistently can involve a lot of repetitive work:

- Finding relevant news
- Reading and filtering articles
- Deciding whether an article is worth sharing
- Writing a draft
- Reviewing the content
- Publishing it

## Screenshots

### Complete n8n Workflow

![Complete n8n Workflow](https://github.com/Maira-Nawaz/ai-news-to-linkedin-automation/blob/main/assets/Workflow.jpeg)

### Human Approval Email

![Human Approval Email](https://github.com/Maira-Nawaz/ai-news-to-linkedin-automation/blob/main/assets/Approval.jpeg)

### Generated LinkedIn Post

![Published LinkedIn Post](https://github.com/Maira-Nawaz/ai-news-to-linkedin-automation/blob/main/assets/Linkedin%20Posting.png)

This workflow automates most of that process using **n8n + LLMs + APIs**, while keeping a **Human-in-the-Loop (HITL)** approval step before anything is published.

The workflow follows a simple principle:

**AI prepares → Human reviews → Human approves → Automation publishes**

## Workflow

```text
Daily Trigger
     ↓
GNews API
     ↓
Normalize Articles
     ↓
Keep Last 24 Hours
     ↓
Limit Articles
     ↓
AI Relevance Analysis
     ↓
Relevant?
     ↓
AI Draft Generation
     ↓
Parse Draft
     ↓
Human Approval via Gmail
     ↓
Approved?
     ↓
Get LinkedIn Member
     ↓
LinkedIn API
     ↓
Published LinkedIn Post
```

## Key Features

### 1. Automated News Discovery

The workflow uses the **GNews API** to retrieve recent articles related to topics such as:

- Artificial Intelligence
- Generative AI
- AI Agents
- Data Engineering
- Analytics
- Automation
- Marketing Technology

### 2. Article Normalization

Raw API responses are transformed into a consistent structure containing title, URL, summary, publication timestamp, source, source URL, and image.

### 3. Time-Based Filtering

Articles are filtered so that only content published within the **last 24 hours** continues through the workflow.

### 4. AI-Powered Relevance Analysis

Each article is evaluated by an LLM before content is generated. The model determines whether the article is genuinely relevant to the target professional audience.

Example structured response:

```json
{
  "relevant": true,
  "reason": "The article discusses a significant development in generative AI."
}
```

Only relevant articles continue to the drafting stage.

### 5. AI-Powered LinkedIn Draft Generation

For relevant articles, an LLM generates a LinkedIn post based on the article information.

The workflow separates:

**Research → Relevance → Content Generation**

## Human-in-the-Loop (HITL)

One of the main design decisions in this project is the **Human-in-the-Loop approval stage**.

The AI does not directly publish its own content.

Instead, the generated draft is sent to the user through Gmail.

The user can:

**Approve → Continue to LinkedIn**

or

**Reject → Stop workflow**

### HITL Flow

```text
AI generates content
        ↓
Human receives draft
        ↓
      Review
      /   \
 Reject   Approve
   ↓        ↓
 Stop     Publish
```

This creates a controlled AI automation pipeline where the model handles repetitive work while the human retains the final publishing decision.

## LinkedIn Integration

After approval, the workflow publishes the generated content using the **LinkedIn API**.

The integration uses:

- OAuth 2.0
- LinkedIn API
- HTTP Request node
- LinkedIn UGC Posts API

The workflow retrieves the authenticated LinkedIn member identifier and constructs the required Person URN:

```text
urn:li:person:{member_id}
```

The final request publishes the approved text as a public LinkedIn post.

## Technologies Used

| Technology | Purpose |
|---|---|
| **n8n** | Workflow orchestration |
| **GNews API** | News discovery |
| **Groq / LLM** | Relevance analysis and content generation |
| **Gmail** | Human approval interface |
| **LinkedIn API** | Automated publishing |
| **OAuth 2.0** | LinkedIn authentication |
| **HTTP Request** | API communication |
| **JavaScript** | Data transformation and filtering |


## Design Principles

### Automate repetitive work

The goal isn't to automate everything. The goal is to remove repetitive tasks that don't require human judgment.

### Keep humans in control

AI generates the recommendation and draft. The human makes the final publishing decision.

### Separate AI responsibilities

```text
Relevance Analysis
       ↓
Content Generation
       ↓
Human Review
       ↓
Publishing
```

This makes the system easier to understand, debug, and improve.

## Security

This repository should **never contain real credentials or secrets**.

Before uploading or sharing the workflow JSON, remove or replace:

- API keys
- OAuth client secrets
- Access tokens
- Refresh tokens
- Passwords
- Private credentials
- Personal email credentials

Use placeholders such as:

```text
YOUR_GNEWS_API_KEY
YOUR_GROQ_CREDENTIAL
YOUR_LINKEDIN_OAUTH_CREDENTIAL
```

Credentials should be configured directly inside n8n.

## Setup

### Prerequisites

You will need:

- n8n
- GNews API account
- LLM provider account
- Gmail connection
- LinkedIn Developer application
- LinkedIn API access

### 1. Import the workflow

Import the workflow JSON from the `workflow/` directory into n8n.

### 2. Configure GNews

Create a GNews API credential and configure the search request.

Example topics:

```text
AI OR "generative AI" OR "AI agents"
```

### 3. Configure the LLM

Connect your preferred LLM provider to the two LLM Chain nodes:

- Relevance Analysis
- Draft Generation

### 4. Configure Gmail

Connect Gmail and configure the Human Approval node.

### 5. Configure LinkedIn OAuth

Create a LinkedIn Developer application and configure OAuth 2.0 with the required permissions for publishing.

### 6. Test the workflow

Run the workflow manually first and verify:

1. News is retrieved
2. Articles are normalized
3. Old articles are filtered
4. Relevance analysis works
5. A draft is generated
6. Approval email arrives
7. Approval is captured
8. LinkedIn authentication works
9. The post is published successfully

## Future Improvements

### Content Quality

- Improve personalization
- Match the user's writing style
- Add stronger content evaluation
- Add tone controls
- Detect AI-generated phrasing

### Content Management

- Duplicate article detection
- Article history
- Published post tracking
- Content database
- Post performance tracking

### AI Improvements

- Multiple-agent architecture
- Source credibility evaluation
- Better relevance scoring
- Content quality scoring
- Fact-checking layer

### LinkedIn Improvements

- Image support
- Article previews
- Hashtag optimization
- Scheduled publishing
- Post analytics

### Reliability

- Error handling
- Retry mechanisms
- API rate-limit handling
- Logging
- Failure notifications

## Lessons Learned

Building this workflow reinforced an important idea:

**AI automation isn't just about connecting an LLM to an API.**

A useful AI system needs:

```text
Input
 ↓
Processing
 ↓
AI reasoning
 ↓
Validation
 ↓
Human oversight
 ↓
Action
 ↓
Feedback
```

The workflow becomes much more useful when each part has a clear responsibility.

## Architecture

At a high level, the system can be viewed as five layers:

```text
┌───────────────────────────────┐
│        DATA INGESTION         │
│          GNews API            │
└───────────────┬───────────────┘
                ↓
┌───────────────────────────────┐
│       AI PROCESSING           │
│   Relevance + Drafting        │
└───────────────┬───────────────┘
                ↓
┌───────────────────────────────┐
│      HUMAN OVERSIGHT          │
│        Gmail HITL             │
└───────────────┬───────────────┘
                ↓
┌───────────────────────────────┐
│          ACTION               │
│       LinkedIn API            │
└───────────────┬───────────────┘
                ↓
┌───────────────────────────────┐
│           OUTPUT              │
│      Published LinkedIn       │
│             Post              │
└───────────────────────────────┘
```

## Key Takeaway

This project demonstrates how **AI, APIs, workflow automation, and human oversight can work together to solve a real repetitive task.**

The objective isn't to remove humans from the workflow.

It's to let AI handle the repetitive work while keeping humans responsible for decisions that matter.

## Author

**Maira Nawaz**

Data & AI Professional

Building reliable data pipelines, actionable insights, automation, and AI-powered solutions.

## License

This project is provided for educational and portfolio purposes.

You are free to explore and adapt the workflow for your own projects, but make sure to configure your own API credentials and follow the terms of the services you connect.
