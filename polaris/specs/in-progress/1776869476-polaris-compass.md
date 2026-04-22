---
type: spec
id: 1776869476
goal: 1776774422-lifecycle-command-completeness
related_issues: []
branch: feat/1776869476
created: 2026-04-22
weak_dimensions: []
---

# polaris-compass

## What changes (S1)

다음 파일이 추가/수정된다:

- **새 파일**: `commands/polaris.md` — `/polaris` 슬래시 커맨드 정의. step-by-step procedure (Claude Code 가 실행).
- **수정**: `CLAUDE.md.snippet` — "사용자가 confusion signal (예: '뭐 해야지', '지금 뭐가 필요해', '어디까지 했지') 보이면 `/polaris` 제안" 규칙 추가.
- **수정**: `.claude-plugin/plugin.json` — version bump.
- **수정**: `README.md` — `/polaris` 문서 추가 (5개 커맨드 섹션 옆).

관찰 가능한 동작:
- `/polaris` 입력 → phase + active Goals + in-progress Specs + open Issues 요약 + 상태 기반 1–3개 next-move 제안 출력.
- `/polaris <맥락 문자열>` 입력 → 기본 출력 + 맥락 반영한 specific 조언 (interactive 확장).
- `polaris/mission.md` 없는 repo 에서 실행 → "`/init` 먼저" 안내.

## Done criteria (S2)

- `claude plugin validate .` 통과.
- Polaris 세팅된 repo 에서 `/polaris` 실행 → phase + active Goals + in-progress Specs + open Issues 요약 + next-move 제안 출력 확인.
- Polaris 미세팅 repo 에서 `/polaris` 실행 → `/init` 안내 출력 확인.
- `/polaris test 실패 중` 같은 맥락 arg 주고 실행 → 기본 출력 + 맥락 반영한 specific 조언 출력 확인.
- `CLAUDE.md.snippet` diff 에 "confusion signal 시 `/polaris` 제안" 규칙 존재 확인.

## Out of scope (S3)

다음은 이번 PR 에서 **명시적으로 안 함**:

- 다른 커맨드 (`/goal`, `/spec`, `/init`, `/issue`, `/philosophy`) 의 "Confirm-on-ambiguous" Step 0 추가 — 별도 Spec.
- `/help-polaris` 같은 커맨드 목록 전용 커맨드 — `/polaris` 가 부분적으로 커버한다고 가정.
- `/polaris` 가 제안한 next action 의 자동 실행 — 제안만 출력, 실행은 사용자 입력.
- 세션 시작 시 `/polaris` auto-run — 사용자가 명시적으로 칠 때만.
- GUI/웹 dashboard — Goal #1 G3.2 와 일치.

## Why now (S4)

Kang 실사용 중 "뭐 해야할지 모르겠을때" moment 가 방금 발생 — Goal #2 G1 이 정의한 "Goal #1 추구 중 실제로 필요해진 커맨드" 의 reactive trigger 가 작동한 실제 사례.

## User / consumer (S5)

1차 수혜자: **Kang 본인** — product-lab 에서 Polaris 쓰는 1인 개발자. "뭐 해야지" 순간마다 compass 가 filesystem 과 다음 커맨드 쪽으로 방향 잡아줌.

## Riskiest assumption (S6)

길 잃은 사용자가 실제로 `/polaris` 를 치고(A), 그 시점에 LLM 이 filesystem scan 기반으로 유용한 next-move 제안을 한다(B). 둘 중 하나라도 틀리면 compass 역할 실패.

- A 가 틀리면 (아무도 안 침 / AI 에 직접 묻고 말음): `/polaris` 는 dead code. delivery 메커니즘 재설계 필요 (auto-greet, session-start trigger 등).
- B 가 틀리면 (제안이 매번 generic): Goal #2 G4 bet ("lifecycle 은 command set 으로 커버 가능") 흔들림. command 가 맞는 primitive 인지 재고 대상.

falsification 방법: A 는 실사용 usage 관찰, B 는 실제 제안 품질 review.

## Implementation notes

<!--
Filled on completion (when this Spec moves to done/ or canceled/).
Record: lessons learned, decisions made during implementation, deviations
from the original Spec, and anything future work on this codebase should know.
Leave empty until completion.
-->
