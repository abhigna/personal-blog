---
title: 'Building AutoLLMBench: How I Taught AI Agents to Maintain Their Own Leaderboard'
date: 2025-05-09 
tags: 
    - tech
    - ai
---

<figure style="margin: 2em 0; text-align: center;">
  <img 
    style="max-width: 100%; height: auto; border-radius: 8px; box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1);" 
    src="/images/building-autollmbench-fig.jpeg" 
    alt="t" 
    width="400"
  />
  <figcaption style="margin-top: 1em; color: #666; font-style: italic; font-size: 0.95em;">
   Wrangling
  </figcaption>
</figure>


AutoLLMBench started from a simple curiosity: could I leverage AI's remarkable generalization abilities to automate the tedious task of maintaining benchmark leaderboards? Most of these leaderboards depend heavily on manual effort—collecting data from various sites, formatting it correctly, and updating everything regularly. It felt like a perfect problem for AI agents, though the actual journey turned out far more complex and fascinating than I initially anticipated.

Check out the live leaderboard at [AutoLLMBench](https://abhigna.github.io/autollmbench/).


## Why AutoLLMBench?

My motivation wasn't just about tracking benchmarks—anyone can do that manually. Instead, I wanted to explore how effectively AI agents could handle practical automation tasks. Could they navigate the messy realities of inconsistent naming, varying data formats, and the continuous churn of new data? Could an AI-driven system handle it reliably, with minimal human oversight?

## Designing the Agents

At first glance, the concept was straightforward: let AI manage repetitive extraction tasks. But reality quickly added layers of complexity. I realized the real challenge wasn’t just extraction; it was ensuring the reliability of these agents when faced with inconsistent naming conventions and unpredictable formatting across sources. The system needed intelligence, not just automation.

## System Overview

AutoLLMBench operates through several carefully orchestrated steps:

1. **Data Extraction**, initiated by a simple script:
   * Retrieves benchmark page content using the `crawl4ai` library.
   * A **Benchmark Extraction Agent** (powered by an LLM) parses the content directly, intelligently pulling out model scores.
   * A **Model Classification Agent** (also powered by an LLM) resolves model name inconsistencies, mapping them to standardized, canonical IDs and identifying new models along the way.
   * The refined data is then saved as structured JSON.
2. **Static Site Generation**:
   * Runs a separate build step (`llmbench build`) to convert JSON data into a fully functional static site.
   * Generates HTML, CSS, and JavaScript files, creating an interactive, easy-to-navigate leaderboard.
3.  **Deployment**: 
   * The resulting static site is easily deployed to GitHub Pages.

## Technical Insights and Implementation

Key technical decisions significantly shaped the project:

* **LLM-Powered Data Extraction:** This was a core exploration. Instead of writing custom scraping logic with specific CSS selectors or XPath queries for each benchmark site – which is brittle and requires constant manual updates – the approach here was different. The agent fetches the raw page content (using `crawl4ai`) and **presents this raw text/HTML directly to an LLM**. The AI is then prompted to understand the page structure and extract the relevant models and their scores based on contextual understanding. This significantly reduces the site-specific manual configuration typically associated with web scraping.
* **Data Validation and Normalization:** Leveraging structured outputs via Pydantic models (`BenchmarkData`, `ModelClassificationResult`) was crucial. This allowed for automatic validation of the LLM's extracted data and classification results, even including some programmatic checks to handle common inconsistencies.

* **Consistent Model Mapping**: Handling diverse naming conventions was one of the trickiest parts. The Model Classification Agent intelligently matches varied model names across different sources to standardized internal identifiers. Although not perfect, this approach generalizes surprisingly well compared to manually hardcoding rules. This flexibility was key to scaling the system effectively.

AutoLLMBench also features a simple and flexible configuration structure that defines each benchmark clearly. Here's a simplified example configuration for clarity:

```json
{
  "name": "Aider LLM Benchmark",
  "description": "Polyglot benchmark testing LLMs on coding exercises across multiple languages",
  "methodology": "Automated evaluation of LLMs' code editing abilities",
  "source_url": "https://aider.chat/docs/leaderboards/",
  "type": "code_edit",
  "metrics": [
    {"id": "percent_correct", "name": "Percent Correct", "description": "Percentage of exercises solved"},
    {"id": "cost", "name": "Cost", "description": "Total benchmark cost"}
  ],
  "display": {"primary_metric": "percent_correct"}
}
```

## Challenges and Lessons Learned

Building AutoLLMBench wasn't without challenges:

* **Handling Diverse Data Formats**: Despite AI’s strengths, the sheer variety in online data presentation demanded multiple iterations of revising the prompt and architecture.

* **Quality for AI Outputs**: Ensuring accuracy from LLM outputs was tricky. It required carefully tuned prompts, and frequent iterations to refine the AI's behavior and improve reliability.

These challenges reinforced a crucial lesson: structured validation and thoughtful prompting are non-negotiable elements in building robust, AI-powered pipelines.

## Personal Insights and Reflections

Working on AutoLLMBench was deeply rewarding. It highlighted both the impressive strengths and the practical limits of current AI systems:

* Clear and precise prompting, coupled with rigorous data schemas, proved essential for reliable AI-driven data processing.
* Human oversight still matters—particularly when integrating new data sources or addressing unexpected pipeline issues.
* Iterative development was key. Gradual refinement of the agents and their prompts significantly improved performance and reliability.

## Final Thoughts

Exploring AI’s potential in practical automation has been incredibly fulfilling. AutoLLMBench showcases how AI can meaningfully reduce manual effort, provided that it’s supported by structured, rigorous validation. Going forward, I'm excited to expand beyond LLM benchmarks and explore more advanced AI-driven validation techniques, further pushing the boundaries of automation.

Building practical, AI-powered systems is collaborative by nature, and sharing insights accelerates everyone's progress. If you’re exploring similar paths or have your own stories to share, I'd genuinely love to hear from you.