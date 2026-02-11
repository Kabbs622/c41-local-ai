# C41 Cinema: Hardware Research & Architecture (February 2025)

## Final Architecture: Two-Box Setup

After extensive research into real-world builds, practitioner benchmarks, and community feedback from r/LocalLLaMA, the optimal architecture for C41 Cinema is a **two-machine setup**: a Mac Studio for always-on LLM inference and an NVIDIA workstation for AI content generation.

### Why Two Boxes?

- The LLM (chat, writing, coding, agents) should **never go down**. If the NVIDIA box is crunching a video gen job, your AI assistant stays responsive.
- LLM inference benefits from unified memory (Mac). Gen work benefits from CUDA (NVIDIA). Each machine plays to its strengths.
- Multi-user support: two people can use the LLM simultaneously while gen jobs run independently.
- Clean separation of concerns. No resource conflicts.

---

## Machine 1: Mac Studio M3 Ultra 512GB (LLM Server)

**Purpose:** Always-on AI assistant for LLM chat, creative writing, coding, reasoning, agents.

**Specs:**
- Apple M3 Ultra: 32-core CPU, 80-core GPU
- 512GB unified memory
- 819 GB/s memory bandwidth
- Thunderbolt 5 connectivity
- ~100W idle power draw
- Silent operation

**Estimated cost:** ~$9,500 (base $3,999 + $1,500 CPU/GPU upgrade + $4,000 memory upgrade)

**What it runs:**
- MiniMax M2.1 (7.777 creative writing score): 38-42 tok/s at 4K context, 30 tok/s at 16K context (verified on exact hardware)
- GPT-OSS-120B (fast daily driver): ~80 tok/s single user (verified on M2 Ultra, M3 Ultra similar bandwidth)
- Qwen3-Coder-Next (coding): excellent speed
- Any future model up to ~400GB
- Kimi K2.5 (~350GB): fits but slower (~8-12 tok/s estimated). Acceptable for non-interactive use.

**Multi-user capability:** Two simultaneous users see moderate speed degradation. MiniMax M2.1 benchmark: single user 42 tok/s drops to ~25-30 tok/s each with two users (based on MoE batched inference characteristics). Both users get separate accounts, separate chat histories, and separate AI personalities through Open WebUI.

**Why Mac?** Apple Silicon's unified memory architecture lets the CPU and GPU share the same 512GB memory pool. No other consumer platform offers 512GB of GPU-accessible memory in a silent, compact form factor.

---

## Machine 2: NVIDIA AI Workstation (Gen Server)

**Purpose:** AI image generation, video generation, audio generation, lip-sync, voice synthesis.

**Specs:**
- AMD Threadripper PRO 7975WX (32-core)
- ASUS Pro WS WRX90E-SAGE SE motherboard (7x PCIe x16 slots)
- 64GB DDR5 ECC RAM
- 1x NVIDIA RTX PRO 6000 Blackwell 96GB
- 1600W 80+ Titanium PSU
- 4TB NVMe SSD
- Rack-mountable or quiet tower case

**Estimated cost:** ~$16,000 (CPU ~$3,735 + mobo ~$1,291 + RAM ~$400 + GPU ~$8,500 + case/PSU/storage ~$2,000)

**What it runs (all on one 96GB GPU):**
- Flux 2 Dev (image gen, non-human subjects): 90GB full precision, ~32GB quantized (fits 96GB GPU)
- Z-Image (image gen, human subjects): fits easily
- Wan 2.2 (video gen): ~40-60GB depending on resolution
- HuMO lip-sync (24-32GB for 1.7B model, full 17B at 720p needs ~96GB): fits in 96GB
- MMAudio (video audio/SFX): small footprint
- Qwen3-TTS (voice synthesis): small footprint
- ComfyUI orchestrates all of the above

**Why Threadripper?**
- Future-proof platform: 7 PCIe x16 slots means adding a second (or third) RTX PRO 6000 later is trivial
- 1600W PSU has headroom for additional GPUs
- Workstation-grade reliability for 24/7 operation
- 32-core CPU matches the Mac Studio's core count

**Why RTX PRO 6000?**
- 96GB GDDR7 VRAM: every gen model fits in a single GPU
- Outperforms the H100 on single-GPU inference (3,140 vs 2,987 tok/s in benchmarks)
- Full CUDA ecosystem: ComfyUI, PyTorch, vLLM all optimized for NVIDIA
- Blower-style cooler designed for workstation/rack environments

---

## Total Investment

| Component | Estimated Cost |
|---|---|
| Mac Studio M3 Ultra 512GB (32c CPU, 80c GPU) | ~$9,500 |
| NVIDIA Workstation (platform: CPU, mobo, RAM, case, PSU, storage) | ~$7,500 |
| RTX PRO 6000 96GB GDDR7 | ~$8,500 |
| **Total** | **~$25,500** |

---

## Existing Hardware (Not Part of Investment)

Kyle's current desktop (Ryzen 9 7950X + RTX 5090 32GB) serves as the daily driver workstation and remote access terminal into both servers.

---

## Research: Builds People Are Actually Running

