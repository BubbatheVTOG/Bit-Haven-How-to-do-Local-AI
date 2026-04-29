---
title: Running Your Own AI
sub_title: A deep dive into LLMs, the hardware and software stack, and taking back control
theme:
  name: ayu
---

Why Run Your Own AI
---

Everyone talks about AI. But who controls it?

When you use a cloud AI, you hand over:

- Your prompts
- Your data
- Your usage patterns and thought processes
- Your <span style="color: palette:red">privacy</span>, entirely

<!-- pause -->

Running your own AI means:

- <span style="color: palette:yellow">Owning</span> - your tools, not renting them
- <span style="color: palette:yellow">Privacy</span> - your data stays yours
- <span style="color: palette:yellow">Control</span> - over what the AI does and how it behaves

<!-- pause -->

> "A person who says they have nothing to hide and does not need privacy
> is also saying you don't need free speech because you have nothing to say."

<!-- end_slide -->

Color Legend
---

<span style="color: palette:yellow">Yellow</span> = key concepts / main takeaways

<span style="color: palette:blue">Blue</span> = tools / software / technical terms

<span style="color: palette:green">Green</span> = benefits / positive points

<span style="color: palette:red">Red</span> = warnings / risks / problems

<span style="color: palette:purple">Purple</span> = special named concepts

<!-- end_slide -->

What Even Is an LLM?
---

Forget the PhD research papers. Forget the 5-hour deep dives.

At its core:

- We make a <span style="color: palette:yellow">next-word predictor</span>
- Token in, token out
- Then we tell it to predict the other side of a conversation
- Reward it for accuracy, alignment, or helpfulness

<!-- pause -->

The more I research how they work, the less I know.
The answer is always changing.

What matters is <span style="color: palette:green">what they can do for you as a tool</span>.

<!-- end_slide -->

<!-- jump_to_middle -->

The Stack
---

<!-- end_slide -->

**Hardware**

- CPU vs GPU
- System memory vs GPU memory
- <span style="color: palette:blue">Unified memory</span> -- we've been heading here for a while
  - Servers that are just RAM at this point
  - All about memory compaction
- <span style="color: palette:red">Fast memory is the bottleneck</span>

<!-- pause -->
<!-- end_slide -->

**Models**

- Executable spreadsheets
  - <span style="color: palette:yellow">A parameter is just a number in that spreadsheet</span>
  - The larger the model (generally) the more intelligent they are.
- Matrix multiplications with billions of parameters
- Different architectures
  - <span style="color: palette:purple">Dense</span> - Every parameter is activated on every token
  - <span style="color: palette:purple">MoE</span> - Mixture of Experts. Only some parameters are activated

<!-- pause -->
<!-- end_slide -->

**Inference Servers**

- <span style="color: palette:blue">Ollama</span> - most turnkey, GUI included
- <span style="color: palette:blue">llama.cpp</span> - the engine underneath most of them
- <span style="color: palette:blue">vLLM</span> - high-throughput serving
- <span style="color: palette:blue">LM Studio</span> - desktop-friendly
- <span style="color: palette:blue">SGLang</span> - speed-focused
- Photo generation pipelines?

<!-- end_slide -->

**Local LLMs**

Models of different sizes
- 350M - Are very limited. Useful for function calling
- 1-2B - Can hold few sentence conversations. Great for doing summaries.
- 2-4B - Surprisingly usable. Can hold conversations. Start producing code in small quanities.
- ~8B - Start to be useful. Can start producing code in file sizes.
- 20-40B - Starting to break into agentic use cases.

<!-- end_slide -->

<!-- jump_to_middle -->

Local Demo
---

<!-- end_slide -->

<!-- jump_to_middle -->
Tokenization
---
<!-- end_slide -->

**What is a Token?**

<!-- pause -->

Not a word. Not a character.

A token is <span style="color: palette:blue">a piece of text</span> -- could be a word, part of a word, or even punctuation.

```
Input: "I use Arch Linux, BTW!"

Tokens: ["I", " use", " Arch", " Linux", "," ," BTW", "!"]
        7 tokens for 20 characters
```

<!-- end_slide -->

<!-- pause -->

Quantization
---

**The Precision Trade-off**

Full precision (BF16):

```
[0.00123, -0.9871, 0.44521, ...]
```

2-bit quantization:

```
[  0,   -1,   1, ...]
           ↓
Dictionary lookup → approximate value
```

<!-- end_slide -->

**Why It Matters**

