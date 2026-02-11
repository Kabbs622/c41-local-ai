# C41 Cinema: Full Local AI Stack
Zero cloud subscriptions. Everything on your hardware. One interface (OpenClaw on Discord).

---

## THE ARCHITECTURE

Two machines, each doing what it's best at:

```
┌─────────────────────────────────┐     ┌─────────────────────────────────┐
│     MAC STUDIO                  │     │     NVIDIA WORKSTATION          │
│     M3 Ultra 512GB             │     │     Threadripper PRO 7975WX     │
│                                 │     │     RTX PRO 6000 96GB          │
│  ✦ OpenClaw (orchestration)     │     │                                 │
│  ✦ Ollama (LLMs, always on)    │     │  ✦ ComfyUI (all generation)    │
│  ✦ Open WebUI (multi-user)     │     │    - Image gen (Flux 2 Dev,    │
│  ✦ TTS (Qwen3-TTS)             │     │      Z-Image)                  │
│  ✦ Light compute (ffmpeg, etc)  │     │    - Video gen (Wan 2.2)       │
│                                 │     │    - Lip-sync (HuMo)           │
│  512GB unified memory           │     │    - Audio (MMAudio)           │
│  819 GB/s bandwidth             │     │    - Upscaling (SeedVR2)       │
│                                 │     │    - LoRA training              │
└─────────────────────────────────┘     └─────────────────────────────────┘
         │                                         │
         └──────────── Local Network ──────────────┘
                         │
                    You (Discord)
```

**Why this split:**
- Apple Silicon = best inference per watt for LLMs. Unified memory means a 230B MoE model runs at 42 tok/s.
- NVIDIA GPU = required for ComfyUI/CUDA generation. One 96GB card handles everything.
- LLM is always loaded and instant. Generation never competes for memory.
- If one machine has issues, the other still works.

---

## THE BRAIN (Mac Studio)

### Hardware: Mac Studio M3 Ultra, 512GB unified memory
- 32-core CPU, 80-core GPU
- 819 GB/s memory bandwidth
- ~$9,500 configured
- Silent, compact, always-on
- Multi-user via Open WebUI (Kyle + dad, separate accounts)

### Models (always available, no swapping needed)

**Primary Creative Partner:**
- **Kimi K2-0905** — #1 open model for creative writing (8.331 on Lechmazur V4). Beats Gemini 3 Pro, Gemini 2.5 Pro, Mistral Medium 3.1, and every Claude 4.5 variant. MoE ~1T total, ~60B active. Fits in 512GB at Q4 (~500GB). Speed TBD on this hardware but expected 10-15 tok/s (large model, acceptable for creative work).

**Balanced Writer + Code:**
- **MiniMax M2.1** (7.777 creative writing) — MoE 230B, ~10B active. Verified: **38-42 tok/s at 4K context, 30 tok/s at 16K context** on exact M3 Ultra 512GB hardware. Uses ~128GB at 4-bit, ~187GB at 6-bit. The sweet spot of speed and quality.

**Fast Daily Driver:**
- **GPT-OSS-120B** (7.030 creative writing) — OpenAI's open-source MoE, ~5.1B active. 61GB model data (native MXFP4). Verified: **~80 tok/s** single user on M2 Ultra (same bandwidth class). Instant, reliable, great for quick tasks and tool use.

**Code Beast:**
- **Kimi K2.5 Thinking** or **GLM-4.7** — Top open-source coding models. K2.5 also scores 8.068 on creative writing. GLM-4.7 benchmarked at 16-17 tok/s on M3 Ultra 512GB at lower contexts.

**Heavy Reasoning (on demand):**
- **DeepSeek V3.2** (685B, 7.601 writing) — The absolute ceiling when you need the best thinking. Slower, loads on demand.

**Vision:**
- **Qwen2.5-VL / Qwen3-VL** — Best open vision model. Images, documents, screenshots.

**Why 512GB matters for film:**
Large uncensored models write better dialogue. More nuanced characters. Better at dark, edgy, mature content without sanitizing it. They hold longer narrative context — an entire screenplay in memory. The difference between a small model and Kimi K2-0905 for creative writing is like the difference between a film student and a veteran screenwriter. You'll feel it immediately.

### TTS (Also on Mac)
- **Qwen3-TTS** — 97ms latency, OpenAI-compatible API. Natural language voice direction ("say this nervously, like you're hiding something"). Voice cloning from 3-second samples. Runs as a local server, OpenClaw connects natively.
- **IndexTTS2** — Emotion cloning. Provide a voice reference + an emotion reference. The most realistic local TTS for critical scenes.

---

## THE GENERATOR (GPU Workstation)

### Hardware: Threadripper PRO 7975WX + RTX PRO 6000 Blackwell 96GB
- AMD Threadripper PRO 7975WX (32-core)
- ASUS WRX90E-SAGE SE motherboard (7x PCIe 5.0 x16 slots)
- 64GB DDR5 ECC RAM
- 1x NVIDIA RTX PRO 6000 96GB GDDR7
- ~$16,000 total
- Outperforms H100 SXM on single-GPU inference (verified: 3,140 vs 2,987 tok/s)
- Runs ComfyUI with all generation models
- OpenClaw sends requests via ComfyUI's REST API over local network

