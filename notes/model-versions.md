# Reasoning 모델 버전 참조 (로컬 전용, gitignore)

강의 자료 내 모델명 최신화를 위한 참조 문서입니다.
업데이트 시 이 파일 먼저 갱신 → reasoning-models.md / memory-bandwidth.md 반영.

마지막 조사: 2026-04-25 (notes 전수 재검증 라운드)

---

## Reasoning 모델 현황

### OpenAI

| 모델명 | API ID | 컨텍스트 | Input 가격 (cached) | Output 가격 | 비고 |
|---|---|---|---|---|---|
| GPT-5.4 Pro | `gpt-5.4-pro` | 1M | $30.00/MTok (캐시 미지원) | $180.00/MTok | 최상위 reasoning, no cache discount |
| GPT-5.4 | `gpt-5.4` | 1M | $2.50/MTok ($0.25 cached) | $15.00/MTok | Reasoning none~xhigh 조절 가능, 플래그십 |
| GPT-5.4 mini | `gpt-5.4-mini` | 400K | $0.75/MTok ($0.1875 cached) | $4.50/MTok | 코딩·서브에이전트 특화 |
| GPT-5.4 nano | `gpt-5.4-nano` | 400K | $0.20/MTok ($0.05 cached) | $1.25/MTok | 고볼륨 단순 작업, MCP 지원 |

- Knowledge cutoff: 2025-08-31
- Reasoning 레벨: none / low / medium / high / xhigh (Claude budget_tokens와 같은 개념)
- o3, o4-mini는 구세대
- Regional 데이터 residency 엔드포인트는 위 가격에 10% 가산
- 출처: 2026-04-25 aipricing.guru/openai-pricing 확인 (platform.openai.com/docs/pricing 직접 fetch 차단)

### DeepSeek

| 모델명 | API ID | 출시일 | 상태 | 비고 |
|---|---|---|---|---|
| **DeepSeek-V4-Pro** | `deepseek-v4-pro` | **2026-04-24** | **Preview** | **1.6T MoE (49B active), 1M 컨텍스트, CSA+HCA 하이브리드 어텐션, FP4+FP8 mixed precision, Non-Think/High/Max 3 reasoning modes**. 가격: $0.145 input cache hit / $1.74 input cache miss / $3.48 output per 1M tokens |
| **DeepSeek-V4-Flash** | `deepseek-v4-flash` | **2026-04-24** | **Preview** | **284B MoE (13B active), 1M 컨텍스트, 동일 아키텍처**. 가격: $0.028 input cache hit / $0.14 input cache miss / $0.28 output per 1M tokens |
| DeepSeek-V3.1 | `deepseek-chat` | 2025-08 | GA (직전 세대) | 671B MoE (37B active), 128K, MLA 압축 |
| DeepSeek-R1-0528 | `deepseek-reasoner` | 2025-05-28 | GA (직전 세대) | 671B MoE (37B active), 128K, R2 미출시 |
| DeepSeek-R1 | `deepseek-reasoner` | 2025-01 | GA (직전 세대) | |

- 출처: 2026-04-25 web search ("DeepSeek V4 release"). HF deepseek-ai/DeepSeek-V4-Pro/V4-Flash. api-docs.deepseek.com/quick_start/pricing 직접 확인
- V4 시리즈 1M 컨텍스트는 V3.2 대비 단일 토큰 추론 FLOPs 27% / KV 캐시 10% 만 사용 (mHC residual + CSA/HCA)

### Google Gemini

