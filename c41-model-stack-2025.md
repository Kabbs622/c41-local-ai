# C41 Cinema: The Model Stack
Fresh research, February 2025. Models only, no hardware constraints.

Your needs: a screenwriting partner that feels human with zero guardrails, plus professional content generation (photo, video, audio) for client work.

---

## 1. THE BRAIN — Language Models

### Creative Writing Rankings (Verified Data)

The Lechmazur Creative Story-Writing Benchmark V4 is the most rigorous creative writing evaluation available. 400 stories per model, scored by 7 independent grader LLMs on an 18-question rubric covering character depth, plot, world-building, originality, voice, prose quality, and element integration.

**Open-weight models ranked by creative writing score:**

| Rank (Overall) | Model | Score | Type |
|------|-------|-------|------|
| #7 | **Kimi K2-0905** | 8.331 | MoE ~1T total, ~60B active |
| #15 | **Qwen 3 Max Preview** | 8.091 | Dense |
| #16 | **Kimi K2.5 Thinking** | 8.068 | MoE |
| #18 | **Qwen3 Max** | 7.842 | Dense |
| #19 | **MiniMax M2.1** | 7.777 | MoE 230B, ~10B active |
| #20 | **Kimi K2 Thinking** | 7.687 | MoE |
| #21 | **DeepSeek V3.2** | 7.601 | MoE 685B |
| #24 | **Baidu Ernie 4.5 300B** | 7.506 | MoE 300B, ~47B active |
| #25 | **GLM-4.6** | 7.452 | MoE ~107B |
| #28 | **GPT-OSS-120B** | 7.030 | MoE ~120B, ~3B active |
| #30 | **Llama 4 Maverick** | 5.777 | MoE (dead last) |

For context, the closed models above them: Claude Opus 4.6 (8.561), GPT-5.2 (8.511), GPT-5 Pro (8.474). **Kimi K2-0905 at 8.331 beats Gemini 3 Pro (8.221), Gemini 2.5 Pro (8.219), Mistral Medium 3.1 (8.201), and every version of Claude Opus 4.5 and Sonnet 4.5.** That's an open model outperforming most closed frontier models at creative writing.

### Coding Rankings (SWE-bench, December 2025)

| Model | Resolved Rate | Note |
|-------|--------------|------|
| **GLM-4.7** | Top open-source | Ranks alongside GPT-5.1-codex |
| **Kimi K2.5** | Near-Opus level | "Costs 10% of Opus at similar performance" |
| **GPT-OSS-120B** | Large jump w/ reasoning | Best all-around open model per practitioners |

### The Models

#### Kimi K2-0905 — The Creative Powerhouse
- **#1 open model for creative writing.** Period. Verified by the most rigorous benchmark available.
- MoE architecture (~1T total params, ~60B active). Fast despite enormous total size.
- Profile from the benchmark: "Embodied interiority, patterned escalation, environment-as-constraint, closure with cost." Its top stories scored 9.13/10.
- Practitioner caveat: tendency to hallucinate factual details. When a story needs real aerodynamics or technical accuracy, K2 defaults to genre tropes instead of real physics. It will confidently fabricate sources and deny doing so.
- **Best for:** Screenwriting, dialogue, character development, short films, narrative work. Your primary creative partner.

#### Kimi K2.5 Thinking — The Coder + Writer
- Newer than K2. "Best open model for coding" (community consensus post).
- Scores 8.068 on creative writing (still top-tier, above MiniMax and DeepSeek).
- Costs ~10% of Opus at similar performance for code.
- **Best for:** Code, technical work, AND creative writing when you need reasoning.

#### GPT-OSS-120B — The All-Rounder
- OpenAI's open-source model. MoE, ~5.1B active params during inference. 61GB model data (MXFP4), ~65GB total with KV cache at 32K context.
- **Fast.** ~80 tok/s on M2 Ultra 192GB (verified llama.cpp benchmark). ~97 tok/s on a quad R9700 build (different hardware). 65 tok/s on M3 Ultra 256GB (user report, lower spec config). On the 512GB M3 Ultra, expect ~80-85 tok/s single user.
- Natively trained in MXFP4 (not standard 4-bit). Consistent, reliable tool calling, great at everything.
- Lower creative writing score (7.030) but the speed and reliability make it the go-to daily driver for non-creative tasks.
- Unsloth supports fine-tuning for GPT-OSS.
- **Best for:** Fast daily driver, code, tool use, general tasks. Your "instant response" model.

