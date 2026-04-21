---
type: philosophy
owner: Kang Minhyeok
created: 2026-04-22
---

# Philosophy

Principles that remain invariant across Goal changes. These are identity-level commitments — the "how we operate" and "what we believe" beneath the Mission's "what and why".

Unlike Goals (which come and go as outcomes shift), Philosophy stays. If a principle changes, that is a signal to pause and reflect — not a routine update.

## filesystem-single-source-of-truth

**Statement:** Filesystem이 strategic state의 single source of truth다. 디렉토리가 status이며, 메타데이터나 인덱스가 filesystem reality를 override할 수 없다.

**Why it matters:** Spec/Goal 상태를 `status:` 프런트매터 필드로 표현하지 않고 디렉토리 위치(`planned/`, `in-progress/`, `done/`, `canceled/`; `active/`, `achieved/`, `abandoned/`)로만 표현한다. 향후 "검색 속도 개선용 JSON 인덱스", "Notion/Linear 양방향 동기화 허브" 같은 제안을 거부하는 근거. Lifecycle 전이는 `git mv` 외의 수단을 허용하지 않는다.

**Violation trigger:** active Goal이나 Spec이 수십 개 쌓여 `ls`가 느리게 느껴질 때 "한 번만 인덱스 JSON을 추가하자"는 유혹이 온다. `polaris/` 하위에 `.md` 이외의 state-bearing 파일(JSON, SQLite, 캐시)이 생성되는 순간이 관찰 가능한 violation marker다.

## ai-agents-primary-consumer

**Statement:** 이 코드베이스의 primary consumer는 AI agent다. 모든 format / 구조 / tone 선택은 에이전트의 신뢰성을 먼저, 인간 가독성을 그 다음으로 최적화한다.

**Why it matters:** 템플릿은 `frontmatter + 구조화된 H2 섹션 + 주석`으로 쓴다(자유 서술 거부). `CLAUDE.md.snippet`의 규칙은 "Before / When / If / Never" trigger-action 헤딩을 쓴다(서술형 prose 거부). Clarity Gate의 PASS/WEAK/FAIL rubric은 에이전트가 채점 가능하도록 명시된다. Command 파일은 "You are executing … follow exactly" step-by-step 프로시저로 작성된다.

**Violation trigger:** 향후 GUI dashboard 확장 계획이 현실화되면 "GUI 사용자에게 보기 좋은 별도 데이터 모델"을 만들고 싶은 유혹이 온다. Agent가 읽는 `polaris/*.md`와 GUI가 읽는 store가 divergence하는 순간 — 즉 agent-source와 별도의 human-source가 병존하기 시작하는 순간 — 이 원칙이 위반된 것이다. GUI는 agent-first files 위의 view로만 허용된다.

## reactive-over-speculative

**Statement:** 기능 / 커맨드 / 레이어는 실사용에서 blocker로 증명된 후에만 추가한다. 예측으로 미리 만들지 않는다.

**Why it matters:** v0.1.0의 "What's NOT in v0.1.0" 리스트(Initiative layer, `/retro`, `/archaeology` 등)가 이 원칙의 직접 산물이다. 개밥먹기 중 발견된 `/init` 개선 후보들을 별도 저장소 없이 reactive filter에 맡긴다. 향후 "다른 PM tool에 있는 기본 기능(burndown, roadmap view, 태깅)"이나 "나중에 쓸 것 같으니 구조만 미리 깔자" 유형의 제안을 거부하는 근거.

**Violation trigger:** "나중에 쓸 거 같으니 구조만 미리", "다른 툴에 다 있으니 기본으로 넣자"는 류의 PR justification이 등장한다. 반년 이상 실사용 없이 codebase에 머무는 커맨드 / 프런트매터 필드 / 레이어의 존재 자체가 violation marker다. Phase는 Build / Scale로 바뀔 수 있지만 이 원칙은 phase-invariant로 유지된다 — "실사용 증거 없이는 추가하지 않는다"는 Discovery의 하위 집합이 아니라 독립된 identity-level commitment다.
