---
type: goal
id: 1776774421
created: 2026-04-21
weak_dimensions: [G1]
---

# product-lab-adoption

## Target outcome (G1)

product-lab 프로젝트(`/Users/minhyeok/conductor/workspaces/product-lab/`)에 Polaris를 정착시키는 것이 성공하면 achieved.

Observable raw signal은 git log로 verify 가능:
- `product-lab/polaris/` 디렉토리가 존재하고 유지됨
- commit 빈도와 `polaris/` 하위 변경 이력
- `mission.md` 완전 재작성 / `polaris/` 디렉토리 wipe 같은 "갈아엎기"가 없음

단, achieved 판정의 최종 threshold — "몇 일 연속", "Spec N개 done", "갈아엎기의 정확한 정의" — 는 본인이 실사용 경험 쌓은 후 결정한다. 이 자의성 때문에 **G1은 WEAK**로 기록.

## Connection to Mission (G2)

Mission 문장의 **"1인 개발자"** 부분과 Riskiest strategic assumption("1인 개발자는 자기 프로젝트에 대해 조직 전체의 전략적 사고를 하고 싶어한다")을 본인 **N=1 사례로 확증/반증**하는 실험.

- 본인이 product-lab에서 Polaris 정착에 성공 = assumption이 최소 N=1에서 참
- 실패하면 **제작자 본인도 정착 못 하는 도구** → Mission 자체가 misguided

## Not this (G3)

1. **`/retro`, `/archaeology` 등 추가 커맨드 구현은 포함하지 않는다.** v0.1.0 기능만으로 정착 시도. (단, Goal #2 `lifecycle-command-completeness`에서 reactive하게 커맨드가 추가될 수는 있음.)
2. **GUI dashboard 작업은 포함하지 않는다.** 로드맵에 있지만 이 Goal의 범위 밖.
3. **본인 외 다른 1인 개발자의 validation은 포함하지 않는다.** N=1 실험, 외부 사용자 검증은 향후 별도 Goal의 일.

**In scope 명시:** Marketplace publishing은 포함 — "정식 publish된 Polaris가 product-lab에서 사용되는" 상태까지가 정착의 의미.

## Riskiest strategic bet (G4)

**"Polaris v0.1.0 기능(Mission + Goal + Spec, /init·/goal·/spec 3개 커맨드)만으로 정착이 가능하다."**

본인이 이미 의심 중인 bet — 정착 시도 과정에서 `/retro` 등 추가 커맨드 필요성이 blocker로 드러날 것으로 예상. 이 bet이 틀리면 이 Goal 추구 중 "Polaris 자체 추가 개발" 사이드 퀘스트에 시간이 녹아서 "정착 가능한가"의 순수 검증을 못 하는 risk가 있다. Goal #2가 이 bet 실패 시의 완충재 역할을 일부 수행.

## Outcome notes

<!--
Filled on completion (when this Goal moves to achieved/ or abandoned/).
Record: the observed outcome, whether G4's bet held, what this means for
Mission or subsequent Goals. Leave empty until completion.
-->
