# C41 Cinema: Hardware Research
Fresh research, February 2025. Starting from scratch — what's the best hardware to run the full model stack?

---

## THE PROBLEM

You need to run two fundamentally different workloads:

**1. LLM Inference** — Large language models (up to ~1T parameter MoE). Needs massive memory capacity and bandwidth. Doesn't need CUDA.

**2. Content Generation** — Image gen (Flux 2 Dev, Z-Image), video gen (Wan 2.2), lip-sync (HuMo), audio (MMAudio), TTS. Needs CUDA cores, tensor cores, and fast VRAM. These are all NVIDIA-dependent — ComfyUI, diffusers, PyTorch all run on CUDA.

These two workloads have completely different hardware requirements. Trying to run both on the same machine means compromise. The smart move is **two machines, each optimized for its job**.

---

## THE HARDWARE LANDSCAPE (Feb 2025)

### Option A: Mac Studio M4 Ultra (for LLMs)

**Specs:**
- M4 Ultra: 32-core CPU, 80-core GPU
- Up to 512GB unified memory
- Memory bandwidth: ~819 GB/s
- Thunderbolt 5 (120 Gbps) for clustering
- Power draw: ~150W under load
- Price: ~$11,999 (512GB / 8TB config)

**What it's good at:**
- Massive unified memory means you can load models that won't fit in any GPU VRAM
- Kimi K2-0905 (~1T MoE, ~350GB quantized) fits in a single 512GB machine
- DeepSeek V3.2 (685B) at FP16 needs ~1.3TB — fits in a 2-machine cluster
- Qwen3-235B at full FP16 (~470GB) fits in a single machine
- GPT-OSS-120B (64GB native 4-bit) runs at ~97 tok/s — very fast daily driver
- Quiet, low power, zero driver issues, works out of the box
- MLX framework is mature and fast for inference

**What it's not good at:**
- No CUDA. Cannot run ComfyUI, Stable Diffusion, or any diffusion-based generation
- Slower per-token than equivalent NVIDIA VRAM (when model fits in VRAM)
- Can't fine-tune or train models
- Expensive per GB of memory vs. DDR4/DDR5 system RAM

**Real-world performance (from r/LocalLLaMA practitioners):**
- 2x Mac Studio M3 Ultra 512GB running Kimi K2.5: **24 tok/s** (verified post, upvoted)
- Single Mac Studio M4 Ultra 512GB on Qwen3-235B Q4: ~15 tok/s
- 4x Mac Studio cluster on Kimi K2 Thinking: ~28 tok/s
- M4 generation has 4x faster prompt processing than M3 (Apple fixed interconnect)
- A llama.cpp developer with an M3 Ultra said they're considering clustering with an M5 when it comes out

**Community consensus:** Mac Studio is the king of "load giant models and just use them." If you don't need CUDA, it's the best dollar-per-useful-GB of memory you can buy, and it Just Works.

---

### Option B: NVIDIA RTX PRO 6000 Blackwell (for Generation)

**Specs:**
- 96GB GDDR7 VRAM per card
- Blackwell architecture, 5th gen Tensor Cores
- PCIe Gen5 x16
- FP4/FP8 native support
- Price: ~$6,800-7,500 per card (estimated street price)
- Power: ~350W TDP per card

**What it's good at:**
- CUDA ecosystem — runs everything: ComfyUI, diffusers, PyTorch, all gen models
- 96GB VRAM is enough for every generation model simultaneously
- Flux 2 Dev, Wan 2.2, Z-Image, HuMo, MMAudio all fit comfortably
- Tensor cores accelerate diffusion model inference dramatically
- Can also run LLMs (but limited to what fits in VRAM)
- LoRA training for client-specific models
- Native FP4 quantization on hardware

**What it's not good at:**
- 96GB is not enough for the large creative writing LLMs (Kimi K2-0905 at ~350GB)
- Two cards = 192GB VRAM, still not enough for the biggest models
- Expensive per GB compared to Mac unified memory
- You'd need CPU RAM offloading for large LLMs, which tanks speed
- Power hungry, needs beefy PSU, loud under load

**Real-world context:**
- The Reddit poster planning 2x RTX PRO 6000 + 1TB DDR4 for Kimi K2.5 estimated 12-22 tok/s at 16K context — but that's with heavy CPU offloading. Mac cluster at 24 tok/s beats it with better latency consistency.
- For generation workloads (ComfyUI), nothing touches NVIDIA. Period.
- One RTX PRO 6000 is more than enough for all generation tasks. You'd only need two if you want to run a large LLM AND generate simultaneously.

---

### Option C: NVIDIA DGX Spark

**Specs:**
- GB10 Grace Blackwell Superchip
- 128GB unified LPDDR5x memory
- Memory bandwidth: 273 GB/s
- 1 PFLOP FP4 performance
- Price: ~$3,000
- Power: ~140W TDP

