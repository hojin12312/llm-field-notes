# 모델 컨텍스트 창 참조 (로컬 전용, gitignore)

memory-bandwidth.md KV 캐시 표의 "-" 마킹 기준.
마지막 조사: 2026-04-25 (notes 전수 재검증 라운드)

---

## 컨텍스트 크기 일람

| 모델 | 최대 컨텍스트 | 256K 지원 여부 | 비고 |
|---|---|---|---|
| Llama 3.2 3B | 128K | ✗ | |
| Phi-4-mini 3.8B | 128K | ✗ | |
| Qwen3 4B/8B/14B/32B | 128K | ✗ | |
| Qwen3.5 9B | 262K | ✓ | |
| Qwen3.5 27B | 262K | ✓ | |
| Qwen3.6 27B | 262K (1M w/ YaRN) | ✓ | DeltaNet 하이브리드, HF Qwen/Qwen3.6-27B 카드 확인 (2026-04-25) |
| Phi-4 14B | 16K | ✗ | RoPE 확장 시 ~32K 가능 |
| Gemma4:31b | 256K | ✓ | Hybrid attention (SWA:Global = 5:1, 10회 반복 = 50:10 레이어) |
| Gemma4 26B-A4B | 256K | ✓ | HF google/gemma-4-26b-a4b-it 카드 확인 (2026-04-25), sliding window 1024, 30 layers |
| GLM-4.7-Flash | 128K | ✗ | |
| Nemotron 3 Nano | 1M | ✓ | Mamba-Transformer 하이브리드; Mamba 레이어는 KV 캐시 없음 |
| Nemotron 3 Super | 1M | ✓ | |
| Qwen3.5 35B-A3B | 262K | ✓ | |
| Qwen3.5 122B-A10B | 262K | ✓ | Ollama 표기 "256K" = 262,144 토큰 |
| Qwen3.6 35B-A3B | 1M | ✓ | native 262K, extendable 1M |
| Llama 3.3 70B | 128K | ✗ | |
| Llama 4 Scout | 10M | ✓ | 멀티모달 MoE, llama.com/models/llama-4 공식 (2026-04-25) |
| Mistral Small 4 | **256K** | ✓ | mistral.ai/news/mistral-small-4 공식 (262K 표기는 오류) |
| Nemotron Cascade 2 | 262K | ✓ | HF nvidia/Nemotron-Cascade-2-30B-A3B 카드 (vLLM `--max-model-len 262144`) |
| Qwen3-235B-A22B | 128K | ✗ | |
| Qwen3.5 397B-A17B | 262K | ✓ | |
| Llama 3.1 405B | 128K | ✗ | |
| **DeepSeek-V4-Pro** | **1M** | ✓ | **2026-04-24 출시, 1.6T MoE (49B active), CSA+HCA 하이브리드. KV 캐시 V3.2 대비 10%만 사용** |
| **DeepSeek-V4-Flash** | **1M** | ✓ | **2026-04-24 출시, 284B MoE (13B active), 동일 아키텍처** |
| DeepSeek-V3.1 | 128K | ✗ | MLA 압축, 직전 세대 (HF deepseek-ai/DeepSeek-V3.1 직접 확인 — model description 명시) |
| DeepSeek-R1-0528 | 128K | ✗ | MLA 압축, 직전 세대 |
| GLM-5.1 / GLM-5.1-FP8 | 203K | ✗ (203K < 256K) | MLA 압축, 754B MoE (HF zai-org/GLM-5.1) |
| GLM-4.7 | 200K | ✗ | 358B MoE, 2026-01-29 |
| GLM-4.6 | 200K | ✗ | 357B MoE (HF zai-org/GLM-4.6 직접 확인) |
| Kimi K2 / Kimi K2-Instruct | **128K** | ✗ | **HF moonshotai/Kimi-K2-Instruct 직접 확인 — Model Summary "Context Length: 128K"** |
| Kimi K2.5 (Code Preview) | 256K | ✓ | 2026-01 출시 |
| **Kimi K2.6** | **256K (262,144)** | ✓ | **2026-04-20 출시, 1T MoE (32B active, 384 experts), MLA, INT4 양자화 지원, 자동 컨텍스트 압축. 외부 출처(latent.space): "262,144 tokens, up from 256K on K2.5"** |
| MiniMax M2.5 | 198K | ✗ (198K < 256K) | 2026-02 출시 |
| MiniMax M2.7 | 205K | ✗ | 230B MoE (10B active), 2026-03-18 출시 (HF MiniMaxAI/MiniMax-M2 직접 확인 — 단, M2 페이지가 가장 가까운 1차 출처고 M2.7 직접 카드는 미확인. 외부 출처 minimax.io/news/minimax-m27-en 일치) |
| MiniMax M2 (이전 GA) | 128K | ✗ | 230B MoE (10B active), HF MiniMaxAI/MiniMax-M2 직접 확인 |

---

## KV 캐시 크기 계산 규칙

KV 캐시는 컨텍스트 길이에 **선형 비례**합니다:

```
KV(64K) = KV(32K) × 2
KV(128K) = KV(32K) × 4
KV(256K) = KV(32K) × 8
```

Q8 양자화 시 BF16의 약 절반.

MLA(Multi-head Latent Attention) 모델 — DeepSeek, GLM-5.1 — 은 표준 GQA 대비 KV 캐시가 60배 이상 작습니다.

---

## 확인 완료 항목

- Nemotron 3 Nano: 1M (Ollama 공식, NVIDIA 공식)
- Qwen3.5 122B-A10B: 262K (Ollama library, "256K" 표기 = 262,144 토큰)
- MiniMax M2.5: 198K (Ollama library)
- DeepSeek-V4-Pro/Flash: 1M (HF unsloth/DeepSeek-V4-Pro 카드 직접 확인 — Model Downloads 표 "DeepSeek-V4-Pro | 1.6T | 49B | 1M". MIT, Native FP4+FP8 Mixed, Hybrid Attention CSA+HCA, mHC, Muon optimizer. 4라운드 추가 검증 완료 2026-04-25)
- Kimi K2.6: 256K=262,144 (unsloth/Kimi-K2.6-GGUF 직접 확인 — Total 1T, Active 32B. 4라운드 추가 검증 완료 2026-04-25)
- DeepSeek-V3.1: 128K (HF deepseek-ai/DeepSeek-V3.1 직접 확인, Model Downloads 표 명시)
- Kimi K2-Instruct: 128K (HF moonshotai/Kimi-K2-Instruct 직접 확인, Model Summary 표 명시)
- Kimi K2.6 (외부 출처 보조): Marktechpost/latent.space 일관 (1T MoE / 32B active / 384 experts), Modified MIT
- MiniMax M2: 128K (HF MiniMaxAI/MiniMax-M2 직접 확인)
- GLM-4.6: 200K (HF zai-org/GLM-4.6 직접 확인, GLM-4.5의 128K에서 확장)
- GLM-5.1: 754B 파라미터 확정 (HF zai-org organization 페이지 직접 확인)
