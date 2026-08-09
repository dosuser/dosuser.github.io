---
layout: post
title: "마인크래프트로 성경 숏폼 영상을 만들기 위한 작은 실험"
date: 2026-08-09
author: dosuser
description: Bedrock Add-On, Minecraft 호환 스킨, 웹 기반 시네마틱 프리뷰, 야곱의 얍복강 씨름 장면 DSL을 한 번에 묶어 본 제작 기록.
categories: [AI, Game]
tags: [Minecraft, Bedrock, 성경, 게임개발, 시네마틱, AI제작, Three.js, Godot]
image:
  path: /images/minecraft-bible-cinematic-prototype/03-israel-name-reveal.png
  alt: Minecraft풍 야곱의 얍복강 씨름 장면에서 이름을 받는 순간
---

Bedrock Add-On, Minecraft 호환 스킨, 웹 기반 시네마틱 프리뷰, 야곱의 얍복강 씨름 장면 DSL을 한 번에 묶어 본 제작 기록.


<p class="lead">성경의 주요 장면을 사람들이 재미있게 볼 수 있는 짧은 Minecraft풍 영상으로 만들 수 있을까. 이번 작업은 그 질문에서 출발했다.</p>

<blockquote>
  목표는 단순한 게임 데모가 아니라, Minecraft 월드와 캐릭터를 촬영 세트처럼 쓰면서 성경 에피소드를 숏폼 애니메이션으로 풀어내는 제작 파이프라인을 잡는 것이다.
</blockquote>

<section>
  <h2>GitHub 프로젝트와 재현 자료</h2>
  <p>소스 코드, 정제된 대화 흐름, 프롬프트 히스토리, 작업일지, 검증 기록은 별도 GitHub 프로젝트에 공개했다. 원문 로그 전체를 그대로 올리지는 않고, 로컬 경로와 인증 정보가 섞이지 않도록 재현 가능한 공개 기록으로 정리했다.</p>
  <ul class="asset-list">
    <li><a href="https://github.com/dosuser/minecraft-bible-cinematic-prototype">GitHub 프로젝트</a><br>웹 게임, Bedrock Add-On, Godot 실험, 문서와 재현 자료</li>
    <li><a href="https://github.com/dosuser/minecraft-bible-cinematic-prototype/blob/main/reproducibility/prompt-history.md">프롬프트 히스토리</a><br>요청 흐름과 재사용 가능한 프롬프트 템플릿</li>
    <li><a href="https://github.com/dosuser/minecraft-bible-cinematic-prototype/blob/main/reproducibility/worklog.md">작업일지</a><br>단계별 구현 내용과 남은 일</li>
    <li><a href="https://github.com/dosuser/minecraft-bible-cinematic-prototype/blob/main/reproducibility/reproduction-guide.md">재현 가이드</a><br>설치, 빌드, 실행, 검증 명령</li>
    <li><a href="https://github.com/dosuser/minecraft-bible-cinematic-prototype/blob/main/reproducibility/verification.md">검증 기록</a><br>빌드와 브라우저 검증의 범위</li>
  </ul>
</section>

<section>
  <h2>블로그에서 바로 실행</h2>
  <p>GitHub Pages 정적 파일만으로도 Three.js 웹 게임은 그대로 실행된다. 아래 플레이어를 클릭한 뒤 이동, 점프, 공격, 시네마 카메라 쇼케이스를 바로 확인할 수 있다.</p>
  <div class="game-embed" aria-label="Minecraft cinematic playable prototype">
    <iframe
      src="/game/minecraft-cinematic/?demo=1&amp;episode=jacob-wrestling"
      title="Minecraft Bible Cinematic Prototype"
      loading="lazy"
      allow="fullscreen; gamepad"
    ></iframe>
  </div>
  <p class="game-embed-actions">
    <a href="/game/minecraft-cinematic/?demo=1&amp;episode=jacob-wrestling" target="_blank" rel="noopener noreferrer">새 창에서 크게 실행</a>
  </p>
</section>

<section>
  <h2>이번에 만든 것</h2>
  <div class="meta-grid">
    <div class="meta-box"><strong>Bedrock Add-On</strong><span><code>starter:k_follow_robot</code> 엔티티와 패키징 가능한 <code>.mcaddon</code></span></div>
    <div class="meta-box"><strong>Minecraft 스킨</strong><span>64x64 skin layout 기반 야곱, 신적 사자, 군중 캐릭터</span></div>
    <div class="meta-box"><strong>웹 시네마틱 프리뷰</strong><span>Three.js 기반 사막 세트, 점프, 칼 공격, 피격 피드백, 대사창</span></div>
    <div class="meta-box"><strong>장면 DSL</strong><span>캐릭터 이동, 액션, 대사, 카메라 숏을 타임라인으로 정의</span></div>
  </div>
