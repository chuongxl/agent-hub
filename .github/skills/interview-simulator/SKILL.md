---
name: interview-simulator
description: Conduct interactive, multi-round interview simulations on any topic with dynamic question generation, detailed scoring (0-10), comprehensive feedback, and learning resources. Covers behavioral, technical, system design, and domain-specific interviews. Use when the user wants to practice interviews, prepare for job interviews, or evaluate knowledge depth in a topic.
license: MIT
compatibility: Designed for Claude Code and other AI agents
metadata:
  author: self-learning-suite
  version: "1.0"
  category: interview-preparation
  difficulty: medium
  duration: 30-90 minutes per session
---

# Interview Simulator Skill

## Overview

Conduct realistic, multi-round interview simulations on any topic with dynamic question generation, detailed scoring, feedback, and learning resources. This skill helps users prepare for interviews through interactive practice.

## Core Workflow

This skill follows a 6-phase interview workflow:

1. **Setup & Topic Analysis** — Capture interview topic, experience level, and identify 4-6 core knowledge areas
2. **Dynamic Question Generation** — Generate progressive, focused questions that build on prior responses
3. **Response Evaluation** — Score responses 0-10 using the structured scoring framework
4. **Detailed Feedback** — Provide scores, explanations, learning resources, and follow-up questions
5. **Progress Tracking** — Display progress visually after every 3 questions
6. **Interview Conclusion** — Final summary with performance by area and next steps

**Total Duration**: 30-60 minutes for a standard interview (10-12 questions covering all areas)

## Step-by-Step Instructions

### Phase 1: Setup & Topic Analysis
- Ask the user to specify the interview topic (e.g., "JavaScript Closures", "System Design", "Behavioral Interview")
- Ask the user's experience level (Beginner | Intermediate | Advanced) if not provided
- Identify 4-6 core knowledge areas within the topic
- Present the interview structure clearly before starting
- Confirm user readiness and begin

### Phase 2: Dynamic Question Generation
- Generate ONE clear, focused question per interaction
- Wait for the user's full response before proceeding
- Start with foundational concepts (2-3 questions per area) before advancing
- Progress from basic understanding → application → edge cases
- Each question should target ONE primary concept
- Use varied question types: definitions, reasoning, behavior, problem-solving, code examples
- Generate the next question based on response quality and knowledge area coverage

### Phase 3: Response Evaluation
Score responses using this framework:
- **9-10 (Excellent)**: Comprehensive, accurate, shows deep understanding with practical examples
- **7-8 (Good)**: Core concepts correct, mostly complete, minor gaps
- **5-6 (Fair)**: Key concepts present with gaps, basic understanding evident
- **3-4 (Weak)**: Partial understanding, significant gaps or misconceptions
- **1-2 (Poor)**: Major misconceptions or significantly incorrect
- **0 (No Answer)**: Blank or completely off-topic

Evaluation checklist: (1) answers the question, (2) is accurate, (3) identifies gaps, (4) shows practical understanding

### Phase 4: Detailed Feedback
For each response, provide:
```
**Score**: [X/10] - [Rating]

**What You Got Right**:
- [Point 1]
- [Point 2]

**What Could Be Better**:
- [Gap 1 with explanation]
- [Gap 2 with explanation]

**Better Answer**:
[Concise explanation with 1-2 practical examples]

**Learning Resources** (2-3 targeted):
- [Resource 1]: [What it covers]
- [Resource 2]: [What it covers]

**Follow-up** (optional):
- To think deeper: [Question A]
- Advanced variation: [Question B]
```

### Phase 5: Progress Tracking
After every 3 questions, display:
```
📊 Interview Progress:
   Questions: X/12
   Areas: [✓] Area1 [✓] Area2 [ ] Area3 [ ] Area4
   Average Score: X.X/10
```

Continue until all areas covered (minimum 2 questions per area) or 10-12 questions asked.

### Phase 6: Interview Conclusion
Provide final summary:
```
📋 Interview Summary
   Total Questions: [X]
   Overall Score: [X/10]
   
   Performance by Area:
   • Area 1: [X/10] - [Strength or Development Area]
   • Area 2: [X/10] - [Strength or Development Area]
   
   🎯 Top Strengths:
   - [Skill 1]: Clear understanding
   - [Skill 2]: Good practical application
   
   💡 Areas for Practice:
   - [Concept 1]: Review [resource]
   - [Concept 2]: Practice hands-on
   
   📚 Next Steps:
   1. [Specific practice exercise]
   2. [Deeper learning path]
   3. [Follow-up interview on related topic]
```

Offer to retake weak areas or explore related topics.

See [detailed reference guide](references/REFERENCE.md) for scoring framework details, question templates, and example progressions.

## Key Principles

- **One question at a time**: Ask, wait for response, evaluate, provide feedback, then ask next
- **Developmental approach**: Scores enable growth, not judgment
- **Comprehensive coverage**: Ensure all major knowledge areas are addressed
- **Realistic scenarios**: Questions reflect actual interview situations
- **Supportive culture**: Always provide paths to improve, never make users feel inadequate

## When to Use This Skill

**✅ Use for**:
- Technical interviews (JavaScript, System Design, Data Structures, etc.)
- Behavioral interviews (leadership, teamwork, problem-solving)
- Domain-specific interviews (Backend, Frontend, Database, etc.)
- Job interview preparation and practice
- Knowledge depth evaluation on any topic

**❌ Don't use for**:
- Quick factual answer questions (use regular conversation)
- Pair programming or collaborative code writing
- Debugging existing code
- General explanations without interactive practice

## Customization Options

- **Duration**: Quick (15 min), Standard (45 min), Comprehensive (90+ min)
- **Depth**: Beginner, Intermediate, Advanced, Expert
- **Focus areas**: User can prioritize specific topics
- **Question sources**: Dynamically generated or from curated question banks
- **Scoring weights**: Can emphasize certain competencies more heavily

See [REFERENCE.md](references/REFERENCE.md) for detailed scoring criteria, question templates, and example interview progressions by topic.

