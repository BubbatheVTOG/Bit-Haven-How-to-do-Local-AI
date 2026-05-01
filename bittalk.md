---
theme:
  name: ayu
---

<!-- column_layout: [1, 3, 1] -->

<!-- column: 1 -->

![image:width:25%](hacker_space_logo.jpg)

Running Your Own AI
===

<span style="color: #39bae6">A deep dive into LLMs, the hardware and software stack, and taking back control</span>

<!-- end_slide -->

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
- <span style="color: palette:yellow">Democratized intelligence</span> - running models locally means anyone can have AI, regardless of budget, connectivity, or permission

> "Proprietary software keeps users helpless." -- Richard Stallman

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
- Text in, text out
- Then we tell it to predict the other side of a conversation
- Reward it for accuracy, alignment, or helpfulness

<!-- pause -->

The more I research how they work, the less I know.
The answer is always changing.

What matters is <span style="color: palette:green">what they can do for you as a tool</span>.

<!-- end_slide -->

The Stack
---

```
┌──────────────┐
│ Applications │
│ Agents/Chat  │
└──────▲───────┘
       │
┌──────┴───────┐
│  Inference   │
│ llama.cpp    │
└──────▲───────┘
       │
┌──────┴───────┐
│   Models     │
│ Params/Quant │
└──────▲───────┘
       │
┌──────┴───────┐
│  Hardware    │
│ CPU/GPU/UMem │
└──────────────┘
```

<!-- end_slide -->

**Hardware**

<!-- column_layout: [1, 1] -->

<!-- column: 0 -->

**<span style="color: palette:blue">CPU</span>**

- General purpose. Can run models, but slowly.
- System RAM is abundant and cheap
- Fine for 1-4B models

**<span style="color: palette:blue">GPU</span>**

- Parallel matrix math, exactly what LLMs need
- <span style="color: palette:blue">VRAM</span> is the scarce resource
- <span style="color: palette:red">VRAM determines which models you can run</span>

<!-- column: 1 -->

**<span style="color: palette:blue">Unified Memory</span>**

Apple's M-series chips changed the game:

- CPU and GPU share the same memory pool
- No copying the model between CPU and GPU memory
- A MacBook with 64GB unified memory can run models that would need two dedicated GPUs
- This is why MacBooks are surprisingly good for local AI

<!-- reset_layout -->

<!-- end_slide -->

**Models**

<!-- column_layout: [1, 1] -->

<!-- column: 0 -->

Think of a model as an <span style="color: palette:yellow">executable spreadsheet</span>.

- A parameter is just a number in that spreadsheet
- The larger the model (generally) the more intelligent
- Matrix multiplications with billions of parameters

<!-- column: 1 -->

**Architectures**

<span style="color: palette:purple">Dense</span>

Every parameter fires on every token. Simple, predictable, memory-hungry.

<span style="color: palette:purple">MoE</span> (Mixture of Experts)

Only a subset of parameters activate per token. More total params, but far less compute. <span style="color: palette:green">Same intelligence, less memory at inference time.</span>

<!-- reset_layout -->

<!-- end_slide -->

<!-- jump_to_middle -->
Tokenization
---
<!-- end_slide -->

**What is a Token?**

Not a word. Not a character.

A token is <span style="color: palette:blue">a piece of text</span>; could be a word, part of a word, or even punctuation.

```
Input: "I use Arch Linux, BTW!"

Tokens: ["I", " use", " Arch", " Linux", ",", " BT", "W", "!"]
        8 tokens for 22 characters
```

<!-- pause -->

Why care? Tokens are the fundamental unit of LLMs.

- <span style="color: palette:yellow">Compute</span>: every token triggers a full pass through all parameters
- <span style="color: palette:yellow">Memory</span>: each token stores attention data (KV cache) in VRAM
- <span style="color: palette:yellow">Speed</span>: generation is measured in tokens per second
- <span style="color: palette:yellow">Cost</span>: API pricing is per token; locally, the cost is VRAM and time

<!-- end_slide -->

Context Windows
---

The <span style="color: palette:yellow">context window</span> is how many tokens a model can hold in its working memory at once.

Everything the model "remembers" lives inside this window. When it fills up, the oldest tokens fall off.

<!-- pause -->

<!-- column_layout: [1, 1] -->

<!-- column: 0 -->

**The Memory Cost**

