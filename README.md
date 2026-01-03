Multi-Agent LLM System for Scientific Research Assistance

1. Problem Overview

This project implements a multi-agent LLM system that functions as an Intelligent Scientific Research Assistant.
Based on user input, the system identifies key concepts, searches for relevant scientific literature, generates a coherent research summary, and interacts with the user to refine or expand the research.

The system is designed as a coordinated set of autonomous agents connected through task handoff.


2. Problem Analysis

The addressed problem is complex and can be decomposed into smaller, well-defined subproblems:
1. Understanding the user’s intent and extracting key concepts.
2. Searching for relevant scientific publications.
3. Synthesizing information from retrieved literature into a structured text.
4. Enabling iterative interaction with the user for follow-up questions and research expansion.

Each subproblem is handled by a dedicated agent, allowing modularity, extensibility, and robustness.


3. Subproblems and Agents

The system consists of four subproblems, each solved by a specialized agent.

1. Request Analysis
-Task: Validate user input, extract keywords, identify the main research topic.
-Input:User input string.
-Output: Dictionary containing the main topic and extracted keywords.
-Agent: RequestAnalysisAgent

2. Literature Search
-Task: Search for relevant scientific papers.
-Input: Dictionary containing extracted keywords.
-Output: List of scientific papers with metadata.
-Agent: LiteratureSearchAgent(uses the arXiv API as an external tool)

3. Text Generation
-Task: Generate a coherent research overview from the retrieved literature using an LLM.
-Input: Dictionary containing topic, keywords, and retrieved papers.
-Output: Markdown-formatted research summary.
-Agent: TextGenerationAgent

4. User Interaction
-Task: Enable user follow-up questions and optionally trigger additional research cycles.
-Input: User input string.
-Output: Generated response or restart of the research workflow.
-Agent: InteractiveAgent


4. Agent Descriptions
   
1. RequestAnalysisAgent
Uses the Qwen/Qwen2.5-0.5B-Instruct model.
Validates user input and extracts keywords and topic.
Automatically hands off the task to LiteratureSearchAgent.
Handled errors:
  Empty input
  Input too short
  No valid keywords detected

2. LiteratureSearchAgent
Uses the arXiv API as an external tool.
Constructs search queries from extracted keywords.
Parses and filters search results.
Hands off results to TextGenerationAgent.
If no results are found, hands control to InteractiveAgent.
Handled errors:
  Network or timeout errors
  No search results
  Invalid query parameters

3. TextGenerationAgent
Uses the Qwen/Qwen2.5-0.5B-Instruct model.
Generates a structured markdown document containing:
  Introduction
  Literature synthesis
  Key concepts
  Current trends
  Conclusion
Hands off control to InteractiveAgent.
Handled errors:
  Insufficient literature
  LLM generation failure
  Invalid or incomplete output structure

4. InteractiveAgent
Uses the Qwen/Qwen2.5-0.5B-Instruct model.
Displays generated content to the user.
Uses LLM reasoning to decide whether the user:
  Asks a new question
  Requests expansion of existing research
Can restart the research cycle if needed.

5. System Workflow and Example
   
Workflow Overview
User
 ↓
RequestAnalysisAgent (LLM)
 ↓
LiteratureSearchAgent (LLM + arXiv tool)
 ↓
TextGenerationAgent (LLM)
 ↓
InteractiveAgent (LLM)
 ↓
User

Agents communicate via handoff, where each agent explicitly transfers control to the next one using a handoff_to() mechanism.

Example Execution:
User input:
"Deep learning"
RequestAnalysisAgent extracts:
Topic: Deep Learning
Keywords: ["deep", "learning"]
LiteratureSearchAgent queries arXiv and retrieves five papers.
TextGenerationAgent generates a structured markdown summary.
InteractiveAgent displays the text and waits for follow-up questions.

6. Conclusions

Each agent is autonomous and guided by a specialized system prompt.
Multi-layer error handling improves system stability.
The system uses a pretrained LLM; fine-tuning on scientific literature could further improve quality.
LLMs are used for decision-making within agents, while task orchestration and handoff logic are explicitly implemented.
