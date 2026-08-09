# Cinematic Camera Language for Shortform Bible Episodes

이 문서는 `web-game`의 짧은 마인크래프트풍 성경 애니메이션을 영화처럼 보이게 하기 위한 카메라 연출 사전이다. 목표는 캐릭터를 계속 따라가는 게임 카메라가 아니라, 장면을 해석해서 보여주는 제 3의 시청자 시점이다.

## 적용 원칙

1. 카메라는 플레이어가 아니라 이야기의 감정 중심을 따라간다.
2. 숏은 3~5초 단위로 설계한다. 짧은 영상에서는 한 숏이 한 문장처럼 읽혀야 한다.
3. 같은 장면에서도 `wide -> medium -> insert -> reaction -> reveal`처럼 거리와 정보량을 바꾼다.
4. 마인크래프트 스타일의 블록 지형은 수평/수직 라인이 강하므로, 카메라 이동은 단순하고 명확하게 둔다.
5. 과한 흔들림보다 의도 있는 이동을 우선한다. 핸드헬드는 긴장이나 현장감을 줄 때만 쓴다.

## 47 Shot Catalog

| # | Preset ID | 연출 | 쓰임 | 현재 게임 구현 |
|---|---|---|---|---|
| 01 | `establishing-crane` | Establishing crane | 사막 전체와 군중 위치를 한 번에 소개한다. | 높은 위치에서 내려오며 선생/군중을 조망한다. |
| 02 | `slow-push-in` | Slow push-in | 이야기의 핵심 인물 또는 감정으로 관객을 끌어당긴다. | 선생 쪽으로 천천히 밀고 들어간다. |
| 03 | `pull-back-reveal` | Pull-back reveal | 작은 행동이 큰 군중/기적과 연결되는 느낌을 준다. | 바구니에서 시작해 군중까지 뒤로 빠진다. |
| 04 | `lateral-tracking` | Lateral tracking | 이동감과 공간감을 만든다. | 군중 앞을 옆으로 미끄러지듯 지난다. |
| 05 | `parallax-orbit` | Parallax orbit | 피사체와 배경의 층을 분리해 입체감을 준다. | 바구니/선생 주변을 반원으로 돈다. |
| 06 | `low-angle-hero` | Low-angle hero | 인물을 크게, 상징적으로 보이게 한다. | 로봇과 선생을 낮은 각도에서 올려다본다. |
| 07 | `high-angle-god-view` | High-angle view | 장면의 질서와 규모를 보여준다. | 위에서 군중/길/오아시스 배치를 본다. |
| 08 | `over-shoulder` | Over-the-shoulder | 관객이 장면 안에 서 있는 듯한 관찰감을 준다. | 로봇 어깨 너머로 선생과 군중을 본다. |
| 09 | `pov-approach` | POV approach | 관객을 캐릭터의 행위에 가까이 붙인다. | 플레이어 진행 방향으로 걸어 들어가는 시점. |
| 10 | `whip-pan` | Whip pan | 관심 대상을 빠르게 전환한다. | 군중에서 바구니로 빠르게 팬한다. |
| 11 | `dutch-angle` | Dutch angle | 불안, 놀람, 전환감을 준다. | 약한 카메라 롤로 순간적인 긴장을 만든다. |
| 12 | `rack-focus-sim` | Rack focus simulation | 앞/뒤 관심점을 전환한다. | FOV와 look target을 바꿔 초점 이동처럼 보이게 한다. |
| 13 | `top-down-map` | Top-down map sweep | 지도처럼 동선을 이해시킨다. | 위에서 길을 따라 스윕한다. |
| 14 | `side-profile-track` | Side profile track | 걷는 캐릭터의 방향성과 리듬을 보여준다. | 플레이어 옆에서 나란히 따라간다. |
| 15 | `arc-around-basket` | Arc around object | 중요한 소품을 의식적으로 강조한다. | 바구니를 중심으로 짧은 호를 돈다. |
| 16 | `insert-close-up` | Insert close-up | 손, 음식, 검 같은 디테일을 강조한다. | 바구니/빵/물고기 가까이 붙는다. |
| 17 | `reaction-pan` | Reaction pan | 사건의 효과를 사람들의 반응으로 보여준다. | 군중 얼굴을 따라 천천히 팬한다. |
| 18 | `backlit-silhouette` | Backlit silhouette | 상징적이고 기억에 남는 이미지를 만든다. | 낮은 위치에서 하늘을 배경으로 실루엣을 만든다. |
| 19 | `handheld-witness` | Handheld witness | 현장감과 급박함을 준다. | 아주 약한 흔들림과 지연으로 관찰자 느낌을 낸다. |
| 20 | `long-lens-compression` | Long-lens compression | 군중과 피사체 사이의 거리감을 압축한다. | 멀리서 좁은 FOV로 군중/선생을 함께 잡는다. |
| 21 | `dialogue-master-two-shot` | Dialogue master two-shot | 두 사람이 대화하는 공간 관계를 먼저 알려준다. | 대화 A/B를 함께 잡는 마스터 샷. |
| 22 | `shot-reverse-speaker-a` | Shot reverse A | A가 말할 때 B의 어깨 너머로 잡는다. | 선생 쪽 대사 coverage. |
| 23 | `shot-reverse-speaker-b` | Shot reverse B | B가 말할 때 A의 어깨 너머로 잡는다. | 로봇/상대 인물 대사 coverage. |
| 24 | `dirty-over-shoulder-a` | Dirty OTS A | listener 일부를 프레임에 남겨 관계를 유지한다. | B 실루엣 너머로 A를 본다. |
| 25 | `dirty-over-shoulder-b` | Dirty OTS B | 반대 방향 dirty OTS로 리듬을 만든다. | A 실루엣 너머로 B를 본다. |
| 26 | `walk-and-talk-profile` | Walk-and-talk profile | 두 사람이 걸으며 대화하는 숏. | 좌우 구도 tracking으로 대화 리듬을 유지한다. |
| 27 | `listening-reaction-hold` | Listening reaction hold | 대사를 듣는 반응을 길게 보여준다. | listener 얼굴 중심으로 hold. |
| 28 | `dialogue-tableau-wide` | Dialogue tableau wide | 대화 관계와 군중/공간을 함께 읽힌다. | 선생, 로봇, 군중을 wide로 구성한다. |
| 29 | `combat-wide-geography` | Combat wide geography | 전투 전 위치와 거리, 퇴로를 보여준다. | 플레이어와 training dummy를 wide로 잡는다. |
| 30 | `combat-low-chase` | Combat low chase | 공격자의 추진력을 강조한다. | 낮은 위치에서 뒤따라간다. |
| 31 | `combat-impact-insert` | Combat impact insert | 칼과 타격 지점을 강조한다. | 플레이어와 dummy 사이 가까운 insert. |
| 32 | `combat-orbit-swing` | Combat orbit swing | 타격 동작의 방향성을 살린다. | 공격 중심을 짧게 회전한다. |
| 33 | `combat-overhead-tactics` | Combat overhead tactics | 다수 대상과 거리감을 한 번에 설명한다. | 위에서 dummy와 이동 경로를 본다. |
| 34 | `combat-handheld-chaos` | Combat handheld chaos | 혼란과 긴장감을 준다. | 약한 shake와 roll을 넣는다. |
| 35 | `combat-victory-push` | Combat victory push | 전투 후 승리감을 만든다. | 플레이어 쪽으로 천천히 push-in. |
| 36 | `combat-threat-reveal` | Combat threat reveal | 다음 위협을 드러낸다. | 첫 dummy에서 두 번째 dummy로 관심점을 전환한다. |
| 37 | `monologue-center-frame` | Monologue center frame | 독백을 정면에서 또렷하게 전달한다. | 선생을 중심에 둔 medium shot. |
| 38 | `monologue-slow-circle` | Monologue slow circle | 내면적 전환을 부드럽게 만든다. | 인물 주변을 천천히 돈다. |
| 39 | `monologue-profile-negative-space` | Profile negative space | 고독, 결심, 묵상을 표현한다. | 옆얼굴과 빈 사막 공간을 함께 둔다. |
| 40 | `monologue-skyward-tilt` | Skyward tilt | 하늘/신성/규모감으로 감정을 확장한다. | 인물에서 하늘 쪽으로 tilt. |
| 41 | `monologue-lantern-close` | Monologue prop close | 상징적 소품을 통해 대사를 받쳐준다. | 바구니/빵/물고기 foreground close. |
| 42 | `monologue-walkaway-wide` | Monologue walkaway wide | 독백 뒤 여운과 여정을 남긴다. | 인물이 풍경 속으로 작아지는 wide. |
| 43 | `wrestling-river-wide` | Jabbok river wide | 얍복강 밤 장면과 야곱의 고립을 소개한다. | 강, 야곱, 브니엘 방향을 wide로 잡는다. |
| 44 | `wrestling-clinch-orbit` | Wrestling clinch orbit | 씨름의 물리적 접촉과 버팀을 보여준다. | 야곱/상대의 중심을 짧게 orbit한다. |
| 45 | `wrestling-hip-touch-impact` | Hip touch impact | 허벅지 타격 순간을 강조한다. | 낮고 가까운 insert와 약한 shake를 쓴다. |
| 46 | `israel-name-reveal` | Israel name reveal | 이름이 바뀌는 장면을 상징적으로 보이게 한다. | 야곱을 향해 push-in하며 축복 순간을 잡는다. |
| 47 | `sunrise-limp-wide` | Sunrise limp wide | 다친 몸과 새 이름을 엔딩 이미지로 남긴다. | 야곱이 절뚝이며 작아지는 wide. |

