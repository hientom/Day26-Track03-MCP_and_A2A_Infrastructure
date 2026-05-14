# LAB 26 - MULTI-AGENT SYSTEM WITH A2A PROTOCOL

## Student Report

**Name:** Nguyen Thi Thu Hien
**MSSV (Student ID):** 2A202600212
**Date:** May 14, 2026

---

## Table of Contents

- [Introduction](#introduction)
- [Lab Setup and Environment Configuration](#setup)
- [Stage 1: Direct LLM Calling](#stage1)
- [Stage 2: LLM + RAG & Tools](#stage2)
- [Stage 3: Single Agent with ReAct](#stage3)
- [Stage 4: Multi-Agent System](#stage4)
- [Stage 5: Distributed A2A System](#stage5)
- [Key Learnings and Insights](#learnings)
- [Conclusion](#conclusion)

---

## <a id="introduction"></a>Introduction

This report documents the completion of Lab 26: "Multi-Agent System with A2A Protocol". The lab demonstrates a progressive learning path from basic LLM usage to a sophisticated distributed multi-agent system using Google's Agent-to-Agent (A2A) protocol.

The system architecture consists of specialized AI agents (Customer, Law, Tax, Compliance) that collaborate through a central Registry service, enabling dynamic discovery and communication via the A2A protocol.

---

## <a id="setup"></a>Lab Setup and Environment Configuration

### Prerequisites Installed:
- Python 3.14.2
- uv package manager (v0.11.14)
- OpenRouter API key configured in `.env` file

### Environment Configuration Steps:
1. Created `.env` file from `.env.example`
2. Added OpenRouter API key
3. Installed dependencies using `uv sync`
4. Configured PATH to include uv Scripts directory
5. Modified `common/llm.py` to add token limits (`max_tokens=512`, `temperature=0.3`, `top_p=0.9`) to work with free tier limitations

---

## <a id="stage1"></a>Stage 1: Direct LLM Calling

**Objective:** Understand basic LLM interaction without tools or external knowledge.

**Implementation:**
- Used `get_llm()` to initialize ChatOpenAI client pointing to OpenRouter
- Sent system prompt + user question directly to LLM
- Received response based solely on LLM's training data

**Results:**
- Successfully analyzed legal consequences of NDA breach
- Demonstrated limitations: no external knowledge, no tools, stateless operation

**Key Learning:** Understanding the foundation of LLM interactions before adding complexity.

---

## <a id="stage2"></a>Stage 2: LLM + RAG & Tools

**Objective:** Add retrieval-augmented generation and tool calling capabilities.

**Implementation:**
- Implemented `search_legal_database` tool to search legal knowledge base
- Implemented `calculate_damages` tool for financial calculations
- Used `.bind_tools()` to attach tools to LLM
- Created manual tool-call loop orchestration

**Results:**
- LLM successfully searched database and calculated damages
- Responses became grounded in specific statutes (DTSA, UCC, etc.)
- Reduced hallucination risk through external knowledge grounding

**Key Learning:** How RAG and tools enhance LLM reliability and accuracy.

---

## <a id="stage3"></a>Stage 3: Single Agent with ReAct

**Objective:** Implement autonomous reasoning loops using LangGraph's `create_react_agent`.

**Implementation:**
- Used `create_react_agent` with multiple tools (search, calculate, compliance check)
- Implemented autonomous Think → Act → Observe loop
- Added verbose output to trace agent reasoning steps

**Results:**
- Agent automatically decided which tools to call and when
- Handled complex multi-part questions about tech startup violations
- Performed multi-step reasoning: search → calculate → search again

**Key Learning:** Autonomous agent orchestration eliminates manual tool-loop implementation.

---

## <a id="stage4"></a>Stage 4: Multi-Agent System

**Objective:** Create specialized agents working in parallel using LangGraph StateGraph.

**Implementation:**
- Defined `LegalState` with annotated reducers for parallel writes
- Created graph topology: `analyze_law` → `check_routing` → parallel `call_tax`/`call_compliance` → `aggregate`
- Used LangGraph's `Send` API for parallel execution
- Implemented routing function to dispatch parallel tasks

**Results:**
- Lead attorney analyzed legal aspects
- Router determined need for tax and compliance specialists
- Specialists ran concurrently and produced domain-specific analyses
- Aggregator combined results into comprehensive response

**Key Learning:** Specialization and parallelism significantly improve analysis quality and efficiency.

---

## <a id="stage5"></a>Stage 5: Distributed A2A System

**Objective:** Deploy agents as independent HTTP services communicating via A2A protocol.

**Implementation:**
- Started Registry service (port 10000) for service discovery
- Started Customer Agent (10100), Law Agent (10101), Tax Agent (10102), Compliance Agent (10103)
- All agents self-registered with registry on startup
- Used `a2a_client.py` for delegation and `registry_client.py` for discovery
- Implemented trace propagation with `trace_id` and `context_id`

**Results:**
- Test client successfully sent request through full A2A flow
- Verified dynamic discovery: agents found each other via registry
- Confirmed trace propagation through all services
- Demonstrated fault tolerance and loose coupling

**Key Learning:** Distributed architecture enables scalability, independent deployment, and production readiness.

---

## <a id="learnings"></a>Key Learnings and Insights

### Technical Concepts Mastered:
- **LLM Fundamentals:** Direct API calls, system/user messages, token management
- **RAG Implementation:** Knowledge base searching, tool integration, grounding responses
- **Agent Orchestration:** ReAct pattern, autonomous reasoning loops, state management
- **Multi-Agent Architecture:** Specialization, parallel execution, graph topology design
- **Distributed Systems:** Service discovery, A2A protocol, HTTP-based inter-agent communication
- **Production Considerations:** Trace propagation, error handling, depth guards, registry patterns

### Architecture Insights:
- **Progressive Complexity:** Each stage builds on previous concepts
- **Specialization Benefits:** Domain-specific agents produce deeper, more accurate analysis
- **Loose Coupling:** A2A protocol enables independent development and scaling
- **Dynamic Discovery:** Registry service eliminates hardcoded URLs and enables flexible deployment

### Practical Applications:
- Legal advisory systems with specialized expertise
- Financial compliance monitoring
- Healthcare diagnosis assistance
- Business intelligence platforms
- Customer support automation with domain specialists

---

## <a id="conclusion"></a>Conclusion

This lab successfully demonstrated the complete evolution from simple LLM usage to a production-ready distributed multi-agent system. The A2A protocol provides a robust foundation for building scalable, maintainable, and specialized AI applications.

The hands-on experience with LangGraph's StateGraph, ReAct agents, and distributed service architecture provides valuable skills for developing modern AI systems that require collaboration between specialized components.

Future work could include adding memory/conversation history, implementing authentication, adding retry logic, or integrating monitoring and observability tools.

