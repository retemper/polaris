---
type: spec
id: 1777375767
goal: 1776774422-lifecycle-command-completeness
related_issues: []
branch: devKangMinHyeok/review-profile-cmd
created: 2026-04-28
weak_dimensions: []
---

# review-command-with-profiles

## What changes (S1)

`/review` 슬래시 커맨드와 "review profile" 개념을 Polaris에 추가한다. Profile은 사용자가 자기 프로젝트에 맞춰 작성하는 markdown 파일로, AI agent가 변경된 파일을 어떤 관점으로 리뷰할지 정의한다. Profile은 코드 리뷰뿐 아니라 전략 리뷰 등 사용자가 정의하기 나름이며 — Polaris는 빈 scaffolding만 제공한다 (bundled profile 없음).

**추가**

- `commands/review.md` — 새 slash command. Profile 수집 → 매칭 → 리뷰 실행. Profile 없거나 부족하면 inline interview.
- `templates/review-profile.md` — minimal scaffolding. Frontmatter (`name:`, `file_patterns:`) 만 강제, 본문은 자유 markdown.

**수정**

- `commands/init.md` — Part E (optional / target 1 / skip 허용) 추가: Review profile interview. Step 7 file-write에 `polaris/reviews/profiles/.gitkeep` 생성 추가.
- `commands/polaris.md` — Step 5 next-move suggestions rules에 "no review profile yet → `Run /review to create one.`" 케이스 추가. **Read-only 원칙 보존** (파일 생성 X, suggestion line만).
- `CLAUDE.md.snippet` — `/review` trigger-action 규칙 1–2개 추가.
- `.claude-plugin/plugin.json` — minor version bump.
- `README.md` — `/review` + Review profile 섹션 추가, 명령어 목록에 `/review` 포함.

**관찰 가능한 동작**

- `/init` 끝 무렵: "Review profile도 지금 하나 만들까요?" 질문. yes → interview → `polaris/reviews/profiles/{slug}.md` 생성. skip 시 파일 생성 없이 종료.
- `/polaris` 호출 시 `polaris/reviews/profiles/` 가 비어 있으면 next-move suggestions에 `No review profile yet. Run /review to create one.` 라인 출력. 파일 수정 없음.
- `/review` (인자 없음) → 변경 파일 (main 대비 + staged + unstaged) 수집 → 각 profile의 frontmatter `file_patterns:` 와 매칭 → 매칭 OK면 해당 profile(들)로 리뷰. 매칭 안 되거나 부족한 파일이 있으면 "이 파일에 맞는 profile을 추가할까요?" 질문. profile 0개 상태에서는 first-run interview → profile 생성 → 리뷰.
- `/review {name}` — 해당 profile로 리뷰 (변경 파일이 frontmatter 매칭에 부합하지 않더라도 명시 호출은 그대로 실행).

**원칙 영향 (의도된 변경)**

- "**Five commands, one bootstrap**" 원칙 부분 완화 — `/init` 외에도 `/review` 가 `polaris/reviews/profiles/` 하위에 한해 파일 생성 가능. `polaris/` 디렉토리 자체는 여전히 `/init` 만 생성.
- `/polaris` 의 "read-only compass" 원칙은 **보존** — suggestion 라인만 추가, 파일은 안 건드림.

## Done criteria (S2)

**자동 검증**

- [ ] `claude plugin validate .` 통과.

**Scratch repo 수동 검증 (dry-run)**

- [ ] `/init` 끝 무렵 Part E 질문 등장. yes 선택 시 `polaris/reviews/profiles/{slug}.md` 생성. skip 선택 시 파일 생성 없이 종료.
- [ ] Profile 없는 상태로 `/polaris` 실행 → next-move suggestions에 `No review profile yet. Run /review to create one.` 라인 출력. `polaris/` 하위 어떤 파일도 수정/생성되지 않음 (read-only 보존 확인).
- [ ] Profile 없는 상태로 `/review` 실행 → first-run interview → `polaris/reviews/profiles/{slug}.md` 생성 + 변경 파일 기준 리뷰 출력.
- [ ] Profile 있는 상태로 `/review` → frontmatter `file_patterns:` 매칭 → 리뷰 수행.
- [ ] `/review {name}` → 해당 profile 단독 실행.
- [ ] 변경 파일 중 어느 profile에도 매칭되지 않는 파일이 있을 때 → "이 파일에 맞는 profile을 추가할까요?" prompt 출현.

