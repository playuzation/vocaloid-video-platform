# 05. 외부 플랫폼 배포(크로스포스트) — API·승인/감사·쿼터·비용·사양·정책 조사 노트

> 기준일 2026-10-07. 조사 방식: WebSearch 스니펫만 사용(WebFetch/curl은 환경 정책상 차단). **조사 도중 세션 공유 WebSearch 예산(200회)이 소진되어** 일부 항목(YouTube 라우드니스 기준, 커버곡 Content ID·배급사 화이트리스트, CapCut/Canva/Buffer 구현 사례, Bilibili 投稿 자격 세부, 크레딧 블록 실례 등)은 추가 검색을 하지 못했다. 이런 항목은 각 절의 Gaps에 표시했다.
> 표기: **미확인** = 출처로 검증하지 못함 / **추정** = 인용한 사실에서 이끌어낸 추론 / **(2차)** = 벤더 블로그 등 2차 출처 / **(검색 요약 기준)** = 검색 도구 요약문에서 나온 내용이라 원문 문언은 확인하지 못함.

---

## 1. YouTube Data API v3 — 업로드 쿼터, OAuth 검증, 감사(private 잠금), resumable 업로드, 메타데이터 필드, Shorts 분류, 권장 인코딩, API 약관 의무

### Takeaway
2026년 현재 업로드 쿼터는 크게 완화됐다. 2025-12-04에 업로드 1회 비용이 약 1,600 units에서 약 100 units로 내려갔고, 2026-06-01부터 `videos.insert`는 별도의 "Video Uploads" 버킷으로 분리됐다(호출당 1 unit, 프로젝트당 하루 100회). 하지만 **공개(public) 업로드**를 하려면 두 가지가 모두 필요하다. ① Google OAuth 민감(sensitive) 범위 검증(`youtube.upload`. CASA 보안 평가는 필요 없음) ② YouTube API Services 컴플라이언스 감사. 2020-07-28 이후 생성된 미검증 프로젝트에서 올린 영상은 감사 전까지 private로 잠긴다.

### Cited Findings

