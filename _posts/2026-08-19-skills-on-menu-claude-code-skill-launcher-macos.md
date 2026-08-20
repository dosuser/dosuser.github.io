---
layout: post
title: "SkillsOnMenu 사용법: Claude Code 스킬을 macOS 메뉴바에서 실행하기"
date: 2026-08-19 20:30:00 +0900
author: dosuser
description: "SkillsOnMenu은 Claude Code 스킬과 MCP 워크플로를 macOS 메뉴바에서 실행하는 런처다. 설치, 스킬 등록, 입력값, 개별 단축키, MCP 실행 방법을 안내한다."
tags: [Claude Code, MCP, macOS, AI Agent, Developer Tools, SkillsOnMenu]
---

Claude Code 스킬을 터미널 명령으로 반복 실행하고 있다면, **SkillsOnMenu**을 실행 창으로 쓸 수 있다. 이미 설치한 Claude Code 스킬과 MCP 서버를 찾아 카드로 만들고, 필요한 값만 입력한 뒤 메뉴바에서 실행하는 macOS 앱이다.

> **SkillsOnMenu: a Claude Code skill launcher for macOS and an MCP menu-bar launcher.**

이 글은 앱을 처음 설치한 사람이 실제 작업을 등록하고 실행할 수 있도록 설명한다. 스킬 설치·동기화 도구가 아니라, 자주 쓰는 작업을 빠르게 실행하는 런처라는 점부터 기억하면 된다.

![SkillsOnMenu에서 저장된 스킬과 MCP 작업을 실행하는 화면](https://raw.githubusercontent.com/dosuser/skills-on-menu/main/docs/images/en/01-home.png)

## 1. 설치하기

macOS 14 이상과 Claude Code CLI가 필요하다. Claude Code CLI가 없다면 먼저 설치한다.

```sh
npm i -g @anthropic-ai/claude-code
```

Homebrew로 SkillsOnMenu을 설치한다.

```sh
brew tap dosuser/skills-on-menu
brew install --cask skills-on-menu
```

앱을 처음 실행하면 macOS가 실행을 막을 수 있다. Finder에서 앱을 Control-클릭한 뒤 **Open**을 선택한다. 소스에서 직접 실행하려면 아래처럼 빌드한다.

```sh
git clone https://github.com/dosuser/skills-on-menu
cd skills-on-menu
./build.sh
open build/SkillsOnMenu.app
```

설치가 끝나면 메뉴바의 SkillsOnMenu 아이콘을 클릭한다.

## 2. 발견된 스킬을 실행 카드로 저장하기

SkillsOnMenu은 `~/.claude/skills`, `~/.agents/skills`, 플러그인 캐시, `~/.claude/commands`에서 스킬을 찾고 Claude 설정의 MCP 서버도 읽는다. 카드가 없다면 아래 순서로 등록한다.

1. 메뉴바 팝오버에서 **Add skill**을 선택한다.
2. 발견된 스킬을 선택하거나, 직접 실행할 슬래시 명령을 입력한다.
3. 카드 이름과 한 줄 설명을 정한다.
4. 저장한 뒤 카드 오른쪽의 실행 버튼을 누른다.

예를 들어 `/llmTrend` 명령을 자주 쓴다면 카드의 실행 프롬프트에 다음처럼 등록할 수 있다.

```text
/llmTrend {{request}}
```

`{{request}}`는 사람이 입력해야 하는 값이다. 반면 날짜나 로컬 폴더는 실행 시점에 계산되는 토큰으로 지정하면 매번 고칠 필요가 없다.

- `{{today}}`, `{{yesterday}}`, `{{tomorrow}}` — 오늘·어제·내일 날짜
- `{{weekAgo}}`, `{{monthAgo}}` — 7일 전·30일 전 범위
- `{{home}}`, `{{downloads}}`, `{{documents}}` — 현재 Mac의 로컬 경로

## 3. 입력이 필요한 스킬 실행하기

스킬 문서의 파라미터, `$ARGUMENTS`, 사용 예시를 바탕으로 입력 칸을 만든다. 값이 모두 정해진 카드는 바로 실행되고, 검색어·티켓 번호처럼 사람이 알아야 하는 값이 남은 카드는 입력 폼을 연다.

![실행 전에 필요한 값만 확인하고 Run을 누르는 화면](https://raw.githubusercontent.com/dosuser/skills-on-menu/main/docs/images/en/02-run-form.png)

예를 들어 작업일지 요약 카드에서 시작일을 `{{weekAgo}}`로 두면, 실행할 때마다 그 날의 7일 전 날짜로 바뀐다. 화면에 표시되는 실제 값과 명령 미리보기를 확인한 뒤 **Run**을 누르면 된다.

## 4. 스킬마다 다른 전역 단축키 지정하기

자주 쓰는 카드는 **Skill Manager**에서 각각 다른 단축키를 지정한다. 단축키는 앱이 열려 있지 않아도 macOS 전역에서 동작한다.

1. 팝오버 오른쪽 위의 설정 아이콘으로 **Skill Manager**를 연다.
2. 왼쪽에서 카드를 선택한다.
3. **Keyboard shortcut**에 단축키를 입력하고 저장한다.

`⌥⌘T`(Trend Briefing), `⌥⌘D`(Document Search), `⌥⌘W`(Worklog Summary)처럼 카드마다 하나씩 둘 수 있다. 일반 입력을 가로채지 않도록 Command, Option, Control 중 하나 이상의 보조 키를 포함해 설정한다.

## 5. MCP 작업도 같은 방식으로 실행하기

Claude 설정에 등록된 MCP 서버도 카드로 표시된다. 서버 이름, 전송 방식, 호스트만 읽으며 `headers`와 `env` 값은 카드에 복사하지 않는다. 따라서 API 키를 다시 입력하거나 화면에 노출하지 않고, 이미 구성한 MCP 워크플로를 메뉴바에서 실행할 수 있다.

MCP 카드가 보이지 않으면 Claude Code의 MCP 설정이 먼저 정상인지 확인한 뒤 SkillsOnMenu을 다시 연다.

## 6. 실행 결과 확인과 설정 위치

실행 중에는 Claude의 텍스트와 도구 호출 진행 상태가 표시된다. 완료 결과는 팝오버, macOS 알림, `~/SkillsOnMenu/*.md`에서 다시 확인할 수 있다. 카드·입력값·단축키 설정은 아래 파일에 저장되므로 앱을 다시 설치해도 유지된다.

```text
~/.config/skillsonmenu/config.json
```

이전 SkillDock 사용자는 처음 실행할 때 기존 `~/.config/skilldock/config.json`의 저장 카드를 자동으로 가져온다.

## 어떤 작업에 적합한가

- 매일 같은 형식으로 AI·LLM 동향을 정리하는 스킬
- 날짜 범위만 바꿔 반복하는 작업일지·주간 보고 초안
- 검색어만 입력하면 되는 문서·이슈 조회
- 이미 Claude Code에 연결한 MCP 점검·조회 작업

반대로 스킬을 설치·배포·동기화하는 일은 별도의 스킬 관리 도구에 더 적합하다. SkillsOnMenu은 **이미 준비된 작업을 빠르게 실행**하는 데 집중한다.

소스, 릴리스, 문제 제보는 [SkillsOnMenu GitHub 저장소](https://github.com/dosuser/skills-on-menu)에서 확인할 수 있다.