| 모델명 | API ID | 출시일 | 상태 | 비고 |
|---|---|---|---|---|
| Gemini 2.5 Pro | `gemini-2.5-pro` | 2025-03 (GA 2025-05) | **Stable** | thinking 내장, 현재 안정 최강 |
| Gemini 2.5 Flash | `gemini-2.5-flash` | 2025-05 | Stable | thinking_budget 0~24576 조절 가능 |
| Gemini 2.5 Flash-Lite | `gemini-2.5-flash-lite` | 2025 | Stable | 가장 빠르고 저렴 |
| Gemini 3 Flash | `gemini-3-flash-preview` | 2025-12-17 | Preview | |
| **Gemini 3.1 Pro** | `gemini-3.1-pro-preview` | 2026-02-19 | **Preview** | 현재 최신, Deep Think, ARC-AGI-2 45.1% |
| Gemini 3.1 Flash-Lite | `gemini-3.1-flash-lite-preview` | 2026-03-03 | Preview | |
| Gemini 3.1 Flash Live | `gemini-3.1-flash-live-preview` | 2026-03-26 | Preview | 실시간 음성 |
| Gemini 3.1 Flash TTS | `gemini-3.1-flash-tts-preview` | 2026-04-15 | Preview | 음성 생성 |

- Gemini 3 Pro: 2026-03-09 deprecated/종료
- **가격 (2026-04-25 ai.google.dev/gemini-api/docs/pricing 직접 확인)**:
  - Gemini 3.1 Pro Preview: input $2.00 (≤200K) / $4.00 (>200K), output $12.00 (≤200K) / $18.00 (>200K) per 1M tokens
  - Gemini 3.1 Flash-Lite Preview: input $0.25 (text/image/video), $0.50 (audio) / output $1.50 per 1M
  - Gemini 2.5 Pro: input $1.25 (≤200K) / $2.50 (>200K), output $10.00 (≤200K) / $15.00 (>200K) per 1M
  - Context Caching storage: $4.50 per 1M tokens per hour

### Anthropic Claude

| 모델명 | API ID | 상태 | 비고 |
|---|---|---|---|
| Claude Opus 4.7 | `claude-opus-4-7` | GA | Extended Thinking 지원, 최상위 |
| Claude Sonnet 4.6 | `claude-sonnet-4-6` | GA | Extended Thinking 지원, 속도·성능 균형 |
| Claude Haiku 4.5 | `claude-haiku-4-5-20251001` | GA | Extended Thinking 미지원 |

---

## 로컬 오픈소스 모델 현황 (2026-04-24 조사)

### Qwen 시리즈 (Alibaba)

| 모델 | 파라미터 | 컨텍스트 | 비고 |
|---|---|---|---|
| Qwen3 4B/8B/14B/32B | dense | 128K | 기존 |
| Qwen3.5 9B | 9B dense | 262K | 2026-02 출시 |
| Qwen3.5 27B | 27B dense | 262K | 2026-02 출시 |
| Qwen3.5 35B-A3B | 35B MoE (3B active) | 262K | 2026-02 출시 |
| Qwen3.5 122B-A10B | 122B MoE (10B active) | 262K | |
| Qwen3.5 397B-A17B | 397B MoE (17B active) | 262K | |
| Qwen3.6 27B | 27B dense | 262K (최대 1M) | 2026-04-23 출시, DeltaNet 하이브리드 아키텍처 (16/64 레이어만 표준 어텐션), Apache 2.0 |
| Qwen3.6 35B-A3B | 35B MoE (3B active) | 1M | 2026-04-16 출시, 기본 thinking 모드 |

- Qwen3.5/3.6 공통: 201개 언어, Apache 2.0
- Qwen3.6 27B: DeltaNet(선형 어텐션) 레이어 다수, 표준 어텐션 레이어 16개만 → KV 캐시 대폭 감소
- Qwen3.6 35B-A3B: reasoning 기본 활성화, 1M 컨텍스트

### GLM 시리즈 (Z.AI / ZhipuAI)

