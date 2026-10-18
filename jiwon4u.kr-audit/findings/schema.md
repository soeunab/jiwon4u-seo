# Schema / Structured Data — 60/100

## 잘 되어 있는 것
- WebSite + SearchAction
- BreadcrumbList 마이크로데이터
- 18개 글 FAQPage

## 발견 사항

| 심각도 | 항목 | 근거 | 조치 |
|---|---|---|---|
| High | Article 스키마 2개 충돌 | 테마 NewsArticle(Person)과 BlogPosting(Organization)의 작성자·날짜·이미지가 다름 | BlogPosting 하나로 통일 |
| Medium | 블로그에 NewsArticle 사용 | 뉴스 매체가 아님 | BlogPosting 사용 |
| Medium | publisher logo / Organization 엔터티 없음 | logo, sameAs 없음 | Organization(@id, logo, sameAs) 추가 |
| Medium | 9개 글 BlogPosting·FAQPage 누락 | p18~p26 | 같은 템플릿 적용 |
| Low | 홈 NewsArticle에 본문 전체가 description으로 | 고지문 포함 수천 자 | 홈에서는 Article 블록 제거 |
