# Dissertation Research Workflow v1.0

박사학위 논문 연구문제 설정부터 최종 원고·구술심사 대비까지, 전문 에이전트 팀이 체계적으로 학술 연구를 지원하는 워크플로우.

**GRA(Grounded Research Architecture)** 기반 할루시네이션 방지 및 품질 보증 시스템 적용.

---

## Overview

- **Input**: 연구 주제(Topic) | 연구문제 직접 입력(RQ) | 논문 시리즈 계획(Series)
- **Output**: 연구 패키지 + 챕터 아웃라인 + 논문 원고 초안 + 심사 대비 자료
- **Frequency**: On-demand
- **Quality Level**: 국제 학술지 투고 수준 (토큰 비용 무관, 품질 최우선)
- **Architecture**: GRA + External Memory Strategy + DQAS 4축 평가

---

## 핵심 아키텍처

### 1. GRA (Grounded Research Architecture)

```
┌─────────────────────────────────────────────────────────────┐
│              GRA 3-Layer Architecture (Dissertation)         │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  Layer 1: Agent Self-Verification                            │
│  ├─ GroundedClaim 출력 스키마 준수                          │
│  ├─ Citation Firewall 통과 (미검증 인용 차단)               │
│  └─ Mini-DQAS 자기 평가                                     │
│                                                              │
│  Layer 2: Cross-Validation Gates                             │
│  ├─ Gate 1: Wave 1 → Wave 2 (문헌·격차 검증)               │
│  ├─ Gate 2: Wave 2 → Wave 3 (방법론·논지 검증)             │
│  └─ Gate 3: Wave 3 → Wave 4 (심층 분석 검증)               │
│                                                              │
│  Layer 3: DQAS Unified Evaluation                            │
│  ├─ Sr (Scholarly Rigor): 학문적 엄밀성                    │
│  ├─ Ac (Argument Coherence): 논지 일관성                   │
│  ├─ Ms (Methodological Soundness): 방법론 타당성            │
│  └─ Cc (Contribution Clarity): 연구 기여도                 │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### 2. External Memory Strategy (3-File Architecture)

```
┌─────────────────────────────────────────────────────────────┐
│                  3-File Architecture                         │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  state.yaml          ← 전체 워크플로우 상태 SOT             │
│  session.json        ← 현재 세션 도메인 상태                │
│  todo-checklist.md   ← 단계별 완료 체크리스트               │
│                                                              │
│  SOT 쓰기 권한: Orchestrator만                              │
│  읽기 권한: 모든 에이전트                                   │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### 3. HITL 체크포인트 (6개)

```
HITL-1: 연구문제 확정     ← /dissertation-select-rq
HITL-2: 문헌고찰 검토     ← /dissertation-review-lit
HITL-3: 방법론 승인       ← /dissertation-set-method
HITL-3b: 논지 확정        ← /dissertation-confirm-thesis
HITL-4: 아웃라인 승인     ← /dissertation-approve-outline
HITL-5a: 형식 설정        ← /dissertation-set-format
HITL-5b: 최종 검토        ← /dissertation-finalize
HITL-6: 구술심사 대비     ← /dissertation-defense
```

---

## Phase 0: 초기화

### Step 0-1: 입력 모드 분류
```
AGENT: Orchestrator
ACTION: classify_input_mode()
  - Topic mode: 연구 분야/주제만 주어진 경우
  - RQ mode: 구체적 연구문제가 주어진 경우
  - Series mode: 논문 시리즈 또는 연구 맥락이 주어진 경우
OUTPUT → session.json.input_mode
```

### Step 0-2: 3-File Architecture 초기화
```
AGENT: Orchestrator
ACTION:
  1. dissertation-output/[topic-YYYY-MM-DD]/ 디렉터리 생성
  2. state.yaml 초기화 (phase: 0, status: initializing)
  3. session.json 초기화 (domain_state, hitl_checkpoints)
  4. todo-checklist.md 초기화 (전체 단계 목록)
OUTPUT → 3-File Architecture 준비 완료
```

### Step 0-3: 연구 컨텍스트 수집 (HITL-1 준비)
```
AGENT: research-question-designer
ACTION: 연구 주제/RQ 명확화
  - 연구 분야, 연구문제, 연구대상, 연구기간 확인
  - PICO/SPIDER 프레임 적용 (해당 시)
  - 연구문제의 구체성·실현가능성 평가
OUTPUT → research_context.md
```

**[HITL-1: 연구문제 확정]** — `/dissertation-select-rq`
사용자가 연구문제·범위·연구방법 방향을 확인·수정