## Scene Coverage Recipes

| 장면 유형 | 기본 순서 |
|---|---|
| 두 사람 대화 | `dialogue-master-two-shot` -> `shot-reverse-speaker-a` -> `shot-reverse-speaker-b` -> `listening-reaction-hold` |
| 군중 앞 설교 | `dialogue-tableau-wide` -> `slow-push-in` -> `reaction-pan` -> `high-angle-god-view` |
| 전투 | `combat-wide-geography` -> `combat-low-chase` -> `combat-impact-insert` -> `combat-victory-push` |
| 혼란스러운 전투 | `combat-threat-reveal` -> `combat-handheld-chaos` -> `combat-overhead-tactics` -> `combat-orbit-swing` |
| 혼자 독백 | `monologue-center-frame` -> `monologue-profile-negative-space` -> `monologue-skyward-tilt` |
| 엔딩 독백 | `monologue-slow-circle` -> `monologue-lantern-close` -> `monologue-walkaway-wide` |
| 야곱의 얍복강 씨름 | `wrestling-river-wide` -> `wrestling-clinch-orbit` -> `wrestling-hip-touch-impact` -> `israel-name-reveal` -> `sunrise-limp-wide` |

## Runtime Usage

```bash
npm run game:serve
```

브라우저에서 다음 URL을 연다.

```text
http://127.0.0.1:4173/web-game/?demo=1&camera=showcase
```

특정 숏만 고정해서 보려면 `shot` 파라미터를 붙인다.

```text
http://127.0.0.1:4173/web-game/?demo=1&shot=low-angle-hero
```

기존 캐릭터 추적 카메라가 필요하면 다음처럼 연다.

```text
http://127.0.0.1:4173/web-game/?camera=follow
```
