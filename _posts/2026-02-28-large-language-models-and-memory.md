---
date: 2026-02-28 15:00:17
layout: post
title: "Large language models and memory"
subtitle: The North remembers...
description: Giving LLM-powered chatbots a persistent memory is not straightforward. In this post I want to share my experience on the matter, talk about what I have discovered and the pitfalls that I have been into. 
image: https://res.cloudinary.com/dkdnwcvui/image/upload/v1772365521/Screenshot_2026-03-01_at_12.44.56_dltskd.png
optimized_image: https://res.cloudinary.com/dkdnwcvui/image/upload/v1772365521/Screenshot_2026-03-01_at_12.44.56_dltskd.png
category: code
tags:
    - chatbot applications
    - memory
    - LLM
    - Artificial intelligence
author: deniz
paginate: false
---

>Shelby [*running*]: "OK, so what am I doing? Oh, I'm chasing this guy." 
>[*Dodd shoots at Shelby*] 
>"No... he's chasing me!"

There is a 2000 thriller movie called "Memento", but it could objectively be called "backwards movie" (if you have seen it, you know what I mean). As the result of a brutal attack, the protagonist Leonard Shelby ends up with a type of amnesia which prevents him from storing any further memories. But he wants revenge from the attackers so he is after them. In order to keep track of his progress, he uses notes, polaroid photos or even tatoos.

When I work with LLMs, sometimes I am reminded of Shelby. Each session starts fresh with a clean context window. The model does not *remember* anything about the previous conversations and sometimes it even forgets the beginning of the conversation.

# The core problem: LLMs are stateless 

Large Language Models process each conversation as a standalone process. They calculate the most probable word to follow the conversation given all the words that have been said previously. And they are astonishingly good at doing that. Basically they operate by building up a large text document and calculating what is the most likely next word to complete the document. The illusion is there, the user feels like it is having a real conversation but under the hood it is just a series of steps that grow a text document.

