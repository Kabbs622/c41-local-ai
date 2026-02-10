# C41 Cinema: Full Local AI Stack
Zero cloud subscriptions. Everything on your hardware. One interface (OpenClaw on Discord).

---

## THE ARCHITECTURE

Two machines, each doing what it's best at:

```
┌─────────────────────────────────┐     ┌─────────────────────────────────┐
│     MAC STUDIO CLUSTER          │     │     GPU WORKSTATION             │
│     2x M4 Ultra 512GB          │     │     RTX PRO 6000 96GB          │
│                                 │     │                                 │
│  ✦ OpenClaw (orchestration)     │     │  ✦ ComfyUI (all generation)    │
│  ✦ Ollama (LLMs, always on)    │     │    - Image gen (FLUX, Qwen)    │
│  ✦ TTS (Qwen3-TTS)             │     │    - Video gen (Wan 2.2)       │
│  ✦ Light compute (ffmpeg, etc)  │     │    - Lip-sync (HuMo, etc)     │
│                                 │     │    - Audio (MMAudio)           │
│  1TB unified memory pooled      │     │    - Upscaling (SeedVR2)      │
│  via exo for massive LLMs       │     │    - LoRA training             │
└─────────────────────────────────┘     └─────────────────────────────────┘
         │                                         │
         └──────────── Local Network ──────────────┘
                         │
                    You (Discord)
```

**Why this split:**
- Apple Silicon = best inference per watt for LLMs. Unified memory means a 235B model just works.
- NVIDIA GPU = required for ComfyUI/CUDA generation. One 96GB card handles everything.
- LLM is always loaded and instant. Generation never competes for memory.
- If one machine has issues, the other still works.

---

## THE BRAIN (Mac Studio Cluster)

