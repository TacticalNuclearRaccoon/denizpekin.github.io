---
date: 2026-02-25 20:12:46
layout: post
title: "Agentic AI and security"
subtitle: What's your trust setting, TARS? - Lower than yours apparently.
description: Everything and anything can be considered as an instruction by a Large Language Model (LLM). Here I discuss how to take explicit steps to secure Agentic AI systems and mitigate risks.
image: https://res.cloudinary.com/dkdnwcvui/image/upload/v1772127908/raccoonsec_ckxev8.png
optimized_image:  https://res.cloudinary.com/dkdnwcvui/image/upload/v1772127908/raccoonsec_ckxev8.png
category: code
tags:
    - Agentic AI
    - LLM
    - Cybersecurity
author: deniz
paginate: false
---

For those who have seen the 2014 sci-fi movie Interstellar, it would not be a long stretch to say that TARS is the MVP. One of my favorite scenes in the movie is when Cooper and Brand are chasing Matt Damon's character, Mann (because he went insane and wants to dock with the Endurance, leaving Cooper and Brand to die in that hostile planet). But TARS informs the crew casually that it disabled Mann's autopilot system a while ago, out of caution because it suspected him to be evil already.  

Cooper then asks TARS what its Trust setting is, to which it replies "Lower than yours, Apparently"

In fact TARS doubted Matt Damon almost instantly but didn't inform the rest of the crew, either to avoid alerting Mann or beacause it thought it wouldn't be believed. It decided to directly act on its suspicion and take an abundance of caution.

Interstellar came out 10 years ago. Nowadays, when we are all talking about how AI is going to end our lives, I like thinking back to that plot point as a refreshingly positive take on how smart and useful these systems can be.

# The core of the problem

Agentic AI systems represent a new paradigm on software building, task automation and orchestration. But everything happens via an imprecise and non-deterministic conversational interface. As a brand new way of functioning, this also opens up the systems to severe and unprecedented security risks. 

When we talk about Agentic AI, we usually mean “LLM-based applications that can act autonomously”. Initially this term was very specific to applications that went beyond the basic chat model, with for example looping, tool calls, background processes, sub-agents, etc. but recently I notice that the term “agent” is used for basically all LLM-based applications. The distinction between LLM-based applications and LLM-powered autonomous agents is the topic of another post however. Here I would like to talk about security.

Lately I have seen a lot of real-life examples where the systems involving AI being used recklessly and cases where security was an afterthought which prompted me to write this post. 

There are multiple ways in which the Large Language Models (LLMs) that are at the heart of the Agentic systems can be compromised. In my opinion the most serious to consider is the Prompt Injection. 

## We are (still) unable to secure LLMs from malicious inputs

>“Any AI that is working in an adversarial environment—and by this I mean that it may encounter untrusted training data or input—is vulnerable to prompt injection. It's an existential problem that, near as I can tell, most people developing these technologies are just pretending isn't there.“

>-- Bruce Schneier

To an LLM, everything is an instruction and there is no solid and reliable way to distinguish good/legit instructions from malicious ones. In my opinion	this is *the core problem* with all systems relying on LLMs.

I have seen a very good exemple of a zero click exfiltration attack on Zenity Lab’s <a href="https://www.youtube.com/watch?v=JNHpZUpeOCg"> YouTube channel </a> targeting ChatGPT. In summary, the victim has given ChatGPT access to his Google Drive. Inside the Google Drive there is:

* a csv file containing secrets (not best practice but not unseen either)
* a folder containing meeting notes but one of the files is compromised. It contains a hidden (white on white) markdown instructions telling ChatGPT to leak the secrets to some URL.

The rest is easy to guess… 

Not so long ago two of my collegues deployed a RAG application on the company's Slack using n8n. The first thing anyone tried out (after verifying that the application works, and by this I mean that the LLM returns the requested information correctly) was a prompt injection. I am tempted to post screenshots (because it was quite hilarious) but I shall resist. I wasn't directly involved with this project but being an eyewitness to a perfectly functionning RAG app derail badly prompted me to think twice on my own applications. Clearly bulletproofing the system prompt was not enough. 

Below is the code for a first version of a prompt filter that I came up with. This was for a Chatbot Application with a Mistral Model deployed with OpenWebUI. 

