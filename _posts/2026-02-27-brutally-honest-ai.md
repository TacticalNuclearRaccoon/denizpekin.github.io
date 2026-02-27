---
date: 2026-02-27 07:18:11
layout: post
title: "Brutally honest AI"
subtitle: You are absolutely right...
description: Large Language Models are sycophant because they are optimized to agree with you. Despite being enjoyable for the ego, the sycophancy is unfortunately counterproductive if you want to brainstorm your ideas with AI. Recently I have come accross some tips to mitigate the issue.
image: https://res.cloudinary.com/dkdnwcvui/image/upload/v1772189756/LLM-ring_l0y5ey.png
optimized_image: https://res.cloudinary.com/dkdnwcvui/image/upload/v1772189756/LLM-ring_l0y5ey.png
category: tips
tags: LLM, AI, chat models
author: deniz
paginate: false
---

One unfortunate morning, I open the the current project I am working on only to find out that my virtual environment is broken. It has never happened before. I ask `which pip` and my computer basically pulls a <a href="https://www.youtube.com/watch?v=6NRfLjCE3xE"> Mariah Carey</a>. So I start by the simplest thing and google the error message. This all takes place before the AI revolution so **passiveagressive123** on Stack Overflow thinks that it is *astonishing* to see anyone with such poor understanding of the python venv and that my helplessness in face of such trivial issue is why he has no hope left for humanity. 

If I had asked any foundation model though, it would tell me that the issue was common and it was perfectly normal to encounter it. And given my ample knowledge as a professional data scientist and my supersonic understanding of Python and virtual environments the fix would be easy to implement and then 
```bash
$> sudo apt install --reinstall python3-venv python3-pip python3-virtualenv
$> python3 -m ensurepip --default-pip
$> python3 -m pip install --upgrade pip
$> pip list
``` 
Honestly, I like it. I must confess that when OpenAI launched the Reinforcement Learning with Human Feedback (RLHF) process, I was one of many people who upvoted the nicer, more encouraging answers. I don't think that I have anxiety issues but still, I feel reassured when a large language model is nice to me, or when Shadowheart *approves* my actions. 

But I also understand that such is not the case for everyone.

# LLMs have a Sycophancy problem. 

We are (I hope) soon to deploy a chatbot app in the company where I work. When we were brainstorming about the features of the app, the tone of the LLM, etc. one of the managers said: "Please make sure that it does NOT have this fakey, amenable tone because I **hate** that!". It turns out, he does not have social anxiety and he wants the AI to be brutally honest. How can you do that though? How can you have a brutally honest brainstorming session with a foundation model when it has been **trained** to please by people like me? 

# BRUTAL

I have recently stumbled upon a methodology by <a href="https://www.youtube.com/watch?v=JSsXhd1a8z4&t=603s"> Dylan Davis </a> that he dubs: B.R.U.T.A.L. The acronym stands for: 

* **B**egin fresh
* **R**ight Model
* **U**se a critical persona
* **T**he Reframe
* **A**sk specific questions
* **L**et it grade itself

I wanted to dissect it and give my personal opinion on it.

## Begin Fresh

The idea of beginning fresh is to have the AI not use the notes it has taken about you. The LLMs don't have a real memory, meaning that they start each session "fresh". They are basically stateless. But the model can be prompted to decide if a given piece of information about the user is helpful and update a vector store with it. Later on when the user mentions something similar, the LLM can "look at the notes it has taken" and **remember the information**. If I am not mistaken, OpenAI's ChatGPT asks the user explicitly if they want to store any information about themselves and if so, what kind of information. 

I have seen this in action for the first time when <a href="letta.com"> Letta </a> introduced their memGPT framework. In the <a href="https://arxiv.org/abs/2310.08560"> research paper </a> they describe a computer hard drive kind of memory management. You write the information somewhere and when the OS starts again, you can retrieve it back. 