---

## Phase 1: Research (문헌 및 연구 기반 구축)

### Wave 1: 문헌 기반 구축 (병렬 실행)

```
┌─────────────────────────────────────────────────────────────┐
│                         Wave 1                              │
│                    (병렬 실행, ~20분)                        │
├──────────────────┬──────────────────┬───────────────────────┤
│ literature-      │ gap-analyst      │ theory-builder        │
│ reviewer         │                  │ (thesis-architect)    │
│                  │                  │                       │
│ - PRISMA 프로토콜│ - 연구격차 맵핑  │ - 이론적 틀 설계      │
│ - 핵심 문헌 50+  │ - 기여도 포지션  │ - 개념 프레임워크     │
│ - 인용 검증      │ - 연구 공백 식별 │ - 변수 관계 정의      │
└──────────────────┴──────────────────┴───────────────────────┘
                            │
                   Cross-Validation Gate 1
                            │
              Wave 1 결과가 상호 일관성 있는가?
              문헌·격차·이론이 논리적으로 연결되는가?
```

**Gate 1 통과 기준**: DQAS Sr ≥ 7, Ac ≥ 6

### Wave 2: 방법론 설계 (병렬 실행)

```
┌─────────────────────────────────────────────────────────────┐
│                         Wave 2                              │
│                    (병렬 실행, ~15분)                        │
├──────────────────┬──────────────────┬───────────────────────┤
│ methodology-     │ statistics-      │ thesis-architect      │
│ advisor          │ advisor          │                       │
│                  │                  │                       │
│ - 연구설계 선택  │ - 분석 전략      │ - 논지 구체화         │
│ - 데이터수집계획 │ - 표본 설계      │ - 챕터 구조 초안      │
│ - 타당도 검토    │ - 분석 도구      │ - 논리 흐름 맵        │
└──────────────────┴──────────────────┴───────────────────────┘
                            │
                   Cross-Validation Gate 2
                            │
              방법론이 연구문제와 이론적 틀에 정합하는가?
              논지 구조가 연구설계를 지지하는가?
```

**Gate 2 통과 기준**: DQAS Ms ≥ 7, Ac ≥ 6

**[HITL-2: 문헌고찰 검토]** — `/dissertation-review-lit`
**[HITL-3: 방법론 승인]** — `/dissertation-set-method`

### Wave 3: 심층 분석 (병렬 실행)

```
┌─────────────────────────────────────────────────────────────┐
│                         Wave 3                              │
│                    (병렬 실행, ~20분)                        │
├──────────────────┬──────────────────┬───────────────────────┤
│ academic-        │ citation-manager │ statistics-advisor    │
│ reviewer         │                  │                       │
│                  │                  │                       │
│ - 논리 취약점    │ - APA/MLA 형식   │ - 분석 결과 해석      │
│ - 반론 예측      │ - 인용 일관성    │ - 통계 타당성 검증    │
│ - 강화 제안      │ - DOI 검증       │ - 오류 경계 분석      │
└──────────────────┴──────────────────┴───────────────────────┘
                            │
                   Cross-Validation Gate 3
                            │
              학술적 엄밀성이 검증되었는가?
              인용이 모두 검증 가능한가?
```

**Gate 3 통과 기준**: DQAS Sr ≥ 8, Cc ≥ 7

### Wave 4: 논지 통합 (순차 실행)

```
AGENT: thesis-architect + research-question-designer
ACTION:
  1. Wave 1-3 결과 통합
  2. 핵심 논지(Thesis Statement) 최종 도출
  3. 연구 기여도 3가지 명확화
  4. 챕터별 핵심 주장 매핑

DQAS 전체 평가:
  - Sr (Scholarly Rigor): ___/10
  - Ac (Argument Coherence): ___/10
  - Ms (Methodological Soundness): ___/10
  - Cc (Contribution Clarity): ___/10
  - grade = min(Sr, Ac, Ms, Cc)
  → grade ≥ 7: Phase 2 진행
  → grade < 7: 해당 Wave 재실행
```

**[HITL-3b: 논지 확정]** — `/dissertation-confirm-thesis`

### 연구 종합 (Research Synthesis)

```
AGENT: Orchestrator (research-synthesizer 위임)
ACTION: 전체 Phase 1 결과를 2000-2500자로 압축
OUTPUT → research-package/synthesis.md
  - 핵심 논지 1문장
  - 연구 기여도 3가지
  - 챕터별 핵심 발견
  - 방법론 정당화
  - 인용 목록 (검증 완료)
```