#### Qwen3-Coder-Next — The Fast Daily Driver
- MoE ~80B total, ~3B active. 38.88 tok/s on 128GB VRAM builds.
- "For non-coders, do not sleep on Qwen3-Coder-Next. It's the closest model to Gemini quality locally. I genuinely feel I am conversing with a fellow thinker rather than an echo chamber." (Community post)
- Pragmatic problem-solver. Unprompted, suggests authors, books, theories. Not a sycophant.
- 256K context. Runs great on Apple Silicon via Ollama.
- Qwen 3.5 is coming soon (PR opened on GitHub) which will likely replace this.
- **Best for:** Chat, quick tasks, brainstorming. Backup daily driver alongside GPT-OSS.

#### MiniMax M2.1 — The Balanced Writer
- MoE 230B total, ~10B active. 48.78 tok/s on 128GB builds.
- Scores 7.777 creative writing. The €9K GH200 guy chose this specifically over GLM, DeepSeek, and Kimi at tight quantizations because those "seem to be lobotomised."
- Good tool use. Works with Claude Code natively.
- **Best for:** Creative work when K2 is too slow or too large. Good balance of speed and writing quality.

#### DeepSeek V3.2 — The Reasoning Ceiling
- MoE 685B. The largest open model available.
- Scores 7.601 creative writing. Personality research: "the enthusiastic friend who over-explains everything." Verbose, confident, proactive.
- 25 tok/s on the $17K 10-GPU build at Q2 quantization.
- **Best for:** Maximum reasoning power when you need the absolute best thinking. Heavy, slow, but the smartest open model.

#### GLM-4.7 — The Code Beast
- Strongest open-source model on SWE-bench, period. Ranks alongside GPT-5.1-codex (closed).
- One senior engineer's workflow: "I use Opus 4.5 for planning, then GLM 4.7 to execute." That's an open model replacing the second step of a cloud workflow.
- GLM-4.6 scores 7.452 on creative writing; 4.7 is newer and likely better.
- Also has GLM-4.5 Air variant (lighter, good for agentic dev).
- **Best for:** Serious software development, when code quality matters more than speed.

#### Baidu Ernie 4.5 300B A47B — The Wildcard
- 300B total, 47B active. Scores 7.506 on creative writing (above GLM-4.6).
- Less discussed in the community but benchmarks well.
- **Best for:** Worth testing as an alternative voice.

### The Uncensored Question

**RLHF alignment literally compresses personality.** Research measuring LLM hidden states across 7 behavioral axes found: base Llama is cold, reluctant, verbose. After alignment, it becomes the flattest model tested (4/7 axes in "weak zone"). Alignment makes models safer but creatively flatter.

**Abliteration** (Heretic tool) can remove refusal behavior from model weights without fine-tuning. It also has a **slop-reduction** mode that turned Mistral Nemo's prose from syrupy AI-ish ("cobblestone streets whispered tales of old") to clean Hemingway-esque prose ("Every morning, Henry opened his shop at 7:00 AM sharp"). However, UncensorBench research shows abliteration is imperfect: models still refuse ~70% of the time but express refusal in harder-to-detect ways.

**The real answer:** Chinese models (Kimi, Qwen, DeepSeek, MiniMax) have lighter creative restrictions than Western models (Llama, Mistral). Kimi K2-0905 writing samples include dark, complex, morally ambiguous narratives without sanitization. Community fine-tunes on HuggingFace go further.

### My Recommendation for Your Stack

| Role | Model | Why |
|------|-------|-----|
| Primary creative partner | Kimi K2-0905 | #1 open creative writing model, beats most closed models |
| Code + technical | Kimi K2.5 or GLM-4.7 | Near-Opus coding, K2.5 also writes well |
| Fast daily driver | GPT-OSS-120B or Qwen3-Coder-Next | Instant, reliable, great for quick tasks |
| Heavy reasoning | DeepSeek V3.2 | The absolute ceiling when you need it |
| Balanced creative | MiniMax M2.1 | Speed + writing quality balance |

### Model Sizes (for future hardware planning)

| Model | Total Params | Active | FP16 Size | Q4 Size |
|-------|-------------|--------|-----------|---------|
| Kimi K2-0905 | ~1T | ~60B | ~2TB | ~500GB+ |
| DeepSeek V3.2 | 685B | MoE | ~1.3TB | ~350GB |
| Baidu Ernie 4.5 | 300B | ~47B | ~600GB | ~150GB |
| MiniMax M2.1 | 230B | ~10B | ~460GB | ~115GB |
| GLM-4.7 | ~218B | ~32B | ~436GB | ~110GB |
| GPT-OSS-120B | ~117B | ~5.1B | 61GB (native MXFP4) | 61GB (native format is already 4-bit) |
| Qwen3-Coder-Next | ~80B | ~3B | ~160GB | ~40GB |

