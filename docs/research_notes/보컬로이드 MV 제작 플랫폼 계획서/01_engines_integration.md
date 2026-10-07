# 가창 합성(SVS) 엔진·보이스뱅크·프로젝트 포맷과 웹 플랫폼 통합 옵션 — 하쓰네 미쿠 / 카사네 테토 중심

> 조사 기준일 2026-10-07. 방법은 세 가지다. (1) WebSearch(일본어·영어) 결과 스니펫. (2) GitHub 공개 저장소를 git으로 직접 열람: stakira/OpenUtau(+wiki), openvpi/DiffSinger, openvpi/vocoders, sdercolin/utaformatix3, SoulMelody/LibreSVIP, VOICEVOX/voicevox_engine, Dreamtonics/svstudio-scripts, nnsvs/nnsvs. (3) npm 레지스트리 조회. WebFetch는 egress 정책으로 막혀 있다(kasaneteto.jp 시도 실패, dreamtonics.com 등은 사전 고지된 차단 대상). **이 세션의 웹 검색 할당량이 조사 중간에 소진되어** NEUTRINO·VoiSona·ACE Studio API·Yamaha Music Connect API·UTAU 테토 세부 규약·NT/V6 EULA의 AI 학습 조항은 검증하지 못했다(각 Gaps 참조). 법률 자문이 아니라 1차 문서를 요약한 것이다. "추정"은 근거로부터의 추론이고, "미확인"은 출처로 확인하지 못한 항목이다.

## 1. 하쓰네 미쿠 제품 라인업(2026년 10월 기준): VOCALOID 계열 / Crypton 자체 엔진 NT 계열, 출시일·가격·플러그인·내보내기

### Takeaway
2026년 10월 현재 미쿠는 세 계통이 병존한다. ① VOCALOID4 기반 V4X(구형). ② Crypton 자체 엔진 「初音ミク NT (Ver.2)」+Piapro Studio NT2(2025-03-18 정식판, ¥19,800, VST3/AU+스탠드얼론). ③ **2026-04-14 출시된 VOCALOID6(VOCALOID:AI) 기반 「初音ミク V6」**(스타터팩 ¥24,200 / 보이스뱅크 ¥10,780, ARA2 지원 VOCALOID6 Editor 사용). 셋 다 데스크톱/플러그인 제품이다. 산출물은 WAV, 각자의 프로젝트 파일(.ppsf / .vpr / .vsqx), 일부 MIDI다.

