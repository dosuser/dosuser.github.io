---
layout: post
title: "한로로 노래 분석 프로젝트 2편 — 방법론 1: 일반적 노래 분석 4계층 프레임워크"
date: 2026-08-03
author: dosuser
tags: [한로로, 노래분석, 음악학, 대중음악학, MIR, soundbox, Tagg, MIREX]
---

> **자가 확인용 기록**: 이 글은 프로젝트를 다음에 이어서 진행할 수 있도록 작성자가 개인적으로 진행 상황을 기록·검증하기 위해 쓴 글이다. 공식 발표나 완결된 튜토리얼이 아니라 작업 로그에 가깝다.

한 줄 요약:

> "한 곡을 제대로 분석하려면 전통 음악학 → 대중음악학(녹음물 중심) → 가사 → 컴퓨터 분석, 4개 계층을 모두 거쳐야 한다 — 각 계층은 독립적으로 실행 가능하며 최종 리포트에서 상호 참조한다."

---

## 목차

1. [왜 4계층인가](#1-왜-4계층인가)
2. [계층 1: 전통 음악학 분석](#2-계층-1-전통-음악학-분석)
3. [계층 2: 대중음악학 (녹음물 중심) 분석](#3-계층-2-대중음악학-녹음물-중심-분석)
4. [계층 3: 가사 분석](#4-계층-3-가사-분석)
5. [계층 4: MIR 컴퓨터 분석](#5-계층-4-mir-컴퓨터-분석)
6. [이 시리즈의 다른 글](#6-이-시리즈의-다른-글)

---

## 1. 왜 4계층인가

![4계층 노래 분석 프레임워크](/images/hanroro-song-analysis-02-methodology-general/01-four-layer-framework.jpg)

음악학 분석 방법론을 조사했을 때 바로 드러난 문제: **방법론이 다양하지만 계층이 다르다.**

전통 음악학(악보 기반)과 대중음악학(녹음물 기반)은 분석 대상 자체가 다르고, 가사 분석은 언어학에 가깝고, MIR(Music Information Retrieval)은 신호처리 영역이다. 이 네 영역이 서로 다른 관점에서 같은 대상을 보기 때문에, **통합하지 않으면 각자 편향된 결론**을 낸다.

4계층이 필요한 이유:
- 계층 1(악보)만으로는: 녹음된 사운드의 공간감·프로덕션을 놓친다
- 계층 2(녹음물)만으로는: 화성 구조를 정확히 기술하기 어렵다
- 계층 3(가사)만으로는: 가사와 음악의 상호작용을 놓친다
- 계층 4(MIR)만으로는: 숫자는 나오지만 해석이 없다

---

## 2. 계층 1: 전통 음악학 분석

### ■ 형식 분석 (Verse-Chorus Form)

곡을 기능 모듈로 분해한다: intro / verse / prechorus / chorus / bridge / outro.

기준: Open Music Theory ([http://openmusictheory.github.io/popRockForm.html](http://openmusictheory.github.io/popRockForm.html))
> Chorus: "The primary module, which contains the title lyrics, the most memorable melody, and which ends the song."
> Verse: "A secondary module, which contains the main narrative text, and which begins the song."

〈사랑하게 될 거야〉 적용 계획: 02:45의 짧은 러닝타임에서 제목 가사("사랑하게 될 거야")가 등장하는 모듈을 chorus로 확정하고 타임코드 포함 모듈 지도를 작성.

### ■ 화성 분석 (로마자 분석)

각 화음을 로마 숫자(I, IV, V, vi…)로 표기하고 기능(T/S/D)을 판정한다.

대중음악 단조 표기는 **"six-based minor"** 관행을 고려:

출처: Trevor de Clercq, *Music Theory Online* 27.4 (2021) — [https://mtosmt.org/issues/mto.21.27.4/mto.21.27.4.de_clercq.html](https://mtosmt.org/issues/mto.21.27.4/mto.21.27.4.de_clercq.html)
> "Nashville musicians do not normally use Roman numerals; instead, they use an alternative functional chord notation called the Nashville number system, in which six-based minor is standard"

### ■ 선율·리듬 분석

- **선율**: 음역, 프레이즈 구조, 절정음 위치, 가사-선율 정렬
- **리듬**: 싱커페이션, 반복 패턴, 그루브 (→ 방법론 C 싱크 타이밍 분석과 연결)

### ■ 셴커 분석 (선택적)

심층 성부진행(Urlinie) 도출. 단, 록/팝의 리듬·반복 구조에는 한계가 있어 보조적으로만 사용.

출처: "Schenkerian Analysis and Popular Music" (*TRANS*) — [https://www.sibetrans.com/trans/articulo/240/schenkerian-analysis-and-popular-music](https://www.sibetrans.com/trans/articulo/240/schenkerian-analysis-and-popular-music)
> "the amount of Schenkerian principles adopted in these analyses varies broadly among them; some try to apply most of them, whereas others can hardly be called Schenkerian analyses."

---

## 3. 계층 2: 대중음악학 (녹음물 중심) 분석

계층 1이 악보를 분석한다면, 계층 2는 **녹음된 음악 자체**를 분석 대상으로 삼는다.

### ■ Moore의 soundbox·persona 분석

믹스의 가상 3차원 공간(좌우 패닝 × 전후 깊이 × 상하 음역)에서 각 악기 위치를 기술하는 **soundbox** 개념:

출처: Allan Moore, *Song Means* (2012) 서평 — [https://www.arpjournal.com/asarpwp/song-means-analysing-and-interpreting-recorded-popular-song/](https://www.arpjournal.com/asarpwp/song-means-analysing-and-interpreting-recorded-popular-song/)
> "the soundbox provides a way of conceptualizing the textural space that a recording inhabits, by enabling us to literally hear recordings taking space"

그리고 **persona** 분석:
> "the way that a persona becomes clear to a listener is partly, self-evidently, through the lyrics of the track but, perhaps more importantly, by means of the melody through which those lyrics are delivered, and by means of the voice through which the lyrics and melody are articulated"

〈사랑하게 될 거야〉 적용 계획: 한로로 보컬의 persona(독백적/고백적 화자)와 밴드 사운드의 공간 배치를 구간별로 기술.

### ■ Tagg의 기호학적 분석

Philip Tagg의 방법론 — [https://www.tagg.org/articles/pm2anal.html](https://www.tagg.org/articles/pm2anal.html):

1. 곡을 최소 의미 단위(**museme**: 음악적 의미의 최소 단위)로 분해
2. **상호객관 비교(Intersubjective Comparison, IOC)**: 비슷한 스타일의 다른 곡과 비교
   > "describing music by means of other music; it means comparing the AO with other music in a relevant style and with similar functions"
3. **가설적 치환**: 요소를 바꿔보며 어떤 파라미터가 정서를 실어 나르는지 검증
   > "alter the various parameters of musical expression one by one, in order to pinpoint what part of the music actually carries" (특정 정서적 의미)

### ■ 음색·프로덕션 분석 (Lavengood/Heidemann)

스펙트로그램에서 관찰 가능한 음색 속성을 유표/무표 이항 대립으로 기술:

출처: Megan Lavengood, *Music Theory Online* 26.3 (2020) — [https://mtosmt.org/issues/mto.20.26.3/mto.20.26.3.lavengood.html](https://mtosmt.org/issues/mto.20.26.3/mto.20.26.3.lavengood.html)
> "I have built into my methodology a vocabulary for describing timbral attributes that are visible in spectrograms, grounded in a system of binary oppositions."

보컬 음색 기술: Kate Heidemann, *Music Theory Online* 22.1 — [https://mtosmt.org/issues/mto.16.22.1/mto.16.22.1.heidemann.pdf](https://mtosmt.org/issues/mto.16.22.1/mto.16.22.1.heidemann.pdf)

---

## 4. 계층 3: 가사 분석

- **시학적 분석**: 이미지·상징·비유·화자
- **서사 구조**: 이야기 흐름과 관점 변화
- **운율**: 각운·내부운·음절 강세와 박자의 정렬

출처: Vaia Lyric Analysis — [https://www.vaia.com/en-us/explanations/music/music-analysis/lyric-analysis/](https://www.vaia.com/en-us/explanations/music/music-analysis/lyric-analysis/)
> "Imagery is a powerful technique in lyric analysis where words are used to create vivid pictures in the listener's mind."

**lyric-setting(가사-선율 정렬) 분석**: 가사가 선율의 어느 박자 위치에 얹히는지, 음절 강세와 음악 강박이 어떻게 정렬되는지 — "Structural Models for Lyric-Setting Analysis in Popular Song" ([https://www.researchgate.net/publication/394391337_Structural_Models_for_Lyric-Setting_Analysis_in_Popular_Song](https://www.researchgate.net/publication/394391337_Structural_Models_for_Lyric-Setting_Analysis_in_Popular_Song))

〈사랑하게 될 거야〉 적용 계획:
- 앨범 『이상비행』의 비행/추락 모티프와 제목의 미래시제("~하게 될 거야") **서법(mood) 분석**
- 후렴 반복 시 가사 변형 여부 추적

---

## 5. 계층 4: MIR 컴퓨터 분석

| 태스크 | 도구 | 비고 |
|---|---|---|
| **코드(화음) 인식** | Chordify류 / MIREX ACE 계열 | [MIREX](https://music-ir.org/mirex/wiki/2020:Audio_Chord_Estimation): "This task requires participants to extract or transcribe a sequence of chords from an audio music recording." |
| **구조 분할** (verse/chorus 자동 검출) | librosa segmentation | [librosa](https://librosa.org/doc/latest/index.html): "a python package for music and audio analysis." |
| **선율 추출** | Essentia (Melodia) | [Essentia](https://essentia.upf.edu/): "Open-source C++ library for audio analysis and audio-based music information retrieval." |
| **스펙트로그램 정밀 청취** | Sonic Visualiser | [Sonic Visualiser](https://www.sonicvisualiser.org/): "designed to be the first program you reach for when want to study a music recording closely." |

MIR 계층의 강점은 **재현 가능성** — 같은 스크립트를 실행하면 누가 실행해도 같은 수치가 나온다. 약점은 해석 맥락 없이는 숫자만 남는다는 것. 계층 1·2·3이 해석 맥락을 제공한다.

실제 구현 코드는 [5편 상세 구현]({% post_url 2026-08-03-hanroro-song-analysis-05-implementation %})에서, 실측 수치는 [6편 실측 결과]({% post_url 2026-08-03-hanroro-song-analysis-06-empirical-results %})에서 확인할 수 있다.

---

## 6. 이 시리즈의 다른 글

| 편 | 제목 |
|---|---|
| [1편]({% post_url 2026-08-03-hanroro-song-analysis-01-design %}) | 설계 — 왜, 어떻게 |
| **2편** (현재) | 방법론 1: 일반적 노래 분석 4계층 프레임워크 |
| [3편]({% post_url 2026-08-03-hanroro-song-analysis-03-methodology-song-dna %}) | 방법론 2: 촘스키식 Song-DNA 인코딩 |
| [4편]({% post_url 2026-08-03-hanroro-song-analysis-04-methodology-sync-timing %}) | 방법론 3: 악기·보컬 싱크 타이밍 분석 |
| [5편]({% post_url 2026-08-03-hanroro-song-analysis-05-implementation %}) | 상세 구현 — 코드·스크립트·트러블슈팅 |
| [6편]({% post_url 2026-08-03-hanroro-song-analysis-06-empirical-results %}) | 실측 결과 — Frozen Lace 파이프라인 검증 수치 |

---

*작성일: 2026-08-03*  
*저자: 신대용 (daeyong.shin@navercorp.com)*  
*작성 보조: Claude (Fable 5) — 방법론 수립·조사·오케스트레이션 / Antigravity (agy) — 실행·집필·이미지 생성*
