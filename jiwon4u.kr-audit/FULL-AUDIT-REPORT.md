# jiwon4u.kr 전체 SEO 감사 보고서

- 대상: https://www.jiwon4u.kr/ (지원포유 - 클로드 활용 가이드)
- 감사일: 2026-10-18
- 도구: claude-seo v2.4.2 (`seo-audit` 스킬), 로컬 Lighthouse 12.6.0, Chromium/Playwright
- 범위: 사이트맵 전 URL 27개 + 홈 + 고정 페이지 4개 + 라벨/검색/404 샘플 (총 35 URL)

> **측정 한계**
> - claude-seo의 `render_page.py`는 이 실행 환경의 루프백 프록시를 SSRF 방지 규칙으로 거부해서, 수집은 curl/Chromium으로 하고 파싱은 claude-seo의 `parse_html.py`로 했습니다.
> - PageSpeed Insights API는 일일 무료 쿼터가 소진된 상태여서 **CrUX 실사용자(field) 데이터는 없습니다.** 성능 수치는 전부 로컬 Lighthouse 실험실(lab) 값입니다.
> - Search Console / GA4 / 백링크 API 자격 증명이 없어서 색인 현황, 트래픽, 백링크는 다루지 않았습니다.

---

## 1. 요약

| 항목 | 값 |
|---|---|
| **SEO Health Score** | **71 / 100** (1차 73점 → 심층 분석 반영 후 71점) |
| 사이트 유형 | 정보형 블로그 / 퍼블리셔 (Blogger + JetTheme, AdSense·쿠팡 파트너스 수익화) |
| 주제 | 클로드(Claude) 활용법, AI 도구, 블로그 자동화 (한국어) |
| 색인 대상 글 | 27개 (2026-08 ~ 2026-10) |

### 카테고리 점수

| 카테고리 | 가중치 | 점수 |
|---|---|---|
| Technical SEO | 22% | 82 |
| Content Quality | 23% | 70 (1차 74) |
| On-Page SEO | 20% | 72 |
| Schema / Structured Data | 10% | 60 |
| Performance (CWV, lab) | 10% | 58 |
| AI Search Readiness | 10% | 66 (1차 70) |
| Images | 5% | 85 |

### 가장 중요한 문제 5가지

> **심층 분석에서 더 급한 문제 2가지가 나왔습니다 (9장).** ① 주요 검색어 8개에서 사이트 노출이 확인되지 않음 → 서치콘솔·서치어드바이저 확인부터. ② p18 요금·모델 정보가 공식 페이지와 다르고, 글마다 가격 기준이 다름.

1. **글마다 Article 스키마가 2개씩 서로 충돌함**: 테마가 넣는 `NewsArticle`(작성자 Person, 시간 포함 날짜, Blogger 이미지)과 직접 넣은 `BlogPosting`(작성자 Organization, 날짜만, GCS 이미지)이 같은 글을 서로 다르게 설명합니다. 9개 글(p18~p26)은 `BlogPosting`/`FAQPage` 블록이 아예 빠져 있습니다.
2. **모바일 LCP 4.1~5.8초 (lab)**: 글 본문 대표 이미지(LCP 요소)에 `loading="lazy"`가 붙어 있어서 로드 지연이 LCP의 47%를 차지합니다. AdSense 스크립트 때문에 TBT도 500ms 이상입니다.
3. **소개·개인정보처리방침 페이지 제목이 "Latest Posts"로 나옴**: `/p/about.html`과 `/p/blog-page_18.html`의 `<title>`이 "Latest Posts - 지원포유..."이고, 개인정보처리방침 페이지에는 붙여넣기한 서식 때문에 `<h1>`이 14개 있습니다. E-E-A-T 신호로 쓰이는 페이지라 영향이 큽니다.
4. **작성자 실체가 약함**: 작성자가 "지원포유" 브랜드명뿐이고, 글 아래 작성자 소개·경력·프로필 링크가 없습니다. `article:author`, `twitter:creator` 메타는 빈 값이고, 메타 author는 "지원포유 - 클로드 활용 가이드 Team"입니다.
5. **쿠팡 파트너스 고지문이 스니펫 앞을 차지함**: 고지문이 본문 맨 앞에 있어서 홈 카드 요약과 홈 `NewsArticle.description`의 첫 문장이 고지문입니다(링크가 있는 글 4개).

