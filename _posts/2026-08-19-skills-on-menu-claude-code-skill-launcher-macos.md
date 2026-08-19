---
layout: post
title: "SkillsOnMenu: macOS 메뉴바에서 Claude Code 스킬과 MCP를 바로 실행하는 방법"
date: 2026-08-19 20:30:00 +0900
author: dosuser
description: "SkillsOnMenu is a Claude Code skill launcher for macOS and an MCP menu-bar launcher. 반복하는 Claude Code 스킬과 MCP 워크플로를 메뉴바에서 찾아 입력값을 채워 실행한다."
tags: [Claude Code, MCP, macOS, AI Agent, Developer Tools, SkillsOnMenu]
---

Claude Code 스킬을 쓰기 시작하면, 같은 작업을 다시 실행하는 시간이 생각보다 길어진다. 터미널을 열고, 스킬 이름을 기억하고, 지난번과 비슷한 인자를 다시 만들고, 실행이 끝날 때까지 다른 창 사이를 오간다.

**SkillsOnMenu**은 이 반복을 줄이기 위한 macOS 메뉴바 앱이다.

> **SkillsOnMenu is a Claude Code skill launcher for macOS and an MCP menu-bar launcher.**

범용 스킬 관리자가 아니라, 이미 설치해 둔 스킬과 MCP 서버를 **빠르게 실행하는 화면**에 집중한다. 메뉴바를 열고 작업을 고른 뒤, 필요한 값만 채우거나 이미 채워진 기본값으로 바로 실행한다.

## 무엇이 다른가

스킬 관리 도구는 설치, 동기화, 편집, 여러 에이전트에 배포하는 일을 잘한다. 그러나 매일 같은 스킬을 실행하는 사람에게는 한 단계가 더 필요하다. 자주 쓰는 작업은 어디에 있는지 찾는 일이 아니라, **지금 실행하는 일**이기 때문이다.

SkillsOnMenu의 흐름은 다음처럼 짧다.

```text
메뉴바 열기
  → 저장한 스킬 또는 MCP 작업 선택
  → 기본값 확인 또는 필요한 입력 한두 개 작성
  → 실행 상태와 결과 확인
```

다음 같은 작업에 특히 잘 맞는다.

- 매일 LLM·AI 동향을 같은 형식으로 수집하기
- 반복되는 작업일지·주간 보고 초안 만들기
- 사내 문서나 이슈를 정해 둔 질문으로 조회하기
- MCP 도구를 사용하는 정기 점검을 실행하기

## 입력값을 다시 만들지 않기

스킬은 `SKILL.md`의 파라미터 표, `$ARGUMENTS`, 사용 예시를 읽어 입력 항목을 추론한다. 날짜·폴더처럼 실행 때마다 달라지는 값은 토큰으로 보관한다.

| 토큰 | 실행 순간의 값 |
|---|---|
| `{{today}}`, `{{yesterday}}`, `{{tomorrow}}` | 현재 날짜 |
| `{{weekAgo}}`, `{{monthAgo}}` | 7일 또는 30일 전 |
| `{{home}}`, `{{downloads}}`, `{{documents}}` | 현재 Mac의 로컬 경로 |

사람만 알 수 있는 검색어·티켓 번호가 남아 있으면 작은 입력 폼을 연다. 반대로 모든 값이 해결된 카드는 한 번 클릭해 바로 실행할 수 있다.

## MCP도 메뉴바 작업으로

설정된 MCP 서버는 별도 카드로 표시한다. SkillsOnMenu은 서버 이름, 전송 방식, 호스트만 읽는다. `headers`나 `env` 값은 카드에 복사하지 않으므로, API 키가 UI에 노출되는 경로를 만들지 않는다.

이 원칙은 편의성보다 먼저 지켜야 한다. 메뉴바에서 실행하기 쉬워질수록, 보이지 않아야 할 값은 더 확실히 분리해야 한다.

## 시작하기

현재 소스와 설치 안내는 [GitHub 저장소](https://github.com/dosuser/skills-on-menu)에서 확인할 수 있다. macOS 14 이상과 Claude Code CLI가 필요하다.

```sh
git clone https://github.com/dosuser/skills-on-menu
cd skills-on-menu
./build.sh
open build/SkillsOnMenu.app
```

기존 SkillDock 사용자는 첫 실행 때 `~/.config/skilldock/config.json`의 저장 카드를 새 설정 경로로 가져온다. 이미 만들어 둔 카드와 단축키를 다시 등록할 필요가 없다.

## 다음 단계

이 앱의 성패는 기능 수가 아니라, 사용자가 매일 한 번이라도 터미널 대신 메뉴바를 여는지에 달려 있다. 그래서 다음에는 설치 수보다 첫 실행과 재사용을 확인하고, 실제로 자주 쓰이는 작업을 더 쉽게 등록하는 데 집중하려 한다.

사용 중인 Claude Code 스킬이나 MCP 워크플로 중 메뉴바에서 바로 실행하고 싶은 것이 있다면, 저장소의 이슈로 알려주면 좋겠다.
