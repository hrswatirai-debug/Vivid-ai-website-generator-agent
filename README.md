# Vivid-ai-website-generator-agent
### Conversational AI agent that generates complete, production-ready HTML websites from a single text prompt — with real photography, randomised design direction, and built-in output validation.

---

## Overview

This n8n workflow converts a plain-English website description into a complete, self-contained HTML page in under 15 seconds. It classifies intent, randomly assigns a professional design direction, fetches real contextual photography from Pexels, and sends a richly structured prompt to GPT-4o — returning validated, deployment-ready HTML directly in the chat interface.

Built as an applied Generative AI project under the **Black Elephant AI Learning Ecosystem**, this workflow demonstrates end-to-end agentic pipeline design: multi-step prompt engineering, API orchestration, structured JSON output parsing, and programmatic HTML validation — all without a backend server.

---

## Architecture

```
Chat Trigger
    └── Intent Check (JS classifier)
            ├── [non-website input] → Guide Response
            └── [website intent] → Prompt Refiner (random design direction)
                                        └── Build Request Body
                                                └── Fetch Images (Pexels API)
                                                        └── Merge Context (JS)
                                                                └── OpenAI GPT-4o (structured JSON output)
                                                                        └── Extract & Validate HTML (JS)
                                                                                └── Return to Chat
```

**Node count:** 10  
**External APIs:** OpenAI GPT-4o, Pexels  
**Trigger:** n8n built-in chat interface (publicly accessible)

---

## Key Engineering Decisions

**Intent Classification without an LLM call**  
A JavaScript classifier runs before any API call. It detects greetings, off-topic inputs, and prompts shorter than 15 characters, routing them to a guide response. This eliminates wasted OpenAI tokens on non-actionable inputs.

**Randomised Design Direction System**  
Six named design directions are defined in code with full aesthetic briefs:

| Direction | Aesthetic |
|---|---|
| `editorial` | Serif display, earth tones, asymmetric grid |
| `brutalist-modern` | Oversized sans-serif, high contrast, raw layout |
| `soft-luxury` | Cream/emerald palette, rounded, photography-forward |
| `tech-minimal` | Near-black bg, neon accent, dense information hierarchy |
| `warm-handcrafted` | Terracotta, humanist type, organic shapes |
| `gradient-vibrant` | Mesh gradients, glassmorphism, high-energy color |

On every execution, one direction is randomly selected and injected into the system prompt as a detailed aesthetic brief. No two generations produce the same visual style.

**Real Photography via Pexels API**  
The user's prompt is passed directly as the Pexels search query. Up to 6 landscape images are fetched and their URLs embedded into the OpenAI request payload, so the model can reference real photography in `<img>` tags — no placeholder images.

**Structured JSON Output**  
OpenAI is prompted to return a JSON object with three fields: `html`, `title`, and `description`. This avoids markdown fence parsing hacks and allows field-level extraction with zero regex.

**Programmatic HTML Validation**  
Before the output reaches the chat, a JavaScript validator checks:
- DOCTYPE declaration present
- Closing `</html>` tag present
- Minimum length threshold (rejects truncated outputs)
- Tailwind CDN injection (auto-appended if missing)
- Viewport meta injection (auto-appended if missing)

Retry logic (2 attempts, 3s delay) is configured on the OpenAI HTTP node.

---

## Sample Outputs

| Prompt | Design Direction Applied |
|---|---|
| `build a website for a primary school` | gradient-vibrant |
| `wellness and meditation platform` | soft-luxury |
| `fresh produce grocery store` | warm-handcrafted |

All outputs are complete, single-file HTML pages renderable directly in a browser or HTML viewer with no post-processing.

---

## Setup

### Prerequisites
- n8n (self-hosted via Docker, v2.14+)
- OpenAI API key (GPT-4o access)
- Pexels API key (free tier sufficient)

### Import
1. Clone or download this repository
2. In n8n: **Settings → Import workflow** → upload `World-Class_Website_Generator__Lean_.json`
3. Configure credentials:
   - `OpenAI account` → your OpenAI API key
   - Update the Pexels `Authorization` header in the `Fetch Images Pexels` node with your Pexels API key
4. Activate the workflow
5. Click the **Chat** button in the n8n toolbar

### Usage
Send any website description in the chat:
```
A portfolio site for a AI consultant
An e-commerce homepage for a local grocery shop 
A home page of a school website
A home page of Spiritual wellness website
```

The agent returns complete HTML. Copy it into any HTML viewer or save as a `.html` file.

---

## Project Context

**Curriculum:** Black Elephant AI Learning Ecosystem — Applied Generative AI Projects  
**Mentor:** Shabari  
**Author:** Swati Rai | HR professional turned AI entrepreneur  
**Stack:** n8n · OpenAI GPT-4o · Pexels API · Tailwind CSS · JavaScript  
**GitHub:** [hrswatirai-debug](https://github.com/hrswatirai-debug)

---

## Known Limitations

- No persistent memory — each chat message generates an independent website (not iterative by design in this lean version)
- Pexels images are contextual but not always perfectly matched to the prompt
- Generation time: 10–20 seconds depending on OpenAI API latency
- Tailwind CDN used for simplicity; not suitable for production deployment without a build step

---

## License

MIT
