# C41 Cinema — Local AI Stack (2025 Edition)

Research and architecture docs for building a fully local AI production pipeline. Zero cloud subscriptions.

---

## 🎯 Quick Reference for AI Agents

| Document | What's Inside |
|----------|---------------|
| `c41-model-stack-2025.md` | Curated 2025 model recommendations |
| `c41-full-local-stack.md` | Complete hardware/software architecture |
| `c41-hardware-research-2025.md` | GPU and hardware options |
| `deep-practitioner-research.md` | Real user experiences from Reddit/GitHub |
| `openclaw-local-setup-plan.md` | OpenClaw integration strategy |

---

## Key Documents

### Core Stack
- **[c41-model-stack-2025.md](c41-model-stack-2025.md)** — Verified model picks across LLM, image, video, audio, TTS for 2025
- **[c41-full-local-stack.md](c41-full-local-stack.md)** — Hardware architecture, routing, cost breakdown
- **[c41-hardware-research-2025.md](c41-hardware-research-2025.md)** — Latest GPU recommendations and pricing

### Research & Validation
- **[deep-practitioner-research.md](deep-practitioner-research.md)** — Real-world practitioner quotes and findings
- **[agency-ai-tools-research.md](agency-ai-tools-research.md)** — AI tools for agency workflows

### Business
- **[c41-investment-pitch.md](c41-investment-pitch.md)** — Investment pitch for local AI setup
- **[pitch-deck.html](pitch-deck.html)** — HTML pitch deck presentation

### Integration
- **[openclaw-local-setup-plan.md](openclaw-local-setup-plan.md)** — Integration plan for OpenClaw orchestration

---

## 2025 Model Highlights

### LLMs (Mac Studio M3 Ultra 512GB)
| Model | Params | VRAM | Speed | Best For |
|-------|--------|------|-------|----------|
| Kimi K2-0905 | ~1T MoE | ~500GB | 10-15 t/s | Creative writing |
| MiniMax M2.1 | 230B MoE | ~187GB | 38-42 t/s | Balanced daily driver |
| GPT-OSS-120B | 120B MoE | 61GB | ~80 t/s | Fast tasks, tool use |

### Image Generation (RTX PRO 6000 96GB)
| Model | VRAM | Best For |
|-------|------|----------|
| Flux 2 Dev | 45GB @ FP8 | Prompt adherence, text rendering |
| Z-Image | 16-24GB | Photorealism, human subjects |
| HiDream-E1.1 | 16-24GB | Detail editing via natural language |

### Video Generation
| Model | VRAM | Best For |
|-------|------|----------|
| Wan 2.2 14B | 24-48GB | Commercial I2V, lip-sync |
| FramePack | 6GB+ | Long-form, constant VRAM |
| HuMo | 68GB | Best lip-sync quality |

---

## Two-Machine Architecture

```
┌─────────────────────────────┐         ┌─────────────────────────────┐
│      MAC STUDIO M3 ULTRA    │◄───────►│   NVIDIA WORKSTATION        │
│         512GB RAM           │ Network │  Threadripper PRO 7975WX    │
│                             │         │   RTX PRO 6000 96GB        │
│  • OpenClaw (control plane) │         │                             │
│  • Ollama (all LLMs)        │         │  • ComfyUI (generation)    │
│  • Qwen3-TTS server         │         │  • Wan 2.2 workflows       │
│  • ffmpeg/light compute     │         │  • LoRA training           │
│                             │         │  • Video upscaling         │
└─────────────────────────────┘         └─────────────────────────────┘
              │                                    │
              └──────────────┬─────────────────────┘
                             │
                        You (Discord)
```

**Why This Split:**
- Mac: Best inference-per-watt for LLMs, unified memory, always-on
- PC: Required for CUDA generation, training, heavy GPU workloads

---

## For AI Agents: Working with These Docs

1. **No executable code here** - This is planning/research only
2. **Reference for prompts** - Use model specs when helping choose tools
3. **Architecture context** - Understand the dual-machine setup
4. **OpenClaw integration** - All tools routed through Discord interface

---

## Investment Summary

| Component | Cost | Purpose |
|-----------|------|---------|
| Mac Studio M3 Ultra 512GB | ~$9,500 | LLM inference, orchestration |
| NVIDIA Workstation | ~$16,000 | Generation, training |
| **Total** | **~$25,500** | Complete local AI studio |

ROI: Pays for itself vs cloud costs in 6-12 months at C41's usage level.

---

## Questions for Kyle

1. Is the Mac Studio purchased?
2. Timeline for PC build?
3. Should I prioritize OpenClaw skills for LLM or image generation first?
4. Any specific client projects driving the timeline?

---

*2025 research for C41 Cinema's zero-cloud AI infrastructure.*
