---
type: mission
owner: Kang Minhyeok
phase: Discovery
created: 2026-04-21
---

# Mission

Polaris는 1인 개발자가 조직 전체의 전략적 사고(mission → goal → task)를 혼자 감당할 수 있도록, 그 인지 부하를 AI agent와 공유하는 strategy-as-code 프레임워크다.

# Anti-strategy

Things this codebase will explicitly *never* do, even when tempting. Concrete boundaries, not platitudes.

1. 대규모 팀을 타깃하지 않는다. 1인 개발자용 도구이며, 팀 협업 기능 요청은 기본적으로 거절.
2. 비-AI 워크플로우 / Claude Code 이외의 AI 환경을 지원하지 않는다. Claude Code를 사용한다고 가정하며, portability를 위해 Claude Code 특화 기능(예: `${CLAUDE_PLUGIN_ROOT}`, 슬래시 커맨드)을 포기하지 않는다.
3. Time-based quarterly OKR planning을 도입하지 않는다. 날짜 기반 목표 관리는 stale/abandoned 문제를 재현하며, directory-as-status 철학과 충돌한다.
4. Multi-user approval workflow를 추가하지 않는다. Spec/Goal review, 서명, 승인 큐 같은 조직 협업 기능은 1인 개발자 포커스와 충돌.
5. Polaris 자체를 SaaS/hosted service로 만들지 않는다. 수익화 유혹이 와도 filesystem-first, git-native 원칙을 포기하지 않는다.
6. 기존 PM tool (Linear / Jira / Notion)의 대체나 경쟁자를 지향하지 않는다. 기능 확장 pressure가 와도 strategy-as-code 범위를 넘어가지 않는다.

# Current phase

**Discovery**

현재 v0.1.0은 Discovery 가설을 검증하기 위한 프로토타입이다. 구조·커맨드·철학 어느 것이든 실사용 증거에 따라 갈아엎거나 크게 바꿀 수 있음을 전제로 한다. Build phase는 "방향이 맞다는 실사용 증거가 있고 이제 성실히 구현하는 단계"인데, 아직 그 증거는 없다.

# Riskiest strategic assumption

1인 개발자는 실제로 자기 프로젝트에 대해 조직 전체의 전략적 사고를 하고 싶어한다. 그냥 coding만 하고 싶은 게 아니라, mission/goal/anti-strategy 같은 메타 레이어를 관리하는 게 가치 있다고 느낀다. 이 가정이 틀리면 — 즉 1인 개발자가 메타 레이어 관리를 burden으로만 느끼면 — Polaris의 존재 이유 자체가 사라진다.
