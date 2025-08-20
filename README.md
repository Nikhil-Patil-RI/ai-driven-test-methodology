# 📄 AI-Agent Driven Testing Workflow for Microservices

## 🎯 Purpose
This document provides a standardized approach for writing test cases across all microservices using **AI-assisted agents** (e.g., Augment Code, Cursor, Roo Code, GitHub Copilot).  
The goal is to ensure **consistent test methodology**, **90–95% coverage**, and **high-quality test design** across the team.

---

## How to Use This Methodology

1. **Understand the Context**
   - Our test methodology is defined in a shared `TESTING_METHODOLOGY.md` file.  
   - This ensures that every service follows the same structure, coverage goals, and mocking strategies.

2. **Copy Methodology to Your Service**
   - Copy the `TESTING_METHODOLOGY.md` file into your microservice’s `docs/` directory.  
   - This gives AI coding agents context for how tests should be written in your service.  
   - Example:
     ```bash
     cp ../shared/TESTING_METHODOLOGY.md ./docs/TESTING_METHODOLOGY.md
     ```

3. **Use AI Agents for Test Generation**
   - Open your service project in **Augment Code, Cursor, Roo Code, or other AI-assisted IDEs**.  
   - Provide the agent with the methodology file (`docs/TESTING_METHODOLOGY.md`) so it has context on:
     - Test organization & naming conventions  
     - Mocking patterns for gRPC, database, and external services  
     - Coverage requirements (90%+ per file, 90–95% overall)  
     - Fixture design patterns  
     - Error handling and edge case coverage  

4. **Provide the Testing Prompt**
   - After including the methodology file, give the agent the **testing prompt** found in `AI_AGENT_TESTING_PROMPT.md`.  
   - This prompt tells the AI exactly **how to start writing test cases** for your service functions.

---

## Standard Workflow for Each Developer

1. Copy the shared testing methodology file into your microservice.  
2. Open the microservice in your AI-assisted IDE.  
3. Load the methodology file as context.  
4. Paste the `AI_AGENT_TESTING_PROMPT.md` into the AI agent.  
5. Review the generated test cases:  
   - Ensure they follow naming conventions.  
   - Ensure they use mocks (not real DB/API calls).  
   - Verify coverage targets are met.  
6. Run tests with coverage:
   ```bash
   poetry run pytest --cov=app --cov-report=term-missing