**N=1 dogfood (product-lab)**

- [ ] product-lab 에서 review profile 최소 1개 생성 (코드/전략 무관).
- [ ] 해당 profile로 `/review` 1회 실행, 개선 포인트 1개 이상 도출.

## Out of scope (S3)

- **Bundled profile 제공** — `templates/review-profile.md` 는 빈 scaffolding만. `code` / `design` / `copy` 같은 예시 profile은 ship 하지 않는다.
- **Review log 파일 저장** — `/review` 결과는 터미널 출력만. `polaris/reviews/logs/` 같은 영속화는 별도 Spec.
- **Multi-profile 동시 실행 최적화 / 충돌 해결 매트릭스** — florence-v5 식 우선순위 표 (e.g. SEO vs Copy 충돌 시 누가 우선) 는 v1에 포함하지 않는다. 매칭된 profile들을 순차 실행, 충돌은 user가 읽고 판단.
- **`/retro` 연동** — review 결과가 done Spec 회고로 feedback 되는 ritual은 v0.1.0 "not in" 목록의 `/retro` 와 묶여 있음. 별도 Spec.

## Why now (S4)

지금 product-lab에서 florence-v5의 review skill을 쓰고 있어서 review 부하가 Polaris 밖에 머물고 있다 — product-lab 정착의 friction이며, 이 상태가 굳어지면 Polaris의 정체성이 "전략 레이어 전용"으로 고착된다.

`/review`는 Goal #2 (`lifecycle-command-completeness`) G1 — *"Goal #1 추구 중 실제로 필요해진 커맨드 = shipped"* — 의 정확한 trigger 정의에 부합한다 (product-lab adoption 과정에서 reactive하게 surface된 첫 추가 커맨드 요구). 동시에 Goal #2 G4 bet — *"lifecycle은 command set으로 커버 가능"* — 의 결정적 data point: profile 같은 declarative 구조가 hook이나 watcher 없이 review 같은 lifecycle 면을 cover할 수 있는지를 처음으로 N=1 검증한다.

부수적으로 Goal #1 G1 (`product-lab/polaris/` 실사용 evidence — 파일 수, commit 빈도, 갈아엎기 없음) 도 review 부하 흡수를 통해 전진한다.

## User / consumer (S5)

1차: **1인 개발자 (본인 포함)** — 지금까지 프로젝트별로 review skill을 scratch부터 만들어 쓰던 부하를, Polaris가 제공하는 표준 구조 위에서 profile만 작성하면 해결. Review를 AI agent와 "profile"이라는 공용 계약으로 공유.

2차: **product-lab 프로젝트 자체** — review 실행 문턱이 낮아져 review 누락이 줄고, 코드/전략 품질이 review 피드백을 더 자주 받음.

## Riskiest assumption (S6)

**Frontmatter (`name:`, `file_patterns:`) + 자유 markdown 본문** 만으로 AI agent가 (a) 변경 파일에 맞는 적절한 profile을 매칭하고 (b) 본문의 자유 서술된 페르소나 / 체크리스트를 읽어 일관되고 쓸만한 review를 생성할 수 있다.

이 가정이 틀리면 — 본문이 자유 markdown이라 AI가 일관된 review를 못 만들거나, frontmatter `file_patterns:` 만으로는 매칭이 너무 거칠면 — profile 구조를 더 강하게 정의해야 하거나(philosophy의 `ai-agents-primary-consumer` 원칙을 본문에까지 깊이 적용), `/review` 출력이 florence-v5 rich-scaffolding 버전보다 현저히 얕아 user가 다시 scratch skill로 돌아간다. 양쪽 모두 이 PR을 의미 없게 만든다.

## Implementation notes

<!--
Filled on completion (when this Spec moves to done/ or canceled/).
Record: lessons learned, decisions made during implementation, deviations
from the original Spec, and anything future work on this codebase should know.
Leave empty until completion.
-->
