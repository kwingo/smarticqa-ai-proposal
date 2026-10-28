# smarticqa-ai-proposal

SmartICQA: AI-Powered Inventory Quality Assistant
Project type: Comprehensive AI Web Application Proposal
Author: Kirk Wingo
Status: Proposed project (not yet implemented)
Step 1: Project Overview
SmartICQA is a proposed web application that helps Inventory Control and Quality Assurance (ICQA) associates investigate inventory discrepancies, classify defects, find relevant procedures, and summarize operational trends. The application will combine a large language model (LLM) with a searchable knowledge base and human review.

Inventory teams often need to compare records, read standard operating procedures (SOPs), and review past investigations to understand why counts do not match. These manual searches can take time, and similar problems may be investigated repeatedly. SmartICQA aims to reduce research time and make relevant information easier to find without allowing AI to make unapproved inventory changes.

The first version will focus on decision support, not automatic inventory adjustment. To protect sensitive operational information, development and demonstrations will use synthetic or explicitly authorized sample data.
Step 2: Use Case and Workflow
Primary users: ICQA associates and team leads. Secondary users: managers reviewing recurring defects and proposed improvements.
1. An associate enters a discrepancy description or selects an existing sample case.
2. The application validates the request and retrieves relevant SOP excerpts and similar historical cases from an approved knowledge base.
3. An LLM summarizes the evidence, suggests a defect category and possible causes, and recommends verification steps.
4. The application displays a structured result with citations, confidence limitations, and any missing information.
5. An associate checks the physical inventory and source records, then accepts, edits, or rejects the suggestions.
6. A manager or other authorized employee approves any consequential inventory adjustment or process change outside the AI recommendation flow.
7. The application stores the review outcome and non-sensitive feedback for future evaluation.
How AI helps: RAG reduces manual document searching, and the LLM turns relevant information into a concise investigation summary. Deterministic code handles calculations, input validation, permissions, and database operations. The AI never serves as the sole source of truth for inventory quantities.
Step 3: AI Features to Be Implemented
AI feature	Planned implementation	Why it matters
Prompt engineering	Use a system prompt instructing the LLM to rely on retrieved evidence, distinguish facts from hypotheses, cite sources, and state when information is insufficient.	Makes responses more consistent and reduces unsupported claims.
Structured outputs	Require JSON matching a schema with defect_category, possible_causes, verification_steps, source_ids, and needs_human_review.	Allows the interface to display and validate predictable results.
RAG and vector database	Chunk approved SOPs and sanitized past case notes, create embeddings, store them in a vector index, and retrieve relevant passages for each question.	Provides context-specific answers grounded in operational references.
Evaluation framework	Maintain a labeled set of sample discrepancies and expected outcomes; test retrieval relevance, citation support, classification accuracy, and JSON validity.	Measures whether the assistant is helpful and reliable before use.
Observability tools	Log request latency, token usage, estimated API cost, retrieval results, validation errors, and human feedback without recording secrets or unnecessary sensitive content.	Helps detect failures, monitor costs, and improve performance.


Human-in-the-loop: Associates validate classifications and proposed root causes; authorized personnel approve consequential decisions. AI-generated suggestions are clearly labeled as suggestions.
Step 4: Technical Approach
Proposed stack
- Frontend: React for a simple dashboard with a case-entry form, source citations, and review controls.
- Backend: Python with FastAPI for request handling, validation, and access control.
- LLM: An API-accessible language model (for example, an OpenAI model) for summarization, classification, and explanation.
- Embeddings and vector store: An embedding model with Chroma for a small prototype; a managed vector database could be considered if the project grows.
- Structured validation: Pydantic models to validate JSON outputs.
- Development: VS Code or Cursor, Git, and GitHub for version control and code reviews.
- Evaluation and monitoring: A Python test suite and an observability platform such as Langfuse, plus application metrics.
High-level architecture
ICQA associate (React UI)
          |
          v
FastAPI backend --> authentication / validation
          |
          v
Search approved documents --> embeddings + Chroma
          |
          v
Retrieved SOPs and sample case excerpts
          |
          v
LLM with evidence-grounded prompt
          |
          v
Pydantic JSON validation
          |
          v
Results + citations + human review controls
          |
          v
Feedback and privacy-conscious monitoring
Security and boundaries: Keep API keys in environment variables, exclude .env from Git, restrict document access by role, and avoid uploading confidential inventory or employee information to external services without organizational approval. If retrieval produces no adequate evidence, the app should say so rather than inventing an answer. The prototype will not connect to or modify a production inventory system.
Step 5: Example Prompts and Expected Outputs
Example 1: Investigate a discrepancy
User prompt:
The system shows 25 units in bin A12, but a physical count found 21. What should I investigate first?

Expected behavior: The assistant retrieves relevant counting and reconciliation procedures, proposes plausible checks, and avoids claiming a confirmed root cause.

Illustrative structured output (sample values only):
{
  "defect_category": "count_discrepancy",
  "possible_causes": [
    "Possible miscount",
    "Unrecorded or delayed inventory movement"
  ],
  "verification_steps": [
    "Recount the bin using the approved procedure",
    "Review authorized inventory movement records",
    "Escalate unresolved differences to an authorized reviewer"
  ],
  "source_ids": ["SOP-COUNT-01"],
  "needs_human_review": true
}
SOP-COUNT-01 is an illustrative placeholder; the actual app must cite only documents genuinely retrieved from its approved knowledge base.
Example 2: Find recurring patterns
User prompt:
Summarize the most common inventory discrepancy categories from the sample cases this week and suggest next steps.

Expected behavior: The backend calculates category counts from the selected sample records; the LLM produces a brief summary of those verified counts, points to relevant procedures, and suggests follow-up actions. The app must not fabricate metrics when no records are available.

Expected format: A short summary, a table of category counts from the database, cited supporting documents, and suggested actions marked Requires human review.
Step 6: Evaluation Strategy
I will create a small test set of synthetic discrepancy scenarios with human-labeled categories and relevant reference documents. Evaluation will include:
Measure	How it will be assessed	Initial prototype target
Classification accuracy	Compare suggested categories with human-labeled examples.	At least 85% on the test set
Retrieval recall@5	Check whether a relevant reference appears in the top five retrieved passages.	At least 90%
Citation support	Manually verify that cited passages support the claims made.	At least 95% of sampled claims
Structured-output validity	Validate responses against the JSON schema.	100% after validation/retry handling
Response latency	Track end-to-end time for typical requests.	Median under 5 seconds in the test environment
Human usefulness	Ask reviewers to rate whether the response helped their investigation.	Average 4/5 or higher


These are proposed goals, not measured results. I will review failures, revise prompts and retrieval settings, and rerun the same test set to compare changes. I will also test missing-document cases, ambiguous questions, and prompt-injection attempts embedded in retrieved text.

Step 7: Observability Plan
The application will monitor:
- Performance: Response time, retrieval time, model latency, and timeout rate.
- Reliability: API errors, invalid JSON, empty retrievals, and failed citation checks.
- Usage and cost: Request counts, token usage, and estimated model costs.
- Quality: Human approvals, edits, rejections, and repeated failure categories.
A dashboard will show aggregate trends over time. Logs will use request IDs and redacted metadata rather than unnecessary raw operational data. Access to logs will be limited, with a defined retention period. Alerts will be configured for unusually high error rates or costs.

Expected Outcome:
SmartICQA would demonstrate how LLMs, RAG, structured outputs, evaluation, and observability can support an inventory-quality workflow. The proposed design keeps people responsible for verification and approvals while using AI to make searching, summarizing, and investigating issues more efficient.
