---
layout: post
title: "LLM 트렌드 2주 결산 1편 — Hot Source: 오픈소스 모델·논문·프레임워크 (2026-07-26 ~ 08-09)"
date: 2026-08-09
author: dosuser
tags: [LLM, AI트렌드, 오픈소스, HuggingFace, GitHub, 에이전트, 논문리뷰, "2026"]
---

> **시리즈**: LLM 에이전트 트렌드 2주 결산 (2026-07-26 ~ 2026-08-09)
> 매일 HuggingFace Daily Papers, GitHub Trending, 주요 업체 블로그를 수집해 만든 2주 요약본이다.

한 줄 요약:

> "오픈소스 모델이 폐쇄형을 한 자릿수 차이까지 추격하는 동안, HuggingFace 논문 피드는 에이전트 루프·하네스·A2A로 가득 찼다."

---

## 시리즈 목차

1. **이 글** — Hot Source: 오픈소스 모델·논문·프레임워크
2. [Agent Loop 2주 트렌드](/posts/llm-trend-weekly-02-agent-loop/)
3. [Agent-to-Agent (A2A) 프로토콜 2주 동향](/posts/llm-trend-weekly-03-agent-to-agent/)
4. [Harness & Evaluation 2주 동향](/posts/llm-trend-weekly-04-harness/)

---

## 1. 7월 오픈소스 모델 붐 — 폐쇄형 모델과의 격차가 사라졌다

2026년 7월은 오픈소스 LLM 역사상 가장 강력한 한 달이었다.

| 모델 | 출시일 | 파라미터 | 주요 벤치마크 | 라이선스 |
|------|--------|----------|-------------|---------|
| **GLM-5.2** | 2026-06-13 | 744B MoE | GPQA Diamond 91.2%, SWE-bench Pro 62.1% | MIT |
| **Kimi K3** | 2026-07 | 2.8T MoE | 1M 컨텍스트, 네이티브 비전 | Open-weight |
| **Kimi K2.7 Code** | 2026-06-12 | — | 최고 성능 budget 코딩 에이전트 | — |
| **DeepSeek V4 Pro** | 2026-04-24 | — | SWE-bench Verified 80.6%, 1M 컨텍스트 | MIT |

**핵심 인사이트**: GLM-5.2와 Kimi K3가 폐쇄형 모델(GPT-4o, Claude Sonnet)과의 격차를 한 자릿수 %포인트까지 좁혔다. 비용은 4~10배 저렴하다.

---

## 2. GitHub Trending 2주 키워드

### 7월 말 — AI 인프라·자동화 수요 폭발

