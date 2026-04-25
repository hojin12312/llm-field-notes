# Claude Code 명령어 참조 (로컬 전용, gitignore)

강의 자료 작성 시 참조용. 웹 서치 없이 확인할 수 있도록 정리.
마지막 검증: 2026-04-25 (notes 전수 재검증 라운드, 호진님 직접 확인 항목 유지)

---

## 빌트인 슬래시 커맨드

| 명령어 | 설명 | 확인 |
|---|---|---|
| `/help` | 사용 가능한 명령어 목록 표시 | ✅ |
| `/clear` | 대화 히스토리 초기화 (컨텍스트 리셋) | ✅ |
| `/compact [지침]` | 컨텍스트를 요약 압축 (선택적으로 집중할 내용 지정 가능) | ✅ |
| `/cost` | 현재 세션 토큰 사용량·비용 표시 | ✅ |
| `/status` | 계정·시스템 상태 확인 | ✅ |
| `/model` | 사용 모델 변경 | ✅ |
| `/fast` | Fast 모드 토글 (Opus 4.6 기반, 출력 속도 향상) | ✅ |
| `/plan` | Plan 모드 토글 — 파일 수정 없이 계획만 제안 | ✅ |
| `/memory` | 메모리 파일 편집 | ✅ |
| `/init` | 현재 프로젝트에 CLAUDE.md 자동 생성 | ✅ |
| `/btw` | 메인 흐름을 건드리지 않고 곁가지 질문 처리 | ✅ (호진님 확인) |
| `/branch` | 현재 컨텍스트를 공유하면서 다른 방향 탐색 | ✅ (호진님 확인) |

---

## 스킬 기반 슬래시 커맨드

스킬은 사용자 또는 Anthropic이 정의한 확장 명령어. `/스킬명` 형태로 호출.

| 명령어 | 설명 |
|---|---|
| `/init` | CLAUDE.md 초기화 (빌트인과 동일) |
| `/review` | 현재 브랜치 PR 검토 |
| `/security-review` | 보안 검토 |
| `/simplify` | 변경된 코드 품질·간결성 검토 및 수정 |
| `/loop [간격] [명령어]` | 명령어 반복 실행 (예: `/loop 5m /review`) |
| `/schedule` | 예약 원격 에이전트 설정 |
| `/update-config` | settings.json 변경 (hooks, 권한 설정 등) |
| `/keybindings-help` | 키보드 단축키 커스터마이징 |
| `/fewer-permission-prompts` | 자주 쓰는 명령어 허용 목록 자동 등록 |
| `/claude-api` | Claude API / Anthropic SDK 관련 작업 |

---

## 키보드 단축키

| 단축키 | 동작 |
|---|---|
| `Shift + Tab` | Plan 모드 토글 |
| `Esc` | 현재 작업 중단 |
| `↑` | 이전 명령어 불러오기 |

---

## 특수 입력 방식

| 형태 | 동작 |
|---|---|
| `@파일명` | 파일을 컨텍스트에 직접 주입 (예: `@CONTINUE.md`) |
| `! 명령어` | 셸 명령어를 세션에서 직접 실행 후 결과를 대화에 포함 |
| `#태그` | 메모리 태그 (사용 방식은 별도 확인 필요) |

---

## 강의 자료 내 언급 현황

- README.md 섹션 6: `/btw`, `/branch`, `/plan` 설명
- README.md 섹션 7: `Shift+Tab`, `/plan` 설명
- appendix/tools/claude-code-3-axes.md: CLAUDE.md·Hooks·Skills·MCP 상세

---

## 미확인 / 업데이트 필요 항목

- `#태그` 입력 방식 정확한 동작 미확인
- 스킬 목록은 설치 환경마다 다를 수 있음 (커스텀 스킬 혼재)
- `/compact` 옵션 파라미터 정확한 문법 재확인 권장

> 공식 문서: https://code.claude.com/docs/en (2026-04-25 기준, 구 `docs.anthropic.com/en/docs/claude-code` 및 `docs.claude.com/en/docs/claude-code` 는 이 주소로 301 리다이렉트)
