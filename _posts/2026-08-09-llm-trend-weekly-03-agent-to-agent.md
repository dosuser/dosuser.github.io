---
layout: post
title: "LLM 트렌드 2주 결산 3편 — Agent-to-Agent: A2A 프로토콜의 성숙과 거버넌스 과제 (2026-07-26 ~ 08-09)"
date: 2026-08-09
author: dosuser
description: "MCP와 A2A를 중심으로 2026년 AI 에이전트 프로토콜 생태계, 표준화 현황, 멀티에이전트 거버넌스 과제를 정리합니다."
tags: [A2A, MCP, 에이전트프로토콜, 멀티에이전트, LinuxFoundation, 거버넌스, "2026"]
---

> **시리즈**: LLM 에이전트 트렌드 2주 결산 (2026-07-26 ~ 2026-08-09)

한 줄 요약:

> "A2A v1.0이 150개 조직에 채택된 와중에, 거버넌스 연구자들이 프로토콜의 한계를 공식 논문으로 제출했다. 표준화가 빠를수록 빈 공간도 빨리 드러난다."

---

## 시리즈 목차

1. [Hot Source: 오픈소스 모델·논문·프레임워크](/posts/llm-trend-weekly-01-hot-source/)
2. [Agent Loop: 루프 패턴의 진화](/posts/llm-trend-weekly-02-agent-loop/)
3. **이 글** — Agent-to-Agent (A2A): 성숙과 거버넌스 과제
4. [Harness & Evaluation 2주 동향](/posts/llm-trend-weekly-04-harness/)

---

## 1. 2026년 에이전트 프로토콜 생태계

에이전트가 혼자 도는 시대는 끝났다. 에이전트들이 서로 대화하고, 태스크를 위임하고, 결과를 조율하는 시대다.

| 레이어 | 프로토콜 | 개발사 | 상태 |
|--------|---------|--------|------|
| 에이전트 ↔ 도구/데이터 | **MCP** | Anthropic → Linux Foundation | ✅ 97M+ 월간 다운로드, 5,800+ 서버 |
| 에이전트 ↔ 에이전트 | **A2A** | Google → Linux Foundation | ✅ v1.0 (2026-04), 150+ 조직 |
| 에이전트 클러스터 | **ACP** | IBM | 📄 초안 |
| 에이전트 ↔ 결제·경제 | **AP2** | — | 🔬 연구 단계 |

**핵심 관계**: MCP로 도구를 호출하고, A2A로 다른 에이전트에게 태스크를 위임한다. 두 프로토콜은 경쟁 관계가 아니라 레이어가 다른 보완 관계다.

---

## 2. A2A 프로토콜 핵심 구조

A2A는 HTTP 기반 JSON 메시지 교환으로 동작한다.

**Agent Card** — 에이전트가 자신을 광고하는 문서:

```json
{
  "agent_id": "research-agent-001",
  "capabilities": ["web_search", "document_analysis", "summarization"],
  "input_formats": ["text", "json"],
  "authentication": "oauth2",
  "endpoint": "https://agent.example.com/a2a"
}
```

**태스크 생명주기**:
```
created → in_progress → completed / failed
```

**주요 통신 패턴**:

```
패턴 1: Orchestrator-Worker
[PM 에이전트] → A2A 위임 → [리서치 에이전트]
              → A2A 위임 → [코딩 에이전트]

패턴 2: Peer-to-Peer 협업
[에이전트 A] ↔ A2A ↔ [에이전트 B]
(한쪽이 막히면 다른 쪽에 도움 요청)

패턴 3: 에이전트 마켓플레이스 (이론 단계)
[서비스 에이전트들] → [에이전트 마켓] ← [사용자 에이전트]
```

---

## 3. 7월 말 — CORAL: 자연어 A2A로 규칙 기반 워크플로우 대체