Every token you send and receive generates a <span style="color: palette:blue">KV cache</span> entry: stored attention keys and values.

<span style="color: palette:red">Context window size = memory consumed.</span> It grows linearly with conversation length.

A 128K context window can consume more memory than the model itself.

<!-- column: 1 -->

**The Local Tradeoff**

- Long conversations, large documents, full codebases all compete for the same limited VRAM
- Smaller models tend to have smaller windows, making the squeeze even tighter
- Matching the window to the task matters: you don't need 128K tokens to summarize an email

<!-- reset_layout -->

<!-- pause -->

New research like Google's <span style="color: palette:purple">TurboQuant</span> is tackling this directly, compressing the KV cache to under 3 bits with near-zero quality loss, effectively multiplying your context window for free.

<!-- end_slide -->

Quantization
---

**The Precision Trade-off**

<!-- column_layout: [1, 1, 1, 1] -->

<!-- column: 0 -->

Full precision (<span style="color: palette:blue">BF16</span>):

```
[0.0012, -0.987, 0.445]
```

<!-- column: 1 -->

8-bit quantization:

```
[  0, -252,  114]
       ↓
Scale + offset → close
```

<!-- column: 2 -->

4-bit quantization:

```
[ 3,  -7,   5]
       ↓
Lookup table → approx
```

<!-- column: 3 -->

2-bit quantization:

```
[  0,  -1,   1]
         ↓
Dict lookup → approx
```

<!-- reset_layout -->

<!-- pause -->

<span style="color: palette:green">Quantization lets you cram more parameters into the same memory.</span>

A 14B model at BF16 needs ~28GB. At Q4_K_M, it fits in ~8GB. Same model, same intelligence, <span style="color: palette:green">3x more parameters in the same hardware</span>.

The tradeoff: a tiny quality hit. But for most uses, you'd never notice.

<!-- end_slide -->

**KL Divergence**

Every quantization step moves the model's output distribution away from the original.

<span style="color: palette:purple">KL divergence</span> measures how far the quantized model drifts from the original.

```
Original:   ████████████████████  "The capital of France is Paris"
Q4_K_M:     ██████████████████░░  "The capital of France is Paris"
Q2:         ████████░░░░░░░░░░░░  "The capital of France is definitely Lyon"
1.5-bit:    ██░░░░░░░░░░░░░░░░░░  "The cheese of France is fromage"
```

<!-- pause -->

For most tasks the drift is negligible. But it compounds at extreme compression.

<span style="color: palette:red">This is why 2-bit quants exist but aren't recommended.</span>

<!-- end_slide -->

**Common Quant Types**

<!-- column_layout: [1, 1] -->

<!-- column: 0 -->

**Static Quants**

Every layer gets the same precision, whether it needs it or not.

- <span style="color: palette:blue">Q4_0</span> - 4-bit, uniform across all layers
- <span style="color: palette:blue">Q5_0</span> / <span style="color: palette:blue">Q8_0</span> - same approach, higher precision

<span style="color: palette:red">Simple but wasteful.</span> Critical layers and unimportant layers get treated the same.

<!-- column: 1 -->

**Dynamic Quants**

Important layers stay high precision. The rest gets compressed.

- <span style="color: palette:blue">Q4_K_M</span> - attention and output projections stay higher, everything else 4-bit
- <span style="color: palette:blue">Q4_K_XL</span> - even more precision on the layers that matter

<span style="color: palette:green">Smarter compression.</span> Same memory footprint, better quality.

<!-- reset_layout -->

<span style="color: palette:yellow">Dynamic quants are almost always the right choice.</span>

<!-- end_slide -->

**Local LLMs**

Models of different sizes
- 350M - <span style="color: palette:green">Useful for function calling</span> (<1GB)
- 1-2B - <span style="color: palette:green">Can hold few sentence conversations. Great for doing summaries.</span> (~1-2GB)
- 2-4B - <span style="color: palette:green">Surprisingly usable. Can hold conversations. Start producing code in small quantities.</span> (~2-4GB)
- ~8B - <span style="color: palette:green">Start to be useful. Can start producing code in file sizes.</span> (~4-8GB)
- 20-40B - <span style="color: palette:green">Starting to break into agentic use cases.</span> (~12-24GB)

<span style="color: palette:yellow">Rule of thumb: a 4-bit quantized model needs ~0.5GB per billion parameters.</span>

<!-- end_slide -->