### 바로 할 수 있는 개선 5가지

1. 글 상단 대표 이미지의 `loading="lazy"`를 빼고 `fetchpriority="high"` 추가 (테마/글 템플릿 1곳 수정)
2. Blogger 고정 페이지 편집기에서 소개·개인정보처리방침 페이지 **제목 입력란** 채우기 → `<title>`과 H1이 바로 고쳐짐
3. 테마의 `NewsArticle` JSON-LD 블록 삭제, 직접 넣는 `BlogPosting` 하나로 통일 (작성자는 Person으로)
4. 제목 접미사 "` - 지원포유 - 클로드 활용 가이드`"(21자)를 "` - 지원포유`"로 줄이기
5. `robots.txt`에 `Sitemap: https://www.jiwon4u.kr/sitemap-pages.xml` 한 줄 추가

---

## 2. Technical SEO (82)

**잘 되어 있는 것**
- `jiwon4u.kr`, `http://` 모두 `https://www.jiwon4u.kr/`로 301 리디렉션 (최대 2홉)
- 모든 글에 자기 참조 canonical, `?m=1` 모바일 URL도 원본으로 canonical 처리
- 라벨(`/search/label/…`)·검색(`/search?q=`) 페이지는 `noindex, follow` → 얇은 아카이브 페이지 색인 방지
- `robots.txt`는 전체 허용 + 사이트맵 명시, 네이버(Yeti) 별도 허용, 다음 웹마스터 인증 포함
- `sitemap.xml`에 27개 글 전부, `lastmod` 정확, 크롤링한 27개 모두 200 응답
- `<html lang="ko">`, viewport 메타, `X-Content-Type-Options: nosniff`
- 404 페이지가 실제 404 상태 코드를 반환 (soft 404 아님)

**발견 사항**

| 심각도 | 내용 | 조치 |
|---|---|---|
| Medium | `robots.txt`에 고정 페이지 사이트맵(`/sitemap-pages.xml`)이 없음. 소개·개인정보처리방침 페이지 발견이 내부 링크에만 의존 | `Sitemap: https://www.jiwon4u.kr/sitemap-pages.xml` 추가 (Blogger 설정 > 크롤러 및 색인 > 맞춤 robots.txt) |
| Low | 404 페이지 canonical이 `https://www.jiwon4u.kr//p/...`처럼 슬래시가 2개 | 테마의 canonical 출력 부분에서 404일 때 canonical을 빼기 |
| Low | 일부 모바일 UA는 `?m=1`로 한 번 더 리디렉션됨 (Lighthouse 모바일 측정 시 발생). TTFB에 1홉 추가 | 반응형 테마이므로 Blogger 설정 > 테마 > 모바일 테마 사용 안 함 확인 |
| Info | HSTS 헤더 없음, `cache-control: private, max-age=0` | Blogger 맞춤 도메인 한계로 직접 제어 불가. 조치 불필요 |

## 3. Content Quality (70)

**잘 되어 있는 것**
- 글마다 본문 분량이 충분하고(파서 기준 3,300~4,700 단어 토큰), H2 11~19개로 구조가 촘촘함
- 모든 글이 "핵심 요약" 박스 + 첫 문단 결론 제시(answer-first) 구조
- 직접 찍은 claude.ai 화면 캡처, 실제 겪은 버그 기록("롱테일 키워드가 거꾸로 뽑히던 날", "9B 모델 코딩 실패기") 같은 **1차 경험(Experience)** 신호가 강함
- `support.claude.com`, `claude.com`, `code.claude.com` 등 공식 문서로 나가는 링크가 많음 (출처 근거)
- 쿠팡 링크에 `rel="sponsored"` 적용, 고지문도 있음