| 저장소 | Stars Today (최고) | 설명 |
|--------|------------------|------|
| [n8n-io/n8n](https://github.com/n8n-io/n8n) | +170 | AI 기능 포함 워크플로우 자동화 |
| [windmill-labs/windmill](https://github.com/windmill-labs/windmill) | +30 | 스크립트 → 워크플로우 AI-native 플랫폼 |
| [usestrix/strix](https://github.com/usestrix/strix) | — | AI 침투 테스트 — 2026 최고 속성장 오픈소스 |
| [DeusData/codebase-memory-mcp](https://github.com/DeusData/codebase-memory-mcp) | — | 코딩 에이전트용 코드베이스 이해 MCP (토큰 99% 절감) |
| [HKUDS/Vibe-Trading](https://github.com/HKUDS/Vibe-Trading) | — | 자연어 → 백테스트 + 실시간 거래 변환 |

### 8월 초 — 에이전트 인증 인프라 급부상

| 저장소 | Stars Today | 설명 |
|--------|------------|------|
| [goauthentik/authentik](https://github.com/goauthentik/authentik) | 🔥 **530→467** (2일 연속) | 에이전트 인증 통합 플랫폼 |
| [OpenBB-finance/OpenBB](https://github.com/OpenBB-finance/OpenBB) | +51 | AI 에이전트 기반 금융 분석 플랫폼 |
| [NVIDIA/Megatron-LM](https://github.com/NVIDIA/Megatron-LM) | +13 | 대규모 LLM 훈련 프레임워크 |

authentik이 2일 연속 Top에 오른 것은 눈여겨볼 신호다. **에이전트가 외부 서비스에 인증하는 인프라**에 대한 수요가 GitHub 레벨에서 가시화되기 시작했다.

---

## 3. HuggingFace Daily Papers 2주 하이라이트

### 논문 흐름의 3대 축

**축 1: 에이전트 자기개선 루프**

| 날짜 | 논문 | upvotes | 핵심 |
|------|------|---------|------|
| 07-26 | [AREX: Recursively Self-Improving Agent](https://huggingface.co/papers/2607.21461) | — | 재귀 자기개선 에이전트 딥 리서치 |
| 07-31 | [SkillRise: Cross-Task Skill Evolution](https://huggingface.co/papers/2607.26784) | 19 | 크로스 태스크 스킬 진화 RL |
| 08-01 | [SkillOpt: Self-Evolving Agent Skills](https://arxiv.org/abs/2605.23904) | 264 | 스킬 자기진화 실행 전략 |
| 08-08 | [AgentOPSD: Recursive Self-Distillation](https://huggingface.co/papers/2608.05987) | **75** | 재귀 자기증류 에이전트 RL |

약 2주 사이에 "에이전트가 스스로를 개선하는 루프"가 4편 등장했다. AREX(재귀 자기개선) → SkillRise(스킬 전이) → SkillOpt(스킬 갱신) → AgentOPSD(자기증류)로 점점 구체화됐다.

**축 2: VLA·로봇 에이전트**

| 날짜 | 논문 | upvotes | 핵심 |
|------|------|---------|------|
| 07-31 | [TurboVLA: 32Hz 실시간 VLA](https://huggingface.co/papers/2607.27205) | 121 | RTX 4090에서 <1GB VRAM으로 실시간 구동 |
| 07-31 | [HumanCLAW: VLM이 몸을 통해 행동](https://huggingface.co/papers/2607.27180) | 66 | VLM → 로봇 제어 |
| 08-09 | [DyPES-VLA: Multi-Robot Shared Dynamics](https://huggingface.co/papers/2608.06374) | 18 | 크로스-엠보디먼트 공유 동역학 |

**축 3: 에이전트 메모리 & 시간 추론**

| 날짜 | 논문 | upvotes | 핵심 |
|------|------|---------|------|
| 08-08 | [Activity Frames: Deterministic Screen-Activity](https://huggingface.co/papers/2608.05784) | 15 | GUI 에이전트 메모리 확정성 |
| 08-09 | [ChronoVision: Temporal Reasoning via Latent State](https://huggingface.co/papers/2608.05631) | 33 | 시간 추론 잠재 상태 재구성 |

---

## 4. 주요 업체 동향

| 업체 | 이벤트 |
|------|--------|
| **Anthropic** | 연환산 매출 **$30B** 달성 — OpenAI ($24~25B) 추월 (7월 말 공개) |
| **Anthropic** | AMD MI450/Helios 최대 **2GW** 컴퓨트 확보, AMD 지분 $5B 포함 |
| **Frontis AI** | Frontis-MA1 공개 — AI4AI 재귀 자기개선 최초 공개 구현 |
| **Linux Foundation** | A2A v1.0 공식 출시 (4월), 150+ 조직 채택 확인 |

---

## 5. 2주 트렌드 요약

```
7월 말 키워드: 오픈소스 모델 붐 · VLA 로봇 · 자기개선 루프
8월 초 키워드: 재귀 자기증류 · 하네스 최적화 · 에이전트 경제 · 거버넌스 갭
```

- **오픈소스 모델**: 폐쇄형과의 성능 격차 사실상 해소 (GLM-5.2, Kimi K3, DeepSeek V4 Pro)
- **에이전트 자기개선**: 2주간 4편 — AREX → SkillRise → SkillOpt → AgentOPSD
- **인프라**: authentik 2연속 Top — 에이전트 인증 인프라 수요 표면화
- **논문 테마 전환**: 7월은 "무엇을 만드나", 8월은 "어떻게 더 잘 돌리나"

---

*다음 편: [Agent Loop — 2주간 루프 패턴의 진화](/posts/llm-trend-weekly-02-agent-loop/)*

---

_수집: agent-loop-survey 스케줄 태스크 | 기간: 2026-07-26 ~ 2026-08-09_