#### 1-1. 쿼터
- 기본 쿼터는 프로젝트당 하루 10,000 units다. "All developers using YouTube's API Services must complete an API Compliance Audit in order to be granted more than the default quota allocation of 10,000 units" — [YouTube API Services - Audit and Quota Extension Form](https://support.google.com/youtube/contact/yt_api_form?hl=fa); [YouTube API Services - Developer Policies](https://developers.google.com/youtube/terms/developer-policies?authuser=7)
- **2025-12-04**: 문서와 Quota Calculator에 "a change in the quota cost of a video upload from approximately 1600 units to approximately 100 units"가 반영됐다 — [YouTube Data API Revision History](https://developers.google.com/youtube/v3/revision_history); [Quota Calculator](https://developers.google.com/youtube/v3/determine_quota_cost)
- **2026-06-01**: "granular quota system"으로 전환되어 `videos.insert`와 `search.list` 호출이 각자 별도 쿼터 버킷으로 과금된다. Quota Calculator 기준 `videos.insert`는 **"Video Uploads" 버킷에서 호출당 1 unit, 하루 100 calls**다 — [Quota Calculator](https://developers.google.com/youtube/v3/determine_quota_cost); [Revision History](https://developers.google.com/youtube/v3/revision_history)
- 2차 출처 해설: 나머지 메서드는 기존 10,000-unit 버킷에 남았다. 변경 전에는 기본 쿼터로 하루 약 6회(10,000 ÷ 1,600)밖에 업로드할 수 없었다 — [OutlierKit](https://outlierkit.com/resources/youtube-api-quota/) (2차); [Blotato](https://www.blotato.com/blog/youtube-api-pricing) (2차); [PostZen](https://www.postzen.dev/blog/youtube-api-quota-exceeded) (2차)
- 부가 호출 비용(Quota Calculator 스니펫 기준): `thumbnails.set` 약 50 units, `captions.insert` 400 units — [Quota Calculator](https://developers.google.com/youtube/v3/determine_quota_cost)

#### 1-2. OAuth 범위(`youtube.upload`)와 Google 앱 검증
- `https://www.googleapis.com/auth/youtube.upload`는 Google 분류상 **sensitive** 범위로 소개된다 — [Phyllo](https://www.getphyllo.com/post/youtube-oauth-scopes) (2차); [Nango docs](https://docs.nango.dev/integrations/all/youtube) (2차)
- Google 공식 설명:
  - Sensitive Scope Verification은 앱의 범위 사용이 기만적이지 않고 적절한 용도인지 확인하는 절차다.
  - 추가 Security assessment(CASA 프레임워크)는 **restricted** 범위에만 요구되며, restricted 범위 앱은 이 평가를 해마다 받아야 한다.
  - 출처 — [Restricted scope verification](https://developers.google.com/identity/protocols/oauth2/production-readiness/restricted-scope-verification); [Google Cloud Console Help – FAQ](https://support.google.com/cloud/answer/13463817?hl=en)
- 소요 기간: Brand Verification 2–3 영업일, Sensitive Scope Verification 10 영업일, Restricted Scope Verification 6주 — [Google Cloud Console Help – FAQ](https://support.google.com/cloud/answer/13463817?hl=en) (검색 요약 기준)
- 검증 전에 민감·제한 범위를 쓰는 앱에는 "unverified app" 화면이 뜨고 **100 new-user cap**이 걸린다. 한도가 소진되면 신규 사용자의 Google 로그인이 막힌다 — [Unverified apps](https://support.google.com/cloud/answer/7454865?hl=en)
- YouTube 범위 검증에 내는 자료(2차): 범위 사용 정당화, OAuth 흐름과 범위 사용을 보여주는 데모 영상, 사용 사례 설명, YouTube API Services ToS 동의. 2–4주 걸린다 — [Nango: register your own YouTube OAuth app](https://nango.dev/docs/api-integrations/youtube/how-to-register-your-own-youtube-api-oauth-app.md) (2차)
- **상충**: 일부 2차 출처는 "sensitive든 restricted든 검증과 security assessment가 모두 필요"하다고 쓴다 — [Ayrshare](https://www.ayrshare.com/solutions/google-api-error-403-unverified-app-how-to-fix-the-audit-pipeline/). 반면 Google 공식 문서는 security assessment를 restricted 범위의 요건으로 규정한다 — [Restricted scope verification](https://developers.google.com/identity/protocols/oauth2/production-readiness/restricted-scope-verification)

#### 1-3. 미검증 프로젝트의 private 잠금과 감사
- 규칙: 2020-07-28 이후 생성된 미검증(unverified) API 프로젝트가 `videos.insert`로 올린 영상은 모두 private viewing mode로 제한된다. 제한을 풀려면 프로젝트마다 ToS 준수 감사(audit)를 받아야 한다 — [Videos: insert](https://developers.google.com/youtube/v3/docs/videos/insert); [YouTube API Services – revision history](https://developers.google.com/youtube/terms/revision-history)
- Developer Policies도 "미검증 API 프로젝트가 업로드한 영상은 기본 private이고, 공개하려면 감사를 받아야 한다"고 적고 있다 — [Developer Policies](https://developers.google.com/youtube/terms/developer-policies)
- 해당 프로젝트로 업로드한 크리에이터에게는 "영상이 private로 잠겼으며, 공식 서비스나 감사받은 서비스를 쓰면 이 제한을 피할 수 있다"는 이메일이 간다. 이 규칙은 쿼터나 privacy 파라미터와 무관한 프로젝트 단위 규칙이라 API 파라미터로 우회할 수 없다 — [Ayrshare](https://www.ayrshare.com/solutions/google-api-error-403-unverified-app-how-to-fix-the-audit-pipeline/) (2차)
- 실제 개발자 이슈 사례 — [porjo/youtubeuploader issue #86](https://github.com/porjo/youtubeuploader/issues/86)
- 감사와 쿼터 확장은 "YouTube API Services - Audit and Quota Extension Form" 하나로 신청한다. 사업자 정보, API Client 정보, YouTube API Services 접근·사용 방식을 묻는다 — [Audit and Quota Extension Form](https://support.google.com/youtube/contact/yt_api_form?hl=fa)

#### 1-4. Resumable 업로드 프로토콜
- 절차: 세션 시작 POST → 응답 `Location` 헤더의 업로드 URL(세션 URI)을 저장 → 바이트를 전송한다. 업로드가 끊겼거나 진행 중이면 서버가 HTTP `308 Resume Incomplete`와, 이미 받은 바이트를 알려주는 `Range` 헤더를 돌려준다 — [Resumable Uploads](https://developers.google.com/youtube/v3/guides/using_resumable_upload_protocol)
- `Content-Range` 헤더는 `FIRST_BYTE`(0부터 시작하는 인덱스)–`LAST_BYTE`/`TOTAL_CONTENT_LENGTH` 세 값으로 구성된다. 청크 크기는 **256 KB의 배수**여야 한다(마지막 청크 제외) — [Resumable Uploads](https://developers.google.com/youtube/v3/guides/using_resumable_upload_protocol)

#### 1-5. 주요 리소스·필드
- `status.privacyStatus`: `private` / `public` / `unlisted` — [Videos resource](https://developers.google.com/youtube/v3/docs/videos)
- `status.publishAt`: 예약 공개 시각(ISO 8601). "It can be set only if the privacy status of the video is private" — [Videos resource](https://developers.google.com/youtube/v3/docs/videos)
- `status.selfDeclaredMadeForKids`: 영상의 "made for kids" 여부를 자가 선언하는 필드 — [Videos resource](https://developers.google.com/youtube/v3/docs/videos)
- `status.containsSyntheticMedia`:
  - **2024-10-30**에 추가됐고, `videos.insert`와 `videos.update`에서 설정할 수 있다.
  - 채널 소유자가 "realistic Altered or Synthetic (A/S) content" 포함 여부를 공개하는 용도다.
  - 예시: 실존 인물이 하지 않은 말·행동을 한 것처럼 보이게 하기, 실제 사건·장소 영상 변조, 실제로 없었던 사실적 장면 생성.
  - 출처 — [Revision History](https://developers.google.com/youtube/v3/revision_history); [Videos: insert](https://developers.google.com/youtube/v3/docs/videos/insert)
- `thumbnails.set`: 최대 2MB, MIME `image/jpeg`, `image/png`, `application/octet-stream` — [Thumbnails: set](https://developers.google.cn/youtube/v3/docs/thumbnails/set?hl=en)
- `captions.insert`: 400 units(1-1 참조) — [Quota Calculator](https://developers.google.com/youtube/v3/determine_quota_cost)

#### 1-6. Shorts 분류
- **2024-10-15**부터 화면비가 정사각형(1:1) 또는 세로형이고 길이가 **3분 이하**인 업로드는 자동으로 Shorts로 분류된다. 그 이전 업로드는 소급해서 재분류되지 않는다(이전 기준은 60초) — [YouTube Help: Understand three-minute YouTube Shorts](https://support.google.com/youtube/answer/15424877?hl=en); [Kapwing](https://kapwing.com/resources/youtube-shorts-is-making-a-huge-change-to-its-maximum-video-length-and-it-will-affect-creators); [Mezha](https://mezha.media/en/2024/10/04/youtube-will-allow-you-to-upload-shorts-up-to-3-minutes-long)
- 가득 차게 표시되는 형식은 세로 9:16이고, 1:1은 대안이다 — [Kapwing](https://kapwing.com/resources/youtube-shorts-is-making-a-huge-change-to-its-maximum-video-length-and-it-will-affect-creators) (2차)
- Content ID claim이 걸린 1분 초과 Shorts는 차단된다(→ §2)

#### 1-7. YouTube 권장 업로드 인코딩(공식 Help)
출처는 모두 [YouTube recommended upload encoding settings](https://support.google.com/youtube/answer/1722171?hl=en)이다.
- **컨테이너**: MP4. moov atom을 파일 앞쪽에 두고(Fast Start), Edit Lists는 쓰지 않는다.
- **오디오**:
  - 코덱: AAC-LC, Opus, Eclipsa Audio 중 하나
  - 채널: Stereo 또는 Stereo + 5.1
  - 샘플레이트: 48kHz(스니펫 기준)
- **비디오**:
  - 코덱·프로필: H.264, progressive(인터레이스 없음), High Profile
  - 구조: 연속 B프레임 2개, Closed GOP, CABAC
  - 비트레이트 방식: VBR
  - 크로마: 4:2:0
- **SDR 비트레이트**:

  | 해상도 | 24–30fps | 48–60fps |
  |---|---|---|
  | 1080p | 8 Mbps | 12 Mbps |
  | 2160p | 35–45 Mbps | 53–68 Mbps |

- **프레임레이트**: 제작한 프레임레이트 그대로 올린다. 흔히 쓰는 값은 24, 25, 30, 48, 50, 60이다.

#### 1-8. YouTube API 약관(Developer Policies / RMF) 중 우리에게 영향을 주는 의무
- RMF: 업로드를 허용하는 API Client는 사용자가 영상의 제목·설명·공개 설정(privacy settings) 같은 속성을 직접 정할 수 있게 해야 한다 — [Required Minimum Functionality](https://developers.google.com/youtube/terms/required-minimum-functionality)
- 메타데이터 편집 기능이 있는 API Client는 사용자의 명시적 동의 없이 영상의 공개 상태를 바꿀 수 없다 — [Developer Policies](https://developers.google.com/youtube/terms/developer-policies)
- 데이터 보관: 30일이 지나면 저장한 데이터를 삭제하거나 새로 고쳐야 한다. 비인가(non-authorized) 통계는 30일을 넘겨 보관할 수 없다. 인가 데이터도 30일마다 사용자 허락과 영상 존재 여부를 다시 확인해야 한다 — [Developer Policies](https://developers.google.com/youtube/terms/developer-policies?authuser=7) (검색 요약 기준)
- **2026-06-01 개정**: "Additional policies on derived metrics and data storage"가 명확해졌다. 감사를 받은 분석(analytics) 용도 개발자가 쿼터 확장 양식으로 별도 허가를 받으면 일부 통계를 30일 넘게 보관할 수 있다 — [YouTube API Services – revision history](https://developers.google.com/youtube/terms/revision-history)
- 브랜딩: YouTube 브랜드 요소와 출처 표시를 해야 하며, 임베드 플레이어 안의 출처 표시를 가려서는 안 된다 — [Developer Policies](https://developers.google.com/youtube/terms/developer-policies?authuser=7) (검색 요약 기준. 원문 문언은 미확인)

### Inferences
- **(추정) 업로드 병목**: 2026-06 이후 병목은 units가 아니라 "프로젝트당 하루 100회 업로드 호출"이다. 이 한도는 사용자별이 아니라 **플랫폼 전체 합산**이므로, 하루 100건 넘는 YouTube 크로스포스트가 예상되면 출시 전에 감사와 쿼터 확장을 신청해야 한다. Video Uploads 버킷도 같은 양식으로 증설되는지는 미확인이다.
- **(추정) 부가 호출 비용**: 업로드 1건에 썸네일(~50)과 가사 자막(400)을 붙이면 공용 10,000 버킷에서 ~450 units를 쓴다. 이 경우 기본 쿼터로는 하루 ~22건이 "풀 패키지" 상한이다. 따라서 다음 두 가지를 기본 설계로 검토할 만하다.
  - `captions.insert`는 선택 옵션으로 둔다.
  - 가사는 설명란에 적는 것을 기본값으로 한다.
  - 2026-06 이후 thumbnails/captions 비용이 바뀌었는지는 미확인이다.
- **(추정) 공개 업로드까지의 경로**:
  1. OAuth 동의 화면 브랜드 검증과 `youtube.upload` 민감 범위 검증(약 2주. CASA 불필요)
  2. YouTube API 컴플라이언스 감사(소요 기간 미확인)
  - 2단계가 끝나기 전에는 API로 올린 영상이 private로 잠겨 실서비스에 쓸 수 없다.
  - 그동안의 폴백: "MP4 다운로드 + YouTube Studio 업로드 안내", 또는 감사를 마친 통합 게시 API 사업자 활용(§9)
- **(추정) 업로드 구현 구조**: 우리 플랫폼은 서버에서 렌더링한 파일을 가지고 있다. 따라서 브라우저가 아니라 백엔드 작업 큐에서 resumable 업로드를 하는 편이 적합하다.
  - 청크: 256KB의 배수(예: 8 MiB)
  - 장애 시: 저장해 둔 세션 URI로 업로드 지점을 조회한 뒤 재개
- **(추정) 업로드 UI에 꼭 넣을 요소**:
  - 제목·설명 편집
  - 공개 범위 선택: 사용자가 명시적으로 고르게 하고, 기본값을 강요하지 않는다.
  - 예약 공개: `publishAt`을 쓰려면 `private`로 업로드해야 한다.
  - "아동용(made for kids)" 선택
  - "변조·합성 콘텐츠" 체크박스(`containsSyntheticMedia`)
- **(추정) 통계 표시**: 커뮤니티 화면에 YouTube 조회수 같은 통계를 보여주려면 30일 갱신·삭제 정책을 지켜야 한다. 동기화 잡을 30일 이내 주기로 돌려야 한다.

### Gaps
- Video Uploads 버킷(하루 100 calls) 증설 절차와 감사 소요 기간: 미확인(검색 예산 소진).
- `captions.insert`에 필요한 OAuth 범위: 미확인.
  - 기존 지식으로는 `youtube.force-ssl`이 필요하다고 알고 있으나 검증하지 못했다.
  - 2차 출처는 `youtube.upload`로 자막·썸네일도 관리할 수 있다고 쓴다 — [Phyllo](https://www.getphyllo.com/post/youtube-oauth-scopes)
  - 구현 전에 공식 레퍼런스를 확인해야 한다.
- 미확인 항목:
  - 자막 파일 지원 포맷(SRT, WebVTT 등)
  - 맞춤 썸네일 사용 자격(채널 전화 인증 등)
  - 15분 초과 업로드 자격
  - 채널별 일일 업로드 한도
- RMF의 업로드 관련 고지 의무 원문(예: YouTube 약관·커뮤니티 가이드 링크 표시): 미확인.
- API에서 Shorts를 명시적으로 지정하는 필드가 있는지: 미확인. (추정: 없다. 화면비와 길이로 자동 분류된다.)
- 이번 스니펫에 없었던 수치: 권장 오디오 비트레이트(스테레오 384kbps 등), 720p·1440p 비트레이트 — 미확인.
- 라우드니스 정규화 기준(약 -14 LUFS): YouTube 공식 문서로 확인하지 못했다. 업계 통설로만 알려져 있다 — 미확인.

---

## 2. YouTube 정책 — YPP "inauthentic content"(2025-07), 변조·합성(A/S) 콘텐츠 공개, Content ID(커버·Shorts)

### Takeaway
2025-07-15부로 YPP의 "repetitious content" 가이드라인이 "inauthentic content"로 이름이 바뀌고 내용이 명확해졌다. 대량 생산되거나 반복적인 콘텐츠는 **수익화에서 배제**된다(삭제가 아니라 수익화 문제다). 템플릿 기반 MV 플랫폼이라면 한 채널에서 비슷한 영상을 대량으로 올리는 패턴을 막는 설계가 필요하다. 애니메이션이거나 명백히 비현실적인 MV는 A/S 공개 의무 대상이 아니다. 기존곡 커버나 리믹스를 Shorts로 낼 때 1분을 넘기고 Content ID claim이 걸리면 전 세계에서 차단된다.

### Cited Findings
- **YPP "inauthentic content" 개정**:
  - YouTube는 **2025-07-15**부터 "mass-produced and repetitious content" 식별에 집중하도록 YPP 정책을 고치고, 기존 "repetitious content" 가이드라인을 "inauthentic content"로 바꿨다 — [Plagiarism Today (2025-07-08)](https://www.plagiarismtoday.com/2025/07/08/youtube-targets-inauthentic-content/); [Gulf News](https://gulfnews.com/technology/youtube-updates-monetisation-policies-ai-and-repetitive-content-ban-begins-july-15-1.500192660)
  - TeamYouTube 측(보도상 Community Manager)은 "a minor update to our longstanding guideline on repetitious content"라고 설명했다. 새로운 제한이 아니며 기존 집행 기준을 유지한다는 취지다 — [PPC Land](https://ppc.land/youtube-clarifies-inauthentic-content-policy-changes/)
  - 재사용 영상이나 AI 생성 영상을 금지하는 것은 아니다. 해설·편집·내레이션처럼 독창적 가치를 더하면 수익화할 수 있고, AI를 쓰는 크리에이터도 콘텐츠가 authentic하면 자격을 유지한다 — [The Daily Star](https://d11.thedailystar.net/tech-startup/news/youtubes-new-monetisation-rule-targets-mass-produced-inauthentic-content-3936511); [Gulf News](https://gulfnews.com/technology/youtube-updates-monetisation-policies-ai-and-repetitive-content-ban-begins-july-15-1.500192660)
  - 관련 보도: 비독창(unoriginal) 콘텐츠 탐지 시스템 개선 — [PPC Land](https://ppc.land/youtube-improves-detection-systems-for-unoriginal-content/)
- **A/S 콘텐츠 공개 제도**:
  - **2024년 3월** Creator Studio에 공개 도구가 들어왔다. 대상은 시청자가 실제 인물·장소·장면·사건으로 착각할 수 있는 "realistic" 콘텐츠를 변조·합성 미디어(생성형 AI 포함)로 만든 경우다 — [Google Blog (India)](https://blog.google/intl/en-in/products/platforms/how-were-helping-creators-disclose-altered-or-synthetic-content/); [exchange4media](https://www.exchange4media.com/digital-news/youtube-to-ask-creators-to-disclose-ai-usage-133287.html)
  - **공개 불필요**: "We're not requiring creators to disclose content that is clearly unrealistic, animated, includes special effects, or has used generative AI for production assistance." 예: 애니메이션, 유니콘을 타고 판타지 세계를 달리는 장면, 색보정·조명 필터, 배경 흐림·빈티지 같은 특수효과, 뷰티 필터 — [Google Blog (India)](https://blog.google/intl/en-in/products/platforms/how-were-helping-creators-disclose-altered-or-synthetic-content/)
  - **공개 필요**: 실존 인물 얼굴 교체나 음성 합성 같은 사실적 인물 초상 사용, 실제 사건·장소 변조(실제 건물에 불이 난 것처럼 보이게 하기 등), 사실적 장면 생성 — [Google Blog (India)](https://blog.google/intl/en-in/products/platforms/how-were-helping-creators-disclose-altered-or-synthetic-content/)
  - 라벨은 설명란 확장 영역이나 플레이어 앞면에 표시된다. 같은 기능의 API 필드가 `status.containsSyntheticMedia`(2024-10-30 추가)다 — [Google Blog (India)](https://blog.google/intl/en-in/products/platforms/how-were-helping-creators-disclose-altered-or-synthetic-content/); [Revision History](https://developers.google.com/youtube/v3/revision_history)
- **Content ID와 Shorts**:
  - 1분 초과 Shorts에 활성 Content ID claim이 있으면 정책 종류와 무관하게(수동 claim 포함) YouTube 전역에서 차단된다. 재생·추천·수익화가 모두 불가하지만 채널 페널티는 없다. 해당 부분을 빼거나, 잘못된 claim이면 이의를 제기해 해결하면 다시 볼 수 있다 — [Understand three-minute YouTube Shorts](https://support.google.com/youtube/answer/15424877?hl=en); [Manage Shorts as a rights holder](https://support.google.com/youtube/answer/13053317)
  - 같은 Help 문서 스니펫에 "Official Artist Channels 또는 음악 Content Owner에 연결된 채널은 (1–3분 세로 영상의 Shorts 분류) 기준일이 2025-12-08"이라는 언급이 있다 — [Understand three-minute YouTube Shorts](https://support.google.com/youtube/answer/15424877?hl=en) (정확한 의미 해석은 미확인)
  - Content ID claim의 일반 설명 문서 — [Learn about Content ID claims](https://support.google.com/youtube/answer/6013276)

### Inferences
- **(추정) 템플릿 MV의 "inauthentic" 리스크**: 곡(음원)이 각각 다르고 사용자가 일러스트·연출을 커스터마이즈한다면 리스크는 낮다. 다만 아래 패턴은 "mass-produced/repetitious"로 평가될 위험이 크다.
  - (a) 같은 템플릿에 가사만 바꾼 영상을 한 채널이 대량 업로드
  - (b) 한 곡에서 자동 생성한 Shorts 변형을 연속 업로드
  - (c) 플랫폼 공식 채널이 사용자 작품을 자동으로 일괄 재업로드
- **(추정) 대응책**:
  - 템플릿을 다양하게 만들고, 필수 커스터마이즈 단계를 둔다.
  - 일괄 자동 업로드 기능은 피하고, 업로드마다 사용자가 직접 확인하게 한다.
  - 크레딧과 제작 노트로 독창적 기여를 드러낸다.
- **(추정) A/S 공개 판단**:
  - VOCALOID/Synthesizer V 가창에 일러스트·애니메이션을 입힌 MV는 "clearly unrealistic/animated"에 해당하므로 대개 공개 의무가 없다.
  - 플랫폼이 사실적인 AI 영상 생성이나 실존 가수 음색 복제(보이스 컨버전)를 제공한다면 `containsSyntheticMedia=true`를 안내해야 한다.
  - UI는 기본값 false에 설명 툴팁을 달고, 최종 선택은 사용자에게 맡긴다.
- **(추정) Shorts 길이 제한**: 기존 보카로곡 커버나 리믹스는 작곡 권리자가 Content ID claim을 걸 수 있다. "커버·리믹스" 플래그가 켜진 작품은 Shorts 출력을 60초 이하로 제한하는 옵션이 바람직하다.
- **(추정) 자기 곡 claim 주의**: 사용자가 배급사(DistroKid, TuneCore 등)를 통해 자기 곡을 Content ID에 등록했다면, 우리 플랫폼을 거친 본인 업로드에도 claim이 걸릴 수 있다. "본인 채널을 허용 목록(화이트리스트)에 등록했는지 확인하라"는 안내를 검토할 만하다. 이를 뒷받침하는 검증된 출처는 없다.

### Gaps
- 미확인(검색 예산 소진):
  - 커버곡 Content ID 처리 세부(작곡권과 음원권, JASRAC·NexTone 관리곡의 수익 배분)
  - 배급사 Content ID와 본인 채널 화이트리스트 절차
- YPP "inauthentic content" 공식 Help 원문의 정의 문구: 보도로만 확인했고 원문은 확인하지 못했다.
- OAC/Content Owner 채널의 2025-12-08 기준일이 정확히 무엇을 뜻하는지: 미확인.

---

## 3. TikTok Content Posting API — Direct Post vs Upload(초안), 감사와 SELF_ONLY 제한, 레이트 리밋, 사양, AI 라벨

### Takeaway
TikTok은 두 가지 게시 경로를 제공한다. **Direct Post**는 우리 앱에서 바로 게시하는 방식이고, **Upload**는 사용자의 TikTok 받은편지함으로 초안을 보내 앱에서 마무리하게 하는 방식이다. 감사를 받기 전의 Direct Post는 `SELF_ONLY`(비공개)로 강제되고, 24시간에 최대 5명, 비공개 계정만 쓸 수 있어 실서비스에는 감사가 필수다. 감사는 Content Sharing Guidelines의 UX 요건을 지켰는지를 본다(상호작용 설정 기본 해제, 상업 콘텐츠 공개 토글, Music Usage Confirmation 동의 문구 등).

### Cited Findings
- **두 가지 게시 경로**:
  - **Direct Post**: 크리에이터가 앱에서 곧바로 자기 프로필에 게시한다. 캡션, 해시태그, 공개 범위 선택 같은 게시 설정은 TikTok 앱과 같다.
  - **Upload**: 초안을 TikTok에 올리면 크리에이터가 받은편지함 알림을 받고, TikTok 제작 흐름 안에서 편집한 뒤 게시한다.
  - 출처 — [Content Posting API (product)](https://developers.tiktok.com/products/content-posting-api); [Share to TikTok](https://developers.tiktok.com/docs/en/content-publishing-landing); [Get started – Upload content](https://developers.tiktok.com/doc/content-posting-api-get-started-upload-content/)
- **감사 전 제한(Direct Post)**:
  - 감사 전 클라이언트도 Direct Post를 쓸 수는 있지만, 게시물은 ToS 준수 감사를 통과할 때까지 private로 제한된다. 사용자가 무엇을 고르든 `privacy_level`이 `SELF_ONLY`로 강제된다 — [Get started](https://developers.tiktok.com/doc/content-posting-api-get-started); [Direct Post API reference](https://developers.tiktok.com/docs/en/content-posting-api-reference-direct-post); [Postiz docs](https://docs.postiz.com/providers/tiktok.md) (2차)
  - 감사 전에는 24시간 안에 게시할 수 있는 사용자가 최대 5명이고, 그 계정들은 게시 시점에 비공개 계정이어야 한다 — [Direct Post API reference](https://developers.tiktok.com/docs/en/content-posting-api-reference-direct-post); [bundle.social (2026-08-02)](https://bundle.social/blog/tiktok-api-approval) (2차)
  - "앱 승인이 Direct Post의 마지막 단계는 아니다. 공개 게시를 하려면 감사가 따로 필요하다" — [bundle.social](https://bundle.social/blog/tiktok-api-approval) (2차)
- **`creator_info` 조회(필수)**:
  - 앱은 이 API로 해당 계정에서 고를 수 있는 `privacy_level_options`와 상호작용 설정을 화면에 보여줘야 한다.
  - `max_video_post_duration_sec`는 크리에이터마다 다르며, 이 값으로 너무 긴 영상의 게시를 막아야 한다.
  - 사용자 토큰당 분당 20회로 제한된다.
  - 출처 — [Query Creator Info](https://developers.tiktok.com/doc/content-posting-api-reference-query-creator-info)
- **`privacy_level` 값**: `PUBLIC_TO_EVERYONE`, `MUTUAL_FOLLOW_FRIENDS`, `FOLLOWER_OF_CREATOR`, `SELF_ONLY`. 반드시 `creator_info`가 돌려준 옵션 중 하나여야 한다 — [Direct Post API reference](https://developers.tiktok.com/docs/en/content-posting-api-reference-direct-post)
- **AI 라벨 `is_aigc`**(boolean): true로 보내면 AI 생성 콘텐츠로 표시되어 "Creator labeled as AI-generated" 태그가 붙는다 — [Direct Post API reference](https://developers.tiktok.com/docs/en/content-posting-api-reference-direct-post)
- **미디어 전송**:
  - 청크 크기는 5–64MB다. 마지막 청크만 최대 128MB까지 허용된다.
  - 5MB 미만 파일은 통째로 올린다.
  - 청크 수는 1–1000개다.
  - 영상 최대 크기는 **4GB**다.
  - 출처 — [Media Transfer Guide](https://developers.tiktok.com/docs/en/content-posting-api-media-transfer-guide)
- **레이트 리밋**: Direct Post init은 사용자 액세스 토큰당 **분당 6회**다 — [Direct Post API reference](https://developers.tiktok.com/docs/en/content-posting-api-reference-direct-post); [PostZen](https://www.postzen.dev/blog/tiktok-api) (2차)
- **일일 게시 상한**: 크리에이터당 하루 약 **15건**이다. 앱별 한도가 아니라 Direct Post를 쓰는 모든 API 클라이언트를 합친 수치다 — [PostZen](https://www.postzen.dev/blog/tiktok-api) (2차); [BulkPublish](https://www.bulkpublish.com/blog/tiktok-content-posting-api/) (2차)
- **길이**: API 업로드는 3초–10분이라는 2차 정보가 있다 — [PostProxy](https://postproxy.dev/blog/how-to-post-to-tiktok-via-api/) (2차). 공식 기준은 크리에이터별 `max_video_post_duration_sec`다.
- **Content Sharing Guidelines(감사 기준 UX)** — 출처는 모두 [Content Sharing Guidelines](https://developers.tiktok.com/docs/en/content-sharing-guidelines)이다.
  - 상호작용 설정은 사용자가 직접 켜야 하며, 기본으로 체크된 항목이 있어서는 안 된다.
  - 게시 버튼 앞에 "By posting, you agree to TikTok's Music Usage Confirmation"이라는 동의 문구를 둬야 한다.
  - Content Disclosure Setting은 기본값이 off다. 켜면 "Your brand"와 "Branded content" 체크박스가 나온다.

### Inferences
- **(추정) MVP 경로**: 감사 전에는 **Upload(초안 → 받은편지함)**를 기본 경로로 삼는 편이 낫다. 최종 게시는 사용자가 TikTok 앱에서 하므로 공개 범위 제한을 피할 수 있다. 다만 Upload 경로에도 감사 요건이 붙는지는 미확인이다. Direct Post는 감사를 통과한 뒤 켠다.
- **(추정) 감사 대비 UX 체크리스트**:
  - 크리에이터 닉네임과 아바타 표시
  - `creator_info` 기반 공개 범위 드롭다운(기본값을 두지 않는 것으로 추정 — 미확인)
  - 댓글·듀엣·스티치 토글 기본 off
  - 상업 콘텐츠 공개 토글
  - Music Usage Confirmation 문구
  - 게시 전 미리보기와 처리 상태 표시
  - `max_video_post_duration_sec`를 넘는 영상 차단
- **(추정) AI 라벨**: 보카로 MV는 AI 가창 합성 소프트웨어(Synthesizer V의 AI 보이스뱅크 등)를 쓰는 경우가 많다. 이것이 `is_aigc` 라벨 의무 대상인지는 TikTok 정책 원문으로 확인하지 못했다. 사용자가 고르는 토글로 제공하고, 플랫폼이 생성형 AI 영상을 썼다면 기본값을 true로 두는 정책을 검토할 만하다.
- **(추정) 레이트 리밋 대응**: 사용자별 작업 큐로 분당 6회 init을 지키고, 일일 약 15건 상한을 사용자에게 안내한다. 렌더 파일은 서버에서 FILE_UPLOAD 방식으로 청크 업로드한다(예: 청크 10MB).

### Gaps
- TikTok 공식 영상 사양(지원 컨테이너·코덱, 해상도 범위, fps): 이번 조사에서 공식 스니펫을 얻지 못했다 — 미확인.
- 스코프 이름(`video.publish`, `video.upload` 등), 앱 리뷰 절차·소요 기간, 감사 소요 기간: 미확인.
- `PULL_FROM_URL`을 쓸 때 도메인·URL 접두사 검증 요건: 미확인.
- Content Sharing Guidelines의 "공개 범위 기본값 없음" 조항과 닉네임 표시 의무의 원문: 스니펫으로 확인하지 못했다 — 미확인.
- 커뮤니티 가이드라인의 AIGC 라벨 의무 범위와 C2PA 자동 라벨: 검색 예산 소진으로 미확인.
- [Migration Notice for TikTok's Share Video API](https://developers.tiktok.com/bulletin/migration-notice-share-video-api/)의 내용: 미확인.

---

## 4. Instagram Reels — Instagram Platform API 게시 요건(계정 유형·권한), 일일 한도, 영상 사양

### Takeaway
Reels는 Instagram Platform API로 게시할 수 있다. 공식 문서 기준 **프로페셔널 계정(비즈니스·크리에이터)만** 지원하고(개인 계정 불가), 계정당 24시간 이동창으로 **API 게시 100건**까지다. `instagram_business_content_publish` 등의 권한에 Advanced Access를 받으려면 Meta App Review가 필요하다(2차). 사양 상한은 15분, 300MB, 25Mbps다.

### Cited Findings
- **게시 한도**: "Instagram accounts are limited to 100 API-published posts within a 24-hour moving period." 캐러셀은 1건으로 계산하며, 캐러셀 하나에 최대 10개까지 담을 수 있다 — [Publish Content using the Instagram Platform](https://developers.facebook.com/docs/instagram-platform/content-publishing/)
- **상충(과거 정보)**: 예전 문서와 기사에는 "비즈니스 계정당 24시간 25건", "크리에이터 계정 미지원"이라고 되어 있다 — [Medium (datkira)](https://datkira.medium.com/instagram-graph-api-overview-content-publishing-limitations-and-references-to-do-quickly-99004f21be02); [WERSM](https://wersm.com/the-new-instagram-content-publishing-api-makes-scheduling-posts-a-reality/). 현행 공식 문서는 100건이고, 아래처럼 크리에이터도 지원한다.
- **Instagram API with Instagram Login**: Instagram 프로페셔널 계정(businesses and creators)이 앱을 통해 콘텐츠 게시를 포함한 활동을 관리할 수 있다 — [Instagram API with Instagram Login](https://developers.facebook.com/docs/instagram-platform/instagram-api-with-instagram-login/); [Overview of the Instagram API](https://developers.facebook.com/docs/instagram-platform/overview/)
- Facebook Login 경로에 대한 2차 정보: "Facebook 페이지에 연결된 Instagram 비즈니스 계정만 지원" — [ContentStudio](https://docs.contentstudio.io/article/830-what-are-the-requirements-to-directly-publish-images-and-videos-to-instagram) (2차)
- **권한**: `instagram_business_basic`과 `instagram_business_content_publish`가 필요하며, Advanced Access를 받으려면 Meta App Review를 통과해야 한다 — [PostProxy](https://postproxy.dev/blog/post-to-instagram-via-api/) (2차); [upload-post](https://www.upload-post.com/instagram-api/) (2차); [Mallary](https://mallary.ai/blog/instagram-api) (2차)
- **2단계 게시**:
  1. `POST /{ig-user-id}/media`로 컨테이너 생성
  2. `POST /{ig-user-id}/media_publish`로 게시
  - 남은 한도 확인: `GET /{ig-user-id}/content_publishing_limit`
  - 컨테이너는 정확히 24시간 뒤 만료된다. 24시간 넘게 예약하려면 게시 직전(3–5분 전)에 컨테이너를 만들어야 한다.
  - 출처 — [IG User Media](https://developers.facebook.com/docs/instagram-platform/instagram-graph-api/reference/ig-user/media/); [PostProxy Reels guide](https://postproxy.dev/blog/instagram-reels-api-publishing-guide/) (2차)
- **Reels 영상 사양** — [IG User Media](https://developers.facebook.com/docs/instagram-platform/instagram-graph-api/reference/ig-user/media/); [AdaptlyPost](https://adaptlypost.com/blog/instagram-reels-api-max-length-file-size) (2차)

  | 항목 | 사양 |
  |---|---|
  | 컨테이너 | MOV 또는 MP4(MPEG-4 Part 14), moov atom 앞쪽 |
  | 오디오 | AAC, 최대 48kHz, 1–2채널, 128kbps |
  | 비디오 코덱 | HEVC 또는 H.264, progressive, closed GOP, 4:2:0 |
  | 프레임레이트 | 23–60fps |
  | 해상도 | 가로 최대 1920px |
  | 화면비 | 0.01:1–10:1 (9:16 권장) |
  | 비디오 비트레이트 | VBR, 최대 25Mbps |
  | 길이 | 3초–15분 |
  | 파일 크기 | 최대 300MB |

### Inferences
- **(추정) 계정 전환 안내**: 우리 사용자 중 상당수는 개인 계정일 것이다. 연동할 때 "프로페셔널 계정(크리에이터)으로 전환하세요"라는 안내 흐름이 필요하다.
- **(추정) 영상 전달 방식**: 컨테이너를 만들 때는 공개적으로 접근 가능한 영상 URL을 넘기는 방식이 일반적이다. 렌더 결과물을 짧게 만료되는 서명 URL(CDN)로 노출하고, 게시가 끝나면 만료시키는 구조가 필요하다.
- **(추정) 게시 한도의 의미**: 하루 100건은 개인 크리에이터에게 사실상 무제한이다. 병목은 App Review 통과다.
- **(추정) 공통 세로 프리셋 기준**: 15분, 300MB, 25Mbps가 공통 세로 프리셋의 상한이 된다. Shorts(3분), TikTok(4GB)보다 보수적이기 때문이다.

### Gaps
- 미확인:
  - App Review 소요 기간과 Business Verification 필요 여부
  - Instagram Login 경로에서 Facebook 페이지 연결이 필요 없는지
- 영상 URL 대신 직접(resumable) 업로드를 지원하는지: 미확인.
- Reels 옵션 필드(`share_to_feed`, `cover_url`, `thumb_offset`, `audio_name`, collaborators, trial reels)의 현행 사양: 미확인.
- Meta의 AI 라벨("AI info")을 API로 설정하는 필드가 있는지: 미확인.

---

## 5. X API — 미디어 업로드(청크)와 2025–26 요금 체계·한도

### Takeaway
X API는 **2026-02-06부터 pay-per-use(선불 크레딧)가 기본**이 됐고, 무료 티어는 신규 가입에서 폐지됐다. 2026-04-20 개정 기준으로 게시 1건은 **$0.015**, **URL을 포함한 게시는 $0.20**이다. 영상은 v2 청크 업로드(initialize → append → finalize → status)로 올린다. 영상 길이·용량 한도는 문서마다 다르다(구 문서는 140초·512MB, 최근 2차 자료는 20분·8GB).

### Cited Findings
- **요금 체계 전환**:
  - **2026-02-06** pay-per-use가 출시되어 기본 요금제가 됐고, 신규 가입자의 무료 티어는 폐지됐다. 기존 무료 사용자에게는 1회성 $10 바우처가 지급됐다 — [X Developers: Announcing the Launch of X API Pay-Per-Use Pricing](https://devcommunity.x.com/t/announcing-the-launch-of-x-api-pay-per-use-pricing/256476); [MediaNama (2026-02)](https://www.medianama.com/2026/02/223-x-developer-api-pricing-pay-per-use-model/); [X Developers: No free plan?](https://devcommunity.x.com/t/no-free-plan/257026)
  - 그 전에 pilot 공지가 있었다 — [Announcing the X API Pay-Per-Use Pricing Pilot](https://devcommunity.x.com/t/announcing-the-x-api-pay-per-use-pricing-pilot/250253) (공지 날짜 미확인)
- **2026-04-20 요금 개정** — [X API Pricing Update (effective 2026-04-20)](https://devcommunity.x.com/t/x-api-pricing-update-owned-reads-now-0-001-other-changes-effective-april-20-2026/263025); [About the X API Pricing Update](https://devcommunity.x.com/t/about-the-x-api-pricing-update/263073)

  | 항목 | 가격 |
  |---|---|
  | `POST /2/tweets` 게시 | $0.015/건 (이 날짜부로 인상) |
  | URL 포함 게시 | $0.20/건 |
  | summoned replies | $0.01/건 |
  | Owned Reads | $0.001/리소스 |

- **크레딧 운용**: 크레딧은 미리 사서 쓰고, 사용량만큼 자동 차감된다. 구독료나 월 상한은 없다. 크레딧이 떨어지면 요청이 실패하며, 실패한 요청은 과금되지 않는다. 월 Post read 상한은 2백만 건이다 — [Usage and Billing](https://docs.x.com/x-api/fundamentals/post-cap); [X API introduction](https://docs.x.com/x-api/introduction)
- **상충**: 2차 출처는 읽기 상한을 월 3백만 건으로 적었다 — [upload-post](https://www.upload-post.com/x-api-pricing/) (2차). 출시 당시 보도에는 게시물 읽기가 $0.005/건으로 나온다 — [MediaNama](https://www.medianama.com/2026/02/223-x-developer-api-pricing-pay-per-use-model/)
- **미디어 업로드 과금이 불명확함**: 공식 요금표에는 Post: Create $0.015와 Media Metadata $0.005만 있다. 그런데 개발자 포럼에는 "미디어 객체 1개마다 PostCreate 1건, 최종 게시에 1건이 더 과금되는 것 같다"는 정황이 보고됐다 — [X Developers: Undocumented PostCreate usage for media uploads](https://devcommunity.x.com/t/undocumented-postcreate-usage-for-media-uploads-please-clarify-pay-per-use-billing/274646); [Pay-per-Use cost not matching](https://devcommunity.x.com/t/pay-per-use-cost-not-maching-with-real-cost/259330)
- **레거시 티어(2차)**: Basic($200/월)은 2026-06-01 이후, Pro($5,000/월)는 2026-09-01 이후 순차적으로 pay-per-use로 강제 전환된다. 2026-09 기준 신규 가입자에게는 Free·Basic·Pro 티어가 없다 — [upload-post](https://www.upload-post.com/x-api-pricing/) (2차); [Blotato](https://www.blotato.com/blog/twitter-api-pricing) (2차)
- **레이트 리밋(2차)**:
  - 미디어 업로드: 15분당 500회, 24시간당 50,000회
  - `POST /2/tweets`: 사용자당 15분에 100회, 앱당 24시간에 10,000회
  - 출처 — [upload-post](https://www.upload-post.com/x-api-pricing/) (2차)
- **신규 크레딧 인센티브**: 첫 결제 카드 등록 시 $20, 첫 자동충전 금액을 최대 $50까지 매칭한다. xAI API 크레딧을 최대 20% 환급한다 — [X API introduction](https://docs.x.com/x-api/introduction); [Usage and Billing](https://docs.x.com/x-api/fundamentals/post-cap) (검색 요약 기준)
- **v2 청크 업로드 순서**:
  1. `POST /2/media/upload/initialize`
  2. `/2/media/upload/{id}/append`
  3. `/2/media/upload/{id}/finalize`
  4. `GET /2/media/upload?command=STATUS`
  - 출처 — [Chunked Media Upload](https://docs.x.com/x-api/media/quickstart/media-upload-chunked); [Media upload endpoints on the X API v2](https://docs.x.com/x-api/media/introduction); [bundle.social](https://bundle.social/blog/x-api-media-upload) (2차)
  - 청크는 5MB 이하를 권장한다 — [Media best practices (docs.x.com)](https://docs.x.com/x-api/media/quickstart/best-practices.md) (검색 요약 기준)
- **영상 사양(Media Best Practices, v1.1 문서)** — [Media Best Practices](https://developer.x.com/en/docs/x-api/v1/media/upload-media/uploading-media/media-best-practices)
  - 코덱: H.264 High Profile 영상, AAC-LC 오디오(HE-AAC 미지원)
  - fps: 30 또는 60
  - 권장 해상도: 1280×720, 720×1280, 720×720
  - 제약: 32×32–1280×1024, 60fps 이하, 512MB 이하, 0.5–140초, 화면비 1:3–3:1
  - 오디오: 모노 또는 스테레오(5.1 불가), 128kbps(스니펫 기준)
- **상충**: `tweet_video`/`amplify_video`는 기본 최대 20분·8GB, X Premium은 125분·16GB라는 정보가 있다 — [bundle.social](https://bundle.social/blog/x-api-media-upload) (2차); [AdaptlyPost](https://adaptlypost.com/blog/twitter-api-media-category-tweet-video-amplify-video) (2차)

### Inferences
- **(추정) 건당 비용**:
  - URL 없는 영상 게시 1건 ≈ $0.015 + 미디어 관련 과금(불명확. 최대 +$0.015) ≈ **$0.015–0.03**
  - 플랫폼 링크를 넣으면 **$0.20 이상**
- **(추정) 월 1만 건 크로스포스트 기준 비용**: URL 없이 약 $150–300, URL 포함 시 약 $2,000 이상이다.
- **(추정) 비용 대응 설계**:
  - (a) API 게시 본문에는 기본적으로 링크를 넣지 않는다.
  - (b) 링크는 사용자가 직접 답글이나 프로필로 붙이게 안내한다.
  - (c) X 크로스포스트는 유료 플랜이나 일일 한도로 게이팅한다.
- **(추정) 비용 부담 구조**: 모든 사용자의 X 게시 비용이 우리 개발자 계정 크레딧에서 빠진다. 사용자별 일일 한도, 크레딧 잔액 알림, 남용 방지가 필요하다.
- **(추정) 보수적인 X 프리셋**:
  - 해상도: 1280×720(가로) 또는 720×1280(세로)
  - 코덱: H.264 High, AAC-LC 128kbps 이상
  - 한도: 140초 이하, 512MB 이하
  - 긴 MV는 140초 이하 하이라이트 클립과 원본 링크(사용자 수동) 조합으로 처리한다.
- **(추정) 웹 인텐트**: X 웹 인텐트(브라우저 공유 창)는 API 비용이 없지만 영상을 첨부할 수 없다. 텍스트와 링크만 공유하는 무료 대안이다.

### Gaps
- v2 미디어 업로드에 필요한 OAuth 2.0 scope(예: `media.write`)와 v1.1 `media/upload` 폐기 일정: 미확인.
- 레거시 Basic·Pro 전환 일정: 공식 문서로는 확인하지 못했다(2차 출처만 있음).
- `tweet_video`의 실제 길이·용량 상한(140초 vs 20분): 최신 공식 문서를 확인하지 못해 상충 상태로 남아 있다.

---

## 6. Niconico — 공식 업로드 API 부재와 대안, 업로드 사양, 보카로 태그 관례, ボカコレ 참가 방식

### Takeaway
니코니코동화에는 제3자가 쓸 수 있는 공식 업로드 API가 확인되지 않는다. 일부 외부 API는 2023-04경 종료됐고 대체 API도 예정이 없으며, 업로드는 비공개 내부 API로만 이뤄진다. 따라서 현실적인 방법은 "MP4 다운로드 + 메타데이터 복사 + 업로드 페이지 열기"라는 **투고 어시스트**다. 업로드 용량은 **2024-10-29부터 1개당 6GB**다. ボカコレ는 니코니코에 투고하고 지정 부문 태그를 1개만 붙인 뒤 **태그를 잠그는** 방식으로 참가한다.

### Cited Findings
- **공식 API 상황**:
  - 2023년 4월경을 기점으로 flapi.nicovideo.jp 등 여러 API의 제공이 끝났고, 대체 API를 준비할 예정은 없다 — [ニコニコインフォ: 一部非公開APIの提供終了](https://blog.nicovideo.jp/niconews/182541.html)
  - 비공개(비공식) API를 정리한 커뮤니티 프로젝트가 있다 — [niconicolibs/api](https://github.com/niconicolibs/api)
  - 비공식 API로 업로드하는 방법(로그인 → 투고 요청 → 청크 업로드 → 메타데이터 전송)을 소개한 글이 있지만, 업로드 API가 바뀌어 예전 방법으로는 더 이상 투고할 수 없다 — [Zenn: ニコ動で動画をAPIからアップロードする](https://zenn.dev/negima1072/articles/howto-upload-nicovideo-by-api)
  - 2008년 API 개방 계획 공지(역사적 맥락) — [ニコニコ動画 開発者ブログ (2008-03)](http://blog.nicovideo.jp/developers_diary/2008/03/api.html)
- **업로드 사양**:
  - **2024-10-29(화)**부터 업로드 용량이 1개당 3GB에서 **6GB**로 늘었다. 같은 업데이트로 PC판 투고 화면의 태그 설정에서 같은 태그를 쓰는 영상 수와 비슷한 태그를 볼 수 있게 됐다 — [ニコニコインフォ](https://blog.nicovideo.jp/niconews/233213.html); [ニコニコ公式 X](https://x.com/nico_nico_info/status/1851178744657990072)
  - 이력: 2016년 최대 1.5GB로 확대(일부 사용자 대상)되며 권장 포맷도 바뀌었다 — [dwango](https://dwango.co.jp/news/6888216682088765601/); [ITmedia](https://www.itmedia.co.jp/news/articles/1608/10/news135.html); [INTERNET Watch](https://internet.watch.impress.co.jp/docs/news/1014801.html)
- **重音テト**: 2023년 4월 Synthesizer V 상용 소프트로 발매됐다. 현재 UTAU를 넘어 음성합성 캐릭터의 대표 격이다 — [ニコニコ大百科: 重音テト](https://dic.nicovideo.jp/a/%E9%87%8D%E9%9F%B3%E3%83%86%E3%83%88)
- **태그 사용 실례**:
  - 原口沙輔의 「ノシノシ」(2024-09-14 니코니코 공개)는 대백과 동영상 페이지에서 "初音ミク, 重音テトSV動画"로 분류돼 있다. 「重音テトSV」가 태그·분류명으로 쓰이고 있다는 뜻이다 — [ニコニコ大百科: ノシノシ (sm44102888)](https://dic.nicovideo.jp/v/sm44102888)
  - 검색 결과에서 重音テトSV를 쓴 오리지널곡들이 「VOCALOIDオリジナル曲」 태그와 함께 투고된 것으로 요약됐다 — [ニコニコ大百科: 重音テト](https://dic.nicovideo.jp/t/a/%E9%87%8D%E9%9F%B3%E3%83%86%E3%83%88) (검색 요약 기준. 개별 영상의 태그는 미확인)
- **ボカコレ 부문 구성**:
  - **TOP100**: 모든 보카로P 참가 가능
  - **ルーキー**: 보카로P 데뷔 2년 이내
  - **REMIX**: 기존곡에서 리믹스 착상을 얻은 작품
  - **エキシビション**: 랭킹에 참가하지 않고 신곡이나 2차 창작 영상을 투고
  - 출처 — [ボカコレ公式](https://vocaloid-collection.jp/); [ボカコレ企画一覧](https://vocaloid-collection.jp/project/)
- **ボカコレ2026夏 일정**: 2026-08-20~24 — [初音ミクWiki: ボカコレ2026夏](https://w.atwiki.jp/hmiku/pages/75362.html); [ボカコレ公式](https://vocaloid-collection.jp/)

  | 부문 | 투고 기간 |
  |---|---|
  | エキシビション | 8/20 19:00 – 8/24 17:00 |
  | ルーキー | 8/21 19:00 – 8/24 17:00 |
  | TOP100·REMIX | 8/22 0:00부터 |

- **태그 규칙**: 부문 태그 예시는 「ボカコレ2026夏TOP100参加」「ボカコレ2026夏ルーキー参加」「ボカコレ2026夏REMIX参加」「ボカコレ2026夏ex」다. 부문 태그를 여러 개 붙이면 집계 대상에서 빠지며, 부문 태그는 반드시 잠가야(ロック) 한다 — [ボカコレ公式 FAQ](https://vocaloid-collection.jp/faq/); [初音ミクWiki](https://w.atwiki.jp/hmiku/pages/75362.html); [Yahoo!知恵袋](https://detail.chiebukuro.yahoo.co.jp/qa/question_detail/q12329358587) (검색 요약 기준)
- **상충(과거 회차)**: 예전 FAQ에는 "여러 랭킹 기획에 동시에 참가할 수 있다. 참가할 기획의 태그를 붙이고 투고자가 잠가 달라"고 되어 있다 — [ボカコレ 2022春 FAQ](https://vocaloid-collection.jp/2022-spring/faq/). 최근 회차에서는 부문 태그를 여러 개 붙이는 것이 금지된 것으로 보인다(추정).
- **점수 산정**: 니코니코 랭킹과 같이 재생수, 댓글수, 마이리스트수, 좋아요 수를 바탕으로 집계한다 — [ボカコレ 2022春 FAQ](https://vocaloid-collection.jp/2022-spring/faq/)
- **타 플랫폼 동시 투고**: "니코니코 투고와 동시에 YouTube 등 다른 동영상 사이트에 투고해도 문제없다"(과거 회차 FAQ) — [ボカコレ 2022春 FAQ](https://vocaloid-collection.jp/2022-spring/faq/)
- **다음 회차**: 2027-02-19(목)~23(화·공휴일) — [ボカコレ 공식 X](https://x.com/the_voca_colle/status/2090559508703768657)
- 투고 팁 페이지가 따로 있다 — [ボカコレ投稿Tips](https://vocaloid-collection.jp/creatormanual/)

### Inferences
- **(추정) 비공식 API는 쓰지 않는다**: 공식 API가 없으므로 비공식 내부 API로 자동 업로드하는 방식은 비권장이다. 약관 위반·계정 정지 위험이 있고, 업로드 API가 이미 한 번 바뀌어 기존 방법이 막혔을 정도로 사양 변경이 잦다.
- **(추정) "니코니코 투고 어시스트" 흐름**:
  1. MP4(6GB 이하)와 썸네일을 다운로드한다.
  2. 제목, 설명, 태그 목록을 클립보드로 복사한다.
  3. 니코니코 투고 페이지를 새 탭에서 연다.
  4. 투고가 끝나면 사용자가 sm 번호(또는 URL)를 입력해 우리 커뮤니티에 링크·임베드를 연결한다.
- **(추정) ボカコレ 지원 기능**:
  - 개최 기간 배너
  - 부문 태그를 하나만 고르게 하는 UI
  - "태그 잠금을 잊지 마세요" 체크리스트
  - 다음 회차 태그 예상: 「ボカコレ2027冬TOP100参加」 형식(회차명 표기는 미확인)
- **(추정) 기본 태그 세트**: `VOCALOID`, `初音ミク`, `重音テトSV`(SV판) 또는 `重音テト`(UTAU판), `VOCALOIDオリジナル曲`, 엔진명(`SynthesizerV` 등). 태그는 니코니코 태그 수 한도 안에서 고른다.

### Gaps
- 미확인:
  - 니코니코 현행 권장 인코딩과 해상도
  - 최대 재생 시간
  - 태그 개수 한도(일반·프리미엄 회원별)
- 업로드 페이지 URL과, 쿼리 파라미터로 제목·태그를 미리 채울 수 있는지: 미확인. (추정: 불가)
- 「ボカロオリジナル曲」 태그의 사용 관례: 미확인. 확인된 것은 「VOCALOIDオリジナル曲」과 「重音テトSV」뿐이다.
- 최신 FAQ 원문으로 확인하지 못한 ボカコレ 항목:
  - 허용 엔진 범위(VOCALOID 외 UTAU, CeVIO, SynthV 허용 여부)
  - 최근 회차의 YouTube 동시 투고 허용 문구
  - 회차명 규칙
- ニコニ・コモンズ 親作品登録과 クリエイター奨励プログラム의 보카로 관례: 미조사(검색 예산 소진).

---

## 7. Bilibili 开放平台 — 제3자 투고(投稿) API 가용성과 요건

### Takeaway
Bilibili에는 공식 개방 플랫폼(openhome.bilibili.com)이 있다. 비디오 원고(视频稿件) 투고 흐름의 arcopen API와 데모 코드도 공개돼 있다(초기화 → 업로드/분할 업로드 → 완료 → 표지 업로드 → 원고 제출). 다만 권한(scope) 신청과 사용자 인가가 필요하고, 신청 자격(기업·개인 여부, 중국 법인 필요 여부)은 확인하지 못했다. 비공식 API 문서화 프로젝트는 2026-01에 Bilibili 측 법적 경고를 받고 중단됐다는 2차 보도가 있다.

### Cited Findings
- 개방 플랫폼은 Bilibili 콘텐츠 커뮤니티 생태계를 바탕으로 기관, UP주, 브랜드 서비스 제공자에게 기본 서비스 역량, 업종별 솔루션, 맞춤 서비스를 제공한다 — [平台简介 - 哔哩哔哩开放平台](https://openhome.bilibili.com/doc/4)
- 공식 데모 저장소에는 视频稿件投稿 흐름의 언어별 예제 코드가 있다. 개발자는 개방 플랫폼에서 인터페이스 권한과 기술 자원을 받아 원고 일괄 게시 같은 기능을 구현할 수 있다 — [bilibili-openplatform/demo](https://github.com/bilibili-openplatform/demo)
- arcopen API 구성: 업로드 초기화, 업로드 또는 분할 업로드, 완료, 표지 업로드, 원고 제출, 그리고 원고 목록 조회·편집 — [bilibili-openplatform/demo](https://github.com/bilibili-openplatform/demo) (검색 요약 기준)
- 사용자 영상 원고 목록을 조회하려면 권한 신청(Scope: `ARC_BASE`)과 사용자 인가가 필요하다 — [查询用户视频稿件列表](https://openhome.bilibili.com/doc/4/a24030b7-6b8f-b36c-32d8-a4aae67fcc35)
- 개발자 서비스 약관 — [哔哩哔哩开放平台开发者服务协议](https://openhome.bilibili.com/agreement/developer-service). 제휴 문의 메일은 openplatform-feedback@bilibili.com이다 — [bilibili-openplatform/demo](https://github.com/bilibili-openplatform/demo)
- 제3자 커넥터 프로젝트에 Bilibili 개방 플랫폼 provider를 추가한 사례가 있다 — [oomol-lab/open-connector PR #579](https://github.com/oomol-lab/open-connector/pull/579)
- 비공식 API 수집 프로젝트(bilibili-API-collect)는 2026-01-28 Bilibili가 위임한 법률사무소의 경고 서한을 받은 뒤 유지보수를 멈추고 문서와 소스를 삭제했다 — [CSDN 정리 글](https://adg.csdn.net/69707c57437a6b40336a746a.html) (2차. 원 공지는 미확인)

### Inferences
- **(추정) 우선순위**: 공식 경로는 있지만 진입 장벽이 높을 가능성이 크다(기업 자격, 심사, 중국어 운영·고객 대응). 1차 출시 범위에서는 후순위로 둔다.
- **(추정) 비공식 API 금지**: 비공식 API에는 실제 법적 리스크가 확인됐으므로 쓰지 않는다.
- **(추정) 단계별 접근**:
  - 단기: 니코니코와 같은 방식의 "투고 어시스트"(중국어 제목·简介·태그 템플릿과 파일 다운로드)를 제공한다.
  - 장기: 중화권 수요가 확인되면 개방 플랫폼의 투고 권한을 신청한다.
- **(추정) AI 표시 규정**: 중국의 AI 생성 콘텐츠 표시 규정이 적용될 수 있다. 투고 어시스트에 "AI 표시" 옵션을 두는 방안을 검토한다(규정 원문은 미조사).

### Gaps
- 미확인:
  - 투고용 scope 이름
  - 신청 자격(개인·기업, 중국 법인이나 ICP 필요 여부)
  - 심사 기간과 쿼터
  - 해외 기업의 신청 가능 여부
- Bilibili 업로드 사양(용량, 해상도, 길이)과 AI 콘텐츠 표시 정책: 미확인(검색 예산 소진).

---

## 8. Vocaloid 업로드 메타데이터 관례 (YouTube / Niconico) — 제목 형식, 크레딧 블록, 해시태그

### Takeaway
YouTube 공식 MV는 「ボカロP - 曲名 feat. 歌声」 형식이 흔하다(예: 「ピノキオピー - 超主人公 feat. 初音ミク」). 니코니코에서는 「【歌声】曲名【オリジナル曲】」 같은 대괄호 형식도 쓰인다(예: GYARI 「【重音テトSV】ドリル無双。…【オリジナル曲】」). Miku×Teto 듀엣은 가창자를 나란히 적는다(初音ミク・重音テトSV). 설명란 크레딧 블록(Music/Lyrics/Illust/Movie)의 실례는 검색 예산이 소진되어 수집하지 못했다. 아래 템플릿은 추정이다.

### Cited Findings
- **YouTube식 제목 실례**: 「ピノキオピー - 超主人公 feat. 初音ミク」(Bilibili 전재 페이지 제목에 그대로 남아 있음) — [bilibili av642081587](https://www.bilibili.com/video/av642081587)
- **니코니코식 대괄호 제목 실례**: GYARI 「【重音テトSV】ドリル無双。～異世界転生して神からドリルを授かった私は、…スローライフを送ります～※これは音楽動画です【オリジナル曲】」 — [Last.fm (GYARI)](https://www.last.fm/music/GYARI/_/%E3%80%90%E9%87%8D%E9%9F%B3%E3%83%86%E3%83%88SV%E3%80%91%E3%83%89%E3%83%AA%E3%83%AB%E7%84%A1%E5%8F%8C%E3%80%82%EF%BD%9E%E7%95%B0%E4%B8%96%E7%95%8C%E8%BB%A2%E7%94%9F%E3%81%97%E3%81%A6%E7%A5%9E%E3%81%8B%E3%82%89%E3%83%89%E3%83%AA%E3%83%AB%E3%82%92%E6%8E%88%E3%81%8B%E3%81%A3%E3%81%9F%E7%A7%81%E3%81%AF%E3%80%81%E7%8E%8B%E5%A5%B3%E3%82%92%E5%8A%A9%E3%81%91%E3%81%9F%E3%82%8A%E3%83%8B%E3%82%BB%E5%8B%87%E8%80%85%E3%82%92%E8%BF%BD%E6%94%BE%E3%81%97%E3%81%9F%E3%82%8A%E9%AD%94%E7%8E%8B%E3%82%92%E8%A8%8E%E4%BC%90%E3%81%97%E3%81%A6%E6%9C%80%E5%BC%B7%E3%81%AB%E3%81%AA%E3%81%A3%E3%81%9F%E3%81%AE%E3%81%A7%E3%82%B9%E3%83%AD%E3%83%BC%E3%83%A9%E3%82%A4%E3%83%95%E3%82%92%E9%80%81%E3%82%8A%E3%81%BE%E3%81%99%EF%BD%9E%E2%80%BB%E3%81%93%E3%82%8C%E3%81%AF%E9%9F%B3%E6%A5%BD%E5%8B%95%E7%94%BB%E3%81%A7%E3%81%99%E3%80%90%E3%82%AA%E3%83%AA%E3%82%B8%E3%83%8A%E3%83%AB%E6%9B%B2%E3%80%91/+albums)
- **Miku×Teto 듀엣 실례**:
  - サツキ 「メズマライザー」(初音ミク・重音テトSV): 2024년 히트곡으로, 보카로곡으로서는 기록적인 속도로 1억 회 재생을 넘었다 — [KAI-YOU](https://kai-you.net/article/89581); [電ファミニコゲーマー](https://news.denfaminicogamer.jp/news/2504012j) (검색 요약 기준. YouTube 제목 원문은 미확인)
  - 原口沙輔 「ノシノシ」(初音ミク・重音テトSV, 2024-09-14) — [ニコニコ大百科](https://dic.nicovideo.jp/v/sm44102888)
  - かてらざわ 「重音テトはこんなパーティ二人で抜け出せるのか ～Everlasting Edition～」(重音テトSV×初音ミク): 2025년 곡을 약 3분으로 재편곡해 2026-08 **"YouTube Music Weekend 12.0"** 참가곡으로 냈다. YouTube 쪽에도 보카로 이벤트가 있다는 근거다 — [RAG MUSIC](https://www.ragnet.co.jp/vocaloid-new-songs?disp=more)

### Inferences
- **(추정) 제목 템플릿**(사용자가 고르게 하는 프리셋):
  - YouTube: `{ボカロP} - {曲名} feat. 初音ミク・重音テトSV` 또는 `{曲名} / {ボカロP} feat. 初音ミク・重音テト`
  - Niconico: `{曲名} / 初音ミク・重音テトSV` 또는 `【初音ミク・重音テトSV】{曲名}【オリジナル曲】`
  - Shorts·TikTok·Reels 캡션: `{曲名} / {ボカロP} #初音ミク #重音テト #ボカロ #VOCALOID` (Shorts는 3분 이하·세로면 자동 분류되므로 `#Shorts` 해시태그는 필수가 아니라고 추정)
- **(추정) 크레딧 블록 템플릿**(업계 관례에 기반한 추정. 실례는 수집하지 못함):
  ```
  Music & Lyrics: {ボカロP}
  Illust: {絵師}
  Movie: {動画師}
  Vocal: 初音ミク / 重音テトSV
  ---
  Made with {플랫폼명} ({작품 URL})
  ```
- **(추정) 엔진 표기**: SV판 테토는 「重音テトSV」, UTAU판은 「重音テト」로 구분해 적는다. 니코니코 대백과 분류에서 이 구분이 쓰인다(§6 인용).
- **(추정) 이벤트 태그 프리셋**: ボカコレ 같은 니코니코 이벤트뿐 아니라 YouTube Music Weekend 같은 YouTube 측 보카로 이벤트도 있으므로, 이벤트 태그·해시태그 프리셋 기능은 가치가 있다.

### Gaps
- 실제 공식 MV 설명란의 크레딧 블록 실례와 YouTube 해시태그 관례(첫 해시태그 3개가 제목 위에 표시되는 동작 등): 미확인(검색 예산 소진).
- 「メズマライザー」 등의 YouTube·니코니코 제목 원문 표기: 미확인.
- YouTube Music Weekend의 주최와 참가 방식: 미확인.

---

## 9. 크로스포스팅 선례 — CapCut→TikTok, Canva→YouTube/TikTok, Buffer/Later/Restream, 통합 게시 API

### Takeaway
CapCut, Canva, Buffer, Later, Restream의 구체적인 구현(직접 게시인지 초안·알림 게시인지, 감사를 어떻게 처리했는지)은 검색 예산이 소진되어 확인하지 못했다. 확인된 선례는 **감사를 마친 통합 소셜 게시 API 사업자**(Ayrshare, Late/Zernio, bundle.social, upload-post, Blotato 등)와 자체 앱 방식의 오픈소스 스케줄러(Postiz)다. TikTok Content Posting API 자체가 "당신의 플랫폼에서 만든 콘텐츠를 공유"하는 용도로 설계됐다.

### Cited Findings
- TikTok Content Posting API의 목적은 "provide creators with the capabilities they need to share content created in your platform"이다 — [TikTok Content Posting API](https://developers.tiktok.com/products/content-posting-api)
- 통합 API 사업자들이 TikTok 연동을 "Minutes, Not Months" 같은 문구로 판매한다. 즉, 자체 감사 없이 사업자의 감사된 앱을 쓰는 모델이다 — [Late](https://getlate.dev/tiktok); [Zernio TikTok API](https://zernio.com/tiktok-api); [bundle.social](https://bundle.social/tiktok-content-posting-api); [Blotato TikTok API](https://www.blotato.com/api/tiktok)
- YouTube 미검증 앱 403 오류와 감사 파이프라인 문제를 다루는 사업자 문서가 있고, YouTube도 크리에이터에게 "공식 또는 감사받은 서비스를 쓰면 제한을 피할 수 있다"고 안내한다 — [Ayrshare](https://www.ayrshare.com/solutions/google-api-error-403-unverified-app-how-to-fix-the-audit-pipeline/); [Ayrshare YouTube API docs](https://www.ayrshare.com/docs/apis/post/social-networks/youtube); [upload-post YouTube guide](https://www.upload-post.com/youtube-api/)
- 자체 개발자 앱으로 연결하는 오픈소스 스케줄러(Postiz)도 TikTok 감사 전 비공개 제한을 문서에 적어 두었다 — [Postiz docs](https://docs.postiz.com/providers/tiktok.md)

### Inferences
- **(추정) 두 가지 전략**:
  - **(A) 자체 감사**: 장기적으로 API 비용이 거의 없고 UX·브랜딩을 직접 통제할 수 있다. 대신 Google 검증과 YouTube·TikTok 감사·Meta App Review에 수주에서 수개월이 걸린다.
  - **(B) 초기에는 통합 게시 API 사업자 사용**: 즉시 공개 게시가 가능하다. 대신 건당·월 과금이 붙고, 사용자 토큰을 제3자가 보관하므로 개인정보 처리 위탁 고지와 약관 검토가 필요하다.
  - **하이브리드**: 감사가 진행되는 동안 B로 운영하고, 감사가 끝나면 A로 옮긴다. 사용자는 다시 연동해야 한다.
- **(추정) CapCut 선례의 한계**: CapCut→TikTok은 같은 ByteDance 계열의 1차 연동이라 일반 제3자의 선례로는 적합하지 않다.

### Gaps
- CapCut, Canva, Buffer, Later, Restream의 TikTok·YouTube·Instagram 연동 방식(Direct Post인지 초안·알림 게시인지)과 감사·비공개 제약 처리 사례: 검색 예산 소진으로 미확인.
- 통합 API 사업자를 쓸 때 YouTube API 약관상 우리 서비스가 "API Client"로 취급되는지, 그 해석: 미확인.

---

## 10. 종합 — 플랫폼별 "공개 업로드" 조건·쿼터·비용 매트릭스와 권장 익스포트 프리셋

### Takeaway
"버튼 하나로 공개 게시"를 실서비스로 열 수 있는 곳은 YouTube, TikTok, Instagram, X 네 곳이다. 각각 출시 전 승인이 필요하다.
- **YouTube**: OAuth 민감 범위 검증과 API 감사(감사 전 private 잠금)
- **TikTok**: 앱 승인과 Direct Post 감사(감사 전 `SELF_ONLY`)
- **Instagram**: Meta App Review
- **X**: 승인은 가볍지만 게시 건당 비용이 든다($0.015, URL 포함 시 $0.20)

Niconico는 공식 API가 없고, Bilibili는 공식 경로가 있지만 요건이 확인되지 않아 두 곳 모두 "투고 어시스트"로 대응하는 것이 현실적이다. 렌더러는 ① 가로 16:9 마스터, ② 세로 9:16 숏폼 공통, ③ X 보수형의 세 가지 프리셋이면 대부분의 사양을 맞출 수 있다.

### Cited Findings
| 플랫폼 | 공식 제3자 업로드 API | 공개 게시 전 필요한 승인 | 승인 전 동작 | 핵심 쿼터·레이트 | 비용 |
|---|---|---|---|---|---|
| YouTube(일반·Shorts) | 있음: `videos.insert`(resumable) — [Resumable Uploads](https://developers.google.com/youtube/v3/guides/using_resumable_upload_protocol) | `youtube.upload` 민감 범위 검증 — [FAQ](https://support.google.com/cloud/answer/13463817?hl=en); API 컴플라이언스 감사 — [Videos: insert](https://developers.google.com/youtube/v3/docs/videos/insert) | 2020-07-28 이후 생성된 프로젝트는 private 잠금; 미검증 OAuth 앱은 신규 사용자 100명 상한 — [Unverified apps](https://support.google.com/cloud/answer/7454865?hl=en) | Video Uploads 버킷 하루 100 calls/프로젝트(1 unit/call, 2026-06-01~); 그 외 하루 10,000 units; `thumbnails.set` ~50, `captions.insert` 400 — [Quota Calculator](https://developers.google.com/youtube/v3/determine_quota_cost) | 쿼터 기반(요금 없음 — 추정) |
| TikTok | 있음: Direct Post / Upload(초안) — [Content Posting API](https://developers.tiktok.com/products/content-posting-api) | 앱 승인과 Direct Post 감사 — [Get started](https://developers.tiktok.com/doc/content-posting-api-get-started) | `SELF_ONLY` 강제, 24시간 5명, 비공개 계정만 — [Direct Post ref](https://developers.tiktok.com/docs/en/content-posting-api-reference-direct-post) | init 분당 6회/토큰, `creator_info` 분당 20회, 크리에이터당 하루 ~15건(2차), 최대 4GB·청크 5–64MB — [Media Transfer Guide](https://developers.tiktok.com/docs/en/content-posting-api-media-transfer-guide) | 요금 없음(추정) |
| Instagram Reels | 있음: `/media` → `/media_publish` — [IG User Media](https://developers.facebook.com/docs/instagram-platform/instagram-graph-api/reference/ig-user/media/) | App Review(Advanced Access, 2차) — [PostProxy](https://postproxy.dev/blog/post-to-instagram-via-api/) | 미확인 | 계정당 24시간 100건 — [Content Publishing](https://developers.facebook.com/docs/instagram-platform/content-publishing/) | 요금 없음(추정) |
| X | 있음: v2 청크 미디어 업로드와 `POST /2/tweets` — [Chunked Media Upload](https://docs.x.com/x-api/media/quickstart/media-upload-chunked) | 개발자 계정과 선불 크레딧 — [Usage and Billing](https://docs.x.com/x-api/fundamentals/post-cap) | 크레딧이 없으면 요청 실패 | 월 Post read 200만 건; 쓰기 레이트는 2차 정보 | 게시 $0.015, URL 포함 $0.20 — [Pricing Update 2026-04-20](https://devcommunity.x.com/t/x-api-pricing-update-owned-reads-now-0-001-other-changes-effective-april-20-2026/263025) |
| Niconico | 확인된 공식 API 없음(외부 API 2023년 종료) — [ニコニコインフォ](https://blog.nicovideo.jp/niconews/182541.html) | 해당 없음 | 해당 없음 | 1개당 6GB — [ニコニコインフォ](https://blog.nicovideo.jp/niconews/233213.html) | 해당 없음 |
| Bilibili | 있음(개방 플랫폼 arcopen, 권한 신청 필요) — [demo](https://github.com/bilibili-openplatform/demo) | 권한(scope) 신청과 사용자 인가 — [openhome doc](https://openhome.bilibili.com/doc/4/a24030b7-6b8f-b36c-32d8-a4aae67fcc35) | 미확인 | 미확인 | 미확인 |

### Inferences
- **(추정) 권장 익스포트 프리셋**: 출처의 사양을 조합한 것이다. 라우드니스 값은 공식 근거가 없어 업계 관행을 따랐다.

  | 프리셋 | 해상도·비율 | fps | 비디오 | 오디오 | 길이·용량 가드 | 주 용도 |
  |---|---|---|---|---|---|---|
  | **P1 가로 MV 마스터** | 1920×1080 16:9(옵션 3840×2160) | 원본 유지(24/30/60) | MP4(faststart, Edit List 없음), H.264 High, progressive, closed GOP, CABAC, 4:2:0, VBR, B프레임 2개. 1080p30 ≥8Mbps / 1080p60 ≥12Mbps, 4K30 35–45 / 4K60 53–68Mbps | AAC-LC 48kHz 스테레오(비트레이트는 미확인. 추정 320–384kbps) | 6GB 이하(니코니코 상한) | YouTube 본편, Niconico 수동 업로드 |
  | **P2 세로 숏폼 공통** | 1080×1920 9:16 | 30(또는 60) | H.264 High(HEVC는 Reels만 확실), VBR 25Mbps 이하(Reels 상한), 목표 8–12Mbps | AAC-LC 48kHz 스테레오 128kbps 이상 | **3:00 이하**(Shorts), 커버·claim 위험이 있으면 **1:00 이하**, 300MB 이하(Reels), TikTok은 `max_video_post_duration_sec` 준수 | YouTube Shorts, TikTok, Instagram Reels |
  | **P3 X 보수형** | 1280×720 / 720×1280 / 720×720 | 30 또는 60 | H.264 High, 512MB 이하 | AAC-LC(HE-AAC 불가) 128kbps 이상, 모노·스테레오 | **140초 이하**(상충이 풀릴 때까지) | X |

  - 라우드니스 목표(추정): 통합 -14 LUFS 내외, True Peak -1 dBTP. 플랫폼 정규화를 전제로 하며, 공식 근거는 미확인이다.
  - 썸네일(추정): JPEG/PNG 2MB 이하(`thumbnails.set` 상한). 1280×720 정도를 권장하지만 이 해상도는 미확인이다.
- **(추정) 출시 순서**:
  1. **YouTube**: 일본 보카로 청취층의 핵심 무대다. Google 검증과 감사를 가장 먼저 신청한다(신청 → 승인까지 수주).
  2. **TikTok**: 출시 시점에는 Upload(초안) 경로로 시작하고, 감사가 끝나면 Direct Post를 연다.
  3. **Niconico 투고 어시스트**: API가 필요 없어 개발 비용이 낮고, ボカコレ 시즌(다음 회차 2027-02-19~23)에 맞추면 효과가 크다.
  4. **Instagram Reels**: App Review를 받은 뒤 연다.
  5. **X**: 비용 게이팅을 먼저 설계한다.
  6. **Bilibili**: 수요가 확인되면 진행한다.
- **(추정) 공통 아키텍처**:
  - 렌더가 끝난 파일을 기준으로 플랫폼별 "게시 작업(job)"을 큐에 넣는다.
  - 플랫폼마다 레이트 리밋과 쿼터 카운터를 따로 관리한다(YouTube는 프로젝트 단위 하루 100건, TikTok은 사용자당 분당 6회 init).
  - 상태 머신으로 처리 상태를 추적한다(업로드 중 → 처리 중 → 게시됨/실패/잠김(private)).
  - 게시 결과(영상 ID와 URL)를 커뮤니티 작품 페이지에 연결한다.
- **(추정) 공통 UX 원칙**(YouTube RMF와 TikTok 가이드라인의 공통분모):
  - 공개 범위는 사용자가 명시적으로 고른다.
  - 상호작용·상업 콘텐츠 설정은 기본값이 off다.
  - AI·합성 콘텐츠 공개 토글을 둔다.
  - 게시 전 미리보기를 보여준다.
  - 플랫폼 약관 동의 문구를 표시한다.

### Gaps
- 승인 소요 기간(YouTube 감사, TikTok 감사, Meta App Review)과 거절 사유 사례: 미확인.
- 각 플랫폼 API의 실제 요금 여부(YouTube, TikTok, Instagram은 무료로 알려져 있음): 공식 문서로 확인하지 못했다 — 미확인.
- 통합 API 사업자의 단가: 미조사.
- 이번 조사는 WebSearch 예산 소진으로 일찍 끝났다. 추가 검증이 필요하면 후속 요청으로 이어서 검색해야 한다. 우선순위가 높은 항목:
  - captions.insert scope
  - TikTok 공식 영상 사양
  - YouTube 라우드니스와 오디오 비트레이트
  - 크레딧 블록 실례
  - CapCut·Canva·Buffer 사례
  - Bilibili 투고 자격
