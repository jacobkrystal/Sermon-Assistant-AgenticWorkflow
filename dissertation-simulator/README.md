# Dissertation Simulator

**AI 기반 박사학위 논문 작성 시뮬레이터** — 연구 주제 선정부터 최종 구술심사 준비까지, 11개 박사급 전문 에이전트가 체계적으로 논문 연구를 돕습니다.

[AgenticWorkflow](../AGENTICWORKFLOW-ARCHITECTURE-AND-PHILOSOPHY.md) 프레임워크(만능줄기세포)에서 분화된 자식 시스템입니다.
부모의 전체 DNA(절대 기준, 품질 보장, 안전장치, 기억 체계)를 구조적으로 내장하면서,
박사논문 연구 도메인에 특화된 GRA(Grounded Research Architecture)로 할루시네이션을 원천 차단합니다.

## 핵심 기능

- **11개 전문 연구 영역**: 연구문제 설계, 체계적 문헌고찰, 연구격차 분석, 연구방법론, 논지 설계, 챕터 집필, 학술 리뷰, 인용 관리, 통계 자문, 논문 심사 시뮬레이션, 구술심사 대비
- **GRA 3계층 품질 보증**: Agent Self-Verification → Cross-Validation Gates → DQAS 4축 평가
- **Hallucination Firewall**: 인용·통계·학술적 주장 생성 시점에서 검증 패턴 차단
- **GroundedClaim 스키마**: 모든 연구 결과에 출처·신뢰도·불확실성 구조화
- **6개 HITL 체크포인트**: 연구문제 확정 → 문헌고찰 검토 → 방법론 승인 → 챕터 아웃라인 → 초고 검토 → 최종 심사
- **3가지 입력 모드**: 주제 기반(Topic), 연구문제 직접 입력(RQ), 논문 시리즈(Series)
- **스타일 학습**: 지도교수·학술지 요구사항 분석 → 문체·형식 반영
- **Context Reset Recovery**: 세션 중단 시 자동 복구 (`/dissertation-resume`)

## 빠른 시작

```bash
git clone https://github.com/idoforgod/Dissertation-Simulator-AgenticWorkflow.git
cd Dissertation-Simulator-AgenticWorkflow
claude          # Claude Code 실행
```

```
# 주제 기반 시작
/dissertation-start topic AI 윤리와 공정성 — 의료 분야 알고리즘 편향 연구

# 연구문제 직접 입력
/dissertation-start rq "의료 AI 시스템에서 알고리즘 편향이 소수 집단의 진단 정확도에 미치는 영향은?"

# 논문 시리즈 계획
/dissertation-start series 사회과학 계열 박사논문 — 혼합연구방법 적용 사례

# 또는 자연어로
시작하자
```

## 워크플로우 구조

```
Phase 0: 초기화 → 3-File Architecture 설정
    │
Phase 1: Research (문헌 및 연구 기반 구축)
    ├── Wave 1 (병렬): 체계적 문헌고찰, 연구격차 분석, 이론적 틀
    │       └── Cross-Validation Gate 1
    ├── Wave 2 (병렬): 연구방법론 설계, 데이터 수집 계획, 분석 전략
    │       └── Cross-Validation Gate 2
    ├── Wave 3 (병렬): 윤리 검토, 타당도·신뢰도 평가, 선행연구 비교
    │       └── Cross-Validation Gate 3
    ├── Wave 4 (순차): 논지 구조화, 기여도 명확화
    │       └── DQAS 4축 평가
    └── 연구 종합 (2000-2500자 압축)
    │
Phase 2: Planning → 챕터 구조 설계 → 핵심 논지 도출 → 아웃라인 설계
    │
Phase 2.5: Style Analysis (조건부) → 지도교수/학술지 스타일 반영
    │
Phase 3: Implementation → 챕터 집필 → 품질 검토 → 최종 원고
    │
Phase 4: Defense Prep (조건부) → 구술심사 시뮬레이션 → Q&A 대비
```

## 프로젝트 구조

