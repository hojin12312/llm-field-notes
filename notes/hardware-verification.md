# 하드웨어 스펙 검증 메모 (로컬 전용, gitignore)

memory-bandwidth.md 업데이트 전 웹 서치로 사전 검증하는 문서.
마지막 검증: 2026-04-25 (notes 전수 재검증 4라운드 — Apple/Intel/Qualcomm/단종 라인업)

---

## 검증 결과

### Apple Silicon

| 항목 | 내용 | 출처 |
|---|---|---|
| M4 Ultra | **개발 취소** — 공식 발표 없이 조용히 취소됨 | 검색 결과 |
| M5 | 2025-10 출시 (MacBook Pro), 32 GB, **153 GB/s** | apple.com/macbook-pro/specs |
| M5 Pro | 2026-03 출시 (MacBook Pro), 64 GB, **307 GB/s** | apple.com/macbook-pro/specs |
| M5 Max 32-core GPU | 2026-03 출시 (MacBook Pro), 128 GB, **460 GB/s** | apple.com/macbook-pro/specs |
| M5 Max 40-core GPU | 2026-03 출시 (MacBook Pro), 128 GB, **614 GB/s** | apple.com/macbook-pro/specs |
| M5 Ultra | **미출시** — 2026 Mac Studio에 탑재 예정 (루머) | Macworld |
| Mac Studio 현 라인업 | M4 Max + M3 Ultra 혼재 판매 중. **2026-03 M3 Ultra 512GB 옵션 단종**, 256GB 옵션 가격 +$400 인상 | Tom's Hardware (2026-03), Macworld 종합 |
| M3 Ultra base 가격 | **$3,999** (96GB, 1TB SSD, 28-core CPU/60-core GPU) | MacRumors (2025-03 출시 보도) |
| M3 Ultra maxed 가격 (단종 전) | **$14,099** (32-core CPU/80-core GPU + 512GB + 16TB SSD) | MacRumors |
| M3 Ultra 256GB 현재 추정가 | **~$8,400** (base $3,999 + CPU/GPU 업그레이드 $1,500 + 96→256GB 업그레이드 ~$2,400 [2026-03 인상 반영]) | 산정 |
| Mac Studio M4 Max 128GB 가격 | **~$3,499** (128GB 1TB 구성 기준, 직접 확인 차단 — Apple/CDW/Best Buy 모두 가격 공시 미게재. retailer 인용가 추정) | 보류 (4라운드도 직접 확인 실패) |
| 메모리 칩 부족 영향 (2026-04) | 일부 M4 Max 128GB / M3 Ultra 256GB 구성 **4-5개월 배송 지연** | Macworld |
| M5 Mac Studio | 2026 H1~10월 예정 (Bloomberg 2026-04-19 보도, supply chain 지연 가능). M5 Max + M5 Ultra 둘 다 탑재 예상. 기본가 +$200 인상 가능성 (512GB → 1TB SSD 베이스 변경) | Macworld |

### NVIDIA 소비자 GPU 실거래가 (2026-04)

| 모델 | MSRP | 실거래가 | 비고 |
|---|---|---|---|
| RTX 5090 | $1,999 | **~$3,999** | 공급난 지속, GDDR7 병목 |
| RTX 5080 | $999 | **~$1,249** | 피크($1,900) 대비 하락 중 |
| RTX 5070 Ti | $749 | **~$999** | 피크($1,220) 대비 하락 추세 |

### NVIDIA 데이터센터 GPU

| 모델 | VRAM | 대역폭 | 단가 | 비고 |
|---|---|---|---|---|
| H200 SXM | 141 GB HBM3e | 4,800 GB/s (4.8 TB/s) | **~$30,000–40,000** | nvidia.com/en-us/data-center/h200 공식 확인 (2026-04-25) |
| H200 NVL | 141 GB HBM3e | 4,800 GB/s (4.8 TB/s) | — | PCIe 변종, 최대 600W |
| B200 | **192 GB HBM3e** | 8,000 GB/s (8 TB/s) | **~$30,000–55,000** | 192 GB가 정확 (이전 180 GB 표기 정정, 2026-04-25) |
| **B300 (Blackwell Ultra)** | **288 GB HBM3e+** | **8,000 GB/s (8 TB/s)** | **~$40,000–50,000+** | 2026-04 NVIDIA 페이지 "Shipping Now" |