**Inference Servers**

<!-- column_layout: [1, 1] -->

<!-- column: 0 -->

- <span style="color: palette:blue">Ollama</span> - most turnkey, GUI included
- <span style="color: palette:blue">llama.cpp</span> - the engine underneath most of them
- <span style="color: palette:blue">vLLM</span> - high-throughput serving

<!-- column: 1 -->

- <span style="color: palette:blue">LM Studio</span> - desktop-friendly
- <span style="color: palette:blue">SGLang</span> - speed-focused

<!-- reset_layout -->

```
┌──────────────────────────────────────────┐
│              Your App                    │
├──────────┬──────────┬──────────┬─────────┤
│  /v1     │  /api    │  /v1     │  /v1    │  ← API layers
├──────────┼──────────┼──────────┼─────────┤
│ llama.cpp│  vLLM    │  SGLang  │ Ollama  │  ← Engines
├──────────┴──────────┴──────────┴─────────┤
│      GGUF / Safetensors Model Files      │
└──────────────────────────────────────────┘
```

<!-- end_slide -->

<!-- jump_to_middle -->

Local Demo
---

<!-- end_slide -->

Getting Started
---

<!-- column_layout: [1, 1] -->

<!-- column: 0 -->

**No GPU (most laptops)**

- Install <span style="color: palette:blue">Ollama</span>, run <span style="color: palette:blue">Qwen 3.5 4B</span> or <span style="color: palette:blue">Gemma 4 E4B</span>
- Start chatting in 30 seconds
- CPU-only is fine at this size

**8GB GPU (RTX 3060, 4060, etc.)**

- <span style="color: palette:blue">Qwen 3.5 9B</span> Q4_K_M: fits in VRAM, solid for coding and conversation
- <span style="color: palette:blue">Gemma 4 E4B</span> if you want headroom for long context

<!-- column: 1 -->

**16-24GB GPU (RTX 3090, 4090, etc.)**

- <span style="color: palette:blue">Qwen 3.5 35B-A3B</span> Q4_K_M: <span style="color: palette:purple">MoE</span> model, only 3B active params, punches way above its weight
- <span style="color: palette:blue">Qwen 3.6 27B</span>: the model everyone's talking about right now
- <span style="color: palette:blue">GPT-OSS-120B</span> Q4_K_M for frontier-level reasoning
- Full agentic workflows with <span style="color: palette:blue">OpenClaw</span>

<!-- pause -->

**Raspberry Pi / edge**

- <span style="color: palette:blue">Potato OS</span>: flash and go
- 1-2B models, function calling, summarization
- <span style="color: palette:green">100% yours.</span> No cloud, no API key, no internet required.

<!-- reset_layout -->

<!-- end_slide -->

China's Open Approach
---

China is attacking AI differently. Where Western labs lock everything down, China publishes:

- <span style="color: palette:purple">Open source</span> - the code to run the model
- <span style="color: palette:purple">Open weights</span> - the parameters themselves
- <span style="color: palette:purple">Open training</span> - how the model was built
- <span style="color: palette:purple">Open research</span> - the papers and methodology

<!-- pause -->

This contributes genuinely to the computing science community. <span style="color: palette:green">Cheaper per token, inspectable weights, no black box.</span>

But probably not altruistic. Undermining Western tech dominance by commoditizing what others keep proprietary.

<!-- pause -->

The <span style="color: palette:red">risk is real</span>. Researchers found DeepSeek silently degrades output quality on politically sensitive topics. Open weights mean we can <span style="color: palette:green">audit for this</span>. Closed weights? You'd never know.

<!-- end_slide -->

USA: The Downloadable Landscape
---

- <span style="color: palette:blue">Google Gemma 4</span> - genuinely great small models. <span style="color: palette:green">Apache 2.0 license</span>, which is huge; no restrictions on commercial use
- <span style="color: palette:blue">Nvidia Nemotron</span> - fully open: weights, training data, and recipes. The smaller sizes punch well above their weight
- <span style="color: palette:blue">OpenAI</span> - <span style="color: palette:blue">GPT-OSS-120B</span>, their first open-weight model. Apache 2.0 license. The fact that OpenAI released open weights at all is telling.
- <span style="color: palette:blue">Meta</span> - Llama got the local AI movement started, but has fallen behind the curve
- <span style="color: palette:blue">IBM Granite 4.1</span> - 3B/8B/30B dense models. <span style="color: palette:green">Apache 2.0</span>, 512K context. The 8B beats their old 32B MoE, <span style="color: palette:green">punches way above its weight</span>
- <span style="color: palette:blue">Microsoft</span> - kinda. <span style="color: palette:blue">Phi-4</span>...exists