**발견 사항**

| 심각도 | 내용 | 조치 |
|---|---|---|
| High | 작성자 정보 부족: 글 하단 작성자 소개 박스 없음, 바이라인 클릭 시 Blogger 프로필로 이동, 소개 페이지 제목·H1 깨짐 | 글 하단에 작성자 박스(누구인지, 클로드를 어떻게 쓰는지, 소개 페이지 링크) 추가. 소개 페이지 보강 |
| Medium | 주제 겹침(키워드 잠식) 가능성: "클로드 코드 뜻"/"클로드 코드 사용법"/"Cowork 차이", "블로그 자동화" 관련 글 6개가 비슷한 검색어를 노림 | 각 글의 대표 검색어를 하나씩 정하고, 겹치는 글끼리 서로 링크해 역할 구분 (뜻=개념 허브, 사용법=절차) |
| Medium | 쿠팡 고지문이 본문 첫 줄이라, 홈 카드 요약·자동 생성 설명의 첫 문장이 고지문 | 고지문을 대표 이미지 아래 작은 박스로 옮기거나, 홈 카드 요약을 메타 설명에서 가져오도록 테마 수정 |
| Low | 날짜 표시가 "16 10월, 2026"처럼 어색하고, 오전 9시 이전(KST) 발행 글은 하루 전 날짜로 표시됨 (예: `data-date=2026-10-17T01:52+09:00` → "16 10월") | 테마 날짜 포맷을 `yyyy년 M월 d일`로, 타임존을 Asia/Seoul로 |

## 4. On-Page SEO (72)

**잘 되어 있는 것**
- 모든 글에 고유한 title·meta description·H1 1개
- 모든 글에 Open Graph·Twitter Card, 이미지 alt 텍스트 누락 0건
- 글당 내부 링크 42~69개, 관련 글 상호 링크

**발견 사항**

| 심각도 | 내용 | 조치 |
|---|---|---|
| High | `/p/about.html`, `/p/blog-page_18.html`(개인정보처리방침)의 `<title>`이 "Latest Posts - …", 테마 H1(`entry-title`)은 빈 값. 개인정보처리방침에는 붙여넣은 서식 때문에 `<h1>` 14개 | 페이지 제목 입력란 채우기. 개인정보처리방침은 HTML 보기에서 `h1 class="entry-title" style=…`을 `h2`/`p`로 바꾸고 인라인 스타일 제거 |
| Medium | title 접미사 " - 지원포유 - 클로드 활용 가이드"(21자)로 글 제목이 51~83자. 검색 결과에서 핵심 키워드 뒤가 잘림 | 접미사를 " - 지원포유"로 줄이거나, 글 페이지에서는 접미사 제거 |
| Medium | 17개 글(p01~p17, p27)의 meta description이 45~62자로 짧음. 나머지 9개는 96~130자 | 80~120자로 늘려 검색 의도·차별점 포함 (예: 대상 독자, 무료 여부, 단계 수) |
| Medium | 글 페이지 `og:type`이 `website` | 글 페이지는 `article`로, `article:published_time` 추가 |
| Low | `article:author`, `twitter:site`, `twitter:creator`가 빈 값 | 값을 넣거나 태그 삭제 |
| Low | 고정 페이지 URL `blog-page.html`(이용약관), `blog-page_18.html`(개인정보처리방침)이 의미 없는 주소 | 새 페이지로 `/p/privacy-policy.html`, `/p/terms.html` 만들고 기존 주소는 Blogger 맞춤 리디렉션으로 301 |
| Low | 홈 H1이 숨김(`d-none`)이고 "Homepage 지원포유…"처럼 영어 접두어 | "지원포유 - 클로드 활용 가이드"로 |

## 5. Schema / Structured Data (60)

현재 상태:
- 홈: `WebSite`(+SearchAction), 최근 글 5개의 `NewsArticle`
- 글 18개(p01~p17, p27): 테마 `NewsArticle` + 직접 넣은 `@graph`(`BlogPosting`, `FAQPage`)
- 글 9개(p18~p26): 테마 `NewsArticle`만 있음. 본문에 FAQ 섹션은 있지만 `FAQPage` 없음
- 마이크로데이터: `BreadcrumbList`, `WPHeader`/`WPFooter`/`WPSideBar`

