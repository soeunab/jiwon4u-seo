# Performance (CWV) — 58/100

## 잘 되어 있는 것
- CLS 0
- 데스크톱 Perf 90~96
- SEO/Best Practices 100

## 발견 사항

| 심각도 | 항목 | 근거 | 조치 |
|---|---|---|---|
| High | LCP 이미지 lazy-load | 모바일 글 LCP 5.8s, 로드 지연 47% | 첫 이미지 eager + fetchpriority=high |
| Medium | AdSense 메인 스레드 부담 | 모바일 TBT 510~550ms, 미사용 JS 150KB | 첫 화면 광고 줄이기 |
| Medium | 이미지 전송 과다 | 80~240KB 절감 가능 | 썸네일 크기 맞추기 |
| Low | 브레드크럼 색 대비 2.72:1 | #f67938 on #fff | 더 진한 색 사용 |
