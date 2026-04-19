---
layout: post
title: OpenClaw Dreaming - AI 가 스스로 기억을 정리하는 백그라운드 시스템
date: 2026-04-19
author: dosuser
tags: [OpenClaw, AI, 메모리, Dreaming, 자동화, 개발자도구, 생산성, 인공지능]
---

안녕하세요! 오늘은 OpenClaw 의 핵심 기능인 **Dreaming**에 대해 소개합니다.

한 줄 요약:

> "단기 기억에서 중요한 것만 골라 장기 기억으로 자동 승격시키는, 인간 두뇌의 수면 학습을 모방한 메모리 통합 시스템"

---

## 목차

1. [왜 Dreaming 이 필요한가?](#1-왜-dreaming-이-필요한가)
2. [3 단계 페이즈 모델](#2-3-단계-페이즈-모델)
3. [Deep 랭킹: 중요도 측정 방법](#3-deep-랭킹-중요도-측정-방법)
4. [Dream Diary: 사람이 읽는 일지](#4-dream-diary-사람이-읽는-일지)
5. [파일 구조](#5-파일-구조)
6. [실제 사용 시나리오](#6-실제-사용-시나리오)
7. [설정 및 사용법](#7-설정-및-사용법)

---

## 1. 왜 Dreaming 이 필요한가?

AI 어시스턴트와 장시간 작업하다 보면 이런 상황이 발생합니다:

> **사용자**: "나 타입스크립트 선호한다고 말했던 것 같은데?"  
> **AI**: "죄송합니다. 이전 대화 기록을 찾을 수 없습니다."

기존 AI 는 세션이 끝나면 모든 맥락이 사라집니다. OpenClaw 는 `MEMORY.md` 파일에 중요한 정보를 저장하지만, 무엇을 저장할지를 매번 사용자가 명시해야 했습니다.

Dreaming 은 이 문제를 백그라운드 자동화로 해결합니다. 인간의 수면 중 기억 정리 과정 (Light sleep → REM → Deep sleep) 에서 영감을 받았습니다.

---

## 2. 3 단계 페이즈 모델

Dreaming 은 매일凌晨 3 시 (기본 설정) 에 자동으로 실행되며, 다음 순서로 진행됩니다:

```
┌─────────────────────────────────────────────────────────┐
│  Light Phase  →  REM Phase  →  Deep Phase              │
│  (수집/스테이징)  (패턴추출)    (승격판정)               │
└─────────────────────────────────────────────────────────┘
         ↓                ↓               ↓
    DREAMS.md       DREAMS.md      MEMORY.md
    (일시적)        (일시적)        (영구)
```

![3 단계 페이즈 모델](/images/openclaw-dreaming/01-three-phases.png)

### Light 페이즈: "오늘 뭐 있었지?"

**목적**: 최근 자료 분류 및 스테이징

**동작**:
- 지난 2 일간 작성된 `memory/YYYY-MM-DD.md` 파일 스캔
- 세션 트랜스크립트에서 개인정보 제거 후 수집
- 반복적으로 언급된 키워드/주제 추출
- 아직 판단하지 않음. 그냥 모아두기만 함

**출력 예시** (DREAMS.md):
```
- Candidate: "TypeScript 선호, JavaScript 는 legacy 프로젝트만"
  - confidence: 0.72
  - evidence: memory/2026-04-18.md:12-15
  - recalls: 3
```

### REM 페이즈: "패턴이 보이네"

**목적**: 주제 및 반복 패턴 추출

**동작**:
- Light 에서 수집된 후보들 간의 상관관계 분석
- recurring theme (반복 주제) 식별
- "이 사용자가 진짜 중요하게 생각하는 것은 무엇인가?" 성찰

**출력 예시** (DREAMS.md):
```
### Reflections
- Theme: `typescript` kept surfacing across 5 memories.
  - confidence: 0.85
  - evidence: memory/2026-04-17.md:4-8, memory/2026-04-18.md:12-15

### Possible Lasting Truths
- TypeScript 선호, JavaScript 는 legacy 프로젝트만 [confidence=0.78]
```

### Deep 페이즈: "이건 영구 보관"

**목적**: 영구 승격 판정 및 MEMORY.md 기록

**동작**:
- 6 가지 가중치 신호로 최종 스코어 계산
- 임계값 (gate) 을 모두 통과한 항목만 승격
- 중요: 승격 전 live 파일에서 스니펫 재확인 (삭제된 항목 제외)

**승격 기준** (모두 통과 필요):

| 게이트 | 기본값 | 설명 |
|--------|--------|------|
| minScore | ≥ 0.8 | 6 점 만점 가중합 |
| minRecallCnt | ≥ 3 회 | 반복 언급 |
| minUniqueQ | ≥ 3 개 | 다양한 컨텍스트 |

![승격 기준 표](/images/openclaw-dreaming/07-phase-gates.png)

---

## 3. Deep 랭킹: 중요도 측정 방법

Dreaming 은 6 가지 신호를 가중 합산하여 최종 스코어를 계산합니다:

| 신호 | 가중치 | 설명 | 예시 |
|------|--------|------|------|
| Relevance | 0.30 | 평균 검색 품질 | 검색 시 상위 |
| Frequency | 0.24 | 누적 신호 횟수 | 5 회 이상 언급 |
| Query diversity | 0.15 | 고유 쿼리 컨텍스트 | 다양한 주제 |
| Recency | 0.15 | 시간 감쇠 신선도 | 오늘 언급 |
| Consolidation | 0.10 | 다일 반복 강도 | 3 일 연속 |
| Conceptual richness | 0.06 | 개념 태그 밀도 | 태그 다수 |

**계산식**:
```
score = (0.30 × relevance) + (0.24 × frequency) + ... + (0.06 × conceptual)
```

![Deep 랭킹 스코어링 차트](/images/openclaw-dreaming/02-ranking-score.png)

### Phase Signal Boost

Dreaming 페이즈에서 히트된 항목은 추가 부스트를 받습니다:

- Light hit: 최대 +0.06 (14 일 반감기)
- REM hit: 최대 +0.09 (14 일 반감기)

이는 "인간의 수면 중 기억 강화" 현상을 모방한 것입니다.

---

## 4. Dream Diary: 사람이 읽는 일지

Dreaming 은 기계 상태 외에도 사람이 읽을 수 있는 일지를 생성합니다.

![Dream Diary 예시](/images/openclaw-dreaming/03-dream-diary.png)

### 어떻게 생성되는가?

각 페이즈 완료 후, memory-core 는 서브에이전트를 실행합니다:

1. 페이즈 결과 수집 (스니펫, 테마, 승격 항목)
2. 서브에이전트에 전달 (60 초 타임아웃)
3. 내러티브 일지 생성 → DREAMS.md 에 추가
4. 120 초 후 세션 정리 (자동 삭제)

### 일지 프롬프트의 특성

서브에이전트는 다음 지침을 따릅니다:

- **페르소나**: 호기심 많고 부드러운 성찰자
- **톤**: 시적이면서도 프로그래머적인 감성
- **규칙**:
  - "꿈꾸고 있다"는 메타 언급 금지
  - AI/모델 관련 자기언급 금지
  - 마크다운 금지 (평문만)
  - 80-180 단어

### 예시 출력

> 오늘은 타입스크립트와 레거시 코드에 대해 많이 생각했다.
> 오전에는 빌드 설정을 만지다가, 오후에는 lint 규칙을 정리했다.
> 흥미로운 점은 매번 "JavaScript 는 기존 프로젝트만"이라고 스스로에게
> 되뇌었다는 것이다. 마치 오래된 친구에게 반복해 말하듯.
>
> 타입세팅은 단순한 선호를 넘어, 안정성에 대한 집착처럼 보인다.
> 14 일 전에도 비슷한 기록이 있다. 그땐 뭐라고 했지?
> "런타임 에러는 미리 잡는다" — 옳은 말이다.
>
> (서버 허밍 소리가 들리는 듯하다. 오늘도 많은 코드가 오갔다.)

---

## 5. 파일 구조

Dreaming 이 사용하는 파일 구조입니다:

![파일 구조](/images/openclaw-dreaming/04-file-structure.png)

```
agent-workspace/
├── MEMORY.md                          # 장기 기억 (영구)
├── DREAMS.md                          # Dream Diary (사람이 읽음)
├── memory/
│   ├── 2026-04-18.md                  # 일일 메모리
│   ├── 2026-04-19.md
│   └── .dreams/                       # 머신 상태 (내부용)
│       ├── short-term-recall.json     # 단기 리콜 엔트리
│       ├── phase-signals.json         # 페이즈 히트 신호
│       ├── daily-ingestion.json       # 일일 수집 상태
│       ├── session-ingestion.json     # 세션 수집 상태
│       └── short-term-promotion.lock  # 동시성 제어
└── .openclaw-repair/
    └── dreaming/                      # 복구 아카이브
```

### 저장소 모드

| 모드 | Light/REM 출력 위치 | 용도 |
|------|---------------------|------|
| inline | memory/YYYY-MM-DD.md 내 블록 | 일일 메모리 |
| separate | memory/dreaming/<phase>/YYYY-MM | 별도 리포트 |
| both | 두 위치 모두 | 중복 기록 |

기본값은 `separate` 입니다.

---

## 6. 실제 사용 시나리오

### 시나리오: 개발자의 코딩 선호도 학습

![사용 시나리오 타임라인](/images/openclaw-dreaming/06-scenario-timeline.png)

**Day 1**: 사용자가 "TypeScript 로 작성해줘" 요청
  → `memory/2026-04-18.md` 에 기록
  → Light Phase: "typescript" 키워드 수집 (recallCount=1)

**Day 2**: "JS 말고 TS 로" 재차 언급
  → `memory/2026-04-19.md` 에 기록
  → Light Phase: "typescript" 재수집 (recallCount=2)

**Day 3**: "타입 안전성 중요해" 3 번째 언급
  → REM Phase: 패턴 식별 ("typescript" 테마, confidence=0.82)
  → Deep Phase: score=0.85 → 승격 조건 충족
  → `MEMORY.md` 에 기록: "사용자는 TypeScript 를 선호한다"

---

## 7. 설정 및 사용법

### 활성화 (기본값: 비활성화)

Dreaming 은 옵트인 기능입니다. 다음 설정으로 활성화합니다:

```json
{
  "plugins": {
    "entries": {
      "memory-core": {
        "config": {
          "dreaming": {
            "enabled": true,
            "timezone": "Asia/Seoul",
            "frequency": "0 3 * * *"
          }
        }
      }
    }
  }
}
```

### Slash Command

```bash
/dreaming status    # 현재 상태 조회
/dreaming on        # 활성화
/dreaming off       # 비활성화
```

### CLI: 승격 미리보기 및 적용

```bash
openclaw memory promote           # 승격 후보 미리보기
openclaw memory promote --limit 10        # 상위 10 개만 확인
openclaw memory promote --apply           # 실제 적용
openclaw memory promote-explain "키워드"  # 판정 이유 설명
openclaw memory rem-harness               # REM 성찰 미리보기
openclaw memory rem-harness --json        # JSON 출력
```

---

## 아키텍처

전체 아키텍처 플로우입니다:

![아키텍처 다이어그램](/images/openclaw-dreaming/05-architecture.png)

---

## 기존 vs Dreaming 비교

| 기존 | Dreaming 적용 후 |
|------|-----------------|
| 사용자가 명시적으로 "기억해"라고 말해야 함 | 백그라운드에서 자동 수집 |
| 모든 세션 기록이 동일하게 취급 | 신호 강도에 따라 선별 |
| 컴팩션 시 중요한 맥락 소실 | 컴팩션 전에 자동 저장 |
| 사람이 직접 정리 | AI 가 스스로 정리, 사람이 검토 |

![기존 vs Dreaming 비교](/images/openclaw-dreaming/08-comparison-table.png)

---

## 관련 링크

- OpenClaw 공식 문서: https://docs.openclaw.ai
- GitHub Repo: https://github.com/openclaw/openclaw

---

*작성일: 2026-04-19*  
*저자: 신대용 (daeyong.shin@navercorp.com)*