### Cited Findings
#### 1-A. 初音ミク NT (Ver.2) / Piapro Studio NT2 (Crypton 자체 엔진)
- 기존 NT 라이선스 보유자에게 Early Access 버전을 2024-10-23 무료로 공개했고, "정규판은 2025년 전반" 출시를 예고했다 — [Crypton 뉴스 2024-10-23](https://www.crypton.co.jp/cfm/news/2024/10/23mikunt2ea), [piapro blog](https://blog.piapro.net/2024/10/z2410251.html)
- 정식판(메이저 업데이트) 릴리스일은 2025-03-18(화) — [SONICWIRE 공지](https://sonicwire.com/news/blog/2025/03/nt-ver-2-2025-3-18), [SONICWIRE 「正式リリースいたしました」](https://sonicwire.com/news/blog/2025/03/nt-ver-2), [piapro blog 2025-03](https://blog.piapro.net/2025/03/ms2503191.html)
- 새 합성 엔진(내부 개발 코드 "M9")을 채택했고, Piapro Studio NT2용으로 조정된 보이스 라이브러리 3종(Original / Dark / Whisper)을 제공한다 — [Crypton 뉴스](https://www.crypton.co.jp/cfm/news/2024/10/23mikunt2ea), [piapro wiki(팬 위키)](https://piapro.fandom.com/wiki/Hatsune_Miku_NT_(Ver.2)) (검색 요약 기준)
- 「Automatic Control」 기능: '歌い方(가창 스타일)'와 'ニュアンス' 프리셋을 고르면 피치 커브·비브라토·음색 등 여러 파라미터가 자동으로 조정된다 — [SONICWIRE 변경점 정리](https://sonicwire.com/news/blog/2025/04/hatsune-miku-nt-ver2-difference), [xexeq 기사](https://xexeq.jp/blogs/media/topics15109)
- 「Dark」 라이브러리는 Piapro Studio NT2 v0.9.4.0(2025-02)에서 추가됐다 — [SONICWIRE 2025-02](https://sonicwire.com/news/blog/2025/02/piapro-studio-nt2-v-0-9-4-0)
- v1.0.1.0 업데이트(2025-05)로 합성 엔진 음질이 개선됐다("クリアで曇りの少ない") — [SONICWIRE 2025-05](https://sonicwire.com/news/blog/2025/05/piapro-studio-nt2-1-0-1-0)
- 가격은 세금 포함 ¥19,800이고, 기존 NT 사용자는 Ver.2를 무상으로 이용한다 — [SONICWIRE 初音ミク NT 특설 페이지](https://sonicwire.com/product/virtualsinger/special/mikunt)
- Piapro Studio NT2는 플러그인(VST3, Audio Units)과 스탠드얼론으로 동작한다 — [SONICWIRE 初音ミク NT](https://sonicwire.com/product/virtualsinger/special/mikunt) (검색 요약 기준)
- DTM Station 기사 제목은 「初音ミク NTバージョン2がリリース。複数のエンジンでクリエイターの思いを存分に反映できる環境を」이다(복수 엔진 구성이 시사되나 세부는 미확인) — [DTM Station](https://www.dtmstation.com/archives/70423.html)
- 2026-04 SONICWIRE 입문 강좌 「CubaseでPiapro Studioを使う方法」: 반주와 합치는 작업을 DAW(Cubase) 안의 플러그인으로 하는 흐름을 안내한다 — [SONICWIRE 2026-04](https://sonicwire.com/news/blog/2026/04/07-start-nt)

#### 1-B. 初音ミク V6 (VOCALOID6 / VOCALOID:AI 기반, Crypton 판매)
- 2026-02-18 발매일(2026-04-14) 발표와 함께 예약을 시작했고, 2026-04-14 정식 출시했다 — [Crypton 뉴스 2026-02-18](https://www.crypton.co.jp/cfm/news/2026/02/18miku_v6), [Crypton 뉴스 2026-04-14](https://www.crypton.co.jp/cfm/news/2026/04/14miku_v6), [gamebiz](https://gamebiz.jp/news/424373)
- 보이스뱅크는 2종이다. 「Original」은 "ミクらしさ"를 잇는 큐트하고 솔직한 음색, 「Soft」는 숨이 섞인 꿈결 같은 음색. 둘 다 VOCALOID:AI 엔진에 최적화됐다 — [시마무라 악기 신제품 안내](https://info.shimamura.co.jp/digital/newitem/2026/02/164674), [PR TIMES](https://prtimes.jp/main/html/rd/p/000000647.000052709.html)
- 대응 언어는 출처마다 다르다. "日本語・英語・中国語" — [gamebiz](https://gamebiz.jp/news/424373), [Famitsu](https://www.famitsu.com/article/202604/71865) / "日本語・英語" — [시마무라(2026-02, 발매 전)](https://info.shimamura.co.jp/digital/newitem/2026/02/164674) **(출처 간 불일치)**
- 가사와 멜로디만 입력해도 자연스러운 숨소리가 자동으로 붙고, 여러 언어를 섞은 유창한 가창이 가능하다 — [Famitsu](https://www.famitsu.com/article/202604/71865)
- 가격: 「VOCALOID6 Editor」를 동봉한 〈스타터팩〉 ¥24,200(세금 포함), 에디터 보유자용 〈보이스뱅크〉 ¥10,780 — [gamebiz](https://gamebiz.jp/news/421198), [Crypton EC(보이스뱅크)](https://ec.crypton.co.jp/pages/prod/virtualsinger/mikuv6), [Crypton EC(스타터)](https://ec.crypton.co.jp/pages/prod/virtualsinger/mikuv6_starter)
- 보이스뱅크 제품에는 기능 제한판 「VOCALOID6 Editor Lite」와 Cubase LE가 딸려 있다. VOCALOID6 Editor는 스탠드얼론 또는 VST/AU 플러그인으로 동작하고, ARA2를 지원하며, VOCALOID3/4/5 보이스뱅크도 쓸 수 있다 — [시마무라](https://info.shimamura.co.jp/digital/newitem/2026/02/164674)
- Yamaha 공식 스토어에도 「VOCALOID6 Voicebank 初音ミク V6」 「VOCALOID6 Starter Pack 初音ミク V6」로 등록돼 있다 — [vocaloid.com 보이스뱅크](https://www.vocaloid.com/products/show/v6vb_hatsune_miku_v6_c), [vocaloid.com 스타터팩](https://www.vocaloid.com/products/show/v6std_44210)
- 키 비주얼은 LAM, 영어 가창 데모를 공개했다(2026-03-09) — [Crypton 뉴스 2026-03-09](https://www.crypton.co.jp/cfm/news/2026/03/09miku_v6)
- Crypton×Yamaha 공동 개발 비화 인터뷰가 있다 — [DTM Station](https://www.dtmstation.com/archives/78243.html), 리뷰 — [サンレコ](https://www.snrec.jp/entry/product/crypton-future-media_hatsune-miku-v6)

#### 1-C. V4X(구형, VOCALOID4 계열 — 엔진 세대는 일반 지식이며 본 조사에서 1차 출처 미확인)
- 「初音ミク V4X」 제품 페이지와 EULA PDF는 계속 공개돼 있다 — [Crypton EC V4X](https://ec.crypton.co.jp/pages/prod/vocaloid/mikuv4x), [V4X EULA PDF](https://ec.crypton.co.jp/download/pdf/eula_MIKUV4X.pdf)

#### 1-D. 내보내기·가져오기와 프로젝트 포맷
- Piapro Studio: 자체 형식(.ppsf) 읽기/쓰기와 WAV 내보내기를 지원한다. VSQ/VSQX/MIDI는 "템포·박자"만 읽고, 멜로디는 MIDI로 내보낼 수 있다 — [Crypton Piapro Studio 페이지](https://ec.crypton.co.jp/pages/prod/virtualsinger/piaprostudio), [piaprostudio.com](https://piaprostudio.com/?page_id=28&lang=en) (검색 요약 기준. **NT2 기준 정보인지 구 Piapro Studio 기준인지 불명확**)
- NT판 .ppsf는 "json 기반 + zip 압축"이고, 구판(VOCALOID용 Piapro Studio) .ppsf는 커스텀 바이너리다 — [LibreSVIP project_formats.md](https://github.com/SoulMelody/LibreSVIP/blob/HEAD/docs/project_formats.md)
- NT .ppsf 내부 `ppsf.json`의 노트 이벤트에는 `pos`, `length`, `lyric`, `note_number`, `symbols`(음소 기호) 필드가 있다. UtaFormatix는 구판 ppsf를 지원하지 않는다(UnsupportedLegacyPpsfError) — [UtaFormatix3 Ppsf.kt](https://github.com/sdercolin/utaformatix3/blob/HEAD/core/src/main/kotlin/core/io/Ppsf.kt)
- V6(VOCALOID6 Editor)의 프로젝트는 .vpr(VOCALOID5+ 형식, zip+json)이다 — [LibreSVIP project_formats.md](https://github.com/SoulMelody/LibreSVIP/blob/HEAD/docs/project_formats.md) (.vpr 상세는 2장)

### Inferences
- 플랫폼에 들어올 미쿠 프로젝트 파일은 .ppsf(NT 계열), .vpr(V6 및 VOCALOID5/6 사용자), .vsqx(V4X/VOCALOID4) 세 종류가 주력일 가능성이 높다. 파서 우선순위는 .vpr > .ppsf(NT) > .vsqx로 보인다(추정).
- 세 계통 모두 로컬 설치형 에디터 또는 DAW 플러그인이다. 공개된 웹·클라우드 API나 SDK는 검색 범위에서 발견되지 않았다. 웹 플랫폼 안에서 "미쿠 목소리로 직접 렌더링"하려면 B2B 계약 없이는 불가능하다고 봐야 한다(추정, 2·5장 참조).
- V6가 Yamaha VOCALOID6 Editor를 쓰므로 V6 사용자는 ARA2로 DAW 안에서 작업할 수 있다. NT2 사용자의 ARA 지원 여부는 확인하지 못했다.

### Gaps
- Piapro Studio NT2의 ARA(ARA2) 지원 여부, MusicXML 가져오기/내보내기, 트랙별 스템 내보내기 사양: 미확인(웹 검색 할당량 소진).
- V6 발매 후 NT(Ver.2)의 판매 지속 여부와 현재 가격, V4X 판매 상태: 미확인.
- V6의 중국어 지원 여부: 출처 간 불일치, 미해결.
- NT/V6 EULA 원문(특히 출력 음성의 AI 학습 이용 조항): 미열람.
- 2026년 10월 현재 "NT Ver.3"이나 V6 이후 신세대 제품: 검색에서 발견되지 않음(부재 증명은 아님).

## 2. Yamaha VOCALOID6(및 후속): 기능, .vpr 형식, 서드파티 앱/서비스용 SDK·API·파트너 라이선스

### Takeaway
VOCALOID6(2022, VOCALOID:AI)이 2026년 10월에도 Yamaha의 현행 세대다. Editor는 2026-08까지 6.13.x로 업데이트됐고 ARA2를 지원한다. .vpr은 "zip 안의 JSON"이라 파싱이 쉽다. 반면 현재 공개된 셀프서브 SDK나 웹 API는 확인되지 않았고 법인 문의 창구만 있다. 과거에는 VOCALOID SDK for Unity(2015), 서버형 NetVOCALOID(Web API), VOCALOID-flex API 같은 임베딩 선례가 있었다.

### Cited Findings
#### 2-A. 기능·버전
- VOCALOID6은 시리즈 최초로 머신러닝("VOCALOID:AI")을 도입했다 — [Wikipedia: Vocaloid 6](https://en.wikipedia.org/wiki/Vocaloid_6), [Yamaha PR TIMES(발매 보도자료)](https://prtimes.jp/main/html/rd/p/000000647.000010701.html)
- 2022-10-20 기사가 VOCALOID6을 "4年ぶり更新"으로 보도했다(기사 시점상 2022년 10월 발매로 판단) — [techno-edge](https://www.techno-edge.net/article/2022/10/20/399.html), [PANORA](https://panora.tokyo/archives/54974)
- VOCALOID6은 ARA2를 지원해 DAW와의 연동이 향상됐고, DAW별 설정 해설이 있다 — [Yamaha 공식 X](https://x.com/vocaloid_yamaha/status/1610954393448288257), [Cubase 설정 가이드](https://www.vocaloid.com/en/learn/ln6202/)
- 알려진 문제: Cubase 15.0.20 이상에서 VOCALOID6 Editor의 ARA2 기능을 쓸 수 없는 불량이 공지됐다 — [vocaloid.com 뉴스](https://www.vocaloid.com/news/news_36/)
- Editor 업데이트 이력: 6.12.0(2026-04-02), 6.13.0(2026-06-23), 6.13.1(2026-08-03), 6.13.2(2026-08-26). 최신 6.13.3은 보안 업데이트와 .vsqx·프로젝트 파일 로딩 수정을 포함한다 — [vocaloid.com 업데이트 공지](https://www.vocaloid.com/en/news/support_64/), [vocaloid.com UPDATE 카테고리](https://www.vocaloid.com/news/category/support/) (검색 요약 기준)
- VOCALO CHANGER(PLUGIN): Ver.1.2.2(2025-01) 이후 1.2.3 이상이 배포 중이다 — [vocaloid.com VOCALO CHANGER FAQ](https://www.vocaloid.com/support/faq/category/29)
- 라이선스는 1대의 컴퓨터에만 설치·사용할 수 있고, 복수 PC 사용·양도·중고 판매는 불가하다 — [vocaloid.com 비즈니스](https://www.vocaloid.com/business/), [설치 매뉴얼 PDF](https://rsc-net.vocaloid.com/assets/pdf_files/bb/VOCALOID_Install_Manual_JPN.pdf) (검색 요약. 원문 위치는 미특정)

#### 2-B. .vpr 프로젝트 형식
- .vpr = zip 아카이브 안의 `Project/sequence.json`(경로 구분자 `\`/`/` 둘 다 대응) — [UtaFormatix3 Vpr.kt](https://github.com/sdercolin/utaformatix3/blob/HEAD/core/src/main/kotlin/core/io/Vpr.kt), "Vocaloid 5+ / json 기반, zip 압축" — [LibreSVIP project_formats.md](https://github.com/SoulMelody/LibreSVIP/blob/HEAD/docs/project_formats.md)
- 최상위 필드는 `masterTrack`(tempo, timeSig), `tracks`, `voices`, `version`, `vender`, `title`이다. 파트(part)는 `pos`, `duration`, `notes`, `styleName`, `voice`, `controllers`를 가진다. 노트(note)는 `pos`, `duration`, `number`(MIDI 음높이), `lyric`, `phoneme`, `velocity`, `exp`, `singingSkill`, `vibrato`, `isProtected`를 가진다 — [UtaFormatix3 Vpr.kt](https://github.com/sdercolin/utaformatix3/blob/HEAD/core/src/main/kotlin/core/io/Vpr.kt)
- VOCALOID:AI 관련 필드로 노트에 `aiExp`, `isAiVibratoEnabled`, `langID`, `phonemePositions`(pos 리스트)가 있고, 파트에 `aiVoice`(compID, langIDs), `stylePresetID`가 있다 — [LibreSVIP vpr/model.py](https://github.com/SoulMelody/LibreSVIP/blob/HEAD/libresvip/plugins/vpr/model.py)
- Yamaha 계열 신형 포맷으로 `vxf`("VOCALOID β-STUDIO", MIDI 2.0 클립(SMF2CLIP) 기반 바이너리)가 컨버터 문서에 등재돼 있다 — [LibreSVIP project_formats.md](https://github.com/SoulMelody/LibreSVIP/blob/HEAD/docs/project_formats.md) (제품 자체의 성격과 출시 상태는 미확인). 관련 가능성이 있는 기사 제목: 「ヤマハ、｢ボーカロイド｣に試作技術実装 制作スムーズに」(2024) — [日本経済新聞](https://www.nikkei.com/article/DGXZQOCC197IQ0Z10C24A7000000/)

#### 2-C. SDK·API·법인 라이선스(현재와 과거)
- 현재 vocaloid.com의 법인 안내: VOCALOID6·VOCALO CHANGER PLUGIN 볼륨 디스카운트, 오리지널 보이스뱅크 제작 수탁(녹음~완성) — [vocaloid.com/business](https://www.vocaloid.com/business/)
- 캐릭터 IP의 보이스뱅크화를 지원하는 크라우드펀딩 서비스 「VOCALOID FAN-ding」이 공개됐다 — [vocaloid.com 뉴스](https://www.vocaloid.com/news/news_30/)
- Yamaha는 VOCALOID 기술의 원천 보유자로서 Crypton 등에 기술 라이선스를 제공한다 — [일본변리사회 사례 페이지](https://www.jpaa.or.jp/case/vocaloid/)
- Yamaha 보이스뱅크로 만든 오리지널 곡의 상용 이용에는 원칙적으로 특별한 허가나 사용료가 필요 없다 — [vocaloid.com FAQ 703](https://www.vocaloid.com/support/faq/703)
- (2015) 「VOCALOID SDK for Unity」: Yamaha와 Unity Technologies Japan이 공동 개발했다. 2015-08 발표, 2015-12 제공. 「Unity 런타임판 VOCALOID Library unity-chan!」을 동봉했고, 유니티짱 라이선스를 지키면 무료다. Windows/Mac OS X/iOS를 지원했다 — [gamebiz](https://gamebiz.jp/news/154379), [GameBusiness 2015-08-28](https://www.gamebusiness.jp/article/2015/08/28/11333.html), [CGWORLD](https://cgworld.jp/news/tool/1508-unityvocaloid12.html)
- (과거) NetVOCALOID: 서버에 VOCALOID를 탑재해 가창 합성 기능을 Web API로 사업자에게 제공했고, 무거운 합성 처리는 Yamaha 서버가 맡았다 — [vocaloid-fan.com NetVOCALOID](https://vocaloid-fan.com/product/netvocaloid.html) (현재 제공 여부는 미확인)
- (과거) VOCALOID-flex API: 가사·피치·음소 길이를 VSXML(Voice Synthesis XML)로 보내면 합성음을 MP3로 돌려줬다 — [DTM Station](https://www.dtmstation.com/archives/51495567.html)
- (2014) 회원제 클라우드 서비스 「ボカロネット」: 자동 작곡(VOCALODUCER), 클라우드 스토리지 등 — [Yamaha 뉴스릴리스 2014-04-24](https://archive.yamaha.com/ja/news_release/2014/14042401.html), [VOCALODUCER 2013-10-21](https://archive.yamaha.com/ja/news_release/2013/13102104.html)
- 모바일: 「Mobile VOCALOID Editor」(iOS)와 구독판 — [App Store](https://apps.apple.com/app/mobile-vocaloid-editor/id947797108), [PR TIMES](https://prtimes.jp/main/html/rd/p/000001039.000010701.html)
- (2024-10) Yamaha가 자사 R&D 기술을 API로 묶은 「Yamaha Music Connect API」를 사업자 대상으로 제공하기 시작했다 — [Yamaha 뉴스릴리스](https://www.yamaha.com/ja/news_release/2024/24100701/) (**가창 합성 포함 여부는 미확인**)

### Inferences
- 개인용 VOCALOID6 라이선스(1대 PC 제한)로는 사용자를 위한 서버 렌더링 서비스를 운영할 수 없다(추정). 플랫폼 내 VOCALOID 합성은 Yamaha와의 B2B 계약이 전제다. 과거 NetVOCALOID·Unity SDK 같은 선례가 있어 협상 여지는 있어 보이나, 현행 조건은 공개돼 있지 않다(추정).
- .vpr은 JSON이므로 노트·가사·템포를 파싱해 가사 자막 타이밍을 만들기 쉽다. 음소 위치(`phonemePositions`)는 사용자가 편집한 경우에만 저장되는 것으로 보이며, 실제 음소 타이밍은 엔진이 렌더링 때 계산한다(추정).

### Gaps
- VOCALOID7 등 VOCALOID6 이후 세대 발표: 검색에서 발견되지 않음(미확인).
- VOCALOID:AI 세대용 SDK/임베딩 라이선스 존재 여부, NetVOCALOID 현재 상태, Yamaha Music Connect API의 가창 합성 포함 여부: 미확인(검색 할당량 소진).
- VOCALOID6 정확한 발매일(2022-10-13로 알려져 있으나 본 조사에서 1차 출처 미확인), VOCALO CHANGER의 기능 상세(사용자 가창을 VOCALOID:AI 음색으로 변환하는 기능으로 알려짐 — 미확인).

## 3. Synthesizer V Studio 2(Dreamtonics): 출시·에디션·기능·스크립팅·.svp 형식·플러그인·임베딩 라이선스

### Takeaway
SV Studio 2 Pro는 2024-12 발표, 2025-03-21 출시, $99다. VST3/AU/AAX(인스트루먼트)와 ARA 플러그인을 제공하고, Lua/JavaScript 스크립팅은 Pro 전용이다. .svp는 JSON(끝에 NUL 바이트)이며 노트·가사·음소 문자열·파라미터 커브·보컬 모드를 담고, 시간 단위는 "blick"(4분음표 = 705,600,000)이다. SDK·클라우드 API는 공개돼 있지 않고, 다른 소프트웨어에 상용으로 임베딩하려면 Dreamtonics/AHS에 별도 문의해야 한다.

### Cited Findings
#### 3-A. 출시·가격·에디션
- 2024-12 발표, 2025-03 출시 — [SynthV Wiki(팬 위키)](https://synthv.fandom.com/wiki/Synthesizer_V_Studio_2). 출시일 2025-03-21 — [Attack Magazine](https://www.attackmagazine.com/news/dreamtonics-announces-the-release-of-synthesizer-v-studio-2-pro/), [Dreamtonics 문서 「2025年3月21日に発売予定」](https://sv1.docs.dreamtonics.com/ja/synthv/upgrade/about-upgrade)
- 가격 $99, 추가 보이스 개당 $79(리뷰 기준) — [Sound On Sound 리뷰](https://www.soundonsound.com/node/4933228), [Dreamtonics Store](https://store.dreamtonics.com/?p=47318)
- 무료 에디션: Basic 제한은 프로젝트당 3트랙, 렌더 스레드 2개다. 크로스링구얼 합성, 자동 피치 튜닝 커스터마이즈, 오너먼트, 호흡음(aspiration) 출력, 노트 디튠, Tone Shift, 플러그인, 보컬 모드, AI Retake, MIDI 키보드, 메트로놈, 스크립팅을 쓸 수 없다 — [Dreamtonics Store 비교](https://store.dreamtonics.com/?p=15) (**SV1 시절 Basic 비교표일 가능성이 있음**)
- SV2 무료판 표기는 출처마다 다르다. "Limited Version at no cost" — [SynthV Wiki](https://synthv.fandom.com/wiki/Synthesizer_V_Studio_2) / "2025년에 구 SV Studio Pro가 basic 역할을 하고 V2 Pro가 새 pro가 됐으며, 가격 차는 $10" — [AudioCipher](https://www.audiocipher.com/post/dreamtonics-synthesizer-v-studio-2-pro) **(불일치, 2차 출처)**
- 보이스 DB만 사고 Pro가 없는 사용자는 Dreamtonics 다운로더로 무료판을 받을 수 있다 — [Dreamtonics Installer Downloader](https://auth.dreamtonics.com/store/download) (검색 요약 기준)

#### 3-B. 주요 기능(2025-03 출시 시점 보도)
- 오프라인 렌더링 최대 300% 고속화(GPU 불필요), 업그레이드된 보이스 전부에 한국어 크로스링구얼 추가 — [Attack Magazine](https://www.attackmagazine.com/news/dreamtonics-announces-the-release-of-synthesizer-v-studio-2-pro/), [AudioCipher](https://www.audiocipher.com/post/dreamtonics-synthesizer-v-studio-2-pro)
- AI Retakes: 타이밍·피치·음색(또는 셋 전부)의 미세 변주를 "여러 테이크"처럼 생성한다 — [Mix 리뷰](https://www.mixonline.com/technology/reviews/others/dreamtonics-synthesizer-v-studio-2-pro-review)
- Smart Pitch Controls, Tone Shift, 랩 전용 엔진(Sing / Rap 모드), Pitch/Timbre/Pronunciation 각각의 보컬 모드, Tension·Breathiness·Voicing·Gender와 신규 Mouth Opening 파라미터 — [Recording Magazine](https://www.recordingmag.com/news/dreamtonics-unveils-synthesizer-v-studio-2-pro/), [Magnetic Magazine](https://magneticmag.com/2025/03/ai-vocals-just-got-real-synthesizer-v-studio-2-pro-is-here/), [Sound On Sound](https://www.soundonsound.com/reviews/dreamtonics-synthesizer-v-studio-2-pro?amp)
- Vocoflex(별도 제품)를 SV Studio 플러그인 뒤 이펙트 체인에 두면 결과 음성을 블렌드·모프·치환·변형할 수 있다 — [Dreamtonics Vocal Creation Bundle](https://store.dreamtonics.com/?p=47354)

#### 3-C. 플러그인·DAW 연동·가져오기
- SV Studio 2 Pro에는 VST3/AudioUnit/AAX 플러그인이 포함되고, 플러그인 종류는 Instrument와 ARA 두 가지다 — [SV2 문서(일본어) 플러그인](https://sv2.docs.dreamtonics.com/ja/plugins), [SV2 문서(영어)](https://sv2.docs.dreamtonics.com/en/plugins)
- 호환 DAW에서 ARA2를 지원한다 — [Sound On Sound](https://www.soundonsound.com/node/4933228)
- 2.1.2 Beta에서 Pro Tools·Cubase·REAPER의 ARA 관련 버그를 수정했다 — [Dreamtonics 포럼](https://forum.dreamtonics.com/t/synthesizer-v-studio-2-pro-2-1-2-beta/4476)
- DAW에서 내보낸 MIDI 파일을 SV Studio 2 Pro로 가져올 수 있다 — [Sound On Sound](https://www.soundonsound.com/node/4933228) (검색 요약 기준)

#### 3-D. 스크립팅 API
- 스크립팅은 Pro 전용이며 Lua와 JavaScript를 지원한다 — [Scripting Manual](https://resource.dreamtonics.com/scripting/), [SV2 문서 Scripts](https://sv2.docs.dreamtonics.com/en/scripts), [Dreamtonics Store 비교](https://store.dreamtonics.com/?p=15)
- 공식 샘플 저장소 `Dreamtonics/svstudio-scripts`는 Apache-2.0이고 Lua·JS 예제를 담고 있다(마지막 커밋 2026-01-14). 샘플은 `SV.getProject`, `SV.getMainEditor`, `SV.getPlayback`, 노트의 `getOnset`/`getDuration`/`getLyrics`, `getTimeAxis().getBlickFromSeconds`, `getParameter`, 커스텀 다이얼로그 등을 사용한다 — [GitHub Dreamtonics/svstudio-scripts](https://github.com/Dreamtonics/svstudio-scripts)

#### 3-E. .svp 형식(1차 근거: 오픈소스 임포터 코드)
- .svp는 JSON이다. OpenUtau는 파일 끝의 `\0`(NUL)을 잘라낸 뒤 JSON으로 역직렬화한다. SV1용과 SV2용 로더(`SVP`/`SVP2`)가 따로 있다 — [OpenUtau SVP.cs](https://github.com/stakira/OpenUtau/blob/HEAD/OpenUtau.Core/Format/SVP.cs)
- 형식 판별: SV2 파일은 `"mouthOpening":` 키로, SV1 파일은 `"version":` 또는 `"database":`로 판별한다 — [OpenUtau Formats.cs](https://github.com/stakira/OpenUtau/blob/HEAD/OpenUtau.Core/Format/Formats.cs)
- 구조: `version`, `time`(meter / tempo[position, bpm]), `library`(그룹), `tracks`(mainGroup, mainRef, groups)를 가진다. 노트는 `onset`, `duration`, `lyrics`, `phonemes`, `pitch`, `musicalType`, `instantMode`, `attributes`/`systemAttributes`(vocalModeParams 포함)를 가진다. 파라미터 커브는 `pitchDelta`, `loudness`, `tension`, `breathiness`, `gender`, `voicing`, `toneShift`, `mouthOpening`이다. 참조(ref)에는 `database.name`, `voice.vocalModeParams`, 반주 오디오 `audio.filename`이 들어간다 — [OpenUtau SVP.cs](https://github.com/stakira/OpenUtau/blob/HEAD/OpenUtau.Core/Format/SVP.cs)
- 시간 단위 blick: OpenUtau는 `blicksPerTick = 705600000.0 / resolution`으로 변환한다(4분음표 = 705,600,000 blick) — [OpenUtau SVP.cs](https://github.com/stakira/OpenUtau/blob/HEAD/OpenUtau.Core/Format/SVP.cs)
- "svp: Synthesizer V Studio, json 기반, 활발히 개발 중" / 구 SV Editor의 s5p도 json 기반이다 — [LibreSVIP project_formats.md](https://github.com/SoulMelody/LibreSVIP/blob/HEAD/docs/project_formats.md)

#### 3-F. SDK·임베딩·파트너
- AHS판 SV EULA: "다른 소프트웨어에 상용 목적으로 임베딩하려면 연락할 것" — [AHS SV EULA(영문)](https://www.ah-soft.com/synth-v/eula_e.html)
- Dreamtonics 성명: EULA 범위를 넘는 Synthesizer V 관련 사업 활동과 상표 사용에는 Dreamtonics의 승인이 필요하고, 승인된 협업은 공식 채널로 공지한다. 파트너사의 보이스 DB는 Dreamtonics 자체 제작 DB와 다른 조건으로 배포된다 — [Statement on Business Activities Related to Synthesizer V](https://dreamtonics.com/statement-on-business-activities-related-to-synthesizer-v/)
- SV Studio 2 Pro EULA 원문(텍스트)의 위치: [SynthesizerVStudio2ProEULA_EN.txt](https://dreamtonics.com/wp-content/uploads/2025/05/SynthesizerVStudio2ProEULA_EN.txt) (egress 차단으로 미열람)

### Inferences
- .svp는 JSON이라 서버에서 가볍게 파싱해 노트 단위 가사 타이밍(blick → 템포 맵 → 초)을 뽑을 수 있다. 다만 실제 음소 타이밍은 엔진이 계산하고, `phonemes`는 사용자가 덮어쓴 문자열일 뿐이다(추정).
- 공식 스크립트 API(Lua/JS, Pro 전용)로 노트·가사·시간축을 읽을 수 있다. 플랫폼이 "SV2 → 플랫폼용 가사 타이밍 JSON 내보내기" 스크립트를 배포하는 방식은 기술적으로 가능해 보인다(추정; Basic 사용자는 쓸 수 없음).
- 서버 측 SV2 엔진 구동이나 웹 임베딩은 공개 SDK가 없고 EULA상 별도 계약 사항이므로, 단기 계획에서는 "사용자가 로컬에서 렌더링해 업로드" 모델이 현실적이다(추정).

### Gaps
- SV2 시대 Basic(또는 Limited)과 Pro의 정확한 기능 차이, SV2 무료판 명칭: 출처 간 불일치, 미해결.
- SV2의 오디오 내보내기 사양(트랙별 스템, 포맷), MIDI 내보내기 지원: 1차 출처 미확인.
- Dreamtonics의 SDK·클라우드·파트너 프로그램 공개 조건, Vocoflex 출시일·가격: 미확인.

## 4. 重音テト(Kasane Teto): 연혁, SV AI / SV2 AI 테토, 공식 가이드라인(TWINDRILL), 상용 규칙

### Takeaway
테토는 VOCALOID 패러디(만우절 기획으로 알려짐)로 생겨난 UTAU 음원이다. 공식 운영은 TWINDRILL, 음성 제공은 小山乃舞世, 디자인은 線이 맡았다. 이후 「Synthesizer V AI 重音テト」(2023년 — 정확한 일자 미확인)를 거쳐 「Synthesizer V 2 AI 重音テト」가 2025-11-27 AHS에서 발매됐다(DL판 ¥9,680). 개인의 동영상 투고·동인 판매·음원 유료 배포·광고 수익은 "상용 이용이 아니다"로 정리돼 있고, 법인 등의 상용 이용은 사전 연락이 필요하다(창구는 Crypton에 위탁).

### Cited Findings
#### 4-A. 연혁·권리 구조
- VOCALOID 패러디에서 태어난 「重音テト」는 상용 제품의 권리 보호를 위해 상표화됐고, 동인 사용은 종전과 같이 허용된다 — [ねとらぼ(ITmedia)](https://nlab.itmedia.co.jp/cont/articles/3268868/)
- 권리 표기는 디자인 「線」, 음성 제작 「小山乃舞世」, 공식 운영 「TWINDRILL」의 3자 연명이다 — [kasaneteto.jp 가이드라인](https://kasaneteto.jp/guidelines/)
- 공식 X 계정(TWINDRILL) — [@twindrill_teto](https://x.com/twindrill_teto/status/1557331269025157121)
- 제품 전개: UTAU 음원, Synthesizer V AI, VOICEPEAK(낭독) 등 — [kasaneteto.jp Synthesizer V AI](https://kasaneteto.jp/synthesizerv/), [kasaneteto.jp VOICEPEAK](https://kasaneteto.jp/voicepeak/), [Wikipedia: Kasane Teto](https://en.wikipedia.org/wiki/Kasane_Teto)

#### 4-B. Synthesizer V AI 重音テト(1세대) / Synthesizer V 2 AI 重音テト
- 1세대 제품 페이지 — [SONICWIRE C0227](https://sonicwire.com/product/C0227). 무료 Lite판(검색 요약상 명칭 "Synthesizer V AI Kasane Teto Lite Version")은 동인 활동을 포함한 모든 수익화가 불가하고, 수익화하려면 유료판을 써야 한다 — [kasaneteto.jp 규약 Q&A](https://kasaneteto.jp/guidelines/faq.html) (검색 요약 기준)
- 「Synthesizer V 2 専用歌声データベース『重音テト』『フリモメン』」은 2025-10-30 발표, 2025-11-27 발매 — [AHS 보도자료](https://www.ah-soft.com/press/synth-v/20251030.html), [PR TIMES](https://prtimes.jp/main/html/rd/p/000000072.000060783.html), [Wikipedia](https://en.wikipedia.org/wiki/Kasane_Teto)
- 가격(검색 요약 기준): 패키지판 ¥10,780 / 다운로드판 ¥9,680 / AHS 유저 특별판 ¥8,580 / SV1 우대판 ¥6,600 / 업그레이드 코드 ¥4,950 — [AHS 제품 페이지](https://www.ah-soft.com/synth-v/teto2/), [시마무라](https://info.shimamura.co.jp/digital/newitem/2025/10/162575), [SONICWIRE C9335](https://sonicwire.com/product/C9335) (우대판·업그레이드 구분의 정확한 조건은 미확인)
- 小山乃舞世의 음성을 바탕으로 한 SV2 전용 DB — [AHS 제품 페이지](https://www.ah-soft.com/synth-v/teto2/)
- 보컬 스타일 「Joyful/Cute/Power/Mellow/Low/Mini/Rock」 — [Wikipedia](https://en.wikipedia.org/wiki/Kasane_Teto). 반면 소매 리스팅은 "4 Vocal Modes"로 표기 — [plusmodels 리스팅](https://plusmodels.com/store/AHS-Synthesizer-V-2-AI-Kasane-Teto-Voicebank-Natural-Singing-4-Vocal-Modes_205937104455.html) **(불일치)**
- 일본어로 녹음했으며, 크로스링구얼로 영어·중국어(표준)·광둥어·스페인어·한국어 가창이 가능하다 — [Wikipedia](https://en.wikipedia.org/wiki/Kasane_Teto)

#### 4-C. 공식 이용 규약(TWINDRILL)
- 음원 이용 규약은 캐릭터 이용 규약과 별개다 — [音源利用規約](https://kasaneteto.jp/guidelines/voice.html), [キャラクター利用規約](https://kasaneteto.jp/guidelines/character.html), [영문 Character Terms](https://kasaneteto.jp/guideline/ctu.html), [영문 voicebank 페이지](https://kasaneteto.jp/guideline.html/en-voicebank.html)
- 비상용 범위에서는 노래·말하기 모두 자유롭게 쓸 수 있으나, 음원에도 규약이 있다 — [kasaneteto.jp 가이드라인](https://kasaneteto.jp/guidelines/)
- 다음은 문제없고 상용 이용에도 해당하지 않는다: 개인이 테토로 부른 곡을 동영상 사이트·SNS에 투고, 개인이 동인 CD를 제작·배포·판매, 개인이 자작곡을 음악 배포 사이트에서 유료 배포, 개인이 테토 사용 동영상에서 광고 수익을 얻는 것 — [規約に関するQ&A](https://kasaneteto.jp/guidelines/faq.html)
- 상용 이용은 사전에 연락해야 한다. 창구는 크립톤 퓨처 미디어에 위탁했다(기존 거래처는 담당자에게, 그 외는 공식 사이트 문의 폼으로) — [kasaneteto.jp 가이드라인](https://kasaneteto.jp/guidelines/), [版権申請フォーム](https://kasaneteto.jp/guidelines/request.html)

### Inferences
- 플랫폼 이용자(개인)가 테토 곡 MV를 투고하는 것 자체는 공식 Q&A가 허용하는 범위로 보인다. 반면 운영사가 테토 음성을 자사 프로모션에 쓰거나, 테토 음성 렌더링을 서비스로 제공하거나, 크리에이터 수익 분배에 "테토"를 내세우는 것은 상용 이용으로 Crypton 창구 협의가 필요할 가능성이 높다(추정).
- 테토 SV2 음성 출력에는 TWINDRILL 규약과 AHS/Dreamtonics EULA가 함께 걸린다. Lite판 사용자의 수익화 금지 같은 에디션별 차이가 있어, 수익화 기능이 있는 플랫폼은 업로드 시 "사용한 에디션/라이선스 자가신고"가 필요할 수 있다(추정).

### Gaps
- UTAU판 테토 음원 규약의 세부(개변·재배포, 음원을 사용한 AI 모델 학습, 서버 설치): 원문 열람 실패(egress 차단), 검색 할당량 소진으로 미확인.
- SV AI 테토 1세대의 정확한 발매일(2023), SV2 테토의 정확한 보컬 모드 수: 미확인/불일치.
- "2008년 만우절(4월 1일) 기획"이라는 구체 연혁은 의뢰서 맥락과 Wikipedia 일반 서술에 근거한 것으로, 본 조사에서 원문 스니펫으로 확인하지 못했다(ねとらぼ는 "VOCALOID 패러디로 탄생"까지만 확인).

## 5. 렌더링된 보컬의 이용 조건: Crypton EULA·PCL(미쿠), Dreamtonics/AHS EULA·TWINDRILL(테토), 그리고 플랫폼에 대한 함의

### Takeaway
미쿠(V4X EULA), AHS판 SV EULA, Dreamtonics 약관은 모두 합성 음성을 쓴 작품의 상용·비상용 배포를 허용한다. 제한은 주로 다음 세 갈래다. ① 상표·캐릭터를 써서 상품이나 서비스를 홍보하는 행위(Crypton). ② 공서양속 위반·제3자 권리 침해, 리버스 엔지니어링, 소프트웨어 임베딩·재배포. ③ Dreamtonics 자체 보이스와 Vocoflex 출력을 머신러닝 학습에 쓰는 행위. PCL은 "캐릭터(이미지)" 라이선스이고 목소리 출력은 각 제품 EULA가 규율한다.

### Cited Findings
#### 5-A. Crypton(미쿠)
- V4X EULA: 합성 음성은 상용·비상용으로 이용할 수 있다. 다음은 금지다: 공서양속에 반하는 가사의 합성 음성 공개·배포, 제3자의 인격권 등을 침해하는 합성 음성. 또한 Crypton의 사전 동의 없이 합성 음성을 이용한 상품·서비스에 "VOCALOID", "ボーカロイド", "VOCALO", "ボカロ", 제품명 "初音ミク V4X"·"初音ミク" 등의 상표를 표시할 수 없다 — [初音ミク V4X EULA PDF](https://ec.crypton.co.jp/download/pdf/eula_MIKUV4X.pdf)
- Crypton FAQ/해설: 캐릭터 보컬 시리즈는 "악기"로 판매되므로, 상용·비상용을 불문하고 악곡 제작에 합성 음성을 쓰는 것은 제품 패키지가 허락한다. 그러나 VOCALOID 기술이나 캐릭터를 연상시키는 말·이미지로 악곡을 상업적으로 홍보하는 것은 패키지 허락 범위 밖이다 — [Crypton FAQ 363](https://ec.crypton.co.jp/support/faq/363), [Crypton FAQ 364](https://ec.crypton.co.jp/support/faq/364), [リットーミュージック 저작권 강좌](https://rittor-music.jp/column/rights/28279)
- PCL(피아프로 캐릭터 라이선스)은 비영리 창작에 최대한 자유로운 이용을 인정한다. 다음은 금지 사항이다: 2차 창작물의 선전·광고 목적 사용, 타인 작품 무단 사용, 캐릭터 가치를 떨어뜨리는 사용, 타인을 불쾌하게 하는 2차 창작물. Crypton은 2024-12-04 가이드라인을 넘는 이용의 증가에 우려를 표명하는 성명을 냈다 — [xexeq 기사(2차 출처)](https://xexeq.jp/blogs/media/topics28818)
- (2012) 미쿠 2차 창작물의 DL 판매는 원칙적으로 NG라고 Crypton이 설명했다(캐릭터 영역) — [ITmedia NEWS 2012-02-22](https://www.itmedia.co.jp/news/articles/1202/22/news116.html)
- 관련 계약서(미열람, 제목만 확인): 「ヤマハ株式会社 VOCALOID 製品 使用契約書」 — [PDF](https://ec.crypton.co.jp/download/pdf/eula_yamaha_v.pdf), 「バーチャルシンガーシリーズ使用契約書」(파일명 eula_MIKUV4XTRY) — [PDF](https://ec.crypton.co.jp/download/pdf/eula_MIKUV4XTRY.pdf), 「エンドユーザー使用許諾契約書」(eula_cv01) — [PDF](https://ec.crypton.co.jp/download/pdf/eula_cv01.pdf)

#### 5-B. Dreamtonics / AHS(테토 SV·SV2) / Vocoflex
- Dreamtonics 약관: 보이스 DB로 만든 작품은 수익 상한 없이 상용·비상용으로 배포·공개할 수 있다. 생성 음성으로 머신러닝 시스템을 학습시키는 것은 금지다. 보이스 DB 원래 이름과 다른 이름으로 작품을 배포하는 것도 금지다. 샘플 팩 제작·배포는 스니펫이 "가능/불가"로 상충해 DB별로 다를 수 있다 — [Dreamtonics Terms](https://dreamtonics.com/terms/) (검색 요약 기준, **샘플 팩 조항 미해결**)
- (트윗 ID 기준 2022-03 추정) Dreamtonics는 자체 보이스(Saki, Kevin, Ryo, Qing Su) 라이선스를 단순화해 "수익·이익 상한 없이 수익화 가능"하다고 공지했다 — [Dreamtonics X](https://twitter.com/dreamtonics_en/status/1507273000793821190?lang=en)
- Vocoflex로 처리한 음성은 머신러닝 모델 학습에 쓸 수 없다 — [Vocoflex EULA](https://dreamtonics.com/vocoflex-eula/)
- 출력에 사용자를 추적할 수 있는 비가청 워터마크가 들어간다는 기술이 있다 — [Vocoflex EULA](https://dreamtonics.com/vocoflex-eula/) / [VI-Control FAQ](https://vi-control.net/community/threads/faq-what-is-synthesizerv.136072/) (검색 요약. **어느 제품(Vocoflex인지 SV2인지)의 조항인지 미확인**)
- AHS판 SV EULA: 따로 정한 경우를 제외하면 생성 합성음은 상용·비상용 무상으로 이용할 수 있다. 리버스 엔지니어링과 복제 방지·기술적 제한의 우회는 금지다. 작품에 SV(또는 SV용 보이스뱅크) 사용을 표기할 수 있다. 다른 소프트웨어에 상용 임베딩하려면 문의해야 한다 — [AHS SV EULA](https://www.ah-soft.com/synth-v/eula_e.html)
- 서드파티 보이스 DB는 Dreamtonics 자체 DB와 다른 조건으로 배포된다(AHS의 테토는 AHS 조건 + TWINDRILL 규약 적용) — [Dreamtonics 성명](https://dreamtonics.com/statement-on-business-activities-related-to-synthesizer-v/), [kasaneteto.jp 가이드라인](https://kasaneteto.jp/guidelines/)

#### 5-C. Yamaha / 기타 엔진의 출력 조건(참고)
- Yamaha 보이스뱅크로 만든 곡의 상용 이용에는 특별한 허가·사용료가 원칙적으로 필요 없다 — [vocaloid.com FAQ 703](https://www.vocaloid.com/support/faq/703)
- DiffSinger 면책 조항: 정치인·유명인 등 본인 동의 없이 그 사람의 목소리를 생성하는 데 이 저장소 기능을 쓰는 것을 금지한다 — [openvpi/DiffSinger README](https://github.com/openvpi/DiffSinger/blob/HEAD/README.md)
- VOICEVOX 소프트웨어는 상용·비상용으로 이용할 수 있다. 생성 음성의 이용은 각 음성 라이브러리 규약을 따르고, 타인에게 이용을 허락할 때도 같은 조항을 지키게 해야 한다 — [OpenUtau wiki: VOICEVOX support](https://github.com/stakira/OpenUtau/wiki/VOICEVOX-support)

### Inferences
- **호스팅 자체**: 각 EULA상 라이선시는 이용자이고 작품 배포는 상용까지 허용되므로, 이용자가 만든 MV를 플랫폼이 호스팅·공유·외부 크로스포스트하는 것은 원칙적으로 EULA와 충돌하지 않아 보인다(추정, 법률 검토 필요).
- **서비스 명칭·마케팅**: V4X EULA는 합성 음성을 쓴 "상품·서비스"에 「VOCALOID」 「ボカロ」 「初音ミク」 등을 무단 표시하는 것을 금지한다. 플랫폼 명칭·도메인·광고 문구에 이 상표를 쓰는 것(예: "Vocaloid MV maker")은 Crypton/Yamaha와의 사전 협의 대상일 위험이 크다(추정). 리포지토리 이름 "vocaloid-video-platform"은 내부 코드명으로만 쓰는 편이 안전하다(추정).
- **AI 기능**: Dreamtonics 보이스 출력과 Vocoflex 출력은 ML 학습 금지가 명시돼 있다. 플랫폼 이용약관에 "업로드 보컬의 AI 학습·스크래핑 금지"를 넣고, 운영사도 업로드 음성으로 모델을 학습하지 않는 것이 보수적인 선택이다(추정). Crypton·AHS·TWINDRILL의 학습 조항은 미확인이지만 같은 원칙을 적용하는 것이 안전하다.
- **스템 재사용·리믹스 기능**: 다른 사용자가 테토/미쿠 보컬 스템을 소재로 재배포하게 하는 기능은 "샘플 팩 배포"나 "다른 이름으로 배포" 조항, 그리고 테토 상용 규정과 충돌할 수 있다(추정).
- **수익화**: 테토는 개인 광고 수익이 비상용으로 명시돼 있다. 그러나 플랫폼이 직접 테토/미쿠 콘텐츠로 수익을 얻거나(예: 운영사 광고 배치, 유료 기능) 이를 홍보하는 행위의 해석은 1차 문서로 확정되지 않는다 → Crypton(미쿠·테토 창구) 문의가 필요하다(추정).

### Gaps
- 初音ミク NT(Ver.2)·V6 EULA 원문, 특히 출력 음성의 AI 학습·보이스뱅크화 금지 여부: 미확인. 참고로 "VOCALOID 출력 음성을 UTAU 음원에 넣어도 되는가"를 Crypton에 문의한 기록(2019)이 있으나 결론은 미열람 — [アマノケイ 블로그](https://amanokei.hatenablog.com/entry/2019/08/23/234506)
- AHS EULA·TWINDRILL 규약의 ML 학습 조항, Dreamtonics 샘플 팩 조항의 정확한 문언: 미확인.
- 음원 배포 플랫폼(TuneCore 등) 쪽 조건은 범위 밖이지만 참고 링크로 남긴다 — [TuneCore Japan VOCALOID 안내](https://support.tunecore.co.jp/hc/ja/articles/360007022772-VOCALOID-%E3%81%9D%E3%81%AE%E4%BB%96%E9%9F%B3%E5%A3%B0%E5%90%88%E6%88%90%E3%82%BD%E3%83%95%E3%83%88%E3%82%92%E5%90%AB%E3%82%80-%E3%81%AB%E3%81%A4%E3%81%84%E3%81%A6)

## 6. 자체 스택에서 돌릴 수 있는 오픈소스·무료 엔진(OpenUtau, DiffSinger, NNSVS, UTAU, VOICEVOX 등)과 "미쿠·테토 같은 목소리"의 적법성

### Takeaway
코드 라이선스 측면에서 가장 현실적인 자체 호스팅 스택은 OpenUtau(MIT, 2026-10 활발히 개발 중) + DiffSinger(Apache-2.0, ONNX 배포)다. 단 세 가지를 유의해야 한다. ① 보이스뱅크는 저마다 별도 라이선스다. ② DiffSinger 커뮤니티 표준 보코더 가중치가 **CC BY-NC-SA 4.0(비상업)**이다. ③ 미쿠의 공식 오픈 모델은 존재하지 않는다. VOICEVOX 엔진은 HTTP 가창 API를 제공한다(LGPLv3/별도 라이선스 이중, 캐릭터별 규약 적용). 합법적으로 "미쿠/테토 목소리"를 내는 길은 공식 제품뿐이고(테토 UTAU는 비상용 무료 + 상용은 사전 협의), 팬 제작 DiffSinger/RVC 모델은 권리상 위험하다.

### Cited Findings
#### 6-A. OpenUtau
- 라이선스 MIT(Copyright 2014 StAkira), 마지막 커밋 2026-10-07, 태그 0.1.572.x-alpha — [OpenUtau LICENSE.txt](https://github.com/stakira/OpenUtau/blob/HEAD/LICENSE.txt), [Releases](https://github.com/stakira/OpenUtau/releases)
- Windows/macOS/Linux를 지원한다. VCV·CVVC·ARPAsing 등 다언어(영·일·중·한·러 등) 포네마이저, ENUNU(NNSVS) 가수, 플러그인 시스템을 갖췄다. "VOCALOID 호환은 목표가 아니다" — [OpenUtau README](https://github.com/stakira/OpenUtau/blob/HEAD/README.md)
- 코어에 DiffSinger·ENUNU·VOICEVOX·Vogen 렌더러 모듈이 있고, DiffSinger용 언어별 포네마이저 클래스(일본어·한국어·영어·중국어·광둥어·스페인어 등 약 19개 파일)를 갖췄다 — [OpenUtau.Core 디렉터리](https://github.com/stakira/OpenUtau/tree/HEAD/OpenUtau.Core/DiffSinger)
- 추론 런타임은 Microsoft.ML.OnnxRuntime 1.24.4, Windows는 DirectML 1.23.0(고정), Linux GPU 빌드다 — [OpenUtau.Core.csproj](https://github.com/stakira/OpenUtau/blob/HEAD/OpenUtau.Core/OpenUtau.Core.csproj)
- DiffSinger 사용 시 nsf_hifigan 보코더를 따로 받아 import해야 하고, 기본은 CPU 렌더링(DirectML/CoreML 선택 가능, macOS 11+)이다. 표현 파라미터는 PITD, GENC, VELC, ENE, BREC, TENC, VOIC, PEXP, SHFC, 보이스 컬러 커브(SV 보컬 모드에 해당) — [OpenUtau wiki: DiffSinger support](https://github.com/stakira/OpenUtau/wiki/DiffSinger-support)
- 내보내기 메뉴: Export Wav Files(트랙별), Mixdown To Wav File, Export Midi File, Export Ust Files, Export DiffSinger Scripts(.ds), Export Project — [OpenUtau Strings.axaml](https://github.com/stakira/OpenUtau/blob/HEAD/OpenUtau/Strings/Strings.axaml)
- DAW 연동 API v1.2: TCP 루프백, 개행 구분 JSON(제어) + 길이 접두 바이너리 프레임(오디오), float32 PCM 44.1kHz 스테레오. 참조 플러그인은 VST3/CLAP 브리지(KakaruHayate/openutau-vst-bridge) — [OpenUtau DawIntegration/API.md](https://github.com/stakira/OpenUtau/blob/HEAD/OpenUtau.Core/DawIntegration/API.md)
- (제안 단계) 「svs.io」 — msgpack 기반 SVS 백엔드 API 제안이다. `note_sequence`, `phoneme_sequence`(note_index, phoneme, duration), `f0` 데이터 구조를 정의하고 ZeroMQ 전송을 권장한다 — [OpenUtau wiki: svs.io 제안](https://github.com/stakira/OpenUtau/wiki/%5BPROPOSAL%5D-svs.io-%E2%80%90-singing-voice-synthesis-backend-API) (구현·채택 상태는 미확인)

#### 6-B. DiffSinger(OpenVPI)와 보코더
- 라이선스 Apache-2.0, 최신 태그 v2.5.1 — [DiffSinger LICENSE](https://github.com/openvpi/DiffSinger/blob/HEAD/LICENSE), [Releases](https://github.com/openvpi/DiffSinger/releases)
- 배포 형식은 ONNX다. variance·acoustic·NSF-HiFiGAN 각각의 export 스크립트를 제공한다 — [GettingStarted.md](https://github.com/openvpi/DiffSinger/blob/HEAD/docs/GettingStarted.md)
- 프로덕션 용도로는 "OpenUTAU for DiffSinger" 사용을 권장한다. 범용 사전학습 NSF-HiFiGAN 보코더는 약 50MB다 — [BestPractices.md](https://github.com/openvpi/DiffSinger/blob/HEAD/docs/BestPractices.md)
- **커뮤니티 보코더 가중치 라이선스는 CC BY-NC-SA 4.0**(재배포 시 라이선스 사본, OpenVPI/DiffSinger Community 표기, 페이지 링크 필요)이다. 저장소 LICENSE는 AGPL-3.0이다. 릴리스: NSF-HiFiGAN 2022-12-11 / 2024-02-19, PC-NSF-HiFiGAN 2025-02-27(44.1kHz, hop 512, 128 mel) — [DiffSinger Community Vocoders](https://openvpi.github.io/vocoders/), [openvpi/vocoders LICENSE](https://github.com/openvpi/vocoders/blob/HEAD/LICENSE)
- 브라우저 실행 후보 런타임: onnxruntime-web 1.30.0, MIT(2026-10-07 npm 조회) — [npm onnxruntime-web](https://www.npmjs.com/package/onnxruntime-web)

#### 6-C. NNSVS / VOICEVOX / 기타
- NNSVS: MIT(Copyright 2020 Ryuichi Yamamoto), 마지막 커밋 2023-10-10 — [nnsvs LICENSE](https://github.com/nnsvs/nnsvs/blob/HEAD/LICENSE)
- VOICEVOX ENGINE: LGPL v3와 "소스 공개가 필요 없는 별도 라이선스(ヒホ에게 요청)"의 이중 라이선스, 마지막 커밋 2026-10-02 — [voicevox_engine LICENSE](https://github.com/VOICEVOX/voicevox_engine/blob/HEAD/LICENSE)
- VOICEVOX ENGINE의 HTTP 가창 합성: `POST /sing_frame_audio_query?speaker=…`(악보 JSON: `key`=MIDI 번호, `frame_length`, `lyric`=히라가나/가타카나 1모라) 다음 `POST /frame_synthesis` → WAV. 기본 프레임레이트는 93.75Hz이고, 첫 노트는 무음이어야 한다 — [voicevox_engine README](https://github.com/VOICEVOX/voicevox_engine/blob/HEAD/README.md)
- OpenUtau는 VOICEVOX(허밍/가창)를 지원한다. "무료·중간 품질의 TTS 및 가창 합성 소프트웨어"이고, 생성 음성은 각 라이브러리 규약을 따른다 — [OpenUtau wiki: VOICEVOX support](https://github.com/stakira/OpenUtau/wiki/VOICEVOX-support)

#### 6-D. 미쿠·테토 음성의 합법적 생성 경로
- 테토: 비상용 범위는 자유 이용, 상용은 사전 연락(Crypton 위탁 창구) — [kasaneteto.jp 가이드라인](https://kasaneteto.jp/guidelines/), [音源利用規約](https://kasaneteto.jp/guidelines/voice.html)
- Dreamtonics 약관은 (Dreamtonics 보이스 DB의) 생성 음성으로 ML 시스템을 학습시키는 것을 금지하고, Vocoflex 출력도 학습 금지다 — [Dreamtonics Terms](https://dreamtonics.com/terms/), [Vocoflex EULA](https://dreamtonics.com/vocoflex-eula/)
- DiffSinger 자체도 동의 없는 타인 음성 생성을 금지한다 — [DiffSinger README](https://github.com/openvpi/DiffSinger/blob/HEAD/README.md)

### Inferences
- **브라우저 추론**: DiffSinger 모델은 ONNX라 onnxruntime-web(WASM/WebGPU)으로 실행하는 것이 원리상 가능하다. 그러나 보코더만 약 50MB이고 acoustic/variance 모델과 포네마이저까지 더해지면 다운로드·메모리·속도 부담이 크다. MVP는 서버(또는 사용자 PC) 렌더링, 브라우저는 미리듣기 수준이 현실적이다(추정. 실제 모델 크기·속도 벤치마크 미확보).
- **상업 플랫폼 라이선스 함정**: OpenUtau(MIT)·DiffSinger(Apache-2.0) 코드는 상용에 쓸 수 있다. 하지만 사실상 표준인 커뮤니티 보코더가 CC BY-NC-SA 4.0이라 상용 서비스에 그대로 쓸 수 없고, 자체 보코더 학습이나 별도 라이선스가 필요하다(추정). 보이스뱅크도 상용 허용 여부를 하나씩 확인해야 한다.
- **미쿠/테토 유사 음성**: SV 출력으로 "테토풍" 모델을 학습하는 것은 Dreamtonics 조건이 적용되는 범위에서 금지되며, AHS판 테토 EULA에 같은 조항이 있는지는 미확인이다. 합법 경로는 공식 제품(미쿠 V4X/NT/V6, 테토 UTAU/SV/SV2)뿐이다. 팬 제작 미쿠·테토 DiffSinger/RVC 모델을 플랫폼이 호스팅·제공하는 것은 성우·권리자의 동의가 없으므로 피해야 한다(추정).
- **실현 가능한 "한 화면 작곡" 대안**: 자체 웹 피아노롤(ustx/ufdata 호환 데이터 모델) + 서버 측 OpenUtau 코어 또는 DiffSinger/VOICEVOX 렌더링 + 상용 허용 보이스. 미쿠·테토는 "외부 에디터 → 파일/오디오 업로드"로 지원하는 이원화가 합리적이다(추정).

### Gaps
- NEUTRINO(라이선스·상용 조건), VoiSona 무료 티어 조건, ACE Studio의 공개 API 유무, Sinsy, 상용 가창 합성 API 서비스(공개 API 보유 여부): **웹 검색 할당량 소진으로 미조사**.
- UTAU 본체(飴屋/菖蒲) 이용 조건, UTAU 테토 음원 원문 규약(개변·AI 학습): 미확인.
- 브라우저에서 DiffSinger를 실행한 공개 구현 사례와 성능 데이터: 미확인.

## 7. 파일 포맷·변환: 노트·가사·음소 타이밍을 담는 형식, 컨버터(UtaFormatix 등), 가사 타이밍·립싱크 데이터 자동 생성

### Takeaway
주요 형식은 대부분 텍스트 직렬화다. .svp(JSON), .vpr(zip+JSON), .ppsf NT(zip+JSON), .ustx(YAML), .vsqx(XML), .ust(텍스트), MusicXML(XML), MIDI(SMF). 따라서 노트 단위 가사 타이밍은 서버에서 쉽게 뽑을 수 있다. 반면 음소 단위 타이밍은 대부분 엔진이 렌더링 때 계산하므로(예외: DiffSinger .ds, 사용자 편집값) 립싱크는 "가사 → 모음 매핑" 또는 오디오 정렬로 보완해야 한다. UtaFormatix3(Apache-2.0, Kotlin/JS 웹앱)와 LibreSVIP(MIT, Python, **LRC/SRT/ASS 자막 생성 플러그인** 포함)가 핵심 오픈소스 도구다.

### Cited Findings
#### 7-A. 형식별 구조(1차: 컨버터·에디터 소스)
- 형식 일람(일부): svp=json, vpr=json+zip(Vocaloid 5+), vsqx=xml(Vocaloid 3/4), vsq=MIDI+INI(Vocaloid 2), ppsf(NT)=json+zip, ppsf(구)=커스텀 바이너리, ustx=yaml(OpenUTAU), ust=커스텀 텍스트(UTAU), MusicXML=xml, mid=SMF, ds=json(DiffSinger), dspx=json(DiffScope), tssln/tssprj=JUCE ValueTree 바이너리(VoiSona), acep v2=CBOR+zstd에 헤더 일부 암호화(ACE Studio), ccs=xml(CeVIO), vvproj=json(VOICEVOX), vxf=MIDI 2.0 클립(VOCALOID β-STUDIO) — [LibreSVIP project_formats.md](https://github.com/SoulMelody/LibreSVIP/blob/HEAD/docs/project_formats.md)
- USTX: YAML 1.2, UTF-8, 1 4분음표 = 480 tick. 최상위는 `name`, `ustx_version`, `resolution`, `time_signature`, `tempos`, `tracks`, `voice_parts`, `wave_parts`. 노트는 `position`, `duration`, `tone`(MIDI), `lyric`, `pitch`, `vibrato`, `phoneme_expressions`, `phoneme_overrides`이고, `phoneme_overrides`는 "포네마이저 결과에 대한 tick 오프셋"이다 — [OpenUtau wiki: USTX file format](https://github.com/stakira/OpenUtau/wiki/USTX-file-format)
- USTX 버전: 위키는 "currently 0.6"이라 하지만 현행 코드는 `kUstxVersion = 0.10`(위키가 낡음) — [OpenUtau USTx.cs](https://github.com/stakira/OpenUtau/blob/HEAD/OpenUtau.Core/Format/USTx.cs)
- OpenUtau가 가져올 수 있는 형식: VSQ3/VSQ4(vsqx), UST, USTX, MIDI, UFDATA, MusicXML, SVP(SV1/SV2) — [OpenUtau Formats.cs](https://github.com/stakira/OpenUtau/blob/HEAD/OpenUtau.Core/Format/Formats.cs)
- .svp 노트/파라미터 필드와 blick 단위(3-E), .vpr 필드(2-B), .ppsf NT 필드(1-D) — [OpenUtau SVP.cs](https://github.com/stakira/OpenUtau/blob/HEAD/OpenUtau.Core/Format/SVP.cs), [UtaFormatix3 Vpr.kt](https://github.com/sdercolin/utaformatix3/blob/HEAD/core/src/main/kotlin/core/io/Vpr.kt), [UtaFormatix3 Ppsf.kt](https://github.com/sdercolin/utaformatix3/blob/HEAD/core/src/main/kotlin/core/io/Ppsf.kt)
- DiffSinger .ds: 음소 시퀀스, 음소 길이(초), 악보, 커브 파라미터를 담은 JSON. OpenUTAU에서 .ds를 내보낼 수 있다(노트 사이 공백 기준으로 분할) — [DiffSinger BestPractices.md](https://github.com/openvpi/DiffSinger/blob/HEAD/docs/BestPractices.md)

#### 7-B. 컨버터
- UtaFormatix3: Apache-2.0(© 2020 sdercolin), Kotlin/JS + React 웹앱, 마지막 커밋 2025-05-19, GitHub Pages 배포 워크플로 — [README](https://github.com/sdercolin/utaformatix3/blob/HEAD/README.md), [LICENSE.md](https://github.com/sdercolin/utaformatix3/blob/HEAD/LICENSE.md), [deploy.yml](https://github.com/sdercolin/utaformatix3/blob/HEAD/.github/workflows/deploy.yml)
  - 가져오기: .vsqx(3/4), .vpr, .vsq, .mid(VOCALOID), .mid(표준), .ust, .ustx, .ccs, .xml/.musicxml, .svp, .s5p, .dv, **.ppsf(NT)**, .tssln, .ufdata — [UtaFormatix3 README](https://github.com/sdercolin/utaformatix3/blob/HEAD/README.md)
  - 내보내기: .vsqx(4), .vpr, .vsq, .mid, .ust, .ustx, .ccs, MusicXML, .svp, .s5p, .dv, .tssln, .ufdata(**.ppsf 내보내기 없음**) — [UtaFormatix3 README](https://github.com/sdercolin/utaformatix3/blob/HEAD/README.md)
  - 보존 정보: 트랙, 노트, 템포, 박자. 일본어 가사 CV↔VCV, 가나↔로마자 변환. 음소 변환은 VOCALOID 계열·.ustx·.svp·.ufdata만 지원. 피치 import/export 표가 있다(SVP/USTX/UST/DV는 비브라토 import 포함) — [UtaFormatix3 README](https://github.com/sdercolin/utaformatix3/blob/HEAD/README.md)
  - 코어 모듈은 JS 라이브러리로 빌드된다(`binaries.library()`, jszip·js-yaml·midi-file 의존) — [core/build.gradle.kts](https://github.com/sdercolin/utaformatix3/blob/HEAD/core/build.gradle.kts)
- 중간 공개 형식 UtaFormatix Data(.ufdata): npm `utaformatix-data` 1.1.0(2023-04-07), Apache-2.0 — [npm utaformatix-data](https://www.npmjs.com/package/utaformatix-data), [UtaFormatix3 README](https://github.com/sdercolin/utaformatix3/blob/HEAD/README.md)
- LibreSVIP: MIT(© 2023-2026 SoulMelody), 마지막 커밋 2026-10-07, 최신 태그 v2.9.0, `pip install libresvip[desktop]`(Python 3.11+), cli/gui/web/tui 모듈 — [LibreSVIP README](https://github.com/SoulMelody/LibreSVIP/blob/HEAD/README.md), [LICENSE](https://github.com/SoulMelody/LibreSVIP/blob/HEAD/LICENSE)
  - **자막·가사 생성 플러그인(내보내기 전용)**: ASS(Advanced SubStation Alpha), LRC, SRT. 옵션으로 오프셋, 줄바꿈 기준, 슬러 노트 무시, 인코딩, 단일 트랙 선택이 있다 — [ass 플러그인](https://github.com/SoulMelody/LibreSVIP/tree/HEAD/libresvip/plugins/ass), [lrc](https://github.com/SoulMelody/LibreSVIP/tree/HEAD/libresvip/plugins/lrc), [srt](https://github.com/SoulMelody/LibreSVIP/tree/HEAD/libresvip/plugins/srt)
  - ACE Studio(.acep/.acet), Piapro Studio(.ppsf, NT판·구판 파서) 플러그인 — [acep](https://github.com/SoulMelody/LibreSVIP/tree/HEAD/libresvip/plugins/acep), [ppsf](https://github.com/SoulMelody/LibreSVIP/tree/HEAD/libresvip/plugins/ppsf)

### Inferences
- **가사 타이밍 자동 생성 파이프라인(추정)**: 업로드된 프로젝트 파일을 공통 모델(ufdata 계열: 트랙·노트[tick, key, lyric]·템포·박자)로 정규화한다 → 템포 맵으로 tick/blick을 초로 환산한다 → 가사 줄/구 단위로 묶는다(LibreSVIP의 ASS/LRC 생성 로직 참고). 서버 측은 LibreSVIP(Python, MIT), 브라우저 측은 UtaFormatix core(Kotlin/JS, Apache-2.0) 재사용이 후보다.
- **오프셋 문제**: 프로젝트 타임라인의 0초와 업로드된 믹스 오디오의 0초가 다를 수 있다(DAW 내 배치, 인트로 무음). 자동 상호상관이나 사용자 수동 오프셋 UI가 필요하다(추정, LibreSVIP 자막 옵션에도 offset이 있음).
- **립싱크**: 프로젝트 파일에 확정된 음소 타이밍은 보통 없으므로, (a) 일본어 가나 가사 → 모음(あいうえお)+ん 매핑을 노트 구간에 배치하는 근사, (b) OpenUtau 코어로 포네마이저를 돌리거나 .ds를 내보내 음소 길이(초)를 얻는 방식, (c) 보컬 스템에 강제 정렬(forced alignment)을 거는 방식 중 택한다(추정). 캐릭터별 립싱크에는 가수별 트랙(또는 스템) 구분이 필요하다.
- **포맷 우선순위(추정)**: .vpr(V6/VOCALOID6), .svp(SV2), .ppsf(NT), .ustx(OpenUtau), MIDI/MusicXML(범용)을 MVP에서 지원하고, .vsqx/.ust는 레거시로 지원한다.

### Gaps
- SV2에서 바뀐 .svp 스키마 전체(공식 문서 부재), .vpr `phonemePositions`가 기본으로 채워지는지 여부: 미확인.
- 프로젝트 파일 파싱의 법적 측면(상호운용 목적의 형식 분석)에 대한 공식 입장: 미조사.

## 8. 실제 워크플로(한 곡에서 미쿠 + 테토)와 플랫폼이 크리에이터에게서 받게 될 산출물

### Takeaway
미쿠(VOCALOID6 Editor 또는 Piapro Studio NT2)와 테토(SV Studio 2)를 한 편집기에서 다룰 방법은 없다. 크리에이터는 ① 하나의 DAW 안에서 각 엔진을 플러그인/ARA로 나란히 쓰거나 ② 각 스탠드얼론 에디터에서 WAV를 렌더링해 DAW에서 믹스한다. 따라서 플랫폼이 현실적으로 받게 될 것은 "최종 믹스 오디오(필수) + 선택적 보컬 스템 + 선택적 엔진별 프로젝트 파일 또는 MIDI/MusicXML"이다.

### Cited Findings
- VOCALOID6 Editor는 ARA2로 DAW와 연동한다(Cubase 설정 가이드 있음, Cubase 15.0.20+ 불량 공지) — [vocaloid.com Cubase 가이드](https://www.vocaloid.com/en/learn/ln6202/), [vocaloid.com 불량 공지](https://www.vocaloid.com/news/news_36/)
- 初音ミク V6 보이스뱅크에는 Cubase LE가 동봉되고, VOCALOID6 Editor는 스탠드얼론/VST/AU/ARA2다 — [시마무라](https://info.shimamura.co.jp/digital/newitem/2026/02/164674)
- Piapro Studio NT2는 VST3/AU + 스탠드얼론이다. Crypton 계열 강좌가 Cubase 안에서 Piapro Studio로 반주와 합치는 방법을 안내한다 — [SONICWIRE NT](https://sonicwire.com/product/virtualsinger/special/mikunt), [SONICWIRE 강좌 2026-04](https://sonicwire.com/news/blog/2026/04/07-start-nt)
- SV Studio 2 Pro는 Instrument + ARA 플러그인(VST3/AU/AAX)을 제공한다. 무료판(Basic)은 플러그인을 쓸 수 없다 — [SV2 문서](https://sv2.docs.dreamtonics.com/ja/plugins), [Dreamtonics Store 비교](https://store.dreamtonics.com/?p=15)
- OpenUtau는 "DAW를 대체하지 않는다"고 명시하고, DAW 브리지(VST3/CLAP)로 실시간 연동한다 — [OpenUtau README](https://github.com/stakira/OpenUtau/blob/HEAD/README.md), [DawIntegration/API.md](https://github.com/stakira/OpenUtau/blob/HEAD/OpenUtau.Core/DawIntegration/API.md)
- 엔진 간 멜로디 이식(예: 미쿠 파트를 SV로 옮기기)은 UtaFormatix/LibreSVIP로 노트·가사·템포를 변환해 처리할 수 있다(.ppsf NT는 UtaFormatix 기준 가져오기만 가능) — [UtaFormatix3 README](https://github.com/sdercolin/utaformatix3/blob/HEAD/README.md), [LibreSVIP project_formats.md](https://github.com/SoulMelody/LibreSVIP/blob/HEAD/docs/project_formats.md)
- 각 에디터 단독으로 가능한 내보내기: Piapro Studio는 WAV와 .ppsf(+멜로디 MIDI) — [Crypton Piapro Studio](https://ec.crypton.co.jp/pages/prod/virtualsinger/piaprostudio). OpenUtau는 트랙별 WAV, 믹스다운 WAV, MIDI, UST, .ds — [OpenUtau Strings.axaml](https://github.com/stakira/OpenUtau/blob/HEAD/OpenUtau/Strings/Strings.axaml)

### Inferences
- **크리에이터가 업로드할 산출물 설계(추정)**:
  1. 필수: 최종 마스터(WAV/FLAC 권장, 길이 = 영상 길이 기준).
  2. 권장: 가수별 보컬 스템(미쿠 스템, 테토 스템). 캐릭터별 립싱크와 이펙트 반응(볼륨 엔벨로프) 생성에 쓴다.
  3. 선택: 엔진별 프로젝트 파일(.vpr/.ppsf/.svp/.ustx) 또는 DAW에서 내보낸 MIDI/MusicXML. 가사·노트 타이밍 자동화에 쓰고, 파일마다 "어느 캐릭터 트랙인지" 매핑 UI를 둔다.
  4. 대체 경로: 가사 텍스트 + 수동/반자동 타이밍(탭 입력, 강제 정렬).
- **ARA 사용자는 프로젝트 타임라인이 DAW 기준**이라 엔진 프로젝트 파일 단독으로는 곡 전체 오프셋이 맞지 않을 수 있다. 오디오 정렬 단계가 필수다(추정).
- **웹 플랫폼 통합 옵션 요약(추정, 1~7장 근거 종합)**:

| 옵션 | 미쿠/테토 공식 음성 | 기술 난이도 | 법적 전제 | 비고 |
|---|---|---|---|---|
| A. 외부 에디터 렌더링 → 오디오·프로젝트 업로드 | 가능(사용자 보유 라이선스) | 낮음 | 이용자 EULA 범위, 플랫폼은 호스팅 | MVP에 적합. 가사 타이밍은 파일 파싱 |
| B. 플랫폼 제공 스크립트/브리지(SV2 Pro 스크립트, OpenUtau DAW 브리지 류) | 가능 | 중간 | 각 EULA상 로컬 사용 | SV Basic 사용자는 스크립트 불가 |
| C. 자체 웹 피아노롤 + 서버 DiffSinger/OpenUtau/VOICEVOX 렌더 | 불가(상용 허용 보이스만) | 높음 | 보이스별 라이선스, 보코더 NC 문제 | "한 화면 작곡" 실현 경로 |
| D. Yamaha/Crypton/Dreamtonics·AHS와 B2B 임베딩·서버 렌더 | 가능(계약 시) | 높음 | 개별 계약(조건 비공개) | 과거 NetVOCALOID·Unity SDK 선례 |
| E. 팬 제작 미쿠/테토 AI 모델 | — | — | 권리자·성우 동의 없음, Dreamtonics 학습 금지 | 채택 불가 |

### Gaps
- 미쿠+테토 듀엣 제작에서 ARA 방식과 스탠드얼론 방식의 실제 사용 비율, 크리에이터가 일반적으로 공개·공유하는 산출물(스템 공개 관행): 정량 근거 없음.
- Piapro Studio NT2의 ARA 지원과 트랙별 스템 내보내기: 미확인.
