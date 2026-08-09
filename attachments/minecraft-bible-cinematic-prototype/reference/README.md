# Minecraft Bedrock Add-On Starter

Minecraft Bedrock Edition 애드온을 TypeScript로 작성하고 `.mcaddon`으로 패키징하는 최소 개발 환경이다.

## 요구사항

- Node.js 20 이상
- npm
- 테스트할 Minecraft Bedrock Edition 클라이언트 또는 Bedrock Dedicated Server

macOS에서는 Bedrock 클라이언트가 기본 제공되지 않으므로, 이 프로젝트는 빌드와 패키징까지 로컬에서 처리한다. 실제 실행 테스트는 Windows, Android, iOS, 콘솔/Realm, 또는 Bedrock Dedicated Server 환경에서 진행한다.

## 빠른 시작

```bash
npm install --registry=https://registry.npmjs.org/
npm run build
npm run mcaddon
```

패키징 결과:

- `dist/starter_bp.mcpack`
- `dist/starter_rp.mcpack`
- `dist/starter-bedrock-addon.mcaddon`
- `releases/starter-bedrock-addon-0.1.1.mcaddon`

Minecraft가 설치된 장치에서는 `dist/starter-bedrock-addon.mcaddon`을 열어 가져온 뒤, 월드 설정에서 Behavior Pack을 활성화한다. Behavior Pack이 Resource Pack을 의존성으로 연결하므로 함께 활성화된다.

GitHub에서 바로 내려받아 iPhone으로 보낼 때는 `releases/starter-bedrock-addon-0.1.1.mcaddon` 파일을 사용한다.

## K Follow Robot

이미지 레퍼런스를 기반으로 만든 커스텀 엔티티가 포함되어 있다.

```mcfunction
/summon starter:k_follow_robot
```

월드에 소환하면 가까운 플레이어를 따라온다. 형태는 `resource_packs/starter_rp/models/entity/k_follow_robot.geo.json`, 색상은 `scripts/make_robot_texture.mjs`, 추적 동작은 `src/main.ts`에서 수정한다.

## 로컬 웹 게임과 녹화

Minecraft 클라이언트 없이 `starter:k_follow_robot` 모델을 로컬 브라우저 게임에서 플레이할 수 있다.

```bash
npm run game:map
npm run game:serve
```

자동 플레이 데모를 녹화하려면 다음 명령을 실행한다.

```bash
npm run game:record
```

녹화 파일과 검증 스크린샷은 `web-game/recordings/` 아래에 생성된다. 자세한 사용법은 `web-game/README.md`를 참고한다.

Bedrock으로 import 가능한 skin pack을 만들 때는 `docs/bedrock-skinpack-research.md`의 공식 문서 링크와 구현 체크리스트를 먼저 확인한다.

웹 게임의 기본 촬영 맵은 인터넷에서 받은 Bedrock 사막 월드 `Desert World [Infinite Biome]`의 메타데이터를 기반으로 생성한 임시 128x128 fallback 지역이다. 최종 구조는 원본 `.mcworld` 전체를 월드 소스로 유지하고, Godot 런타임에서 현재 촬영/카메라 주변 chunk window만 렌더링한다. 원본 `.mcworld`는 라이선스 때문에 `external_maps/`에 로컬 캐시로만 보관하고 git에는 포함하지 않는다. 출처와 재생성 절차는 `docs/minecraft-desert-map-source.md`, streaming 구조는 `docs/minecraft-world-streaming.md`를 참고한다.

숏폼 영상용 카메라 연출은 `docs/cinematic-camera-language.md`에 정리했다. `http://127.0.0.1:4173/web-game/?demo=1&camera=showcase`로 열면 대화/전투/독백/야곱 씨름 전용 컷을 포함한 47개 카메라 숏을 순서대로 확인할 수 있다.

캐릭터 행동 양식은 `docs/character-action-language.md`에 정리했다. 기본 이동/점프/공격 계열과 손흔들기, 대화, 듣기, 기도, 환호, 주고받기, 방어 같은 영화용 동작을 `web-game/src/character-actions.ts`에서 관리한다. Story beat에는 대사창 표시를 위한 `speakerName`과 `line`도 포함할 수 있다.

캐릭터와 카메라 이동 DSL은 `docs/film-scene-dsl.md`와 `web-game/src/scene-dsl.ts`에 정리했다. 야곱의 얍복강 씨름 장면은 다음 URL에서 실행한다.

```text
http://127.0.0.1:4173/web-game/?demo=1&episode=jacob-wrestling
```

## Godot 런타임 포팅

