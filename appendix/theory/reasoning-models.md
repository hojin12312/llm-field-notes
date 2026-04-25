# 리즈닝 모델의 딜레마

**추론 모델(Reasoning Model)** — GPT-5.4, DeepSeek R1-0528, Gemini 2.5 Pro / 3.1 Pro(Preview), Claude Extended / Adaptive Thinking — 은 일반 모델과 다르게 작동합니다.

강력한 추론 능력을 얻는 대신 **컨텍스트 소비·비용·지연 시간** 세 가지가 함께 증가합니다. 이것이 추론 모델의 딜레마입니다.

본문 §2에서 "추론 모델을 언제 꺼내야 하는가" 라는 판단 기준을 언급했는데, 이 부록에서는 그 판단에 필요한 내부 구조와 비용 모델을 풀어 놓습니다.

---

## 외부 하네스 vs 내재화된 하네스

일반 모델은 피드백 루프가 **외부**에 있습니다:

```
[LLM] --> [tool call] --> [result] --> [LLM] --> ...
```

추론 모델은 그 루프를 **내부로 흡수**했습니다:

```
[LLM <thinking>
  ... hypothesis -> verify -> counterexample -> retry ...
</thinking>] --> [final answer]
```

하네스의 피드백 루프가 뇌 안으로 들어간 구조입니다. 외부에서 보면 한 번의 호출처럼 보이지만, 내부에서는 수십~수백 스텝의 자기 검토가 일어나고 있습니다.

여기서 중요한 질문: **단순히 루프가 내부로 들어간 것뿐인가, 아니면 추론 수준 자체가 달라지는가?**

답은 **학습 방식이 다르기 때문에 수준 자체가 달라진다**입니다.

외부 하네스는 LLM이 루프 구조 설계에 관여하지 않습니다. 사람이 미리 짜둔 tool schema(`read_file`, `run_code` 등)에 LLM이 끼워넣어지는 것입니다. 어떤 중간 단계를 밟을지는 하드코딩되어 있습니다.

추론 모델은 **RLVR(Reinforcement Learning with Verifiable Rewards)** 방식으로 학습됩니다:

1. 모델이 `<thinking>...</thinking>` → 최종 답을 자유롭게 생성
2. 최종 답만 reward signal로 평가 (수학 문제면 정답 여부)
3. 올바른 답으로 이어진 thinking 체인이 강화됨

schema 없이 임의의 중간 추론을 생성하면서, **어떤 intermediate thought가 올바른 답으로 이어지는지를 RL로 스스로 학습**합니다. "백트래킹", "반례 탐색", "단계적 검증" 같은 전략은 사람이 설계한 게 아니라 모델이 reward를 보며 발견한 것입니다.

---

## 왜 내부 Chain-of-Thought가 효과적인가

"생각하는 척" 출력이 왜 실제로 성능을 높일까요?

핵심은 **추론을 위한 연산 예산(compute budget)을 늘린다**는 것입니다. 트랜스포머는 레이어 수가 고정되어 있어, 단일 forward pass에서 쓸 수 있는 연산량에 물리적 상한이 있습니다. `<thinking>` 토큰을 생성하면:

1. 각 토큰 생성이 하나의 forward pass
2. N개의 thinking 토큰 = N번의 forward pass
3. 단계마다 이전 토큰들을 KV 캐시로 참조하며 "생각"을 이어감

결과적으로 **test-time compute를 수직 확장**합니다. 학습 시 고정된 파라미터 수에 관계없이, 추론 단계에서 더 많은 계산을 투입할 수 있습니다. AlphaGo가 MCTS로 탐색 깊이를 늘린 것과 같은 원리입니다.

---

## 숨겨진 비용

일반 모델에 `<thinking>` 흉내를 내게 시키면 어떻게 될까요? 속마음이 전부 텍스트로 출력되어 **KV 캐시를 빠르게 잠식합니다.**

추론 모델 자체도 마찬가지입니다. `<thinking>` 토큰은 사용자 눈에는 안 보이지만 컨텍스트 윈도우를 빠르게 소진합니다.

**추가 주의 — Summarized Thinking의 과금 구조**

Claude 4 계열은 기본값으로 **Summarized Thinking**을 반환합니다. 응답에는 요약본만 노출되지만, 과금은 **원본 thinking 토큰 전부**에 대해 이루어집니다. Opus 4.7은 한 단계 더 나아가 `display` 기본값이 `"omitted"` 여서 thinking 블록의 `thinking` 필드가 아예 비어 있습니다 (요약조차 안 보여줌). 응답 길이만 보고 비용을 계산하면 크게 과소평가합니다.

| 상황 | 선택 |
|---|---|
| 수학 증명, 논리 퍼즐, 복잡한 아키텍처 설계 | 추론 모델 |
| 반복적 파일 수정, 코드 검색, 정형화된 작업 | 일반 모델 + 강한 하네스 |
| 긴 작업 파이프라인 자동화 | 일반 모델 + Hook + Skill |

추론 모델은 "단발성 깊은 분석"에 씁니다. 루틴 작업을 추론 모델에 맡기는 것은 스캘펠로 나무를 베는 격입니다.

---

## Thinking Effort 조절 — Claude 4 계열

Claude 4 계열에서 thinking 을 쓰는 방법은 두 가지입니다. 하나는 **Adaptive Thinking** (Claude가 문제 난이도를 보고 알아서 생각량 결정), 다른 하나는 **Manual Thinking** (사용자가 예산 직접 지정). 2026-04 기준 공식 권장은 Adaptive.