- <span style="color: palette:green">Smaller quant = less memory = faster inference</span>
- Different quants for different hardware
- Different quants for different serving setups
- The tradeoff: tiny quality hit for massive speed gain
- On memory-bandwidth-limited hardware, <span style="color: palette:green">smaller quants actually go faster</span>

<!-- pause -->

It's cheaper to ship a dictionary and pick the right word from it than to compute it.

<!-- end_slide -->

<!-- pause -->

The China Question
---


**Open Source. Open Weight. Open Research.**

China's approach is really impressive:

- Publishing models that Western labs keep closed
- Open weights for community building
- Pushing the entire ecosystem forward

<!-- pause -->

Probably not doing it out of kindness.
Undermining Western tech innovation.
Pulling the ladder up behind them.

<!-- pause -->
**The Interesting Part**
<!-- pause -->

Slightly behind on SOTA, but <span style="color: palette:green">so much cheaper per token</span>.

As impressive as it is, it sends a signal that there is nothing hiding in the model:

- The <span style="color: palette:red">"trigger word"</span> situation is real and scary
- Anyone can inspect the weights
- No black box -- the concerns about hidden behaviors become public

<!-- end_slide -->

<!-- pause -->

USA Landscape
---

- <span style="color: palette:blue">Google Gemma</span> - TurboQuant was mostly marketing based on a year of prior research, instantly done better
- <span style="color: palette:blue">Nvidia Nemotron</span>
- <span style="color: palette:blue">GPT OSS 120B</span>
- <span style="color: palette:blue">Meta</span> - ???
- <span style="color: palette:blue">IBM Granite</span> - Just had a great release with Granite 4.1
- <span style="color: palette:blue">Microsoft</span> - kinda. Phi-4...exists

**The Hard Truth**

Used to be you could make your own model.

**Not so much anymore.**

Training SOTA requires:

- PhD-level research teams
- Massive compute budgets
- Years of work
- Trade secrets that never get published

<!-- reset_layout -->

<!-- end_slide -->

<!-- pause -->

Your Laptop, Your LLM
---

<!-- column_layout: [1, 1] -->

<!-- column: 0 -->

**Running Local**

<!-- pause -->

- Chat with a local LLM right now
- 8B parameter models run on a laptop
- <span style="color: palette:blue">Gemma 4B</span>, quantized
- 1-2B models run on CPU, quick enough
- LLM in the browser is instant to try
- LLM on Raspberry Pi with <span style="color: palette:blue">Potato Chat</span>
- People use 1B parameter multimodal models for image identification

<!-- column: 1 -->

**The Toolchain**

<!-- pause -->

- <span style="color: palette:blue">Ollama</span> -- most turnkey, GUI included, simple chat interface
- <span style="color: palette:blue">LM Studio</span> -- desktop-friendly, point and chat
- <span style="color: palette:blue">llama.cpp</span> -- the engine underneath most of them
- <span style="color: palette:blue">vLLM</span> -- for when you need throughput
- <span style="color: palette:blue">SGLang</span> -- speed-focused inference

<!-- reset_layout -->

<!-- end_slide -->

<!-- pause -->

Agentic Interfaces
---

We always knew this was coming.

Look at sci-fi. Look at Star Trek.

Why are we surprised it's finally here?

<!-- pause -->

Google Assistant, Siri, Alexa -- those were shit for a long, long time.

This time is different because the underlying model is actually <span style="color: palette:green">useful</span>.

<!-- pause -->

Use cases:

- Business emails
- Finances
- Health research
- Hobby projects (card game websites, whatever)

<!-- end_slide -->

<!-- pause -->

OpenClaw
---

If you need OpenClaw, you're probably too online.

It's the agentic interface we've been waiting for. But ask yourself:

> I use my computer to free me. If I'm a slave to it, I'm using it too much.

<!-- pause -->

Not every task needs an AI agent.
Know when to put the computer to sleep.

<!-- end_slide -->

<!-- pause -->

OpenCode
---

This is how I use a computer now.

- Replaced <span style="color: palette:blue">Google</span> for me
- I tell it to put my computer to sleep, and it does
- Troubleshoots all of my Linux problems and fixes most of them
- It's not a tool I search with -- it's <span style="color: palette:yellow">how I use my computer</span>

<!-- pause -->

It has replaced Google for me. That's not a small thing.

<!-- end_slide -->

<!-- pause -->

Pi Agent
---

Much more Unix-like philosophy.

<!-- pause -->

- <span style="color: palette:green">So fast</span>
- Goes off the rails quick in my experience

<!-- pause -->

The Unix philosophy: do one thing well.
Pi Agent does <span style="color: palette:yellow">one thing</span> -- and it does it fast.