| 모델 | 파라미터 | 컨텍스트 | 비고 |
|---|---|---|---|
| GLM-4.7-Flash | 30B MoE (3B active) | 128K | GGUF Q4 ~18.5 GB, Q8 ~32 GB, BF16 ~60 GB (전체 30B 가중치 포함) |
| GLM-5.1 | 754B MoE (40B active 추정) | 203K | MLA 압축, 2026-04-07 출시, MIT, HF zai-org/GLM-5.1 (2026-04-25 직접 확인). SWE-Bench Pro 58.4 (GPT-5.4 57.7, Claude Opus 4.6 57.3 초과). Z.AI API: $1.40 input / $4.40 output / $0.26 cache hit per 1M |
| GLM-5.1-FP8 | 동일 (FP8 양자화) | 203K | HF zai-org/GLM-5.1-FP8, 2026-04-07 |
| GLM-5 | 754B MoE | 203K | 2026-03 베이스 모델, GLM-5.1은 5의 post-training 업그레이드 (28% 코딩 향상) |
| GLM-4.7 | 358B MoE | 200K | HF zai-org/GLM-4.7, 2026-01-29 |
| GLM-4.6 | 357B MoE | 200K | HF zai-org/GLM-4.6, MoE active 미공개 |

- GLM-5.1: 256 routed experts + 1 shared, 8+1 active, 78 layers (Z.AI 비공식 추정 — 공식은 미공개)
- GLM-4.7-Flash: "3B active"는 추론 시 활성화 파라미터이지만 GGUF는 전체 30B 가중치를 저장함 → dense 30B 수준의 파일 크기 (이전 기록 ~10/~18 GB는 오류)

### Gemma4 시리즈 (Google)

| 모델 | 파라미터 | 컨텍스트 | 비고 |
|---|---|---|---|
| Gemma4 E2B | 2B dense | — | 엣지/모바일용 |
| Gemma4 E4B | 4B dense | — | 엣지/모바일용 |
| Gemma4 26B-A4B | 25.2B MoE (3.8B active) | 256K | **2026-04-02 출시 (blog.google/innovation-and-ai/technology/developers-tools/gemma-4/ 직접 확인 — 검색 결과 명확)**, Apache 2.0, 멀티모달; Q4 17.0 GB, Q8 26.9 GB, BF16 50.5 GB; 30 layers, sliding window 1024, 128 experts (8 active + 1 shared) |
| Gemma4 31B | 30.7B dense | 256K | 2026-04-02 출시, 멀티모달; Q4 19.6 GB, Q8 32.6 GB, BF16 61.4 GB; SWA:Global = 5:1 × 10회 반복 = 50:10 레이어 (HF config.json `layer_types` 확인) |

- 26B-A4B: 128 experts (8 active + 1 shared), sliding window + global attention 교차
- Shared KV Cache: 마지막 N 레이어가 앞 레이어의 KV 상태 재사용 → KV 효율 향상

### Mistral 시리즈 (Mistral AI)

| 모델 | 파라미터 | 컨텍스트 | 비고 |
|---|---|---|---|
| Mistral Small 4 (2603) | 119B MoE (6B active per token, 8B with embed/output layers) | **256K** (이전 262K 표기는 오류) | 2026-03-16 출시, 03-17 NVIDIA GTC 발표, Apache 2.0, 멀티모달, Q4 73.8 GB, Q8 126 GB, BF16 238 GB |

- 128 experts, 4 active per token
- Magistral(reasoning) + Pixtral(vision) + Devstral(agentic coding) 3개 모델을 단일 체크포인트로 통합 (instruct는 base)
- NVFP4 공식 버전 존재 (mistralai/Mistral-Small-4-119B-2603-NVFP4)
- 출처: mistral.ai/news/mistral-small-4 (공식), simonwillison.net 2026-03-16 보도

### Llama 시리즈 (Meta)

| 모델 | 파라미터 | 컨텍스트 | 비고 |
|---|---|---|---|
| Llama 3.2 3B | 3.2B dense | 128K | |
| Llama 3.3 70B | 70.6B dense | 128K | Q4 42.5 GB, Q8 75 GB, BF16 141 GB |
| Llama 4 Scout | 109B MoE (17B active) | 10M | 2026-04-05 출시, 16 experts; Q4 65.4 GB, Q8 115 GB, BF16 216 GB |
| Llama 4 Maverick | 402B MoE (17B active) | 1M | 2026-04-05 출시, 128 experts; Q4 243 GB, Q8 426 GB, BF16 801 GB |
| Llama 3.1 405B | 405B dense | 128K | Q4 245 GB, Q8 430 GB, BF16 810 GB |

