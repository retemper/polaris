---
type: spec
id: 1777509722
goal: 1776774422-lifecycle-command-completeness
related_issues: []
branch: devKangMinHyeok/spec-issue-hierarchy
created: 2026-04-30
weak_dimensions: []
---

# spec-folder-hierarchy

## What changes (S1)

- `commands/spec.md`: Step 8 직전에 새 단계 추가 — "이 Spec이 더 큰 Spec의 조각인가?". Yes면 `polaris/specs/{planned,in-progress,done,canceled}/` 안의 후보 부모 Spec(또는 그룹 폴더)을 나열해 사용자에게 선택받음. 선택 시 Spec 파일 경로가 `polaris/specs/planned/{parent-slug}/{timestamp}-{slug}.md`로 결정됨. No면 기존 평면 경로 그대로.
- `CLAUDE.md.snippet`: Spec 위계는 폴더로 표현된다는 규칙 추가. Trigger-action 형식. 부모 슬러그 = 그룹 폴더명. 자식 상태가 부모와 갈리면 같은 이름의 그룹 폴더가 다른 status 디렉토리에 함께 등장. `parent_spec:` 같은 frontmatter 필드는 도입하지 않으며, 부모 변경은 `git mv`로만.
- 프로젝트 `CLAUDE.md` (이 repo의):
  - "Load-bearing design principles" 섹션에 항목 추가 — "Spec hierarchy via subfolder, never frontmatter".
  - POLARIS-managed 섹션(`<!-- POLARIS-START -->` ~ `<!-- POLARIS-END -->`)을 `CLAUDE.md.snippet` 최신 내용과 sync. (사전 drift도 같이 정리 — `/polaris` 관련 룰 누락이 있었음.)
- `README.md`: 5번째 줄 v0.2.0 → v0.3.0, "Creating a Spec" 절에 부모 Spec 선택 단계 언급 추가, "Layout (this repo)" 트리에 group folder 표현 한 줄 추가.
- `.claude-plugin/plugin.json`: `version` `0.2.0` → `0.3.0`.
- `templates/spec.md`: 변경 없음.

## Done criteria (S2)

- `claude plugin validate .` 통과.
- 스크래치 target repo에서 `/spec` 실행 시 부모 Spec 선택 단계가 나타나고, "no parent" 선택 시 평면 경로, 부모 선택 시 `polaris/specs/planned/{parent-slug}/{timestamp}-{slug}.md`에 파일이 생성됨.
- 자식 Spec을 `git mv polaris/specs/planned/{parent-slug}/x.md polaris/specs/in-progress/{parent-slug}/x.md` 했을 때 `find polaris/specs -path "*/{parent-slug}/*"`가 부모와 두 위치의 자식들을 모두 반환.

## Out of scope (S3)

- `parent_spec:` frontmatter 필드는 도입하지 않는다 — 폴더가 유일한 canonical. (대안 검토는 했고 의식적으로 배제.)
- 3-level 이상 nesting (grandchild) 지원하지 않는다 — parent → children 한 단계만.
- `/reparent`, `/regroup` 같은 명령어 추가하지 않는다 — 부모 변경은 `git mv` 수동.
- 기존 평면 Spec을 자동으로 폴더 그룹으로 변환해주는 마이그레이션 도구는 만들지 않는다.

## Why now (S4)

사용자가 product-lab dogfooding 중 "Spec이 비대해져 N개로 쪼개고 싶지만 묶일 곳이 없다"는 통증을 직접 보고함. `lifecycle-command-completeness` Goal의 G1("Goal #1 수행 중 추가 커맨드 요구가 더 이상 발생하지 않는 상태")에 기여 — reactive하게 떠오른 한 항목 처리. 다른 두 통증(Spec 의존성, Issue 중복)은 별도 Spec으로 분리.

## User / consumer (S5)

- 1인 개발자 (본인 + product-lab 사용 시) — Spec을 만들고 폴더 구조로 큰 일을 묶는 주체.
- AI agent — `find polaris/specs -path "*/<parent-slug>/*"` 같은 filesystem 쿼리로 그룹 단위 컨텍스트를 자연스럽게 수집.

## Riskiest assumption (S6)

"비대 Spec 쪼개기는 자주 일어나며, 폴더 nesting이 사용자에게 frontmatter 링크보다 자연스럽다."

틀리면: 기능을 만들었는데 거의 사용되지 않음 — 폴더 만드는 마찰이 귀찮아 사용자가 그냥 평면을 유지함. 이 경우 reactive-over-speculative 원칙의 사후 위반 사례가 되고, "이 기능을 reactive에서 출발했다고 봤지만 실은 speculative였다"는 신호.

## Implementation notes

<!--
Filled on completion (when this Spec moves to done/ or canceled/).
Record: lessons learned, decisions made during implementation, deviations
from the original Spec, and anything future work on this codebase should know.
Leave empty until completion.
-->