### 모델별 지원 현황

| 모델 | Adaptive | Manual (`budget_tokens`) |
|---|---|---|
| Claude Opus 4.7 (`claude-opus-4-7`) | **유일 지원 모드** | 400 에러로 거부됨 |
| Claude Opus 4.6 (`claude-opus-4-6`) | 권장 | deprecated, functional |
| Claude Sonnet 4.6 (`claude-sonnet-4-6`) | 권장 | deprecated, functional |
| Claude Sonnet 4.5 / Opus 4.5 이전 | 미지원 | 필수 |

공식 문서가 Adaptive를 미는 이유는 **bimodal 태스크와 long-horizon agentic workflow에서 고정 `budget_tokens` 보다 평균 품질이 높기 때문**입니다. 에이전트 환경에서는 한 턴이 얼마나 어려울지 미리 모르므로, 예산을 고정하는 것보다 모델이 난이도를 보고 할당하는 편이 낫습니다.

### Adaptive Thinking 기본 사용법

```python
import anthropic

client = anthropic.Anthropic()
response = client.messages.create(
    model="claude-opus-4-7",
    max_tokens=16000,
    thinking={"type": "adaptive"},
    output_config={"effort": "high"},   # low | medium | high | xhigh | max
    messages=[{"role": "user", "content": "Prove that sqrt(2) is irrational."}],
)

for block in response.content:
    if block.type == "thinking":
        print("[thinking]", block.thinking[:200], "...")
    elif block.type == "text":
        print("[answer]", block.text)
```

`effort` 는 생각량에 대한 **소프트 가이드**입니다:

| 레벨 | 동작 | 지원 모델 |
|---|---|---|
| `max` | 생각 깊이에 제약 없음, 항상 생각 | Mythos Preview, Opus 4.7/4.6, Sonnet 4.6 |
| `xhigh` | 깊은 탐색, 항상 생각 | **Opus 4.7 전용** |
| `high` (기본값) | 항상 생각, 복잡한 태스크에 깊은 추론 | Adaptive 지원 모델 전부 |
| `medium` | 중간, 아주 간단한 질의는 생각 생략 가능 | 동일 |
| `low` | 최소, 단순 태스크는 생각 생략 | 동일 |

> **Opus 4.7의 display 기본값이 `"omitted"`** 라서 위 코드에서 `block.thinking` 이 비어서 출력될 수 있습니다. 요약이라도 보려면 `thinking={"type": "adaptive", "display": "summarized"}` 로 명시적으로 요청해야 합니다.

### Manual Thinking (Sonnet 4.6 이하 레거시)

Sonnet 4.6 / Opus 4.6 에서는 아직 `budget_tokens` 방식이 동작합니다 (deprecated). 비용을 정확히 상한 제어해야 하는 워크로드용.

```python
response = client.messages.create(
    model="claude-sonnet-4-6",
    max_tokens=16000,
    thinking={
        "type": "enabled",
        "budget_tokens": 10000,   # max_tokens 보다 작아야 함
    },
    messages=[{"role": "user", "content": "Prove that sqrt(2) is irrational."}],
)
```

공식 제약은 `budget_tokens < max_tokens` 하나뿐입니다 (interleaved thinking을 쓰면 이 상한도 컨텍스트 윈도우까지 늘어남). Opus 4.7에서는 이 방식이 400 에러로 거부됩니다 — Adaptive로 마이그레이션하세요.

---

## 에이전트 루프 속 Interleaved Thinking

Claude Code 같은 에이전트 환경에서 thinking을 쓰면, thinking 블록이 **도구 호출 사이사이에도** 끼어들 수 있습니다.

```
[thinking: 파일 구조 파악 계획] --> [read_file] --> [thinking: 결과 해석, 다음 전략] --> [edit_file] --> ...
```

각 도구 호출 직전에 thinking이 들어가므로, "다음 어떤 도구를 써야 하는가"를 한 번 더 추론하게 됩니다. 복잡한 리팩터링이나 다단계 디버깅에서 유효하지만, 도구 호출마다 thinking 비용이 붙으므로 단순 루틴 작업에는 비효율적입니다.

### 모드별 Interleaved 활성화 방식

| 모드 | Interleaved Thinking 동작 |
|---|---|
| Adaptive (Opus 4.7 / 4.6, Sonnet 4.6) | **자동 활성화** — 별도 헤더 불필요 |
| Manual on Sonnet 4.6 | `interleaved-thinking-2025-05-14` beta header 필요 |
| Manual on Opus 4.6 | **지원 안 됨** — interleaved 원하면 Adaptive로 전환 |

에이전트 워크로드에서 Claude 4 계열을 쓴다면 Adaptive가 사실상 기본 선택입니다.

---

## 실무 판단 기준

추론 모델 투입 여부를 빠르게 판단하는 기준:

```
단 한 번의 호출로 끝나는가?
       |
      YES --> 추론 모델 후보
       |
      NO
       |
       v
반복·루틴 실행이 필요한가? --> YES --> 일반 모델 + 하네스
```

추론 모델은 API 호출 비용이 일반 모델 대비 3~10배 비쌉니다. "정말 어려운 단발성 문제인가"를 먼저 확인하세요. 프롬프트를 잘 짜면 일반 모델로 해결되는 문제가 대부분입니다.

> 경량 모델이 대형 추론 모델을 따라잡는 현상은 [경량 모델의 약진](./small-model-advancement.md)에서 벤치마크 수치로 이어 다룹니다.
