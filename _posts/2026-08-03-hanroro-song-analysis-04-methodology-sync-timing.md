---
layout: post
title: "한로로 노래 분석 프로젝트 4편 — 방법론 3: 악기·보컬 싱크 타이밍 분석"
date: 2026-08-03
author: dosuser
tags: [한로로, 노래분석, microtiming, Demucs, onset, laid-back, pushed, 신호처리, MIR]
---

> **자가 확인용 기록**: 이 글은 프로젝트를 다음에 이어서 진행할 수 있도록 작성자가 개인적으로 진행 상황을 기록·검증하기 위해 쓴 글이다. 공식 발표나 완결된 튜토리얼이 아니라 작업 로그에 가깝다.

한 줄 요약:

> "각 악기의 onset이 비트 그리드 대비 몇 ms 앞/뒤에 오는가를 측정한다 — 0~50ms 범위의 미세 편차(microtiming)가 그루브와 장르 특성을 만들며, 5단계 파이프라인(Demucs→비트그리드→onset→강제정렬→편차)으로 자동화할 수 있다."

---

## 목차

1. [마이크로타이밍이란 무엇인가](#1-마이크로타이밍이란-무엇인가)
2. [5단계 파이프라인 개요](#2-5단계-파이프라인-개요)
3. [STEP 1: 소스 분리 (Stem 추출)](#3-step-1-소스-분리-stem-추출)
4. [STEP 2: 비트 그리드 산출](#4-step-2-비트-그리드-산출)
5. [STEP 3: Stem별 Onset 추출](#5-step-3-stem별-onset-추출)
6. [STEP 4: 가사-오디오 강제 정렬 (보컬 타이밍 정밀화)](#6-step-4-가사-오디오-강제-정렬-보컬-타이밍-정밀화)
7. [STEP 5: 편차 계산·시각화](#7-step-5-편차-계산시각화)
8. [오차 요인 (정직 고지)](#8-오차-요인-정직-고지)
9. [이 시리즈의 다른 글](#9-이-시리즈의-다른-글)

---

## 1. 마이크로타이밍이란 무엇인가

![싱크 타이밍 분석 파이프라인 및 Laid-back vs Pushed 개념도](/images/hanroro-song-analysis-04-methodology-sync-timing/01-sync-timing-pipeline.jpg)

**Microtiming**(마이크로타이밍): 각 악기의 실제 onset이 기준 비트 그리드에서 벗어난 **부호 있는 편차(ms)**. 

- 평균이 양수(+): **laid-back** — 비트보다 뒤로 끌림. 느슨하고 릴랙스한 느낌
- 평균이 음수(-): **pushed** — 비트보다 앞으로 밀림. 긴장되고 앞으로 나아가는 느낌

**유의미 범위 근거**: Sioros, Câmara & Danielsen, ISMIR 2019 — [https://archives.ismir.net/ismir2019/paper/000095.pdf](https://archives.ismir.net/ismir2019/paper/000095.pdf)
> "Typical reported values of microtiming deviations in performance range from 0 ms (no displacement) to 50 ms or more, depending on instrument, tempo and genre."
> "drummers were able to consistently play a snare-drum pattern with laid-back and pushed feel significantly behind- and ahead-of an instructed on-beat performance"

**지각 효과**: Senn et al., *Frontiers in Psychology* 2016 — [https://www.frontiersin.org/journals/psychology/articles/10.3389/fpsyg.2016.01487/full](https://www.frontiersin.org/journals/psychology/articles/10.3389/fpsyg.2016.01487/full)
> "groove ratings were high for microtiming magnitudes equal or smaller than those originally performed and decreased for exaggerated microtiming magnitudes"

즉: 그루브는 **0~50ms 수준의 절제된 microtiming에서 발생하고, 과장되면 오히려 떨어진다**.

참고 수치: Danielsen et al., *JNMR* 2022 — 96 BPM laid-back 조건 스네어 평균 **17.4ms 지연** (검색 스니펫 기반, 원문 403 접근 차단으로 ISMIR 2019로 근거 대체)

---

## 2. 5단계 파이프라인 개요

```
[음원] → STEP1 소스 분리 → STEP2 비트 그리드 → STEP3 onset 추출
                                                         ↓
                        [편차 통계(ms)] ← STEP5 편차 계산 ← STEP4 강제 정렬(보컬)
```

이 파이프라인의 특징:
- **기준 그리드**: 절대 메트로놈이 없으므로 **드럼 stem에서 추정한 비트**를 기준으로 사용 (Danielsen 계열 inter-instrument asynchrony 방식)
- **게이팅**: ±80ms를 초과하는 편차는 "다른 음표에 대한 매칭"으로 보고 제외

---

## 3. STEP 1: 소스 분리 (Stem 추출)

**도구**: Demucs v4 `htdemucs` — Facebook Research

출처: [https://github.com/facebookresearch/demucs](https://github.com/facebookresearch/demucs)
> "The v4 version features Hybrid Transformer Demucs, a hybrid spectrogram/waveform separation model using Transformers."

**4-stem 출력**: vocals / drums / bass / other (6-stem은 `htdemucs_6s`로 guitar/piano 추가)

선택 이유: 파형 도메인 병용으로 onset 트랜지언트 보존에 유리 (SDR 9.0 dB — Spleeter/Open-Unmix 대비 우위).

```bash
pip install demucs
demucs -n htdemucs --out stems 곡파일.mp3
```

**실행 시 주의 (실제 경험)**:
- `htdemucs_ft`(4-모델 앙상블)는 개별 모델 파일 해시 불일치 오류 발생 가능 → `htdemucs`(단일 모델)로 전환
- 자세한 트러블슈팅은 [5편]({% post_url 2026-08-03-hanroro-song-analysis-05-implementation %})에서

대안 도구:
- Spleeter (Deezer): [https://github.com/deezer/spleeter](https://github.com/deezer/spleeter) — "100x faster than real-time when run on a GPU"
- Open-Unmix: [https://github.com/sigsep/open-unmix-pytorch](https://github.com/sigsep/open-unmix-pytorch) — 연구 baseline

---

## 4. STEP 2: 비트 그리드 산출

기준 그리드는 **드럼 stem에서 추정한 비트**로 정의하고, 16분음표 서브디비전으로 보간한다.

**1차 도구 (librosa)**:
```python
import librosa
import numpy as np

y_drums, sr = librosa.load("stems/htdemucs/곡명/drums.wav", sr=44100, mono=True)
tempo, beat_frames = librosa.beat.beat_track(y=y_drums, sr=sr, units="frames")
beat_times = librosa.frames_to_time(beat_frames, sr=sr)

# 16분음표 그리드로 보간 (beat 사이 4등분)
grid = np.interp(
    np.arange(0, (len(beat_times) - 1) * 4 + 1) / 4.0,
    np.arange(len(beat_times)), beat_times
)
```

출처: [https://librosa.org/doc/0.11.0/generated/librosa.beat.beat_track.html](https://librosa.org/doc/0.11.0/generated/librosa.beat.beat_track.html)
> "Beats are detected in three stages: 1. Measure onset strength 2. Estimate tempo from onset correlation 3. Pick peaks…"

**2차 도구 (madmom, 더 정밀)**:
```python
from madmom.features.beats import RNNBeatProcessor, DBNBeatTrackingProcessor
beats = DBNBeatTrackingProcessor(fps=100)(RNNBeatProcessor()("drums.wav"))
```

출처: [https://github.com/CPJKU/madmom](https://github.com/CPJKU/madmom)
> "Madmom is an audio signal processing library written in Python with a strong focus on music information retrieval (MIR) tasks."

실측에서는 librosa 두 방식이 이미 일치(97.51 BPM)해 madmom 사용이 불필요했다 — 정밀도가 더 필요하면 madmom 경로를 시도.

---

## 5. STEP 3: Stem별 Onset 추출

**onset**: 각 음표·타격이 시작되는 시각 (초 단위). 

| 악기 | 추출 방법 | 이유 |
|---|---|---|
| **drums** | spectral flux — `librosa.onset.onset_detect` | 타격이 sharp해서 spectral flux로 충분 |
| **bass** | spectral flux | 동일 |
| **vocals** | soft onset이라 아래 STEP 4 강제 정렬로 보완 | 멜리스마·긴 모음에서 onset이 불분명 |
| **other** | spectral flux | 동일 |

```python
def onsets(path):
    y, sr = librosa.load(path, sr=44100)
    return librosa.onset.onset_detect(y=y, sr=sr, units="time", backtrack=False)
```

출처: [https://librosa.org/doc/0.11.0/generated/librosa.onset.onset_detect.html](https://librosa.org/doc/0.11.0/generated/librosa.onset.onset_detect.html)
> "Locate note onset events by picking peaks in an onset strength envelope."

---

## 6. STEP 4: 가사-오디오 강제 정렬 (보컬 타이밍 정밀화)

**강제 정렬(forced alignment)**: 가사 텍스트와 오디오를 음소 단위로 정렬해 각 단어의 타임스탬프를 얻는 기법.

**WhisperX**:
- [https://github.com/m-bain/whisperX](https://github.com/m-bain/whisperX)
- Whisper 전사 + wav2vec2 음소 정렬로 단어 단위 타임스탬프
> "This repository provides fast automatic speech recognition (70x realtime with large-v2) with word-level timestamps and speaker diarization."

**MFA (Montreal Forced Aligner)**:
- [https://montreal-forced-aligner.readthedocs.io/en/stable/user_guide/index.html](https://montreal-forced-aligner.readthedocs.io/en/stable/user_guide/index.html)
- 가사 텍스트를 알 때 음소 수준 정렬
> "Forced alignment is a technique to take an orthographic transcription of an audio file and generate a time-aligned version using a pronunciation dictionary to look up phones for words."

**주의**: speech 모델이라 가창(멜리스마·긴 모음)에서 흔들림 — **보컬 stem을 입력**하고 결과를 육안 검수 필요.

---

## 7. STEP 5: 편차 계산·시각화

**매칭 게이트**: ±80ms — 이 범위를 초과하면 다른 음표에 대한 매칭으로 간주해 제외.

근거: microtiming 유의미 범위 "0 ms … to 50 ms or more" (ISMIR 2019) → 80ms 게이트로 안전하게 잡음 제거.

```python
GATE_MS = 80.0

for name, path in stems.items():
    ons = onsets(path)
    # 각 onset에 가장 가까운 그리드 포인트 찾기
    idx = np.argmin(np.abs(ons[:, None] - grid[None, :]), axis=1)
    dev_ms = (ons - grid[idx]) * 1000.0
    kept = dev_ms[np.abs(dev_ms) < GATE_MS]
    
    # 해석
    if np.mean(kept) > 3:    # +3ms 이상: laid-back
        interpretation = "laid-back"
    elif np.mean(kept) < -3: # -3ms 이하: pushed
        interpretation = "pushed"
    else:                    # ±3ms: 그리드와 거의 일치
        interpretation = "on-grid"
```

**시각화 3종**:

| 그림 | 내용 | 해석 포인트 |
|---|---|---|
| **(a) 편차 히스토그램** | 악기별 편차 분포 오버레이 | 분포 중심이 + or -인지, 얼마나 퍼졌는지 |
| **(b) 시간축 드리프트 곡선** | 10초 슬라이딩 윈도우 평균 편차 | verse vs chorus 구간 비교 가능 |
| **(c) Beat-phase 히트맵** | 각 onset의 16분음표 구간 내 상대 위치(0~1) | 악기가 박자에 얼마나 고정됐는지 한눈에 |

---

## 8. 오차 요인 (정직 고지)

1. **소스 분리 아티팩트**: Demucs의 stem 분리가 완벽하지 않아 다른 악기 소리가 새어들 수 있다 → onset이 수 ms 흔들릴 수 있다. **±수 ms는 오차 범위로 해석할 것.**

2. **WhisperX 타임스탬프 부정확 사례**: [GitHub Issue #1247](https://github.com/m-bain/whisperX/issues/1247) — 가창 특화 환경에서 부정확 보고 있음. 보컬 결과 해석 시 주의.

3. **기준 그리드 자체가 드럼에서 추정됨**: 드럼 편차는 "절대적으로 이르다/늦다"가 아니라 "16분 그리드 대비 서브비트 위치"를 의미한다.

4. **Danielsen 2022의 17.4ms 수치는 퍼블리셔 403**으로 원문 인용 미확보 — ISMIR 2019 동일 그룹 논문으로 근거 대체.

---

## 9. 이 시리즈의 다른 글

| 편 | 제목 |
|---|---|
| [1편]({% post_url 2026-08-03-hanroro-song-analysis-01-design %}) | 설계 — 왜, 어떻게 |
| [2편]({% post_url 2026-08-03-hanroro-song-analysis-02-methodology-general %}) | 방법론 1: 일반적 노래 분석 4계층 프레임워크 |
| [3편]({% post_url 2026-08-03-hanroro-song-analysis-03-methodology-song-dna %}) | 방법론 2: 촘스키식 Song-DNA 인코딩 |
| **4편** (현재) | 방법론 3: 악기·보컬 싱크 타이밍 분석 |
| [5편]({% post_url 2026-08-03-hanroro-song-analysis-05-implementation %}) | 상세 구현 — 코드·스크립트·트러블슈팅 |
| [6편]({% post_url 2026-08-03-hanroro-song-analysis-06-empirical-results %}) | 실측 결과 — Frozen Lace 파이프라인 검증 수치 |

---

*작성일: 2026-08-03*  
*저자: 신대용 (daeyong.shin@navercorp.com)*  
*작성 보조: Claude (Fable 5) — 방법론 수립·조사·오케스트레이션 / Antigravity (agy) — 실행·집필·이미지 생성*
