---
layout: post
title: "한로로 노래 분석 프로젝트 6편 — 실측 결과: CC 라이선스 대체곡으로 파이프라인 검증"
date: 2026-08-03
author: dosuser
tags: [한로로, 노래분석, 실측, microtiming, Song-DNA, Frozen-Lace, ccMixter, 파이프라인검증]
---

> **자가 확인용 기록**: 이 글은 프로젝트를 다음에 이어서 진행할 수 있도록 작성자가 개인적으로 진행 상황을 기록·검증하기 위해 쓴 글이다. 공식 발표나 완결된 튜토리얼이 아니라 작업 로그에 가깝다.

한 줄 요약:

> "한로로 곡 원본을 확보하지 못해, CC BY-NC 4.0 대체곡(Frozen Lace by Wiseman)으로 9단계 파이프라인을 직접 실행했다 — 97.51 BPM, F major/minor 교차, 보컬 ±42.75ms 자유 프레이징, 164노트 Song-DNA 서열 전부 실측 완료."

---

> **⚠️ 중요 고지**: 이 글은 한로로 〈사랑하게 될 거야〉를 분석한 결과가 **아니다**.  
> 한로로 곡은 저작권상 음원을 확보하지 못했다(유튜브 뮤직 DRM 보호, 정식 구매 안내). 아래 수치는 전부 **CC BY-NC 4.0 대체곡 *Frozen Lace* by Wiseman**으로 파이프라인 자체를 검증한 결과다.  
> 한로로 곡에 대한 실제 분석은 정식 구매 후 동일 파이프라인으로 이어갈 예정.

---

## 목차

