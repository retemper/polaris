---
type: goal
id: 1776774422
created: 2026-04-21
weak_dimensions: [G1]
---

# lifecycle-command-completeness

## Target outcome (G1)

Goal #1 (`product-lab-adoption`) 추구 중 **실제로 필요해진 커맨드만 reactive하게 구현 → 전부 shipped** 된 상태. G1 signal은 "Goal #1 수행 중 추가 커맨드 요구가 더 이상 발생하지 않는 상태".

- Observable raw signal: product-lab 작업 중 발생한 "필요해진 command" 목록이 모두 implemented + validation 통과
- achieved 판정 시점은 Goal #1의 종료 시점과 커플링됨 — Goal #1이 achieved/abandoned 되기 전에는 이 Goal도 판정 불가

Goal #1 G1의 WEAK를 상속해서 **G1은 WEAK**로 기록. 주관적 threshold가 Goal #1에 의존.

## Connection to Mission (G2)

Mission 문장의 **"mission → goal → task"** 전체 흐름과 **"인지 부하를 AI agent와 공유"**의 완결성을 advance.

- `/init` → Mission 레이어
- `/goal` → Goal 레이어  
- `/spec` → Task(Spec) 생성
- **[v0.1.0 누락] /retro, lifecycle 전이 커맨드** → Task 이후 review/learning 흐름

Task 생성 이후 review/전이가 수동이면 "인지 부하 공유"가 task 생성 시점에서만 이뤄지고 **가장 부담스러운 review/learning 단계에선 공유 안 됨**. 이 Goal 실패 시 Mission의 "부하 공유" 약속이 부분적으로만 실현됨.

## Not this (G3)

**외부 사용자 피드백 반영한 커맨드 개선은 포함하지 않는다.**

Goal #1의 N=1 실험 원칙과 일관성 유지 — 본인 실사용에서 필요하다고 판단된 것만 shipping. 외부 피드백은 다른 Goal의 일.

## Riskiest strategic bet (G4)

**"lifecycle은 command set으로 커버 가능하다."**

진짜 필요한 건 command가 아니라 hook, watcher, ambient guard 같은 **다른 type of primitive**일 수 있다. 이 bet이 틀리면 Polaris의 command-centric 설계 자체가 재고 대상이 되며, "더 많은 command" 방향은 wrong lever였던 것으로 판명.

징후: /retro 같은 command를 만들었는데 실제로 친화적 운영에 영향이 없거나, 오히려 hook 기반 자동 트리거(예: `git mv` 시 자동 retrospective 유도)가 필요하다고 느껴지기 시작함.

## Outcome notes

<!--
Filled on completion (when this Goal moves to achieved/ or abandoned/).
Record: which commands actually got added, which requests never surfaced,
whether G4's "command set is the right primitive" bet held. Leave empty
until completion.
-->