```
dissertation-simulator/
├── README.md                                         ← 이 파일 (자식 시스템 진입점)
├── CLAUDE.md                                         ← Claude Code 전용 지시서
├── AGENTS.md                                         ← 모든 AI 에이전트 공통 지시서
├── soul.md                                           ← DNA 유전 정의 (도메인 특화)
├── DECISION-LOG.md                                   ← 설계 결정 로그 (ADR)
├── prompt/
│   └── workflow.md                                   ← Dissertation Research Workflow v1.0
├── .claude/
│   ├── settings.json                                 ← Hook 설정
│   ├── agents/                                       ← 11개 에이전트 정의
│   │   ├── research-question-designer.md             (연구문제 설계)
│   │   ├── literature-reviewer.md                    (체계적 문헌고찰)
│   │   ├── gap-analyst.md                            (연구격차 분석)
│   │   ├── methodology-advisor.md                    (연구방법론 자문)
│   │   ├── thesis-architect.md                       (논지 구조 설계)
│   │   ├── chapter-writer.md                         (챕터 집필)
│   │   ├── academic-reviewer.md                      (학술 리뷰)
│   │   ├── citation-manager.md                       (인용 관리)
│   │   ├── statistics-advisor.md                     (통계 자문)
│   │   ├── defense-simulator.md                      (심사 시뮬레이션)
│   │   ├── committee-simulator.md                    (심사위원 시뮬레이션)
│   │   ├── reviewer.md                               (공통: 적대적 리뷰어)
│   │   └── translator.md                             (공통: 번역 에이전트)
│   ├── commands/                                     ← 슬래시 커맨드
│   │   ├── dissertation-start.md                     (/dissertation-start)
│   │   ├── dissertation-select-rq.md                 (/dissertation-select-rq)
│   │   ├── dissertation-review-lit.md                (/dissertation-review-lit)
│   │   ├── dissertation-set-method.md                (/dissertation-set-method)
│   │   ├── dissertation-confirm-thesis.md            (/dissertation-confirm-thesis)
│   │   ├── dissertation-approve-outline.md           (/dissertation-approve-outline)
│   │   ├── dissertation-set-format.md                (/dissertation-set-format)
│   │   ├── dissertation-finalize.md                  (/dissertation-finalize)
│   │   ├── dissertation-defense.md                   (/dissertation-defense)
│   │   ├── dissertation-status.md                    (/dissertation-status)
│   │   ├── dissertation-resume.md                    (/dissertation-resume)
│   │   └── start.md                                  (Smart Router)
│   ├── hooks/scripts/                                ← Hook + 검증 스크립트
│   │   └── _dissertation_lib.py                      (논문 워크플로우 결정론적 라이브러리)
│   └── skills/
│       └── dissertation-orchestrator/SKILL.md        (논문 오케스트레이터 스킬)
├── translations/
│   ├── glossary.yaml                                 ← 번역 용어 사전
│   └── academic-glossary.yaml                        ← 학술 번역 용어 사전
└── dissertation-output/                              ← 워크플로우 산출물 (gitignored)
    └── [topic-YYYY-MM-DD]/                           ← 논문별 하위 디렉터리
        ├── research-package/                         (연구 결과물)
        ├── chapters/                                 (챕터 초고)
        ├── pacs-logs/                                (품질 평가 로그)
        └── session.json                              (도메인 상태 SOT)
```

## 에이전트 팀 (11명)

| 에이전트 | 전문 영역 | Wave |
|---------|----------|------|
| **research-question-designer** | 연구문제 명확화, PICO/SPIDER 프레임 적용 | Phase 0 |
| **literature-reviewer** | 체계적 문헌고찰, PRISMA 프로토콜 | Wave 1 |
| **gap-analyst** | 연구격차 및 기여도 분석 | Wave 1 |
| **methodology-advisor** | 질적·양적·혼합연구 방법론 | Wave 2 |
| **thesis-architect** | 논지 구조 및 논리 흐름 설계 | Wave 2 |
| **chapter-writer** | 학술 챕터 집필 (서론~결론) | Phase 3 |
| **academic-reviewer** | 적대적 학술 리뷰, 논리 취약점 발굴 | Phase 3 |
| **citation-manager** | APA/MLA/Chicago 인용 형식 관리 | 전체 |
| **statistics-advisor** | 통계 분석 전략, R/Python 분석 설계 | Wave 2-3 |
| **defense-simulator** | 구술심사 질문 시뮬레이션 | Phase 4 |
| **committee-simulator** | 심사위원 관점 비판적 검토 | Phase 4 |

## 커맨드 레퍼런스

| 커맨드 | 설명 | HITL |
|--------|------|------|
| `/dissertation-start` | 논문 작성 워크플로우 시작 | - |
| `/dissertation-select-rq` | 연구문제 선정 및 범위 설정 | HITL-1 |
| `/dissertation-review-lit` | 문헌고찰 결과 검토 | HITL-2 |
| `/dissertation-set-method` | 연구방법론 확정 | HITL-3 |
| `/dissertation-confirm-thesis` | 핵심 논지 확정 | HITL-3b |
| `/dissertation-approve-outline` | 챕터 아웃라인 승인 | HITL-4 |
| `/dissertation-set-format` | 형식 및 분량 설정 | HITL-5a |
| `/dissertation-finalize` | 최종 검토 및 완료 | HITL-5b |
| `/dissertation-defense` | 구술심사 시뮬레이션 | HITL-6 |
| `/dissertation-status` | 진행 상태 확인 | - |
| `/dissertation-resume` | 컨텍스트 리셋 후 재개 | - |

## DQAS (Dissertation Quality Assurance System)

설교연구의 SRCS 평가에 대응하는 논문 품질 4축 평가 시스템:

| 축 | 기준 | 약어 |
|----|------|------|
| **Scholarly Rigor** | 학문적 엄밀성·인용 정확성 | Sr |
| **Argument Coherence** | 논지 일관성·논리 흐름 | Ac |
| **Methodological Soundness** | 방법론 타당성 | Ms |
| **Contribution Clarity** | 연구 기여도 명확성 | Cc |

**grade = min(Sr, Ac, Ms, Cc)** — 가장 낮은 축이 전체 등급을 결정.

## 절대 기준 (AgenticWorkflow DNA 유전)

1. **품질 최우선** — 속도, 비용, 작업량보다 최종 논문의 학문적 품질이 유일한 기준
2. **단일 파일 SOT** — state.yaml + session.json + todo-checklist.md 계층 구조
3. **코드 변경 프로토콜 (CCP)** — 결정론적 검증 라이브러리 기반
4. **품질 > SOT, CCP** — 세 기준이 충돌하면 품질이 우선