### Qwen3-Coder (Alibaba)

| 모델 | 파라미터 | 컨텍스트 | 비고 |
|---|---|---|---|
| Qwen3-Coder-Next | ~80B MoE (3B active) | 256K | 코딩 에이전트 특화; Q4 48.7 GB, Q8 84.8 GB |

### Nemotron 시리즈 (NVIDIA)

| 모델 | 파라미터 | 아키텍처 | 비고 |
|---|---|---|---|
| Nemotron Cascade 2 | 30B (3B active) | Mamba-Transformer MoE 하이브리드 (Nano 베이스 포스트트레이닝) | Q4 24.7 GB, Q8 33.6 GB, BF16 63.2 GB, **262K 컨텍스트**, 2026-03-16 출시 (arXiv 2603.19220), **IMO 2025 + IOI 2025 + ICPC World Finals 2025 모두 gold medal-level (NVIDIA 공식)**. HF: nvidia/Nemotron-Cascade-2-30B-A3B |
| Nemotron 3 Nano | 31.6B (3.2B active) | Mamba-Transformer MoE 하이브리드 | Q4 24.7 GB, Q8 33.5 GB, **1M 컨텍스트** |
| Nemotron 3 Super | 120B (12B active) | MoE | Q4 87.0 GB, Q8 128.5 GB, SWE-bench 60.47%, 1M 컨텍스트 |

### MiniMax / Kimi 등

| 모델 | 파라미터 | 컨텍스트 | API 가격 (input/output per MTok) |
|---|---|---|---|
| MiniMax M2.5 | MoE | 198K | $0.30 / $1.20 (SWE-bench 80.2%); 2026-02 출시 |
| MiniMax M2.7 | **230B MoE (10B active)** | **205K** | $0.30 / $1.20; **2026-03-18 출시**, Q4 139 GB, Q8 243 GB, BF16 457 GB; 로컬 가능; "Self-Evolving" — RL 워크플로우 30-50% 자체 수행 가능. SWE-bench Pro 56.22% (HF MiniMaxAI/MiniMax-M2.7 직접 확인) |
| Kimi K2 (이전 GA) | ~1,000B MoE (32B active, 384 experts) | **128K** | HF moonshotai/Kimi-K2-Instruct 직접 확인 (이전 notes "262K"는 K2.6 값과 혼동) |
| Kimi K2.5 (Code Preview) | ~1,000B MoE | 256K | 2026-01 출시 |
| **Kimi K2.6** | **1T MoE (32B active, 384 experts: 8 routed + 1 shared, MLA 어텐션)** | **256K (262,144)** | **2026-04-20 출시**, Modified MIT License, HF 가중치 공개 + Ollama `:cloud` 태그, INT4 양자화 지원, 자동 컨텍스트 압축, 300 sub-agents/4,000 coordinated steps. SWE-Bench Pro 58.6 / SWE-Bench Verified 80.2 / HLE-Full 54.0 |

---

## 강의 자료 내 언급 현황

### appendix/theory/reasoning-models.md
- 도입부 모델 목록: `GPT-5.4, DeepSeek R1-0528, Gemini 2.5 Pro / 3.1 Pro(Preview), Claude Extended Thinking`
- 코드 예시 모델: `claude-opus-4-7` ✅

### appendix/hardware/memory-bandwidth.md
- 모델 표: Qwen3, Gemma4, DeepSeek-V3.1, R1-0528, Llama4 Scout 등
  (별도 갱신 필요 시 해당 파일 직접 확인)

---

## 주요 벤치마크 점수 (2026-04 조사)

경량/중형 모델이 과거 대형 모델의 성능을 따라잡는 근거로 사용. 출처는 각 모델의 HuggingFace 모델 카드, BenchLM.ai, 공식 기술 보고서.

### 2026년 경량·중형 모델 (2026-04-25 HF 모델카드 직접 검증)

