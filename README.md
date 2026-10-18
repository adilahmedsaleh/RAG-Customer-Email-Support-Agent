# ✉️ TechSupport Pro — RAG Customer Email Support Agent

An enterprise-ready, self-hosted **Retrieval-Augmented Generation (RAG)** email support agent built with **n8n**, **ChromaDB**, **Ollama**, and **Gmail API**. 

This system polls unread customer emails, cleans and normalizes message text, retrieves relevant documentation from a vectorized Knowledge Base, generates grounded responses using local LLMs, and safely routes responses through a **Human-in-the-Loop (HITL) confidence gate** before creating Gmail drafts, applying labels, and appending audit records via SSH.

---

## 🏗️ System Architecture

```text
                               ┌─────────────────────────────────────────┐
                               │           Travel / Tech KB              │
                               └────────────────────┬────────────────────┘
                                                    │ (Embeddings)
                                                    ▼
                                           [ ChromaDB Vector Store ]
                                                    │
                                                    ▼
[ Gmail Trigger ] ──► [ Text Cleaning ] ──► [ Vector Retrieval ]
 (Unread Emails)      & Normalization       (Top-5 KB Chunks)
                                                    │
                                                    ▼
[ Gmail Labels ]  ◄── [ Human Gate ]    ◄── [ Grounded AI Agent ]
& SSH Audit Logs      ├── High Conf ──► Create Draft
                      └── Low Conf  ──► Flag Review

🌟 Key Features
📥 Unread Email Normalization: Polled via Gmail API; strips HTML, signature blocks, and quoted email reply chains.   
🧠 Local Vector Search (RAG): Retrieves the top-5 most relevant knowledge chunks from ChromaDB using nomic-embed-text embeddings.   
🚫 Zero-Hallucination Grounding: Powered by local Ollama (llama3.2:3b) with structured JSON output enforcing confidence (high, medium, low) and status (answered, needs_human_review).   
🙋‍♂️ Human-in-the-Loop (HITL) Gate: Only high confidence & answered status requests proceed to auto-create a thread-linked Gmail Draft. Low or medium confidence cases are flagged for human review.   
🛡️ Deduplication & Validation: Prevents double-processing of emails already marked with AI-Processed labels and flags unparseable senders.   
🏷️ Automatic Labeling & Audit Logging: Automatically creates and applies AI-Processed / AI-Needs-Review Gmail labels and appends Base64-encoded audit logs to a local file via SSH.  