Below is a simplified schematic representation of how LLMs operate:
![placeholder](https://res.cloudinary.com/dkdnwcvui/image/upload/v1772806068/promptChain_uevqud.png)

Once the session ends, the big text that the user has been growing with the model disappears. A new conversation starts as “tabula rasa” and any information that has been shared with the model evaporates unless something is done to preserve it. 

However, an agent needs to be stateful in order to maintain contextual understanding across sessions. Even for chat applications; for a customer service bot, remembering the beginning of the conversation is mission critical.  So what are the solutions? 

# When we talk about AI, what does memory even mean?

I have first started digging into the issue a couple of years ago, when one graceful morning my boss told me “I waste so much time reminding ChatGPT what I have already told it before! It is so annoying! I think this is a real pain point in these applications and something should be done about it!” Of course by that he meant “Do something about it”. 

The epistemology around AI memory borrows heavily from neuroscience. 

There is **short-term memory**, which refers to the context window. This is the maximum number of tokens the model can process at once. This short term memory tracks immediate conversation state. The size of the context window depends on the model. By the time I am writing this article (which is 6 years after the great pandemic and a couple of months before Word War III) GPT4 has a context window of 60k tokens, Claude  Sonnet 3.5 has 200k and Gemini has 1M. In order to have an idea of a scale we can say that Claude can hold a novel’s worth of words in its short term memory while Gemini can hold around 7 novels…

The **long-term memory** on the other hand stores accumulated knowledge and agent experiences. 
Types of long-term memory as presented by <a href="https://docs.langchain.com/oss/python/concepts/memory">Langchain documentation</a>:
<table> <thead> 
<tr> <th>Memory Type</th> <th>What is Stored</th> <th>Human Example</th> <th>Agent Example</th> </tr> 
</thead>
<tbody>
<tr>
<td>Semantic</td><td>Facts</td><td>Things I learned in school</td><td>Facts about a user</td>
</tr>
<tr>
<td>Episodic</td><td>Experiences</td><td>Things I did</td><td>Past agent actions</td>
</tr>
<tr>
<td>Procedural</td><td>Instructions</td><td>Instincts or motor skills</td><td>Agent system prompt</td>
</tr>
</tbody>
</table>

The protagonist of the movie Memento is like a stateless agent. He only has a context window (a short term memory) and his polaroid photos, notes, and tatoos serve as long term memory. When we talk about an LLM application with semantic memory, we want to implement the equivalent of the polaroid photo of John Edward "Teddy" Gammell with the annotation: *Teddy should not be trusted* for the LLM model.

## Semantic memory management
The essential facts that ground the model’s responses are stored as semantic memory. Two common representations are collections (to record an unbounded amount of knowledge) and profiles (to record task-specific information following a strict schema).	
As we mentioned above, every LLM operates with a context window of a certain size that limits how many tokens it can process at once. When your chat history exceeds this limit you have a (tough) choice: you can either truncate important context (risking even context collapse) or pay a handsome amount of extra money for processing massive amounts of (redundant) information. 
The point in semantic memory management is to have the model “remember” key takeaways because all information in a conversation is not equally important. 

# Letta: OS-inspired memory

When I started researching how to manage long-term memory for LLM based applications, my first finding was the <a href="https://arxiv.org/abs/2310.08560">MemGPT</a> paper from <a href="letta.com">Letta</a>. As far as I understand, Letta was the first to come up with a solution for the LLMs to manage their own memory through function calling, which allows the model to decide what to keep in context versus external storage. 

The architecture is based on hierarchical storage tiers:

* **Main Context (the equivalent of RAM in an OS):** System instructions, working context, First in first out (FIFO) message queue
* **External Context (equivalent of harddisk):** Recall storage and archival storage for unlimited data
* **Memory Management:** OS-like paging between context tiers via function calls

There is a great free foundations course on <a href="https://www.deeplearning.ai/short-courses/llms-as-operating-systems-agent-memory/">deeplearning.ai</a> by the founders of Letta (Sarah Wooders and Charles Packer) where they explain how to buid Letta agents. 

Letta have said that their solution is model agnostic but they have also specified that it works best with OpenAI models. However, I want to show here how to play with it locally with Ollama if you want to make quick tests with it. For this, you will need to have ollama already installed. Also, you need to pull one LLM as well as one embedding model (below is the ones I used for this example):

![placeholder](https://res.cloudinary.com/dkdnwcvui/image/upload/v1772822730/Screenshot_2026-03-06_at_16.20.53_ts0wis.png)

After creating a virtual environment, you install the following dependencies:
```console
pip install -U letta
pip install llama_index ollama
pip install llama-index-embeddings ollama
```

Then you set where to find ollama as enviroment variable, in order to access the models:
```console
export OLLAMA_BASE_URL=http://localhost:11434
```

And finally you can start the letta server:
```console
letta server
```

The main difference when using Letta is that unlike other agent frameworks it runs on its own service so you connect to a running Letta server (on localhost:8283) and it is via that server that you create agents and interact with them. When you navigate to http://localhost8283 on you browser you will see the user interface where you can create agents, chat with them, etc. 

![placeholder](https://res.cloudinary.com/dkdnwcvui/image/upload/v1772822729/letta_ba5tz6.png)

You have some example personas for LLMs and the *memories* from your conversations are saved locally using the embedding model. This external storage of memories allows to organize memories in searchable structures and retrieve relevant context dynamically. The knowledge is updated as new information arises. 

# Mem0: Lightweight, scalable memory

Another solution that I have found is Mem0 which allows an adaptative memory for LLM applications. 

Below is the extraction architecture from the <a href="https://docs.mem0.ai/core-concepts/memory-operations/add">mem0 documentation page</a>:
![placeholder](https://res.cloudinary.com/dkdnwcvui/image/upload/v1772824903/add_architecture_pp7uba.avif)
Under the hood, Mem0 uses an extraction LLM. This is prompted specifically to extract relevant information from the incoming messages. This LLM is different from the agent, it is the memory portion of the system. The extracted new memories are added to a vector database as a RAG setup (a knowledge database) for each user. But more than a regular RAG, the system is able to perform conflict resolution: existing information is not added twice, contradictions are resolved (the latest truth wins), etc.

You can also use a graph database (Mem0g) in order to store relational information. 

The fetching architecture is represented below:
![placeholder](https://res.cloudinary.com/dkdnwcvui/image/upload/v1772825339/search_architecture_x3ap1m.avif)

For searching memories, the query is executed before the agent answers the user so that the agent can fetch relevant memories required for the conversation. But only the information that is required for the given query is fetched (avoiding useless charges).

Here is a simple test of Mem0 with a local model. I have followed <a href="https://www.youtube.com/watch?v=m4ZnZXlOOYM&t=246s">this example</a> from Beau Carnes with the Spanish tutor. I modified the code slightly in order to have it run with a local Ollama model and since I wasn't very creative I transposed it to an exercice coach. 
```python
import json
import os
import shutil
import sys
from datetime import datetime

from dotenv import load_dotenv
from mem0 import Memory

load_dotenv()

BASE_DIR = os.path.dirname(os.path.abspath(__file__))
QDRANT_PATH = os.path.join(BASE_DIR, ".qdrant_data")
COLLECTION_NAME = "exercice_coach_session"

OLLAMA_BASE_URL = os.getenv("OLLAMA_BASE_URL", "http://localhost:11434")
OLLAMA_CHAT_MODEL = os.getenv("OLLAMA_CHAT_MODEL", "llama3:latest")
OLLAMA_EMBED_MODEL = os.getenv("OLLAMA_EMBED_MODEL", "nomic-embed-text")
OLLAMA_EMBED_DIMS = os.getenv("OLLAMA_EMBED_DIMS")

USER_ID = "deni_student_01"

def create_ollama_client():
    try:
        from ollama import Client
    except ImportError:
        print("⚠️  The Python 'ollama' package is not installed.")
        print("Install it with: pip install ollama")
        sys.exit(1)

    return Client(host=OLLAMA_BASE_URL)


def detect_embedding_dims(client):
    if OLLAMA_EMBED_DIMS:
        return int(OLLAMA_EMBED_DIMS)

    response = client.embeddings(model=OLLAMA_EMBED_MODEL, prompt="dimension probe")
    return len(response["embedding"])


def build_config(embed_dims):
    # We use an explicit collection name and path to ensure persistence.
    # In a real product, naming your collection prevents data leaks between agents.
    return {
        "llm": {
            "provider": "ollama",
            "config": {
                "model": OLLAMA_CHAT_MODEL,
                "ollama_base_url": OLLAMA_BASE_URL,
                "temperature": 0.1,
            },
        },
        "embedder": {
            "provider": "ollama",
            "config": {
                "model": OLLAMA_EMBED_MODEL,
                "ollama_base_url": OLLAMA_BASE_URL,
                "embedding_dims": embed_dims,
            },
        },
        "vector_store": {
            "provider": "qdrant",
            "config": {
                "path": QDRANT_PATH,
                "collection_name": COLLECTION_NAME,
                "on_disk": True,
                "embedding_model_dims": embed_dims,
            },
        },
    }


def reset_qdrant_if_dimension_mismatch(expected_dims):
    meta_path = os.path.join(QDRANT_PATH, "meta.json")
    if not os.path.exists(meta_path):
        return

    try:
        with open(meta_path, "r", encoding="utf-8") as file:
            meta = json.load(file)
        stored_dims = (
            meta.get("collections", {})
            .get(COLLECTION_NAME, {})
            .get("vectors", {})
            .get("size")
        )
    except (OSError, json.JSONDecodeError):
        stored_dims = None

    if stored_dims is None or stored_dims == expected_dims:
        return

    backup_path = f"{QDRANT_PATH}.backup_{datetime.now().strftime('%Y%m%d_%H%M%S')}"
    shutil.move(QDRANT_PATH, backup_path)
    print(
        "\n⚠️  Reset local memory store because the embedding size changed "
        f"from {stored_dims} to {expected_dims}.\n"
        f"    Previous data was backed up to: {backup_path}\n"
    )

def handle_chat():
    memory = None
    memory_map = {}
    client = create_ollama_client()
    
    print("\n" + "="*45)
    print("PERSONALIZED AI EXERCISE COACH")
    print(f"Status: Memory Persistent | Storage: Local Disk | Model: {OLLAMA_CHAT_MODEL}")
    print("Commands: /memories, /forget [num], exit")
    print("="*45 + "\n")

    try:
        embed_dims = detect_embedding_dims(client)
        reset_qdrant_if_dimension_mismatch(embed_dims)
        memory = Memory.from_config(build_config(embed_dims))

        while True:
            user_input = input("You: ").strip()

            if not user_input: continue
            if user_input.lower() in ['exit', 'quit']: break

            # MEMORY OPERATION: LISTING (get_all)
            if user_input == '/memories':
                res = memory.get_all(user_id=USER_ID)
                all_memories = res.get('results', []) if isinstance(res, dict) else res
                
                print("\n🧠 KNOWLEDGE BASE:")
                if not all_memories:
                    print("(Empty)")
                else:
                    memory_map = {} 
                    for i, m in enumerate(all_memories, 1):
                        mem_text = m['memory'] if isinstance(m, dict) else m
                        mem_id = m.get('id') if isinstance(m, dict) else None
                        memory_map[str(i)] = mem_id
                        print(f"{i}. {mem_text}")
                print("-" * 25 + "\n")
                continue

            # MEMORY OPERATION: DELETING (delete)
            if user_input.startswith('/forget '):
                try:
                    num = user_input.split(' ')[1]
                    full_id = memory_map.get(num)
                    if full_id:
                        memory.delete(full_id)
                        print(f"\n🗑️  Pruned memory {num}.\n")
                    else:
                        print("\n⚠️  Invalid index.\n")
                except (IndexError, ValueError):
                    print("\n⚠️  Use: /forget [number]\n")
                continue

            try:
                # MEMORY OPERATION: SEARCHING (search)
                # We pull facts relevant to the CURRENT question only.
                # This keeps the context window clean and cheap.
                search_results = memory.search(user_input, user_id=USER_ID)
                actual_results = search_results.get('results', []) if isinstance(search_results, dict) else search_results
                context = "\n".join([m['memory'] if isinstance(m, dict) else m for m in actual_results])
                
                response = client.chat(
                    model=OLLAMA_CHAT_MODEL,
                    messages=[
                        {
                            "role": "system", 
                            "content": f"You are an exercise coach. Personalize based on this context:\n{context}"
                        },
                        {"role": "user", "content": user_input}
                    ]
                )

                reply = response["message"]["content"]
                print(f"\nAI: {reply}\n")

                # MEMORY OPERATION: ADDING (add)
                # Mem0 automatically extracts the core "facts" from the conversation.
                memory.add(user_input, user_id=USER_ID)
                
            except Exception as e:
                print(f"⚠️  Error: {e}")

    finally:
        # Professional cleanup to prevent Qdrant race conditions
        if memory is not None:
            del memory
        os._exit(0)

if __name__ == "__main__":
    handle_chat()
```

At the first startup a local vector database (qdrant_data) is instanciated to store memories. Below is an example of conversation where I ask an exercice related question (and of course I made a typo...):
![placeholder](https://res.cloudinary.com/dkdnwcvui/image/upload/v1772829801/Screenshot_2026-03-06_at_21.41.52_o2qphg.png)
Then I give some follow-up information:
![placeholder](https://res.cloudinary.com/dkdnwcvui/image/upload/v1772829801/Screenshot_2026-03-06_at_21.42.19_ynmbqm.png)

The model responds according to the coach persona. On a side note, I cannot help but note the sychophant tone of llama3. I have written a whole article about the subject previously (see: **Brutally Honest AI**). If what I describe in my example actually happens to you while exercising, you should see a doctor (and not talk about it with an LLM) but I digress... 

Then I quit the session and restart a new one. Normally the short term memory is erased, therefore the model should not be able to remember the information form the previous session. But the `/memories` returns the information recorded by mem0 and the model can use it to formulate an answer. 
![placeholder](https://res.cloudinary.com/dkdnwcvui/image/upload/v1772830144/Screenshot_2026-03-06_at_21.48.40_qeg4jf.png)  
The answer is still somewhat vague but llama3 is not a huge model either (4GB) so I guess we should cut it some slack.

There are other notable platforms like <a href="https://www.getzep.com/">Zep</a> (temporal/episodic knowledge graphs), <a href="https://github.com/langchain-ai/langmem">LangMem</a> (LangGraph-native, semantic/episodic/procedural) that I haven't tried yet. 

# Forgetting is just as important as remembering

Humans don't erase information (unless some pathologies are involved). When we *forget* something, the synaptic connections forming the information still remain, but their operation somehow becomes difficult (with time, etc.). Let's say two people, Alice and Bob are having a conversation (totally *random* names unrelated to information theory):

* Alice says: "Steve and I have started dating"
* Bob saves the following information:
```json
Alice has a boyfriend
Alice's boyfriend's name is Steve
```
* Later on Alice tells him: "Steve and I broke up."
* Bob saves the following information:
```json
Alice has a boyfriend - no longer true, Alice is single.
Alice's ex boyfriend's name is Steve
```

However, this is not really practical for LLM applications. Beyond remembering useful information, the LLM application should also be able to forget irrelevent or "no longer relevent" content. This prevents an agent for example to make decision on outdated context, also called *memory bloat*. On the code example above, there is a very simple implementation of this with the `/forget` command. 

Below an example usage for the `/forget` command where I delete a specific information from the model's knowledge base:

![palceholder](https://res.cloudinary.com/dkdnwcvui/image/upload/v1772831536/Screenshot_2026-03-06_at_22.12.04_airjpt.png)

Of course this is a very basic example where the specific memory is erased by hand. In a production level LLM application **Contextual Forgetting** is used. This refers to the process of selectively removing or reducing the importance of memory objects based on pre-defined importance factors such as how recent they are, their relevance, etc. In order to do this techniques such as time-weighted scoring, user feedback signals or decay-aware attention mechanisms are used (<a href="https://ajithp.com/2025/06/30/ai-native-memory-persistent-agents-second-me/">source</a>). 

For example, temporary contexts like calendar invites or tactical meeting notes should fade, while decision history and user preferences should remain. Forgetting helps ensure the system stays adaptive, efficient, and aligned with current tasks.

# Security and privacy concerns

Providing memory to stateful LLM applications introduces several risk factors. 

Here are some considerations:

* **Data memorization & leakage:** LLM data memorization is a process through which the model repeatedly returns to certain encoded data until it becomes baked into the system, yet it's incredibly hard to know which information has been memorized, and even harder to erase it if needed. (<a href="https://news.northeastern.edu/2025/11/21/five-ways-llms-expose-your-personal-data/">source</a>)
* **Memory poisoning attacks:** Researchers have demonstrated prompt injection attacks that can extract user queries stored in an agent's memory module, revealing a serious privacy attack surface in memory-enabled agents. (<a href="https://aclanthology.org/2025.acl-long.1227.pdf">source</a>)
* **GDPR tension:** The General Data Protection Regulation presents unique challenges — core principles like data minimization, purpose limitation, and the right to erasure often conflict with how LLMs operate. Unlike traditional databases, information embedded in model weights cannot be easily removed. (<a href="https://www.lasso.security/blog/llm-data-privacy">source</a>)

The radar chart below (by <a href="https://i0.wp.com/ajithp.com/wp-content/uploads/2025/06/image-13.png?ssl=1">Ajith Vallath Prabhakar</a>) provides a visual representation of the impact of memory storage:
![placeholder](https://res.cloudinary.com/dkdnwcvui/image/upload/v1772832461/image-13_zvnjfz.webp)

Mitigations include <a href="https://research.google/blog/fine-tuning-llms-with-user-level-differential-privacy/#:~:text=Differential%20privacy%20(DP)%20allows%20us,%2C%20example%2Dlevel%20DP).">differential privacy</a> during training, token-level <a href="https://strandsagents.com/latest/documentation/docs/user-guide/safety-security/pii-redaction/">Personally Identifiable Information (PII) redaction</a> before prompts are processed, behavioral anomaly detection, and strict audit logs.

# References 

https://mem0.ai/blog/llm-chat-history-summarization-guide-2025

https://docs.langchain.com/oss/python/concepts/memory

https://medium.com/@harikrishnabekkam1590852/the-future-of-ai-agents-how-external-memory-mem0-and-memgpt-are-transforming-long-term-context-23f4ec88f66d

https://www.deeplearning.ai/short-courses/llms-as-operating-systems-agent-memory/

https://www.emergentmind.com/topics/persistent-memory-for-llm-agents

https://www.lasso.security/blog/llm-data-privacy