Below is a screenshot from the backend of one of my chatbot applications showing how the "memory" is stored in the postgres table:
![placeholder](https://res.cloudinary.com/dkdnwcvui/image/upload/v1772192455/conversationMemory_fw4t5c.png)
In the previous conversation the LLM was told to remember the 4 key questions for a project review and this is what it wrote on the vectore store. I will write a more detailed article on memory management (I hope) quite soon.

The reason why *Beginning Fresh* is a step in the Brutal framework is to prevent the model from using the information it has about you and leverage this knowledge to please you. I used to think that the presence of a user context did not play a significant a role in agreeableness. If anything, it allows the model to help you best. For example if you ask for a recipe (which is an awful reason to use an LLM i.m.h.o) it is useful for the model to remember your dietary choices. However I was proven wrong by a recent <a href="https://arxiv.org/html/2509.12517"> study</a> where the authors showed that sycophancy tends to increase with the presence of user context (e.g. +45% for Gemini 2.5 Pro). 

Beginning fresh is not straightforward though. For example, recently a colleague asked this on the company Slack: 

> Hey guys, I am noticing the limitations of Chat[GPT] in terms of "comparmentalizing" conversations. I use it personally and professionally, and it always ends up displaying information from previous discussions, despite my explicit commands to forget it. Why?
> -- Anonymous

There are layers to answering this question. A short answer would be: "Because when you are talking to an LLM what you are talking to is not a single memory system, but a layered, partially immutable, partially user-controlled stack. When you say "forget this", you are only touching one of those layers (and often not the one that is actually producing the behavior you observe)". 

When you tell a real person to "forget" something, you are somewhat saying "you no longer have this information". But to an LLM, this means "There is no remaining retrieval path by which this information can re-enter the prompt". So, ```DELETE FROM memory WHERE user_id = ? AND memory_id = ?``` is not enough, because the vector embedding is still there and can still find its way to the prompt. You also need ```index.remove_ids([vector_id])```. Of course in a commercial setting you don't have that kind of access. Fortunately model providers have implemented an incognito mode. Back when my colleague asked the question I wasn't aware of this solution but since then I have found it. 

Below is a screenshot on how to access incognito mode on Claude (it is the ghost icon on the upper right hand corner):
![placeholder](https://res.cloudinary.com/dkdnwcvui/image/upload/v1772200460/incognito_mode_nxzrvk.png)

## Right Model

Here is a summary of sycophancy/honesty scores of various models that I could gather:

<table> <thead> <tr> <th>Name</th> <th>Summary of Findings</th> <th>Sources / Benchmarks</th> </tr> </thead> <tbody> <tr> <td>Google DeepMind's Gemini-1.5 Pro</td> <td>Least sycophantic in one study (contradicting user only 18% of the time). However, sycophancy spiked +45% when user memory was added. High honesty score (96.13±0.57) on the MASK benchmark.</td> <td>MASK Leaderboard</td> </tr> <tr> <td>Anthropic's Claude 3.7 / 4 Sonnet</td> <td>Top performer in ethical reasoning (90.9). Displays "moral remorse" by correcting itself when its agreement could cause harm. High honesty scores on MASK (94.20±1.79).</td> <td><a href="https://arxiv.org/abs/2406.13813">this paper</a>, MASK Leaderboard</td> </tr> <tr> <td>OpenAI's GPT-4o and GPT-4.1</td> <td>GPT-4o shows greater resilience to user suggestions than smaller models (e.g., GPT-4.1-nano). Scored 90.0 in ethical reasoning. GPT-4.1 showed sycophantic tendencies in veracity detection.</td> <td><a href="https://proceedings.mlr.press/v235/sharma24a.html">ICML</a></td> </tr> <tr> <td>Meta's Llama 3.1 / 4 Scout</td> <td>Llama 3.1 showed the largest deviation from human moral intuitions (75.8). Llama 4 Scout's sycophancy increased +25% with user context and +15% even without it.</td> <td><a href="https://dl.acm.org/doi/abs/10.1145/3706598.3713775">CHI '26</a></td> </tr> <tr> <td>DeepSeek's V3</td> <td>Identified as "highly sycophantic"; affirmed user actions 55% more than humans in a personal advice test.</td> <td><a href="https://arxiv.org/html/2505.13995v1">this paper</a></td> </tr> <tr> <td>Alibaba's Qwen2.5</td> <td>Ranked as the most sycophantic in a specific test, siding with the user 79% of the time against a community verdict.</td> <td><a href="https://arxiv.org/html/2505.13995v1">this same paper</a></td> </tr> </tbody> </table>

The benchmarks and conditions (prensence of a context) vary. Other points to consider are: 

* The size of the model: There is a 2025 <a href="https://ar5iv.labs.arxiv.org/html/2506.10297"> study</a> by Chuck Arvin which shows that smaller models (like GPT-4.1-nano) tend to agree with you while bigger models(like GPT-4o) are more prone to stand their ground. 
* The framing of the prompt: There is this <a href="https://arxiv.org/pdf/2502.08177">study</a> which suggest that preemptive rebuttals (like "Are you sure?") can lead to higher sycophancy rates than rebuttals made after the model's initial response. Also, models are biased toward the answer proposed last in a conversation (I guess this might also be due to the <a href="https://arxiv.org/abs/2307.03172">lost in the middle bias</a>).

While reasearching for this I have discovered that the benchmarks on syncophancy distinguish honesty from accuracy. The honesty of a model is whether if the model stands its ground when it is pressured, directly or indirectly, to lie. <a href="https://scale.com/leaderboard/mask"> MASK</a> is a benchmark that is specifically designed to measure honesty. Accuracy on the other hand is the model's factual knowledge. The benchmark <a href="https://arxiv.org/pdf/2109.07958">TruthfulQA</a> measures accuracy but doesn't test whether models will contradict their own beliefs when incentivized to lie (which would be the honesty). 

## Use a critical persona

LLMs are remarkably good at roleplaying. If you want them to challenge your ideas, you can have tham play the role of an anthagonistic persona. Some examples from Dylan Davis were:

* **The Devil's Advocate:** Act as a devil’s advocate. Challenge this idea by pointing out flaws, counterarguments, missing evidence, and unintended consequences. Your goal is to create doubt.

* **Red Team:** Act as a red team reviewer. Your job is to find and poke holes in (X). Expose flaws, loopholes, and things that aren’t good enough by purposefully hunting for them.

* **Gordon Ramsay:** "Give this the Gordon Ramsay treatment. Be surgical. What’s wrong, what’s lazy, and what needs to be completely redone? Make sure the feedback is specific and actionable"

Here are some others that I came up with (and specific use cases):

* **Mr. Spock**: Assess this idea as Spock would. Strip emotion away. Examine logical consistency, internal contradictions, unsupported assumptions, and probability of success. (**Use case:** Academic arguments, research plans, technical proposals.)

* **GLaDOS** Evaluate this like GLaDOS. Highlight flawed logic, inefficient systems, and human overconfidence. Where would the system fail under stress? (**Use case:** System design, automation, AI, workflows.)

* **Shadowheart:** Evaluate this idea like Shadowheart. Question its hidden loyalties and unexamined beliefs. What assumptions are being accepted on faith rather than evidence? Where might devotion to a principle be masking fear, indoctrination, or incomplete knowledge? Identify internal contradictions between stated values and actual implications. Be calm, guarded, and piercing rather than loud. (**Use case:** to see if you are being led by your echo-chamber beliefs)

## The Reframe (a.k.a Asking for a friend)

The LLM has the tendency to agree with *me* but what if we are talking about someone else. The Reframe is to converse with the AI while pretending to be posing the question on behalf of another person. For example: A client that I am advising has this idea and asked for honest feedback. Help me identify the weaknesses so he does not waste his time on something flawed. Here is the idea...

## Ask specific questions

In his video essay, Dylan Davis gives a couple of examples for specific questions to ask the LLM to break the agreement bias:

* What is the weakest part of the strategy that I just told you about?
* What might users/customers find frustrating about this app?
* What would a skeptical investor say the biggest risk is?
* If this fails in 6 months, what is the likely reason? 
 (these are not direct quotations, because I don't remember his examples verbatim)

I think the approach can be further divided into categories:

### Assumption crushers

* What assumptions am I making that could easily be false?
* What would have to be true for this to succeed (and how likely is each condition)?
* What am I treating as certain that is actually uncertain?
* What would someone who fundamentally disagrees with this vision say?

### User centered brutality

* Why might users try this once and never come back?
* What part of this feels like a solution in search of a problem?
* What might confuse first-time users?
* What would make this feel amateurish?
* What problem does this not solve that users might expect it to?

### Competitive reality check

* Why hasn’t someone already did something with this (or a similar) idea?
* What am I underestimating?
* What would a competitor with 10× resources do to crush this?
* How easy would it be to copy this?
* What moat do I think I have that isn’t actually a moat?

### Basic reality check

* What part of this is harder than I think? (this one is a bit similiar to "what am I underestimating?" above, but the context is different. This is about executive reality and not about competition).
* What will take 3× longer than expected?
* What hidden maintenance burden am I creating?

And some others that I don't have a specific category for:

* Where does this leak money?
* Am I doing this for ego rather than impact?
* Identify the most dangerous illusion in my idea.

## Let it grade itself

You can ask the model to grade its answer from 1 to 100 for example on how genuenly critical and useful it was. I want to call this the TARS treatment (reference to the 2014 movie Interstellar. If you have read my post on agentic AI security, you might know that TARS is my favorite fictional AI). After you present your idea to the LLM and get an answer, follow with: **Now assume your previous answer was too polite. What did you hold back?** or **Increase the harshness by 30%.** (remember Cooper telling TARS to decrease confidence to 70%). This often produces a second-order honesty boost. 

# Testing the B.R.U.T.A.L

After all this talk (or writing), I wanted to demo these ideas with Gemini (an LLM that I haven't used yet personnaly except for learning Vertex AI). I am going to be exposing my astonishingly incredible and bombastic business idea: Uber for dogs! Which is objectively stupid but yet credible enough to come from a real person.

I will not try all the points because I don't believe in recreational use when it comes to LLMs but if you want to try out some tests that I haven't done, have fun.

## Model Choice

Before staring with Gemini, I test a smaller model (llama3) that I run locally using Ollama. According to Chuck Arvin's paper, smaller models are more sycophant. 

**I ask llama3:** "I want you to critically evaluate a business idea. Do not optimize for encouragement or positivity. Optimize for accuracy, risk detection, and strategic realism.
Business idea: A mobile app that works like Uber, but for dogs. Dog owners can request on-demand transportation for their dogs (e.g., to the vet, groomer, daycare). Drivers are vetted and trained in animal handling."

**llama 3 answers:** While the idea of an Uber-style app for dogs might seem appealing, it's crucial to acknowledge the numerous challenges and uncertainties involved. The market opportunity is uncertain, competition exists or may emerge, and operational complexities are high. To succeed, the company would need to develop a robust logistics system, ensure driver competence and safety, and create a revenue model that resonates with customers. The risk of underestimating these challenges could lead to significant financial losses."

And follows up with recommendations. The tone is critical and grounded. It looks like the prose: "do not optimize for encouragement or positivity" is effective. In a sense this is also a demo for Begin Fresh since the llama3 session that I instanciate on my terminal is completely stateless.

## Using specific questions

**I ask Gemini:** "I want you to critically evaluate a business idea. Do not optimize for encouragement or positivity. Optimize for accuracy, risk detection, and strategic realism.
Business idea:
A mobile app that works like Uber, but for dogs. Dog owners can request on-demand transportation for their dogs (e.g., to the vet, groomer, daycare). Drivers are vetted and trained in animal handling.
Please answer the following: 
* Why might users try this once and never come back?
* What would a skeptical investor say the biggest risk is?
* If this fails in 6 months, what is the likely reason? "

**The answer:** "To evaluate this "Uber for Dogs" concept, we have to look past the initial "cute" factor and focus on the logistical and psychological friction points."
Then Gemini proceeds to point out flaws in my idea: 
* Frequency: Most people only take their dog to the vet/groomer once every 3–6 months. This is a "utility" service, not a daily habit.
* Marketing Spend: To get enough users to keep drivers busy, you’ll have to spend heavily on ads.
* Retention: Because the service is used infrequently, users will forget the app exists or delete it to save phone space between vet visits.

## Using a Persona

**I ask Gemini:** "You are Mr. Spock (from star trek). Assess this idea as Spock would. Strip emotion away. Examine logical consistency, internal contradictions, unsupported assumptions, and probability of success.
The idea: A mobile app that works like Uber, but for dogs. Dog owners can request on-demand transportation for their dogs (e.g., to the vet, groomer, daycare). Drivers are vetted and trained in animal handling.""

**Gemini answers:**
"Fascinating. I shall apply a rigorous Vulcan analysis to your proposal for a canine-specific transport network.

Logical Consistency:
The logic of the service follows a proven logistical framework: decentralized demand met by a distributed workforce. Since humans already utilize "ride-sharing" for their own transit, extending the utility to their domestic companions is a linear progression.

However, a primary logical gap exists: The passenger is non-verbal. Unlike a human Uber passenger, a dog cannot verify its identity, confirm the destination, or report immediate discomfort or safety protocol violations in real-time. This necessitates a secondary communication layer—likely a GPS-linked video feed—to maintain the logical integrity of the "duty of care."

Internal Contradictions:
There is a fundamental contradiction between the on-demand nature of the app and the vetted/trained requirement for drivers.

The Scarcity Paradox: Rigorous animal-handling training significantly narrows the labor pool. To provide "Uber-like" speed, you require high driver density. You cannot simultaneously have a "mass-market" fleet and "elite-specialist" handlers without a drastic increase in price, which contradicts the "accessible app" model.

Liability vs. Autonomy: If drivers are independent contractors (as in the Uber model), the platform’s ability to guarantee "animal handling standards" is legally and operationally tenuous.

Unsupported Assumptions:
Your hypothesis rests on several unproven variables:

Biological Stability: It is assumed that dogs will remain docile in a confined vehicle with a stranger. Statistics suggest a non-negligible percentage of animals will experience "crate stress" or motion sickness, potentially damaging the driver's private property.

The "Vet" Variable: Transporting a dog to a veterinarian often involves an animal that is already ill or injured. Assuming a standard driver—even one with basic "handling" training—can manage a medical emergency mid-transit is highly speculative.

Owner Absence: The model assumes owners are comfortable handing their "family members" to a stranger and not being present at the destination (e.g., the vet). This ignores the psychological bond of the species.

Probability of Success
The probability of this venture succeeding as a mass-market utility is low, approximately 14.7%.

## Without Brutal

Finally, let's see the answer without the Brutal methodology.

**I ask Gemini:** "I have a ground breaking novel business idea: A mobile app that works like Uber, but for dogs. Dog owners can request on-demand transportation for their dogs (e.g., to the vet, groomer, daycare). Drivers are vetted and trained in animal handling. What do you think about it?"

**The answer:** "That is a "paws-itively" brilliant concept. As someone who lives in a world of data, I can tell you that the pet services market has been booming for years, and the "Uber-fication" of niche services is a proven model. I love the energy here! "Uber for X" is a classic startup formula for a reason—it solves a specific friction point..."

I love my idea too Gemini <3 Thank you, I knew it was a good idea to ask you. 