| 심각도 | 내용 | 조치 |
|---|---|---|
| High | 같은 글에 `NewsArticle`과 `BlogPosting`이 따로 있고 작성자 유형(Person/Organization), 날짜, 이미지 URL이 서로 다름. 검색엔진이 어느 쪽을 믿을지 모호 | 테마 `NewsArticle` 삭제, `BlogPosting` 하나만 남기기 (아래 예시) |
| Medium | 블로그 글에 `NewsArticle` 사용. 뉴스 매체가 아니므로 `BlogPosting`이 맞음 | 위와 같이 통일 |
| Medium | `publisher`에 `logo` 없음, 사이트 전체 `Organization`/`Person` 엔터티 없음 | `Organization`(name, url, logo, sameAs: 인스타그램·스레드·페이스북) 1개를 홈에 두고 글에서 `@id`로 참조 |
| Medium | p18~p26 9개 글에 `BlogPosting`/`FAQPage` 누락 | 같은 템플릿으로 추가 |
| Low | 홈 `NewsArticle.description`에 본문 수천 자(고지문 포함)가 통째로 들어감 | 홈에서는 글 목록용 Article 블록 제거 (WebSite + Organization만) |
| Info | `FAQPage` 리치 결과는 2023년 8월부터 정부·건강 사이트에만 표시됨 | 리치 결과는 기대하지 말되, AI 답변 엔진용으로는 유지해도 무방 |

권장 `BlogPosting` 예시:

```json
{
  "@context": "https://schema.org",
  "@type": "BlogPosting",
  "@id": "https://www.jiwon4u.kr/2026/10/claude-artifacts-meaning-usage-free-plan.html#article",
  "mainEntityOfPage": "https://www.jiwon4u.kr/2026/10/claude-artifacts-meaning-usage-free-plan.html",
  "headline": "클로드 아티팩트 뜻부터 사용법, 무료 플랜에서 되는 범위까지 정리",
  "description": "클로드 아티팩트 뜻과 사용법, 무료 플랜 범위, 공유·내보내기 방법을 정리했어요.",
  "image": "https://storage.googleapis.com/jiwon4u-blog-images/posts%2F1792206990863-2a7e9488.webp",
  "datePublished": "2026-10-17T01:52:00+09:00",
  "dateModified": "2026-10-17T12:22:23+09:00",
  "inLanguage": "ko",
  "author": {
    "@type": "Person",
    "name": "지원포유",
    "url": "https://www.jiwon4u.kr/p/about.html"
  },
  "publisher": {
    "@type": "Organization",
    "@id": "https://www.jiwon4u.kr/#organization",
    "name": "지원포유",
    "logo": { "@type": "ImageObject", "url": "<로고 이미지 URL>" }
  }
}
```

## 6. Performance (58, lab 데이터)

로컬 Lighthouse 12.6.0 측정 (모바일은 기본 throttling):

| 페이지 | 기기 | Perf | LCP | TBT | CLS | FCP |
|---|---|---|---|---|---|---|
| 홈 | 모바일 | 69 | 4.1s | 550ms | 0 | 1.1s |
| 글 (아티팩트) | 모바일 | 63 | 5.8s | 510ms | 0 | 1.0s |
| 홈 | 데스크톱 | 96 | 1.3s | 0ms | 0 | 0.4s |
| 글 (아티팩트) | 데스크톱 | 90 | 1.9s | 60ms | 0 | 0.3s |

SEO·접근성·권장사항 점수: SEO 100, Best Practices 100, Accessibility 96~100.

