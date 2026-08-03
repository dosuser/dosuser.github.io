---
layout: post
title: "한로로 노래 분석 프로젝트 5편 — 상세 구현: 코드·스크립트·트러블슈팅"
date: 2026-08-03
author: dosuser
tags: [한로로, 노래분석, librosa, Demucs, Python, 구현, 트러블슈팅, MIR, 코드]
---

> **자가 확인용 기록**: 이 글은 프로젝트를 다음에 이어서 진행할 수 있도록 작성자가 개인적으로 진행 상황을 기록·검증하기 위해 쓴 글이다. 공식 발표나 완결된 튜토리얼이 아니라 작업 로그에 가깝다.

한 줄 요약:

> "방법론 3편의 파이프라인을 실제로 어떤 코드로 구현했는가 — pYIN 기반 Song-DNA, Demucs stem 분리 기반 microtiming, librosa 구조분할의 실제 스크립트와 트러블슈팅을 상세히 기록한다."

---

## 목차

1. [전체 데이터 흐름도](#1-전체-데이터-흐름도)
2. [구현 1: 구조 분할·BPM·조성 (step2_3_4_analysis.py)](#2-구현-1-구조-분할bpm조성-step2_3_4_analysispy)
3. [구현 2: 코드 인식 (step4_chords.py)](#3-구현-2-코드-인식-step4_chordspy)
4. [구현 3: Song-DNA 인코딩 (step6_song_dna.py)](#4-구현-3-song-dna-인코딩-step6_song_dnapy)
5. [구현 4: 싱크 타이밍 (step7_sync_timing.py)](#5-구현-4-싱크-타이밍-step7_sync_timingpy)
6. [구현 5: 시각화 (step8_visualize.py)](#6-구현-5-시각화-step8_visualizepy)
7. [트러블슈팅](#7-트러블슈팅)
8. [이 시리즈의 다른 글](#8-이-시리즈의-다른-글)

---

## 1. 전체 데이터 흐름도

![구현 스크립트 데이터 흐름도](/images/hanroro-song-analysis-05-implementation/01-implementation-dataflow.jpg)

음원 파일 한 개에서 시작해 5개의 스크립트가 순서에 따라 연쇄 실행된다. 각 스크립트의 입·출력:

| 스크립트 | 주요 입력 | 주요 출력 |
|---|---|---|
| `Demucs` (외부 실행) | 원본 MP3 | `stems/{vocals,drums,bass,other}.wav` |
| `step2_3_4_analysis.py` | 원본 MP3 | BPM, 비트 그리드, 구조 경계 26개, 조성 |
| `step4_chords.py` | 원본 MP3 | 코드열 78구간 (Harte 표기, JSON) |
| `step6_song_dna.py` | `stems/vocals.wav` | Parsons code 164노트, ±11 반음 서열 (JSON) |
| `step7_sync_timing.py` | `stems/*.wav` | 편차 통계 (mean±SD ms, JSON) |
| `step8_visualize.py` | `stems/*.wav` + 비트 그리드 | fig1·fig2·fig3 PNG |

---

## 2. 구현 1: 구조 분할·BPM·조성 (step2_3_4_analysis.py)

BPM과 조성 추정, 구조 경계를 동시에 처리한다.

```python
import librosa
import numpy as np
import json
from scipy.signal import find_peaks

PATH = "음원파일.mp3"
y, sr = librosa.load(PATH, sr=44100, mono=True)

# STEP 2: BPM 두 방식 교차 검증
tempo, beat_frames = librosa.beat.beat_track(y=y, sr=sr, units="frames")
beat_times = librosa.frames_to_time(beat_frames, sr=sr)
tempo_val = float(np.atleast_1d(tempo)[0])

onset_env = librosa.onset.onset_strength(y=y, sr=sr)
tempo2 = librosa.feature.tempo(onset_envelope=onset_env, sr=sr)
tempo2_val = float(np.atleast_1d(tempo2)[0])
# 두 방식이 일치하면 신뢰도 높음. Frozen Lace 실측: 97.51 BPM (두 방식 일치)

# STEP 4: 조성 — Krumhansl-Schmuckler 프로파일로 24개 장·단조 비교
chroma = librosa.feature.chroma_cqt(y=y, sr=sr)
chroma_mean = chroma.mean(axis=1)

major_profile = np.array([6.35,2.23,3.48,2.33,4.38,4.09,2.52,5.19,2.39,3.66,2.29,2.88])
minor_profile = np.array([6.33,2.68,3.52,5.38,2.60,3.53,2.54,4.75,3.98,2.69,3.34,3.17])
pitch_classes = ['C','C#','D','D#','E','F','F#','G','G#','A','A#','B']

def best_key(profile):
    scores = []
    for shift in range(12):
        p = np.roll(profile, shift)
        score = np.corrcoef(chroma_mean, p)[0,1]
        scores.append(score)
    best = int(np.argmax(scores))
    return pitch_classes[best], scores[best]

maj_key, maj_score = best_key(major_profile)
min_key, min_score = best_key(minor_profile)
# Frozen Lace 결과: F major (상관계수 0.806), F minor (0.601)

# STEP 3: 구조 분할 (self-similarity 기반)
mfcc = librosa.feature.mfcc(y=y, sr=sr, n_mfcc=13)
feat = np.vstack([chroma, mfcc])
feat_sync = librosa.util.sync(feat, beat_frames, aggregate=np.median)
R = librosa.segment.recurrence_matrix(feat_sync, mode='affinity', sym=True)
novelty = np.sum(np.abs(np.diff(R, axis=1)), axis=0)
peaks, _ = find_peaks(novelty, distance=8, height=np.percentile(novelty, 75))
boundary_beats = beat_times[np.clip(peaks, 0, len(beat_times)-1)]
# Frozen Lace 결과: 26개 경계 후보 (의미 라벨은 사람의 청취 확인 필요)
```

**한계**: novelty 기반 26개 경계는 "경계가 있을 가능성이 높은 지점"이지, verse/chorus 같은 의미 라벨이 아니다. 에이전트는 오디오를 직접 청취·해석할 수 없어 이 부분은 사람의 확인이 필요하다.

---

## 3. 구현 2: 코드 인식 (step4_chords.py)

24개 장·단 3화음 템플릿 상관 매칭으로 코드를 추정하고 Harte 표기로 출력한다.

```python
# 24-템플릿: 12개 장조 + 12개 단조 3화음
templates = {}
for i, pc in enumerate(pitch_classes):
    # 장조 (근음, 장3도, 완전5도)
    t_maj = np.zeros(12); t_maj[[i, (i+4)%12, (i+7)%12]] = 1
    templates[f"{pc}:maj"] = t_maj
    # 단조 (근음, 단3도, 완전5도)
    t_min = np.zeros(12); t_min[[i, (i+3)%12, (i+7)%12]] = 1
    templates[f"{pc}:min"] = t_min

# 2비트 이상 지속 구간만 추출 → 78개 구간 (241개 원시 구간 중)
```

**한계**: 7th·sus4 등 확장 화음은 반영되지 않는다 — Harte 표기 확장은 추가 작업 필요.

---

## 4. 구현 3: Song-DNA 인코딩 (step6_song_dna.py)

pYIN(probabilistic YIN) 기반 f0 트래킹으로 보컬 멜로디를 추출하고, Parsons code와 ±11 반음 서열로 변환한다.

```python
import librosa
import numpy as np

VOCALS = "stems/htdemucs/곡명/vocals.wav"
y, sr = librosa.load(VOCALS, sr=44100, mono=True)

# pYIN 기반 f0 트래킹 (보컬 음역대: C2~C6, 65~1000Hz)
f0, voiced_flag, voiced_prob = librosa.pyin(
    y, fmin=librosa.note_to_hz('C2'), fmax=librosa.note_to_hz('C6'), sr=sr
)
times = librosa.times_like(f0, sr=sr)

# voiced 구간만 추출, MIDI 노트 번호로 변환
voiced_idx = np.where(voiced_flag)[0]
midi = librosa.hz_to_midi(f0[voiced_idx])
t_voiced = times[voiced_idx]

# 연속 프레임을 "노트"로 뭉치기: 반음 단위 반올림 후 값이 바뀌는 지점마다 새 노트
midi_round = np.round(midi).astype(int)
notes = []
cur_val, cur_start = midi_round[0], t_voiced[0]
for i in range(1, len(midi_round)):
    gap = t_voiced[i] - t_voiced[i-1]
    if midi_round[i] != cur_val or gap > 0.15:  # 0.15초 이상 끊기면 새 노트
        notes.append({"midi": int(cur_val), "start": float(cur_start), "end": float(t_voiced[i-1])})
        cur_val, cur_start = midi_round[i], t_voiced[i]

# 80ms 미만 노트는 트래킹 잡음으로 제거
MIN_DUR = 0.08
clean_notes = [n for n in notes if (n["end"] - n["start"]) >= MIN_DUR]
# Frozen Lace 결과: 원시 1,467개 → 정제 후 164개

# Parsons code (*, u, d, r)
parsons = "*"
intervals_semitone = []
for i in range(1, len(clean_notes)):
    diff = clean_notes[i]["midi"] - clean_notes[i-1]["midi"]
    intervals_semitone.append(int(diff))
    parsons += "u" if diff > 0 else ("d" if diff < 0 else "r")

# ±11 반음 서열 (옥타브 폴딩: EvoMUSART 방식)
folded = []
for d in intervals_semitone:
    v = ((d + 11) % 24) - 11
    v = max(-11, min(11, v))
    folded.append(v)

# Frozen Lace 결과:
# Parsons code (앞 80자): *uuuddrrdduruddurdurrrdrdrududruurrrrddduduurrddrududuurdurdduuuduuddruddrdurrrd...
# ±11 반음 서열 (앞 20개): [1, 3, 11, 10, -1, 0, 0, -2, -3, 5, 0, 2, -1, -1, -5, 0, -1, 1, 0, 0]
```

---

## 5. 구현 4: 싱크 타이밍 (step7_sync_timing.py)

Stem별 onset을 비트 그리드와 매칭해 편차(ms)를 계산한다.

```python
import librosa
import numpy as np

STEM_DIR = "stems/htdemucs/곡명"
GATE_MS = 80.0

# 드럼 stem으로 비트 그리드
y_grid, sr = librosa.load(f"{STEM_DIR}/drums.wav", sr=44100, mono=True)
tempo, beat_frames = librosa.beat.beat_track(y=y_grid, sr=sr, units="frames")
beat_times = librosa.frames_to_time(beat_frames, sr=sr)
grid = np.interp(
    np.arange(0, (len(beat_times) - 1) * 4 + 1) / 4.0,
    np.arange(len(beat_times)), beat_times
)

def onsets(path):
    y, sr_ = librosa.load(path, sr=44100, mono=True)
    return librosa.onset.onset_detect(y=y, sr=sr_, units="time", backtrack=False)

result = {}
for name in ["drums", "bass", "vocals", "other"]:
    ons = onsets(f"{STEM_DIR}/{name}.wav")
    # 각 onset에 가장 가까운 그리드 포인트
    idx = np.argmin(np.abs(ons[:, None] - grid[None, :]), axis=1)
    dev_ms = (ons - grid[idx]) * 1000.0
    kept = dev_ms[np.abs(dev_ms) < GATE_MS]
    result[name] = {
        "mean_ms": float(np.mean(kept)),
        "std_ms": float(np.std(kept)),
        "n": len(kept)
    }
```

**Frozen Lace 실측 결과**:

| stem | onset 수 | 평균 편차 | 표준편차 | 해석 |
|---|---|---|---|---|
| drums | 1,304 | **-5.25ms** | 12.20ms | pushed(그리드보다 앞) |
| bass | 136 | **+18.52ms** | 21.79ms | laid-back |
| vocals | 548 | **+4.51ms** | **42.75ms** | 박자에 거의 안 묶임 |
| other | 877 | -0.78ms | 28.76ms | 그리드와 거의 일치 |

---

## 6. 구현 5: 시각화 (step8_visualize.py)

3종의 그림을 생성한다. 주요 구현 포인트:

```python
import matplotlib
matplotlib.use("Agg")  # 헤드리스 환경 필수
import matplotlib.pyplot as plt
import matplotlib.font_manager as fm

# 한글 폰트 설정 (macOS)
_font_path = "/System/Library/Fonts/AppleSDGothicNeo.ttc"
fm.fontManager.addfont(_font_path)
plt.rcParams["font.family"] = fm.FontProperties(fname=_font_path).get_name()
plt.rcParams["axes.unicode_minus"] = False  # 마이너스 기호 깨짐 방지

# (a) 히스토그램: 악기별 편차 분포 오버레이
# (b) 시간축 드리프트: 10초 슬라이딩 윈도우 평균
# (c) Beat-phase 히트맵: 16분음표 구간 내 상대 위치(0~1) 분포
```

다크 테마 대응: 흰 배경 기반으로 생성해 jekyll-theme-chirpy dark mode에서도 시인성 확보.

---

## 7. 트러블슈팅

### T1. `htdemucs_ft` 다운로드 실패 (해시 불일치)

**증상**: `demucs -n htdemucs_ft` 실행 시 4개 모델 중 1개 파일이 해시 불일치 오류로 다운로드 실패.

**원인**: `htdemucs_ft`는 4개 모델의 앙상블 — 개별 모델 파일이 여러 위치에서 다운로드되는데 그 중 하나에서 불일치.

**해결**: 단일 모델 `htdemucs`로 전환.
```bash
demucs -n htdemucs --out stems 파일.mp3
```

분리 품질 차이: 공식 문서상 `htdemucs_ft`가 SDR 기준으로 소폭 높지만, 파이프라인 검증 목적에는 `htdemucs`로 충분했다.

### T2. madmom 최신 Python 환경 빌드 실패

**증상**: `collections.MutableSequence` deprecation (Python 3.10+), `np.float` 별칭 제거 등으로 madmom 빌드 실패.

**해결**: librosa `beat.beat_track`의 두 방식(beat_track + tempo estimator)이 이미 일치(97.51 BPM)해 madmom 없이 진행. 정밀도가 더 필요하면:
```bash
pip install madmom  # 실패 시 --no-build-isolation 시도
```

### T3. 보컬 음역 C2~C6 (4옥타브) 이상치

**증상**: pYIN 결과에 `F2(41) → F#2(42) → A2(45) → G#3(56, +15반음 점프) → F#2(42)` 같은 급격한 옥타브 점프가 나타남.

**원인**: pYIN의 옥타브 오류이거나 stem 분리 시 다른 소스가 새어든 결과.

**처리**: Song-DNA **상대 음정 서열**(Parsons code, ±11 서열)은 유효하지만, **절대 음역 주장은 하지 않는다**. 상대 서열은 옥타브 오류에 덜 민감하기 때문.

### T4. 한글 폰트 깨짐 (matplotlib)

**증상**: matplotlib 그림의 한글 레이블이 □□□ 로 출력.

**해결**: macOS의 AppleSDGothicNeo.ttc를 fontManager에 직접 등록.
```python
import matplotlib.font_manager as fm
_font_path = "/System/Library/Fonts/AppleSDGothicNeo.ttc"
fm.fontManager.addfont(_font_path)
plt.rcParams["font.family"] = fm.FontProperties(fname=_font_path).get_name()
```

---

## 8. 이 시리즈의 다른 글

| 편 | 제목 |
|---|---|
| [1편]({% post_url 2026-08-03-hanroro-song-analysis-01-design %}) | 설계 — 왜, 어떻게 |
| [2편]({% post_url 2026-08-03-hanroro-song-analysis-02-methodology-general %}) | 방법론 1: 일반적 노래 분석 4계층 프레임워크 |
| [3편]({% post_url 2026-08-03-hanroro-song-analysis-03-methodology-song-dna %}) | 방법론 2: 촘스키식 Song-DNA 인코딩 |
| [4편]({% post_url 2026-08-03-hanroro-song-analysis-04-methodology-sync-timing %}) | 방법론 3: 악기·보컬 싱크 타이밍 분석 |
| **5편** (현재) | 상세 구현 — 코드·스크립트·트러블슈팅 |
| [6편]({% post_url 2026-08-03-hanroro-song-analysis-06-empirical-results %}) | 실측 결과 — Frozen Lace 파이프라인 검증 수치 |

---

*작성일: 2026-08-03*  
*저자: 신대용 (daeyong.shin@navercorp.com)*  
*작성 보조: Claude (Fable 5) — 방법론 수립·조사·오케스트레이션 / Antigravity (agy) — 실행·집필·이미지 생성*