---

## 2. THE EYES — Image Generation

### Comprehensive Model Comparison (Verified)

A practitioner ran 12 standardized tests across 8 models, scoring composition, text rendering, anatomy, scene complexity. 60 total points.

| Rank | Model | Score | Strength | Weakness |
|------|-------|-------|----------|----------|
| #1 | **Flux 2 Dev** | 51/60 | Best prompt adherence, text, scene composition | Terrible human anatomy (limbs, poses) |
| #2 | **Qwen 2512** | 49/60 | Very well-rounded, good anatomy | Slightly less creative than Flux 2 |
| #3 | **Z-Image base** | 47/60 | Best photorealism, best human bodies/skin | Weak at scene composition |
| #4 | **Flux 2 9B** | 45/60 | Same strengths as Dev but worse | "Non-euclidean chairs," broken anatomy |
| #5 | **Qwen (original)** | 44/60 | Good all-around | Outclassed by Qwen 2512 |
| #6 | **ZIT** (Z-Image Turbo) | 41/60 | Good aesthetics, decent people | Doesn't follow prompts well, no variety |
| #7 | **Flux 1 Krea** | 32/60 | Surprisingly good human anatomy | Weak language understanding |
| #8 | **Flux 2 4B** | 28/60 | Speed and size only | Image coherence "iffy at best" |

**Key takeaways:**
- **Flux 2 Dev** is the overall king BUT cannot do human bodies well (BFL likely removed anatomic training to prevent NSFW content, breaking anatomy like Stability did with SD3).
- **Z-Image** uses Qwen3 4B as its text encoder. Best for photorealistic human subjects. A practitioner said: "IMO the best model for realism. Especially because you can natively gen at high resolution." Runs at 1920px natively. ~95 seconds per 1280x1920 image on RTX 5090.
- **Qwen 2512** is the safe all-rounder. Good at everything, great at nothing specific.

### The Professional Image Pipeline

**For non-human subjects (products, scenes, graphics):**
1. **Flux 2 Dev** — composition, text rendering, scene accuracy
2. **SeedVR2** — upscale to 4K
3. **HiDream-E1.1** — natural language editing ("Remove the watch. Change the background.")

**For human subjects (portraits, talent, lifestyle):**
1. **Z-Image base** — photorealistic skin, bodies, lighting
2. **SeedVR2** — upscale to 4K
3. **HiDream-E1.1** — edit with words

**For brand consistency across campaigns:**
- **Flux 2 Dev** — multi-reference generation. Feed up to 10 reference images, output maintains character/style.

### LoRA Training (The Commercial Moat)
- **kohya-ss** / **ai-toolkit** — Train FLUX/Z-Image LoRAs on client brands, products, characters
- **JoyCaption** — Auto-caption training images
- **Unsloth** — Now supports fast MoE fine-tuning (12x faster, 35% less VRAM)
- Train once, use everywhere. No cloud service offers this.

---

## 3. THE MOTION — Video Generation

### Primary