- B200 192 GB 출처: technical.city/en/video/B200-SXM-192-GB, jarvislabs.ai/gpu/nvidia-b200 (둘 다 192 GB HBM3e)
- B300 출처: nvidia.com/en-us/data-center/dgx-b300 (DGX B300 시스템 = 8x B300, 시스템 메모리 합 2.1 TB, NVLink Switch 14.4 TB/s)
- H200 SXM/NVL 공식: nvidia.com/en-us/data-center/h200 (둘 다 141 GB HBM3e, 4.8 TB/s 동일)

---

## memory-bandwidth.md 반영 완료 항목

- [x] Apple Silicon 표: M4 Ultra 취소 표시 + M5 시리즈 추가
- [x] NVIDIA 데이터센터 표: B300 추가, H200/B200 가격 수정
- [x] RTX 50 시리즈 실거래가 업데이트
- [x] 플랫폼 비교 표: Mac Studio 가격, M5 Max 행 추가

---

## 2026-04-21 추가 조사 결과

### RTX 50 시리즈 실거래가 업데이트 (2026-04)

| 모델 | MSRP | 실거래가 (2026-04-25 재확인) | 출처 |
|---|---|---|---|
| RTX 5060 Ti (16 GB) | $429 | ~$379–$599 | bestvaluegpu.com/history/rtx-5060-ti, videocardz.com (5060 Ti $334~$379 인용) |
| RTX 5070 | $549 | ~$635 (커스텀 $635–$669) | bestvaluegpu.com/history/rtx-5070 |
| RTX 5070 Ti | $749 | ~$999–$1,169 | bestvaluegpu.com/history/rtx-5070-ti |
| RTX 5080 | $999 | ~$1,250 (Feb 2026 기준 25% 상승) | tomshardware GPU pricing 2026 |
| RTX 5090 | $1,999 | ~$3,699–$3,899 (2026-04 신품) | bestvaluegpu.com/history/rtx-5090 (Amazon $3,899, Newegg $3,699), eBay 중고 ~$2,862 |

- RTX 5090 출시: 2025-01-30, MSRP $1,999 (NVIDIA 공식)
- RTX 5060 Ti: 16 GB GDDR7, 448 GB/s, 8 GB 변종 비추천

### NVIDIA 전문가용 GPU (RTX PRO / 구 Quadro)

| 모델 | VRAM | 대역폭 | 가격 | 비고 |
|---|---|---|---|---|
| RTX 4500 Ada | 24 GB GDDR6 ECC | 432 GB/s | ~$2,000–2,500 | |
| RTX 6000 Ada | 48 GB GDDR6 ECC | 960 GB/s | ~$7,800–10,000 | 현재도 유통 |
| RTX PRO 6000 Blackwell | 96 GB GDDR7 ECC | 1,792 GB/s | MSRP $8,565 (2025-03 출시), 2026-04 ~$8,000–9,300 | 24,064 CUDA, 600W TGP, NVLink 미지원 |

- "Quadro" → 2021년 NVIDIA RTX Professional로 리브랜딩 → 2025년 "RTX PRO"
- RTX PRO 6000 Blackwell: 96 GB = RTX 5090(32 GB)의 3배, NVFP4 지원
- 출처 (2026-04-25 재확인): nvidia.com/en-us/products/workstations/professional-desktop-gpus/rtx-pro-6000, videocardz.com, thundercompute.com/blog/nvidia-rtx-pro-6000-pricing

### AMD 전문가용 GPU (Radeon Pro)

| 모델 | VRAM | 대역폭 | 가격 | 아키텍처 |
|---|---|---|---|---|
| Radeon Pro W7800 | 32 GB GDDR6 ECC | 576 GB/s | MSRP $2,499 | RDNA 3, 2023-04-13 출시 |
| Radeon Pro W7900 | 48 GB GDDR6 ECC | 864 GB/s | 출시 MSRP $3,999 → SEP $3,499 | RDNA 3, 384-bit 18 Gbps |
| Radeon AI PRO R9700 | 32 GB GDDR6 | 640 GB/s | **출시가 $1,299** (notes 이전 $1,500–2,000은 부정확) | RDNA 4, 2025-07-23 출시 |