### Verified Practitioner Benchmarks

**Dual RTX PRO 6000 Workstation (192GB VRAM)**
- Source: [r/LocalLLaMA](https://www.reddit.com/r/LocalLLaMA/comments/1qorbdk/)
- Team building enterprise AI workstations
- MiniMax M2.1 INT4: processes 64K tokens in ~15 seconds
- Serves 4+ simultaneous users/agents
- GPT-OSS-120B fits on single GPU with data-parallel across both
- Full vLLM setup guide: [reddit.com/r/LocalLLaMA/comments/1pvk3d9/](https://www.reddit.com/r/LocalLLaMA/comments/1pvk3d9/)

**Quiet Threadripper + RTX 5090 + 4x R9700 (160GB VRAM)**
- Source: [r/LocalLLaMA](https://www.reddit.com/r/LocalLLaMA/comments/1qkghpk/)
- 768GB DDR5, Fractal Design Define 7 XL
- DeepSeek V3.1 Terminus with 37K context: PP 151 t/s, TG 10.85 tok/s
- Builder confirmed system is quiet with power-limited GPUs

**10x GPU Build (8x 3090 + 2x 5090, 256GB VRAM)**
- Source: [r/LocalLLaMA](https://www.reddit.com/r/LocalLLaMA/comments/1qi4uj2/)
- ~$17K, built for graphic designer doing image/video gen
- Kimi K2 TQ1: 19.61 tok/s, GLM 4.6 Q4KXL: 26.61 tok/s

**4x R9700 + Threadripper 9955WX (128GB VRAM)**
- Source: [r/LocalLLaMA](https://www.reddit.com/r/LocalLLaMA/comments/1qgdb7f/)
- ~€9,800 new hardware
- GPT-OSS-120B: 97.47 tok/s, MiniMax M2.1 Q4_K_M: 34.85 tok/s
- Builder's own conclusion: "If I could do it again, I'd buy a single RTX PRO 6000 Blackwell instead."

**2x GH200 96GB (192GB HBM3)**
- Source: [r/LocalLLaMA](https://www.reddit.com/r/LocalLLaMA/comments/1qa1guo/) (706 upvotes)
- €9,000 used datacenter hardware
- MiniMax M2.1 FP8 with vLLM: 66-78 tok/s short context, 163K context window
- Requires significant technical skill to set up

**Kimi K2.5 on RTX PRO 6000 (KTransformers)**
- Source: [r/LocalLLaMA](https://www.reddit.com/r/LocalLLaMA/comments/1qriwnv/)
- 1x PRO 6000 + Epyc + 1TB DDR5: PP 497 t/s, TG 15.56 tok/s at 32K context
- 8x PRO 6000 (SGLang TP=8): TG 57.7 tok/s

**RTX PRO 6000 vs H100 Benchmark**
- Source: [r/LocalLLaMA](https://www.reddit.com/r/LocalLLaMA/comments/1p93r0w/) (58 upvotes)
- Single GPU: PRO 6000 outperforms H100 (3,140 vs 2,987 tok/s)
- At 28% lower cost

**2x M3 Ultra 512GB (Exo Cluster)**
- Source: [r/LocalLLaMA](https://www.reddit.com/r/LocalLLaMA/comments/1qpkbb4/)
- Kimi K2.5: 24 tok/s over Thunderbolt 5

---

## Hardware Options Considered and Ruled Out

| Option | Why Ruled Out |
|---|---|
| DGX Spark | 128GB max, 273 GB/s bandwidth, "disappointing" per Level1Techs |
| Framework Desktop | 128GB max, Vulkan not CUDA |
| 4x Mac Mini M4 cluster | 64GB max per Mini, TB4 = 10x slowdown |
| Multi-GPU consumer builds (4-10 GPUs) | Loud, hot, high power, complex |
| Used datacenter hardware (GH200, A100) | Requires significant expertise, no warranty, availability risk |
| 2x M3 Ultra 512GB cluster | $22K+ just for LLM, marginal speed gain for the cost |

---

## Future Expansion Path

1. **Add second RTX PRO 6000** to NVIDIA box if gen workload increases (board supports it)
2. **Upgrade Mac Studio** when M5 Ultra ships (sell M3 Ultra, keep same architecture)
3. **Add more RAM** to NVIDIA box if CPU offloading ever needed for LLMs
4. **LoRA training** on RTX PRO 6000 for client-specific styles/characters

---

## Key Software Stack

| Layer | Tool | Runs On |
|---|---|---|
| LLM Inference | Ollama / llama.cpp | Mac Studio |
| LLM Web UI | Open WebUI | Mac Studio |
| Orchestration | OpenClaw | Mac Studio (or either) |
| Image Gen | ComfyUI + Flux 2 Dev / Z-Image | NVIDIA Workstation |
| Video Gen | ComfyUI + Wan 2.2 | NVIDIA Workstation |
| Lip Sync | HuMO | NVIDIA Workstation |
| Video Audio | MMAudio | NVIDIA Workstation |
| Voice / TTS | Qwen3-TTS | NVIDIA Workstation |