---

## Phase 2: Planning (챕터 구조 설계)

### Step 2-1: 챕터 아웃라인 설계

```
AGENT: thesis-architect
INPUT: research-package/synthesis.md
ACTION:
  1. 표준 챕터 구조 (또는 사용자 지정)
     - Chapter 1: Introduction
     - Chapter 2: Literature Review
     - Chapter 3: Methodology
     - Chapter 4: Results/Findings
     - Chapter 5: Discussion
     - Chapter 6: Conclusion
  2. 각 챕터별 논지 포인트 3-5개
  3. 챕터 간 논리 연결 맵
  4. 예상 분량 (페이지 수)
OUTPUT → chapters/outline.md
```

**[HITL-4: 아웃라인 승인]** — `/dissertation-approve-outline`

### Step 2-2: 형식 및 스타일 설정

```
AGENT: Orchestrator (사용자 입력 기반)
INPUTS:
  - 지도교수 스타일 가이드 (있는 경우)
  - 목표 학술지 / 학교 논문 양식
  - 인용 스타일 (APA 7th / MLA / Chicago)
  - 목표 분량 (페이지 수)
OUTPUT → session.json.format_settings
```

**[HITL-5a: 형식 설정]** — `/dissertation-set-format`

---

## Phase 2.5: Style Analysis (조건부)

```
조건: 사용자가 기존 논문 샘플 또는 지도교수 요구사항 제공 시
AGENT: style-analyzer (chapter-writer에 위임)
ACTION:
  1. 제공된 논문의 문체·구조 패턴 분석
  2. 선호 표현 방식, 논증 패턴 추출
  3. 스타일 프로파일 생성
OUTPUT → session.json.style_profile
```

---

## Phase 3: Implementation (챕터 집필)

### Step 3-1: 챕터별 초고 작성

```
AGENT: chapter-writer
PER CHAPTER (순차 또는 지정 순서):
  1. 아웃라인 포인트 기반 초고 작성
  2. GroundedClaim 스키마 적용 (모든 주장에 근거)
  3. 인용 삽입 (검증된 것만)
  4. citation-manager와 인용 형식 동기화
OUTPUT → chapters/[chapter-N]-draft.md
```

### Step 3-2: 학술 리뷰

```
AGENT: academic-reviewer
ACTION:
  1. 각 챕터 적대적 리뷰
  2. 논리 취약점 발굴
  3. 반론 및 강화 제안
  4. DQAS 챕터별 평가

  DQAS 임계값:
  - grade ≥ 8: 다음 챕터 진행
  - 7 ≤ grade < 8: 소폭 수정 후 진행
  - grade < 7: 해당 챕터 재작성
OUTPUT → pacs-logs/chapter-N-review.md
```

### Step 3-3: 최종 통합 원고

```
AGENT: chapter-writer + Orchestrator
ACTION:
  1. 모든 챕터 통합
  2. 챕터 간 전환 검토
  3. 참고문헌 목록 완성
  4. Abstract 작성
  5. 전체 DQAS 최종 평가
OUTPUT → dissertation-draft.md
```

**[HITL-5b: 최종 검토]** — `/dissertation-finalize`

---

## Phase 4: Defense Preparation (구술심사 대비, 조건부)

### Step 4-1: 심사 질문 시뮬레이션

```
AGENT: defense-simulator
ACTION:
  1. 논문의 핵심 주장에 대한 예상 질문 20개 생성
  2. 방법론 취약점 공격 질문
  3. 기여도 정당화 질문
  4. 이론적 틀 도전 질문
OUTPUT → defense-prep/anticipated-questions.md
```

### Step 4-2: 심사위원 시뮬레이션

```
AGENT: committee-simulator (3명 페르소나)
  - Committee Member A: 방법론 전문가 (엄격)
  - Committee Member B: 이론 전문가 (비판적)
  - Committee Member C: 실무 전문가 (실용적)
ACTION:
  1. 각 페르소나별 Q&A 시뮬레이션
  2. 답변 전략 제안
  3. 취약점 보강 방안
OUTPUT → defense-prep/committee-simulation.md
```

**[HITL-6: 구술심사 대비]** — `/dissertation-defense`

---

## 품질 보증 (DQAS)

### DQAS 4축 평가