**Why it doesn't work for you:**
- 128GB is not enough for your model stack — Kimi K2-0905 alone needs ~350GB
- Memory bandwidth (273 GB/s) is much worse than Mac Studio (819 GB/s)
- Community consensus: "disappointing inference performance" (Level1Techs review)
- Can't run ComfyUI or generation workloads (ARM Linux, limited CUDA ecosystem maturity)
- Would need multiple units plus linking to match a single Mac Studio
- The DGX Spark is designed for prototyping models up to 200B, not running 1T MoE monsters

**Verdict: Skip it.** It's a developer toy, not a production inference machine at your scale.

---

### Option D: Framework Desktop (AMD)

**Specs:**
- AMD Strix Halo APU
- Up to 128GB LPDDR5x unified memory
- ~256 GB/s memory bandwidth
- Price: ~$2,500-3,000

**Why it doesn't work for you:**
- 128GB max memory — same problem as DGX Spark
- Vulkan, not CUDA — most AI tools don't support it or support it poorly
- No ComfyUI support (needs CUDA)
- Interesting for hobbyists, not for your production stack

**Verdict: Skip it.**

---

### Option E: Multi-GPU PC Workstation (for LLMs via CPU offload)

The approach from the Reddit poster: 1TB DDR4 system RAM + 2x RTX PRO 6000, loading model weights into CPU RAM and using GPUs as a fast cache.

**Specs (estimated build):**
- Threadripper PRO or Xeon W with 1TB DDR4/DDR5 ECC
- 2x RTX PRO 6000 (192GB VRAM total)
- PCIe Gen4/5 x16 slots
- Price: ~$20,000-25,000 total
- Power: ~1,000W under load

**Pros:**
- Single machine does both LLM inference AND CUDA generation
- 1TB system RAM holds any model
- 192GB VRAM provides fast cache for hot MoE experts
- Can run ComfyUI on the same GPUs

**Cons:**
- LLM performance tanks when model doesn't fit in VRAM — constant PCIe transfers
- 12-22 tok/s estimated for Kimi K2.5 (vs. 24 tok/s on Mac cluster that costs less)
- DDR4 bandwidth (~50 GB/s) is the bottleneck, not GPU compute
- GPU memory is split between generation and LLM workloads
- Complex build — driver issues, IOMMU, NCCL, ACS settings
- Loud, power hungry, heat management is real
- When generating images/video, LLM performance drops further

**Verdict: Technically works, but you're paying more for worse LLM performance, and you create resource contention between your two main workloads.**

---

## THE RECOMMENDATION: Two-Machine Architecture

### Machine 1: Mac Studio M4 Ultra 512GB — "The Brain"
**Role:** All LLM inference (creative writing, coding, daily driver, heavy reasoning)
**Price:** ~$11,999

Runs via Ollama or llama.cpp (MLX backend). All your language models live here:
- **Kimi K2-0905** (~350GB quantized) — your creative writing partner
- **Qwen3-Coder-Next** or **Kimi K2.5** — coding
- **GPT-OSS-120B** (64GB) — fast daily driver at ~97 tok/s
- **DeepSeek V3.2** at aggressive quantization — heavy reasoning
- **MiniMax M2.1** — balanced creative at 48 tok/s

The 512GB unified memory means you can have multiple models loaded and switch between them instantly. Ollama handles load/unload automatically. No model competes with generation for resources.

**Why Mac Studio over a GPU workstation for LLMs:**
- 819 GB/s memory bandwidth vs. ~50 GB/s DDR4 = 16x faster memory access
- Models that fit entirely in unified memory run at full speed
- No PCIe bottleneck — everything is on-die
- 150W vs. 1000W power draw
- Silent operation — can sit on your desk
- Zero driver issues — plug in and go

### Machine 2: GPU Workstation — "The Forge"
**Role:** All CUDA-dependent generation (image, video, audio, lip-sync, LoRA training)
**Price:** ~$8,000-10,000

A focused build: doesn't need terabytes of RAM, just fast GPU compute.

**Build:**
- CPU: AMD Ryzen 9 or Intel Core i9 (doesn't need to be server-grade)
- RAM: 64-128GB DDR5 (just for feeding the GPU, managing video files)
- GPU: 1x RTX PRO 6000 Blackwell 96GB (~$7,000)
- Storage: 2TB NVMe (models + working files)
- PSU: 850W
- Case: Mid-tower with good airflow

96GB VRAM is massive overkill for generation. For reference:
- Flux 2 Dev: ~12GB VRAM
- Wan 2.2 (video gen): ~24GB for 720p, ~40GB for 1080p
- HuMo (lip-sync): ~35GB (17B model)
- MMAudio: ~8GB
- Z-Image: ~12GB
- LoRA training: 20-40GB depending on model

You could run ALL of these simultaneously and still have VRAM headroom. The 96GB card future-proofs you for larger generation models.

**Why only 1 GPU?**
- Generation workloads don't benefit much from multi-GPU (they're batch-sequential, not parallelizable the way LLM inference is)
- 96GB is more than enough for anything current
- Saves $7K vs. buying two
- Less power, less heat, less noise

