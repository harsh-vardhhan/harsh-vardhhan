---
layout: post
title:  "Testing Multiple LLMs"
---

# Configurable Agent

I have a code in my AI agent which allows me to switch between [Groq](https://console.groq.com/docs/models) and [Ollama](https://ollama.com/library) models easily.


```
if model == 'GROQ':
    # running on cloud
    llm = ChatGroq(
        temperature=1,
        model_name="phi4",
        groq_api_key=os.environ["GROQ_API_KEY"]
    )
elif model == 'OLLAMA':
    # running locally
    llm = ChatOllama(
        model="qwen2.5-coder:3b",
        temperature=1,
    )
```

Why this setup feels crucial while developing AI agents?

Building AI agents is all about testing and optimising for an LLM but at times there no specific LLM that works best for your use case. Even if it's getting the task done, it could be that model size (parameters) is too large. You want to know which is the smallest possible model that can get the task done precisely for your AI agent.