```yaml
dqas:
  scholarly_rigor:        # Sr — 학문적 엄밀성
    criteria:
      - 모든 주장에 검증된 인용
      - 할루시네이션 없는 통계
      - 방법론 근거 명시
    weight: 1.0

  argument_coherence:     # Ac — 논지 일관성
    criteria:
      - 연구문제 → 방법론 → 결과 → 결론 정합
      - 챕터 간 논리 흐름
      - 일관된 개념 사용
    weight: 1.0

  methodological_soundness:  # Ms — 방법론 타당성
    criteria:
      - 연구문제에 적합한 방법론
      - 타당도·신뢰도 검증
      - 한계점 솔직한 기술
    weight: 1.0

  contribution_clarity:   # Cc — 연구 기여도
    criteria:
      - 3가지 기여도 명확화
      - 기존 연구와 차별화
      - 실무·이론적 함의
    weight: 1.0

grade: min(Sr, Ac, Ms, Cc)
threshold:
  proceed: 7
  minor_revision: 7-7.9
  major_revision: 5-6.9
  reject: <5
```

### Hallucination Firewall (인용 특화)

```python
# 차단 패턴 (citation-manager가 검증)
BLOCKED_PATTERNS = [
    "studies show that",          # 미특정 인용
    "researchers have found",     # 주어 없는 연구 주장
    "it is widely accepted",      # 검증 불가 합의
    "according to experts",       # 미특정 전문가
    "recent studies indicate",    # 미특정 최신 연구
]

# 허용 패턴 (검증 가능한 인용)
ALLOWED_PATTERNS = [
    "Smith (2023) argues that...",
    "According to Jones et al. (2022)...",
    "The meta-analysis by Brown (2021)...",
]
```

---

## 에이전트 팀 구성

| 에이전트 | 역할 | Wave/Phase |
|---------|------|-----------|
| `research-question-designer` | 연구문제 명확화, PICO 프레임 | Phase 0 |
| `literature-reviewer` | 체계적 문헌고찰, PRISMA | Wave 1 |
| `gap-analyst` | 연구격차 맵핑, 기여 포지셔닝 | Wave 1 |
| `methodology-advisor` | 연구설계, 데이터수집 계획 | Wave 2 |
| `thesis-architect` | 논지 구조화, 챕터 아웃라인 | Wave 2, 4 |
| `statistics-advisor` | 분석 전략, 통계 타당성 | Wave 2-3 |
| `academic-reviewer` | 적대적 리뷰, 논리 검증 | Wave 3, Phase 3 |
| `citation-manager` | 인용 형식, Hallucination 차단 | 전체 |
| `chapter-writer` | 학술 챕터 초고 집필 | Phase 3 |
| `defense-simulator` | 심사 질문 시뮬레이션 | Phase 4 |
| `committee-simulator` | 심사위원 Q&A 시뮬레이션 | Phase 4 |

---

## 상태 관리 (state.yaml 스키마)

```yaml
version: "1.0"
phase: 0                    # 현재 Phase
status: "initializing"      # initializing/running/hitl_waiting/completed

input:
  mode: ""                  # topic/rq/series
  topic: ""
  research_question: ""
  discipline: ""            # 학문 분야
  degree_type: ""           # PhD/Masters

research:
  wave1_complete: false
  wave2_complete: false
  wave3_complete: false
  wave4_complete: false
  gate1_passed: false
  gate2_passed: false
  gate3_passed: false
  dqas_scores:
    Sr: 0
    Ac: 0
    Ms: 0
    Cc: 0
    grade: 0

planning:
  outline_approved: false
  format_set: false
  style_profile: null

implementation:
  chapters_drafted: []
  chapters_reviewed: []
  final_draft: false

defense:
  questions_generated: false
  simulation_complete: false

hitl:
  hitl1_rq: false
  hitl2_lit: false
  hitl3_method: false
  hitl3b_thesis: false
  hitl4_outline: false
  hitl5a_format: false
  hitl5b_final: false
  hitl6_defense: false

autopilot:
  enabled: false

outputs:
  research_package: ""
  outline: ""
  chapters: []
  dissertation_draft: ""
  defense_materials: ""
```

---

## 오류 처리

### Gate 실패 시
```
1. 실패 원인 진단 (Abductive Diagnosis)
2. 가장 낮은 DQAS 축 식별
3. 해당 축 전담 에이전트 재실행
4. 최대 재시도: 3회
5. 3회 실패 시 HITL 에스컬레이션
```

### 인용 검증 실패 시
```
1. citation-manager: 검증 불가 인용 격리
2. chapter-writer: 격리된 인용 제거/대체
3. 대체 인용 없을 시: "연구자 확인 필요" 표시
4. 절대 검증 불가 인용으로 최종 원고 작성 금지
```