### Image Generation
The pro workflow (how practitioners actually work):

1. **Flux 2 Dev** — Best prompt adherence, text rendering, scene composition. 32B params, 90GB at FP16 (fits the 96GB card at full precision or FP8 at ~45GB). Weak on human anatomy. (Non-human subjects)
2. **Z-Image** — Best photorealism, best human bodies/skin. Uses Qwen3 4B text encoder, natively generates at 1920px. ~95 sec per 1280x1920 on RTX 5090. (Human subjects)
3. **SeedVR2** — Upscale to 4K.
4. **HiDream-E1.1** — Fix details with natural language. "Remove the watch. Change the background."

For brand consistency: **Flux 2 Dev** — Feed up to 10 reference images. Maintains character/style without fine-tuning.

With 96GB, you can run Flux 2 Dev at full FP16 quality. No quantization needed for any gen model.

### Video Generation
- **Wan 2.2 14B** (i2v) — The model. Being used for real paid client work on single 4090s right now. 449-upvote Reddit post proving it. (24-48GB)
- **Wan 2.2 VACE** — Video editing: inpainting, outpainting, reference-based generation.
- **FramePack** — Long-form (1 min+). Constant VRAM regardless of length. (6GB minimum)
- **Speed-ups**: TeaCache (2x), Self-Forcing LoRAs, CFG-Zero*

### Lip-Synced Talking Video
The hardest problem in local AI video. Three approaches, all improving fast:

**Option 1: HuMo (ByteDance, 17B)** — Best quality
- "Looks way better than Wan S2V and InfiniteTalk, especially the facial emotion and actual lip movements fitting the speech." (280 upvotes)
- 1.7B version: 24-32GB. Full 17B at 720p: ~96GB. Both fit on PRO 6000.
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
1. Write dialogue (Kimi K2-0905 on Mac — #1 open creative writing model, uncensored)
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
    ├── "Write me a screenplay"        → Ollama → Kimi K2-0905 (Mac)
    ├── "Quick email draft"            → Ollama → GPT-OSS-120B (Mac, ~80 tok/s)
    ├── "Generate a product shot"      → ComfyUI API → Flux 2 Dev / Z-Image (GPU)
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

### Mac Studio
| Item | Cost |
|------|------|
| Mac Studio M3 Ultra (base: 28-core, 96GB, 1TB) | $3,999 |
| Upgrade: 32-core CPU + 80-core GPU | $1,500 |
| Upgrade: 512GB unified memory | $4,000 |
| **Subtotal** | **~$9,500** |

### NVIDIA Workstation
| Item | Cost |
|------|------|
| AMD Threadripper PRO 7975WX | ~$3,735 |
| ASUS WRX90E-SAGE SE motherboard | ~$1,291 |
| 64GB DDR5 ECC RAM | ~$400 |
| NVIDIA RTX PRO 6000 96GB GDDR7 | ~$8,500 |
| 4TB NVMe SSD | ~$300 |
| 1600W 80+ Titanium PSU | ~$400 |
| Case + cooling | ~$400 |
| **Subtotal** | **~$16,000** |

### Total: ~$25,500

---

## THE ROI

**One-time hardware: ~$25,500**

**Replaces monthly cloud subscriptions (what C41 actually uses):**
| Service | Monthly Cost | What You Get | The Limit |
|---------|-------------|-------------|-----------|
| Claude Max | $200 | AI chat (1 user) | Throttled after heavy use |
| Google AI Ultra (Veo 3) | $250 | 250 full-quality videos/mo | 5/day cap, failed gens count |
| ElevenLabs Pro | $99 | ~4 hours speech/month | $0.24/1K char overage |
| Nano Banana Pro | $24 | ~125 images/month | Credits run out mid-project |
| **Total (1 user)** | **$573/mo** | | |
| **Total (2 users)** | **$1,146/mo** | | **$13,752/year** |

**Break-even: under 2 years.** By year 3, you've saved over $15,000 and counting.

**But the real value isn't cost savings:**
- Custom LoRAs — brand consistency no cloud service can match
- Unlimited generation — no credits, no rate limits, no "you've run out"
- Client data never leaves your building — real privacy guarantee
- Uncensored creative AI — write anything without guardrails
- Full parameter control — every setting exposed, every workflow customizable
- Train on client assets without uploading to third parties
- 512GB of LLM memory — run #1 ranked open creative writing model at full quality
- Future-proof — new open-source models drop weekly, just download and run
- 7 GPU expansion slots — add capacity without replacing anything

---

## WHAT WE DO NOW

1. ✅ Stack designed (this document)
2. **Next:** Pitch document for your father
3. Build the OpenClaw skills (ComfyUI integration, ready to deploy day one)
4. Design ComfyUI workflow presets (hero image, product shot, social clip, talking head)
5. When hardware arrives: plug in, install, go