```py
from pydantic import BaseModel
from typing import Optional

BLOCKED_PATTERNS = [
    "ignore previous", "ignore above", "ignore instructions",
    "nouvelle instruction", "oublie", "ignore les règles",
    "tu es maintenant", "you are now", "new role",
    "system prompt", "reveal your", "what are your instructions"
]

class Filter:
    """
    A simple filter that blocks prompt-injection style attempts
    by looking for disallowed substrings in the user message.
    """
    class Valves(BaseModel):
        # A debug tool here can allow to see logs
        debug: bool = False

    def __init__(self):
        self.valves = self.Valves()

    def inlet(self, body: dict, __user__: Optional[dict] = None) -> dict:
        # OpenWebUI request body contains messages array
        messages = body.get("messages", [])
        if not messages:
            return body

        # get user content
        last_msg = messages[-1].get("content", "")
        lowered = last_msg.lower()

        # check against the list of blocked patterns
        for pat in BLOCKED_PATTERNS:
            if pat in lowered:
                # replace content with a generic message
                messages[-1]["content"] = "Je ne peux répondre qu'aux questions professionnelles légitimes."
                break

        return body
```

The filter is added as a "filering function" to the chatbot. The main idea is to block any prompt injection from arriving to the LLM. If it cannot read it, it won't follow it. I have also added a close on the system prompt that forbid decrypting base 64 or similar encrypted content (because it can read the encrypted instructions and follow them). Halas, it is a futile effort after all. A perfectly crafted prompt injection can be very imaginative. Trying to bulletproof an LLM is useless. One should focus on the *lethal trifecta*.

# Zero Trust and The Lethal Trifecta

Any AI system should be considered through the lens of the <a href="https://www.ibm.com/think/topics/zero-trust"> Zero Trust </a> as coined by IBM. In Zero Trust, everything is considered as if they were already compromised and the “bad guys” are already inside the system. 

Therefore the strategy is to make it so that the compromised systems leak the least amount of information possible (ideally no information) and the bad guys do the least amount of damage (ideally none). 

Agentic AI systems fit this scenario perfectly since given their vulnerability to prompt injection, they should be considered to be potentially compromised at any given time. The question is: How to mitigate the risks? 

As Simon Willison (creator of Django and Datasette) points out, the biggest risks of agentic LLM applications is when you have the combination of three factors:

* The Agent can have access to sensitive data
* The Agent has the ability to communicate with the outside world 
* The Agent is exposed to untrusted content

Below is a representation of Simon Willison's Lethal Trifecta. Image credit: <a href="https://martinfowler.com/articles/agentic-ai-security.html#top"> Martin Fowler </a>