본격 게임 런타임은 `godot-game/` 아래 Godot 4 프로젝트로 포팅을 시작했다. 현재 첫 포팅 단위는 야곱의 얍복강 씨름 장면이다. TypeScript의 사막 맵/장면 DSL을 Godot용 JSON으로 내보낸 뒤 Godot가 그 데이터를 읽어 3D 씬을 구성한다.

```bash
npm run mcworld:cache -- --input external_maps/the-earth/the-earth-52338.mcworld
npm run mcworld:decode
npm run godot:data
npm run godot:check
npm run godot:capture
```

- `godot:data`: `web-game/src/scene-dsl.ts`, `web-game/src/desert-world-region.ts`, generated Minecraft skin PNG를 `godot-game/`으로 동기화한다.
- `mcworld:cache`: 원본 `.mcworld`의 `db/` LevelDB 파일 전체를 `godot-game/cache/worlds/the-earth/` 아래 ignored local cache로 푼다.
- `mcworld:decode`: cached Bedrock LevelDB `.ldb` SSTable을 읽고 현재 128x128 chunk render window를 `godot-game/data/minecraft_render_window.json`으로 생성한다. LevelDB block compression은 none/snappy/zlib/raw-deflate를 처리하고, Bedrock v9 subchunk palette NBT에서 실제 표면 블록을 복원한다.
- `godot:check`: Godot headless 실행으로 야곱/상대 배우, 현재 render window/fallback 사막 맵, 대사, 카메라, 피격 피드백, 서로 마주보는 facing alignment를 검증한다.
- `godot:capture`: 실제 Godot Forward+ 렌더링으로 현재 검증 프레임 PNG를 `godot-game/recordings/`에 저장한다.

`godot:data`, `godot:check`, `godot:capture`는 LevelDB cache가 있으면 `mcworld:decode --optional`을 자동 실행해서 fallback rows보다 실제 decoded Minecraft surface를 우선 사용한다. 캐시가 없으면 generated decoded JSON을 제거하고 기존 fallback 경로로 돌아간다.

에디터로 열 때:

```bash
npm run godot:data
godot --path godot-game
```

## 개발 흐름

```bash
npm run watch
```

소스는 `src/main.ts`에서 수정한다. 빌드 결과는 `behavior_packs/starter_bp/scripts/main.js`로 생성된다.

직접 개발 팩 폴더로 복사하려면 대상 장치의 `com.mojang` 경로를 지정한다.

```bash
export MINECRAFT_COM_MOJANG_PATH="/path/to/com.mojang"
npm run deploy
```

Windows 기본 경로 예:

```powershell
$env:MINECRAFT_COM_MOJANG_PATH="$env:LOCALAPPDATA\Packages\Microsoft.MinecraftUWP_8wekyb3d8bbwe\LocalState\games\com.mojang"
npm run deploy
```

## 구조

```text
behavior_packs/starter_bp/
  manifest.json
  scripts/main.js        # generated
resource_packs/starter_rp/
  manifest.json
  entity/k_follow_robot.entity.json
  models/entity/k_follow_robot.geo.json
  render_controllers/k_follow_robot.render_controllers.json
  textures/entity/k_follow_robot.png
  texts/en_US.lang
src/
  main.ts
scripts/
  deploy.mjs
  import-mcworld-region.mjs
  check-camera-shots.mjs
  check-jacob-scene.mjs
  make-game-skins.mjs
  package.mjs
  record-game.mjs
  serve-game.mjs
  validate.mjs
web-game/
  assets/skins/
  index.html
  src/main.ts
```

## 버전 조정

현재 설정은 `@minecraft/server` `2.8.0`을 사용한다. 테스트하는 Bedrock 클라이언트가 더 오래된 버전이면 다음 두 곳을 같은 값으로 낮춘다.

- `package.json`의 `devDependencies["@minecraft/server"]`
- `behavior_packs/starter_bp/manifest.json`의 `dependencies` 내 `@minecraft/server` 버전

## 참고 문서

- Manifest reference: https://learn.microsoft.com/en-us/minecraft/creator/reference/content/addonsreference/packmanifest?view=minecraft-bedrock-stable
- TypeScript scripting guide: https://learn.microsoft.com/en-us/minecraft/creator/documents/scripting/next-steps?view=minecraft-bedrock-stable
- Script module versioning: https://learn.microsoft.com/en-us/minecraft/creator/documents/scripting/versioning?view=minecraft-bedrock-stable
- `@minecraft/server` API reference: https://learn.microsoft.com/en-us/minecraft/creator/scriptapi/minecraft/server/minecraft-server?view=minecraft-bedrock-stable
