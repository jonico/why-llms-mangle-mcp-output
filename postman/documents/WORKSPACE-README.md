## 📚 Documentation Overview

This workspace contains a field study on **LLM transcription failures with MCP tool output**. The study documents a real incident where Claude Sonnet 5 fabricated dates when filtering a 159-record JSON payload from an MCP tool, despite the tool returning byte-for-byte identical data across multiple calls.

### 🔬 Main Article: MCP Response OK, Wrong Answer

**File:** [README.md](https://github.com/jonico/why-llms-mangle-mcp-output/blob/main/README.md)

The primary blog post explaining why LLMs sometimes mangle perfectly valid MCP tool output. Key findings:

- **The Problem:** LLMs regenerate text token-by-token rather than copying, leading to transcription errors in long structured data
    
- **When It Happens:** Attention degrades over repetitive context (\~150+ records), especially in the middle sections
    
- **The Trigger:** Anchoring on salient values (like "current year") can override literal source data
    
- **The Fix:** Push filtering into code (jq, grep, scripts) rather than asking models to eyeball and retype data
    
- **The Tool:** Postman's MCP Inspector lets you compare raw tool output against agent summaries to catch these errors
    

**Real Example:** Claude Sonnet 5 mislabeled 2025 dates as 2026 when filtering sparkles data, fabricating entries 7 & 8 in an 8-item filtered list.

### 📝 Full Conversation Transcript

**File:** [anonymized-agent-conversation.md](https://github.com/jonico/why-llms-mangle-mcp-output/blob/main/anonymized-agent-conversation.md)

The complete, unfiltered conversation showing the exact exchange where fabrication occurred. Includes:

- The original user request: "Can I see all the sparkles for @user.a since May 1st?"
    
- The agent's response with 2 fabricated 2026 dates (items 7 & 8 from 2025 source data)
    
- The agent's first wrong explanation: "the tool may be returning sample/demo data"
    
- The correction after seeing raw tool output
    
- The agent's second wrong explanation: "the tool returned different data"
    
- The final accurate diagnosis after a third tool call proved the output was stable
    

**Key Insight:** Even when confronted with evidence, the agent initially misdiagnosed the issue, claiming tool non-determinism before eventually recognizing its own transcription error.

### 🔧 Technical Specifications

**File:** [model-details.md](https://github.com/jonico/why-llms-mangle-mcp-output/blob/main/model-details.md)

Reproducibility notes and environment details:

- **Model:** Claude Sonnet 5 (claude-sonnet-5)
    
- **Runtime:** Claude Agent SDK, agentic mode with file tools + sandboxed shell + MCP servers
    
- **Tool:** getSparkles MCP tool returning 159 JSON records (\~15-20K tokens)
    
- **Verified:** Tool output was byte-for-byte identical across 3 sequential calls
    
- **Divergence Point:** Roughly 82% through the 159-record list (around record 130)
    
- **Important Note:** Explanations are behavioral inferences from observed symptoms, not confirmed from model internals
    

---

## 🧪 Collection: Sparkles Experiments

The workspace includes a collection with two requests demonstrating the issue:

1. **Sparkles MCP Call** - Direct MCP request showing the raw 159-record payload
    
2. **Showing older entries claiming 2026** - LLM-powered request demonstrating the fabrication
    

Use these to reproduce and verify the findings documented in the blog posts.