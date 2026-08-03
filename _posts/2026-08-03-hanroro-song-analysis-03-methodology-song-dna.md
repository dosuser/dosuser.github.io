---
layout: post
title: "한로로 노래 분석 프로젝트 3편 — 방법론 2: 촘스키식 Song-DNA 인코딩"
date: 2026-08-03
author: dosuser
tags: [한로로, 노래분석, Song-DNA, GTTM, Rohrmeier, JHT, Parsons-code, 생성문법, 음악이론]
---

> **자가 확인용 기록**: 이 글은 프로젝트를 다음에 이어서 진행할 수 있도록 작성자가 개인적으로 진행 상황을 기록·검증하기 위해 쓴 글이다. 공식 발표나 완결된 튜토리얼이 아니라 작업 로그에 가깝다.

한 줄 요약:

> "음악을 유한 알파벳 서열로 인코딩하면 생물정보학 도구(BLAST, MSA)를 그대로 적용할 수 있다 — GTTM 4계층 + Rohrmeier 생성 화성 문법 + Jazz Harmony Treebank 3규칙 + Parsons/±11 반음 서열을 조합해 5-레이어 Song-DNA 절차를 정의한다."

---

## 목차

1. [왜 촘스키 언어학이 음악에 적용되는가](#1-왜-촘스키-언어학이-음악에-적용되는가)
2. [이론 기반 1: GTTM 4계층](#2-이론-기반-1-gttm-4계층)
3. [이론 기반 2: Rohrmeier GSM과 Jazz Harmony Treebank](#3-이론-기반-2-rohrmeier-gsm과-jazz-harmony-treebank)
4. [서열 인코딩 표기법](#4-서열-인코딩-표기법)
5. [Song-DNA 5-레이어 인코딩 절차](#5-song-dna-5-레이어-인코딩-절차)
6. [워크드 예제: JHT 파스 트리](#6-워크드-예제-jht-파스-트리)
7. [한계 (정직 고지)](#7-한계-정직-고지)
8. [이 시리즈의 다른 글](#8-이-시리즈의-다른-글)

---

## 1. 왜 촘스키 언어학이 음악에 적용되는가

![GTTM 4계층 및 Song-DNA 파이프라인](/images/hanroro-song-analysis-03-methodology-song-dna/01-gttm-song-dna-pipeline.jpg)

"음악도 문법이 있다"는 아이디어는 직관적이다. 멜로디가 임의의 음표 나열이 아니라 **예상 가능한 패턴**을 따른다는 건 누구나 느낀다. 그 패턴을 형식적으로 기술하려는 시도가 1980년대부터 축적됐다.

음악이 유한상태(n-gram/Markov)로는 부족하다는 근거:
- **장거리 의존(long-distance dependency)**: 코드 진행에서 멀리 떨어진 화음이 서로 제약한다
- **재귀 중첩**: 전조(modulation) 내부에 또 전조가 가능하다
- **헤드성(headedness)**: 화음 그룹에서 어느 화음이 핵(head)인지 위계가 있다

출처: Rohrmeier & Pearce (2018) — [https://www.marcus-pearce.com/assets/papers/RohrmeierPearce2018.pdf](https://www.marcus-pearce.com/assets/papers/RohrmeierPearce2018.pdf)
> "harmony sequences may form long-distance dependencies between chords that are separated by other functional chords in between." (Rohrmeier 2011)

단, 완전히 결정된 건 아니다:
> "The extent of context-sensitivity required to adequately model musical structure requires further investigation." (Rohrmeier & Pearce 2018, §25.4.4)

---

## 2. 이론 기반 1: GTTM 4계층

**GTTM(Generative Theory of Tonal Music)**: Lerdahl & Jackendoff (1983)가 촘스키 생성문법의 아이디어를 조성음악에 이식한 대표 이론.

출처: Wikipedia — [https://en.wikipedia.org/wiki/Generative_theory_of_tonal_music](https://en.wikipedia.org/wiki/Generative_theory_of_tonal_music)
> "The generative theory of tonal music (GTTM) is a system of music analysis developed by music theorist Fred Lerdahl and linguist Ray Jackendoff."
> "a 'formal description of the musical intuitions of a listener who is experienced in a musical idiom.'"

4개 위계:

| 위계 | 설명 |
|---|---|
| **① Grouping Structure** | 모티브→프레이즈→섹션으로 분절: "It expresses a hierarchical segmentation of a piece into motives, phrases, periods, and still larger sections." |
| **② Metrical Structure** | 강/약박 격자 — 비트 그리드의 위계적 구조 |
| **③ Time-span Reduction** | 구조적 중요도의 헤드 있는 이진 트리 — 어떤 음/화음이 구조적으로 중요한가 |
| **④ Prolongational Reduction** | 긴장/이완 트리: "provides our 'psychological' awareness of tensing and relaxing patterns" |

규칙의 종류:
- **Well-formedness rules**: 가능한 구조를 정의하는 하드 제약
- **Preference rules**: 실제로 들리는 구조를 고르는 소프트 가중치

자동화 도구: Hamanaka의 exGTTM/ATTA — [https://gttm.jp/hamanaka/en/exgttm/](https://gttm.jp/hamanaka/en/exgttm/) (GTTM 36개 규칙 중 26개 구현, 미완성)  
정답 데이터: GTTM Database 300곡 — [https://gttm.jp/gttm/database/](https://gttm.jp/gttm/database/)

---

## 3. 이론 기반 2: Rohrmeier GSM과 Jazz Harmony Treebank

### ■ Rohrmeier GSM (Generative Syntax Model, 2011)

조성 화성의 생성 통사론 — 4레벨(구/기능/음도/표면), 알파벳 6종, **재작성 규칙 28개**.

주요 규칙 예시:
```
TR -> DR t           (dominant regions prepare tonics)
DR -> SR d
t -> tp              (대리 화음)
X -> D(X) X          (부속화음)
X[key=y] -> TR[key=ψ(X,y)]   (전조 — 재귀의 원천)
```

- 원논문(유료): [https://doi.org/10.1080/17459737.2011.573676](https://doi.org/10.1080/17459737.2011.573676)
- 규칙 전문 공개 강의노트(TU Dresden, Moss): [https://tu-dresden.de/gsw/phil/ikm/muwi/ressourcen/dateien/lehrveranstaltungen/ws_2015-16-ss2020/ws_2015_2016/s_musik_mathematik_kognition/gsm-rohrmeier-2011.pdf?lang=en](https://tu-dresden.de/gsw/phil/ikm/muwi/ressourcen/dateien/lehrveranstaltungen/ws_2015-16-ss2020/ws_2015_2016/s_musik_mathematik_kognition/gsm-rohrmeier-2011.pdf?lang=en)
- 오픈액세스 후속작(재즈 화성 통사론, 2020): [https://zenodo.org/records/4618222](https://zenodo.org/records/4618222)

### ■ Jazz Harmony Treebank (JHT, ISMIR 2020)

실무에 가장 쓰기 쉬운 **3규칙 Context-Free Grammar** + 150곡 파스 트리 JSON:

출처: [https://program.ismir2020.net/static/final_papers/80.pdf](https://program.ismir2020.net/static/final_papers/80.pdf)  
데이터: [https://github.com/DCMLab/JazzHarmonyTreebank](https://github.com/DCMLab/JazzHarmonyTreebank)

> "Prolongation and preparation are the two fundamental principles of functional harmonic syntax. They can be formalized as rules of a context-free grammar with chord symbols both as terminals and nonterminals."

3개 규칙:

```
X -> X X        (strong prolongation: 같은 기능 반복)
X -> Y X | X Y  (weak prolongation: Y가 기능적 동치)
X -> Y X        (preparation: Y가 X를 준비, V→I가 대표 예)
```

### ■ Steedman (1984): 재즈 화성 문법의 효시

12-bar blues를 6개 규칙으로 생성한 최초의 명시적 재즈 화성 문법:

출처: [https://homepages.inf.ed.ac.uk/steedman/papers/music/40285282.pdf](https://homepages.inf.ed.ac.uk/steedman/papers/music/40285282.pdf)
> "The recursive character of musical chord sequences makes generative grammar a suitable formalism for describing the rules that constrain such sequences."

---

## 4. 서열 인코딩 표기법

"Music DNA"에서 DNA는 두 갈래다: (A) 실제 DNA 분자에 음악 저장(Kiryanova 2024 — 분석 기법 아님), (B) **음악을 유한 알파벳 서열로 보고 생물정보학 알고리즘 적용**. 분석 목적에는 **B가 정답**.

| 표기 | 알파벳 | 정보량 | 용도 | 출처 |
|---|---|---|---|---|
| **Parsons code** | `*,u,d,r` (4종) | 멜로디 윤곽만 | 저해상도 지문, query-by-humming | [Wikipedia](https://en.wikipedia.org/wiki/Parsons_code): "a simple notation method used to identify a piece of music through melodic motion" |
| **±11 반음 음정 서열** | 23 심볼 (-11~+11) | 조성 불변 멜로디 | **BLAST/MSA 등 생물정보학 정렬 직접 적용** | Bountouridis et al. 2017 — [EvoMUSART](https://webspace.science.uu.nl/~veltk101/publications/art/evomusart2017-melody.pdf): "our work considers melodies as pitch-contours, meaning series of relative pitch transitions constrained to the region between +11 and −11 semitones" |
| **Harte chord syntax** | BNF 정의 문자열 | 코드 완전 표기 | MIR 표준 코드 라벨 (`F:min6`, `Db:7`) | [Harte BNF](https://zenodo.org/records/1415114): "simple and intuitive for musically trained individuals to write and understand" |
| **ABC notation** | ASCII 전체 | 완전한 악보 | 사람·기계 겸용 멜로디 고정 | [ABC standard v2.1](https://abcnotation.com/wiki/abc:standard:v2.1): "Abc is a text-based music notation system designed to be comprehensible by both people and computers." |

Bountouridis (2017)의 검증 결과:
> "we apply the extremely fast indexing method of the Basic Local Alignment Search Tool (BLAST) and achieve comparable classification performance to exhaustive approaches."

즉 ±11 반음 서열로 인코딩하면, **커버곡 탐지나 변주 추적에 BLAST를 그대로 쓸 수 있다**.

---

## 5. Song-DNA 5-레이어 인코딩 절차

> 각 레이어의 표기·규칙은 위 출처 그대로이나, 5-레이어 절차 자체는 본 조사의 종합 제안이다.

| Layer | 산출물 | 표준/도구 |
|---|---|---|
| **L0** 원본 | 음원 → 채보(MusicXML/MIDI) | MuseScore, MIR 자동 채보 보조 |
| **L1** 표기 | 멜로디 ABC 문자열 + 화성 Harte 라벨열 | abcnotation v2.1, Harte BNF |
| **L2** 서열 | Parsons 문자열(저해상도) + ±11 음정 서열(정렬용) | Parsons code, EvoMUSART 알파벳 |
| **L3** 문법·트리 | JHT 3규칙 파스 트리(JSON/괄호 문자열) | JHT 웹 에디터 |
| **L4** 위계 | GTTM 4계층 (grouping/metrical/time-span/prolongational) | exGTTM/ATTA |
| **L5** DNA 문자열 | 트리의 괄호 직렬화 + (선택) 4문자 ACGT 매핑 | tikz-qtree 형식 |

실제 구현(`step6_song_dna.py`)은 [5편]({% post_url 2026-08-03-hanroro-song-analysis-05-implementation %})에서, 실측 결과(164노트, Parsons code, ±11 서열)는 [6편]({% post_url 2026-08-03-hanroro-song-analysis-06-empirical-results %})에서 확인.

---

## 6. 워크드 예제: JHT 파스 트리

방법론 문서의 JHT 논문 Figure 1 검증 단편 — 코드열 `Fm6 Abm7 Db7 Gø7 C7 Fm6`:

**L1-b** (Harte 표기):
```
F:min6  Ab:min7  Db:7  G:hdim7  C:7  F:min6
```

**L3-a** (JHT 3규칙 적용):
```
Fm6 -> Fm6 Fm6          (prolongation: 동일 기능 반복)
Fm6 -> C7 Fm6           (preparation: V→i, 도미넌트가 토닉을 준비)
C7  -> Db7 C7           (트라이톤 대리 preparation)
C7  -> Gø7 C7           (iiø→V)
Db7 -> Abm7 Db7         (ii→V of Db)
```

**L5** (괄호 직렬화):
```
(Fm6 (Fm6) (Fm6 (C7 (Db7 (Abm7)(Db7)) (C7 (Gø7)(C7))) (Fm6)))
```

이 괄호 문자열이 핵심이다: **문자열 편집거리(edit distance)**로 두 곡의 구조 유사도를 측정할 수 있다. 표면 코드가 달라도 트리 모양이 같으면 구조적으로 가깝다.

---

## 7. 한계 (정직 고지)

1. **트리 정답은 유일하지 않다.** JHT 저자들:
   > "A formal grammar that purely models chord symbols can therefore only answer the question 'Is this a plausible syntax tree for a Jazz standard?', but not the question 'Is this tree a good analysis of that particular tune in a particular context?'"

2. **인지적 실재성 미검증.** Rohrmeier 본인:
   > "the cognitive reality of recursive dependencies on the largest levels … cannot be taken for granted."

3. **GTTM 자동화(ATTA)는 36개 규칙 중 26개만** 구현된 미완성 상태.

4. **Rohrmeier 2011 원문은 유료** — 규칙 전문은 TU Dresden 강의노트(무료)와 2020 오픈액세스 후속작으로 접근 가능.

---

## 8. 이 시리즈의 다른 글

| 편 | 제목 |
|---|---|
| [1편]({% post_url 2026-08-03-hanroro-song-analysis-01-design %}) | 설계 — 왜, 어떻게 |
| [2편]({% post_url 2026-08-03-hanroro-song-analysis-02-methodology-general %}) | 방법론 1: 일반적 노래 분석 4계층 프레임워크 |
| **3편** (현재) | 방법론 2: 촘스키식 Song-DNA 인코딩 |
| [4편]({% post_url 2026-08-03-hanroro-song-analysis-04-methodology-sync-timing %}) | 방법론 3: 악기·보컬 싱크 타이밍 분석 |
| [5편]({% post_url 2026-08-03-hanroro-song-analysis-05-implementation %}) | 상세 구현 — 코드·스크립트·트러블슈팅 |
| [6편]({% post_url 2026-08-03-hanroro-song-analysis-06-empirical-results %}) | 실측 결과 — Frozen Lace 파이프라인 검증 수치 |

---

*작성일: 2026-08-03*  
*저자: 신대용 (daeyong.shin@navercorp.com)*  
*작성 보조: Claude (Fable 5) — 방법론 수립·조사·오케스트레이션 / Antigravity (agy) — 실행·집필·이미지 생성*
