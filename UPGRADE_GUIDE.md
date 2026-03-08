# 업그레이드 가이드

## 변경 사항 요약

블로그가 다음과 같이 업그레이드되었습니다:

### 1. **Jekyll & GitHub Pages 최신화**
- Jekyll 3.5.0 → GitHub Pages 호환 최신 버전
- `gems` → `plugins` 문법 변경
- jekyll-paginate → jekyll-paginate-v2로 업그레이드
- jekyll-sitemap 추가

### 2. **메타 태그 및 SEO 개선**
- Viewport 설정 개선 (`viewport-fit=cover` 추가)
- Meta description 추가
- Canonical URL 지원
- 페이지별 SEO 최적화

### 3. **폰트 및 아이콘 업데이트**
- Font Awesome 4.7 → 6.4로 업그레이드 (더 많은 아이콘)
- Spoqa Han Sans → Noto Sans KR로 변경
- Google Fonts API v2 사용

### 4. **구조 개선**
- `header.html` 새로 추가 (네비게이션 포함)
- `footer.html` 완전 리뉴얼 (소셜 링크, 섹션 추가)
- `post.html` 현대화 (메타데이터, Schema.org 마크업)
- `page.html` 개선 (유연한 헤더)

### 5. **Google Analytics 업그레이드**
- Google Analytics 4 (GA4) 스크립트 업데이트
- UA → G 추적 ID 지원

### 6. **자동화 및 개발 도구**
- GitHub Actions CI/CD 워크플로우 추가
- 자동 빌드 및 배포 설정
- `.markdownlint.json` 추가
- `.editorconfig` 추가

### 7. **구성 파일 정리**
- `_config.yml` 완전 리뉴얼
  - 현대적 YAML 구조
  - 빌드 설정 최적화
  - 플러그인 설정 명확화

---

## 다음 단계

### 1. 로컬 테스트
```bash
# 의존성 설치
bundle install

# 로컬 서버 실행
bundle exec jekyll serve

# 접속: http://localhost:4000
```

### 2. 포스트 프론트매터 업데이트 (선택사항)
기존 포스트에서 아래와 같은 필드를 추가하면 더 좋습니다:

```yaml
---
layout: post
title: "제목"
date: 2024-01-15
categories: ["카테고리"]
tags: ["태그1", "태그2"]
description: "짧은 설명"
image: "/assets/images/thumbnail.jpg"
---
```

### 3. 로고 이미지 추가 (선택사항)
- 파일명: `assets/images/logo.png`
- 권장 크기: 200x200px

### 4. favicon 추가
- `favicon.ico`: 루트 디렉토리
- `apple-touch-icon.png`: 루트 디렉토리 (180x180px)

### 5. Google Analytics 설정
GA4 추적 ID를 `_config.yml`에 추가:
```yaml
google-analytics:
  id: "G-XXXXXXXXXX"
```

### 6. GitHub Pages 설정 (필요시)
1. GitHub 저장소 Settings
2. Pages 섹션
3. Source: Deploy from a branch
4. Branch: main (또는 master)
5. Folder: / (root)

---

## 주요 기능

### 자동 기능들
- ✅ 자동 사이트맵 생성 (`jekyll-sitemap`)
- ✅ RSS 피드 생성 (`jekyll-feed`)
- ✅ SEO 최적화 (`jekyll-seo-tag`)
- ✅ 자동 배포 (GitHub Actions)

### 스타일 커스터마이징
`_sass/` 폴더 내 파일들 수정:
- `_variables.scss`: 색상, 폰트, 간격
- `_typography.scss`: 텍스트 스타일
- `_color.scss`: 색상 팔레트
- `_site.scss`: 레이아웃

---

## 문제 해결

### Ruby 버전 문제
```bash
ruby --version  # 3.0 이상 필요
bundle update   # 의존성 업데이트
```

### 빌드 오류
```bash
bundle clean --force
bundle install
bundle exec jekyll build
```

### 포트 충돌
```bash
bundle exec jekyll serve --port 5000
```

---

## 도움말

GitHub Pages 공식 문서: https://docs.github.com/en/pages
Jekyll 공식 문서: https://jekyllrb.com/
Font Awesome 아이콘: https://fontawesome.com/icons

---

**업그레이드 완료!** 🎉
