# 로컬 LLM 에코시스템 플랫폼 참조

웹 조사 결과 요약 (2026-04)
OpenClaw 상세 내용은 `notes/openclaw-reference.md` 참조.

---

## HuggingFace Hub (huggingface.co)

### 개요

AI 모델·데이터셋·데모 앱을 공유하는 세계 최대 규모의 AI 공개 저장소.
모델 200만+ 개, 데이터셋 50만+ 개 호스팅. "AI 버전 GitHub"로 통함.

### 주요 기능

| 기능 | 설명 |
|---|---|
| **모델 허브** | 오픈소스 LLM 파일 검색·다운로드 (GGUF, safetensors 등) |
| **Spaces** | 웹 기반 AI 데모 앱 (코딩 없이 브라우저에서 바로 사용) |
| **Inference API** | 클라우드에서 모델 API 호출 (무료 월 100K 크레딧) |
| **AutoTrain** | UI 기반 모델 파인튜닝 (코딩 불필요) |
| **Chat UI** | ChatGPT 유사 채팅 인터페이스 (무료) |

### 무료/유료

- **무료**: 모델·데이터셋 다운로드, Spaces 무료 호스팅, Inference API 월 100K 크레딧
- **유료**: 전용 GPU 컴퓨팅($0.60/hr~), Inference Endpoints, Enterprise Hub

### 출처

- huggingface.co, huggingface.co/pricing

---

## Ollama (ollama.com)

### 개요

로컬 컴퓨터에서 LLM을 실행하기 위한 CLI 기반 오픈소스 도구.
`ollama run qwen3:14b` 한 줄로 모델 다운로드+실행. macOS·Linux·Windows 지원.

### 주요 기능

- **CLI**: `ollama run <모델명>` — 모델 자동 다운로드 후 즉시 대화
- **모델 라이브러리**: ollama.com/library — Llama / Qwen / Gemma / DeepSeek 등
- **OpenAI 호환 REST API**: `localhost:11434/v1/` 엔드포인트, 기존 OpenAI SDK로 바로 연결
- **멀티모달**: LLaVA 계열 이미지 입력 지원

### 최신 버전 (2026-04-25 GitHub 릴리즈 페이지 직접 확인)

| 버전 | 주요 변경 | 날짜 |
|---|---|---|
| v0.21.3-rc0 | release candidate | 2026-04-24 |
| v0.21.2 | OpenClaw 온보딩 안정성, 모델 추천 순서 정렬, 웹 검색 플러그인 번들링 | 2026-04-23 |
| v0.21.1 | **Kimi CLI 지원** (`ollama launch kimi --model kimi-k2.6:cloud`), GLM4 MoE Lite 라우터 최적화, MLX logprobs/top-P/top-K 가속 | 2026-04-22 |
| v0.21.0 | **Gemma 4 on MLX** (Apple Silicon), Hermes Agent + GitHub Copilot CLI 통합 (`ollama launch`), MLX 백엔드 mixed-precision quantization | 2026-04-16 |
| v0.6.2 | Gemma 3 멀티 이미지 지원, AMD Strix Halo GPU 지원, /save 버그 픽스 | **2025-03-18** |
| v0.7.0 (참고) | Llama 4 / Gemma 3 / Qwen 2.5 VL / Mistral Small 3.1 멀티모달 신엔진 — Llama 4 지원은 v0.7.0부터 (notes 이전 v0.6.2 표기는 오류) | 2025년 |

### 출처 (2026-04-25 직접 확인)

- ollama.com, github.com/ollama/ollama/releases (각 v0.21.x · v0.6.2 · v0.7.0 단일 릴리즈 페이지)
- **이전 기록 정정 (hardware-verification.md 와 동일 오류)**:
  - "v0.21.0 Kimi K2.5/GLM-5/MiniMax 지원" → 실제 v0.21.0은 Gemma 4 MLX, Kimi(K2.6)는 v0.21.1, GLM4 MoE Lite 라우터 최적화도 v0.21.1
  - "v0.6.2 (2026-03) Llama 4 / Flash Attention 2.7 / M4 Metal 3" → 전부 잘못된 정보 (실제 v0.6.2는 2025-03-18, Gemma 3 관련)

---

## LM Studio (lmstudio.ai)

### 개요

GUI 데스크탑 앱으로 LLM을 로컬 실행. HuggingFace에서 모델을 검색·다운로드해
ChatGPT처럼 대화하거나, 로컬 OpenAI 호환 서버로 노출 가능.
macOS·Windows·Linux 지원. 완전 무료.

### 주요 기능

- **모델 검색 UI**: HuggingFace 모델을 앱 내에서 직접 검색·다운로드
- **채팅 UI**: 브라우저 없이 데스크탑 앱에서 대화, PDF 업로드 분석
- **로컬 API 서버**: OpenAI 호환 REST API (`localhost:1234/v1/`), Python/JS SDK 제공
- **LM Link** (v0.4.5+): 원격 LM Studio 머신에 Tailscale 암호화로 연결 — 팀 공유 가능
- **MCP Host** (v0.4.10+): MCP 서버 연결, 에이전트 도구 확장
- **다중 GPU 병렬 처리** (v0.4.10+): VRAM 분산 제어

### 최신 버전 (2026-04-25 lmstudio.ai/changelog 직접 확인)

| 버전 | 주요 변경 | 날짜 |
|---|---|---|
| v0.4.12 | Qwen 3.6 지원, PDF 내보내기 스타일 개선, Windows MCP 서버 버그 수정 | 2026-04-17 |
| v0.4.11 | **Gemma 4 채팅 템플릿 업데이트** (notes 이전 "Continuous Batching" 표기는 오류 — 해당 키워드 changelog 미등장) | 2026-04-10 |
| v0.4.10 | Gemma 4 도구 호출 신뢰도 개선, **MCP 서버 OAuth 지원** 추가 (notes 이전 "다중 GPU 제어" 미확인) | 2026-04-09 |
| v0.4.9 | MCP 관련 개선 | 2026-04-02 |
| v0.4.8 | — | 2026-03-26 |
| v0.4.7 | **수학 렌더링 버그 수정, 도구 호출 파싱 개선** (notes 이전 "Linux ARM 지원" 표기는 오류 — changelog 미등장) | 2026-03-18 |
| v0.4.6 | LM Link 보강 | 2026-02-27 |
| v0.4.5 | LM Link 도입 (원격 인스턴스 연결), Qwen 3.5 도구 호출 개선 | 2026-02-25 |

### 출처 (2026-04-25 재확인)

- lmstudio.ai, lmstudio.ai/changelog (직접 확인)
- **이전 기록 정정**: v0.4.11 "Continuous Batching", v0.4.7 "Linux ARM 지원" 모두 changelog 미등장 — 출처 불명 정보였음

---

## 플랫폼 비교 요약

| 항목 | HuggingFace | Ollama | LM Studio |
|---|---|---|---|
| 주요 역할 | 모델 저장소 + 클라우드 | 로컬 CLI 실행 | 로컬 GUI 실행 |
| 비개발자 친화도 | ⭐⭐⭐⭐⭐ (Spaces) | ⭐⭐⭐ (CLI) | ⭐⭐⭐⭐⭐ (GUI) |
| 인터넷 필요 | ✓ (클라우드) | 다운로드 시만 | 다운로드 시만 |
| OpenAI 호환 API | 별도 설정 필요 | ✓ 기본 제공 | ✓ 기본 제공 |
| 무료 여부 | 무료 + 유료 옵션 | 완전 무료 | 완전 무료 |
| 프라이버시 | 클라우드 | 완전 로컬 | 완전 로컬 |