</section>

<section>
  <h2>작업 썸네일</h2>
  <div class="gallery">
<figure>
  <a href="/images/minecraft-bible-cinematic-prototype/01-jacob-wrestling-wide.png"><img src="/images/minecraft-bible-cinematic-prototype/thumbs/01-jacob-wrestling-wide.png" alt="야곱의 얍복강 씨름"></a>
  <figcaption><strong>야곱의 얍복강 씨름</strong><br>창세기 32장 28절의 얍복강 씨름 장면을 Minecraft풍 캐릭터와 시네마 카메라로 재구성했다.</figcaption>
</figure>
<figure>
  <a href="/images/minecraft-bible-cinematic-prototype/02-hit-feedback.png"><img src="/images/minecraft-bible-cinematic-prototype/thumbs/02-hit-feedback.png" alt="공격과 피격 피드백"></a>
  <figcaption><strong>공격과 피격 피드백</strong><br>캐릭터가 서로 마주보고 겨루며, 공격 이벤트가 피격 반응과 카메라 흔들림으로 이어지는지 확인한 컷.</figcaption>
</figure>
<figure>
  <a href="/images/minecraft-bible-cinematic-prototype/03-israel-name-reveal.png"><img src="/images/minecraft-bible-cinematic-prototype/thumbs/03-israel-name-reveal.png" alt="이스라엘 이름 선언"></a>
  <figcaption><strong>이스라엘 이름 선언</strong><br>하나님과 겨루어 이겼다는 의미의 이름을 받는 순간을 대사와 카메라 전환으로 강조했다.</figcaption>
</figure>
<figure>
  <a href="/images/minecraft-bible-cinematic-prototype/04-dialogue-coverage.png"><img src="/images/minecraft-bible-cinematic-prototype/thumbs/04-dialogue-coverage.png" alt="대화 장면 카메라"></a>
  <figcaption><strong>대화 장면 카메라</strong><br>두 인물이 대화할 때 쓸 수 있는 숏 리버스/오버숄더 계열 카메라 처리의 프리뷰.</figcaption>
</figure>
<figure>
  <a href="/images/minecraft-bible-cinematic-prototype/05-camera-showcase.png"><img src="/images/minecraft-bible-cinematic-prototype/thumbs/05-camera-showcase.png" alt="시네마 카메라 쇼케이스"></a>
  <figcaption><strong>시네마 카메라 쇼케이스</strong><br>캐릭터 중심 추적이 아니라 제3의 관객 시점에서 장면을 찍는 카메라 DSL의 기준 컷.</figcaption>
</figure>
<figure>
  <a href="/images/minecraft-bible-cinematic-prototype/06-sunrise-limp-wide.png"><img src="/images/minecraft-bible-cinematic-prototype/thumbs/06-sunrise-limp-wide.png" alt="새벽의 절뚝임"></a>
  <figcaption><strong>새벽의 절뚝임</strong><br>씨름 이후 해가 떠오르고 야곱이 절뚝이며 걸어가는 엔딩 컷.</figcaption>
</figure>
  </div>
</section>

<figure class="wide-figure">
  <img src="/images/minecraft-bible-cinematic-prototype/01-jacob-wrestling-wide.png" alt="야곱의 얍복강 씨름">
  <figcaption><strong>야곱의 얍복강 씨름</strong> - 창세기 32장 28절의 얍복강 씨름 장면을 Minecraft풍 캐릭터와 시네마 카메라로 재구성했다.</figcaption>
</figure>
<figure class="wide-figure">
  <img src="/images/minecraft-bible-cinematic-prototype/02-hit-feedback.png" alt="공격과 피격 피드백">
  <figcaption><strong>공격과 피격 피드백</strong> - 캐릭터가 서로 마주보고 겨루며, 공격 이벤트가 피격 반응과 카메라 흔들림으로 이어지는지 확인한 컷.</figcaption>
</figure>
<figure class="wide-figure">
  <img src="/images/minecraft-bible-cinematic-prototype/03-israel-name-reveal.png" alt="이스라엘 이름 선언">
  <figcaption><strong>이스라엘 이름 선언</strong> - 하나님과 겨루어 이겼다는 의미의 이름을 받는 순간을 대사와 카메라 전환으로 강조했다.</figcaption>
</figure>
<figure class="wide-figure">
  <img src="/images/minecraft-bible-cinematic-prototype/04-dialogue-coverage.png" alt="대화 장면 카메라">
  <figcaption><strong>대화 장면 카메라</strong> - 두 인물이 대화할 때 쓸 수 있는 숏 리버스/오버숄더 계열 카메라 처리의 프리뷰.</figcaption>