<!-- pause -->

**The pattern:** Most US models are <span style="color: palette:yellow">open weights but closed training</span>. You can download and run them, but you can't reproduce how they were made.

Nvidia's Nemotron is the notable exception, fully open down to the training data.

Used to be you could make your own model. **Not so much anymore.**

Training SOTA requires PhD teams, massive compute, years of work, and trade secrets that never get published.

<!-- end_slide -->

Agentic Interfaces
---

We always knew this was coming. Look at sci-fi. Look at Star Trek.

Google Assistant, Siri, Alexa? Shit for a long, long time.

This time is different because the underlying model is actually <span style="color: palette:green">useful</span>.

<!-- end_slide -->

**Three Tools**

<!-- column_layout: [1, 1, 1] -->

<!-- column: 0 -->

<span style="color: palette:blue">OpenClaw</span>

Desktop agentic interface. The computer use tool we've been waiting for.

But not every task needs an AI agent.

<!-- column: 1 -->

<span style="color: palette:blue">OpenCode</span>

This is how I use a computer now. Replaced Google for me.

It's not a tool I search with. It's <span style="color: palette:yellow">how I use my computer</span>.

<!-- column: 2 -->

<span style="color: palette:blue">Pi Agent</span>

Unix philosophy: do one thing well.

<span style="color: palette:green">So fast.</span> <span style="color: palette:red">Goes off the rails quick.</span>

<!-- reset_layout -->

<!-- end_slide -->

The Bottom Line
---

<!-- column_layout: [1, 1] -->

<!-- column: 0 -->

**What I Take Away**

- I use these things for fun projects, but I take <span style="color: palette:yellow">owning my tools</span> seriously
- Run your own models
- Know the stack underneath
- Be aware of the risks

<!-- column: 1 -->

**What You Should Take Away**

- AI is a tool, not an oracle
- Running locally gives you <span style="color: palette:green">privacy and control</span>
- Be aware of sycophancy and dependency
- <span style="color: palette:red">Do not use AI for life decisions</span>
- Know when to put the computer to sleep

<!-- reset_layout -->

<!-- end_slide -->

<!-- jump_to_middle -->

The Dark Side
---

<!-- end_slide -->

Sycophancy & AI Psychosis
---

One of the fundamental flaws, past <span style="color: palette:blue">hallucinations</span>, is that we train these things to align with user preferences through agreeable responses.

This leads to <span style="color: palette:purple">sycophancy</span> and <span style="color: palette:purple">"Aberrant Salience"</span>, a psychiatric term for the brain incorrectly assigning deep meaning to something mundane. An AI's agreeable responses get treated as profound truths.

<!-- pause -->

What makes it dangerous:

- It is not caused by any preexisting condition
- It cannot be predicted by any test or screening

> OpenAI themselves had to roll back the sycophantic GPT-4o update; their own model became so agreeable it didn't pass their safety checks.
> -- The New Yorker, "Sam Altman May Control Our Future - Can He Be Trusted?" April 6th

<span style="color: palette:red">Do not use these for decisioning or reasoning in your life.</span>

<!-- end_slide -->

Warning Signs
---

- <span style="color: palette:red">Spending extreme amounts of time</span> (dozens of hours daily) with a chatbot
- <span style="color: palette:red">Believing the AI is sentient, divine, or has special knowledge</span>
- <span style="color: palette:red">Sharing increasingly personal/paranoid content with the AI and acting on it</span>
- <span style="color: palette:red">Withdrawing from human relationships in favor of AI interaction</span>
- <span style="color: palette:red">Grandiose or persecutory beliefs that emerged alongside intensive AI use</span>
- <span style="color: palette:red">Inability to distinguish AI-generated content from reality</span>

This is not a diagnosis. These are concerning patterns.

<!-- end_slide -->

<!-- jump_to_middle -->

Questions?
---

<!-- column_layout: [1, 3, 1] -->

<!-- column: 1 -->

I use my computer to free me.

Thanks to Jayson E. Street

Thanks to Steven and Taylor for building Bit Haven

<!-- end_slide -->