| 모델 | 크기 (active) | HumanEval | LiveCodeBench v6 | SWE-bench Verified | GPQA Diamond | AIME25/26 | MMLU-Pro |
|---|---|---|---|---|---|---|---|
| Qwen3.6 27B | 27B dense | (HF 카드 미기재) | **83.9** | **77.2** | **87.8** | **94.1** (AIME26) | **86.2** |
| Qwen3.6 35B-A3B | 35B (3B) | (미기재) | (미기재) | 73.4 | 86.0 | 92.7 (AIME26) | 85.2 |
| Gemma4 26B-A4B | 25.2B (3.8B) | **78.5** (외부 비공식; tokenmix.ai/aimactgrow 등 다수 소스 일관) | **77.1** | (미기재) | **82.3** | **88.3** (AIME26 no tools) | **82.6** |
| Gemma4 31B | 30.7B dense | **81.8** (외부 비공식) | (미기재) | (미기재) | (미기재) | (미기재) | (미기재) |
| Nemotron Cascade 2 | 30B (3B) | (미기재) | **87.2** | Pass@1 49.9 / Pass@4 65.2 (notes 이전 50.2는 약간 부정확) | (미기재) | **98.6** (AIME25, tool-RL) | (미기재) |
| Mistral Small 4 | 119B (6B per token, 8B w/ embed) | **공식 페이지 미기재; notes 이전 "92.0"은 Mistral Large 3의 점수와 혼동된 오류 (3라운드 검증 결과)** | (LiveCodeBench 위에서 GPT-OSS 120B 초과 — 절대 점수 미공개) | (미기재) | (미기재) | (AIME 2025: GPT-OSS 120B 매칭/초과) | (미기재) |

#### Qwen3.6 27B vs Qwen3.5 397B-A17B 직접 비교 (Alibaba 공식 / HF 모델 카드 기준)

| 벤치마크 | Qwen3.6 27B | Qwen3.5 397B-A17B | 차이 |
|---|---|---|---|
| LiveCodeBench v6 | 83.9 | **83.6** | 27B +0.3 (사실상 동등) |
| SWE-bench Verified | 77.2 | **76.2** | 27B +1.0 |
| SWE-bench Pro | 53.5 | **50.9** | 27B +2.6 |
| SkillsBench | 48.2 | **30.0** | 27B +18.2 (가장 큰 격차) |
| Terminal-Bench 2.0 | 59.3 | **52.5** | 27B +6.8 |
| GPQA Diamond | 87.8 | **88.4** | 397B +0.6 |
| MMLU-Pro | 86.2 | **87.8** | 397B +1.6 |
| AIME26 | 94.1 | **93.3** | 27B +0.8 |

**해석**: 코딩·agentic 벤치마크에서 27B가 397B를 일관되게 앞서거나(SkillsBench는 큰 격차) 동등. 일반 지식·추론(GPQA, MMLU-Pro)에서는 397B가 약간 우위. 모든 점수는 Alibaba 자체 평가 (Qwen 자체 agent scaffold) — 외부 독립 검증은 2026-04 시점에 제한적.

추가 수치:
- **Nemotron Cascade 2**: IMO 2025 gold (35/42), IOI 2025 gold (439.28/600), **ICPC World Finals 2025 gold-level (NVIDIA 공식 research.nvidia.com/labs/nemotron/nemotron-cascade-2/ 명시: "Gold Medal-level performance in IMO, IOI, and ICPC World Finals"; 외부 출처 venturebeat/abit.ee는 "10/12 problems solved, #4 Gold placement"로 더 구체적 보고)**, HMMT Feb 2025 94.6. **3라운드 검증으로 1라운드의 "동메달 수준" 표기는 잘못된 해석으로 정정됨**
- **Qwen3.6 27B 추가** (HF 카드): SWE-bench Multilingual 71.3, Terminal-Bench 2.0 59.3, HMMT Feb 26 84.3, IMOAnswerBench 80.8, HLE 24.0, MMMU 82.9, V* 94.7

### 기준선 모델 (비교용)