- W7900: 48 GB ECC, RTX 6000 Ada 동급 VRAM 절반 가격 → LLM 온프레미스 가성비
- W9700: 별도 제품 없음 — AMD가 기존 Radeon Pro W 브랜드를 Radeon AI PRO R 브랜드로 교체. R9700이 사실상 W9700 포지션을 대체함 (폐기)
- ROCm으로 llama.cpp/vLLM 구동 가능, CUDA 대비 생태계 미성숙
- 출처 (2026-04-25 재확인):
  - W7900: tomshardware.com/pc-components/gpus/amd-debuts-radeon-pro-gpu-for-ai-workloads-radeon-pro-w7900-dual-slot..., AMD 데이터시트
  - W7800: amazon/newegg 제품 페이지, MSRP $2,499 (cgchannel.com 출시 보도)
  - R9700: phoronix.com/review/amd-radeon-ai-pro-r9700, microcenter/XFX $1,299, AMD 공식 (videocardz.com)

### 기타 플랫폼 (AMD ROCm / Intel / Qualcomm)

- **AMD ROCm**: llama.cpp + vLLM 지원, RX 7900 XTX 24 GB GDDR6 960 GB/s
  - **vLLM ROCm**: 2025-12-29 dedicated CI 출범 → 2026-04 기준 AMD CI 테스트 93% 통과 (2025-11 37%에서 급상승). MI300X/MI325X/MI350X/MI355X 모두 vLLM Docker 공식 지원. AITER로 ROCM_AITER_FA가 legacy ROCM_ATTN 대비 TPOT 2.8-4.6× 향상
  - **llama.cpp**: ROCm 패치 upstream 통합 (별도 fork 불필요). RX 7900 XTX (~$850) 기반 production inference rig 사례 있음
  - 출처: rocm.docs.amd.com, blog.vllm.ai/2026/02/27/rocm-attention-backend.html (2026-04-25 직접 확인)
- **Intel OpenVINO 2026.0** (2026-02-23 출시): GPT-OSS-20B / MiniCPM-V-4.5-8B / MiniCPM-o-2.6 신규 지원. NPU 지원 모델 확대 (MiniCPM-o-2.6, Qwen2.5-1.5B, Qwen3-Embedding-0.6B). INT4 data-aware weight compression for 3D MatMuls (MoE LLM 효율 배포). NPU speculative decoding (text generation 가속).
- **Intel OpenVINO 2026.1.0** (2026-04-07 출시): Qwen3 VL / GPT-OSS-120B (CPU) 지원. **OpenVINO backend for llama.cpp** (Intel CPU/GPU/NPU 최적 추론). TaylorSeer Lite caching (diffusion-transformer 가속). Dynamic LoRA (Qwen3-VL 등 vision-language). Intel Core Series 3 + Arc Pro B70 신규 지원. `openvino.runtime` deprecated namespace 제거.
- **Qualcomm Snapdragon X2 Elite / X2 Elite Extreme** (2025-09-25 발표 / 2026 H1 노트북 출시):
  - 18-core Oryon Prime CPU (12 Prime + 6 Performance), 5.0 GHz boost, 53 MB cache, 3nm 공정
  - 메모리 버스 192-bit, **228 GB/s 대역폭**
  - **Hexagon NPU 80 TOPS INT8** (전세대 X1 Elite 45 TOPS의 1.78× 향상, 37% 성능↑/16% 전력↓), FP8 / BF16 신규 지원
  - QNN SDK + QNN Execution Provider (ONNX Runtime), 30B+ 파라미터 모델 추론 가능
  - X2 Elite Plus (CES 2026): $800 노트북에 탑재 (가격 민주화)
- 출처:
  - OpenVINO: github.com/openvinotoolkit/openvino/releases (2026.0.0 / 2026.1.0 페이지 직접 확인), phoronix.com/news/Intel-OpenVINO-2026.0-Released
  - Qualcomm: qualcomm.com/news/releases/2025/09/new-snapdragon-x2-elite-extreme..., qualcomm.com Product Brief PDF, tomshardware.com (Snapdragon Summit 보도), microcenter.com/site/mc-news/article/snapdragon-x2-elite-extreme-tested.aspx

### Ollama 최신 릴리즈 (2026-04-25 GitHub 릴리즈 페이지 직접 확인)

