# 1. Introduction

## What does

* Answers queries from user

## Who helps

* new recruits
* civil servants

## Value

* Simplifies process of acquiring information
* Increased accuracy via information feed on response generation step
* Increased speed via optimised processes

# 2. System Architecture

\======== HTML  ==========

## Modules

### Auth (SSO or basic)

Authorization part for access control

### Query Preprocessor (embedding + fallback)

* Translates query to English if necessary
* Embeds translated queries using SentenceTransformers
* Handles fallback logic if no result is found

### Vector Store (Qdrant, FAISS)

* Stores and searches embeddings
* Filters responses by freshness (TTL of 30 days)

### Llama 3 Engine (AWS, via Langchain)

* Used for fallback query response generation
* Generates procedural instructions, rewrites, personalized answers
* Hosted on AWS Cloud infrastructure

### Translation Service

* **Inbound:** Translates queries to English
* **Outbound:** Translates responses back to Kazakh/Russian
* Includes fallback confirmation if confidence is low

### Logging, Analytics, and Monitoring

* Logs every query, response, and feedback
* Analytics dashboard for weekly review
* Admin + Compliance Officer access to flagged items

### UI Clients (React, React Native)

App where user can interact with service and perform following actions:

1. Ask questions
2. Search for already asked ones
3. Give feedback

* Responsive design for both desktop and mobile platforms

# 3. Llama 3 Integration

## Where and how Llama is used:

* Query rewriting
* Generating clear procedural instructions
* Answer personalization
* Response generation fallback when no data found

## Model version LLaMA 3.3 70B

Model choice is based on quality need and not so high latency

# 4. Knowledge Base

* How documents are collected (e.g., Akimat DOCX, PDFs)
* How parsed (TextSplitter, OCR if needed)
* How embeddings are created (e.g., SentenceTransformers)
* How frequently re-indexed (e.g., weekly job)

# 5. Security & Compliance

* Local data storage (Postgres, S3, encrypted)
* Personal data anonymization
* Optional offline model for local install
* Role-based access for resources
  **Roles given:**

- User                ----------- Ask questions, see public procedures
- Editor              ----------- Manage documents, upload, update new regs
- Admin               ----------- Review flagged content, monitor feedback
- Compliance Officer  ----------- Access to private, sensitive internal docs

# 6. Feedback Loop

* Responses are rated (1-5) with optional comments
* Feedbacks are stored in separate table, linked by response ID
* Used for fine-tuning of model responses
* Feedback dashboard to highlight:

  * Top poorly rated responses
  * Unanswered or repeated queries
  * Flagged responses for review

# 7. Pilot Implementation Plan

| Phase       | Timeline   | Key Actions                     |
| ----------- | ---------- | ------------------------------- |
| Planning    | Week 1-2   | Get docs, define access         |
| Dev         | Week 3-6   | Build backend + embed KB        |
| Integration | Week 7-9   | UI, document updates, feedback  |
| Testing     | Week 10-12 | Metrics, feedback, final tuning |

# 8. Stack & Infra

| Layer        | Tech                     |
| ------------ | ------------------------ |
| Frontend     | React / React Native     |
| Backend      | FastAPI                  |
| DB           | Postgres                 |
| Vector Store | Qdrant / FAISS           |
| LLM          | Llama 3 via AWS          |
| Hosting      | AWS EC2 / On-prem option |

# 9. Risks & Mitigation

| Risk                | Mitigation                             |
| ------------------- | -------------------------------------- |
| Llama 3 latency     | Cache common queries, pre-generation   |
| Poor search result  | Manual override or rule-based fallback |
| Feedback ignored    | Weekly feedback analytics dashboard    |
| Language errors     | Prompt optimization, Kazakh QA         |
| Integration blocked | Use adapter layer for DMS sync         |

# 10. Architecture Flow (Visual Description)

1. Query received (Mobile, Web, Desktop)
2. Translated to English
3. Embedded & searched in vector DB
4. If match & fresh => return translated response
5. If not => Llama 3 generates response
6. Response translated => vectorized => stored
7. User receives response, can rate it
8. Feedback logged and reviewed in analytics dashboard

# 11. Compliance Deep-Dive

* Encrypted S3 and Postgres for all sensitive data
* Optional air-gapped offline deployments supported
* Only Admin/Compliance Officers access feedback and flagging logs

# 12. Summary

This solution fully meets the Astana Digital Mentor challenge:

* Covers civil servant use-cases with multilingual support
* Has reliable fallback, quality control, and compliance monitoring
* Designed for scalable deployment across devices and departments


# 13. Q&A

* Q: Why use translation to English?
* A: Llama model is trained mainly on English sentences. Thus, quality of responses is lower when queried in Russian or Kazakh. Translating to English ensures better understanding and generation quality.