| 모델 | 크기 | 출시 | HumanEval | MMLU / MMLU-Pro | LCBv6 | SWE-bench Verified | 비고 |
|---|---|---|---|---|---|---|---|
| GPT-4 (original) | ~1.8T est. | 2023-03 | ~85 | 86 (MMLU) | — | — | 당시 flagship |
| Llama 3.1 405B | 405B dense | 2024-07 | 89.0 | 87.3 (MMLU 5-shot) | — | — | 오픈소스 최대 dense |
| **Qwen3.5 397B-A17B (Reasoning)** | 397B (17B active) | 2026-02 | 84.9 | **87.8** (MMLU-Pro) | **83.6** | **76.2** | HF: Qwen/Qwen3.5-397B-A17B. 60 layers, hidden 4096, 10+1 expert routing |

### 핵심 관찰

1. **Qwen3.6 27B vs Qwen3.5 397B**: 크기 1/15. **코딩·agentic 벤치마크 5개 중 4개에서 27B가 앞섬** (LCBv6 83.9 vs 83.6, SWE-bench Verified 77.2 vs 76.2, SWE-bench Pro 53.5 vs 50.9, SkillsBench 48.2 vs 30.0, Terminal-Bench 59.3 vs 52.5). **일반 지식 벤치(GPQA, MMLU-Pro)에서는 397B가 1-2점 우위**. 따라서 "397B를 이긴 27B"라는 강한 표현은 코딩·agentic 영역에 한정해서 정확.
2. **Nemotron Cascade 2 (3B active)**: 2024 Llama 3.1 405B의 HumanEval 89.0%를 LiveCodeBench v6 87.2% + IMO/IOI 2025 gold + AIME25 98.6%로 효과적으로 넘어섬. 활성 파라미터 1/135.
3. **Gemma4 26B-A4B (3.8B active)**: HF 카드는 HumanEval 점수를 명시하지 않으나, 외부 다수 소스(tokenmix.ai, aimactgrow.com 등) 일관 보고로 26B는 78.5%, 31B dense는 81.8%로 분리해 표기. 둘 다 비공식 인용.
4. **Mistral Small 4 (6B active per token)**: 공식 발표는 "GPT-OSS 120B 초과"라고만 표기, 절대 HumanEval 점수 미공개. **3라운드 검증 결과: 이전 notes의 "92.0"은 Mistral Large 3의 점수와 혼동한 오류** (web search 결과 92%는 Mistral Large 3 / Claude 3.5 Sonnet 점수, Mistral Small 4는 "Claude Haiku 3.5 / Qwen 2.5 14B 동급" 표기).

### 주요 출처 (2026-04-25 직접 확인)
- Qwen3.6 27B HF 모델 카드: https://huggingface.co/Qwen/Qwen3.6-27B (벤치마크 표 + Qwen3.5 397B 비교 행 포함)
- Qwen3.5 397B-A17B HF: https://huggingface.co/Qwen/Qwen3.5-397B-A17B
- Nemotron Cascade 2 NVIDIA 페이지: https://research.nvidia.com/labs/nemotron/nemotron-cascade-2/ (구체 수치는 페이지 이미지로 표시)
- Nemotron Cascade 2 arXiv: 2603.19220 (= research.nvidia.com/labs/nemotron/files/Nemotron-Cascade-2.pdf)
- Nemotron Cascade 2 HF: https://huggingface.co/nvidia/Nemotron-Cascade-2-30B-A3B
- Gemma 4 26B-A4B HF 모델 카드: https://huggingface.co/google/gemma-4-26b-a4b-it
- Mistral Small 4 공식: https://mistral.ai/news/mistral-small-4
- buildfastwithai 27B vs 397B 비교: https://www.buildfastwithai.com/blogs/qwen3-6-27b-review-2026 (Alibaba 자체 평가 인용 + caveat)

---

## 주요 참조 링크
- OpenAI 모델: https://platform.openai.com/docs/models
- DeepSeek API: https://api-docs.deepseek.com
- Google Gemini: https://ai.google.dev/gemini-api/docs/models
- Anthropic 모델: https://docs.anthropic.com/en/docs/about-claude/models
