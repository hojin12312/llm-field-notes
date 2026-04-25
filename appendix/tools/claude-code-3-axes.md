# Claude Code 3대 설정 축: CLAUDE.md · Hooks · Skills

본문 [§6 에이전트 통제술 — 설정·컨텍스트 관리](../../README.md#6-에이전트-통제술-part-1-설정컨텍스트-관리)에서 **"CLAUDE.md에 트리거를 걸어 지식을 지연 로드하라"** 는 원칙을 다뤘습니다. 그 원칙을 실제로 굴리려면 Claude Code가 제공하는 세 설정 축 — CLAUDE.md·Hooks·Skills — 이 각각 어떤 역할을 맡고 있는지 구체적으로 알아야 합니다.

이 문서는 세 축의 **역할 분담**을 기준으로 Claude Code 설정을 풀어냅니다. 같은 기능을 세 축 중 어디에 두느냐에 따라 에이전트 품질이 크게 갈리므로, "무엇을 어디에 두는가" 를 판단하는 기준을 잡는 것이 목표입니다.

---

## 목차

0. [서문](#0-서문)
1. [LLM과 에이전트의 구조적 차이 — Claude Code의 위치](#1-llm과-에이전트의-구조적-차이--claude-code의-위치)
2. [Claude Code의 3대 설정 축](#2-claude-code의-3대-설정-축)
3. [CLAUDE.md — 계층적 지침 관리](#3-claudemd--계층적-지침-관리)
4. [Hooks — 이벤트 기반 자동화](#4-hooks--이벤트-기반-자동화)
5. [Skills — 특화된 슬래시 커맨드](#5-skills--특화된-슬래시-커맨드)
6. [실전 조합 워크플로우](#6-실전-조합-워크플로우)
7. [3축 통합 아키텍처 다이어그램](#7-3축-통합-아키텍처-다이어그램)
8. [실전 팁 모음](#8-실전-팁-모음)
9. [LLM의 메모리 병목: KV 캐시와 대역폭](#9-llm의-메모리-병목-kv-캐시와-대역폭)
10. [더 알아보기](#10-더-알아보기)
11. [부록](#11-부록)

---

## 0. 서문

### 이 문서가 답하는 질문들

- **Claude Code 같은 에이전트 도구를 쓸 때, 내가 직접 건드려야 하는 설정은 무엇인가?** 그리고 각 설정은 어떤 역할을 하는가?
- **대형 프로젝트에서 에이전트 답변 품질을 일정하게 유지하려면?** 프롬프트에 매번 똑같은 말을 적어야 하나?
- **같은 실수를 두 번 하지 않게 만들 수 있나?** 반복 작업은 어떻게 자동화하는가?
- **항상 적용되는 규칙 / 자동으로 실행되는 동작 / 필요할 때만 꺼내 쓰는 절차** — 이 셋을 어떻게 구분해서 관리하는가?

### 이 문서 활용법

- **레퍼런스**: 설정을 건드릴 때마다 "이건 CLAUDE.md 감인가 Hook 감인가 Skill 감인가" 판단할 때 펼쳐보는 사전
- **발표 자료 원본**: 각 장의 제목·핵심 메시지를 슬라이드 한 장으로 뽑아 쓸 수 있음
- **실전 카탈로그**: §11 부록에 `settings.json` 샘플을 붙여놓았습니다. 바로 복사·변형해서 쓰세요.

---

## 1. LLM과 에이전트의 구조적 차이 — Claude Code의 위치

LLM(손발 없는 뇌)과 에이전트(하네스를 붙인 몸), 인간과 AI의 구조 대응, 하네스가 모델 크기를 이기는 이유는 본문 [§1~§5](../../README.md)에서 상세히 다룹니다. 여기서는 Claude Code에 특화된 맥락만 짚습니다.

### 1.1 Claude Code의 위치

Claude Code는 **Anthropic이 직접 만든 터미널용 하네스의 레퍼런스 구현체**입니다.

- CLI(터미널에서 실행하는 명령줄 도구) 형태가 메인
- VS Code · JetBrains 확장, 데스크톱 앱(Mac/Windows), 웹앱(claude.ai/code) 형태로도 제공
- 내부적으로는 Claude 모델(Opus / Sonnet / Haiku)과 연결되어 동작

> **유사 도구**: Cursor, Aider, Continue, Roo Code 등. 개념은 모두 비슷하나 하네스 설계가 조금씩 다릅니다. 이 문서는 Claude Code 기준입니다.

### 1.2 핵심 비유: 뇌·몸·훈련법

- **LLM** = 뇌. 읽고 생각하고 답할 수 있음. 하지만 손발이 없음.
- **하네스** = 몸. 손(파일 편집), 발(셸 실행), 눈(화면 캡처), 귀(로그 수신)를 붙여줌.
- **설정(CLAUDE.md · Hooks · Skills)** = 훈련법·성격·습관. 같은 몸이라도 어떻게 훈련했느냐에 따라 결과가 완전히 다름.

이 문서의 나머지 80%는 "훈련법"을 다룹니다.

### 1.3 하네스가 모델 크기를 이긴다

CLAUDE.md가 잘 정의되어 있고, Hook이 오류를 잡아주고, Skill이 절차를 안내한다면 — 작은 모델도 큰 모델보다 실수가 적습니다. **모델 업그레이드보다 설정 정교화가 먼저입니다.** 상세한 비교는 본문 [§5 하네스가 체급을 이긴다](../../README.md#5-하네스가-체급을-이긴다)에서 다루고, 최근 경량 모델의 추격 데이터는 [397B를 이긴 27B](../theory/small-model-advancement.md)를 참고하세요.

---

## 2. Claude Code의 3대 설정 축

| 축 | 역할 | 동작 방식 | 누가 트리거하나 |
|----|------|-----------|----------------|
| **CLAUDE.md** | *무엇을 기억하고 따를 것인가* | 세션 시작 시 자동으로 LLM 컨텍스트에 주입되는 정적 지침 | 하네스 (자동 로드) |
| **Hooks** | *언제 무엇을 자동으로 실행할 것인가* | 특정 이벤트가 터질 때 미리 등록한 셸 커맨드 실행 | 하네스 (이벤트 기반) |
| **Skills** | *어떤 특화 능력을 꺼내 쓸 것인가* | `/<skill-name>` 입력 시 해당 스킬의 지시사항을 컨텍스트에 추가 로드 | 사용자 또는 LLM (명시적 호출 또는 자동 판단) |

### 왜 세 축이 따로 존재하는가?

- **CLAUDE.md** 는 "항상 적용되는 일반 규칙". 매번 적어주기 귀찮은 것을 고정해둠.
- **Hooks** 는 "LLM이 까먹어도 무조건 실행되는 강제 장치". 프롬프트에 부탁하는 것은 확률적이지만 hook은 결정적.
- **Skills** 는 "평소엔 안 쓰지만 특정 작업에만 꺼내 쓰는 전문가 모드". 항상 로드하면 컨텍스트가 낭비됨.

> **한 줄 핵심**: 정적 규칙 = CLAUDE.md / 자동 행동 = Hooks / 수동 특화 = Skills

---

## 3. CLAUDE.md — 계층적 지침 관리

### 3.1 개념: 세션 시작 시 자동 주입되는 컨텍스트

Claude Code는 세션을 시작할 때 **해당 디렉토리에서 상위로 거슬러 올라가며 `CLAUDE.md` 파일을 찾아 모두 LLM 컨텍스트에 주입**합니다.

- 주입 순서: **전역(`~/.claude/CLAUDE.md`) → 작업 루트 → 프로젝트** (상위 → 하위)
- 하위 파일이 상위 파일을 **특화·오버라이드** 합니다.
- 즉, LLM 입장에서는 "내 세션이 시작될 때부터 몇 장의 지침서가 이미 앞에 펼쳐져 있는 상태"가 됨.

이것이 **"매 대화마다 같은 말을 다시 적지 않아도 되는"** 비결입니다.

### 3.2 계층 트리 구성 예시

> 아래는 **구성 방식을 설명하기 위한 더미 예시**입니다. 실제 프로젝트 구조는 각자의 작업 방식에 맞게 설계하세요.

```
~/.claude/CLAUDE.md                        <- 전역 지침
|                                           (persona, shared style, language rules)
|
+-- ~/work-root-A/CLAUDE.md                <- 환경 A (예: 로컬 서버 개발)
|   +-- project-alpha/CLAUDE.md            <- 웹 서비스 프로젝트
|   +-- project-beta/CLAUDE.md             <- 데이터 대시보드
|   +-- project-gamma/CLAUDE.md            <- 에이전트 시스템
|
+-- ~/work-root-B/CLAUDE.md                <- 환경 B (예: 모바일 개발)
|   +-- mobile-app-1/CLAUDE.md
|   +-- mobile-app-2/CLAUDE.md
|
+-- ~/work-root-C/CLAUDE.md                <- 환경 C (제약이 다른 환경, 예: 사내망)
    +-- internal-tool-1/CLAUDE.md
    +-- internal-tool-2/CLAUDE.md
```

**규모 예시**: 전역 1개 + 환경 루트 3개 + 프로젝트 10여 개 = 총 14개 내외의 CLAUDE.md로 관리.

### 3.3 3계층을 나누는 기준

| 계층 | 쓰는 내용 | 쓰지 말아야 할 것 |
|------|----------|-------------------|
| **전역** (`~/.claude/CLAUDE.md`) | 개인 정보·호칭, 모든 프로젝트에 통하는 코드 스타일, 공용 커밋 정책, 대화 언어 | 특정 프로젝트에만 해당하는 기술 스택 |
| **환경 루트** (`~/<root>/CLAUDE.md`) | 해당 루트의 공통 제약 (예: 폐쇄망·OS·언어 생태계), 작업 루트 내 앱 목록·포트맵 | 전역에 써야 할 일반 규칙 |
| **프로젝트** (`~/<root>/<project>/CLAUDE.md`) | 기술 스택, 포트, 파일 구조, 빌드·배포 명령, 프로젝트 고유 규칙 | 오늘 할 일 같은 임시 내용 |

**임시 작업 메모는 CLAUDE.md가 아니라 `CONTINUE.md` 같은 별도 파일로** 관리하는 것을 추천합니다. 세션 종료 시 다음 세션용 인수인계 파일로 써먹을 수 있습니다 (본문 [§7 원칙 7 세션 핸드오프](../../README.md#7-에이전트-통제술-part-2-작업-워크플로우)).

### 3.4 구성 원칙

1. **작업 루트를 용도별로 분리** — 개인/업무, 개방망/폐쇄망, OS별, 언어별 등 각자의 축으로.
2. **하위로 내려갈수록 구체화** — 전역은 추상, 프로젝트는 구체.
3. **전역 오염 방지** — 한 프로젝트 특수 사정을 전역에 올리면 다른 프로젝트 답변이 왜곡됨.
4. **짧게 유지** — LLM은 앞쪽 토큰에 더 집중하는 경향. CLAUDE.md가 길어지면 뒤쪽에 쓴 규칙이 무시될 수 있음.
5. **중복 금지** — 전역에 이미 있는 규칙을 프로젝트에서 또 쓰지 않기. 상속이 일어남.

### 3.5 CLAUDE.md 작성 템플릿 (참고용)

```markdown
# 프로젝트 X

## 기술 스택
- 백엔드: Node.js 20 + Fastify
- 프론트: React 19 + Vite
- DB: PostgreSQL 16

## 실행
- 개발 서버: `npm run dev` (포트 3000)
- 테스트: `npm test`
- 빌드: `npm run build`

## 디렉토리 구조
- `src/api/` — REST 라우트 정의
- `src/ui/` — React 컴포넌트
- `migrations/` — DB 마이그레이션 (파일명: `NNNN_description.sql`)

## 규칙
- 모든 API 응답은 `{ ok: boolean, data?: any, error?: string }` 형식
- 신규 마이그레이션은 **절대로 기존 파일을 수정하지 않음**. 새 파일만 추가.
- 커밋 메시지는 Conventional Commits 규약 (`feat:`, `fix:`, `chore:`)
```

### 3.6 부속 설정 파일 (CLAUDE.md 생태계)

CLAUDE.md 외에도 `~/.claude/` 디렉토리에 둘 수 있는 참고 파일들:

| 파일 | 용도 |
|------|------|
| `active-projects.md` | 현재 활성인 프로젝트 인덱스 |
| `github-config.md` | SSH·GitHub 접속 정보 (환경별로 다를 때 유용) |
| `<app>-config.md` | 특정 외부 시스템 접속 메모 |
| `keybindings.json` | Claude Code 키보드 단축키 커스터마이징 |
| 메모리 저장소 | `~/.claude/projects/<path>/memory/` — 세션 간 영속 기억 |

> **용어 풀이 — 메모리**: CLAUDE.md와 달리 "자동으로 학습·갱신되는 기억". 사용자가 "이거 기억해둬"라고 말하거나, LLM이 교정을 받았을 때 저장됩니다. 지속성은 CLAUDE.md와 동일하지만, 작성 주체가 다름(LLM이 씀 vs 사람이 씀).

### 3.7 컨텍스트 윈도우와 CLAUDE.md의 관계

컨텍스트 윈도우의 정적/동적 레이어 구조와 Compaction은 본문 [§3 컨텍스트 윈도우 해부](../../README.md#3-컨텍스트-윈도우-해부)에서 상세히 다룹니다.

CLAUDE.md와의 직접 연결 포인트: CLAUDE.md 전체는 매 세션 **정적 레이어**로 LLM 컨텍스트에 올라갑니다. 계층이 누적(전역 → 환경 → 프로젝트)되므로 각 파일이 짧을수록 합산 부담이 줄어듭니다.

- 정적 레이어(CLAUDE.md + 호출된 Skill 본문)가 무거울수록 실제 작업(동적 레이어)에 쓸 공간이 줄어듦
- **CLAUDE.md는 짧을수록 좋다** — §3.4의 구성 원칙 4번 이유입니다

정적 레이어가 무거울 때 Prefill 비용이 얼마나 불어나는지(세션 재개 지연)와 Attention 분산 문제는 [트랜스포머 추론의 두 단계: Prefill과 Decode](../theory/transformer-inference.md)에서 수식과 함께 다룹니다.

---

## 4. Hooks — 이벤트 기반 자동화

### 4.1 개념

**Hook** 은 Claude Code 실행 중 **특정 이벤트**가 발생하면 미리 등록해둔 셸 커맨드 또는 HTTP 엔드포인트를 자동으로 실행하는 장치입니다. 이벤트가 발생하면 해당 이벤트의 **JSON 데이터가 hook 커맨드의 stdin으로 전달**되며, 커맨드는 `jq` 같은 도구로 필요한 필드를 파싱해서 씁니다.

```
[User Input]     -->  UserPromptSubmit hook
[Pre Tool Use]   -->  PreToolUse hook
[Post Tool Use]  -->  PostToolUse hook
[Session Start]  -->  SessionStart hook
[Stop]           -->  Stop hook
```

### 4.2 왜 Hook이 필요한가

**프롬프트나 CLAUDE.md로는 "매번 X해줘"를 100% 보장할 수 없습니다.** LLM은 확률적이어서 간혹 잊거나 규칙을 어깁니다.

Hook은 **하네스가 직접 실행하는 코드**이므로, LLM의 주의력이나 기분에 상관없이 **결정적으로 실행**됩니다.

> **비유**: "매일 물 마셔줘"를 친구에게 부탁하는 것 vs. 30분마다 자동으로 물을 따르는 디스펜서를 책상에 두는 것. 첫 번째는 잊혀지고, 두 번째는 반드시 일어납니다.

### 4.3 주요 이벤트 종류

Claude Code에는 **20여 종의 hook 이벤트**가 있습니다. 자주 쓰이는 핵심 5종부터 익히면 충분합니다.

| 이벤트 | 언제 발생하나 | 전형적 용도 |
|--------|-------------|-------------|
| `SessionStart` | 새 세션이 시작될 때 | 인증 상태 확인, 환경 점검, 초기 정보 출력 |
| `UserPromptSubmit` | 사용자가 메시지를 보냈을 때 | 특정 키워드 감지, 대화 로깅, 금칙어 차단 |
| `PreToolUse` | 도구 호출 직전 | 위험 명령 차단, 변경 전 백업, 권한 재확인 |
| `PostToolUse` | 도구 호출 직후 (성공) | 린터 자동 실행, 변경 알림, 테스트 자동 실행 |
| `Stop` | 에이전트가 응답을 마쳤을 때 | 완료 알림, 임시 파일 정리 |

그 밖에 자주 쓰이는 이벤트:

- `SessionEnd`, `StopFailure` — 세션·턴 종료 이벤트 (실패 케이스 분리)
- `PostToolUseFailure`, `PostToolBatch` — 도구 실패 시 처리, 병렬 배치 완료 후 집계
- `PreCompact`, `PostCompact` — 컨텍스트 압축 전후 (Compaction 직전 스냅샷 저장 등)
- `SubagentStart`, `SubagentStop` — 서브에이전트 수명 주기
- `FileChanged`, `CwdChanged` — 파일/작업 디렉토리 변경 감지
- `Notification`, `PermissionRequest`, `PermissionDenied` — 사용자 알림·권한 이벤트

> 전체 이벤트 목록과 최신 스펙은 [공식 문서 — Hooks](https://code.claude.com/docs/en/hooks)에서 확인하세요.

### 4.4 Hook 작성 예시

Hook은 `~/.claude/settings.json` (사용자 전역) 또는 프로젝트의 `.claude/settings.json` (프로젝트 전용)에 정의합니다.

**데이터 전달 방식 — 중요**: hook 커맨드는 이벤트별 **JSON을 stdin으로** 받습니다. 예를 들어 `PreToolUse` 이벤트는 아래와 같은 JSON을 커맨드로 내려보냅니다.

```json
{
  "session_id": "abc123",
  "cwd": "/Users/me/project",
  "hook_event_name": "PreToolUse",
  "tool_name": "Edit",
  "tool_input": { "file_path": "/path/to/file.py", "...": "..." },
  "tool_use_id": "toolu_01ABC..."
}
```

필요한 필드는 `jq` 등으로 추출해서 씁니다. 추가로 쓸 수 있는 **환경 변수**는 `$CLAUDE_PROJECT_DIR` (프로젝트 루트), `$CLAUDE_PLUGIN_ROOT` (플러그인 hook일 때), `$CLAUDE_CODE_REMOTE` (원격 실행 여부) 정도로 제한적입니다. 도구의 인자 값은 환경 변수가 아니라 stdin JSON에서 뽑아야 합니다.

#### 예시 1 — `SessionStart`: 세션 시작 시 git 상태 표시

```json
{
  "hooks": {
    "SessionStart": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "git -C \"$CLAUDE_PROJECT_DIR\" status --short 2>/dev/null || true"
          }
        ]
      }
    ]
  }
}
```

**효과**: 새 세션을 열 때마다 현재 브랜치의 변경 사항이 자동 출력되어, "아 참, 커밋 안 한 게 있었지" 같은 실수를 줄여줍니다.

#### 예시 2 — `PreToolUse`: 프로덕션 환경 파일 보호

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "jq -r '.tool_input.file_path // empty' | grep -qE '\\.prod\\.(env|yaml|json)$' && { echo 'BLOCKED: .prod.* 파일은 직접 편집 금지' >&2; exit 2; } || exit 0"
          }
        ]
      }
    ]
  }
}
```

**효과**: `*.prod.env`, `*.prod.yaml` 같은 파일 수정 시도를 **즉시 차단**합니다. LLM이 실수로 프로덕션 시크릿에 손대는 것을 방지.

**동작 원리**: hook 커맨드는 `PreToolUse` 이벤트의 JSON을 stdin으로 받고, `jq -r '.tool_input.file_path'` 로 대상 파일 경로를 뽑아냅니다. 경로가 `.prod.*` 패턴과 매칭되면 `exit 2` 로 차단(stderr가 LLM에 에러로 전달되어 도구 호출이 중단됨). 그 외는 `exit 0` 으로 통과.

> JSON stdout으로 `{"hookSpecificOutput": {"permissionDecision": "deny", "permissionDecisionReason": "..."}}` 를 내놓는 방식으로도 차단·이유 전달이 가능합니다. `exit 2` 방식이 가장 간단합니다.

#### 예시 3 — `PostToolUse`: 코드 편집 후 자동 린터

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "F=$(jq -r '.tool_input.file_path // empty'); case \"$F\" in *.py) ruff check --fix \"$F\" 2>/dev/null || true ;; *.js|*.ts) eslint --fix \"$F\" 2>/dev/null || true ;; esac"
          }
        ]
      }
    ]
  }
}
```

**효과**: 파이썬 파일은 `ruff`, JS/TS 파일은 `eslint`로 자동 포맷·린트 고정. LLM이 실수로 찍은 공백 한 칸까지 정리됨.

### 4.5 permissions / allowlist — Hook은 아니지만 같이 관리

같은 `settings.json` 안에 **Hook과 함께 자주 쓰이는 설정**이 권한 관리입니다.

```json
{
  "permissions": {
    "allow": [
      "Bash(git status:*)",
      "Bash(git diff:*)",
      "Bash(ls:*)",
      "Bash(python3:*)",
      "Bash(npm run test:*)"
    ],
    "deny": [
      "Bash(rm -rf /*)"
    ]
  }
}
```

- **allow 리스트**에 든 명령은 사용자 승인 없이 실행 가능 → 매번 "y/n" 프롬프트 사라짐
- **deny 리스트**는 절대 금지 명령 (강제 차단)
- 번들 스킬 `/fewer-permission-prompts` 로 최근 자주 쓴 명령을 반자동으로 allow에 넣을 수 있음

**균형점**: 너무 열어두면 위험, 너무 닫아두면 피곤. **읽기 전용·부작용 없는 명령**만 allow에 넣는 것이 일반적 원칙.

### 4.6 Hook 작성 팁

- **짧고 빠르게** — Hook은 세션 응답 경로 위에 있음. 느린 hook은 모든 응답이 느려짐.
- **실패해도 안전하게** — 커맨드 끝에 `|| true` 를 붙여 에러가 세션을 멈추지 않게 (의도적 차단은 `exit 2`).
- **절대 경로 사용** — `PATH` 환경변수에 의존하면 환경마다 다르게 동작.
- **민감 정보 분리** — API 키·토큰은 hook 인라인에 넣지 말고 외부 스크립트로 분리.
- **디버그 먼저** — 처음 만들 때는 `echo "$(</dev/stdin)" >> /tmp/hook.log` 로 stdin JSON 형태부터 로그에 찍어 확인. 실제 로직은 그 뒤에.

---

## 5. Skills — 특화된 슬래시 커맨드

### 5.1 개념

**Skill** 은 **`/<skill-name>` 형식으로 호출하는 특화된 작업 모드**입니다.

- Hook이 **자동 실행**이라면, Skill은 **명시적 호출** (사용자가 직접 `/skill` 입력) 또는 **LLM의 자동 판단 호출**(description이 상황에 맞으면 LLM이 스스로 불러옴) 두 갈래로 동작합니다.
- 호출되면 해당 스킬의 지시사항(`SKILL.md`)이 LLM 컨텍스트에 추가로 로드됨
- 로드된 LLM은 "이 스킬의 절차를 따라 작업"을 수행

### 5.2 왜 Skill이 필요한가

자주 쓰는 프롬프트 · 절차 · 역할을 **재사용 가능한 단위로 패키징**하기 위해서입니다.

예:
- "PR 리뷰"를 매번 똑같이 설명하긴 번거로움 → `/review` 스킬로 포장
- "보안 점검 체크리스트"를 자주 돌리고 싶음 → `/security-review` 스킬로 포장
- "새 프로젝트에 CLAUDE.md 만들어줘" → `/init` 스킬로 포장

**항상 로드하지 않는 것이 포인트** — 컨텍스트 윈도우는 유한하므로, 지금 쓸 스킬만 필요할 때 불러오는 설계입니다. 스킬의 `description` 필드만 세션 시작 시 가볍게 컨텍스트에 올라가고, 본문은 실제 호출 순간에만 로드됩니다.

### 5.3 스킬 저장 위치 — 4계층 우선순위

스킬은 어디에 두느냐에 따라 적용 범위가 달라집니다. **enterprise > personal > project** 순으로 우선순위가 매겨지고, 플러그인은 별도 네임스페이스(`<plugin>:<skill>`)를 씁니다.

| 위치 | 경로 | 적용 범위 |
|------|------|-----------|
| **Enterprise** (관리형) | 조직 설정으로 배포 | 조직 전체 사용자 |
| **Personal** | `~/.claude/skills/<name>/SKILL.md` | 내 모든 프로젝트 |
| **Project** | `.claude/skills/<name>/SKILL.md` | 이 프로젝트만 |
| **Plugin** | `<plugin>/skills/<name>/SKILL.md` | 플러그인 활성화 시 |

같은 이름의 스킬이 여러 위치에 있으면 우선순위 높은 쪽이 이깁니다. 플러그인은 네임스페이스가 달라 충돌하지 않습니다.

> **Custom commands가 Skills로 통합됐습니다**: 과거 `.claude/commands/deploy.md` 는 `/deploy` 커맨드를 만들었고, 지금은 `.claude/skills/deploy/SKILL.md` 도 동일하게 `/deploy` 를 만듭니다. 기존 `commands/` 파일은 그대로 동작하지만, 스킬이 디렉토리 단위로 부속 파일(템플릿, 예시, 스크립트)을 담을 수 있고 frontmatter로 동작을 세밀하게 제어할 수 있어 권장됩니다.

### 5.4 빌트인 커맨드 vs 번들 스킬

Claude Code에 기본 탑재된 슬래시 커맨드는 **두 종류**가 섞여 있습니다.

- **빌트인 커맨드**: Claude Code 내부에 하드코딩된 고정 로직. `/help`, `/clear`, `/compact`, `/cost`, `/status`, `/model`, `/fast`, `/plan`, `/memory`, `/btw`, `/branch` 등.
- **번들 스킬**: 기본 탑재되어 있지만 **프롬프트 기반**으로 동작. LLM이 스킬 본문을 플레이북으로 받아 자기 도구로 일을 처리. `/init`, `/review`, `/security-review`, `/simplify`, `/update-config`, `/fewer-permission-prompts`, `/keybindings-help`, `/loop`, `/schedule`, `/claude-api`, `/debug`, `/batch` 등.

| 스킬 | 용도 | 언제 쓰나 |
|------|------|-----------|
| `/init` | 새 `CLAUDE.md` 생성 | 프로젝트에 처음 진입했을 때 |
| `/review` | PR 리뷰 | 머지 전 자체 검토 |
| `/security-review` | 보안 관점 변경사항 점검 | 배포 직전 마지막 확인 |
| `/simplify` | 변경된 코드의 품질·재사용성 점검 및 수정 | 리팩터링 마무리 단계 |
| `/update-config` | `settings.json` 편집(권한·훅·환경변수) | 자동화 규칙 추가 시 |
| `/fewer-permission-prompts` | 반복 Bash 호출을 allowlist에 자동 추가 | 권한 프롬프트 피로 해소 |
| `/keybindings-help` | 키보드 단축키 커스터마이징 | `~/.claude/keybindings.json` 편집 |
| `/loop` | 주기적 반복 실행 | "5분마다 빌드 상태 확인" |
| `/schedule` | 크론 기반 원격 스케줄 에이전트 관리 | 매일 아침 자동 브리핑 |
| `/claude-api` | Anthropic SDK 앱 개발·디버깅·모델 마이그레이션 | API 코드 작업 시 |

> 빌트인 커맨드의 전체 목록과 단축키는 [Claude Code 명령어 참조](../../notes/claude-code-commands.md)에서 볼 수 있습니다.

### 5.5 플러그인 제공 스킬 (공식 마켓플레이스 기준)

`anthropics/claude-plugins-official` 마켓플레이스에는 수십 개의 플러그인이 있고, 각 플러그인은 스킬·에이전트·훅·MCP 서버를 내포합니다. 대표 예:

- **`plugin-dev`** — 플러그인·에이전트·훅·MCP·스킬 개발 자체를 돕는 메타 스킬 세트
- **`skill-creator`** — 새 스킬을 자동으로 생성
- **`mcp-server-dev`** — MCP 서버 빌드 (`build-mcp-server`, `build-mcp-app`, `build-mcpb`)
- **`pr-review-toolkit`** — PR 자동 리뷰 에이전트 번들 (6종 에이전트)
- **`code-simplifier`, `feature-dev`** — 코드 설계·리뷰 에이전트
- **`claude-md-management`** — CLAUDE.md 파일을 감시하고 개선 제안
- **`hookify`** — 대화 패턴을 분석해 hook 규칙 초안 자동 생성
- **`frontend-design`** — 고급 UI 컴포넌트 제작 지원
- **`swift-lsp`, `clangd-lsp`** — 각각 Swift, C/C++/Objective-C 언어 서버 통합

> **용어 풀이 — MCP (Model Context Protocol)**: LLM에 외부 도구·데이터 소스를 연결하는 표준 프로토콜. 내 에이전트에 "Slack 읽기", "Linear 티켓 조회", "GitHub 이슈 생성" 같은 능력을 붙일 때 사용. 상세는 §5.9.

### 5.6 커스텀 스킬 만들기

가장 단순한 스킬 구조:

```
~/.claude/skills/
+-- my-skill/
    +-- SKILL.md
```

더 복잡한 스킬은 디렉토리에 부속 파일을 얹습니다.

```
my-skill/
+-- SKILL.md              (메인 지침, 필수)
+-- reference.md          (상세 참조, SKILL.md에서 링크로 지시하면 필요 시 로드)
+-- examples/sample.md    (출력 예시)
+-- scripts/helper.py     (스킬이 실행하는 유틸리티)
```

최소 `SKILL.md` 예시:

```markdown
---
name: my-skill
description: 이 줄이 스킬 호출 여부를 결정짓습니다. 구체적·명확하게 작성할 것. 예 - "Python 코드 변경 후 pytest를 자동 실행하고 실패 시 원인 요약"
---

# My Skill

작업 수행 시 따라야 할 단계별 지침을 여기에 작성합니다.

1. 먼저 이렇게 합니다.
2. 그 다음에 이렇게 합니다.
3. 끝으로 이렇게 보고합니다.
```

**핵심 팁 — `description` 필드가 스킬 품질의 80%**:
- LLM은 모든 스킬의 `description`을 보고 "지금 상황에 어떤 스킬을 호출할지"를 판단합니다.
- `description`이 모호하면 엉뚱한 때 호출되거나, 필요할 때 호출되지 않습니다.
- **"언제 써야 하는가"를 구체적 트리거 상황과 함께** 쓰는 것이 좋습니다.

#### Frontmatter 주요 필드

`SKILL.md` 맨 위 `---` 사이에 쓰는 YAML 필드로 동작을 세밀하게 제어할 수 있습니다.

| 필드 | 용도 |
|------|------|
| `name` | 슬래시 커맨드 이름. 생략 시 디렉토리 이름 사용 |
| `description` | 스킬 호출 판단에 쓰이는 한 줄 설명 (권장) |
| `when_to_use` | 트리거 예시 문장을 덧붙여 LLM 판단 정확도 향상 |
| `allowed-tools` | 이 스킬이 활성화된 동안 승인 없이 쓸 수 있는 도구 (예: `Bash(git add *) Bash(git commit *)`) |
| `disable-model-invocation: true` | LLM이 자동으로 호출 못 하게 막고, 사용자 `/` 호출만 허용 (배포·커밋 같은 부작용 큰 작업에 유용) |
| `user-invocable: false` | 반대로 `/` 메뉴에서 숨기고 LLM 자동 호출만 허용 (배경 지식형 스킬) |
| `context: fork` + `agent` | 독립 서브에이전트로 실행 (대규모 탐색·리서치에 유용) |
| `argument-hint`, `arguments` | 인자 자동 완성 힌트와 이름 매핑. 본문에서 `$ARGUMENTS`, `$0`, `$1` 로 참조 |
| `paths` | 특정 파일 glob 패턴에서만 자동 호출 (예: `src/api/**/*.ts`) |
| `model`, `effort` | 이 스킬 동안 모델·추론 effort 오버라이드 |

#### 동적 컨텍스트 주입

스킬 본문에 `` !`<command>` `` 을 쓰면 스킬 로드 **직전에** 셸 커맨드가 실행되고, 그 출력이 자리에 삽입됩니다. LLM은 명령이 아니라 **실행 결과**를 봅니다.

```markdown
---
name: pr-summary
description: 현재 PR 변경사항 요약
allowed-tools: Bash(gh *)
---

## PR 컨텍스트
- 변경 내용: !`gh pr diff`
- 댓글: !`gh pr view --comments`

## 작업
위 컨텍스트를 바탕으로 3줄 요약을 생성하세요.
```

`/pr-summary` 를 치면 `gh pr diff` 결과가 프롬프트에 미리 박힌 채로 LLM에 전달됩니다.

### 5.7 Skill vs Hook — 언제 뭘 쓸까?

| 상황 | 해답 |
|------|------|
| 매번 무조건 일어나야 하는 동작 | **Hook** |
| 가끔, 사용자가 원할 때만 | **Skill** |
| 실행 조건이 환경/이벤트에 달려 있음 | **Hook** |
| 긴 절차·체크리스트를 재사용 | **Skill** |
| LLM의 의사결정이 필요 (코드 분석 등) | **Skill** (LLM을 태워야 하므로) |
| 결정론적 셸 실행이면 충분 | **Hook** |

### 5.8 추론 모델 = 내재화된 하네스

**추론 모델(Reasoning Model)** 이란 GPT-5.4, Claude Extended Thinking처럼 답하기 전에 **내부적으로 긴 사고 과정을 거치는** 모델입니다.

일반 모델은 하네스가 외부에서 피드백 루프를 돌립니다:

```
[LLM] --> [도구 실행] --> [결과 반환] --> [LLM] --> ... (외부 루프)
```

추론 모델은 이 루프를 모델 내부로 흡수했습니다:

```
[LLM <thinking>... 자기 검토 -> 백트래킹 -> 재시도 ...</thinking>] --> [최종 답변]
```

#### 언제 추론 모델을 쓸까?

| 상황 | 선택 |
|------|------|
| 수학 증명, 논리 퍼즐, 복잡한 아키텍처 설계 | 추론 모델 (깊은 내부 검토 필요) |
| 반복적 파일 수정, 코드 검색, 정형화된 작업 | 일반 모델 + 강한 하네스 |
| 체크리스트 기반 코드 리뷰 | 일반 모델 + Skill |

#### 추론 모델의 숨겨진 비용: `<thinking>` 토큰

추론 모델은 내부 사고 과정(`<thinking>` 내부)에서 **엄청난 토큰을 소비**합니다. 사용자 눈에는 안 보이지만 KV 캐시를 빠르게 채웁니다.

- 긴 추론을 요청할수록 컨텍스트가 빠르게 고갈됩니다.
- 추론 모델에 "이것도 저것도 같이" 식 복합 요청은 오히려 역효과입니다.

**실전 규칙**: 추론 모델은 "단발성 깊은 분석"에 씁니다. 반복·자동화 작업은 일반 모델 + 하네스 조합이 훨씬 효율적입니다. thinking 토큰 과금 구조, adaptive / manual thinking 모드 차이, Opus 4.7 이후의 `display="omitted"` 기본값 같은 최신 사항은 [리즈닝 모델의 딜레마](../theory/reasoning-models.md)에서 다룹니다.

### 5.9 확장성: 웹 검색과 MCP 서버

3대 설정 축이 Claude Code의 **내부 동작**을 조율한다면, 웹 검색과 MCP 서버는 **외부 세계**로 뻗어나가는 팔입니다.

**웹 검색** — 기본 내장 도구. 최신 라이브러리 문서, 에러 해결책, 릴리스 노트를 실시간으로 검색하며 작업합니다. 학습 데이터 컷오프 이후의 정보도 바로 참조됩니다.

**MCP 서버(Model Context Protocol)** — 외부 시스템을 Claude Code의 네이티브 도구로 연결하는 표준 프로토콜. 설치하면 LLM이 해당 시스템에 직접 접근합니다.

| 연결 예시 | 가능해지는 것 |
|---|---|
| GitHub | PR 생성·리뷰·이슈 조회를 대화로 처리 |
| Slack / Teams | 채널 읽기·쓰기, 알림 자동 발송 |
| Linear / Jira | 티켓 생성·조회·상태 변경 |
| Google Drive | 문서 읽기·생성, 스프레드시트 조작 |
| PostgreSQL / SQLite | DB 쿼리·스키마 조회를 자연어로 |
| 브라우저 자동화 | 웹 페이지 탐색, 폼 입력, 스크린샷 캡처 |
| Figma | 디자인 컴포넌트 조회·생성 |

**폐쇄망 환경에서는 이 두 가지가 차단됩니다.** 외부망에서는 모두 열려 있어, 코드 작업 중에 GitHub 이슈를 열고, Slack에 결과를 보내고, DB를 직접 조회하는 일이 하나의 대화 흐름 안에서 이루어집니다. 에이전트가 "세상과 연결된 상태"로 동작한다는 게 실제로 어떤 의미인지 체감할 수 있습니다.

> **설치 방법**: MCP 서버는 `settings.json`의 `mcpServers` 항목에 등록합니다. `claude-plugins-official`의 `mcp-server-dev` 스킬이나 번들 스킬 `/update-config`로 설정 편집을 도움받을 수 있습니다.

**폐쇄망 환경 추가 설정** — 차단된 기능을 Claude Code가 호출하려다 실패하는 상황을 사전에 막아야 합니다. 전역 CLAUDE.md에 명시해두면 모든 프로젝트 세션에 자동 적용됩니다.

웹 검색 도구 사용 자체를 금지합니다:

```markdown
## 제약 환경 (폐쇄망)
- WebSearch, WebFetch 도구를 사용하지 말 것 — 사용 불가 환경
```

멀티모달 기능이 없는 온프레미스 환경에서는 Read 도구로 이미지를 읽으려 시도하는 경우가 많습니다. 전역 CLAUDE.md에 명시적으로 막아야 합니다:

```markdown
## 제약 환경 (폐쇄망)
- Read 도구로 이미지 파일(.jpg .jpeg .png .gif .webp .bmp 등)을 읽지 말 것
- 이미지 분석이 필요한 경우 사내 멀티모달 모델을 활용할 것
```

---

## 6. 실전 조합 워크플로우

3축이 **같이** 돌아갈 때 진짜 힘이 나옵니다. 두 가지 시나리오로 감을 잡아봅시다.

### 시나리오 A — 새 프로젝트 킥오프

```
1. mkdir my-new-project && cd my-new-project
2. claude 실행
3. /init 호출  (번들 Skill)
   -> LLM이 프로젝트 구조를 읽고 CLAUDE.md 초안을 생성
4. 생성된 CLAUDE.md를 다듬어 저장
5. 자주 쓸 명령(npm test, pytest 등)을 /fewer-permission-prompts 로 allowlist에 추가
6. 전역 hook(SessionStart)이 git 상태를 자동으로 표시
7. 개발 사이클 진행
8. 머지 직전 /review -> /security-review 로 자체 검토
```

**이 흐름에서 3축의 역할**:
- CLAUDE.md — 프로젝트 기본 문맥 고정 (기술 스택, 포트)
- Hook (SessionStart) — 매 세션마다 git 상태 자동 표시로 실수 방지
- Skill (init, review, security-review) — 특정 순간에만 필요한 전문 작업 꺼내 쓰기

### 시나리오 B — 반복 작업 자동화

```
상황: "빌드가 끝나면 소리로 알려주면 좋겠다"라는 불편 발생

1. /update-config 호출 (번들 Skill)
   -> LLM이 settings.json 편집을 도와줌
2. PostToolUse hook 추가:
   - matcher: "Bash"
   - command: tool_input.command 를 jq 로 뽑아 "npm run build" 패턴 매칭 시 afplay 로 알림 소리
3. CLAUDE.md에 "빌드는 npm run build 로만 한다" 한 줄 추가
   -> 다른 명령으로 빌드하면 hook 이 트리거 안 됨 -> 일관성 강제
```

**이 흐름에서 3축의 역할**:
- Skill (update-config) — hook 작성을 도와주는 메타 도구
- Hook (PostToolUse) — 실제 자동 알림을 실행
- CLAUDE.md — LLM이 hook 트리거 조건과 맞게 행동하도록 유도

---

## 7. 3축 통합 아키텍처 다이어그램

```
+----------------------------------------------------------------------+
|                                 User                                 |
+----------------------------------------------------------------------+
                                  |
                      input (incl. /skill calls)
                                  v
+----------------------------------------------------------------------+
|                         Claude Code Harness                          |
|                                                                      |
|  +----------------------------------------------------------------+  |
|  | Context auto-injected at session start:                        |  |
|  |   [Global CLAUDE.md] + [Env CLAUDE.md] + [Proj CLAUDE.md]      |  |
|  +----------------------------------------------------------------+  |
|                                   |                                  |
|                                   v                                  |
|  +----------------------------------------------------------------+  |
|  |                              LLM                               |  |
|  |                    (Opus / Sonnet / Haiku)                     |  |
|  +----------------------------------------------------------------+  |
|              tool call                  /skill call (explicit)       |
|                  |                                 |                 |
|                  v                                 v                 |
|  +------------------------------+  +------------------------------+  |
|  |         Hooks Events         |  |       Skills Directory       |  |
|  |   - SessionStart             |  |   ~/.claude/skills/          |  |
|  |   - UserPromptSubmit         |  |   <proj>/.claude/skills/     |  |
|  |   - PreToolUse               |  |   Plugin marketplace         |  |
|  |   - PostToolUse              |  |                              |  |
|  |   - Stop                     |  |                              |  |
|  +------------------------------+  +------------------------------+  |
|                  |                                                   |
|                  v                                                   |
|  +----------------------------------------------------------------+  |
|  |       Shell / File System / External API (MCP)                 |  |
|  +----------------------------------------------------------------+  |
+----------------------------------------------------------------------+
```

**읽는 법**:
- **세로축**: 사용자 → 하네스 → LLM → 실제 세계
- **왼쪽 갈래 (Hooks)**: 자동·결정적 경로
- **오른쪽 갈래 (Skills)**: 수동·명시적 경로 (LLM 자동 판단 포함)
- **상단 덩어리 (CLAUDE.md)**: 모든 LLM 호출에 깔리는 배경 문맥

---

## 8. 실전 팁 모음

> 세션 관리(1세션=1작업, `/clear`, 세션 핸드오프, CONTINUE.md)와 일반 자동화 원칙(메타 최적화 우선, 임시방편 금지)은 본문 [§6~§7](../../README.md#6-에이전트-통제술-part-1-설정컨텍스트-관리)에서 다룹니다. 여기서는 3축 설정에 직결된 팁만 정리합니다.

### CLAUDE.md 작성 요령
- **"하지 마" 보다 "이렇게 해"** — 금지형보다 지시형이 LLM에 더 잘 먹힙니다.
- **구체적·짧게** — "친절하게 응답" 같은 추상 지시는 효과가 낮음. "코드 블록 앞에 한 줄 요약" 같이 구체적으로.
- **예시를 같이 주기** — 원하는 출력 형식은 짧은 샘플을 함께 넣으세요.
- **임시방편 규칙 금지** — 에러를 덮으려고 "만약 X 에러가 나면 무시해" 같은 예외를 누적하면 LLM이 모순된 지침 사이에서 환각을 일으킵니다. 근본 원인을 고치세요.

### Hook 작성 요령
- **짧고 빠르게** — 세션 응답 경로 위에 있으니 느린 hook은 모든 응답을 느리게 만듭니다.
- **실패해도 안전하게** — 일반 실패는 `|| true`, 의도적 차단은 `exit 2`.
- **절대 경로 사용** — PATH 환경변수에 의존하면 환경마다 다르게 동작.
- **민감 정보 분리** — API 키·토큰은 hook 인라인에 넣지 말고 외부 스크립트로 분리.
- **미니멀리즘** — 이벤트 하나에 훅 하나, 30줄 이내. 너무 많아지면 디버깅 지옥.
- **설정 변경 시 근거 기록** — 왜 이 hook을 넣었는지 짧은 주석을 settings.json 옆에 남겨두면 3개월 뒤 자신에게 큰 선물이 됩니다.

### Skill 작성 요령
- **description 을 트리거 지향으로** — "이런 상황·이런 키워드가 나오면 이 스킬을 써라"를 한 줄에 담습니다.
- **부작용이 큰 스킬은 `disable-model-invocation: true`** — 배포·커밋·알림 전송처럼 잘못 호출되면 곤란한 작업은 사용자 수동 호출만 받게.
- **본문은 짧게, 나머지는 부속 파일로** — 500줄 초과하면 별도 파일로 빼고 링크로 참조.

### 세션 간 인수인계 (3축 관점)
- **임시 상태는 `CONTINUE.md`에** — 오늘 작업 진행도, 미해결 버그, 다음 단계.
- **영속 지식은 CLAUDE.md에** — 아키텍처, 기술 스택, 규칙.
- **개인 기억은 메모리 시스템에** — LLM이 자동 갱신하는 사용자 프로필·피드백.

---

## 9. LLM의 메모리 병목: KV 캐시와 대역폭

> KV 캐시의 수식 유도, MoE·Hybrid Attention에서의 실제 크기, GPU 대역폭 병목의 상세 내용은 [파라미터 착시: KV 캐시의 진실](../theory/kv-cache.md)과 본문 [§8](../../README.md#8-소프트웨어의-한계-하드웨어의-가능성)에서 다룹니다.

핵심 포인트만 요약:

- **파라미터(가중치) ≠ 추론 시 메모리 전부** — 컨텍스트가 길어지면 KV 캐시가 파라미터를 10배 이상 초과할 수 있음
- **병목은 연산이 아니라 대역폭** — KV 캐시가 클수록 메모리→연산 코어 데이터 이동 속도가 발목을 잡음

### 9.1 에이전트 설정과의 연결

이 하드웨어 제약이 앞서 설명한 설정 원칙들과 직결됩니다:

| 설정 습관 | 메모리 효과 |
|---|---|
| CLAUDE.md를 짧게 유지 | 정적 KV 캐시 감소 → 동적 작업에 쓸 공간 증가 |
| 1 세션 = 1 작업 | 세션 재시작 시 KV 캐시 완전 초기화 |
| Skill을 필요할 때만 로드 | 불필요한 정적 콘텐츠 제거 |
| `/clear` 로 문맥 초기화 | 누적된 동적 레이어 제거 |

**"좋은 설정 = 메모리를 아끼는 설정"** 이기도 합니다. 하드웨어 관점에서 어떤 기기가 얼마만큼의 KV 캐시를 감당할 수 있는지는 [LLM 구동의 주요 스펙: 메모리 용량과 대역폭](../hardware/memory-bandwidth.md)에서 정리합니다.

---

## 10. 더 알아보기

### 공식 자료
- **Claude Code 홈**: [claude.ai/code](https://claude.ai/code)
- **공식 문서**: [code.claude.com/docs/en](https://code.claude.com/docs/en) — Hooks·Skills·Commands·Settings 등 모든 레퍼런스. 각 기능의 필드명·이벤트 스펙은 이 문서가 최종 기준입니다.
- **세션 내 도움말**: `/help` — 현재 세션에서 바로 확인 가능한 기능 목록
- **이슈 리포트·피드백**: [github.com/anthropics/claude-code/issues](https://github.com/anthropics/claude-code/issues)

### 관련 주제 (강의 심화 트랙)
- **KV 캐시와 컨텍스트 윈도우** — §9에서 기초를 다뤘습니다. 더 깊이 들어가면: FlashAttention, PagedAttention, speculative decoding 등 KV 캐시 최적화 기법. [KV 캐시의 진실](../theory/kv-cache.md) 참조.
- **MCP (Model Context Protocol)** — 외부 시스템과 에이전트 연결
- **추론 모델 심화** — §5.8에서 소개한 Extended Thinking의 내부 구조, adaptive / manual thinking 모드 선택 전략, thinking effort 조절. [리즈닝 모델의 딜레마](../theory/reasoning-models.md) 참조.
- **에이전트 평가(Evaluation)** — 내 에이전트 설정이 실제로 좋은지 측정하는 법

---

## 11. 부록

### A. 더미 트리 템플릿 (청중 복사·붙여넣기용)

자기 환경에 맞게 "작업 루트"와 "프로젝트" 이름을 치환해서 쓰면 됩니다.

```
~/.claude/
+-- CLAUDE.md                    <- 전역 지침
+-- settings.json                <- 전역 설정 (hook, permissions)
+-- keybindings.json             <- 키보드 단축키 (선택)
+-- skills/                      <- 사용자 커스텀 스킬
|   +-- my-skill/
|       +-- SKILL.md
+-- projects/                    <- 세션 기록·메모리
    +-- <working-dir>/
        +-- memory/
            +-- MEMORY.md

~/work-root-X/
+-- CLAUDE.md                    <- 환경 공통 지침
+-- project-name/
    +-- CLAUDE.md                <- 프로젝트 지침
    +-- CONTINUE.md              <- 다음 세션 인수인계 (선택)
    +-- .claude/
        +-- settings.json        <- 프로젝트 전용 설정
        +-- skills/              <- 프로젝트 전용 스킬 (선택)
            +-- project-skill/
                +-- SKILL.md
```

### B. 용어 사전

| 용어 | 설명 |
|------|------|
| **LLM** | Large Language Model. 텍스트 입출력의 대규모 언어 모델. |
| **에이전트(Agent)** | LLM을 감싸 도구 호출·반복·상태 관리를 수행하게 만든 실행체. |
| **하네스(Harness)** | 에이전트를 구성하는 실행 환경·안전 장치 계층. |
| **컨텍스트 윈도우** | LLM이 한 번에 읽을 수 있는 토큰 한도 (현세대 수십만 ~ 1M 토큰). |
| **KV 캐시** | 트랜스포머 추론 시 이전 토큰의 키·값을 캐싱한 메모리. 세션이 길어질수록 폭증. |
| **Hook** | Claude Code의 이벤트 기반 자동화 훅. `SessionStart`, `PreToolUse` 등 20여 종. |
| **Skill** | `/<name>` 형식으로 호출하는 특화 작업 모드. 지시사항 파일을 동적 로드. |
| **MCP (Model Context Protocol)** | 외부 시스템과 LLM을 연결하는 표준 프로토콜. |
| **플러그인(Plugin)** | 스킬·에이전트·훅·MCP 서버를 하나의 패키지로 묶어 배포하는 단위. |
| **메모리(Memory)** | 세션 간 지속되는 사용자 프로필·피드백 저장소. |
| **Compaction** | 세션이 길어져 컨텍스트가 꽉 차면 과거 대화를 요약해 공간을 확보하는 과정. |

### C. settings.json 샘플 조각 모음 (개인 정보 제거 버전)

#### C-1. 최소 구성

```json
{
  "permissions": {
    "defaultMode": "auto"
  }
}
```

#### C-2. 읽기 전용 명령 allowlist

```json
{
  "permissions": {
    "allow": [
      "Bash(ls:*)",
      "Bash(pwd)",
      "Bash(git status:*)",
      "Bash(git diff:*)",
      "Bash(git log:*)",
      "Bash(cat:*)",
      "Bash(grep:*)"
    ]
  }
}
```

#### C-3. Hook 3종 패키지 (stdin JSON 방식)

```json
{
  "hooks": {
    "SessionStart": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "git -C \"$CLAUDE_PROJECT_DIR\" status --short 2>/dev/null || true"
          }
        ]
      }
    ],
    "PreToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "jq -r '.tool_input.file_path // empty' | grep -qE '\\.prod\\.(env|yaml|json)$' && { echo 'BLOCKED: .prod.* 파일 직접 편집 금지' >&2; exit 2; } || exit 0"
          }
        ]
      }
    ],
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "F=$(jq -r '.tool_input.file_path // empty'); case \"$F\" in *.py) ruff check --fix \"$F\" 2>/dev/null || true ;; *.js|*.ts) eslint --fix \"$F\" 2>/dev/null || true ;; esac"
          }
        ]
      }
    ]
  }
}
```

> 필드명·이벤트 스펙은 버전마다 달라질 수 있습니다. 최신 스펙은 [공식 문서](https://code.claude.com/docs/en/hooks)로 확인하세요.

#### C-4. 플러그인 활성화

```json
{
  "enabledPlugins": {
    "swift-lsp@claude-plugins-official": true,
    "clangd-lsp@claude-plugins-official": true
  }
}
```

---

## 마무리

세 축을 한 줄로 다시 정리합니다:

> **CLAUDE.md** 는 "항상 말해주는 배경 규칙",
> **Hooks** 는 "말 안 해도 자동으로 일어나는 습관",
> **Skills** 는 "부르면 달려오는 (또는 LLM이 알아서 부르는) 전문가 모드"

좋은 에이전트 설정은 세 축을 **역할에 맞게 분배**하는 데서 나옵니다. 모든 것을 CLAUDE.md에 욱여넣으면 길어지고 잊히고, 모든 것을 hook으로 만들면 디버깅 지옥이 오며, 모든 것을 skill로 만들면 쓰려고 할 때마다 찾아 헤맵니다.

**내 작업 흐름에서 무엇이 "항상"이고, 무엇이 "자동"이고, 무엇이 "가끔"인지** — 이 질문에 답할 수 있으면 절반은 끝난 겁니다.