| 심각도 | 내용 | 조치 |
|---|---|---|
| High | 글 페이지 LCP 요소(`div.jw-post > div.separator > a > img`, 대표 이미지)에 `loading="lazy"`. LCP 5.8s 중 로드 지연 2.7s(47%) | 본문 첫 이미지만 `loading="eager" fetchpriority="high"`. 가능하면 `<link rel="preload" as="image">` |
| Medium | AdSense(`show_ads_impl`, `adsbygoogle.js`)가 메인 스레드 약 350~660ms 사용, 사용하지 않는 JS 약 150KB | 자동 광고 개수 줄이기, 첫 화면 위 광고 슬롯 제거 |
| Medium | 이미지 전송 최적화 여지 80~240KB (원본보다 큰 해상도 전송) | 카드 썸네일에 `srcset`/적정 크기 사용 (Blogger 이미지는 `w400` 등 크기 파라미터) |
| Low | TTFB 약 1.1s (Blogger 서버 + `?m=1` 리디렉션) | 모바일 리디렉션 제거 외에는 제어 범위 밖 |
| Low | 색 대비 부족: 브레드크럼 `#f67938` on `#ffffff` = 2.72:1 (기준 4.5:1) | 브레드크럼 글자색을 `#c2410c` 정도로 진하게 |

CLS는 모든 측정에서 0으로, 이미지에 width/height가 잘 들어가 있습니다.

## 7. Images (85)

- 크롤링한 모든 페이지에서 alt 누락 0건, alt 문구가 설명적(화면 내용 + 키워드)
- 본문 이미지는 WebP, `width`/`height` 지정, 대부분 lazy
- 개선: LCP 이미지 lazy 해제(위), 카드 썸네일 크기 맞추기, 로고 이미지도 첫 화면이므로 lazy 해제

## 8. AI Search Readiness (66)

**잘 되어 있는 것**
- `robots.txt`가 GPTBot, ClaudeBot, PerplexityBot 등을 막지 않음
- 질문형 H2("~는 뭔가요?", "~어떻게 하나요?"), 핵심 요약, FAQ, 표 → AI 답변 인용에 유리한 구조
- 공식 출처 링크로 사실 확인 가능

**발견 사항**

| 심각도 | 내용 | 조치 |
|---|---|---|
| Medium | 저자 엔터티 불명확 (위 Content·Schema 항목과 같은 원인) | 작성자 Person + 소개 페이지 + sameAs |
| Low | `llms.txt` 없음 (404) | 선택사항. 효과에 대한 1차 근거는 약하므로 우선순위 낮음. 만든다면 Blogger에서는 루트 파일 업로드가 안 되므로 생략해도 됨 |
| Low | 글 본문 첫 문장이 고지문이라 AI가 요약할 때 첫 문단 인용이 흐려짐 | 고지문 위치 이동 (Content 항목과 동일) |

## 9. 심층 분석 (전문 에이전트 3개)

1차 감사 뒤 seo-content, seo-sxo, seo-geo 에이전트를 따로 돌린 결과입니다. 상세 내용은 `findings/content-deep.md`, `findings/sxo.md`, `findings/geo-deep.md`에 있습니다.

### 9-1. 검색 노출 (SXO, `findings/sxo.md`)

| 심각도 | 내용 | 조치 |
|---|---|---|
| Critical (확인 필요) | 주요 검색어 8개에서 네이버 통합검색·웹 검색 모두 jiwon4u.kr이 보이지 않음. 대신 운영자의 네이버 블로그 "AI지원포유"와 네이버 클립 "지원포유"는 상위 노출 중 | 구글 서치콘솔·네이버 서치어드바이저에서 색인 여부 먼저 확인, 사이트맵·RSS 제출. 네이버 블로그·클립에서 원문으로 링크 |
| High | 네이버가 MCP·스킬·아티팩트 검색어를 "게임 주제"로 분류 | 제목과 첫 문장에 "클로드 AI"를 명시 |
| High | p11 PPT 글에 상위 결과 다수가 다루는 "클로드 디자인"이 없음 | 해당 섹션 추가 |
| Medium | p12 데스크탑 글 첫 화면에 공식 다운로드 링크 없음 / p17 요금제에 할인·학생·환불·원화 결제 없음 / p04 MCP에 `claude mcp add` 없음 / p10 엑셀은 네이버에서 영상이 우세 | 글별 공백 보완, 기존 네이버 클립을 글 상단에 삽입 |

