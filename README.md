\# Provenance Guard



\## Overview



Provenance Guard is a Flask-based backend service that estimates whether submitted text is more likely to be AI-generated or human-written. Instead of relying on a single detector, the system combines multiple detection signals to produce a confidence score and a transparency label. Every decision is recorded in an audit log, and creators can appeal classifications they believe are incorrect.



\---



\# Features



\* Multi-signal AI attribution

\* Groq LLM detection

\* Stylometric heuristic analysis

\* Combined confidence scoring

\* Transparency labels

\* Audit logging

\* Appeals endpoint

\* Rate limiting with Flask-Limiter



\---



\# Architecture



```text

Client

&#x20;  │

&#x20;  ▼

POST /submit

&#x20;  │

&#x20;  ├── Groq Detection

&#x20;  ├── Stylometric Analysis

&#x20;  ├── Confidence Scoring

&#x20;  ├── Transparency Label

&#x20;  ├── Audit Log

&#x20;  ▼

JSON Response



POST /appeal

&#x20;  │

&#x20;  ▼

Update Status → under\_review



GET /log

&#x20;  │

&#x20;  ▼

Returns Audit History

```



\---



\# Detection Signals



\## Signal 1 – Groq LLM



A Groq-hosted large language model analyzes the writing style and predicts whether the text is more likely AI-generated or human-written.



\## Signal 2 – Stylometric Analysis



The system computes a heuristic score based on:



\* Vocabulary diversity

\* Sentence-length variation

\* Punctuation density



The two signals are averaged to produce a final confidence score.



\---



\# Confidence Scoring



The final confidence score is calculated by averaging:



\* Groq LLM score

\* Stylometric heuristic score



Ranges:



| Confidence  | Result       |

| ----------- | ------------ |

| 0.70 – 1.00 | Likely AI    |

| 0.40 – 0.69 | Uncertain    |

| 0.00 – 0.39 | Likely Human |



\---



\# API Endpoints



\## POST /submit



Submits text for AI attribution.



Request:



```json

{

&#x20;   "text": "Example text",

&#x20;   "creator\_id": "eyobed"

}

```



Example Response:



```json

{

&#x20;   "attribution": "likely\_ai",

&#x20;   "confidence": 0.77,

&#x20;   "signals": {

&#x20;       "groq\_result": "AI",

&#x20;       "llm\_score": 0.85,

&#x20;       "stylometric\_score": 0.70

&#x20;   },

&#x20;   "status": "classified"

}

```



\---



\## POST /appeal



Allows creators to appeal a previous classification.



Request:



```json

{

&#x20;   "content\_id": "...",

&#x20;   "creator\_reasoning": "I wrote this content myself."

}

```



Response:



```json

{

&#x20;   "message": "Appeal received.",

&#x20;   "status": "under\_review"

}

```



\---



\## GET /log



Returns the audit history of submissions and appeals.



\---



\# Rate Limiting



The submission endpoint is protected using Flask-Limiter.



Current limits:



\* 10 requests per minute

\* 100 requests per day



Requests exceeding the limit receive:



```text

429 Too Many Requests

```



\---



\# Testing



The backend was tested by:



\* Submitting AI-style text

\* Receiving AI classifications

\* Verifying confidence scores

\* Submitting appeals

\* Viewing audit logs

\* Triggering rate limiting (HTTP 429)



\---



\# Technologies Used



\* Python

\* Flask

\* Flask-Limiter

\* Groq API

\* python-dotenv



\---



\# Future Improvements



\* Add additional stylometric features

\* Support administrator review of appeals

\* Store audit logs in a database instead of JSON

\* Improve confidence calibration

\* Deploy the API to a cloud platform



\---



\# AI Usage



AI was used to assist with:



\* Designing the Flask API structure.

\* Implementing Groq integration.

\* Developing the stylometric heuristic function.

\* Debugging endpoint and indentation issues.



All generated code was manually reviewed, tested, and revised before submission.



\---



\# Spec Reflection



The project specification helped define the required API endpoints and multi-signal detection pipeline before implementation began.



One implementation choice was simplifying the confidence scoring by averaging two independent signals. This provided a transparent and easy-to-understand scoring method while satisfying the project requirements.