### How OpenClaw Connects Them

OpenClaw sits on the Mac Studio (or your current Mac mini) and routes requests:

```
User → OpenClaw → [LLM request?] → Ollama on Mac Studio (localhost)
                → [Image gen?]   → ComfyUI on GPU Workstation (LAN)
                → [Video gen?]   → ComfyUI on GPU Workstation (LAN)
                → [Audio gen?]   → MMAudio on GPU Workstation (LAN)
                → [TTS?]         → Qwen3-TTS on Mac Studio (lightweight)
```

OpenClaw connects to the GPU workstation over your local network. ComfyUI exposes an API, and MMAudio/other tools can be wrapped in a simple HTTP server. Custom OpenClaw skills handle the routing:

- **comfyui-image** skill: sends prompts to ComfyUI API on the workstation
- **comfyui-video** skill: same, different workflow
- **audio-gen** skill: calls MMAudio endpoint
- **lip-sync** skill: calls HuMo endpoint

The Mac Studio handles LLM inference locally (zero network latency). Generation requests go over LAN (negligible latency for batch jobs that take seconds anyway).

**The key insight:** LLM inference needs instant responses (you're chatting). Generation is batch (you wait 10-60 seconds for an image/video regardless). Network latency only matters for the chatting part — and that stays local.

---

## COST BREAKDOWN

| Item | Price |
|------|-------|
| Mac Studio M4 Ultra 512GB / 8TB | ~$11,999 |
| GPU Workstation (1x RTX PRO 6000 + build) | ~$9,500 |
| Peripherals, cables, networking | ~$500 |
| **Total** | **~$22,000** |

### vs. Previous Plan (2x Mac Studio + RTX PRO 6000)
That was ~$28-33K. This is $6-11K cheaper and actually better because:
- Single Mac Studio 512GB handles every LLM in the model stack
- Second Mac Studio was only needed if you wanted full FP16 on 685B+ dense models — but quantized versions are nearly as good
- You don't need two Mac Studios for a 2x cluster unless you're running DeepSeek V3.2 at full FP16 (which is overkill)

### vs. All-NVIDIA Approach (1TB RAM + 2x RTX PRO 6000)
That would be ~$20-25K but gives you:
- Worse LLM performance (12-22 tok/s vs. 24+ tok/s)
- Resource contention (GPU shared between LLM and gen)
- More complexity (server-grade hardware, driver issues)
- More power draw and noise

---

## FUTURE-PROOFING

**If you outgrow 512GB:**
- Add a second Mac Studio and cluster via Thunderbolt 5 (exo). Doubles memory to 1TB. Full FP16 on anything.
- Cost: another ~$12K when needed, not now.

**If you need more GPU:**
- Add a second RTX PRO 6000 to the workstation. The build should have a second PCIe x16 slot ready.
- Cost: another ~$7K when needed.

**If M5 Ultra comes out:**
- The M5 generation reportedly has 4x faster prompt processing. An M5 Ultra 512GB would be a meaningful upgrade for LLM inference speed.
- Your RTX workstation stays the same — generation hardware has a longer useful life.

**Model evolution:**
- Models are getting more efficient, not less. MoE architectures mean you activate only a fraction of total parameters. Future 2T models might still only activate 60B per token, easily handled by 512GB.
- Image/video models are also getting more efficient. The 96GB GPU card has years of headroom.

---

## SHOULD YOU WAIT?

**For the Mac Studio:** M4 Ultra is current generation and excellent. M5 Ultra is probably 12+ months away. If you're buying now, M4 Ultra is the move.

**For the GPU:** RTX PRO 6000 Blackwell is just shipping. It's the current best. No reason to wait — there's always something newer coming, and the Blackwell architecture is a significant gen-over-gen improvement.

**For models:** Models are dropping weekly. Hardware should be bought for the architecture class (MoE ~1T, diffusion gen), not specific model versions. Your hardware handles the class.

---

## TL;DR

**Buy two machines:**
1. **Mac Studio M4 Ultra 512GB** (~$12K) — runs all LLMs, TTS, orchestration
2. **GPU Workstation with 1x RTX PRO 6000** (~$9.5K) — runs all CUDA generation

**Total: ~$22K.** Cheaper than the previous plan, better performance, cleaner separation, easier to maintain.

OpenClaw orchestrates both over the network. LLMs stay local and fast. Generation goes to the GPU box. No resource contention. Each machine is optimized for its job.
