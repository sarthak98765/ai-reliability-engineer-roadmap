# AI Systems & Reliability Engineer — Roadmap

A roadmap I'm following, and my public working log against it: from Python internals and Linux up to
autonomous AI systems that can be measured, broken, and still survive production.

> *"Anyone can make an agent work once, on a laptop, on a good day. The rare engineer can tell you
> why it failed at 3 a.m., how often that failure happens, and how to make it recover by itself."*

Target level: **AI Reliability / Infrastructure Engineer** — not "I can call LLM APIs".

---

## The roadmap — 10 cycles

Each cycle ends in one flagship system rather than a pile of exercises.

| # | Focus | Capstone |
|---|---|---|
| **1** | **Engineering & Systems Foundation** | **SysProbe** — systems & performance toolkit |
| 2 | Mathematics of Modern AI | MathCore — autograd + optimisers |
| 3 | Deep Learning from First Principles | NanoTorch — NumPy DL framework |
| 4 | Transformers & Architecture Internals | Attention Lab |
| 5 | Build a Language Model End to End | NanoLM |
| 6 | GPUs, Kernels & Distributed Training | Distributed Trainer |
| 7 | Inference Engineering | NanoServe |
| 8 | Distributed Systems & Infrastructure | AI Platform |
| 9 | Agentic Systems from First Principles | AgentCore |
| 10 | Reliability, Evaluation, Security & Economics | The Flagship |

Ordering is deliberate: maths before deep learning, training before inference, agents second-to-last
(learn them early and you become a framework operator who cannot debug the layer beneath),
reliability last because it needs every other layer to already exist.

The capstones compound — the autograd engine powers the framework, the framework teaches the
architecture, the architecture becomes a trained model, which gets served, deployed, driven by an
agent runtime, and finally made trustworthy. One stack, not ten unrelated projects.

---

## Progress

| Folder | Topic | Status |
|---|---|---|
| [week1](week1/) | Python internals: object model, memory, refcounting, GC | 🟡 in progress |

🟡 in progress · ✅ done