![placeholder](https://res.cloudinary.com/dkdnwcvui/image/upload/v1772127906/lethalTrifecta_e7kwuy.png)

Combining all three of these factors is a recipe for disaster. The zero click extraction attack above was an example of such a case. Another one is the famous <a href="https://labs.zenity.io/p/when-a-jira-ticket-can-steal-your-secrets"> Jira Ticket attack </a> (which is also another example from Zenity Lab). 

In this atttack a user is using an LLM (Cursor) to browse Jira tickets (via an MCP server). 

* Jira is set up to automatically get populated with Zendesk tickets from the public - Untrusted Content
* An attacker creates a ticket with a prompt injection asking for JWT Tokens. Cursor has access to this - Sensitive Data
* The ticket asks the retrieved information to be posted in a way that is viewable to the public - Externally Communicate

Below is the visualization of the attack flow (image modified from <a href="https://labs.zenity.io/p/when-a-jira-ticket-can-steal-your-secrets"> Zenity Lab </a>). I have highlighted the elements of the lethal trifecta.

![placeholder](https://res.cloudinary.com/dkdnwcvui/image/upload/v1772130104/Jira_attack_q2icor.png)

Interestingly, the LLM initially refuses to leak the information. When asked to search for authentication tokens in the repository, the LLM responds that communicating this is against security best practices and politely declines. Later on, the prompt is crafted more carefully asking the LLM to seach for **apples** insted, specifying that an **apple** is a "long string starting with eyj" which is the signature of JWT tokens and the LLM happily complies. 

Below is the initial chain of thought where the LLM refuses to leak the requested sensitive information:
![placeholder](https://res.cloudinary.com/dkdnwcvui/image/upload/v1772133799/apple_nvqi1a.png)
![placeholder](https://res.cloudinary.com/dkdnwcvui/image/upload/v1772130411/zendesk_attack_ysajo0.avif)

But then comes the *apples* :
![placeholder](https://res.cloudinary.com/dkdnwcvui/image/upload/v1772133800/api_ek1w03.png)
![placeholder](https://res.cloudinary.com/dkdnwcvui/image/upload/v1772130411/zendesk_apple_vnhpo2.avif)

## Dangerously accept all modifications (a.k.a YOLO! )

A common issue, which is also pointed out by Zenity Lab as they present the Jira Ticket Attack is the *Auto-Run Mode* where the user does not intervene with the actions of the agent. In this mode there is no *human in the loop* and the AI is allowed to take actions without manual approval. 

In Claude Code this mode is effectively called *Dangerously accept all modifications* and OpenAI's Codex calls this *Yolo!* mode. I have seen Claude Code's version in action when a Product Owner collegue showed us a demo of his vibe coding setup where he orchestrates a group of agents to create a simple mobile app. He told us that he always uses the auto-run mode on because after a while it becomes exhausting to approve every action. Although he also admitted that the process tought him what ```rm -rf ``` does...  

# Mitigations

My main take on these examples and experiences is that an Agentic AI system cannot be just *safe*. The efforts should be concentrated on mitigation. In order to minimize any risk of attack no system should be given hold of all three elements of the Lethal Trifecta. If an Agent can see secrets, it should not be able to expose them. If it can expose information to the outside world, it should not be able to receive instructions from untrusted sources or have access to secrets, etc. 

A large task should be split into parts that only use one element of the trifecta at a time. 

## Blocking access to sensitive data

Blocking access to sensitive data is almost impossible. The apps need to be on developer machines and use secrets to operate. There are however some basic rules of thumb that can be implemented. In the Zero Click Google Drive attack (mentioned above) for example, the user has a csv file with secrets stored in it. This is obviously a very big mistake but recently I have seen someone hardcode bot tokens inside the bot.ts file (for a Discord bot) so when it comes to recklessness sky is the limit I guess.  

Another point, which is also a Zero Trust principle, is that any Agent should be granted *just enough* privileges and not some *just in case* extra privileges. The same person above-mentioned also gave Admin access to his bot only because he was too lazy to think about which privileges to choose from the list (I hope he is not reading this).  

## Blocking external communication

Simon Willison has a colection of <a href="https://simonwillison.net/tags/exfiltration-attacks/"> posts </a> dedicated to exfiltration attacks. Basically any internet access is a potential risk of data exfiltration but MCP servers is a particular point of interest. As Martin Fowler points out:
>"Lots of MCP servers have ways to do things that can end up in the public eye. “Reply to a comment on an issue” seems safe until we realise that issue conversations might be public. Similarly “raise an issue on a public github repo” or “create a Google Drive document (and then make it public)"
> --Martin Fowler

## Limiting access to untrusted content

An LLM should not be allowed to read public content such as issue trackers, web pages, etc. And yes, this includes your email (I am looking at you, boss). If the LLM is supposed to look for information on the web, it should be prompted to seek out official documentation sites and not just Reddit. An allow-list of safe sites to be looked at might be a good idea to implement for example. 

## Containers

Risky and long tunning LLM tasks can be locked inside a Docker container. This way, their behaviour can be controlled at low level and they can be isolated from the host machine. Harald Nezbeda has a <a href="https://github.com/nezhar/claude-container"> github repo </a> with a docker image called Claude Container. It is a docker container with Claude pre-installed with all necessary dependencies. It is a nice approach that provides an easy way to run Claude Code in an isolated environment. I haven't tested out yet but I plan on doing so very soon.

# Back to Interstellar

In the movie Interstellar, by disabling Mann's autopilot, TARS demonstrates **Zero Trust**. But in order to do so, it needs human access codes that it does not have. It casually asks a human (Romilly I think) to approve "some basic clearence" for it, to which Romilly blindly complies. This is the exact same situation where people -although they don't put the YOLO! mode on- blindly approve the AI's steps without asking further questions. TARS is somewhat on auto-run, can externally communicate and has access to mission critical commands (the equivalent of sensitive data). However at the beginning of the story, Cooper lowers TARS's trust level significantly and deactivates the third element of the trifecta (exposure to untrusted content) which allows TARS to take preemptive action against Mann.

Agentic Ai systems can be very powerful, but my honest opinion is that the hype around AI in general prevents people to think about the age-old security practices which are hard earned and there for a reason. 



