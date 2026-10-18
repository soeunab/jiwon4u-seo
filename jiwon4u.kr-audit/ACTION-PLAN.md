# jiwon4u.kr SEO 실행 계획

SEO Health Score: **73 / 100** (2026-10-18 기준). 근거는 `FULL-AUDIT-REPORT.md` 참고.

## Critical (즉시)

색인을 막거나 페널티를 부르는 문제는 발견되지 않았습니다.

## High (1주 이내)

| # | 할 일 | 위치 | 확인 방법 |
|---|---|---|---|
| 1 | 글 대표 이미지에서 `loading="lazy"` 빼고 `fetchpriority="high"` 추가 | 글 작성 템플릿 / 테마 | Lighthouse 모바일 `lcp-lazy-loaded` 통과, LCP 4초 미만 |
| 2 | 테마의 `NewsArticle` JSON-LD 삭제, `BlogPosting` 하나로 통일 (author=Person, publisher에 logo) | 테마 HTML 편집 | Rich Results Test에 Article 1개만 나옴 |
| 3 | p18~p26 9개 글에 `BlogPosting` + `FAQPage` 추가 | 각 글 HTML | 위와 동일 |
| 4 | 소개·개인정보처리방침 페이지 제목 입력, 개인정보처리방침의 `<h1>` 14개를 `h2`/`p`로 정리 | Blogger 페이지 편집기 | `<title>`이 "Latest Posts"가 아님, H1 1개 |
| 5 | 글 하단 작성자 박스 추가 (소개 1~2줄 + 소개 페이지 링크 + SNS) | 테마 | 모든 글에 노출 |

## Medium (1개월 이내)

| # | 할 일 |
|---|---|
| 6 | title 접미사를 " - 지원포유"로 줄이기 |
| 7 | 짧은 meta description(45~62자) 18개를 80~120자로 다시 쓰기 |
| 8 | 쿠팡 고지문을 대표 이미지 아래로 옮기거나 홈 카드 요약이 메타 설명을 쓰도록 수정 |
| 9 | `robots.txt`에 `Sitemap: https://www.jiwon4u.kr/sitemap-pages.xml` 추가 |
| 10 | 글 페이지 `og:type`을 `article`로, 빈 `article:author`/`twitter:*` 정리 |
| 11 | 사이트 `Organization` 스키마(logo, sameAs) 추가 |
| 12 | "클로드 코드", "블로그 자동화" 주제 글들의 대표 검색어 분리 + 상호 링크 |
| 13 | 첫 화면 AdSense 슬롯 줄여 모바일 TBT 낮추기 |
| 14 | 카드 썸네일 이미지 크기 맞추기 (전송 80~240KB 절감) |

## Low (백로그)

| # | 할 일 |
|---|---|
| 15 | 날짜 표시 형식 `2026년 10월 17일` + KST 기준으로 |
| 16 | 브레드크럼 색 대비 4.5:1 이상으로 |
| 17 | 404 페이지 canonical(`//`) 제거 |
| 18 | 개인정보처리방침·이용약관을 의미 있는 URL로 옮기고 301 |
| 19 | 홈 숨김 H1의 "Homepage" 접두어 제거 |
| 20 | 모바일 테마(`?m=1` 리디렉션) 꺼져 있는지 확인 |

## 측정 공백 (다음 감사 때 채울 것)

- Search Console 연결 후 `/seo google`로 색인 현황·검색어·CrUX 실사용자 데이터 확인
- PageSpeed Insights API 키 설정 (오늘은 무료 쿼터 소진)