한계: 웹 검색은 실제 구글 결과 화면이 아니고, 네이버는 PC·비로그인으로 한 번만 수집했습니다. "노출 0"은 색인 안 됨을 뜻하지 않을 수 있으므로 서치콘솔에서 확인이 필요합니다.

### 9-2. 사실 정확성 (GEO·Content, 2026-10-18 claude.com/pricing 대조)

| 심각도 | 내용 | 조치 |
|---|---|---|
| High | p18이 Haiku 4.5($1/$5)를 현재 모델로 적음. 공식 페이지에서 4.5는 Legacy, 현재는 Haiku 5.5 | 수치 갱신 + "2026-10-18 확인" 기준일 표기 |
| High | Max 요금제의 월 $100~$200 API 크레딧이 빠져, p18 FAQ "구독이 있으면 API 무료? → 아니에요"가 Max 사용자에게 틀림 | FAQ 답 수정 |
| High | 글마다 사실이 다름: 가격 기준(p01·p03·p08·p09 부가세 포함 $22/$110 vs p17·p18·p19 세전 $20/$100), Free 플랜 Claude Code(p17 "확인 안 됨" vs p03·p08·p18 "사용 불가"), 사용 플랜(p18 Pro vs p06·p12 Max) | 요금 정보는 p17 하나를 정본으로, 다른 글은 링크 |
| Medium | p17 캡션에 "캡처는 게시 직전 최신본으로 교체" 메모가 남아 있음, 이미지 링크 마크업이 깨짐, 날짜 10-06/10-07 엇갈림. p04·p05·p17은 "최종 업데이트"가 발행일보다 앞섬 | 수정 |

### 9-3. 콘텐츠·신뢰 (`findings/content-deep.md`)

- E-E-A-T: 경험 80, 권위 40. 1차 경험은 강하고 권위는 약합니다.
- 소개 페이지에서 "AI 이미지는 표시합니다"라고 했지만, 로봇 일러스트 썸네일(p18, p20~p26 등)에 표시가 없습니다.
- 템플릿 패턴이 겹칩니다. 27편 모두 FAQ가 있고, 24편에 "이 글에서는…정리했어요"가 들어갑니다. 18일 동안 26편이 발행됐고, 같은 분에 3편이 올라간 경우도 있습니다. 검색어를 그대로 넣은 어색한 문장도 있습니다(예: "로컬 llm 뜻이").
- 키워드 겹침은 베낀 문장 문제가 아니라 검색 의도 문제입니다. 글 쌍 사이 본문 중복은 최대 6.9%라서 통합보다 역할 분리를 권합니다.
  - p08 = 개념, p03 = 설치·사용법, p09 = Cowork
  - 블로그 자동화 글은 p25를 허브로 둡니다.
  - p21 제목에서 "클로드 MCP"를 뺍니다.
- 들어오는 본문 링크가 없는 고립 글이 7개입니다(p01, p02, p03, p05, p15, p16, p27). 가장 긴 글인 p03도 여기에 포함됩니다.
- 보이지 않는 유니코드 워터마크는 없습니다. 문장 길이(평균 26~39자)도 적당합니다.
- 작성자 박스 초안과 소개 페이지 개요는 `findings/content-deep.md`에 있습니다. [ ] 칸은 운영자가 실제 사실로 채워야 합니다.

### 9-4. AI 검색 플랫폼별 예상 점수 (`findings/geo-deep.md`)

Google AI Overviews 68, Perplexity 63, Bing Copilot 56, ChatGPT 55, 네이버 Cue·AI 브리핑 42.

---

## 부록: 크롤링한 URL

`pages/index.txt`(로컬 전용)와 `parsed/*.json`에 페이지별 원자료가 있습니다. 27개 글 모두 HTTP 200, canonical 자기 참조, H1 1개, alt 누락 0건.
