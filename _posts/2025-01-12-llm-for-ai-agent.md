---
layout: post
title:  "Which LLM to choose for building AI agent?"
---

# Which is the right model?
The right model (LLM) is the one that helps your AI agents achieves your task. But the real question you should be asking to yourself is

> which is the smallest possible model that helps AI agent acheive it's goals?

why does it matter? well the smaller paramter size of the model, the cheaper it will be to prompt. There are high chances that a model like [Llama 3.3:70B](https://ollama.com/library/llama3.3:70b) will give more accurate responses but the cost of running a model this large could go significantly high as prmopt increases.

So the right way is to start testing your AI agent with the smallest possile model which can deliver similar accuracy.

# Can small models compete with large models?
Answer to this question lies in in the context you ask. Probably if you ask can smallers models over all smarter than larger models then answer is no but smaller models can be equally or even more smart at times for certain use case. That's often cause the models were trained/fine-tuned for that particular use-case.

In my own RAG application, I have tested different models with same prompts to test the capability. Here the RAG application fetches scraped data of Google Flights.

**Prompt**: What is the cheapest flight from Hanoi to New Delhi?

**❌ [qwen2.5:3b](https://ollama.com/library/qwen2.5:3b)**
```
SELECT date, origin, destination, price_inr, flightType 
FROM flights 
WHERE origin = 'Hanoi' AND destination = 'New Delhi' 
ORDER BY date ASC 
LIMIT 5
```

**✅ [qwen2.5-coder:3b](https://ollama.com/library/qwen2.5-coder)**
```
SELECT date, origin, destination, price_inr, flightType 
FROM flights 
WHERE origin = 'Hanoi' AND destination = 'New Delhi' 
ORDER BY price_inr ASC 
LIMIT 5;
```

**❌ [llama3.2:3b](https://ollama.com/library/llama3.2:3b)**
```
SELECT date, origin, destination, price_inr, flightType 
FROM flights 
WHERE price_inr = ( SELECT MIN(price_inr) FROM flights );
```

**✅ [phi4:14b](https://ollama.com/library/phi4:14b)**
```
SELECT date, origin, destination, price_inr, flightType 
FROM flights 
WHERE destination = 'New Delhi' AND origin = 'Hanoi' 
ORDER BY price_inr ASC 
LIMIT 5;
```

It can be observed that Qwen's 3B coder model is able to generate SQL queries as precisely as Phi's 14B model because it is trained specifically for coding. Hence, you don't need to opt for larger models here. However, it can also be seen that the Qwen2.5:3B model failed to achieve this because it is a general model. Building a good AI agent is all about testing and iterating from the smallest to the largest parameter sizes, along with searching for models that are specifically trained for your domain problem.

Does  **qwen2.5-coder:3b** is the LLM I have finalised? for now, yes. Currently I am still developing the AI agent and as long as the LLM fulfills the criteria. But if there's any time in future I can find a better model (smaller parametter & better accuracy), there's no doubt I'll swap it with new one.

# Configurable AI agent

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