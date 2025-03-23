---
title: "Engineering with LLMs: How I Built a System to Curate My Tech News"
date: 2025-03-22 01:12:41
tags: 
    - tech
    - llms
---

<figure style="margin: 2em 0; text-align: center;">
  <img 
    style="max-width: 100%; height: auto; border-radius: 8px; box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1);" 
    src="/images/engineering-with-llms-news-curation-main-fig.jpeg" 
    alt="A visualization of filtering the overwhelming stream of news into a personalized digest" 
    width="400"
  />
  <figcaption style="margin-top: 1em; color: #666; font-style: italic; font-size: 0.95em;">
    From information overload to personalized insight
  </figcaption>
</figure>

I got my weekly [HackerNewsletter](https://hackernewsletter.com/) with a long list of reading recommendations. After scanning through about 30 of them, I found exactly zero articles that interested me. This isn't to blame the newsletter (I've been a subscriber for over 7 years) – their curation is quite good. But the gap between what they think I want and what I actually care about still feels massive.

This experience pushed me into doing something I've been contemplating recently - to try to build my own curated tech news while also learning a bit more about LLMs (inspired by the excellent daily AI News Digest).

So I built my own tech news curation system using LLMs. The results have been surprisingly effective, and the approach offers some interesting lessons about how to build reliable systems based on LLMs.

## The Problem: Information Overload

Every morning, I used to start my day with the ritual of scanning Hacker News and various newsletters. Despite the time invested, I constantly felt I was:

1. Missing important developments relevant to my interests
2. Wasting time on clickbait or shallow content
3. Getting stuck in filter bubbles that reinforced existing views

The core problem wasn't that good content wasn't available – it was that finding it among the noise had become a job in itself. And unlike recommendation algorithms optimizing for clicks, I wanted to optimize for long-term value and genuine insight.

## Building a Better Filter: Evaluation-First AI

After reading Hamel Husain's excellent piece on [building evaluation systems for LLMs](https://hamel.dev/blog/posts/evals/), I had decided to use his key insight: **most AI projects fail because they lack robust evaluation systems**. Instead of jumping straight to building the perfect news filter, I needed to start by defining what "good" looks like and systematically measuring it.

The approach I landed on was simple but powerful: build a system where every filtering decision is evaluated explicitly, and those evaluations feed back into improving the system.

## The Judge System

The most important part of my system is the evaluation framework - the judge system that assesses the quality of filtering decisions. Rather than assuming my filters would work perfectly, I built a feedback loop to continuously improve them.

Here's how the complete evaluation process works:

1. An LLM makes the initial filtering decision based on my stated interests
2. All LLM decisions are logged as traces with structured output
3. A separate judge app generates critiques for the decision using a more powerful model, along with reasoning
4. I periodically review the judgments using the judge's critique as a base for my own assessment
5. The system tracks agreement rates between me and the filter over time

Instead of writing lengthy guidelines that might miss my actual preferences, I implemented a comprehensive `JudgeSystem` class that:

- Tracks all LLM evaluations in trace logs
- Stores human feedback on those evaluations
- Calculates agreement statistics between human and model judgments
- Extracts examples of disagreements to improve future prompts

What makes this approach powerful is that it focuses on binary judgments (pass/fail) backed by explicit reasoning that I can review. This turns out to be far more valuable than complex scoring systems or multi-dimensional evaluations.

## The Judge UI

To make evaluation easy and consistent, I built a Streamlit-based judge app that provides:

<figure style="margin: 2em 0; text-align: center;">
  <img 
    style="max-width: 100%; height: auto; border-radius: 8px; box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1);" 
    src="/images/engineering-with-llms-news-curation-judge-app.png" 
    alt="A visualization of filtering the overwhelming stream of news into a personalized digest" 
  />
  <figcaption style="margin-top: 1em; color: #666; font-style: italic; font-size: 0.95em;">
    Streamlit makes it easy to build tiny domain specific apps
  </figcaption>
</figure>


When reviewing articles in the judge app, I can:
- See the original article alongside the LLM's evaluation
- Generate a critique of the LLM's decision using a separate critique model
- Provide my own decision (pass/fail) and notes

Over time, these disagreement examples get incorporated back into the filtering prompt, creating a continuous improvement loop. This feedback mechanism has been crucial - even a small amount of human feedback dramatically improves results.

## The System Architecture

The complete system consists of several simple Python modules that work together:

1. **Data Collector (`hn_data_collector.py`)**: Fetches articles from Hacker News via RSS and uses Jina.ai to extract content
2. **LLM Gateway (`llm_gateway.py`)**: Creates a unified interface for LLM interactions with structured outputs using Instructor
3. **Content Filter (`content_filter.py`)**: The LLM that makes initial decisions using a Pydantic model for consistency
4. **Content Summarizer (`content_summarizer.py`)**: Generates concise summaries of articles that pass the filter
5. **Judge System (`judge_system.py`)**: Manages feedback examples and evaluation metrics
6. **Judge App (`judge_app.py`)**: A Streamlit app that lets me review judgments and provide feedback
7. **Digest Compiler (`digest_compiler.py`)**: Formats everything into a readable digest

## Using Structured Outputs for Reliability

One of the most important improvements I made was using Pydantic models (via Instructor) for structured LLM outputs. Here's what a typical content filter response looks like:

```python
class ContentEvaluation(BaseModel):
    pass_filter: bool = Field(
        description="Whether the article passes the filter based on relevance to user interests"
    )
    main_topics: List[str] = Field(
        description="The main topics covered in the article (be specific and descriptive)"
    )
    reasoning: str = Field(
        description="Brief explanation for why this article passes or fails the filter"
    )
    specific_interests_matched: List[str] = Field(
        description="The specific user interests that this article matches",
        default=[]
    )
```

This structured approach has several advantages:

1. Consistent outputs that can be easily parsed and stored
2. Explicit fields for reasoning that make the LLM's decision-making more transparent
3. Simpler error handling and validation

## Implementing the LLM Gateway

One of the most useful components I built was the `LlmGateway` class that handles all LLM interactions. It provides:

1. A consistent interface for prompting and response parsing
2. Automatic caching to reduce API costs and speed up testing
3. Comprehensive logging of all interactions
4. Structured output parsing via Instructor

This abstraction layer makes it easy to swap between different LLM providers and models. Currently, I'm using OpenRouter to access various models, but the system could easily work with OpenAI, Anthropic, or other providers.

## Conclusion: Results, Lessons, and Future Directions

After running this system for about a month, the results have been transformative. My daily digest now contains just 2-3 high-quality articles (instead of sifting through 30+ iffy articles). The system catches 90% of what I would manually select and—perhaps more importantly—filters out 95%% of what I don't want to see.

Building this system taught me several valuable lessons about working with LLMs:

1. **Evaluation is everything**: Clear metrics for success make iterative improvement possible
2. **Human feedback loops are essential**: Even a small amount of human feedback dramatically improves results
3. **Binary decisions work better than scores**: Simple pass/fail decisions with explicit reasoning are more reliable han complex scoring systems

I'm actively exploring several technical improvements to make the system even more effective: fine-tuning a custom model on my historical judgments to better match my preferences, implementing a RAG-based approach to dynamically retrieve and incorporate the most relevant human feedback examples, making the summarization component better, and expanding data sources beyond Hacker News.

If you're drowning in content and not finding what matters to you, consider building your own filter. I've made the full codebase available at [GitHub link](https://github.com/abhigna/news-digest), so you can adapt it for your own interests. The tools are more accessible than ever, and the payoff is substantial.