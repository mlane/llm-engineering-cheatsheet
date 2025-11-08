# LLM Engineering Cheatsheet

A timeless guide to **thinking and building like a prompt engineer**. This cheatsheet focuses on core principles and patterns that apply across any model, provider, or tool — whether you're using OpenAI, Claude, Llama, or something that doesn't exist yet.

> This is not a cookbook or quickstart. It's a mindset guide — built for those who want to reason clearly and build reliably with LLMs.

---

## Core Philosophy

LLMs are **probabilistic next-token predictors**, not deterministic logic machines. Prompt engineering is about:

- Designing **clear, structured inputs**
- Working within **context and token limits**
- Thinking **iteratively**, not magically
- Debugging failures like a **system**, not like a mystery

Treat prompts as **interfaces**, not incantations.

---

## Prompting Patterns (Universal)

### Zero-Shot

Ask the model to do a task with no examples.

```txt
"Summarize the following article in 3 bullet points: ..."
```

### One-Shot / Few-Shot

Give one or more examples to improve reliability.

```txt
Review: "Great product, but shipping was late."
Response: "Thanks for your feedback! Sorry about the delay..."

Review: "Terrible quality."
Response: "We're sorry to hear that. Could you share more details so we can improve?"
```

### Role-Based Prompting

Set a role for the model to adopt.

```txt
System: You are a technical support agent who speaks clearly and concisely.
User: My internet keeps cutting out. What should I do?
```

### Constrained Output

Ask for output formats explicitly.

```txt
"List the steps as JSON: [step1, step2, step3]"
```

---

### Sampling Controls

Controls randomness. Range: `0.0` to `2.0`.

- `0.0` → more deterministic, repeatable
- `0.2 – 0.5` → reliable tasks (Ng recommendation)
- `0.7` → balanced (OpenAI default)
- `1.0+` → creative, less reliable

Use `top_p` for more granular control of randomness.

---

## Prompt Structure: The Anatomy

Always structure prompts with these components:

1. **Role** – Who is the model?
2. **Task** – What do you want?
3. **Input** – What info do they need?
4. **Constraints** – What form should the output take?
5. **Examples** _(optional)_ – Show what success looks like

### Example Prompt (all parts applied)

```txt
System: You are a helpful travel assistant that gives concise city guides.
User: I’m visiting Tokyo for 3 days. Suggest an itinerary with 3 activities per day.
Constraints:
- Format your response as bullet points grouped by day.
- Keep each activity description under 20 words.
Example:
Day 1:
- Visit Meiji Shrine in the morning
- Eat sushi at Tsukiji Market
- Explore Shibuya Crossing at night
```

---

## Context Management

- Be **aware of token limits** (e.g. 4k, 8k, 128k)
- Use **summarization** for long chat histories
- Drop irrelevant history when possible
- **Explicit > implicit** — don't assume the model remembers everything
- Fewer tokens = faster responses and lower cost
- Use tools like [OpenAI Tokenizer](https://platform.openai.com/tokenizer) to inspect prompt size

---

## Mindsets That Scale

The best results come when you treat LLMs as tools that **augment your thinking**, not replace it.

### 1. Use AI for Fast, Focused Tasks

AI thrives when the task is something a human could do in a second or two — things like renaming files, summarizing short content, or generating scaffolding. Don’t force it to solve problems that are too vague or complex. **Break hard problems into small, clear ones.** That’s when AI shines.

> _This is aligned with Andrew Ng’s “One-Second Rule” — tasks that a human can perform in under one second are great candidates for automation._

### 2. Prioritize Accuracy, But Accept Imperfection

When you evaluate a model's performance, **accuracy is key** — but perfect accuracy is not realistic. Ambiguity, nuance, and subjectivity are baked into language. Instead of aiming for 100%, aim for **consistent and explainable behavior**, and iterate. Treat errors as feedback loops, not failures.  
_(In practice, many real-world tasks operate safely with 70–90% accuracy — just make sure you know your risk tolerance.)_

> _Andrew Ng emphasizes setting high but achievable accuracy standards, and treating improvement as an ongoing process._

### 3. You and the LLM Are Partners

Don't outsource your learning. Let the LLM **guide, question, and collaborate** — not do everything for you. Ask it for scaffolding, instructions, or alternatives, then build it yourself. That way, you stay in control and deepen your understanding. **If you can't maintain the code later, you're not really building.**

> _Experts recommend a human-in-the-loop mindset where you learn with the AI, not through it._

---

## Evaluation Principles

LLM output is fuzzy. Define quality like this:

- Does it meet the **task objective**?
- Is the output **formatted correctly**?
- Would a human say it's **reasonable**?
- Can you detect regressions with **A/B comparisons**?

### Perspective Shift

LLM success isn’t about perfection — it’s about clarity, consistency, and feedback loops.\
**Reliable > perfect.** Iterate like a product, not like a test.

---

## Common Failure Modes

| Symptom         | Likely Cause                             |
| --------------- | ---------------------------------------- |
| Hallucination   | Vague or underspecified prompts          |
| Repetition      | Poor constraint or unclear output format |
| Refusal         | Misalignment between task and role       |
| Loss of context | Too much history or poor summarization   |

---

## Debugging Checklist (When Output Fails)

- Is the **task clearly stated**?
- Are you using the right **prompting pattern**?
- Is there a clear **role and structure**?
- Could the context window be too full?
- Try asking the model: _“Why did you respond this way?”_

---

## Structured Output & Tool Use (Advanced)

Some models support **function calling** or **structured tool use** — great for API responses or JSON output.

> Example: OpenAI’s `function_call` or `response_format="json"`

---

## Recommended Resources

- [OpenAI Best Practices](https://platform.openai.com/docs/guides/prompt-engineering)
- [LangChain Docs](https://python.langchain.com/docs/introduction)
- [Ollama for Local Models](https://ollama.com)

---

## Minimal Python Example

```python
import os

from openai import OpenAI

client = OpenAI(
    api_key=os.environ.get("OPENAI_API_KEY"),
)

response = client.chat.completions.create(
    model="gpt-4o",
    messages=[
        {"role": "system", "content": "You are a concise technical writer."},
        {
            "role": "user",
            "content": "Explain what a vector database is in simple terms.",
        },
    ],
    temperature=0.3,  # Lower = more deterministic
)

print(response.choices[0].message.content)
```

---

## Final Thought

This guide helps you stay grounded when everything else is changing. Focus on clarity. Prompt with intent. And always think like an engineer.