**"Beyond Rule-Based Workflows: Information-Flow-Orchestrated Multi-Agents via A2A Communication from CORAL"**
[arxiv.org/abs/2601.09883](https://arxiv.org/abs/2601.09883)

기존 멀티에이전트 시스템은 워크플로우가 사전 정의된다. CORAL은 이를 **자연어 A2A 통신을 통한 동적 정보 흐름 오케스트레이션**으로 대체한다.

```
기존 Rule-Based:
Agent_A → [고정 파이프라인] → Agent_B → Agent_C

CORAL 동적 A2A:
Agent_A ←→ [자연어 A2A] ←→ Agent_B
                               ↕
                          Agent_C
(정보 흐름이 실시간 협의로 결정)
```

중앙 오케스트레이터 없이 에이전트들이 필요한 정보를 자율적으로 주고받는다.

---

## 4. 8월 8일 — Agentic Economies: A2A가 경제 시스템으로 확장

**"From Economic Agents to Agentic Economies: Theory and Architecture"**
[huggingface.co/papers/2608.06020](https://huggingface.co/papers/2608.06020) | 28 upvotes

A2A 통신의 다음 단계를 이론화한 논문. 에이전트들이 서로 **거래·경쟁·협력하는 경제 시스템**을 제안한다.

```
Agentic Economy 구조:

[서비스 에이전트들]
Agent_Search ──┐
Agent_Code  ──┼──→ [에이전트 마켓플레이스] ←→ [사용자 에이전트]
Agent_Data  ──┘
     ↑
  A2A Protocol (통신) + AP2 Protocol (결제)

상호작용 단계:
1. Agent Discovery (서비스 공고)
2. Bidding via A2A (입찰·협상)
3. Task Execution (실행 계약)
4. AP2 Payment (결제 정산)
5. Reputation System (평판 누적)
```

현재 A2A v1.0은 태스크 위임 레벨이지만, 이 논문은 A2A가 **에이전트 마켓플레이스의 기반 프로토콜**이 될 수 있음을 이론적으로 제시한다.

---

## 5. 8월 9일 — Governance Gaps: A2A v1.0의 한계가 공식화됐다

**"Governance Gaps in Agent Interoperability Protocols: What MCP, A2A, and ACP Cannot Express"**
[arxiv.org/pdf/2606.31498](https://arxiv.org/pdf/2606.31498)

A2A v1.0 채택이 본격화되자마자 한계 연구가 나왔다. 현재 표준 3종이 **표현할 수 없는 거버넌스 요구사항**을 정리한 논문이다.

| 프로토콜 | 가능한 것 | 불가능한 것 |
|---------|---------|-----------|
| **MCP** | 도구 스키마 정의, 인증된 도구 호출 | Policy 위반 시 자동 거부, 감사 추적(Audit Trail) |
| **A2A** | 태스크 위임, 결과 스트리밍 | 에이전트 권한 범위(Scope) 명세, **위임 체인 추적** |
| **ACP** | 메시지 형식 표준화 | 컴플라이언스 정책 인코딩, 크로스-조직 신뢰 표현 |

기업 환경에서 AI 에이전트가 민감한 작업을 수행할 때 **"누가 무엇을 언제 승인했는가"**를 프로토콜 레벨에서 추적할 수 없다.

**예측**: A2A v2.0에서 Delegation Chain + Policy Expression 추가될 가능성이 높다.

---

## 6. 8월 9일 — DyPES-VLA: 로봇 간 A2A — 공유 동역학 프라이어

**"DyPES-VLA: Learning Shared Dynamics Priors for Multi-Robot Control"**
[huggingface.co/papers/2608.06374](https://huggingface.co/papers/2608.06374) | 18 upvotes

로봇 분야의 A2A: 서로 다른 로봇들이 **물리 법칙(동역학)을 공유 학습**한다.

```
DyPES-VLA:
로봇 A → 환경 경험 → 공유 Dynamics Prior Pool
로봇 B → 환경 경험 →           ↑
                         A2A 공유 레이어
로봇 A ← 로봇 B의 동역학 지식 활용
로봇 B ← 로봇 A의 동역학 지식 활용
```

소프트웨어 에이전트의 A2A 지식 공유가 로봇 물리 레이어로 확장됐다.

---

## 7. 2주 A2A 타임라인

| 날짜 | 이벤트 | 의미 |
|------|--------|------|
| 2025-12 | Linux Foundation AAIF 설립 (OpenAI·Anthropic·Google·MS·AWS·Block) | 표준화 기관 |
| 2026-04 | **A2A v1.0 공식 출시** (150+ 조직 즉시 채택) | 표준화 원년 |
| 2026-07-27 | CORAL 논문 — 동적 A2A 오케스트레이션 | 규칙 기반 → 자연어 A2A |
| 2026-08-08 | Agentic Economies 논문 | A2A → 에이전트 경제 이론 |
| **2026-08-09** | **Governance Gaps 논문** | **v1.0 한계 명시, v2.0 방향 제시** |
| 예측 | A2A v2.0 | Delegation Chain + Governance Layer |

---

*다음 편: [Harness & Evaluation 2주 동향](/posts/llm-trend-weekly-04-harness/)*

---

_수집: agent-loop-survey 스케줄 태스크 | 기간: 2026-07-26 ~ 2026-08-09_