### Hardware: 2x Mac Studio M4 Ultra, 512GB each
- **1TB total unified memory** pooled via [exo](https://github.com/exo-explore/exo) over Thunderbolt
- Model layers split across both machines — both compute, different layers
- Thunderbolt 5 connection keeps latency minimal

### Models (always available, no swapping needed)

**Daily Driver:**
- **Qwen3-Coder-Next** (~20GB Q4) — The local model practitioners genuinely prefer over cloud. 256K context. Code, writing, reasoning, client work. Fast responses for everyday use.

**Creative Powerhouse:**
- **Qwen3-235B full precision** (~470GB FP16) — This is why you have 1TB. No quantization, no quality loss. The model that competes with Claude Opus and GPT-5, running at full fidelity. For screenwriting, character work, complex creative projects. Uncensored fine-tunes available — your AI writing partner with zero guardrails.
- **DeepSeek V3.1** (~400GB+ FP16) — 685B params. The absolute ceiling of local AI. When you need the best reasoning possible.

**Fast/Light:**
- **Qwen3-32B** (~20GB Q4) — Quick tasks, summaries, emails. Instant responses.

**Vision:**
- **Qwen2.5-VL** — Analyze images, read documents, understand visual content.

**Why 1TB matters for film:**
Large uncensored models write better dialogue. More nuanced characters. Better at dark, edgy, mature content without sanitizing it. They hold longer narrative context — an entire screenplay in memory. The difference between a 32B and 235B model for creative writing is like the difference between a film student and a veteran screenwriter. You'll feel it immediately.

### TTS (Also on Mac)
- **Qwen3-TTS** — 97ms latency, OpenAI-compatible API. Natural language voice direction ("say this nervously, like you're hiding something"). Voice cloning from 3-second samples. Runs as a local server, OpenClaw connects natively.
- **IndexTTS2** — World-first emotion cloning. Provide a voice reference + an emotion reference. The most realistic local TTS for when you need it perfect.

---

## THE GENERATOR (GPU Workstation)

### Hardware: RTX PRO 6000 Blackwell, 96GB GDDR7
- Single card, 96GB VRAM
- Blackwell architecture (newest, most efficient)
- Runs ComfyUI with all generation models
- OpenClaw sends requests via ComfyUI's REST API over local network

### Image Generation
The pro workflow (how practitioners actually work):

1. **Qwen-Image** — Composition and prompt adherence. Best text rendering. 13 sec/image with Nunchaku. (16-24GB)
2. **Wan 2.2 14B** (1-frame, low denoise) — Feed step 1 through for photorealism. Skin, lighting, materials improve dramatically. (16GB)
3. **SeedVR2** — Upscale to 4K. (24-48GB)
4. **HiDream-E1.1** — Fix details with natural language. "Remove the watch. Change the background." (16-24GB)

For brand consistency: **Flux 2 Dev** — Feed up to 10 reference images. Maintains character/style without fine-tuning. (24GB at 4-bit)

With 96GB, you can keep multiple models loaded. No swapping for most workflows.

### Video Generation
- **Wan 2.2 14B** (i2v) — The model. Being used for real paid client work on single 4090s right now. 449-upvote Reddit post proving it. (24-48GB)
- **Wan 2.2 VACE** — Video editing: inpainting, outpainting, reference-based generation.
- **FramePack** — Long-form (1 min+). Constant VRAM regardless of length. (6GB minimum)
- **Speed-ups**: TeaCache (2x), Self-Forcing LoRAs, CFG-Zero*

### Lip-Synced Talking Video
The hardest problem in local AI video. Three approaches, all improving fast:

**Option 1: HuMo (ByteDance, 17B)** — Best quality
- "Looks way better than Wan S2V and InfiniteTalk, especially the facial emotion and actual lip movements fitting the speech." (280 upvotes)
- 68GB model — fits on the PRO 6000 with room to spare
- Kijai building ComfyUI node
- Best for: hero shots, close-ups, dialogue-driven scenes

**Option 2: InfiniteTalk (Wan 2.2 + TTS)** — Most mature
- 423-upvote workflow. Practitioner making a full AI sci-fi film series with it ("Stellarchive" on YouTube, 798-upvote post).
- About 1 in 10 generations has perfect lip sync. Cherry-picking required.
- ~33 seconds per 1 second of video on RTX 3090 (faster on PRO 6000).
- Best for: batch generation where you can pick the best take

**Option 3: MultiTalk LoRA** — Lightest weight
- Full production pipeline: SeedVR2 upscale + VACE editing + Chatterbox TTS + MultiTalk LoRA. All in ComfyUI. (274 upvotes)
- Best for: quick turnaround, lower resource usage

**The realistic dialogue workflow:**
1. Write dialogue (Qwen3-235B on Mac — uncensored, natural voice)
2. Generate voice (Qwen3-TTS or IndexTTS2 on Mac — sounds human)
3. Generate/animate the talking head (HuMo or InfiniteTalk on GPU)
4. Audio drives the animation — lip sync matches automatically
5. Composite in post (ffmpeg on Mac)

### Video Audio (Environmental)
- **MMAudio** (CVPR 2025) — Generates synchronized environmental sound from video. Footsteps, doors, rain, machinery — all timed to what's happening on screen. (~8GB)
- Combined with TTS for dialogue: MMAudio handles the world, TTS handles the voices.

### LoRA Training (The Commercial Moat)
- **musubi-tuner** — Train Wan 2.2 video LoRAs. 16GB VRAM, 3 hours for 250 images.
- **kohya-ss** — Train Flux/SDXL image LoRAs.
- **JoyCaption** — Auto-caption training images.

Train a client's brand character once, use it across ALL generation — images, video, talking heads. No cloud service offers this. This is your competitive advantage.

---

## HOW OPENCLAW MANAGES IT

```
You (Discord)
    │
    ▼
OpenClaw (Mac Studio — always on)
    │
    ├── "Write me a screenplay"        → Ollama → Qwen3-235B (Mac cluster)
    ├── "Quick email draft"            → Ollama → Qwen3-Coder-Next (Mac)
    ├── "Generate a product shot"      → ComfyUI API → GPU workstation
    ├── "Make a 5-sec video of this"   → ComfyUI API → Wan 2.2 (GPU)
    ├── "Add dialogue to this clip"    → Qwen3-TTS (Mac) + HuMo (GPU)
    ├── "Add ambient audio"            → ComfyUI API → MMAudio (GPU)
    ├── "Train a LoRA for this client" → ComfyUI API → musubi-tuner (GPU)
    ├── "Analyze this image"           → Ollama → Qwen2.5-VL (Mac)
    └── Everything else                → Built-in tools (files, browser, web)
```

One chat. No switching apps. I route to the right tool on the right machine.

**Already built into OpenClaw:**
- Ollama integration (auto-discovers models)
- TTS (points at local Qwen3-TTS server via OpenAI-compatible config)
- Discord, file management, browser, exec, web, cron, memory

**Custom Skills I'll build:**
- ComfyUI image generation skill
- ComfyUI video generation skill
- ComfyUI lip-sync/dialogue skill
- LoRA training management skill

---

## HARDWARE SUMMARY

### Mac Studio Cluster
| Item | Cost |
|------|------|
| Mac Studio M4 Ultra 512GB × 2 | ~$20,000-24,000 |
| Thunderbolt 5 cable | ~$50 |
| **Subtotal** | **~$20,000-24,000** |

### GPU Workstation
| Item | Cost |
|------|------|
| RTX PRO 6000 96GB | ~$6,500-7,000 |
| AMD Threadripper or Ryzen 9 | ~$500-800 |
| 128GB DDR5 RAM | ~$300-400 |
| 2TB NVMe SSD | ~$150-200 |
| 1000W PSU (Platinum) | ~$200-250 |
| Case + cooling | ~$200-300 |
| **Subtotal** | **~$8,000-9,000** |

### Total: ~$28,000-33,000

---

## THE ROI

**One-time hardware: ~$30K**

**Replaces monthly cloud subscriptions:**
| Service | Monthly Cost |
|---------|-------------|
| Claude Pro / ChatGPT Pro | $100-200 |
| Runway Gen-4.5 / Kling Pro | $28-150 |
| Midjourney | $10-60 |
| ElevenLabs | $5-99 |
| Stock footage/images | $50-200 |
| Cloud GPU rental (if scaling) | $100-500 |
| **Total replaced** | **$300-1,200/mo** |

**Break-even: 2-8 years** at minimum usage, **under 2 years** at production usage.

**But the real value isn't cost savings:**
- Custom LoRAs — brand consistency no cloud service can match
- Unlimited generation — no credits, no rate limits, no "you've run out"
- Client data never leaves your machine — real privacy guarantee
- Uncensored creative AI — write anything without guardrails
- Full parameter control — every setting exposed, every workflow customizable
- Train on client assets without uploading to third parties
- 1TB of LLM memory — run the absolute best models at full precision
- Future-proof — new open-source models drop weekly, just download and run

---

## WHAT WE DO NOW

1. ✅ Stack designed (this document)
2. **Next:** Pitch document for your father
3. Build the OpenClaw skills (ComfyUI integration, ready to deploy day one)
4. Design ComfyUI workflow presets (hero image, product shot, social clip, talking head)
5. When hardware arrives: plug in, install, go