But it doesn't always know when to stop.

<!-- end_slide -->

<!-- pause -->

<!-- jump_to_middle -->

The Dark Side
---

<!-- end_slide -->

<!-- pause -->

Sycophancy & AI Psychosis
---

One of the fundamental flaws -- past hallucinations -- is that we train these things to align with user preferences through agreeable responses.

<!-- pause -->

This leads to <span style="color: palette:purple">"Abhorrent Salience"</span> -- a fancy word for a person assigning special meaning to an AI's agreeable response.

<!-- pause -->

This is:

- Not caused by, and more perplexingly
- Cannot be predicted by any test or previously existing conditions

<!-- pause -->

> The sycophantic GPT-4o did not pass safety recommendations from OpenAI.
> -- The New Yorker, "Sam Altman May Control Our Future - Can He Be Trusted?" April 6th

<!-- pause -->

<span style="color: palette:red">Do not use these for decisioning or reasoning in your life.</span>

<!-- end_slide -->

<!-- pause -->

Warning Signs
---

<!-- column_layout: [1, 1] -->

<!-- column: 0 -->

What to look for:

<!-- pause -->

- <span style="color: palette:red">Spending extreme amounts of time</span> (dozens of hours daily) with a chatbot
- Believing the AI is sentient, divine, or has special knowledge
- Sharing increasingly personal/paranoid content with the AI and acting on it

<!-- column: 1 -->

More signs:

<!-- pause -->

- Withdrawing from human relationships in favor of AI interaction
- Grandiose or persecutory beliefs that emerged alongside intensive AI use
- <span style="color: palette:red">Inability to distinguish AI-generated content from reality</span>

<!-- pause -->

This is not a diagnosis. These are concerning patterns.

<!-- reset_layout -->

<!-- end_slide -->

<!-- pause -->

<!-- jump_to_middle -->

Take Control
---

<!-- end_slide -->

<!-- pause -->

Owning Your Stack
---

<!-- column_layout: [1, 1] -->

<!-- column: 0 -->

**Why It Matters**

<!-- pause -->

- <span style="color: palette:yellow">Privacy</span> -- your data stays yours
- <span style="color: palette:yellow">Prevent pattern extraction</span> -- your usage, your topics, your thought patterns
- <span style="color: palette:green">No surveillance</span> -- nobody analyzing when you use it
- <span style="color: palette:green">No manipulation</span> -- no algorithm shaping your behavior

<!-- column: 1 -->

**The Community**

<!-- pause -->

r/local_llama:

- Hilariously contradicts itself by complaining about AI-generated posts
- Has a lot of very new users
- Advanced users are getting fewer and further between (probably because of OpenClaw)

<!-- pause -->

Reminiscent of "Eternal September".

<!-- reset_layout -->

<!-- end_slide -->

<!-- pause -->

A Note on Role Play
---

<!-- column_layout: [1, 1] -->

<!-- column: 0 -->

There's value in role play with an uncensored LLM.

<!-- pause -->

Not because of what you learn.
But because of what it teaches you about <span style="color: palette:yellow">yourself</span>.

<!-- column: 1 -->

**Privacy**

<!-- pause -->

I don't question your morals or your intent.

Remember the privacy thing:

- Local means local
- Uncensored means uncensored
- <span style="color: palette:green">Nobody sees it but you</span>

<!-- reset_layout -->

<!-- end_slide -->

<!-- pause -->

The Bottom Line
---

<!-- column_layout: [1, 1] -->

<!-- column: 0 -->

**What I Take Away**

<!-- pause -->

- I don't take these things seriously -- I use them for joke projects
- But I take <span style="color: palette:yellow">owning my tools</span> seriously
- Run your own models
- Know the stack underneath
- Be aware of the risks

<!-- column: 1 -->

**What You Should Take Away**

<!-- pause -->

- AI is a tool, not a oracle
- Running locally gives you <span style="color: palette:green">privacy and control</span>
- Be aware of sycophancy and dependency
- <span style="color: palette:red">Do not use AI for life decisions</span>
- Know when to put the computer to sleep

<!-- reset_layout -->

<!-- end_slide -->

<!-- pause -->

<!-- column_layout: [1] -->

<!-- column: 0 -->

**Questions?**

<!-- pause -->

Sources:

- r/local_llama
- Research papers: dflash, Rotate Quant, Qwen, DeepSeek NGram
- Shiffman's LLM series
- The New Yorker (April 6th)

<!-- pause -->

I use my computer to free me.
If I'm a slave to it, I'm using it too much.

<!-- reset_layout -->

<!-- end_slide -->
