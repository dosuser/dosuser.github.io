# dosuser.github.io

`jekyll-theme-chirpy` 테마를 적용한 개인 블로그 저장소입니다.

## 로컬 실행

```bash
bundle install
bundle exec jekyll s
```

브라우저에서 `http://127.0.0.1:4000` 접속.

## 주요 설정

- 테마: `jekyll-theme-chirpy`
- 설정 파일: `_config.yml`
- 탭 페이지: `tabs/`

## 검색·통계 설정

- 공개 정규 주소는 `https://blog.dosuser.com`입니다. 배포 후 sitemap과 canonical URL도 이 주소로 생성됩니다.
- Google Analytics 4를 만들면 `_config.yml`의 `analytics.google.id`에 측정 ID(`G-...`)를 넣습니다. ID를 비워 두면 추적 스크립트는 출력되지 않습니다.
- Google Search Console에는 `https://blog.dosuser.com/sitemap.xml`을 제출합니다.