| 릴리즈 | 날짜 (UTC) | 핵심 변경 |
|---|---|---|
| v0.21.3-rc0 | 2026-04-24 | release candidate |
| v0.21.2 | 2026-04-23 | OpenClaw 온보딩 안정성, 모델 추천 순서 정렬, 웹 검색 플러그인 번들링 |
| v0.21.1 | 2026-04-22 | **Kimi CLI 지원** (`ollama launch kimi --model kimi-k2.6:cloud`), MLX logprobs/top-P/top-K, GLM4 MoE Lite 라우터 최적화 |
| v0.21.0 | **2026-04-16** (이전 04-17 표기는 오류) | **Gemma 4 on MLX**, Hermes Agent + GitHub Copilot CLI 통합 (`ollama launch`), MLX 백엔드 mixed-precision quantization, 다양한 새 op wrappers, OpenClaw 버그 픽스 |
| v0.6.2 | **2025-03-18** (notes 이전 "2026-03" 오류) | Gemma 3 멀티 이미지 지원, AMD Strix Halo GPU 지원, /save 버그 픽스. **Llama 4 / Flash Attention 2.7 / M4 Metal 3 언급 없음 — notes 이전 기술은 전부 오류** |
| v0.7.0 | (참고) | Llama 4 지원은 v0.7.0부터 (Llama 4, Gemma 3, Qwen 2.5 VL, Mistral Small 3.1 멀티모달 신엔진) |

- 출처: github.com/ollama/ollama/releases (각 v0.21.x / v0.6.2 / v0.7.0 단일 릴리즈 페이지 직접 확인)
- **이전 notes 기록 오류 정정**:
  - "v0.21.0이 Kimi K2.5/GLM-5/MiniMax 지원 추가" → 실제 v0.21.0은 Gemma 4 MLX, v0.21.1에서 Kimi K2.6 CLI 추가, GLM은 별도 모델 추가가 아닌 GLM4 MoE Lite 최적화
  - "v0.6.2 (2026-03): Llama 4, Flash Attention 2.7, M4 Metal 3" → 실제 v0.6.2는 2025-03 Gemma 3 관련

### NVIDIA TensorRT-LLM 양자화 (2026-04-25 nvidia.github.io/TensorRT-LLM/features/quantization.html 직접 확인)

| 포맷 | 비트 | 대상 GPU 아키텍처 | 비고 |
|---|---|---|---|
| **NVFP4** | 4-bit FP | **Blackwell (SM120/SM100/SM103) 전용** | ModelOpt 오프라인 양자화 필수. FP16 대비 메모리 3.5× 절감, FP8 대비 1.8× 절감 |
| **FP8 (Per Tensor)** | 8-bit FP | Blackwell + Hopper + Ada Lovelace | 가장 광범위한 FP8 호환 |
| **FP8 (Block Scaling)** | 8-bit FP | Blackwell (SM100/103) + Hopper | SM100/103에서는 MXFP8 (E4M3) 레시피, Hopper는 표준 FP8 |
| **FP8 (Rowwise)** | 8-bit FP | Hopper 전용 | |
| **FP8 KV Cache** | 8-bit | 모든 지원 아키텍처 | KV 캐시 양자화, 모델 가중치와 별개로 활성화 가능 |
| **W4A8 / W4A16 AWQ** | 4-bit weight | Blackwell + Hopper + Ada + Ampere | 소형 배치 최저 지연 |
| **INT4 GPTQ (W4A16/W4A8)** | 4-bit weight | Blackwell + Hopper + Ada + Ampere | |

- **변경**: 이전 notes의 "INT8 모든 NVIDIA / FP8 H100, RTX 4090+ / INT4 AWQ RTX 소비자 포함" 표는 부정확 — TensorRT-LLM 공식 문서에 standalone INT8 항목 미언급, FP8 대응은 Ada Lovelace까지 모두 지원
- TensorRT-LLM 0.17+에서 NVFP4 native 지원 (B200/Blackwell)
- 출처: nvidia.github.io/TensorRT-LLM/features/quantization.html (2026-04-25 직접 확인)

---

## 다음 검증 필요 항목

- M5 Ultra 출시 시 스펙 업데이트 (Mac Studio 2026 H1~10월 예정)
- Mac Studio M4 Max 128GB 정확 USD 가격: 4라운드도 차단됨. Apple Store 직접 fetch 차단, retailer 가격 미공시. ~$3,499 추정만 보류 (보류 항목 재해소 시점은 2026-Q3 M5 Mac Studio 출시 즈음으로 예상)
- M3 Ultra 256GB 가격 인상 후 정확 USD: $400 인상 사실 확인, base 기준 산정치 ~$8,400만 추정. Apple Store 직접 fetch 시 정확치 확인 가능
- RTX 50 시리즈 가격 정상화 여부 (수급 안정화 시)
- B300 개별 GPU 공식 단가 확정 시
- AMD Radeon AI PRO R9700 대역폭: 640 GB/s 확인 완료 (AMD 공식 데이터시트)
- Radeon Pro W9700: 폐기 — AMD가 W 브랜드 → AI PRO R 브랜드로 교체, R9700이 해당 포지션 대체 (2026-04-21 확인)
