# Technical SEO — 82/100

## 잘 되어 있는 것
- non-www/http → https://www 301
- 모든 글 자기 참조 canonical, ?m=1도 canonical 처리
- 라벨·검색 페이지 noindex,follow
- sitemap.xml 27개 글 전부 200, lastmod 정확
- lang=ko, viewport, 404 상태 코드 정상

## 발견 사항

| 심각도 | 항목 | 근거 | 조치 |
|---|---|---|---|
| Medium | robots.txt에 고정 페이지 사이트맵 누락 | /sitemap-pages.xml이 robots.txt에 없음 | Sitemap: https://www.jiwon4u.kr/sitemap-pages.xml 추가 |
| Low | 404 페이지 canonical에 이중 슬래시 | https://www.jiwon4u.kr//p/... 형태 | 404에서는 canonical 출력 제거 |
| Low | 일부 모바일 UA ?m=1 리디렉션 | Lighthouse 모바일 측정 시 1홉 추가 | Blogger 모바일 테마 사용 안 함 확인 |