1. [대체곡 선정 배경 및 라이선스](#1-대체곡-선정-배경-및-라이선스)
2. [9단계 진행 상황](#2-9단계-진행-상황)
3. [실측 1: 곡 기본 정보 (BPM·조성)](#3-실측-1-곡-기본-정보-bpm조성)
4. [실측 2: 코드 진행 (Harte 표기)](#4-실측-2-코드-진행-harte-표기)
5. [실측 3: Song-DNA 서열](#5-실측-3-song-dna-서열)
6. [실측 4: 싱크 타이밍 (stem별 microtiming)](#6-실측-4-싱크-타이밍-stem별-microtiming)
7. [시각화 3종](#7-시각화-3종)
8. [한계 (정직 고지)](#8-한계-정직-고지)
9. [다음 단계](#9-다음-단계)
10. [이 시리즈의 다른 글](#10-이-시리즈의-다른-글)

---

## 1. 대체곡 선정 배경 및 라이선스

**왜 대체곡인가**:

> **사용자**: "한로로 〈사랑하게 될 거야〉 음원으로 파이프라인 실행해줘"  
> **에이전트**: 유튜브 뮤직·벅스·멜론 등에서 DRM 없는 파일 확보 불가 확인. 임의 다운로드는 저작권법 위반.  
> **판단**: CC 라이선스 곡으로 파이프라인 자체를 먼저 검증 → 한로로 곡은 정식 구매 후 동일 파이프라인 적용.

**저작자 표시** (CC BY-NC 4.0 의무):

> **Frozen Lace** by Wiseman (ft. Zenboy1955, Stefan Kartenberg, Kara Square)  
> ccMixter — [https://ccmixter.org/files/Wiseman/66344](https://ccmixter.org/files/Wiseman/66344)  
> License: Creative Commons Attribution-NonCommercial 4.0 — [https://creativecommons.org/licenses/by-nc/4.0/](https://creativecommons.org/licenses/by-nc/4.0/)  
> 길이: 4:12 (252.7초), 256kbps MP3, 44.1kHz 스테레오

---

## 2. 9단계 진행 상황

| # | 단계 | 상태 | 비고 |
|---|---|---|---|
| **①** | Demucs stem 분리 | ✅ 완료 | `htdemucs_ft`는 해시 불일치로 실패 → `htdemucs`(단일 모델)로 전환 성공 |
| **②** | BPM·비트 그리드 | ✅ 완료 | librosa 두 방식 일치: **97.51 BPM** — madmom은 이번엔 미사용(librosa로 충분) |
| **③** | 형식 지도 | ⚠️ 부분 | novelty 기반 경계 후보 26개 자동 추출. verse/chorus 라벨은 청취 확인 필요 — 에이전트는 오디오 청취·해석 불가 |
| **④** | 코드열 (Harte 표기) | ✅ 완료 | 24-템플릿 상관 매칭, 2비트 이상 78개 구간 추출 |
| **⑤** | 파스 트리 (JHT 규칙) | ⚠️ 예시만 | 코드열 앞부분 예시 1개. 전곡 자동 파싱은 비결정적 음악적 판단 필요 |
| **⑥** | Song-DNA 서열 | ✅ 완료 | Parsons code + ±11 반음 서열 (164노트). 음역 이상치는 한계로 명시 |
| **⑦** | 싱크 타이밍 | ✅ 완료 | stem별 onset 편차(ms), 악기 간 pairwise 비동기까지 계산 |
| **⑧** | 시각화 | ✅ 완료 | 히스토그램/시간축 드리프트/비트-위상 히트맵 3종 생성 |
| **⑨** | 통합 해석 | ⚠️ 부분 | 정량 결과 통합은 이 글. soundbox·persona 등 청취 기반 해석은 미수행 |

**요약**: 9단계 중 자동화 가능한 6단계(①②③일부④⑥⑦⑧) 전부 실행 완료. ⑤와 ⑨는 사람의 음악적 판단·청취가 필요해 부분 수행.

---

## 3. 실측 1: 곡 기본 정보 (BPM·조성)

| 항목 | 값 | 비고 |
|---|---|---|
| 길이 | 252.68초 (4:12) | librosa `get_duration` |
| **템포** | **97.51 BPM** | beat_track / onset-envelope tempo 두 방식 일치 |
| 비트 수 | 399개 | 첫 비트 0.894s, 마지막 검출 비트 247.13s |
| **조성 (추정)** | **F major** | Krumhansl-Schmuckler 상관계수 **0.806** |
| 2위 | F minor | 상관계수 0.601 |

**해석**: F major/minor 교차 — 같은 근음(F)의 장·단조를 오가는 동주조(parallel key) 색채 전환이 주된 패턴. 최빈 코드: `G:min`(110박) > `F:maj`(106박) > `F:min`(59박) 순으로 F 관련 코드가 압도적.

---

## 4. 실측 2: 코드 진행 (Harte 표기)

전체 241개 원시 구간 중 2비트 이상 지속 구간만 추림(78개). 초반부 예시:

```
1.50s~2.77s   F:maj  (3박, conf=0.65)
4.02s~4.64s   F:maj  (2박, conf=0.70)
5.27s~5.89s   F:min  (2박, conf=0.74)
7.76s~9.61s   F:maj  (4박, conf=0.67)
10.23s~10.86s F:min  (2박, conf=0.70)
12.81s~13.40s F:maj  (2박, conf=0.75)
```

**최빈 코드 분포** (beat count 기준):

| 코드 | 비트 수 | 비율 |
|---|---|---|
| G:min | 110 | 27.6% |
| F:maj | 106 | 26.6% |
| F:min | 59 | 14.8% |
| C:maj | 32 | 8.0% |
| A#:min | 15 | 3.8% |
| G:maj | 15 | 3.8% |

**해석**: 기능화성적 진행(예: ii-V-I)보다는 **모달/색채적 진행**에 가깝다. 장르 태그(ambient, chill, downtempo)와 부합. 후반부(233.5s~238.5s)에서 `G:min` 9박 연속 지속.

**JHT 3규칙 파스 트리 예시** (0~13.4초 구간):

```
코드열: F:maj → F:maj → F:min → F:maj → F:min → F:maj

F:maj -> F:maj F:maj    (strong prolongation: 동일 기능 반복)
F:maj -> F:min F:maj    (weak prolongation: 동주조 교체)
```

---

## 5. 실측 3: Song-DNA 서열

- 검출 노트: 원시 1,467개 → 80ms 미만 제거 후 **164개**

**Parsons code** (앞 80자):
```
*uuuddrrdduruddurdurrrdrdrududruurrrrddduduurrddrududuurdurdduuuduuddruddrdurrrd...
```

**±11 반음 서열** (앞 20개):
```
[1, 3, 11, 10, -1, 0, 0, -2, -3, 5, 0, 2, -1, -1, -5, 0, -1, 1, 0, 0]
```

**해석**:
- Parsons: `u`(상행)와 `d`(하행)이 비슷한 빈도. `r`(반복)이 산발적으로 등장 → 선율이 비교적 움직임이 많음
- ±11 서열: `[1, 3, 11]`처럼 큰 도약이 초반에 등장. `11`은 옥타브 바로 아래 장7도 상행(또는 폴딩된 단2도 하행)
- 이 서열 자체는 BLAST/MSA로 다른 곡과 비교 가능 — 멜로디 유사도 측정의 기반 데이터

**주의**: 음역 추정치(C2~C6, 4옥타브)는 pYIN 옥타브 오류 가능성이 커 신뢰 낮음. 상대 음정 서열(Parsons, ±11)은 유효하나 절대 음역 주장은 하지 않는다.

---

## 6. 실측 4: 싱크 타이밍 (stem별 microtiming)

기준 그리드: 드럼 stem에서 추정한 비트를 16분음표 단위로 보간. 매칭 게이트 ±80ms.

| stem | onset 수(게이트 내) | 평균 편차 | 표준편차 | 해석 |
|---|---|---|---|---|
| **drums** | 1,304 | **-5.25ms** | 12.20ms | pushed — 단, 그리드 자체가 드럼에서 추정됐으므로 "16분 그리드 대비 서브비트 위치"를 뜻함 |
| **bass** | 136 | **+18.52ms** | 21.79ms | laid-back — 다운템포 장르에 흔한 베이스 그루브 |
| **vocals** | 548 | **+4.51ms** | **42.75ms** | 평균은 그리드 근처지만 표준편차 압도적 → **박자에 거의 안 묶임** |
| **other** | 877 | -0.78ms | 28.76ms | 그리드와 거의 일치 |

**보컬-드럼 pairwise 비동기**: 442쌍, 평균 **+7.85ms**, 표준편차 40.04ms

**핵심 해석**:
- 베이스 +18.52ms: 다운템포 장르 특성. Danielsen 계열 연구의 96BPM 스네어 17.4ms 지연과 유사한 규모
- 보컬 SD 42.75ms: "일관된 laid-back"이 아니라 **자유로운 보컬 프레이징** — 리듬에 느슨하게 얹히는 앰비언트/다운템포 스타일과 정확히 일치
- microtiming 유의미 범위 0~50ms (ISMIR 2019 기준) 내에서 베이스가 가장 명확한 패턴을 보임

---

## 7. 시각화 3종

### (a) 편차 히스토그램

![악기별 마이크로타이밍 편차 히스토그램](/images/hanroro-song-analysis-06-empirical-results/01-fig1-deviation-histogram.png)

드럼(파란색)은 0 근처에 뾰족하게 몰려 있고, 보컬(초록색)은 넓게 퍼져 있다. 베이스(주황색)는 뚜렷하게 오른쪽(양수, laid-back) 쪽으로 치우쳐 있다.

### (b) 시간축 드리프트 곡선 (10초 슬라이딩 윈도우)

![시간축 마이크로타이밍 드리프트](/images/hanroro-song-analysis-06-empirical-results/02-fig2-time-drift.png)

시간 진행에 따른 편차 평균 변화. 드럼·other는 전 구간 0 근처. 보컬은 구간별 편차가 크게 요동치며, 베이스는 안정적으로 +10~+25ms 범위를 유지.

### (c) 비트-위상 히트맵

![악기별 비트-내 위상(phase) 분포](/images/hanroro-song-analysis-06-empirical-results/03-fig3-beat-phase-heatmap.png)

각 onset이 16분음표 구간 내 어느 상대 위치(0~1)에 속하는지 분포. **드럼/other는 위상 0.9~1.0 부근에 뾰족하게 몰려 박자에 강하게 고정**. **보컬은 전 구간(0~1)에 고르게 퍼져** 자유로운 타이밍을 시각적으로 확인.

---

## 8. 한계 (정직 고지)

1. **보컬 음역 C2~C6(4옥타브)는 신뢰 낮음**: 노트 미리보기에서 급격한 옥타브 점프(`G#3(56)` → `+15반음`) 확인. pYIN 옥타브 오류이거나 stem 분리 잡음일 가능성 높음. Song-DNA 상대 서열은 유효, 절대 음역 주장은 하지 않음.

2. **형식 라벨(verse/chorus)과 최종 통합 해석(§9)은 미수행**: novelty 기반 경계 후보 26개는 냈지만 어떤 구간이 "후렴"인지는 사람이 들어야 확정. 에이전트는 오디오를 청취·해석할 수 없어 이 부분은 자동 추출 결과로 대체.

3. **JHT 파스 트리는 전곡이 아니라 예시 1개**: 전곡 자동 파싱은 화성 기능 판단(어떤 코드가 prolongation vs preparation인지)에 사람 수준의 음악적 판단이 필요.

4. **`htdemucs_ft` 대신 `htdemucs` 사용**: fine-tuned 앙상블 버전 대신 단일 모델 사용으로 분리 품질이 약간 낮을 수 있다(SDR 기준 공식 문서상 소폭 차이).

5. **madmom 미사용**: librosa 두 방식이 이미 일치해 미사용. 정밀도가 더 필요하면 madmom `DBNBeatTracker`로 보완.

6. **코드 인식 한계**: 24개 장·단 3화음 템플릿만 사용 — 7th·sus4·aug 등 확장 화음은 반영되지 않음.

7. **이 분석은 Frozen Lace이지 한로로 곡이 아님**: 모든 수치와 해석은 대체곡에 해당하며, 한로로 〈사랑하게 될 거야〉에 대한 직접적 결론이 아니다.

---

## 9. 다음 단계

이 파이프라인은 **어떤 음원에도 적용 가능한 상태**로 검증됐다. 한로로 〈사랑하게 될 거야〉를 정식 구매하면:

1. `demucs -n htdemucs --out stems 사랑하게될거야.mp3` — stem 분리
2. `step2_3_4_analysis.py` 실행 → BPM/조성 실측 (미검증 추정치 "약 94 BPM, G키" 검증)
3. `step4_chords.py` → `step6_song_dna.py` → `step7_sync_timing.py` → `step8_visualize.py` 순차 실행
4. Frozen Lace와 동일한 형식의 리포트 생성

특히 확인하고 싶은 것:
- 실제 BPM과 조성 (추정치 94 BPM, G키가 맞는지)
- 보컬 laid-back 패턴 — Verse vs Chorus에서 얼마나 달라지는가
- 후렴 "사랑하게 될 거야" 가사의 가사-선율 정렬(lyric-setting) 분석

---

## 10. 이 시리즈의 다른 글

| 편 | 제목 |
|---|---|
| [1편]({% post_url 2026-08-03-hanroro-song-analysis-01-design %}) | 설계 — 왜, 어떻게 |
| [2편]({% post_url 2026-08-03-hanroro-song-analysis-02-methodology-general %}) | 방법론 1: 일반적 노래 분석 4계층 프레임워크 |
| [3편]({% post_url 2026-08-03-hanroro-song-analysis-03-methodology-song-dna %}) | 방법론 2: 촘스키식 Song-DNA 인코딩 |
| [4편]({% post_url 2026-08-03-hanroro-song-analysis-04-methodology-sync-timing %}) | 방법론 3: 악기·보컬 싱크 타이밍 분석 |
| [5편]({% post_url 2026-08-03-hanroro-song-analysis-05-implementation %}) | 상세 구현 — 코드·스크립트·트러블슈팅 |
| **6편** (현재) | 실측 결과 — Frozen Lace 파이프라인 검증 수치 |

---

*저작자 표시 (CC BY-NC 4.0): Frozen Lace by Wiseman (ft. Zenboy1955, Stefan Kartenberg, Kara Square), CC BY-NC 4.0 — https://ccmixter.org/files/Wiseman/66344*

*작성일: 2026-08-03*  
*저자: 신대용 (daeyong.shin@navercorp.com)*  
*작성 보조: Claude (Fable 5) — 방법론 수립·조사·오케스트레이션 / Antigravity (agy) — 실행·집필·이미지 생성*