</figure>
<figure class="wide-figure">
  <img src="/images/minecraft-bible-cinematic-prototype/05-camera-showcase.png" alt="시네마 카메라 쇼케이스">
  <figcaption><strong>시네마 카메라 쇼케이스</strong> - 캐릭터 중심 추적이 아니라 제3의 관객 시점에서 장면을 찍는 카메라 DSL의 기준 컷.</figcaption>
</figure>
<figure class="wide-figure">
  <img src="/images/minecraft-bible-cinematic-prototype/06-sunrise-limp-wide.png" alt="새벽의 절뚝임">
  <figcaption><strong>새벽의 절뚝임</strong> - 씨름 이후 해가 떠오르고 야곱이 절뚝이며 걸어가는 엔딩 컷.</figcaption>
</figure>

<section>
  <h2>핵심 장면: 야곱의 얍복강 씨름</h2>
  <p>이번 프로토타입의 기준 장면은 창세기 32장 28절의 야곱 이야기다. 장면 DSL에는 두 배우의 위치, 마주보기, 씨름 액션, 피격 피드백, 이름 선언 대사, 새벽 엔딩 컷이 들어간다.</p>
  <p>중요했던 발견은 실제 게임 엔진보다 먼저 "Minecraft처럼 보이는 기준선"을 잡아야 한다는 점이었다. 웹 프리뷰는 단순하지만 픽셀 스킨, 블록 하늘, 사막 소품, 카메라 컷이 맞물려 훨씬 Minecraft에 가까운 인상을 줬다.</p>
</section>

<section>
  <h2>왜 Godot 포팅은 덜 Minecraft 같았나</h2>
  <p>Godot 실험은 실제 <code>.mcworld</code> 데이터를 읽는 기술 검증에 가까웠다. 표면 컬럼만 단색 박스로 찍는 단계였기 때문에, Minecraft의 텍스처, 바이옴 색, 블록별 모델, 하늘과 그림자 감성이 빠졌다.</p>
  <p>반대로 웹판은 원본 맵을 완벽히 읽은 것은 아니지만, 보는 사람이 "이건 Minecraft 영상이다"라고 느낄 만한 시각 단서를 먼저 잡았다. 그래서 다음 단계는 Godot을 억지로 밀기보다, 실제 Minecraft Bedrock 클라이언트를 촬영 엔진으로 쓰는 방향이 더 자연스럽다.</p>
</section>

<section>
  <h2>다음 방향</h2>
  <ul>
    <li>웹 게임은 빠른 스토리보드와 카메라 DSL 프리뷰로 유지한다.</li>
    <li>Bedrock Add-On은 실제 월드, 스킨팩, 대사, 카메라 명령을 실행하는 본 무대로 둔다.</li>
    <li>최종 영상은 Minecraft 클라이언트에서 촬영하고, 필요하면 Replay/Camera 계열 도구를 붙인다.</li>
    <li>Godot은 당장은 보조 프리뷰나 기술 실험으로 낮춘다.</li>
  </ul>
</section>

<section>
  <h2>첨부 작업물</h2>
  <ul class="asset-list">
    <li><a href="/attachments/minecraft-bible-cinematic-prototype/downloads/starter-bedrock-addon.mcaddon">starter-bedrock-addon.mcaddon</a><br>Bedrock에서 import 가능한 Add-On 패키지</li>
    <li><a href="/attachments/minecraft-bible-cinematic-prototype/downloads/starter_bp.mcpack">starter_bp.mcpack</a><br>Behavior Pack</li>
    <li><a href="/attachments/minecraft-bible-cinematic-prototype/downloads/starter_rp.mcpack">starter_rp.mcpack</a><br>Resource Pack</li>
    <li><a href="/attachments/minecraft-bible-cinematic-prototype/reference/README.md">프로젝트 README 복사본</a><br>빌드와 실행 흐름</li>
    <li><a href="/attachments/minecraft-bible-cinematic-prototype/reference/docs__film-scene-dsl.md">Film Scene DSL</a><br>캐릭터와 카메라 타임라인 정의</li>
    <li><a href="/attachments/minecraft-bible-cinematic-prototype/reference/docs__cinematic-camera-language.md">Camera Language</a><br>대화, 전투, 독백, 씨름 장면 카메라 숏</li>
  </ul>
</section>

<section>
  <h2>로컬 실행</h2>
  <p>블로그 배포판은 <code>/game/minecraft-cinematic/</code> 아래의 정적 파일로 실행한다. 개발 중에는 아래 로컬 URL로 같은 장면을 확인했다.</p>
  <p><code>http://127.0.0.1:4174/web-game/?demo=1&amp;episode=jacob-wrestling</code></p>
</section>

<div class="tags">
<span>#Minecraft</span>
<span>#Bedrock</span>
<span>#성경</span>
<span>#게임개발</span>
<span>#시네마틱</span>
<span>#AI제작</span>
<span>#Three.js</span>
<span>#Godot</span>
</div>