**Wan 2.2 14B** (image-to-video)
- Undisputed king of local video. Verified: being used for real paid client work (Jeffu's 449-upvote post with full workflow on r/StableDiffusion).
- Image-to-video and text-to-video.
- ~24-48GB VRAM for full quality.
- DGX Spark benchmark: 720p@24fps with base ComfyUI template.

**Wan 2.2 VACE** — Video editing
- Inpainting, outpainting, reference-based generation.

### Long-Form

**FramePack** — Extended video (1 minute+)
- Constant VRAM regardless of video length.
- ~6GB minimum.

### Native Audio+Video

**LTX-2** — Generates video AND audio together
- GGUF quantized versions run on 16GB VRAM.
- English audio decent. Non-English broken (practitioner needed ~200 re-rolls for usable Japanese clips).
- Lip-sync inconsistent. ~5% yield rate for usable clips.
- Worth having for quick drafts, not production-ready for dialogue.

### Speed Optimizations
- **TeaCache** — 2x speed boost
- **Self-Forcing LoRAs** — Faster generation
- **CFG-Zero*** — Reduce guidance steps

### Video LoRA Training
- **musubi-tuner** — Train Wan 2.2 video LoRAs. 16GB VRAM, 3 hours for 250 images.
- **Unsloth** now supports Wan 2.2 MoE fine-tuning.

---

## 4. THE VOICE — Text-to-Speech

### Primary

**Qwen3-TTS** — Full family just open-sourced
- 5 models (0.6B and 1.8B), 10 languages.
- VoiceDesign, CustomVoice, and Base variants.
- 97ms latency. OpenAI-compatible API.
- Voice direction: "Say this nervously." Voice cloning from 3-second samples.
- VoiceDesign: describe a voice and it creates one from scratch.

### Maximum Realism

**IndexTTS2** — Emotion cloning
- Clone both voice AND emotional delivery from separate references.
- Most realistic local TTS for critical scenes. Heavier, slower.

### Lightweight

**Chatterbox 2** — Voice cloning, works even on CPU
- One person runs it on an i5-8500 with no GPU. ~2 min per 75-word clip.
- Fast on GPU. Good voice cloning from short samples.

### Tiny

**Pocket TTS (Kyutai)** — 100M params
- Barely uses resources. Could run on a phone.

---

## 5. THE EARS — Video Audio (Environmental)

### Primary

**MMAudio** (CVPR 2025)
- Synchronized environmental audio from video analysis.
- Footsteps, doors, rain, engines, crowds timed to on-screen events.
- ~8GB VRAM. ComfyUI node: kijai/ComfyUI-MMAudio.

### Alternative

**LTX-2 native audio** — Convenient (one model, one output) but less controllable and English-only quality.

---

## 6. THE MOUTH — Lip-Synced Talking Video

### Best Quality

**HuMo** (ByteDance, 17B)
- Audio-driven face animation. "Looks way better than InfiniteTalk, especially facial emotion and lip movements fitting speech." (280 upvotes)
- 1.7B version: 24-32GB VRAM. Full 17B at 720p: ~96GB (4x24GB multi-GPU or single large GPU). Kijai building ComfyUI integration.
- Currently limited to short clips (3-4 sec per gen).

### Most Mature

**InfiniteTalk** (Wan 2.2 + TTS pipeline)
- One practitioner making full AI sci-fi series with it ("Stellarchive," 798 upvotes).
- ~1 in 10 generations has good lip sync. Cherry-picking required.
- ~33 sec per 1 sec of video on RTX 3090.

### Lightest

**MultiTalk LoRA** (for Wan 2.2)
- LoRA adding lip-sync to standard Wan workflows.
- Used in 8-step production pipeline (274 upvotes).

---

## 7. THE LENS — Vision/Understanding

**Qwen2.5-VL** / **Qwen3-VL**
- Best open vision model. Images, documents, charts, screenshots.
- Through Ollama alongside your LLM.

---

## THE FULL STACK AT A GLANCE

**Always loaded (daily drivers):**
- GPT-OSS-120B or Qwen3-Coder-Next — instant chat, code, quick tasks
- Qwen3-TTS — voice output

**On demand (creative):**
- Kimi K2-0905 — screenwriting, dialogue (THE creative partner)
- DeepSeek V3.2 — hardest reasoning tasks

**On demand (generation):**
- Flux 2 Dev / Z-Image → SeedVR2 — image pipeline
- Wan 2.2 14B — video generation
- HuMo — lip-sync
- MMAudio — video environmental audio

**The dialogue-in-video workflow:**
1. Write script → Kimi K2-0905 (best creative writing, uncensored)
2. Generate voice → Qwen3-TTS (emotionally directed, voice cloned)
3. Animate talking head → HuMo (best quality) or InfiniteTalk (batch volume)
4. Add environmental audio → MMAudio (synchronized)
5. Composite → ffmpeg

**The client image workflow:**
1. Generate → Flux 2 Dev (non-human) or Z-Image (human subjects)
2. Upscale → SeedVR2
3. Edit → HiDream-E1.1
4. Brand consistency → Flux 2 Dev multi-reference + LoRA

---

## ADDITIONAL MODELS INVESTIGATED

These came up in deep research but don't change the core recommendations:

**LLMs:**
- **Mistral Medium 3.1** — Scored 8.201 on writing benchmark (#10 overall, above Claude Opus 4.5). Need to verify if open-weight or API-only. If open, it slots in above MiniMax M2.1 for creative work.
- **Mistral Large 3** — Scored 7.595. Likely API-only.
- **Nemotron 3 Nano 30B** — NVIDIA's open MoE (30B/3B active). Practitioners found it "benchmaxxed" — hallucinates NeMo Evaluator answer formats in plain API calls, premature EOS 71% of the time. Tool calling broken. Skip for now.
- **GPT-OSS-20B** — Smaller sibling of 120B. Runs on potato hardware (10 tok/s on an i3-8145U). Great for lightweight/mobile use.
- **Cohere Command A** — Scored 6.794 on writing. Below the useful threshold.
- **Grok 4.1** — Scored 7.567 on writing. Not open-weight.
- **Baidu Ernie 4.5 300B A47B** — Already in main list. Scored 7.506, above GLM-4.6. Worth testing.

**Image:**
- **Klein 9B base** — Image model. Z-Image practitioner tested it alongside Z-Image, SDXL, Chroma. Z-Image won for realism. Klein 9B is decent but outclassed.
- **Chroma** — Another image model. Outclassed by Z-Image and Flux 2 Dev.
- **ZIT (Z-Image Turbo)** — Distilled version of Z-Image. Scored 41/60 in comparison. Faster but less variety, worse prompt adherence. Use the base model instead.
- **Flux 2 4B** — Smallest Flux. Only advantage is speed/size. 28/60 score. Skip for professional work.
- **Flux 1 Krea** — Best of prior generation (32/60). Surprisingly good anatomy but weak language understanding.
- **bigASP v2.5** — SDXL finetune with flow matching, 13M images, 150M samples. Community-trained. The creator also makes **JoyCaption** (training image auto-captioner). Niche but shows SDXL isn't dead for specialized use.

**TTS:**
- **Chatterbox optimized** — Community member tripled inference speed with torch.compile + bfloat16. Gets 101+ it/s on RTX 3090 (from ~38). Only 2.5GB VRAM. Actively maintained via TTS-WebUI.
- **VibeVoice Large** — Mentioned in earlier research. Less community traction than Qwen3-TTS and Chatterbox.

**Tools:**
- **Unsloth** — Now supports fast MoE fine-tuning for GPT-OSS, Qwen3, DeepSeek, GLM. 12x faster, 35% less VRAM. Critical for LoRA training.
- **Heretic** — Abliteration tool. Has a "slop reduction" mode that removes AI-ish prose style. Could be applied to any model.
- **JoyCaption** — Auto-caption training images for LoRA datasets. By the bigASP creator.

**Architecture Research (Bleeding Edge):**
- **Superlinear** — New subquadratic attention (O(L^1.5) vs O(L^2)). 1M-10M token context on single GPU. 76 tok/s decode at 10M context. Currently research-grade, not production. Could be huge for screenwriting (entire scripts in context).
- **LingBot-World** — Open-source world model outperforming Google's Genie 3. Real-time interactive environments at 16fps with 60-second spatial memory. Could be relevant for future interactive content.

**Confirmed Coming Soon:**
- **Qwen 3.5** — PR opened on GitHub. If Qwen3-Coder-Next is a preview of where Qwen is heading, 3.5 could shake up the rankings significantly.
- **HuMo ComfyUI integration** — kijai actively building it. Will make the best lip-sync model accessible through the standard ComfyUI workflow.

---

## WHAT I EXCLUDED (AND WHY)

- **Llama 4 Maverick** — Dead last on creative writing (5.777). Personality research confirms it's the most constrained model (4/7 behavioral axes in "weak zone"). Skip.
- **Hermes 4 405B** — $17K build guy was "so underwhelmed by the response quality I forgot to record." Also 3.52 tok/s. Skip.
- **Stable Diffusion 3.5** — Outclassed by Z-Image and Flux 2 for professional work.
- **Qwen-Image** (original, not 2512) — Outclassed by the newer Qwen 2512.
- **GLM-4.5** — Scores 7.120 on writing, outclassed by 4.6 (7.452) and 4.7.

## MODELS TO WATCH

- **Qwen 3.5** — PR opened on GitHub. Coming soon. If Qwen3-Coder-Next is a preview, this should be major.
- **LingBot-World** — Open-source world model that outperforms Google's Genie 3. Real-time interactive environments. Could be relevant for future video work.
- **Superlinear** — New subquadratic attention architecture. 1M-10M token context on single GPU. Architecture research, not production yet, but could change everything for long-form screenwriting.

---

*Research sourced from: Lechmazur Creative Writing Benchmark V4 (github.com/lechmazur/writing), SWE-rebench December 2025 leaderboard, practitioner comparison of 8 image models (r/StableDiffusion), community reviews on r/LocalLLaMA and r/StableDiffusion (upvote-weighted), hardware benchmark posts from verified builds. February 2025.*
