# Film Scene DSL

`web-game/src/scene-dsl.ts`는 짧은 성경 애니메이션을 데이터로 연출하기 위한 DSL이다. 캐릭터 이동, 캐릭터 액션, 대사, 카메라 shot, 피격 피드백을 하나의 timeline에 둔다.

## Schema

```ts
type FilmSceneDsl = {
  id: string;
  title: string;
  bibleReference: string;
  durationSeconds: number;
  source: { label: string; url: string; note: string };
  sky?: { topColor: number; horizonColor: number; sunColor: number };
  actors: DslActorDef[];
  timeline: DslCue[];
};
```

| Cue | 역할 |
|---|---|
| `actor` | actor id, position, lookAt, action, movement interpolation을 바꾼다. |
| `camera` | cinematic shot id를 선택한다. `shot=` URL 파라미터가 있으면 고정 shot을 우선한다. |
| `dialogue` | speaker와 line을 dialogue panel에 표시한다. |
| `hit` | attacker/receiver를 지정하고 맞은 캐릭터에게 hurt/block 계열 피드백을 준다. |

## Runtime Contract

- Actor position은 world 좌표 `[x, yOffset, z]`다. `yOffset`은 현재 terrain height 위에 더해진다.
- `lookAt`은 actor id 또는 좌표를 받을 수 있다. Minecraft skin box의 front UV가 붙은 `+Z` 면이 목표를 향하도록 계산한다.
- `action`은 `web-game/src/character-actions.ts`의 `CharacterActionId`를 사용한다.
- `hit` cue는 한 loop 안에서 한 번만 발화한다. loop가 다시 시작되면 fired event set이 초기화된다.
- 피격 피드백은 세 가지로 보인다: hit reaction action, 짧은 recoil, voxel impact burst.
- 카메라 anchor는 actor id에서 생성된다. 야곱 장면에서는 `jacob`, `divine-messenger`, `dialogue-a`, `dialogue-b`, `combat-target`, `monologue-subject`가 DSL actor를 가리킨다.

## Jacob Wrestling Scene

소스 본문은 [대한성서공회 창세기 32장](https://www.bskorea.or.kr/bible/korbibReadpage.php?version=GAE&book=gen&chap=32&sec=1&cVersion=SAENEW%5E&fontSize=15px&fontWeight=normal)이다. 장면의 핵심은 유다가 아니라 야곱이다. 야곱은 홀로 남아 씨름하고, 허벅지를 맞고, 축복을 구하고, `이스라엘`이라는 이름을 받는다.

| Time | Object | Change |
|---:|---|---|
| 0.0 | `jacob` | `walk`, camp side에서 Jabbok river 방향으로 이동한다. |
| 2.7 | `jacob` | `pray`, 두려움과 고립감을 보여준다. |
| 4.4 | `divine-messenger` | `guard`, 야곱 쪽으로 접근한다. |
| 6.0 | both | `wrestle`, 서로를 바라보고 가까운 거리에서 밀고 당긴다. |
| 7.0 | `divine-messenger` | `hit` receiver, `block` reaction과 impact burst가 나온다. |
| 9.35 | `jacob` | `hit` receiver, `hurt` reaction, recoil, camera shake가 나온다. |
| 10.8 | `jacob` | `grapple`, 다친 뒤에도 놓지 않는 상태를 보여준다. |
| 16.1 | `divine-messenger` | `bless`, 축복과 이름 변경 cue로 전환한다. |
| 16.2 | `jacob` | `kneel`, 이름을 받는 순간을 만든다. |
| 20.2 | `jacob` | `limp`, 해가 밝아오는 쪽으로 절뚝이며 나간다. |

## Play URL

```text
http://127.0.0.1:4173/web-game/?demo=1&episode=jacob-wrestling
```

## Validation

```bash
npm run game:jacob-check
```

검증은 browser snapshot에서 `story.episodeId === "jacob-wrestling"`, actor `jacob`/`divine-messenger`, 2회 이상의 character hit feedback, 47개 이상의 camera shot, nonblank canvas를 확인한다.
