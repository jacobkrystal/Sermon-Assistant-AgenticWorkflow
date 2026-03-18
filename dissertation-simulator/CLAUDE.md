# Dissertation Simulator — Claude Code 지시서

Claude Code 기반의 박사학위 논문 작성 시뮬레이터.

## 최종 목표

박사과정 연구자가 연구문제 설정부터 최종 원고·구술심사 준비까지 AI 에이전트 팀의 체계적 지원을 받아 **학문적으로 엄밀하고 독창적인 박사논문**을 완성하는 것.

## 절대 기준 (부모 DNA 유전 — 변경 불가)

### 절대 기준 1: 최종 결과물의 품질
> **속도, 토큰 비용, 작업량, 분량 제한은 완전히 무시한다.** 유일한 기준은 **최종 논문의 학문적 품질**이다.

### 절대 기준 2: 단일 파일 SOT + 계층적 메모리 구조
> 모든 공유 상태는 단일 파일(SOT)에 집중. SOT 쓰기는 Orchestrator만. 병렬 에이전트의 동일 파일 동시 수정 금지.

### 절대 기준 3: 코드 변경 프로토콜 (CCP)
> 코드를 작성·수정·추가·삭제하기 전에 Step 1(의도 파악) → Step 2(영향 범위 분석) → Step 3(변경 설계)를 내부적으로 수행.

### 절대 기준 간 우선순위
> **절대 기준 1(품질)이 최상위**. 2(SOT)와 3(CCP)은 품질을 보장하기 위한 동위 수단.

---

## 프로젝트 구조

```
dissertation-simulator/
├── CLAUDE.md                  ← 이 파일
├── AGENTS.md                  ← 모든 AI 에이전트 공통 지시서
├── README.md                  ← 프로젝트 개요
├── soul.md                    ← DNA 유전 정의
├── DECISION-LOG.md            ← 설계 결정 로그
├── prompt/workflow.md         ← Dissertation Research Workflow v1.0
├── .claude/
│   ├── settings.json
│   ├── agents/                ← 11개 에이전트 정의
│   ├── commands/              ← 슬래시 커맨드
│   ├── hooks/scripts/         ← _dissertation_lib.py 등
│   └── skills/dissertation-orchestrator/
├── translations/
│   ├── glossary.yaml
│   └── academic-glossary.yaml
└── dissertation-output/       ← 런타임 산출물 (gitignored)
```

## 자연어 라우팅 (Smart Router)

| 사용자 입력 패턴 | 라우팅 |
|----------------|--------|
| "시작", "start", "논문 써줘" | `/dissertation-start` |
| "연구문제 설정", "RQ 정하자" | `/dissertation-select-rq` |
| "문헌 검토", "lit review" | `/dissertation-review-lit` |
| "방법론 정하자", "methodology" | `/dissertation-set-method` |
| "심사 준비", "defense prep" | `/dissertation-defense` |

## 설계 원칙 (부모 DNA 유전)

1. **P1 — 정확도를 위한 데이터 정제**: 인용·통계 전달 전 Python으로 노이즈 제거
2. **P2 — 전문성 기반 위임 구조**: 전문 에이전트에게 위임, Orchestrator는 조율만
3. **P3 — 이미지/리소스 정확성**: 정확한 인용 경로 명시, placeholder 불가
4. **P4 — 질문 설계 규칙**: 최대 4개 질문, 각 3개 선택지. 모호함 없으면 질문 없이 진행

## Autopilot Mode

HITL 체크포인트를 자동 승인하는 모드. `state.yaml`의 `autopilot.enabled: true`로 활성화.

**4계층 품질 보장**: L0(Anti-Skip Guard) → L1(Verification Gate) → L1.5(DQAS Self-Rating) → L2(Calibration)

## 언어 및 스타일 규칙

- **사용자 대화**: 한국어
- **워크플로우 실행**: 영어 (AI 성능 극대화 — 절대 기준 1 근거)
- **최종 산출물**: 사용자 지정 언어 (기본: 영어 학술 논문체)
- **기술 용어**: 영어 유지 (SOT, HITL, DQAS 등)

## Context Preservation

세션 시작 시 `[CONTEXT RECOVERY]` 표시되면 안내된 파일을 **반드시 Read tool로 읽어** 이전 맥락을 복원.

| Hook 이벤트 | 동작 |
|------------|------|
| SessionStart | 이전 세션 상태 복원 |
| PostToolUse | 작업 로그 누적 |
| Stop | 증분 스냅샷 저장 |
| PreCompact / SessionEnd | 전체 스냅샷 저장 |
