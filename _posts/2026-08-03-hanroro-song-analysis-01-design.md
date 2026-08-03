---
layout: post
title: "한로로 노래 분석 프로젝트 1편 — 왜 이걸 만들었고, 어떻게 설계했나"
date: 2026-08-03
author: dosuser
tags: [한로로, 노래분석, MIR, Song-DNA, 방법론, AI에이전트, Claude, Antigravity]
---

> **자가 확인용 기록**: 이 글은 프로젝트를 다음에 이어서 진행할 수 있도록 작성자가 개인적으로 진행 상황을 기록·검증하기 위해 쓴 글이다. 공식 발표나 완결된 튜토리얼이 아니라 작업 로그에 가깝다.

한 줄 요약:

> "음악학·언어학·신호처리 세 갈래를 하나의 파이프라인으로 엮어, 한 곡을 9단계로 해부하는 자동화 분석 시스템 — Claude Code가 설계하고 Antigravity/agy가 실행 검증했다."

---

## 목차

1. [이 프로젝트가 왜 생겼나](#1-이-프로젝트가-왜-생겼나)
2. [세 갈래 방법론과 전체 아키텍처](#2-세-갈래-방법론과-전체-아키텍처)
3. [9단계 실행 계획](#3-9단계-실행-계획)
4. [AI 에이전트 워크플로우: Claude + Antigravity](#4-ai-에이전트-워크플로우-claude--antigravity)
5. [저작권 문제와 검증 전략](#5-저작권-문제와-검증-전략)
6. [이 시리즈의 다른 글](#6-이-시리즈의-다른-글)

---

## 1. 이 프로젝트가 왜 생겼나

좋아하는 곡을 분석하고 싶을 때 보통 어떻게 하는가?

> **"이 곡 왜 이렇게 좋지?"** → 검색 → "BPM 몇이야" → "코드 진행이 뭐야" → 분석 영상 시청 → 만족 못 함

그 "왜 좋은지"에 정확하게 답하려면 악보 읽기·음악 이론·신호처리 세 도메인이 동시에 필요한데, 어떤 도구도 이 셋을 통합해서 한 곡에 대한 **정량적 + 구조적 + 해석적 리포트**를 내놓지 않는다.

이 프로젝트의 목표:

- 분석 대상: 한로로 〈사랑하게 될 거야〉 (Landing in Love), 『이상비행』 6번 트랙, 2023-08-29 발매, 02:45, 작사·작곡 한로로 / 편곡 이새 ([벅스 트랙 페이지](https://music.bugs.co.kr/track/32944687))
- 분석 방법: 음악학 4계층 + 촘스키식 Song-DNA 인코딩 + 악기·보컬 싱크 타이밍 정량 측정
- 구현: Python 스크립트(librosa, Demucs 등) + AI 에이전트 오케스트레이션

> **주의**: 이 프로젝트는 한로로 〈사랑하게 될 거야〉의 원본 음원을 확보하지 못한 상태다. 저작권상 유튜브 뮤직 다운로드 요청이 거절됐고, 정식 구매 안내를 받았다. 현재는 **CC BY-NC 4.0 라이선스 대체곡(Frozen Lace by Wiseman, ccMixter)**으로 파이프라인 자체를 검증한 단계다. 한로로 곡 실제 분석은 정식 구매 후 이어갈 예정이다.

---

## 2. 세 갈래 방법론과 전체 아키텍처

![전체 파이프라인 아키텍처 다이어그램](/images/hanroro-song-analysis-01-design/01-pipeline-architecture.jpg)

세 갈래를 조합한 이유:

| 방법론 | 핵심 질문 | 한계 (단독으로는) |
|---|---|---|
| **A. 일반 노래 분석** (4계층) | "이 곡의 구조·화성·가사·음색은?" | 정성적 — 수치화 어렵다 |
| **B. 촘스키식 Song-DNA** | "이 곡의 구조를 서열(문자열)로 인코딩하면?" | 파스 트리 작성에 음악적 판단 필요 |
| **C. 싱크 타이밍** | "각 악기가 비트 그리드 대비 몇 ms 앞/뒤에 오는가?" | 해석 맥락(장르·의도) 없이는 숫자만 나온다 |

셋을 통합하면 A의 해석이 C의 수치를 설명하고, B의 구조가 A의 형식 분석을 정형화한다. 예를 들어:
- Chorus에서 보컬 laid-back이 커진다 → persona 분석(화자의 감정 이완)과 연결
- Song-DNA 서열의 변이 구간 = 가사 서사의 전환점

---

## 3. 9단계 실행 계획

방법론 문서 §4 ([`2026080200_한로로_사랑하게될거야_분석방법론_문서.md`](https://github.com/dosuser/dosuser.github.io))에서 정의한 9단계:

| 단계 | 작업 | 방법론 | 산출물 |
|---|---|---|---|
| **①** | 음원 확보 + Demucs `htdemucs_ft` stem 분리 | C | vocals/drums/bass/other wav |
| **②** | madmom으로 BPM·비트 그리드 실측 | C | 확정 BPM, 비트 시각 배열 |
| **③** | librosa 구조 분할 + 청취 → 형식 지도 | A | intro/verse/chorus 타임코드 |
| **④** | 코드 인식 → Harte 표기 + 로마자 분석 | A+B | 섹션별 코드열 |
| **⑤** | JHT 3규칙 파스 트리 + GTTM grouping/time-span | B | 파스 트리 JSON, 괄호 문자열(L5) |
| **⑥** | 후렴 멜로디 Parsons + ±11 음정 서열 인코딩 | B | Song-DNA 서열, 보존/변이 구간 |
| **⑦** | stem별 onset 추출 + 가사 정렬 → 편차(ms) | C | 악기별 microtiming 통계 |
| **⑧** | 시각화 3종(히스토그램·드리프트·beat-phase) | C | 그림 3종 |
| **⑨** | soundbox·persona·음색·가사 + 정량 결과 통합 | A | 최종 분석 리포트 |

**교차 검증 포인트** 3개:
1. 단계 ③ 형식 경계 ↔ 단계 ⑤ GTTM grouping 경계 일치 여부
2. 단계 ⑥ 멜로디 변이 구간 ↔ 단계 ⑦ 보컬 타이밍 편차 동조 여부  
3. 단계 ⑧ verse/chorus 간 보컬 laid-back 차이 → 단계 ⑨ persona 해석과 연결

---

## 4. AI 에이전트 워크플로우: Claude + Antigravity

이 프로젝트 자체가 멀티 에이전트 협업의 결과물이다.

**단계 분리**:
- **Claude Code (Fable 5)**: 방법론 수립 — 병렬 리서치 에이전트 3개를 동시 실행해 음악학·언어학·MIR 문헌 조사. 산출물: 방법론 문서 + 실행 지시서
- **Antigravity (agy CLI)**: 실행 검증 — Python 스크립트 작성 및 CC 라이선스 대체곡으로 파이프라인 전 단계 직접 실행

**tmux 오케스트레이션**:
```bash
# Claude가 수립한 지시서를 agy에게 위임
tmux new-session -d -s agy-hanroro -c ~/worklog/assets/hanroro-analysis
tmux send-keys -t agy-hanroro "agy" Enter
# 이후 agy가 지시서를 읽고 스크립트 작성·실행
```

- agy 기본 모델(Gemini 3.6 Flash) quota 초과 → Claude Sonnet 4.6 (Thinking)으로 전환 후 정상 진행
- GUI 앱은 tmux 제어 불가; CLI(`/Users/user/.local/bin/agy`)로만 자동화 가능함을 확인

이 블로그 시리즈 자체도 Antigravity가 작성 — Claude가 방법론 수립·1차 검증 후 블로그 문서화를 Antigravity에게 위임한 구조다.

---

## 5. 저작권 문제와 검증 전략

**상황**: 한로로 〈사랑하게 될 거야〉는 스트리밍 서비스의 DRM 보호 하에 있어 음원 파일을 직접 다운로드해 분석에 쓸 수 없다.

**판단**: 저작권 침해 없이 파이프라인을 검증하기 위해 **CC 라이선스 곡으로 전체 파이프라인을 먼저 실행**하고, 한로로 곡은 정식 구매 후 동일 파이프라인 적용.

**검증 대체곡**:
> **Frozen Lace** by Wiseman (ft. Zenboy1955, Stefan Kartenberg, Kara Square)  
> ccMixter — https://ccmixter.org/files/Wiseman/66344  
> License: Creative Commons BY-NC 4.0

검증 결과는 [6편 실측 결과]({% post_url 2026-08-03-hanroro-song-analysis-06-empirical-results %})에서 확인할 수 있다.

---

## 6. 이 시리즈의 다른 글

| 편 | 제목 |
|---|---|
| **1편** (현재) | 설계 — 왜, 어떻게 |
| [2편]({% post_url 2026-08-03-hanroro-song-analysis-02-methodology-general %}) | 방법론 1: 일반적 노래 분석 (4계층 프레임워크) |
| [3편]({% post_url 2026-08-03-hanroro-song-analysis-03-methodology-song-dna %}) | 방법론 2: 촘스키식 Song-DNA 인코딩 |
| [4편]({% post_url 2026-08-03-hanroro-song-analysis-04-methodology-sync-timing %}) | 방법론 3: 악기·보컬 싱크 타이밍 분석 |
| [5편]({% post_url 2026-08-03-hanroro-song-analysis-05-implementation %}) | 상세 구현 — 코드·스크립트·트러블슈팅 |
| [6편]({% post_url 2026-08-03-hanroro-song-analysis-06-empirical-results %}) | 실측 결과 — Frozen Lace 파이프라인 검증 수치 |

---

*작성일: 2026-08-03*  
*저자: 신대용 (daeyong.shin@navercorp.com)*  
*작성 보조: Claude (Fable 5) — 방법론 수립·조사·오케스트레이션 / Antigravity (agy) — 실행·집필·이미지 생성*
