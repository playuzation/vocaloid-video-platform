# 한 화면 미쿠×테토 MV 플랫폼을 단계적으로 짓는다

가창 합성은 플랫폼 밖에 두고, 영상 제작·안전 검사·커뮤니티·외부 업로드를 한 화면에 묶는 것이 이 플랫폼의 현실적인 형태다. 사용자는 VOCALOID6 Editor·Piapro Studio NT2(미쿠)나 Synthesizer V Studio 2(테토)에서 만든 오디오와 프로젝트 파일을 올리고, 플랫폼은 그 파일에서 가사 타이밍과 입 모양을 자동으로 뽑아 리릭 모션, 레이어 분리 일러스트 기반의 2D 스켈레톤 캐릭터, 글로우·글리치·스트로브 같은 효과, 미쿠×테토 듀엣/VS 템플릿으로 MV를 완성하게 한다. 이 순서가 맞는 이유는 세 가지다. 첫째, 두 엔진 모두 공개 웹 SDK가 확인되지 않았다. 둘째, 수요가 확실하다. 미쿠·테토 듀엣곡 「メズマライザー」는 **2026-06-28 YouTube에서 가장 많이 재생된 보카로곡**이 됐고([ja.wikipedia](https://ja.wikipedia.org/wiki/%E3%83%A1%E3%82%BA%E3%83%9E%E3%83%A9%E3%82%A4%E3%82%B6%E3%83%BC)), 2026년 상반기 Billboard JAPAN 니코니코 보카로 차트 1·2위는 테토 곡이었다([Billboard JAPAN](https://www.billboard-japan.com/d_news/detail/162077)). 셋째, 대체하려는 시장이 뚜렷하다. 커버 영상 외주는 5천~3만 엔, 정지화·루프 리릭 MV는 5만~10만 엔이고([歌ってみた動画依頼](https://utattemitatukurikata.com/mvirai/), [Lancers](https://www.lancers.jp/menu/tag/%E3%83%9C%E3%82%AB%E3%83%AD)), 납기는 보통 14–50일이다([無印かげひと](https://note.com/kagehito_muji/n/nea1037033d4f)). 분석한 미쿠×테토 MV에는 강한 점멸이 반복적으로 나오므로 점멸 검사는 필수 기능으로 넣어야 한다([Vocaloid Lyrics Wiki: PPPP](https://vocaloidlyrics.miraheze.org/wiki/PPPP/TAK)). 외부 업로드도 버튼 하나로 끝나지 않는다. YouTube는 2026-06-01부터 업로드를 프로젝트당 하루 100회로 제한하고, 감사(audit)를 받지 않은 프로젝트에서 올린 영상은 비공개로 잠근다([Quota Calculator](https://developers.google.com/youtube/v3/determine_quota_cost), [Videos: insert](https://developers.google.com/youtube/v3/docs/videos/insert)). 그래서 승인 절차를 개발 착수와 동시에 시작해야 한다.

> **조사 기준일과 표기 규칙**: 기준일은 2026-10-07이다. 출처 링크가 붙은 문장은 확인된 사실이다. **(추정)**은 근거에서 끌어낸 추론, **(미확인)**은 출처로 확인하지 못한 항목, **(제안)**은 이 계획서가 내리는 설계 결정, **(계산)**은 인용한 수치로 직접 계산한 값이다. 법률 자문이 아니다.
>
> **조사의 한계**: 이 환경에서는 웹 페이지와 데이터 API(VocaDB, 니코니코, YouTube)를 직접 열 수 없었다. 근거는 웹 검색 결과(스니펫과 요약), 공개 GitHub 저장소(git으로 직접 열람), npm·PyPI 패키지 데이터(메타데이터와 tarball/wheel 내부 파일), MDN `@mdn/browser-compat-data` 8.1.4에서 나왔다. **MV는 한 편도 직접 보지 않았다.** 시각 묘사는 크레딧·인터뷰·기사·팬 고찰을 옮긴 것이다. 조사 도중 공용 웹 검색 한도(200회)가 소진되어 일부 항목은 검증하지 못했고, 그 목록과 검증 계획을 11절에 모았다.

## 1. 요약: 업로드형 MVP로 시작하고, 효과 엔진과 안전 검사에 투자한다

**무엇을 만드는가.** 이 계획서가 정의하는 제품은 세 부분으로 이뤄진다. 브라우저 한 화면에서 가사·캐릭터·효과·렌더를 끝내는 MV 스튜디오, 작품을 공개하고 좋아요·유효 조회수·댓글·랭킹으로 반응을 주고받는 커뮤니티, 완성 영상을 YouTube·TikTok·니코니코 등으로 옮기는 내보내기 계층이다. 미쿠 V6(2026-04-14 출시)와 SV2 테토(2025-11-27 출시)는 모두 로컬 에디터·DAW 플러그인 제품이다([Crypton](https://www.crypton.co.jp/cfm/news/2026/04/14miku_v6), [AHS](https://www.ah-soft.com/press/synth-v/20251030.html)). Yamaha는 법인 문의 창구만 공개하고([vocaloid.com/business](https://www.vocaloid.com/business/)), SV 엔진을 다른 소프트웨어에 상용으로 넣으려면 별도로 문의해야 한다([AHS SV EULA](https://www.ah-soft.com/synth-v/eula_e.html)). 그래서 플랫폼 안에서 미쿠·테토 목소리를 직접 합성하는 기능은 범위에서 뺀다(제안).

**왜 이 형태가 시장에 맞는가.** 히트 MV 상당수는 보카로P가 혼자 또는 일러스트레이터 1명과 만들었다. 「オーバーライド」는 P가 레이어 분리 일러스트를 받아 직접 움직였고([Billboard JAPAN 인터뷰](https://www.billboard-japan.com/special/detail/4683)), 「人マニア」는 P가 3D 테토를 직접 모델링했다([Real Sound](https://realsound.jp/tech/2024/02/post-1576883.html)). 이런 제작자가 쓰는 도구 생태계는 글로우(Deep Glow·Saber), 글리치 스크립트, 자동 리릭 모션 스크립트에 집중돼 있다([nanika.design](https://nanika.design/blog/1651/), [FLAPPER](https://seguimiii.com/aviutl-tech/autolyricmotion)). 따라서 MVP의 핵심은 3D나 풀 애니메이션이 아니다. "리릭 모션 + 컷아웃(파츠 분리) 캐릭터 + 화면 효과 + 듀엣 템플릿"이다(제안).

**어떻게 만드는가.** 렌더링은 PixiJS v8(MIT, WebGPU/WebGL2)로 하고, 인코딩은 WebCodecs와 Mediabunny(MPL-2.0)로 한다. 타임라인과 합성기는 직접 만든다. 같은 렌더 런타임을 서버의 headless Chromium에서도 돌려 미리보기와 결과물을 일치시킨다(제안, 근거 2.4절과 6절). 다음 후보들은 쓰지 않는다. Remotion은 직원 4명 이상 기업에 렌더당 $0.01, 월 최소 $100을 받는다([Remotion 가격](https://www.remotion.dev/docs/license/pricing)). FFmpeg.wasm 코어는 GPL이고([npm @ffmpeg/core](https://www.npmjs.com/package/@ffmpeg/core)), Theatre.js studio는 AGPL이다([npm](https://www.npmjs.com/package/@theatre/studio)). Spine은 사용자마다 에디터 라이선스가 필요하고([spine-core LICENSE](https://www.npmjs.com/package/@esotericsoftware/spine-core)), Live2D는 사용자 모델 임포트 앱에 사전 심사와 특별 계약을 요구한다([Live2D](https://www.live2d.com/en/sdk/license/expandable/)).

**핵심 결정 11가지**(모두 제안이며, 근거는 표의 절에 있다)

| # | 결정 | 핵심 근거 | 절 |
|---|---|---|---|
| D1 | 가창 합성은 플랫폼 밖에 둔다. 마스터 오디오(필수), 가수별 보컬 스템(권장), 엔진 프로젝트 파일(선택)을 받는다 | 공개 SDK·웹 API 미발견, VOCALOID6 라이선스는 PC 1대 한정, SV 임베딩은 별도 문의 | 2.1 |
| D2 | .svp·.vpr·.ppsf(NT)·.ustx·MIDI를 파싱해 가사 타이밍과 5모음 립싱크를 자동 생성한다 | 형식이 JSON/zip+JSON/YAML이고, 오픈소스 파서(UtaFormatix3·LibreSVIP·OpenUtau)가 있다 | 2.1, 5 |
| D3 | MVP 제작 기능은 리릭 모션, PSD 컷아웃 리그(FK 본), P0 효과 12종, 템플릿 4종(1장 그림+가사, 루프 리릭, 미쿠×테토 듀엣, VS 분할)이다 | P 자작형 히트, 리릭 툴 수요, 외주 가격대 | 2.3, 4, 5 |
| D4 | 타임라인·합성 엔진을 직접 만든다(PixiJS v8 + WebCodecs + Mediabunny) | Remotion 렌더 과금, GPL/AGPL 회피 | 2.4, 6 |
| D5 | 같은 렌더 런타임을 브라우저와 서버 headless Chromium에서 실행한다 | 미리보기와 내보내기 일치, 미지원 브라우저 폴백 | 2.4, 6 |
| D6 | 점멸 안전을 3중으로 건다: 프리셋 상한, 실시간 미터, 렌더 후 IRIS 게이트 | WCAG 2.3.1(1초 3회), 미쿠×테토 MV 점멸 경고 | 2.6, 5.6 |
| D7 | 공개 조회수는 "유효 조회"만 센다(최소 시청 시간, 중복 제거, 지연 집계). 랭킹은 부문별로 나눈다 | PeerTube 10초·1시간 규칙, 니코니코 2019 랭킹 개편 | 2.6, 4 |
| D8 | 계보(부모 작품)와 자산별 권리·AI 사용 신고를 1급 데이터로 둔다 | 니코니코 コンテンツツリー, channel의 재이용 허가, PCL 비영리 조건 | 2.6, 4 |
| D9 | YouTube 직접 업로드는 개발과 동시에 검증·감사를 신청한다. 통과 전에는 MP4 다운로드와 비공개 업로드로 운영하고, 니코니코는 투고 어시스트로 지원한다 | 2020-07-28 이후 생성된 미검증 프로젝트는 비공개 잠금, 하루 100회 업로드 버킷, 니코니코 공식 API 부재 | 8 |
| D10 | 크립톤과 협의하기 전에는 운영사 UI·마케팅·기본 리그에 미쿠·테토를 쓰지 않는다. 서비스명에 「VOCALOID」「ボカロ」「初音ミク」를 넣지 않는다 | V4X EULA 상표 조항, PCL "비영리·무상", 2024-12-04 크립톤 성명 | 2.6, 9 |
| D11 | 모듈 그래프를 "하향 단방향 트리 + ⚠️공유 기반 7개"로 제한한다. Nx·dependency-cruiser·eslint-plugin-boundaries로 강제한다 | 순환 금지 도구 3종 모두 MIT | 6 |

## 2. 조사 근거 요약: 여섯 영역의 사실이 같은 설계를 가리킨다

### 2.1 엔진과 연동: 미쿠·테토 목소리는 사용자의 로컬 에디터에서만 나온다

**미쿠는 세 계통이 동시에 팔리고 있다.** 하나는 크립톤 자체 엔진인 「初音ミク NT (Ver.2)」와 Piapro Studio NT2다. 2025-03-18 정식 출시했고 ¥19,800이며, VST3/AU 플러그인과 스탠드얼론으로 동작한다([SONICWIRE](https://sonicwire.com/news/blog/2025/03/nt-ver-2), [SONICWIRE NT](https://sonicwire.com/product/virtualsinger/special/mikunt)). 또 하나는 VOCALOID6(VOCALOID:AI) 기반 「初音ミク V6」다. 2026-04-14 출시했고 스타터팩 ¥24,200, 보이스뱅크 ¥10,780이다([Crypton](https://www.crypton.co.jp/cfm/news/2026/04/14miku_v6), [gamebiz](https://gamebiz.jp/news/421198)). 여기에 쓰는 VOCALOID6 Editor는 스탠드얼론, VST/AU, ARA2를 지원한다([시마무라](https://info.shimamura.co.jp/digital/newitem/2026/02/164674)). 나머지 하나는 구형 V4X다([V4X EULA](https://ec.crypton.co.jp/download/pdf/eula_MIKUV4X.pdf)). VOCALOID6 Editor는 6.13.2(2026-08-26)까지 나왔고, 이후 보안 수정을 담은 6.13.3이 배포됐다(검색 요약, [vocaloid.com](https://www.vocaloid.com/en/news/support_64/)). Yamaha가 임베딩을 허용한 선례로는 2015년 「VOCALOID SDK for Unity」([gamebiz](https://gamebiz.jp/news/154379))와 서버형 Web API였던 NetVOCALOID([vocaloid-fan.com](https://vocaloid-fan.com/product/netvocaloid.html))가 있다. 다만 현행 조건은 공개돼 있지 않고 법인 문의 창구만 있다([vocaloid.com/business](https://www.vocaloid.com/business/)). 2024-10에 시작한 「Yamaha Music Connect API」가 가창 합성을 포함하는지는 **미확인**이다([Yamaha](https://www.yamaha.com/ja/news_release/2024/24100701/)).

**테토는 Synthesizer V 계열이 주력이다.** SV Studio 2 Pro는 2025-03-21 출시했고 $99다([Attack Magazine](https://www.attackmagazine.com/news/dreamtonics-announces-the-release-of-synthesizer-v-studio-2-pro/), [Sound On Sound](https://www.soundonsound.com/node/4933228)). VST3/AU/AAX 인스트루먼트 플러그인과 ARA 플러그인을 제공하고([SV2 문서](https://sv2.docs.dreamtonics.com/en/plugins)), Lua·JavaScript 스크립팅은 Pro 전용이다([Scripting Manual](https://resource.dreamtonics.com/scripting/)). 「Synthesizer V 2 AI 重音テト」는 2025-11-27 발매됐고 다운로드판이 ¥9,680이다([AHS](https://www.ah-soft.com/press/synth-v/20251030.html), [AHS 제품](https://www.ah-soft.com/synth-v/teto2/)). 보컬 스타일 수는 출처마다 다르다. Wikipedia는 7종, 소매 리스팅은 "4 Vocal Modes"로 적었다(불일치, [Wikipedia](https://en.wikipedia.org/wiki/Kasane_Teto)). AHS판 EULA는 다른 소프트웨어에 상용으로 임베딩하려면 문의하라고 요구한다([AHS SV EULA](https://www.ah-soft.com/synth-v/eula_e.html)). Dreamtonics도 EULA 범위를 넘는 사업 활동에는 승인이 필요하다고 밝혔다([Dreamtonics 성명](https://dreamtonics.com/statement-on-business-activities-related-to-synthesizer-v/)).

**직접 호스팅할 수 있는 오픈 스택에는 상업용 함정이 있다.** OpenUtau는 MIT이고 2026-10-07에도 커밋이 이어지고 있다([LICENSE](https://github.com/stakira/OpenUtau/blob/HEAD/LICENSE.txt)). DiffSinger는 Apache-2.0이다([LICENSE](https://github.com/openvpi/DiffSinger/blob/HEAD/LICENSE)). 그런데 사실상 표준인 커뮤니티 보코더 가중치는 **CC BY-NC-SA 4.0(비상업)**이다([DiffSinger Community Vocoders](https://openvpi.github.io/vocoders/)). 미쿠의 공식 오픈 모델은 없고, DiffSinger 자체도 본인 동의 없이 타인의 목소리를 생성하는 것을 금지한다([DiffSinger README](https://github.com/openvpi/DiffSinger/blob/HEAD/README.md)). Dreamtonics 보이스로 생성한 음성을 머신러닝 학습에 쓰는 것도 금지돼 있다([Dreamtonics Terms](https://dreamtonics.com/terms/)). 팬이 만든 미쿠·테토 AI 음성 모델을 플랫폼이 제공하는 선택지는 이 시점에서 닫힌다.

**프로젝트 파일은 대부분 텍스트라 가사 타이밍 자동화의 재료가 된다.**

| 형식 | 엔진 | 직렬화 | 시간 단위·핵심 필드 | 파서 근거 |
|---|---|---|---|---|
| .svp | SV1/SV2 | JSON, 끝에 NUL 바이트. SV2는 `"mouthOpening":` 키로 판별 | blick(4분음표 = 705,600,000), 노트 `onset`·`duration`·`lyrics`·`phonemes` | [OpenUtau SVP.cs](https://github.com/stakira/OpenUtau/blob/HEAD/OpenUtau.Core/Format/SVP.cs), [Formats.cs](https://github.com/stakira/OpenUtau/blob/HEAD/OpenUtau.Core/Format/Formats.cs) |
| .vpr | VOCALOID5/6 | zip 안의 `Project/sequence.json` | 노트 `pos`·`duration`·`number`·`lyric`·`phoneme`, AI 필드 `phonemePositions` | [UtaFormatix3 Vpr.kt](https://github.com/sdercolin/utaformatix3/blob/HEAD/core/src/main/kotlin/core/io/Vpr.kt), [LibreSVIP vpr](https://github.com/SoulMelody/LibreSVIP/blob/HEAD/libresvip/plugins/vpr/model.py) |
| .ppsf | Piapro Studio NT | zip+JSON(`ppsf.json`). 구판은 커스텀 바이너리 | `pos`·`length`·`lyric`·`note_number`·`symbols` | [UtaFormatix3 Ppsf.kt](https://github.com/sdercolin/utaformatix3/blob/HEAD/core/src/main/kotlin/core/io/Ppsf.kt) |
| .ustx | OpenUtau | YAML 1.2 | 4분음표 = 480 tick, `position`·`duration`·`tone`·`lyric` | [USTX 위키](https://github.com/stakira/OpenUtau/wiki/USTX-file-format) |
| .vsqx / MIDI / MusicXML | VOCALOID3·4 / 범용 | XML / SMF / XML | 노트·템포·박자 | [LibreSVIP 형식 일람](https://github.com/SoulMelody/LibreSVIP/blob/HEAD/docs/project_formats.md) |

변환기는 두 개가 핵심이다. UtaFormatix3(Apache-2.0, Kotlin/JS)는 .ppsf(NT)를 가져올 수 있고 코어를 JS 라이브러리로 빌드한다([README](https://github.com/sdercolin/utaformatix3/blob/HEAD/README.md)). LibreSVIP(MIT, Python)는 ASS·LRC·SRT 자막을 생성하는 플러그인을 갖고 있다([ass 플러그인](https://github.com/SoulMelody/LibreSVIP/tree/HEAD/libresvip/plugins/ass)). 다만 대부분의 형식에서 확정된 음소 타이밍은 엔진이 렌더할 때 계산하고 파일에는 저장하지 않는다. 그래서 립싱크는 "가나 → 모음" 근사나 오디오 정렬로 보완해야 한다(추정).

**통합 옵션 비교와 결정**(노트 01의 종합을 이 계획서의 결정으로 옮김)

| 옵션 | 미쿠·테토 공식 음성 | 난이도 | 법적 전제 | 결정 |
|---|---|---|---|---|
| A. 외부 에디터에서 렌더 → 오디오·프로젝트 업로드 | 가능(사용자 라이선스) | 낮음 | 이용자 EULA 범위, 플랫폼은 호스팅 | **MVP 채택**(제안) |
| B. 플랫폼 배포 스크립트(SV2 Pro 스크립트로 가사 타이밍 JSON 내보내기 등) | 가능 | 중간 | 로컬 사용 | Phase 2 보조(제안). SV Basic 사용자는 쓸 수 없음 |
| C. 자체 웹 피아노롤 + 서버 DiffSinger/OpenUtau/VOICEVOX | 불가(상용 허용 보이스만) | 높음 | 보이스별 라이선스, 보코더 NC 문제 | Phase 4 조건부(제안) |
| D. Yamaha·Crypton·Dreamtonics/AHS와 B2B 서버 렌더 | 계약 시 가능 | 높음 | 개별 계약, 조건 비공개 | 협상만 병행(제안) |
| E. 팬 제작 미쿠·테토 AI 모델 | — | — | 권리자·성우 동의 없음, 학습 금지 조항 | **배제** |

크리에이터가 실제로 내놓는 산출물도 옵션 A와 맞는다. 미쿠와 테토를 한 편집기에서 다룰 방법은 없다. 그래서 DAW 안에서 각 엔진을 플러그인·ARA로 나란히 쓰거나, 각 에디터에서 WAV를 렌더해 믹스한다(노트 01 종합). 실제로 「T氏の話を信じるな」는 初音ミク V4X와 重音テトSV를 한 곡에 섞었다([VocaDB](https://vocadb.net/S/805591)). ARA로 작업하면 타임라인 기준이 DAW이므로, 엔진 프로젝트 파일의 0초와 마스터 오디오의 0초가 어긋날 수 있다. 오디오 정렬(오프셋) 단계가 필수인 이유다(추정).

### 2.2 미쿠×테토 트렌드: 한때의 유행이 아니라 3년째 차트 상위를 지키는 정전이다

**타임라인.** 「Synthesizer V AI 重音テト」가 2023-04-27 발매된 뒤([AHS](https://www.ah-soft.com/press/synth-v/20230403.html)) 「人マニア」(2023-08-24)와 「オーバーライド」(2023-11-29)로 테토 솔로 히트가 시작됐다([ja.wikipedia 人マニア](https://ja.wikipedia.org/wiki/%E4%BA%BA%E3%83%9E%E3%83%8B%E3%82%A2), [ja.wikipedia オーバーライド](https://ja.wikipedia.org/wiki/%E3%82%AA%E3%83%BC%E3%83%90%E3%83%BC%E3%83%A9%E3%82%A4%E3%83%89_%28%E6%9B%B2%29)). 2024-04-27 공개된 サツキ 「メズマライザー」(初音ミク·重音テトSV)는 **13일 만에 YouTube 1,000만**(2024-05-11, 보카로 오리지널 사상 최속)을 넘었고, **1억**(2024-11-17, YouTube 보카로곡 사상 최속)과 **2억**(2026-05-20)을 거쳐 2026-06-28에 **YouTube 최다 재생 보카로곡**이 됐다([ja.wikipedia](https://ja.wikipedia.org/wiki/%E3%83%A1%E3%82%BA%E3%83%9E%E3%83%A9%E3%82%A4%E3%82%B6%E3%83%BC)).

테토 곡의 1억 돌파는 이어졌다. 「テトリス」는 2025-12-01 테토 단독곡 최초로 1억을 넘었고([ja.wikipedia テトリス](https://ja.wikipedia.org/wiki/%E3%83%86%E3%83%88%E3%83%AA%E3%82%B9_%28%E6%9F%8A%E3%83%9E%E3%82%B0%E3%83%8D%E3%82%BF%E3%82%A4%E3%83%88%E3%81%AE%E6%9B%B2%29)), 「オーバーライド」는 2026-04-21 1억을 넘었다. 미쿠 VS 테토 대립곡 「ダイダイダイダイダイキライ」는 2025-04-25 1,000만을 넘었다([ja.wikipedia](https://ja.wikipedia.org/wiki/%E3%83%80%E3%82%A4%E3%83%80%E3%82%A4%E3%83%80%E3%82%A4%E3%83%80%E3%82%A4%E3%83%80%E3%82%A4%E3%82%AD%E3%83%A9%E3%82%A4)). 2026년에는 ボカコレ2026冬 1·2위를 테토 곡 「デロスサントス」와 「ブレインロット」가 차지했고([初音ミクWiki](https://w.atwiki.jp/hmiku/pages/71031.html)), 「ブレインロット」는 공개 39일 만에 1,000만을 넘었다([KAI-YOU](https://kai-you.net/article/95094)).

**차트 점유.** 아래 집계 대상인 Billboard JAPAN 'ニコニコ VOCALOID SONGS'는 재생수, 2차 창작 수, 코멘트, 좋아요에 가중치를 둔다([Billboard JAPAN](https://www.billboard-japan.com/d_news/detail/156167)).

| 집계 | 테토 관여곡의 TOP10 진입 | 대표 순위 | 출처 |
|---|---|---|---|
| 2024 연간 | 최소 2곡(추정 최대 4곡) | 1위 オーバーライド, 2위 メズマライザー | [Billboard JAPAN](https://www.billboard-japan.com/d_news/detail/144219) |
| 2025 연간 | 4곡 | 2위 テトリス, 3위 メズマライザー, 4위 オーバーライド, 7위 ダイダイダイダイダイキライ (1위는 미쿠 「モニタリング」) | [Billboard JAPAN](https://www.billboard-japan.com/d_news/detail/156167) |
| 2026 상반기 | 4곡 | 1위 デロスサントス, 2위 ブレインロット, 5위 メズマライザー, 8위 テトリス | [Billboard JAPAN](https://www.billboard-japan.com/d_news/detail/162077) |

**듀엣은 드물지만 파급력이 크다.** 노트가 정리한 2023~2026년 대표곡 20곡을 나누면 테토 단독 9곡, 미쿠 단독 7곡, 미쿠+테토 4곡이다(분류는 추정). 그 4곡 가운데 하나가 장르 최다 재생곡이다. 그래서 듀엣 템플릿은 "자주 쓰이지는 않아도 성공했을 때 효과가 큰 포맷"으로 다룬다(추정).

**인기 요인 가운데 출처가 있는 것은 세 가지다.** 첫째는 기술적 계기다. SV AI판으로 테토 가창이 자연스러워지면서 투고가 늘었다([Real Sound](https://realsound.jp/2024/10/post-1803284.html)). 둘째는 2008년 만우절의 "가짜 보컬로이드"에서 시작한 기원 서사다([AHS](https://www.ah-soft.com/press/synth-v/20230403.html)). 셋째는 듀엣의 대비 연출이다. 「メズマライザー」에서는 최면에 빠지는 미쿠와 저항하는 테토가 맞서고([en.wikipedia](https://en.wikipedia.org/wiki/Mesmerizer_%28song%29)), 「ダイダイダイダイダイキライ」에서는 둘이 말다툼을 벌인다. 색 대비(미쿠의 청록과 테토의 적색)와 음색 대비는 그럴듯하지만 직접 출처가 없어 **추정**으로 남긴다.

**2차 창작이 트렌드의 엔진이다.** 「メズマライザー」 MV를 만든 channel은 공개 8일 뒤인 2024-05-05에 공지를 냈다. 영상 재이용과 트레이스는 허가 없이 할 수 있고, 상품화와 무편집 전재는 금지한다는 내용이다([X](https://x.com/x_cast_x/status/1787051821565145369)). 이 영상은 Know Your Meme 항목이 생길 만큼 밈이 됐다([KYM](https://knowyourmeme.com/memes/mesmerizer-by-hatsune-miku-kasane-teto/photos)). 니코니코에서는 「オーバーライド(吉田夜世)」 태그 관련 영상만 **1,407건**이다(2026-10 검색 시점, [니코니코 태그](https://www.nicovideo.jp/tag/%E3%82%AA%E3%83%BC%E3%83%90%E3%83%BC%E3%83%A9%E3%82%A4%E3%83%89%28%E5%90%89%E7%94%B0%E5%A4%9C%E4%B8%96%29)). 2차 창작 수가 Billboard 니코니코 차트에 반영되므로([Billboard JAPAN](https://www.billboard-japan.com/d_news/detail/144219)), 플랫폼의 "파생작 수"와 계보 표시는 크리에이터에게 실질적인 동기가 된다(추정). 미쿠의 기반도 단단하다. 매지컬 미라이 2026은 9만 명 이상을 모았다([piapro blog](https://blog.piapro.net/2026/09/b2609041.html)).

다만 2026-09에는 자작 UTAU 음원 カロガド를 쓴 「デビットビット」가 주간 차트 4주 연속 1위를 했다([Yahoo!ニュース](https://news.yahoo.co.jp/articles/7e91c10cb2afbf68989d27bc1b22bd453925cd7e)). 따라서 데이터 모델과 템플릿은 미쿠·테토 전용으로 굳히지 말고 "가수 N명, 캐릭터 N명"으로 일반화한다(제안).

### 2.3 MV 비주얼·효과: 히트작은 강한 모티프 하나와 혼자 다룰 수 있는 기법으로 만들어졌다

**제작 방식은 세 유형이다.** 아래 내용은 MV를 직접 본 결과가 아니라 크레딧과 기사를 바탕으로 정리했다.

| 유형 | 사례 | 근거 |
|---|---|---|
| ① 보카로P 자작형 | 「オーバーライド」: 曲·詞·動画 吉田夜世, イラスト シシア. P가 "밈 같은 움직임"을 처음부터 구상하고 소재 분리를 세세하게 요청했다 | [Billboard JAPAN](https://www.billboard-japan.com/special/detail/4683) |
| | 「テトリス」: 映像 柊マグネタイト, 絵 瀬奈悠太. 회전하는 테토와 텍스트 개그가 나온다 | [ja.wikipedia](https://ja.wikipedia.org/wiki/%E3%83%86%E3%83%88%E3%83%AA%E3%82%B9_%28%E6%9F%8A%E3%83%9E%E3%82%B0%E3%83%8D%E3%82%BF%E3%82%A4%E3%83%88%E3%81%AE%E6%9B%B2%29), [ダ・ヴィンチWeb](https://ddnavi.com/article/d1454757/a/) |
| | 「人マニア」: P가 3D 테토를 직접 모델링했다 | [Real Sound](https://realsound.jp/tech/2024/02/post-1576883.html) |
| ② 일러스트레이터 1인형 | 「メズマライザー」: channel이 일러스트와 영상을 모두 맡았다. サツキ와 공유한 테마는 "최면술" 하나였다 | [KOTORA JOURNAL](https://www.kotora.jp/c/113950-2/) |
| | 「ダイダイダイダイダイキライ」: MV 暗闇まよい. 『うごくメモ帳』풍 애니메이션과 글리치 아트 연출을 썼다 | [pixiv百科事典](https://dic.pixiv.net/a/%E3%83%80%E3%82%A4%E3%83%80%E3%82%A4%E3%83%80%E3%82%A4%E3%83%80%E3%82%A4%E3%83%80%E3%82%A4%E3%82%AD%E3%83%A9%E3%82%A4) |
| ③ 스튜디오·팀형 | 「モニタリング」: OTOIRO 작화팀 | [OTOIRO](https://otoiro.co.jp/topics/104605/) |
| | 「PPPP」(미쿠·테토, 2025-09-17): 수작업 2D, 3DCG, 도트 애니메이션, 이펙트 작화를 섞었다 | [Patreon](https://www.patreon.com/posts/mv-tak-pppp-feat-141557760) |

플랫폼이 직접 대체할 수 있는 영역은 ①과 ②의 기법이다. ③의 표현 요소는 프리셋으로 흉내 내는 수준이 현실적이다(추정).

**미쿠×테토 MV의 문법을 기능으로 번역하면 다음과 같다.**

| 반복 요소 | 근거 MV | 플랫폼 기능(제안) |
|---|---|---|
| 두 캐릭터의 대비 서사(최면/저항, 말다툼) | メズマライザー, ダイダイダイダイダイキライ | 듀엣·VS 템플릿, 가수별 활성 강조, 분할 화면 |
| 최면 소용돌이 역할을 하는 스트라이프, 진자처럼 흔들리는 프레임(팬 고찰) | メズマライザー ([KOTORA](https://www.kotora.jp/c/113950-2/)) | 진자 스윙 프리셋(P0), 최면 모티프 팩(P2, 패턴 검사 필수) |
| 영어 번역이 들어간 MV, 비비드한 색채 | メズマライザー ([Billboard JAPAN 칼럼](https://www.billboard-japan.com/special/detail/5057)) | 번역 자막 트랙, 그라디언트 맵·색보정 |
| 밈 같은 반복 모션, 소소한 개그 | オーバーライド, テトリス | 루프 모션 프리셋, 텍스트 개그·스티커 레이어 |
| 우고메모풍 저프레임, 글리치, 공식 안무 | ダイダイダイダイダイキライ | 스프라이트 교체(P1), 글리치(P0), 댄스는 3D(P2) |
| 3D 댄스, 실사+CG 합성, 도트 애니 | 人マニア, イガク([ja.wikipedia](https://ja.wikipedia.org/wiki/%E3%82%A4%E3%82%AC%E3%82%AF)), PPPP | VRM 레이어(P1), 영상 클립 합성(P1), 픽셀화(P1) |
| 백룸·리미널 스페이스 배경 | ブレインロット, ループザルーム(노트 02) | 배경 프리셋 팩(P1) |

**효과의 빈도는 과소 집계되어 있다.** 12곡 가운데 출처로 확인된 효과는 다음과 같다. 가사·텍스트 그래픽 3곡, 레이어 분리 컷아웃 4곡, 3DCG·실사 합성 4곡, 수작업 프레임 애니 3곡, 글리치 1곡, 점멸 1곡(표 밖 3곡 추가). 글로우를 쓴 MV는 확인하지 못했다. 그러나 보카로 MV의 정석 AE 플러그인으로 Deep Glow 2와 Saber가 소개되고([nanika.design](https://nanika.design/blog/1651/)), AviUtl용 블록 노이즈·디스플레이스먼트·데이터모시 스크립트가 따로 유통된다([FLAPPER 블록 노이즈](https://seguimiii.com/aviutl-tech/anmblocknoise), [FLAPPER 디스플레이스먼트](https://seguimiii.com/aviutl-tech/displacementmapdeglitch), [데이터모시](https://jisyu-seisaku.net/archives/489)). 영상을 보지 않고 출처의 기술만 셌으므로 실제 빈도는 이보다 높다(추정). RGB 분리, CRT·VHS, 하프톤은 개별 MV 근거가 없어 **추정 수요**로 분류한다.

**타이포그래피는 명조가 강하다.** 18년치 180곡을 분석한 결과 1위가 HG明朝, 4위가 游明朝, 5위가 MS明朝였다([いからげ note](https://note.com/ika_rage/n/n31662be9d918)). DECO\*27은 「チェリーポップ」 MV 전용 폰트를 패스 데이터로 무료 배포했다(2025-09, [ITmedia](https://www.itmedia.co.jp/news/articles/2509/02/news105.html)).

**제작 공정의 고통은 네 가지다.** 첫째, **가사 타이밍이 수작업이다.** AE에서는 구절마다 텍스트 레이어를 만들고 한 글자씩 불투명도 애니메이터를 건다([グローバルゲート](https://www.globalgate.co.jp/blog/maiking-lyric-video-using-aftereffects)). AviUtl로 노래방식 자막을 만드는 일도 상당히 번거롭다([AKETAMA](https://aketama.work/aviutl-karaoke)). 이 수요 때문에 재생하면서 "SET"을 누르는 탭 싱크 도구가 나왔다([kanoの缶詰知識](https://eternallways.com/aviutltool)). 둘째, **도구의 진입 장벽이 높다.** AviUtl2(2025-07-07 베타)는 AVX2 CPU와 DirectX 11.3·ROV GPU를 요구한다([GIGAZINE](https://www.gigazine.net/news/20250708-aviutl-exedit2-beta1/), [roboin](https://roboin.io/article/2025/07/08/aviutl-returns-after-6-years-with-64-bit-support-and-built-in-exedit)). 셋째, **비용과 기간이 크다.** 오리지널 MV 외주는 4만~15만 엔이고([ちゃすく note](https://note.com/chask/n/nd8ffb16d536c)), 납기는 14–50일이다.

넷째, **점멸 표기 책임이 제작자에게 있다.** MV 제작자 Nehan은 격한 점멸이 있으면 「点滅注意」를 표기하자고 동업자들에게 호소했다([X](https://x.com/Na_yu_ta_ta/status/1759983307624955983)). Vocaloid Lyrics Wiki는 「PPPP」, 「ダダダダダル」(2026-03-15), 「ヤラララ」, 「スプリットダンス」에 광과민성 경고를 붙였다([PPPP](https://vocaloidlyrics.miraheze.org/wiki/PPPP/TAK), [ダダダダダル](https://vocaloidlyrics.miraheze.org/wiki/%E3%83%80%E3%83%80%E3%83%80%E3%83%80%E3%83%80%E3%83%AB_(Dadadadadaru))). **네 곡 모두 테토가 부르고, 그중 세 곡은 미쿠×테토 듀엣이다.** 표본이 작아 일반화할 수는 없지만, 이 장르를 겨냥한 플랫폼이 점멸 검사를 선택 기능으로 둘 수 없다는 근거로는 충분하다(추정).

### 2.4 웹 기술 스택: 2026년에는 브라우저 렌더가 주요 데스크톱 브라우저 전부에서 성립한다

| 영역 | 확인된 사실(버전·라이선스·날짜) | 결정(제안) |
|---|---|---|
| 인코딩 | WebCodecs Video Encoder/Decoder는 Chrome·Edge 94, Firefox 130, Safari 16.4부터 지원하고 Audio는 Safari 26부터 지원한다. Firefox Android는 미지원이다([BCD 8.1.4](https://www.npmjs.com/package/@mdn/browser-compat-data), [WebKit](https://webkit.org/blog/17333/webkit-features-in-safari-26-0/)) | 클라이언트 렌더를 기본으로 한다. `isConfigSupported()`로 런타임에 판정하고 실패하면 서버로 넘긴다 |
| 먹싱 | Mediabunny 1.61.3(MPL-2.0, 2026-10-05). mp4-muxer·webm-muxer는 이 라이브러리로 대체되며 deprecated됐다([npm mediabunny](https://www.npmjs.com/package/mediabunny), [npm mp4-muxer](https://www.npmjs.com/package/mp4-muxer)) | 채택. 소스를 수정하면 해당 파일만 공개 의무가 생긴다 |
| FFmpeg.wasm | 코어는 GPL-2.0-or-later다. 100MB WAV→MP3 변환이 네이티브 약 0.8초, WASM 약 4.5초로 느리다([npm](https://www.npmjs.com/package/@ffmpeg/core), [DEV](https://dev.to/baojian_yuan/moving-ffmpeg-to-the-browser-how-i-saved-100-on-server-costs-using-webassembly-4l9f)) | 클라이언트에서 배제 |
| GPU | WebGPU는 Chrome 113(Linux는 144부터 Intel Gen12+), Firefox 141(Windows)·145(macOS Tahoe의 Apple Silicon)에서 지원하고 Safari 26부터 지원한다. Firefox의 Linux·Intel Mac·Android는 미지원이다. WebGL2는 사실상 보편적이다([BCD](https://www.npmjs.com/package/@mdn/browser-compat-data)) | WebGPU를 우선하고 WebGL2 폴백을 필수로 둔다 |
| 2D 합성 | pixi.js 8.22.0, pixi-filters 6.1.5(MIT). v8은 WebGPU를 핵심 패러다임으로 통합했다([npm](https://www.npmjs.com/package/pixi.js), [PixiJS blog](https://pixijs.com/blog/pixi-v8-launches)) | 합성 코어로 채택 |
| 3D | three 0.186.1(MIT, WebGPURenderer·TSL 포함), @pixiv/three-vrm 3.5.5(MIT, 라이선스 메타와 립싱크 표정 `aa/ih/ou/ee/oh` 포함). three.js의 MMD 모듈은 r172(2024-12-31)에서 제거됐다([npm three](https://www.npmjs.com/package/three), [npm three-vrm](https://www.npmjs.com/package/@pixiv/three-vrm)) | VRM은 P1. MMD는 babylon-mmd(MIT)로 P2 |
| 셰이더 자산 | gl-transitions는 125종 중 MIT 123, BSD 2. LYGIA는 Prosperity/Patron 라이선스다([npm gl-transitions](https://www.npmjs.com/package/gl-transitions), [npm lygia](https://www.npmjs.com/package/lygia)) | gl-transitions 채택, LYGIA는 라이선스 구매 전까지 금지 |
| 영상 프레임워크 | Remotion은 직원 3명 이하 무료, Automators는 렌더당 $0.01·월 최소 $100이며 5.0에서 라이선스 변경을 예고했다. Theatre studio는 AGPL, Etro는 GPL-3.0, Diffusion Studio는 워터마크 제거가 유료다([Remotion](https://www.npmjs.com/package/remotion), [etro](https://www.npmjs.com/package/etro), [Diffusion Studio](https://www.npmjs.com/package/@diffusionstudio/core)) | 모두 의존하지 않는다. "프레임 = 시간의 순수 함수" 설계만 차용한다 |
| 2D 리깅 | Spine은 사용자마다 에디터 라이선스가 필요하다. Live2D는 Expandable Application 심사와 특별 계약이 필요하다. Rive 런타임은 MIT지만 내보내기에 유료 좌석이 필요하다([Spine](https://www.npmjs.com/package/@esotericsoftware/spine-core), [Live2D](https://www.live2d.com/en/sdk/license/expandable/), [Rive](https://rive.app/blog/rive-s-new-9-mo-plan)) | 자체 본·메시 런타임을 만든다. 노트 추정 공수는 런타임 3–6인월, 에디터 6–12인월 |
| 일본어 텍스트 | Noto Sans JP, M PLUS, Dela Gothic One, DotGothic16, Zen Maru Gothic은 OFL-1.1, Kosugi Maru는 Apache-2.0이다. Noto Sans JP 400은 unicode-range 조각 120개, 합계 약 2.77MB다. troika-three-text에는 세로쓰기 옵션이 없다([npm fontsource](https://www.npmjs.com/package/@fontsource/noto-sans-jp), [subset-font](https://www.npmjs.com/package/subset-font), [troika](https://www.npmjs.com/package/troika-three-text)) | UI는 조각 단위로 로딩하고, 렌더용 폰트는 프로젝트 가사 글자만 서브셋한다. 세로쓰기는 자체 레이아웃으로 구현한다(P2) |
| 오디오 분석 | 허용형: web-audio-beat-detector(MIT), loudness-worklet(MIT, BS.1770-5), Demucs 4.1.0(MIT), whisperX(BSD-2), MFA(MIT), pyopenjtalk(MIT). 배제: essentia.js(AGPL), aubio(GPL), madmom(비상업), whisper-timestamped(동봉 LICENSE가 AGPL), MMS 정렬 모델(CC-BY-NC)([PyPI whisper-timestamped](https://pypi.org/project/whisper-timestamped/), [PyPI ctc-forced-aligner](https://pypi.org/project/ctc-forced-aligner/), [npm loudness-worklet](https://www.npmjs.com/package/loudness-worklet)) | 허용형만 쓴다. 프로젝트 파일 기반 립싱크를 1순위로 둔다 |
| 서버 | puppeteer·playwright(Apache-2.0)의 headless Chromium GL 옵션은 `angle`·`egl`·`vulkan`·`swiftshader` 등이다. shaka-packager는 BSD-3, hls.js는 Apache-2.0이다([npm @remotion/renderer](https://www.npmjs.com/package/@remotion/renderer), [npm shaka-packager](https://www.npmjs.com/package/shaka-packager)) | 서버 렌더와 HLS 패키징에 채택한다. 서버 내부에서만 쓰는 GPL FFmpeg는 배포에 해당하지 않는다(추정, 법무 확인) |
| 경계 강제 | Nx 23.2.1 `enforce-module-boundaries`(`depConstraints`), dependency-cruiser 18.5.0 `no-circular`, eslint-plugin-boundaries 7.2.0은 모두 MIT다. `turbo boundaries`는 Experimental이다([npm @nx/eslint-plugin](https://www.npmjs.com/package/@nx/eslint-plugin), [npm dependency-cruiser](https://www.npmjs.com/package/dependency-cruiser), [npm turbo](https://www.npmjs.com/package/turbo)) | Nx + dependency-cruiser + eslint-plugin-boundaries를 쓴다(6절) |

**메모리 산술이 설계를 결정한다.** 4K RGBA 프레임 한 장은 약 33.2MB이고, 4분짜리 1080p30 MV는 7,200프레임이다(계산, 노트 04). 원시 프레임을 쌓아 둘 수 없으므로 `encodeQueueSize` 백프레셔, `VideoFrame.close()`, OPFS 스트리밍 기록이 필수다. `showSaveFilePicker`는 Chromium 계열만 지원하므로 Firefox·Safari에서는 Blob 다운로드로 처리한다([BCD](https://www.npmjs.com/package/@mdn/browser-compat-data)). 매니지드 비디오 서비스 단가, 브라우저별 인코딩 코덱 매트릭스, 상용 웹 에디터의 레퍼런스 아키텍처는 **미확인**이다(11절).

### 2.5 배포 API: 공개 업로드 경로마다 승인 절차가 있다

**YouTube.** 업로드 쿼터는 두 번 바뀌었다. 2025-12-04에 업로드 1회의 비용이 약 1,600 units에서 약 100 units로 내려갔고([Revision History](https://developers.google.com/youtube/v3/revision_history)), 2026-06-01에는 `videos.insert`가 별도의 "Video Uploads" 버킷으로 분리됐다. 이 버킷은 호출당 1 unit, **프로젝트당 하루 100회**다([Quota Calculator](https://developers.google.com/youtube/v3/determine_quota_cost)).

하지만 쿼터보다 큰 장벽은 승인이다. 2020-07-28 이후 생성된 미검증 API 프로젝트가 올린 영상은 감사(audit)를 통과할 때까지 **비공개로 잠긴다**([Videos: insert](https://developers.google.com/youtube/v3/docs/videos/insert)). `youtube.upload`는 sensitive 범위로 소개되는데(2차 출처, [Phyllo](https://www.getphyllo.com/post/youtube-oauth-scopes)), Google 공식 문서에 따르면 CASA 보안 평가는 restricted 범위에만 요구된다([Google](https://developers.google.com/identity/protocols/oauth2/production-readiness/restricted-scope-verification)). 검증 기간은 Brand Verification 2–3 영업일, Sensitive Scope Verification 10 영업일이고([Google Cloud FAQ](https://support.google.com/cloud/answer/13463817?hl=en)), 검증 전에는 신규 사용자 100명 상한이 걸린다([Unverified apps](https://support.google.com/cloud/answer/7454865?hl=en)).

정책 측면에서는 세 가지를 지켜야 한다. 2024-10-15부터 정사각형·세로형이면서 3분 이하인 영상은 자동으로 Shorts가 되고, 1분을 넘는 Shorts에 Content ID claim이 걸리면 전 세계에서 차단된다([YouTube Help](https://support.google.com/youtube/answer/15424877?hl=en)). 2025-07-15부터 YPP는 대량 생산형 "inauthentic content"를 수익화에서 배제한다([Plagiarism Today](https://www.plagiarismtoday.com/2025/07/08/youtube-targets-inauthentic-content/)). 반면 명백히 비현실적인 애니메이션은 변조·합성(A/S) 콘텐츠 공개 의무 대상이 아니다([Google Blog](https://blog.google/intl/en-in/products/platforms/how-were-helping-creators-disclose-altered-or-synthetic-content/)).

**다른 플랫폼.**

| 플랫폼 | 확인된 조건 | 출처 |
|---|---|---|
| TikTok | 감사 전 Direct Post는 `SELF_ONLY`로 강제되고, 24시간에 5명, 비공개 계정만 쓸 수 있다. 초안을 받은편지함으로 보내는 Upload 경로가 따로 있다 | [Direct Post](https://developers.tiktok.com/docs/en/content-posting-api-reference-direct-post), [Content Posting API](https://developers.tiktok.com/products/content-posting-api) |
| Instagram Reels | 프로페셔널 계정만 쓸 수 있고, 24시간 이동창에 100건까지다. 영상은 15분·300MB·25Mbps 이하다 | [Content Publishing](https://developers.facebook.com/docs/instagram-platform/content-publishing/), [IG User Media](https://developers.facebook.com/docs/instagram-platform/instagram-graph-api/reference/ig-user/media/) |
| X | 2026-02-06부터 pay-per-use다. 2026-04-20부터 게시 1건 $0.015, URL 포함 게시 $0.20이다 | [X Developers](https://devcommunity.x.com/t/announcing-the-launch-of-x-api-pay-per-use-pricing/256476), [가격 개정](https://devcommunity.x.com/t/x-api-pricing-update-owned-reads-now-0-001-other-changes-effective-april-20-2026/263025) |
| 니코니코 | 2023년 4월경 외부 API 제공을 종료했고 대체 API 예정도 없다. 업로드 상한은 2024-10-29부터 1개당 6GB다 | [ニコニコインフォ](https://blog.nicovideo.jp/niconews/182541.html), [6GB 공지](https://blog.nicovideo.jp/niconews/233213.html) |
| Bilibili | 개방 플랫폼 arcopen 투고 흐름이 있지만 권한 신청이 필요하다. 비공식 API 문서 프로젝트는 2026-01-28 법적 경고를 받고 중단됐다(2차) | [demo](https://github.com/bilibili-openplatform/demo), [CSDN](https://adg.csdn.net/69707c57437a6b40336a746a.html) |

상세 전략은 8절에서 다룬다.

### 2.6 권리·안전: 개인 투고는 넓게 허용되지만 운영사의 상업 이용은 별개다

**목소리(출력 음성).** 미쿠 V4X EULA는 합성 음성의 상용·비상용 이용을 허용한다. 그러나 크립톤의 사전 동의 없이 합성 음성을 이용한 상품·서비스에 「VOCALOID」「ボカロ」「初音ミク」 등의 상표를 표시하는 것은 금지한다([V4X EULA](https://ec.crypton.co.jp/download/pdf/eula_MIKUV4X.pdf)). 크립톤은 캐릭터를 연상시키는 말이나 이미지로 곡을 상업적으로 홍보하는 것도 패키지 허락 범위 밖이라고 설명한다([Crypton FAQ 364](https://ec.crypton.co.jp/support/faq/364)). 테토 공식 Q&A는 개인이 테토로 부른 곡을 동영상 사이트·SNS에 투고하는 것, 동인 CD를 제작·배포·판매하는 것, 자작곡을 음악 배포 사이트에서 유료 배포하는 것, 테토를 쓴 동영상으로 광고 수익을 얻는 것을 모두 문제없고 상용 이용에도 해당하지 않는다고 본다([規約Q&A](https://kasaneteto.jp/guidelines/faq.html)). 반면 법인 등의 상용 이용은 사전 연락이 필요하고, 창구는 크립톤에 위탁돼 있다([kasaneteto.jp](https://kasaneteto.jp/guidelines/)).

**캐릭터.** PCL은 "비영리이면서 무상"인 2차 창작만 허락한다([PCL](https://piapro.jp/license/pcl)). 크립톤은 2024-12-04 성명에서 선전·광고 목적의 이용을 금지 사항으로 명확히 했다([XEXEQ](https://xexeq.jp/blogs/media/topics28818)). 개인의 YouTube 수익화는 허용했지만, 제3자 권리 콘텐츠가 들어간 영상은 대상에서 뺐다(개정 연도 미확인, [KAI-YOU](https://kai-you.net/article/80543)). TWINDRILL은 공식이 파악하지 못하는 것을 "重音テト"로 재배포하지 말라고 했고, SV AI 테토로 AI 학습을 하는 것은 Dreamtonics 규약 위반이라고 밝혔다([X 2023-06-29](https://x.com/twindrill_teto/status/1674403130287751168)).

여기서 나오는 결론은 다음과 같다(추정). 사용자의 MV를 호스팅하고 크로스포스트하는 것은 이용자 라이선스 범위 안에 있다. 반대로 운영사가 미쿠·테토를 서비스 마스코트, 광고 소재, "공식처럼 보이는 리그"로 쓰려면 크립톤과 상업 라이선스를 협의해야 한다.

**음악 저작권.** JASRAC의 UGC 포괄 이용허락은 서비스 단위로 맺는 계약이다([JASRAC 목록](https://www.jasrac.or.jp/information/topics/20/ugc.html)). 포괄계약은 작사·작곡 권리만 처리하므로 원반·オフボーカル 사용과 편곡(翻案)은 따로 허락받아야 한다([nayutas](https://nayutas.net/school/kitasenju/blog/86453/), 2차). 그래서 신규 플랫폼은 계약 전까지 기존 관리곡의 커버를 직접 호스팅하지 않는 편이 맞다(추정).

신고 처리 절차는 국가별로 다르다. 한국 저작권법 103조는 소명 → 즉시 중단·통보 → 재개 요구 → 재개라는 절차를 정한다([국가법령정보센터](https://www.law.go.kr/LSW/lsLawLinkInfo.do?lsJoLnkSeq=900605727&lsId=000798&chrClsCd=010202&print=print)). 일본 情報流通プラットフォーム対処法(2025-04-01 시행)은 대규모로 지정된 사업자에게 신청 창구, 원칙 7일 내 통지, 투명성 의무를 지운다([総務省](https://www.soumu.go.jp/johotsusintokei/whitepaper/ja/r07/html/nd123210.html)).

녹음 지문 식별에는 두 경로가 있다. Chromaprint는 전체적으로 LGPL 2.1이고([LICENSE](https://github.com/acoustid/chromaprint/blob/master/LICENSE.md)), AcoustID를 상업적으로 쓰려면 유료 플랜이 필요하다. Audible Magic은 UGC 플랫폼이 업로드 시점에 붙여 쓰는 상용 서비스다([Audible Magic](https://www.audiblemagic.com/?p=6226)).

**커뮤니티 장치와 지표 무결성.** 니코니코는 두 장치로 2차 창작 생태계를 돌린다. コンテンツツリー로 부모 작품을 등록하게 하고, 부모 작품 크리에이터에게 "子ども手当"로 보상을 돌려준다([ニコニコインフォ](https://blog.nicovideo.jp/niconews/172165.html)). 2019년에는 랭킹 입력을 재생·코멘트·マイリスト로 제한하고 접속 정보를 더해 조작을 어렵게 했다([ニコニコ窓口](https://ch.nicovideo.jp/nicotalk/blomaga/ar1740969)).

오픈소스 코드에서도 바로 쓸 수 있는 규칙을 찾았다. PeerTube는 10초 이상 시청해야 조회로 세고, 같은 시청자는 1시간 안에 다시 세지 않으며, 집계를 30분 버퍼링한다. 클라이언트 세션 ID는 위조될 수 있다고 경고한다([PeerTube config](https://github.com/Chocobozzz/PeerTube/blob/develop/config/default.yaml)). Mastodon은 트렌드를 계산할 때 기준선 대비 이상치를 고유 계정 수로 재고, 점수에 반감기 1시간을 적용하며, 운영자 승인 게이트를 거치게 한다([Mastodon](https://github.com/mastodon/mastodon/blob/main/app/models/trends/statuses.rb)).

ボカコレ 2026 夏에서는 규칙 적용 논란이 있었다. MV에 닌텐도 3DS를 쓴 곡이 랭킹에서 빠지자 반발이 일었고, 운영 측은 사전 주지가 부족했던 점을 사과했다([ITmedia](https://www.itmedia.co.jp/news/article/2608/25/2000000733/)). 이 사례는 이벤트 규칙을 미리 공지해야 한다는 반면교사다.

**생성형 AI.** 보카로 MV에 AI 일러스트를 쓰면 반발이 생겼다(2024-02 사례, [X](https://x.com/TomoyaKinoshita/status/1760123788212183356)). pixiv는 2026-03-18부터 AI 설정 허위 신고를 금지하고, 위반 의심 작품을 기본 비표시로 하는 옵션을 도입했다([ITmedia AI+](https://www.itmedia.co.jp/aiplus/article/2602/18/1260218132/)). Synthesizer V AI처럼 음성합성 자체가 AI 기반이므로, 라벨은 "음성합성 소프트웨어 사용"과 "생성 AI로 그림·곡 생성"을 구분해야 한다(추정).

**광과민성 기준.** WCAG 2.3.1(Level A)은 어느 1초 동안이든 3회를 넘는 점멸을 금지한다. 예외는 일반·적색 플래시 임계값보다 작은 경우뿐이다([W3C WCAG](https://github.com/w3c/wcag/blob/main/guidelines/sc/20/three-flashes-or-below-threshold.html)). 면적 기준은 10° 시야의 25%로, 1024×768 화면에서는 341×256 px, 즉 화면의 약 11.1%다(계산). 적색 플래시는 한쪽 상태가 R/(R+G+B) ≥ 0.8인 경우다([정의](https://github.com/w3c/wcag/blob/main/guidelines/terms/20/general-flash-and-red-flash-thresholds.html)). EA의 IRIS(3-clause BSD 형태, 의존하는 FFmpeg는 LGPL 2.1)는 1초에 3회 초과를 **flash failure**, 5초 연속 초당 2–3회를 **extended flash failure**, 유해 패턴 0.5초 이상을 **pattern failure**로 판정하되, 인증 용도가 아님을 스스로 밝힌다([IRIS README](https://github.com/electronicarts/IRIS/blob/main/README.md), [LICENSE](https://github.com/electronicarts/IRIS/blob/main/LICENSE.txt)).

## 3. 제품 정의: 곡을 가진 사람이 외주 없이 하루 안에 MV를 공개하게 한다

**목표.** 미쿠·테토 곡을 가진 크리에이터가 외주 없이 한 화면에서 공개 가능한 MV를 만들고, 커뮤니티 반응(좋아요·유효 조회수·댓글·파생작)을 얻고, YouTube 등으로 옮겨 올리게 하는 것이 목표다(제안). 이 목표는 외주 기준선과 대조해 정했다. 외주 납기는 14–50일이고([無印かげひと](https://note.com/kagehito_muji/n/nea1037033d4f)), 가격은 커버 영상 5천~3만 엔, 정지화·루프 리릭 MV 5만~10만 엔이다. 그래서 MVP의 핵심 지표는 "첫 MV 완성까지 걸리는 시간"과 "완성작 중 공개·크로스포스트 비율"로 잡는다(제안).

"한 화면"은 미디어·가사 패널, 캐릭터(리그) 패널, 효과 패널, 미리보기, 점멸 안전 미터, 게시·내보내기 패널을 하나의 타임라인(오디오 파형, BPM 그리드, 가수별 트랙) 주위에 배치한 단일 편집 화면이다. 사용자는 모달 없이 이 타임라인 위에서 오간다(제안).

**대상 사용자.**

| 사용자 | 지금의 방식과 고통 | 플랫폼이 주는 것(제안) | 근거 |
|---|---|---|---|
| 보카로P(자작형) | AviUtl·AE로 직접 만든다. 가사 타이밍 수작업, AviUtl2의 하드웨어 요구, AE 비용이 고통이다 | 프로젝트 파일 기반 자동 가사·립싱크, 템플릿, BPM 동기 효과 | オーバーライド·テトリス·人マニア 자작 사례, [roboin](https://roboin.io/article/2025/07/08/aviutl-returns-after-6-years-with-64-bit-support-and-built-in-exedit) |
| 일러스트레이터·動画師 | P의 요청에 따라 소재를 나눠 그리지만 완성 영상을 모른 채 작업한다 | PSD 파츠 분할 가이드, 리그 미리보기, 역할별 크레딧(P1) | [Billboard JAPAN](https://www.billboard-japan.com/special/detail/4683) |
| 커버(歌ってみた)·2차 창작자 | 반트레이스와 본가 재현을 외주하고 공식 소재를 찾는다 | 원작자가 허락한 소재 패키지와 계보 연결(Phase 3, 권리 확인 후) | [suika-vlive](https://suika-vlive.com/utattemita_illust_irai/), [BOOTH 커버용 동영상 소재](https://booth.pm/ja/items/6430321) |
| 해외 팬·번역자 | 한국어 커버, 한글 자막, 중국어권 전재가 이뤄진다 | 다국어 번역 자막 트랙 | [한국어 커버](https://www.youtube.com/watch?v=I9xlkG-WEXM), [JOYSOUND](https://news.joysound.com/article/604380) |
| 시청자 | 니코니코 랭킹, 2차 창작 연쇄, 함께 듣기(Kiite Cafe)를 즐긴다 | 피드, 좋아요, 저장, 유효 조회수, 부문별 랭킹, 시간 동기 코멘트(P1) | [ニコニコ窓口](https://ch.nicovideo.jp/nicotalk/blomaga/ar1740969), [産総研](https://staff.aist.go.jp/m.goto/PAPER/SIGMUS202109tsukuda.pdf) |

**핵심 사용자 여정(MVP 기준)**

| 단계 | 사용자가 하는 일 | 플랫폼이 자동으로 하는 일 | 근거 |
|---|---|---|---|
| 0. 곡 준비(플랫폼 밖) | VOCALOID6 Editor·Piapro Studio NT2·SV Studio 2와 DAW로 믹스한다 | — | 2.1 |
| 1. 업로드 | 마스터 WAV(필수), 가수별 보컬 스템(권장), .svp/.vpr/.ppsf/.ustx/MIDI(선택), 가사 텍스트(대체)를 올린다 | 프로젝트 파일 파싱, 트랙→가수 매핑 제안 | [OpenUtau SVP.cs](https://github.com/stakira/OpenUtau/blob/HEAD/OpenUtau.Core/Format/SVP.cs) |
| 2. 정렬 | 오프셋을 확인하고 고친다 | 템포 맵으로 tick·blick을 초로 바꾼다. 마스터와 프로젝트 0초 사이 오프셋 후보를 제시한다. BPM과 첫 박을 검출한다 | [web-audio-beat-detector](https://www.npmjs.com/package/web-audio-beat-detector) |
| 3. 템플릿 | 1장 그림+가사, 루프 리릭, 미쿠×테토 듀엣, VS 분할 중 하나를 고른다 | 가수 트랙에 따라 레이아웃과 색 테마를 배치한다 | 2.2, 2.3 |
| 4. 캐릭터 | PSD를 올린다(캐릭터별) | 레이어 이름 규칙으로 파츠를 분류하고 기본 본을 만든다. 5모음 입 모양을 노트 타이밍에 배치한다 | [VRM 표정 aa/ih/ou/ee/oh](https://www.npmjs.com/package/@pixiv/three-vrm) |
| 5. 가사 | 리릭 모션 프리셋과 폰트를 고르고 번역을 입력한다 | 노트 단위 가사 타이밍, 파트별 색, 줄바꿈(BudouX) | [budoux](https://www.npmjs.com/package/budoux) |
| 6. 효과 | 효과를 추가하고 BPM 분할로 동기한다 | 효과별 점멸 위험을 계산하고 상한을 적용한다 | 5절 |
| 7. 검사 | 경고 구간을 고치거나 경고 카드를 넣는다 | 실시간 점멸 미터, 권리·AI 신고 체크리스트 | [WCAG 2.3.1](https://github.com/w3c/wcag/blob/main/guidelines/sc/20/three-flashes-or-below-threshold.html) |
| 8. 렌더 | 내보내기를 누른다 | 브라우저 WebCodecs 렌더(1080p30). 미지원이면 서버 렌더. 결과물에 IRIS 검사 | [BCD](https://www.npmjs.com/package/@mdn/browser-compat-data), [IRIS](https://github.com/electronicarts/IRIS/blob/main/README.md) |
| 9. 공개 | 제목·크레딧·계보·공개 범위를 정한다 | HLS 패키징, 작품 페이지, 크레딧 블록 자동 생성 | [shaka-packager](https://www.npmjs.com/package/shaka-packager) |
| 10. 크로스포스트 | YouTube 업로드(승인 후 공개), 니코니코 투고 어시스트 | 플랫폼별 프리셋으로 재인코딩, 메타데이터 템플릿, 점멸 경고 문구 자동 삽입 | 8절 |
| 11. 반응 | 좋아요·저장·댓글, 조회수와 랭킹을 확인한다 | 유효 조회 집계, 부문별 랭킹, 외부 통계 동기화(30일 정책 준수) | [PeerTube](https://github.com/Chocobozzz/PeerTube/blob/develop/config/default.yaml), [Developer Policies](https://developers.google.com/youtube/terms/developer-policies) |

**전체 범위(제품 수명 전체 기준).** 포함과 제외를 미리 선언해 이후 범위 논쟁을 줄인다(제안).

| 영역 | 포함 | 명시적 제외(또는 조건부) | 제외 이유 |
|---|---|---|---|
| 음성 | 오디오·스템·프로젝트 파일 수용, 가사·립싱크 자동화 | 플랫폼 내 미쿠·테토 가창 합성(Phase 4 조건부), 팬 제작 AI 음성 모델(영구 제외) | 공개 SDK 부재, 임베딩 계약 필요, 학습 금지 조항 |
| 영상 제작 | 리릭 모션, 2D 컷아웃·본·메시 리그, 효과·트랜지션, 템플릿, VRM 3D(P1), 영상 클립 합성 | Live2D·Spine 런타임(계약 전 제외), MMD(P2 조건부), 사실적 AI 영상 생성 | 라이선스 구조, A/S 공개 의무, 커뮤니티 반발 |
| 안전 | 점멸 3중 검사, 경고 카드, 신고·테이크다운, AI 사용 신고 | 법적 "인증" 표시 | IRIS는 인증 도구가 아니다 |
| 커뮤니티 | 작품 페이지, 좋아요, 저장, 댓글, 유효 조회수, 랭킹, 계보·fork(P1), 이벤트(P2) | 수익 분배(Phase 4), DM(검토 전 제외) | 일본에서는 DM 같은 통신 매개가 届出 대상이 될 수 있다(노트 06) |
| 음악 권리 | 오리지널곡, 권리자가 허락한 자산 | JASRAC·NexTone·KOMCA 관리곡 커버 직접 호스팅(계약 전 제외) | 포괄계약은 서비스 단위 |
| 배포 | MP4 다운로드, YouTube, TikTok(초안→Direct), 니코니코·Bilibili 투고 어시스트, Instagram, X(비용 게이팅) | 니코니코·Bilibili 비공식 API 자동 업로드(영구 제외), 일괄 자동 업로드 | 약관·법적 위험, YPP inauthentic 정책 |
| 캐릭터 IP | 사용자 업로드 자산(규약 준수 확인), 오리지널 캐릭터 기본 리그 | 운영사의 미쿠·테토 마케팅·공식처럼 보이는 리그(크립톤 협의 전 제외) | PCL·EULA·2024-12 성명 |

## 4. 기능 분해: 11개 에픽, P0은 "업로드에서 공개까지 한 번 끝까지"에 필요한 것만

우선순위 정의(제안): **P0**은 MVP(Phase 1)에 반드시 들어가는 기능이다. **P1**은 Phase 2–3 확장 기능이다. **P2**는 장기 과제이거나, 계약·수요 확인을 전제로 한 조건부 기능이다. 근거 열의 "추정 수요"는 개별 MV 출처가 없는 항목이다.

### E1. 소스 수집·정규화

| ID | 기능 | 우선 | 근거 |
|---|---|---|---|
| E1-1 | 마스터 오디오 업로드(WAV/FLAC 권장). 영상 길이의 기준이 된다 | P0 | 엔진 출력은 WAV이고 크리에이터가 DAW에서 믹스한다(노트 01 §8) |
| E1-2 | 가수별 보컬 스템 업로드와 "스템 → 캐릭터" 매핑 | P0(선택 입력) | 캐릭터별 립싱크와 효과 반응에 필요하다. 엔진을 섞는 사례가 있다([VocaDB](https://vocadb.net/S/805591)) |
| E1-3 | 프로젝트 파일 파서: .svp(SV1/SV2), .vpr, .ppsf(NT), .ustx, MIDI, MusicXML → 공통 노트 모델 | P0 | 텍스트 직렬화 형식이다([LibreSVIP 형식 일람](https://github.com/SoulMelody/LibreSVIP/blob/HEAD/docs/project_formats.md)) |
| E1-4 | .vsqx·.ust 레거시 파서 | P1 | V4X·UTAU 사용자층이 있다([UtaFormatix3](https://github.com/sdercolin/utaformatix3/blob/HEAD/README.md)) |
| E1-5 | 템포 맵 환산(tick/blick → 초)과 오프셋 정렬(자동 후보 + 수동 슬라이더) | P0 | 4분음표 = 705,600,000 blick이다([SVP.cs](https://github.com/stakira/OpenUtau/blob/HEAD/OpenUtau.Core/Format/SVP.cs)). LibreSVIP 자막 옵션에도 offset이 있다 |
| E1-6 | 가사 텍스트 직접 입력(프로젝트 파일이 없을 때) | P0 | 대체 경로(노트 01 §8) |
| E1-7 | PSD 임포트(레이어 트리와 이름 보존) | P0 | 소재 분리 관행([Billboard JAPAN](https://www.billboard-japan.com/special/detail/4683), [あずまき note](https://note.com/azmk08477/n/n98f05a9ce2ec)). PSD 파서 라이브러리는 미조사 |
| E1-8 | 영상 클립 임포트와 프레임 정확 디코드(WebCodecs `VideoDecoder`) | P1 | 「イガク」 실사+CG 합성([ja.wikipedia](https://ja.wikipedia.org/wiki/%E3%82%A4%E3%82%AC%E3%82%AF)) |
| E1-9 | SV2 Pro용 "가사 타이밍 내보내기" 스크립트 배포 | P1 | 공식 스크립트 API로 노트·가사·시간축을 읽을 수 있다([svstudio-scripts](https://github.com/Dreamtonics/svstudio-scripts)) |
| E1-10 | OFL 일본어 폰트 기본 라이브러리. 사용자 폰트 업로드는 라이선스 자기신고를 받는다 | P0 / P1 | [fontsource](https://www.npmjs.com/package/@fontsource/noto-sans-jp), 명조 강세([いからげ](https://note.com/ika_rage/n/n31662be9d918)) |

### E2. 오디오 분석·동기

| ID | 기능 | 우선 | 근거 |
|---|---|---|---|
| E2-1 | 파형 타임라인과 오디오 시계 기반 미리보기 동기(`getOutputTimestamp`, `outputLatency`) | P0 | `outputLatency`는 Safari 18.4부터 지원한다([BCD](https://www.npmjs.com/package/@mdn/browser-compat-data)) |
| E2-2 | BPM·첫 박 검출과 BPM 그리드 스냅 | P0 | `guess()`가 bpm과 offset을 돌려준다([npm](https://www.npmjs.com/package/web-audio-beat-detector)). BPM 170에서 1박은 약 0.353초, 30fps 기준 약 10.6프레임이다(계산) |
| E2-3 | 다운비트·구간(인트로/후렴) 분석으로 자동 컷과 효과 배치 | P1 | beat-this·allin1(MIT, 서버)([PyPI beat-this](https://pypi.org/project/beat-this/), [allin1](https://pypi.org/project/allin1/)) |
| E2-4 | 보컬 스템 RMS 엔벨로프로 가수 활성 판정과 효과 반응 | P0 | 노트 01 산출물 설계(추정) |
| E2-5 | 스템이 없을 때 서버 음원 분리 | P1 | Demucs 4.1.0(MIT)([PyPI](https://pypi.org/project/demucs/)). 브라우저판은 모델이 172MB라 기본 경로에서 뺀다([demucs-web](https://www.npmjs.com/package/demucs-web)) |
| E2-6 | 라우드니스(통합·True Peak) 표시 | P1 | [loudness-worklet](https://www.npmjs.com/package/loudness-worklet). YouTube −14 LUFS 기준은 미확인 |
| E2-7 | `OfflineAudioContext`로 결정적 오디오 믹스다운 | P0 | 모든 주요 브라우저가 지원한다([BCD](https://www.npmjs.com/package/@mdn/browser-compat-data)) |

### E3. 가사·타이포그래피

| ID | 기능 | 우선 | 근거 |
|---|---|---|---|
| E3-1 | 노트 → 가사 타이밍 자동 생성(줄·구 묶기, 슬러 노트 처리) | P0 | LibreSVIP ASS/LRC/SRT 생성 로직([ass](https://github.com/SoulMelody/LibreSVIP/tree/HEAD/libresvip/plugins/ass)) |
| E3-2 | 탭 싱크(재생 중 SET 입력)와 드래그 미세 조정 | P0 | [kanoの缶詰知識](https://eternallways.com/aviutltool) |
| E3-3 | 오디오만 있을 때 강제 정렬(분리 → ASR/정렬 → G2P) | P1 | demucs → faster-whisper → whisperX → pyopenjtalk 사슬([whisperX](https://pypi.org/project/whisperx/), [pyopenjtalk](https://pypi.org/project/pyopenjtalk/)). 합성 보컬에서의 정확도는 미확인 |
| E3-4 | 문자 단위 리릭 모션 프리셋(약 12종) | P0 | [全自動リリックモーション](https://seguimiii.com/aviutl-tech/autolyricmotion), [Aulymo](https://booth.pm/ja/items/6403113), [AviUtl2 스크립트](https://github.com/korarei/AviUtl2_AutoLyricAnimation_K_Script) |
| E3-5 | 파트별 색 구분과 콜 앤 리스폰스 하이라이트 | P0 | 미쿠 VS 테토 니코카라 파트 분할([YouTube](https://www.youtube.com/watch?v=n0TIajZPXK4)) |
| E3-6 | 번역 자막 트랙(다국어)과 SRT/VTT 내보내기 | P0 | 「メズマライザー」의 영어 번역 MV([Billboard JAPAN 칼럼](https://www.billboard-japan.com/special/detail/5057)), 한국어 한글화 영상 |
| E3-7 | 루비(후리가나). 초안은 자동, 수정은 사용자 | P1 | [kuroshiro](https://www.npmjs.com/package/kuroshiro). GPU 경로는 자체 레이아웃이 필요하다(노트 04) |
| E3-8 | 세로쓰기·縦中横 | P2 | Canvas/WebGL에는 세로쓰기 API가 없다([BCD](https://www.npmjs.com/package/@mdn/browser-compat-data)). MV 수요는 미확인 |
| E3-9 | 타이틀 로고 카드, SDF 다중 외곽선·그림자 | P0 | 「ダイダイ」 로고 전담 크레딧, AviUtl 기본 테두리 품질 불만([scrapbox](https://scrapbox.io/leje-campus/%E8%84%B1%E3%80%8CAviUtl%E3%81%A3%E3%81%BD%E3%81%95%E3%80%8D%E3%81%AE%E3%81%99%E3%82%9D%E3%82%81)) |

### E4. 캐릭터 리그·애니메이션

| ID | 기능 | 우선 | 근거 |
|---|---|---|---|
| E4-1 | PSD 파츠 자동 분류(눈·입·앞머리·트윈테일 등 이름 규칙)와 분할 가이드 체크리스트 | P0 | Live2D MV 실무: 파츠 분할을 고려해 일러스트를 준비한다([雨宮ミト](https://note.com/ammy_mito/n/ne4577b9af2d3)) |
| E4-2 | 컷아웃 트랜스폼 프리셋(회전, 진자, 바운스, 밈풍 루프, BPM 동기) | P0 | 회전하는 테토, 진자 프레임, 밈 모션(2.3) |
| E4-3 | FK 본 계층(강체 파츠)과 피벗 편집 | P0 | 자체 런타임 권고(Spine·Live2D 라이선스, 노트 04) |
| E4-4 | 5모음 입 모양 자동 배치. 프로젝트 파일 기반은 P0, 오디오 기반은 P1 | P0 / P1 | 가나 → `aa/ih/ou/ee/oh` 매핑([three-vrm](https://www.npmjs.com/package/@pixiv/three-vrm)), Rhubarb 기본 인식기는 영어 전용([npm](https://www.npmjs.com/package/rhubarb-lip-sync)) |
| E4-5 | 눈 깜빡임 자동(시드 고정) | P1 | 자동 리깅 템플릿(노트 04 추정) |
| E4-6 | 메시 변형과 가중치 페인팅(선형 블렌드 스키닝) | P1 | 에디터 공수 6–12인월(노트 04 추정) |
| E4-7 | IK(2본 해석해, FABRIK) | P1 | 노트 04 런타임 범위 |
| E4-8 | 결정적 스프링 2차 모션(트윈테일, 드릴 머리) | P1 | 추정 수요. 테토 공식 운영 계정 이름이 「ツインドリル」일 만큼 드릴 트윈테일은 상징 요소다([X](https://x.com/twindrill_teto/status/1889916232427774196)) |
| E4-9 | 저프레임 스프라이트 교체(우고메모풍, 2·3컷 홀드) | P1 | 「ダイダイ」([pixiv百科](https://dic.pixiv.net/a/%E3%83%80%E3%82%A4%E3%83%80%E3%82%A4%E3%83%80%E3%82%A4%E3%83%80%E3%82%A4%E3%83%80%E3%82%A4%E3%82%AD%E3%83%A9%E3%82%A4)) |
| E4-10 | VRM 3D 레이어, VRMA 모션, 모델 라이선스 게이트 | P1 | `commercialUsage`·`allowRedistribution` 메타([three-vrm](https://www.npmjs.com/package/@pixiv/three-vrm)). 테토 VRM 팬 모델([BOOTH](https://booth.pm/ja/items/5038772)) |
| E4-11 | MMD(PMX/VMD) 임포트 | P2 | [babylon-mmd](https://www.npmjs.com/package/babylon-mmd). three.js MMD 모듈은 제거됐다 |
| E4-12 | 오리지널 캐릭터 기본 리그와 사용자 리그 템플릿 공유 | P1 | 공식 미쿠 Live2D 모델은 미확인. 공식처럼 보이는 재배포는 금지([TWINDRILL](https://x.com/twindrill_teto/status/1674403130287751168)) |
| E4-13 | 크립톤 라이선스를 받은 미쿠·테토 공식 리그 | P2(조건부) | PCL "비영리·무상"([PCL](https://piapro.jp/license/pcl)) |

### E5. 합성·효과 엔진

| ID | 기능 | 우선 | 근거 |
|---|---|---|---|
| E5-1 | 레이어, 그룹, 마스크, 블렌드 모드, 베지어 키프레임 | P0 | AviUtl 합성 제약 불만(「컴포지트까지의 부자유」, [scrapbox](https://scrapbox.io/leje-campus/%E8%84%B1%E3%80%8CAviUtl%E3%81%A3%E3%81%BD%E3%81%95%E3%80%8D%E3%81%AE%E3%81%99%E3%82%9D%E3%82%81)) |
| E5-2 | 효과 플러그인 API. 매니페스트에 라이선스, 결정성, 지원 백엔드, **점멸 위험 프로파일**을 담는다 | P0 | 플러그인 등록 선례([untitled-pixi-live2d-engine](https://www.npmjs.com/package/untitled-pixi-live2d-engine)), gl-transitions 메타데이터 |
| E5-3 | P0 효과 12종(5절 카탈로그) | P0 | 5절 |
| E5-4 | 트랜지션 약 20종(gl-transitions 중 MIT 선별) | P0 | 125종 중 MIT 123([npm](https://www.npmjs.com/package/gl-transitions)) |
| E5-5 | 카메라: 팬·줌·셰이크·회전 | P0 | AviUtl 카메라 평행이동 스크립트([あずまき](https://note.com/azmk08477/n/n98f05a9ce2ec)) |
| E5-6 | 결정성 보장: 유리수 시간, 프레임의 순수 함수, 시드 고정 RNG, 고정 스텝 물리 | P0 | 노트 04 §2 설계, [OTIO](https://pypi.org/project/OpenTimelineIO/) RationalTime 개념 |
| E5-7 | WebGPU 우선, WebGL2 폴백 이중 백엔드 | P0 | Firefox Linux·Android의 WebGPU 미지원([BCD](https://www.npmjs.com/package/@mdn/browser-compat-data)) |

### E6. 템플릿

| ID | 기능 | 우선 | 근거 |
|---|---|---|---|
| E6-1 | 1장 그림 + 가사 | P0 | 「ボカロMVは一枚絵+歌詞表示でいい理由」([koharuoto](https://koharuoto.net/blog-article332)). 정지화 리릭 MV 외주 5만 엔([Lancers](https://www.lancers.jp/menu/tag/%E3%83%9C%E3%82%AB%E3%83%AD)) |
| E6-2 | 루프 애니메이션 리릭 MV | P0 | 루프 기반 리릭 MV 외주 7만~10만 엔(같은 출처) |
| E6-3 | 미쿠×테토 듀엣: 좌우 배치, 가수 활성 강조, 캐릭터 색 테마 | P0 | 「メズマライザー」, 듀엣 4/20곡(2.2). 색 테마 값은 추정 |
| E6-4 | VS 분할 화면(대립 서사, 가사 교차 표시) | P0 | 「ダイダイダイダイダイキライ」([ja.wikipedia](https://ja.wikipedia.org/wiki/%E3%83%80%E3%82%A4%E3%83%80%E3%82%A4%E3%83%80%E3%82%A4%E3%83%80%E3%82%A4%E3%83%80%E3%82%A4%E3%82%AD%E3%83%A9%E3%82%A4)) |
| E6-5 | 템플릿 필수 커스터마이즈 단계(같은 템플릿의 대량 복제 방지) | P0 | YPP "inauthentic content"(2025-07-15, [Plagiarism Today](https://www.plagiarismtoday.com/2025/07/08/youtube-targets-inauthentic-content/)) |
| E6-6 | 밈 콜라주·텍스트 개그·스티커 템플릿 | P1 | 「オーバーライド」 밈 모션, 「テレパシ」 밈 레퍼런스([natalie](https://natalie.mu/music/column/614594)) |
| E6-7 | 리미널 스페이스(백룸) 배경 팩 | P1 | 「ブレインロット」·「ループザルーム」([ja.wikipedia](https://ja.wikipedia.org/wiki/%E3%83%AB%E3%83%BC%E3%83%97%E3%82%B6%E3%83%AB%E3%83%BC%E3%83%A0)) |
| E6-8 | 레트로 게임·도트 템플릿 | P1 | 「テトリス」, 「PPPP」 도트 애니 크레딧 |
| E6-9 | 본가 재현 커버 템플릿(원작자가 허락한 무음 MV·레이어 슬롯) | P2 | 반트레이스·본가 재현 수요([SKIMA](https://skima.jp/item/detail?item_id=253985)). 커버 권리 조건 충족이 전제 |

### E7. 안전·권리·신뢰

| ID | 기능 | 우선 | 근거 |
|---|---|---|---|
| E7-1 | 효과 프리셋 점멸 상한(기본 2Hz, 3Hz 초과는 차단). 적색·면적 경고 | P0 | WCAG 2.3.1, IRIS extended failure(5초 연속 2–3Hz) |
| E7-2 | 미리보기 실시간 점멸 미터(축소 프레임의 휘도·적색 전이를 1초 창으로 집계) | P0 | [WCAG 임계값 정의](https://github.com/w3c/wcag/blob/main/guidelines/terms/20/general-flash-and-red-flash-thresholds.html) |
| E7-3 | 렌더 결과물 IRIS 검사, 공개 게이트, 실패 구간 타임라인 표시 | P0 | [IRIS](https://github.com/electronicarts/IRIS/blob/main/README.md) |
| E7-4 | 「点滅注意 / Flashing lights warning」 카드와 설명란 문구 자동 삽입 | P0 | [Nehan](https://x.com/Na_yu_ta_ta/status/1759983307624955983), [VocaDB 태그](https://vocadb.net/T/2913/flashing-lights-warning) |
| E7-5 | 시청 전 경고 카드(클릭해야 재생). 점멸 완화 재생 모드는 P2 | P0 / P2 | 노트 06 §7 |
| E7-6 | 자산별 권리 메타데이터(유형×권리자×라이선스×출처 URL)와 업로드 시 권리 확인. 라이선스 호환성 검사는 P1 | P0 / P1 | 피아프로 라이선스 조건([piapro blog](https://blog.piapro.net/2011/03/post-437.html)) |
| E7-7 | 자산별 AI 사용 신고(사람/AI 보조/AI 생성)와 "음성합성 소프트웨어" 별도 범주. 필터·허위 신고 제재는 P1 | P0 / P1 | pixiv 2026-03-18 개정([ITmedia](https://www.itmedia.co.jp/aiplus/article/2602/18/1260218132/)) |
| E7-8 | 캐릭터 권리 표기 자동 삽입(크립톤 표기, 테토 3자 연명) | P0 | [テト ガイドライン](https://kasaneteto.jp/guidelines/character.html) |
| E7-9 | 신고·테이크다운 워크플로: 저작권 103조, 정보통신망법 44조의2 임시조치, 일본은 7일 내 응답을 모범 관행으로 | P0 | [103조](https://www.law.go.kr/LSW/lsLawLinkInfo.do?lsJoLnkSeq=900605727&lsId=000798&chrClsCd=010202&print=print), [44조의2](https://easylaw.go.kr/CSP/CnpClsMain.laf?csmSeq=293&ccfNo=2&cciNo=1&cnpClsNo=1) |
| E7-10 | 녹음 지문 대조(Chromaprint + 자체 DB) | P1 | fork와 소재 재사용을 시작하는 시점에 필요하다([Chromaprint](https://github.com/acoustid/chromaprint/blob/master/LICENSE.md)) |
| E7-11 | 커버곡 원곡 선택 강제와 라이선스 카탈로그 대조 | P2 | 포괄계약 이후(2.6) |

### E8. 렌더·내보내기

| ID | 기능 | 우선 | 근거 |
|---|---|---|---|
| E8-1 | 클라이언트 렌더: WebCodecs + Mediabunny, 1080p30 H.264 High + AAC-LC 48kHz, MP4 faststart | P0 | [YouTube 권장 인코딩](https://support.google.com/youtube/answer/1722171?hl=en), [Mediabunny](https://www.npmjs.com/package/mediabunny) |
| E8-2 | 서버 렌더: headless Chromium에서 같은 렌더 런타임 + FFmpeg. 미지원 기기와 4K용 | P1 | [playwright](https://www.npmjs.com/package/playwright), 노트 04 §8 |
| E8-3 | 출력 프리셋: 가로 마스터(P0), 세로 9:16 숏폼(P1), X 보수형(P2) | P0–P2 | 8절 프리셋 표 |
| E8-4 | 9:16 자동 리프레이밍(캐릭터 추적 키프레임 크롭)과 하이라이트 추출 | P1 | 쇼츠를 "CM"처럼 쓰는 관행([kadenzp](https://kadenzp.hatenablog.com/entry/20210626/1624713243)) |
| E8-5 | 썸네일 생성(프레임 선택 + 텍스트) | P0 | `thumbnails.set` 2MB 이하([YouTube](https://developers.google.cn/youtube/v3/docs/thumbnails/set?hl=en)) |
| E8-6 | OPFS 스트리밍 저장, 청크 업로드와 재개 | P0 | [MDN OPFS](https://developer.mozilla.org/en-US/docs/Web/API/File_System_API/Origin_private_file_system) |
| E8-7 | 프로젝트 자동 저장과 버전 이력 | P0 | 협업·fork의 전제(추정) |
| E8-8 | OTIO 내보내기(Premiere·Resolve 연동) | P2 | [OpenTimelineIO](https://pypi.org/project/OpenTimelineIO/) |

### E9. 커뮤니티·지표

| ID | 기능 | 우선 | 근거 |
|---|---|---|---|
| E9-1 | 작품 페이지: HLS 재생, 크레딧 블록, 엔진·가수 표기(「重音テトSV」와 「重音テト」 구분) | P0 | [ニコニコ大百科](https://dic.nicovideo.jp/v/sm44102888), [hls.js](https://www.npmjs.com/package/hls.js) |
| E9-2 | 좋아요(1인 1회, 취소 가능), 저장(マイリスト형 공개 목록), 공유 | P0 | 노트 06 §3 |
| E9-3 | 유효 조회수: 최소 시청 시간, 중복 제거 창, 지연 집계, 봇 필터. 자체 조회수와 외부 조회수를 섞지 않는다 | P0 | [PeerTube](https://github.com/Chocobozzz/PeerTube/blob/develop/config/default.yaml) |
| E9-4 | 댓글(P0)과 시간 동기 코멘트 오버레이(밀도 제한·NG 필터, P1) | P0 / P1 | 노트 06 §1(탄막 1차 자료는 미확보) |
| E9-5 | 피드: 신규·인기 hot(P0). 급상승은 이상치 + 반감기 + 운영 승인(P1). 부문별 랭킹: 오리지널·리믹스·신인(P1) | P0 / P1 | [PeerTube hot](https://github.com/Chocobozzz/PeerTube/blob/develop/server/core/models/video/sql/video/videos-id-list-query-builder.ts), [Mastodon](https://github.com/mastodon/mastodon/blob/main/app/models/trends/statuses.rb), [ボカコレ FAQ](https://vocaloid-collection.jp/faq/?category=ranking) |
| E9-6 | 크리에이터 대시보드: 조회 추이, 외부 통계 동기화(30일 갱신) | P1 | [Developer Policies](https://developers.google.com/youtube/terms/developer-policies) |
| E9-7 | 부모 작품 링크 입력(외부 URL 포함, P0). 유형 엣지를 가진 불변 계보 그래프(P1) | P0 / P1 | [コンテンツツリー](https://blog.nicovideo.jp/niconews/172165.html), [VocaDB SongType](https://github.com/VocaDB/vocadb/blob/main/VocaDbModel/Domain/Songs/SongType.cs) |
| E9-8 | fork·재사용 허가 플래그(공개와 분리, 기본 비허용)와 소재 패키지(무음 MV, 오프보컬, 레이어 PNG) 배포 | P1 | channel의 재이용 허가([X](https://x.com/x_cast_x/status/1787051821565145369)), BandLab의 public/forkable 분리(2차) |
| E9-9 | 팔로우·알림 | P1 | 일반 기능 |
| E9-10 | 이벤트(규칙 사전 공지, 부문 태그, 이의 제기 절차) | P2 | ボカコレ 2026 夏 논란([ITmedia](https://www.itmedia.co.jp/news/article/2608/25/2000000733/)) |
| E9-11 | 희소 응원 포인트, 함께 보기(Kiite Cafe형) | P2 | [bilibili 코인](https://www.bilibili.com/html/point.html), [産総研](https://staff.aist.go.jp/m.goto/PAPER/SIGMUS202109tsukuda.pdf) |

### E10. 외부 업로드

| ID | 기능 | 우선 | 근거 |
|---|---|---|---|
| E10-1 | MP4 다운로드와 플랫폼별 메타데이터 복사(제목·설명·태그·크레딧·점멸 경고) | P0 | 8절 |
| E10-2 | YouTube 직접 업로드: OAuth, resumable, 사용자 지정 제목·설명·공개 범위, 예약, 아동용, A/S 토글 | P0(감사 전에는 비공개) | [RMF](https://developers.google.com/youtube/terms/required-minimum-functionality), [Videos](https://developers.google.com/youtube/v3/docs/videos) |
| E10-3 | 니코니코 투고 어시스트: 6GB 가드, 태그 세트, ボカコレ 부문 태그 1개와 잠금 체크리스트, sm 번호 연결 | P0 | [6GB](https://blog.nicovideo.jp/niconews/233213.html), [ボカコレ FAQ](https://vocaloid-collection.jp/faq/) |
| E10-4 | 게시 작업 큐, 상태 머신, 플랫폼별 쿼터 카운터 | P0 | 노트 05 §10 |
| E10-5 | TikTok: 초안(Upload) 경로 먼저, 감사 후 Direct Post | P1 | [TikTok](https://developers.tiktok.com/products/content-posting-api) |
| E10-6 | Instagram Reels(App Review 후, 단기 서명 URL 전달) | P1 | [IG User Media](https://developers.facebook.com/docs/instagram-platform/instagram-graph-api/reference/ig-user/media/) |
| E10-7 | Bilibili 투고 어시스트(중국어 템플릿). 개방 플랫폼 연동은 P2 | P1 / P2 | [demo](https://github.com/bilibili-openplatform/demo) |
| E10-8 | X 게시(비용 게이팅, URL 없는 본문, 140초 클립) | P2 | [X 가격](https://devcommunity.x.com/t/x-api-pricing-update-owned-reads-now-0-001-other-changes-effective-april-20-2026/263025) |

### E11. 계정·운영·법무

| ID | 기능 | 우선 | 근거 |
|---|---|---|---|
| E11-1 | 계정(소셜 로그인)과 성인 콘텐츠 비허용 정책 | P0 | 청소년보호책임자 부담 완화(노트 06 §8, 추정) |
| E11-2 | OAuth 토큰 금고: 암호화, 최소 스코프, 연동 해제 시 폐기, 접근 로그 | P0 | [개인정보 안전성 확보조치(김앤장 요약)](https://www.kimchang.com/ko/insights/detail.kc?sch_section=4&idx=30601) |
| E11-3 | 관리자 콘솔: 신고 처리, 점멸 판정 기록, 트렌드 승인 게이트(P1) | P0 / P1 | Mastodon `review_threshold` |
| E11-4 | 약관(업로드 음성의 AI 학습·스크래핑 금지, 운영사 학습 불사용), 개인정보, 外部送信 공표 | P0 | [Dreamtonics Terms](https://dreamtonics.com/terms/), [総務省](https://www.soumu.go.jp/main_content/000862755.pdf) |
| E11-5 | OSS 라이선스 감사 파이프라인(클라이언트 번들 GPL/AGPL 차단, NOTICE 생성) | P0 | 2.4 라이선스 플래그 |
| E11-6 | 규모 기반 의무 대비(부가통신 신고, 청소년보호책임자, 情プラ法) | P2 | [CRMS](https://www.crms.go.kr/lay1/S1T54C59/contents.do), 노트 06 §8 |

## 5. 영상 효과·애니메이션·타이포그래피 카탈로그: 모든 효과는 점멸 위험 프로파일을 갖는다

**카탈로그를 설계하는 규칙(제안).** 효과마다 플러그인 매니페스트에 다섯 가지를 선언한다. 라이선스와 출처(셰이더 원작자 포함), 결정성 여부(같은 입력이면 같은 프레임), 지원 백엔드(WebGPU, WebGL2, 서버), BPM 동기 가능 여부, 그리고 **점멸 위험 프로파일**이다. 점멸 위험 프로파일은 위험 유형(없음·휘도·적색·공간 패턴)과 최대 변조 주파수, 최대 면적 비율을 함께 적는다. 에디터는 이 프로파일로 프리셋 상한을 걸고, 미리보기 미터에 위험을 합산하고, 렌더 후 IRIS 결과와 대조한다. 셰이더 출처가 불명확한 코드(예: Shadertoy, 기본 라이선스 미확인)는 반입하지 않는다(노트 04 §2). 모든 시각 요소는 "프레임 번호의 순수 함수"로 계산한다.

### 5.1 타이포그래피·가사

| 효과 | 근거 | 우선 | 핵심 파라미터 | 구현 방식 | 점멸 안전 연계 |
|---|---|---|---|---|---|
| 문자 단위 등장·퇴장(페이드, 스케일, 슬라이드, 회전, 바운스) | [全自動リリックモーション](https://seguimiii.com/aviutl-tech/autolyricmotion), [Aulymo](https://booth.pm/ja/items/6403113)(일괄 적용·고급 이징), AE 불투명도 애니메이터 | P0 | 단위(문자·단어·줄), stagger(ms), 이징 곡선, 노트 onset 기준 오프셋, 지속 | 글리프별 SDF 쿼드 인스턴싱. 글리프 변환을 frame의 순수 함수로 계산해 인스턴스 버퍼에 기록한다(텍스트 레이어당 1 draw call) | 0↔1 불투명도 반복은 휘도 전이다. 화면 면적 임계(1024×768 기준 약 11%, 계산)를 넘는 대형 글자를 BPM 분할로 깜빡이면 미터가 경고한다 |
| 카라오케 와이프(노래방식 채움) | 노래방 자막 수작업의 번거로움([AKETAMA](https://aketama.work/aviutl-karaoke)), 니코카라 | P0 | 가수별 채움 색, 진행 곡선(노트 onset·duration), 외곽선 색 | 글리프 단위 진행값 uniform. 프래그먼트 셰이더에서 x좌표 임계로 색을 섞는다 | 낮음(색만 바뀐다) |
| 파트별 색, 콜 앤 리스폰스 하이라이트 | 미쿠 VS 테토 니코카라 파트 분할, 듀엣 4/20곡 | P0 | 가수→색 매핑, 동시 가창 표현(줄 분할·그라데이션), 비활성 가수 감쇠율 | 프로젝트 트랙→가수 매핑에서 스타일을 자동 생성한다 | 적색 계열(테토 테마로 추정) 대형 텍스트를 빠르게 교대하면 적색 플래시(R/(R+G+B) ≥ 0.8)가 된다. 교대 빈도를 검사한다 |
| 글리치 텍스트(채널 오프셋, 블록 절단) | [BOOTH グリッチ風テキスト](https://booth.pm/ja/items/2285269), 「ダイダイ」 글리치 아트 | P0 | 강도, 블록 높이, 채널 오프셋(px), 발생 빈도(Hz), 시드 | 텍스트를 렌더 텍스처로 그린 뒤 화면 글리치 패스(5.3의 블록 노이즈·RGB 분리)를 재사용한다 | 발생 빈도는 프로젝트 공용 점멸 예산(기본 2Hz)을 소비한다 |
| 외곽선·그림자·글로우 텍스트 | AviUtl 기본 테두리 품질 불만([scrapbox](https://scrapbox.io/leje-campus/%E8%84%B1%E3%80%8CAviUtl%E3%81%A3%E3%81%BD%E3%81%95%E3%80%8D%E3%81%AE%E3%81%99%E3%82%9D%E3%82%81)) | P0 | 다중 외곽선 두께·색, 그림자 오프셋·블러, 글로우 반경 | SDF 거리값 임계로 외곽선 여러 겹을 한 셰이더에서 처리한다. 굵은 디스플레이 폰트는 `sdfGlyphSize`를 높인다([troika](https://www.npmjs.com/package/troika-three-text)) | 글로우 펄스는 5.3 글로우 규칙을 따른다 |
| 타이틀 로고 카드 | 「ダイダイ」 로고 전담 크레딧, 「チェリーポップ」 전용 폰트 공개([ITmedia](https://www.itmedia.co.jp/news/articles/2509/02/news105.html)) | P0 | 폰트, 효과 스택 프리셋, 등장 애니메이션 | 위 효과와 글로우를 묶은 프리셋 | 플래시 인트로를 쓰면 스트로브 규칙이 적용된다 |
| 번역 자막 트랙 | 「メズマライザー」 영어 번역 MV, [한글화 영상](https://www.youtube.com/watch?v=HBwSqC_PVrk) | P0 | 언어, 위치, 폰트, 원문 줄 연동 | 원문 줄 타임코드를 공유하는 보조 텍스트 레이어. SRT/VTT 내보내기 | 없음 |
| 타자기·스크램블(무작위 문자 뒤 확정) | 추정 수요(리릭 모션 관행) | P1 | 속도(문자/초), 치환 문자 집합, 시드 | 시드 고정 RNG로 frame마다 글리프를 고른다 | 낮음 |
| 루비(후리가나) | [kuroshiro](https://www.npmjs.com/package/kuroshiro), CSS ruby 지원 | P1 | 모노·그룹 루비, 크기 비율, 간격 | HarfBuzz 셰이핑([harfbuzzjs](https://www.npmjs.com/package/harfbuzzjs)) + 자체 배치 | 없음 |
| 세로쓰기·縦中横 | 기술 근거만(MV 수요 미확인) | P2 | 열 간격, 라틴·숫자 회전 규칙 | HarfBuzz `vert`/`vrt2` + 자체 열 배치 | 없음 |
| 필순(손글씨) 드로잉 | AE용 CuttanaNir 수요([YouTube 해설](https://www.youtube.com/watch?v=DyGd4-2Gy6I)) | P2 | 획 속도, 붓 두께 | 글리프 아웃라인 경로 길이 기반 stroke reveal(벡터 경로 필요) | 없음 |

폰트는 OFL 일본어 폰트(Noto Sans JP, M PLUS 1p, Dela Gothic One, DotGothic16, Zen Maru Gothic, Reggae One)와 Apache-2.0인 Kosugi Maru를 기본으로 싣는다([fontsource](https://www.npmjs.com/package/@fontsource/dela-gothic-one)). 명조가 강세이므로(2.3) 명조 계열 오픈 라이선스 폰트를 추가해야 한다. 다만 源ノ明朝 등의 라이선스와 OFL 예약 폰트명(RFN) 조항은 **미확인**이라 출시 전에 검증한다.

### 5.2 캐릭터 애니메이션

| 효과·기능 | 근거 | 우선 | 핵심 파라미터 | 구현 방식 | 점멸 안전 연계 |
|---|---|---|---|---|---|
| 트랜스폼 프리셋: 회전, 진자 스윙, 바운스, 스케일 펄스, 밈 루프 | 회전하는 테토(「テトリス」), 진자처럼 흔들리는 프레임(「メズマライザー」, 팬 고찰), 밈 같은 움직임(「オーバーライド」) | P0 | 피벗, 진폭(°·px), 주기(BPM 분할 1/1·1/2·1/4), 위상, 이징, 감쇠 | 레이어 변환을 (frame, beatGrid)의 해석식으로 계산한다. 키프레임이 필요 없다 | 움직임은 점멸이 아니다. 단 고대비 줄무늬 레이어를 회전시키면 공간 패턴 위험이 생기므로 최면 모티프 규칙(5.4)을 적용한다 |
| FK 본 리그(강체 파츠) | 소재 분리 관행, AviUtl 「パーツ分解」 | P0 | 본 계층, 피벗, 회전 제한 | 본 행렬(CPU) → 파츠 스프라이트 변환 | 없음 |
| 5모음 립싱크 | VRM 표정 `aa/ih/ou/ee/oh`([three-vrm](https://www.npmjs.com/package/@pixiv/three-vrm)), 노트 04 가나 매핑 | P0 | あ~お단 → 입 모양 5종. ん·っ·쉼표는 닫힘, ま/ば/ぱ행 자음 시작에는 짧은 닫힘, 장음(ー)은 직전 모음 유지, 최소 유지 프레임(제안: 2), 가수별 트랙 | 노트·가사로 viseme 트랙을 결정적으로 만든 뒤, 2D는 입 스프라이트를 교체하고 3D는 표정 가중치를 바꾼다. 한자 가사는 [pyopenjtalk](https://pypi.org/project/pyopenjtalk/)·kuromoji로 읽기를 구하고 사용자가 고친다 | 없음 |
| 듀엣 활성 강조 | 대비 연출(2.2), 가수별 트랙 | P0 | 강조 방식(스케일·채도·밝기), 전환 시간, 비활성 감쇠 | 스템 RMS 또는 노트로 활성 가수를 구해 키프레임을 자동 생성한다 | 빠른 콜 앤 리스폰스에서 **밝기**를 토글하면 점멸이 된다. BPM 170에서 8분음표마다 교대하면 밝기 전이는 초당 약 5.7회, 플래시(상반된 전이 한 쌍)로는 약 2.8회라 5초 넘게 이어지면 extended 경고 대상이고, 16분음표 교대면 플래시가 초당 약 5.7회로 3회를 넘는다(계산). 그래서 기본 강조는 스케일·채도로 둔다(제안) |
| 메시 변형(선형 블렌드 스키닝)과 가중치 페인팅 | 노트 04 런타임·에디터 범위 | P1 | 메시 밀도, 버텍스당 최대 4본 가중치, 변형 키 | 삼각분할 + 버텍스 셰이더 스키닝(본 행렬 텍스처) | 없음 |
| IK | 노트 04 | P1 | 체인 길이, 목표, 굽힘 방향 | 2본 해석해 + 고정 반복 수 FABRIK(결정성) | 없음 |
| 스프링 2차 모션(트윈테일·드릴 머리) | 추정 수요. 테토 공식 운영 계정 이름이 「ツインドリル」다([X](https://x.com/twindrill_teto/status/1889916232427774196)) | P1 | 강성, 감쇠, 중력, 최대 각 | 고정 스텝 적분(1/fps의 정수 분할). 임의 프레임 탐색 시 처음부터 재시뮬레이션하거나 캐시한다 | 없음 |
| 눈 깜빡임 자동 | 노트 04 자동 리깅 템플릿 | P1 | 평균 간격, 편차, 시드 | 시드 고정 RNG 스케줄 | 없음 |
| 저프레임 스프라이트 교체(우고메모풍) | 「ダイダイダイダイダイキライ」 | P1 | 홀드(2·3컷), 선 흔들림(boil) 강도 | 시간 양자화 + 노이즈 변위 셰이더 | 흑백 배경 교대가 생기면 휘도 플래시를 검사한다 |
| VRM 3D 레이어 + VRMA 모션 | 「人マニア」 자작 3D 댄스, Desktop Mate 테토 모션 26종([インフィニットループ](https://www.infiniteloop.co.jp/pr-blog/2025/08/desktop-mate-dlc-kasane-teto-release/)) | P1 | 카메라, MToon, 표정, 모션, 라이선스 메타 | three.js 렌더 텍스처를 Pixi 레이어로 합성한다. 업로드 때 `commercialUsage` 등을 파싱해 수익화·리믹스를 제한한다 | 3D 조명 효과에도 스트로브 규칙을 적용한다 |
| 영상 클립 합성 | 「イガク」 실사+CG | P1 | 블렌드, 크롭, 속도 | WebCodecs `VideoDecoder` 프레임을 텍스처로 쓴다 | 원본 영상의 점멸은 IRIS가 최종 판정한다 |
| 댄스 모션 프리셋, MMD | 「ダイダイ」 공식 안무, 「人マニア」, MMD 생태계 | P2 | 모션 클립, 리타깃 | babylon-mmd 별도 플러그인. VMD→VRM 리타깃 라이브러리는 미확인 | 없음 |

### 5.3 화면 효과

E5-3의 "P0 효과 12종"은 이 표의 P0 행이다. 노트 03은 데이터모시와 픽셀 소트를 P0으로 제안했다. 이 계획서는 둘을 P1로 내린다. 두 효과는 결정성을 확보하기 어렵고 구현 비용이 크기 때문이다(제안).

| 효과 | 근거 | 우선 | 핵심 파라미터 | 구현 방식(셰이더·패스) | 점멸 안전 연계 |
|---|---|---|---|---|---|
| 글로우·블룸 | Deep Glow 2와 Saber가 보카로 MV 정석 플러그인으로 소개된다([nanika.design](https://nanika.design/blog/1651/)). AviUtl 기본 기능. 직접 확인한 MV는 없다 | P0 | 임계값, 강도, 반경, 틴트, BPM 펄스 진폭 | 브라이트 패스 → 1/2·1/4·1/8 다운샘플 피라미드(분리형 가우시안 또는 dual-Kawase) → 업샘플 가산 합성(4–6패스) | 펄스로 상대휘도가 10% 이상 오르내리면 플래시 1쌍이다. 펄스 주파수가 점멸 예산을 쓴다 |
| 블록 노이즈 글리치 | [anm版ブロックノイズ](https://seguimiii.com/aviutl-tech/anmblocknoise), 「ダイダイ」 글리치 아트 | P0 | 블록 크기, 변위량, 발생 확률, 시드, 빈도(Hz) | 블록 좌표 해시(frame, seed)로 UV 오프셋을 정하는 단일 프래그먼트 패스 | 블록이 밝기를 반전시키면 휘도 플래시다. 빈도 예산 적용 |
| 디스플레이스먼트 글리치 | [ディスプレイスメントマップ グリッチ](https://seguimiii.com/aviutl-tech/displacementmapdeglitch) | P0 | 변위 맵, 변위량, 방향, 애니메이션 속도 | 변위 텍스처 샘플로 UV 오프셋(1패스) | 낮음 |
| 깜빡임 글리치(화면 チラつき) | [画面をチラつかせるグリッチ](https://seguimiii.com/aviutl-tech/glitch) | P0(상한 강제) | 빈도, 밝기 변화 폭, 지속 | 노출(곱셈) 변조 패스 | **고위험**: 기본 2Hz 상한, 3Hz 초과 설정 불가, 5초 넘게 이어지면 extended 경고 |
| 플래시·스트로브 | 「PPPP」 등 4곡의 점멸 경고([Vocaloid Lyrics Wiki](https://vocaloidlyrics.miraheze.org/wiki/PPPP/TAK)), 제작자 간 표기 호소 | P0(안전 우선) | 색, 면적 비율, 주파수(BPM 분할 스냅), 지속, 감쇠 곡선 | 단색 레이어 가산·스크린 합성 | **최고위험**: 기본값으로는 BPM 분할 가운데 2Hz 이하만 고를 수 있고, 사용자가 해제하면 3Hz까지 허용한다. BPM 170이면 2분음표(1.42Hz)는 기본 허용, 4분음표(2.83Hz)는 해제 후 허용하되 5초 넘게 이어지면 extended 경고, 8분음표(5.67Hz)는 차단한다(계산). 적색(R/(R+G+B) ≥ 0.8)과, 동시에 점멸하는 면적이 10° 시야의 25%(1024×768 기준 341×256 px, 화면의 약 11%)를 넘는 경우는 경고한다 |
| 카메라 팬·줌·셰이크·회전 | [あずまき note](https://note.com/azmk08477/n/n98f05a9ce2ec)(카메라 평행이동), 진자 프레임 | P0 | 경로 키프레임, 셰이크 진폭·주파수·시드, 회전, 모션 블러 on/off | 루트 컨테이너 변환 + 시드 노이즈 | 점멸은 없다. 시각 피로 상한은 근거가 없어 권고만 한다(미확인) |
| 그라디언트 맵 | AviUtl 4색 그라디언트 스크립트, 「メズマライザー」의 "비비드한 색채" | P0 | 그라디언트 스톱(2–4색), 혼합 비율 | 휘도 → 1D 그라디언트 텍스처 룩업(1패스) | 키프레임으로 스톱을 급변시키면 휘도·적색 플래시다 |
| 색보정(커브, 3D LUT, 채도, 대비) | AviUtl 기본 색보정([ニコニコ道具箱](https://niconico-toolbox.blog.jp/archives/655676.html)) | P0 | 커브, .cube LUT, 채도, 대비, 색조 | 3D LUT 텍스처 샘플(1패스) | 반전 키프레임은 플래시 1쌍이다 |
| 가우시안 블러 | AviUtl 기본 기능 | P0 | 반경, 방향 | 분리형 2패스 | 없음 |
| 모자이크 | AviUtl 기본 기능 | P0 | 블록 크기 | UV 양자화(1패스) | 없음 |
| 필름 그레인·노이즈 | AviUtl 기본 기능, 리미널 배경 질감 | P0 | 강도, 입자 크기, 시드 | 해시 노이즈 f(frame, seed) | 0.1° 미만 미세 패턴은 WCAG 예외로 본다(추정) |
| 분할 화면·마스크 와이프 | VS 대립 서사(「ダイダイ」) | P0 | 분할 각도, 경계 너비, 애니메이션 | SDF 마스크 셰이더 | 좌우 밝기 교대는 검사 대상이다 |
| RGB 분리(색수차) | 개별 MV 근거 없음(추정 수요) | P1 | 채널별 오프셋, 방사형 여부 | 채널별 3회 샘플 | 낮음 |
| 데이터모시풍 | [데이터모시·디코드 에러 스크립트](https://jisyu-seisaku.net/archives/489) | P1 | 지속 프레임, 블록 이동량, 시드 | 실제 P프레임을 조작하면 브라우저마다 결과가 달라 결정성이 깨진다(추정). 이전 프레임 피드백 버퍼와 블록 이동으로 근사한다 | 프레임 붕괴 순간의 밝기 급변을 검사한다 |
| 픽셀 소트 | 노트 03(AviUtl Pixel Sort 스크립트) | P1 | 휘도 임계, 방향, 최대 길이 | WebGPU 컴퓨트로 행·열 구간 정렬. WebGL2는 다중 패스로 근사한다(추정) | 낮음 |
| 픽셀화·팔레트 제한·디더 | 「PPPP」 도트 애니 크레딧, 「テトリス」 레트로 게임 | P1 | 픽셀 크기, 팔레트, 디더 행렬 | 양자화 + Bayer 디더(추정) | 큰 체커 디더는 공간 패턴 위험이다 |
| 방사형 블러·집중선 | 추정 수요 | P2 | 중심, 강도, 선 수 | 방사 샘플링 / 절차적 선 | 고대비 집중선은 패턴 위험이다 |
| 스캔라인·CRT·VHS | 추정 수요 | P2 | 라인 간격, 곡률, 노이즈, 롤링 속도 | 다중 패스 | 롤링하는 밝기 띠는 플래시·패턴 검사 대상이다 |
| 하프톤·스크린톤 | 추정 수요 | P2 | 망점 크기, 각도 | 셀 기반 원형 마스크 | 공간 패턴 위험이다 |
| 파티클(빛 입자, 꽃잎)·라이트 리크 | 추정 수요 | P2 | 방출량, 수명, 시드 | GPU 인스턴싱, 시드 고정 시뮬레이션 | 라이트 리크 펄스는 글로우 규칙을 따른다 |

### 5.4 모티프 팩과 템플릿 효과

| 모티프 | 근거 MV | 우선 | 핵심 파라미터 | 구현 방식 | 점멸·권리 연계 |
|---|---|---|---|---|---|
| 리미널 스페이스(백룸) 배경 팩 | 「ブレインロット」, 「ループザルーム」(2026 상반기 2·4위) | P1 | 배경 에셋, 그레인, 형광등 깜빡임 | 에셋 + 그레인 + 노출 변조 | 형광등 깜빡임 프리셋에는 스트로브 규칙을 적용한다 |
| 밈 콜라주·스티커 | 「オーバーライド」, 「テレパシ」, 「踊っチャイナ」([初音ミクWiki](https://w.atwiki.jp/hmiku/pages/65538.html)) | P1 | 스티커, 등장 타이밍, 루프 모션 | 이미지 레이어 + 트랜스폼 프리셋 | **권리**: 제3자 게임기·로고를 주된 연출로 쓰면 이벤트 규약 위반 소지가 있다(ボカコレ 2026 夏 3DS 사례). 업로드 체크리스트에 넣는다 |
| 최면 소용돌이·스트라이프 | 「メズマライザー」 | P2 | 줄 수, 대비, 회전 속도, 색 | 극좌표 절차적 셰이더 | **패턴 위험이 가장 큰 모티프다.** 고대비 규칙 줄무늬가 0.5초 넘게 이어지면 IRIS pattern failure가 날 수 있다. 대비·줄 수·면적에 기본 상한을 둔다(제안) |
| 레트로 게임 블록 낙하 | 「テトリス」(코로베이니키 인용은 권리 확인을 거쳤다, [ja.wikipedia](https://ja.wikipedia.org/wiki/%E3%83%86%E3%83%88%E3%83%AA%E3%82%B9_(%E6%9F%8A%E3%83%9E%E3%82%B0%E3%83%8D%E3%82%BF%E3%82%A4%E3%83%88%E3%81%AE%E6%9B%B2))) | P2 | 그리드, 낙하 속도, 색 | 결정적 그리드 시뮬레이션 | 줄 제거 플래시는 스트로브 규칙. 실제 게임 상표·디자인은 쓰지 않는다 |
| 도어스코프 어안 | 「モニタリング」([OTOIRO](https://otoiro.co.jp/topics/104605/)) | P2 | 왜곡 계수, 비네트 | 배럴 왜곡 셰이더 | 없음 |

### 5.5 트랜지션

gl-transitions 1.71.0의 125종은 MIT 123종, BSD 2종이며 트랜지션마다 작성자와 라이선스 메타데이터가 있다([npm](https://www.npmjs.com/package/gl-transitions)). 이 가운데 약 20종을 골라 P0으로 싣고, 메타데이터를 플러그인 매니페스트로 그대로 옮긴다(제안). 흰색으로 페이드하는 전환처럼 휘도가 크게 변하는 트랜지션은 점멸 프로파일 "휘도"로 분류해 예산에 넣는다. 비트에 맞춰 자르는 자동 컷은 다운비트·구간 분석(E2-3)이 생기는 P1에서 연다.

### 5.6 점멸 안전 파이프라인: 세 단계로 막는다

**1단계: 저작 단계 예산(에디터).** 프로젝트에 "점멸 예산"을 둔다. 모든 효과의 휘도 변조 주파수를 합산한 값이 기본 2Hz를 넘으면 경고하고, 3Hz를 넘는 설정은 막는다. 기준은 WCAG 2.3.1의 "1초 3회"와 IRIS의 extended failure(5초 연속 2–3회)다([W3C](https://github.com/w3c/wcag/blob/main/guidelines/sc/20/three-flashes-or-below-threshold.html), [IRIS](https://github.com/electronicarts/IRIS/blob/main/README.md)). 적색 플래시(한쪽 상태 R/(R+G+B) ≥ 0.8)와, 동시 점멸 면적이 10° 시야의 25%(1024×768 기준 화면의 약 11%)를 넘는 경우는 별도로 경고한다(제안).

**2단계: 미리보기 실시간 미터.** 렌더된 프레임을 작은 격자(예: 160×90, 제안)로 줄여 칸마다 상대휘도를 구한다. WCAG 정의에 따라 상대휘도가 10% 이상 변하고 어두운 쪽이 0.80 미만인 상반된 전이를 칸마다 찾은 뒤, 동시에 전이한 칸의 면적을 합산해 면적 임계를 넘는 전이를 1초 창으로 센다([정의](https://github.com/w3c/wcag/blob/main/guidelines/terms/20/general-flash-and-red-flash-thresholds.html)). 결과는 타임라인 히트맵으로 보여 준다. 이것은 근사치이며 인증이 아니다.

**3단계: 렌더 후 IRIS 게이트(서버).** 공개되는 모든 렌더 결과물에 IRIS를 돌린다. IRIS는 프레임별 CSV/JSON을 출력하고 flash·extended·pattern failure를 판정한다. 판정 결과는 다음과 같이 처리한다(제안).

| IRIS 판정 | 처리 |
|---|---|
| flash failure, pattern failure | 기본적으로 공개 전에 수정을 요구한다 |
| extended failure | 「点滅注意 / Flashing lights warning」 카드 삽입과 시청 전 클릭 재생을 강제한다 |

수정 없이 경고만으로 공개를 허용할지는 10절의 미결 사항이다. 판정 결과는 작품 메타데이터에 남겨 크로스포스트 설명란의 경고 문구 자동 삽입(8절), VocaDB의 "flashing lights warning"과 같은 태그 표시([VocaDB](https://vocadb.net/T/2913/flashing-lights-warning)), 추천 노출 조정에 쓴다.

IRIS는 3-clause BSD 형태라 상업 서비스에서 쓸 수 있다. 단 의존 라이브러리인 FFmpeg(LGPL 2.1)의 고지와 링크 방식은 지켜야 한다([NOTICE](https://github.com/electronicarts/IRIS/blob/main/NOTICE.txt)).

**이 장르에 특히 중요한 이유가 두 가지 있다.** 하나는 테토의 상징색인 적색(추정)으로 전면 점멸을 하면 적색 플래시 기준에 걸린다는 점이다. 다른 하나는 「メズマライザー」의 대표 모티프인 고대비 회전 줄무늬가 정확히 IRIS의 공간 패턴 판정 대상이라는 점이다. 두 효과를 사용자 요청대로 그대로 제공하면 가장 인기 있는 연출이 가장 위험한 연출이 된다. 그래서 프리셋 단계에서 상한을 걸어 두는 것이 사후 차단보다 낫다(추정).

## 6. 아키텍처: 하향 단방향 트리에 공유 노드 7개만 허용한다

### 6.1 설계 원칙

**응집과 결합의 기준.** 모듈은 "같은 이유로 함께 바뀌는 코드"끼리 묶는다(응집). 이 기준으로 경계를 나누면 바뀌는 이유마다 다음과 같이 격리된다.

| 바뀌는 이유 | 격리하는 모듈 |
|---|---|
| 엔진 업데이트(예: 공식 문서가 없는 SV2 .svp 스키마 변경) | 프로젝트 파서 |
| 외부 API 정책 변경(YouTube 쿼터 개편, X 요금 개정) | 플랫폼별 커넥터 |
| 효과 추가 | 효과 플러그인 |

모듈은 아래쪽 자식의 공개 진입점에만 의존하고, 형제와 위쪽은 import하지 않는다(결합).

**형제가 협력해야 할 때.** 형제끼리 import하는 대신 의존성 역전을 쓴다. 필요한 쪽이 인터페이스(포트)를 선언하고, 앱의 composition root가 구현을 주입한다. 플러그인 등록 선례로는 PixiJS v8 확장 시스템과 Mediabunny의 코덱 확장 패키지가 있다([npm untitled-pixi-live2d-engine](https://www.npmjs.com/package/untitled-pixi-live2d-engine), [npm @mediabunny/aac-encoder](https://www.npmjs.com/package/@mediabunny/aac-encoder)).

**엄밀한 트리는 불가능하다.** 공유 core 때문에 순수한 트리는 성립하지 않는다(노트 04 §10). 그래서 형제 간 import 금지, 하향 단방향 의존, 순환 0이라는 세 조건을 "트리형 계층"의 운영 정의로 삼는다(제안). 여러 부모가 불가피하게 공유하는 노드는 **⚠️로 표시하고 7개로 묶어 둔다.** 새 공유 노드를 추가하려면 아키텍처 결정 기록(ADR)이 필요하다.

### 6.2 모듈 트리(부모 → 자식 = "부모가 자식을 import한다")

```text
apps/  ── 최상위 composition root. 앱끼리 import 금지
│
├─ studio (브라우저 원스크린 에디터)                         [type:app, scope:web]
│  ├─ shell (상태 저장소, 이벤트 버스, DI 컨테이너; 앱 내부)
│  ├─ features/import ─────────── libs/project-formats, libs/psd-import
│  ├─ features/timeline ───────── libs/edit-commands
│  ├─ features/lyrics ─────────── libs/lyric-timing, libs/ja-text
│  ├─ features/rig-editor ─────── libs/rig-authoring, libs/lipsync-gen
│  ├─ features/effects-panel
│  ├─ features/safety-meter
│  ├─ features/export ─────────── libs/encode-client, libs/audio-mixdown
│  ├─ features/publish-dialog
│  ├─ libs/audio-preview (WebAudio 재생, 파형, BPM 검출)
│  ├─ ⚠️ engine/render-runtime ……………………………… (render-worker와 공유)
│  └─ ⚠️ foundation/ui-kit ……………………………………… (site와 공유)
│
├─ site (커뮤니티 웹: 작품 페이지, 피드, 랭킹, 대시보드)      [type:app, scope:web]
│  ├─ features/watch (hls.js 플레이어, 점멸 경고 카드, 유효 조회 하트비트)
│  ├─ features/feed-ranking
│  ├─ features/creator-dashboard
│  └─ ⚠️ foundation/ui-kit ……………………………………… (studio와 공유)
│
├─ api (Node 백엔드)                                         [type:app, scope:server]
│  ├─ services/identity (계정, OAuth 토큰 금고)
│  ├─ services/assets (업로드, 저장소, 서명 URL)
│  ├─ services/projects (프로젝트 문서, 버전)
│  ├─ services/jobs (렌더·트랜스코드·분석 작업 큐)
│  ├─ services/community (작품, 좋아요, 저장, 댓글)
│  ├─ services/metrics (유효 조회, 랭킹, 이상치 보류)
│  ├─ services/rights-lineage (자산 권리, 계보 그래프, 호환성 검사)
│  ├─ services/moderation (신고·테이크다운, 점멸 판정 기록, AI 라벨)
│  └─ services/publish
│     ├─ connectors/youtube, connectors/tiktok, connectors/instagram, connectors/x
│     └─ assist/niconico, assist/bilibili (메타데이터 템플릿 생성)
│
├─ render-worker (headless Chromium + FFmpeg)                [type:app, scope:server]
│  ├─ worker/frame-pump (프레임 순차 렌더, 백프레셔)
│  ├─ worker/encode-ffmpeg
│  └─ ⚠️ engine/render-runtime (빌드된 번들 아티팩트로 소비)
│
├─ media-worker (트랜스코드·패키징·검사)                    [type:app, scope:server]
│  ├─ media/transcode-hls (FFmpeg + Shaka Packager)
│  ├─ media/flash-scan-iris (IRIS 래퍼)
│  └─ media/fingerprint (Chromaprint, P1)
│
└─ align-worker (Python: Demucs, whisperX, pyopenjtalk, beat-this)  [scope:server]
   └─ TS 패키지 의존 없음. ⚠️ foundation/contracts가 생성한 JSON Schema만 읽음

engine/render-runtime ⚠️  (공개 진입점: createRenderer / renderFrame(project, frameIndex))
├─ engine/scene (장면 그래프, 키프레임 평가 = 프레임의 순수 함수)
├─ engine/compositor-pixi (PixiJS v8, WebGPU 우선·WebGL2 폴백)
├─ engine/rig-runtime (FK, 선형 블렌드 스키닝, IK, 결정적 스프링)
├─ engine/text-layout (SDF 글리프, 루비, 세로쓰기)
├─ engine/flash-probe (프레임 휘도·적색 통계)
├─ engine/layer-3d (P1: three + three-vrm → 렌더 텍스처)
└─ engine/effects-standard ── plugins/effect-glow, effect-glitch-block, … , transition-gl-*

foundation/  ── 모두 ⚠️ 공유 노드. 내부 의존도 하향만 허용
├─ project-model ── core-time
├─ plugin-api ───── core-time      (Effect·Transition 인터페이스, 매니페스트 스키마, RenderNode 계약, 레지스트리)
├─ contracts ────── project-model  (API DTO, OpenAPI·JSON Schema 생성물)
├─ safety-rules                    (WCAG·IRIS 임계값 상수와 순수 판정 함수)
├─ core-time                       (RationalTime, 템포 맵, BPM 그리드)
└─ ui-kit                          (표현 컴포넌트, 디자인 토큰)
```

foundation 노드는 여러 앱과 엔진이 함께 쓰므로 트리 안에서 반복 표기하지 않았다. 각 노드의 부모(소비자) 목록은 6.3 표에 있다. 트리를 읽을 때 놓치기 쉬운 결정이 네 가지 있다(모두 제안).

**① 프로젝트 파일 파싱은 브라우저 한 곳에만 둔다.** 파싱은 `libs/project-formats`의 부모가 `features/import` 하나뿐이다. 서버는 정규화된 노트 JSON만 받아 `project-model`로 검증한다. 원본 엔진 파일이 서버에 갈 필요가 없고, 파서가 두 언어로 중복되지 않는다.

**② 립싱크는 편집 시점에 데이터로 굳힌다.** `libs/lipsync-gen`이 viseme 키프레임을 생성해 `project-model`에 저장한다. 그래서 렌더 런타임은 데이터를 재생만 한다. 가사 읽기(가나)도 `features/lyrics`가 노트마다 모델에 저장한다. 덕분에 `libs/ja-text`를 lipsync-gen과 공유하지 않아도 된다.

**③ 렌더 런타임은 각 앱의 root만 import한다.** studio의 각 feature는 root가 DI로 넘겨 준 퍼사드를 쓰고, 그 퍼사드의 타입은 `plugin-api` 인터페이스로 정한다. 같은 이유로 api 서비스끼리도 서로 import하지 않는다. 예를 들어 `publish`는 `TokenProvider` 포트를 선언하고, `identity`의 구현은 api root가 연결한다.

**④ 서버 인코딩은 FFmpeg로 한다.** 클라이언트 인코딩 모듈(Mediabunny)을 서버와 공유하지 않는다. 서버에서만 실행하는 GPL FFmpeg는 배포에 해당하지 않는다(추정, 법무 확인).

### 6.3 ⚠️ 공유 노드 7개와 완화책

| 공유 노드 | 부모(소비자) | 공유가 불가피한 이유 | 완화책(제안) |
|---|---|---|---|
| ⚠️ `engine/render-runtime` | studio, render-worker | 미리보기와 서버 결과물이 같은 코드여야 픽셀 결과가 맞는다. WebGPU와 WebGL2 사이, GPU 벤더 사이의 픽셀 일치는 보장되지 않는다(노트 04 §2) | 공개 진입점 2개만 노출한다. UI·네트워크·저장소 의존을 금지한다. 버전 번들 아티팩트로 배포하고 프로젝트에 런타임 버전을 기록한다. 두 호스트에서 골든 프레임 회귀 테스트를 돌린다. 호환이 깨지는 변경은 메이저 버전과 마이그레이션으로만 한다 |
| ⚠️ `foundation/project-model` | render-runtime, studio(DI 타입), api, contracts | 프로젝트 문서는 클라이언트와 서버가 함께 읽는 데이터다 | 타입·스키마·마이그레이션만 담는다(런타임 로직 금지). 스키마를 바꾸려면 ADR이 필요하다. 이전 버전 문서 픽스처로 마이그레이션을 테스트한다 |
| ⚠️ `foundation/core-time` | project-model, plugin-api, engine 하위 | 유리수 시간과 템포 맵은 모든 계층의 공통 언어다 | 외부 의존이 없는 순수 수학만 둔다. API를 동결하고 단위 테스트 100%를 목표로 한다 |
| ⚠️ `foundation/plugin-api` | render-runtime과 하위 모듈, 효과 플러그인, studio(DI 타입) | 의존성 역전의 접점이다. 인터페이스가 없으면 엔진이 구체 효과를 import하게 된다 | 인터페이스와 매니페스트 스키마만 둔다. 매니페스트에 버전을 매기고, 구버전 플러그인용 어댑터를 둔다 |
| ⚠️ `foundation/safety-rules` | engine/flash-probe, media/flash-scan-iris | 클라이언트 미터와 서버 게이트가 같은 임계값을 써야 판정이 엇갈리지 않는다 | 상수와 순수 함수만 둔다. 변경 시 안전 리뷰를 받는다. 작품의 점멸 기록에 규칙 버전을 저장한다 |
| ⚠️ `foundation/contracts` | studio, site, api, align-worker(JSON Schema) | 언어가 다른 서비스 사이의 데이터 계약이다 | 단일 원천에서 생성하고 손으로 고치지 않는다. 하위 호환 변경만 허용한다. 소비자별 계약 테스트를 둔다 |
| ⚠️ `foundation/ui-kit` | studio, site | 같은 디자인 시스템을 쓴다 | 표현 컴포넌트와 디자인 토큰만 둔다. 도메인과 foundation 패키지 import를 금지한다. 시각 회귀 테스트를 둔다 |

foundation 안의 의존도 아래 방향으로만 흐른다. `contracts → project-model → core-time`, `plugin-api → core-time` 순서이고, `safety-rules`와 `ui-kit`은 의존이 없다. 따라서 foundation 내부에도 순환이 생길 수 없다.

### 6.4 경계를 강제하는 도구

| 강제 지점 | 도구(버전·라이선스) | 규칙(제안) |
|---|---|---|
| 패키지 그래프 | pnpm workspaces(12.9.1, MIT) + **Nx**(23.2.1, MIT)의 `@nx/enforce-module-boundaries` | 태그 `type:app·feature·lib·engine·foundation`과 `scope:web·server·shared`를 쓴다. 각 feature 소유 lib에는 `owner:<feature>` 태그를 달아 그 feature만 import하게 한다. `ignoredCircularDependencies`는 빈 배열, `allowCircularSelfDependency`는 false, `banTransitiveDependencies`는 true로 둔다([npm @nx/eslint-plugin](https://www.npmjs.com/package/@nx/eslint-plugin)) |
| 파일 단위 순환·형제 금지 | **dependency-cruiser**(18.5.0, MIT) | `no-circular`를 error로 두고 CI 필수 체크로 건다. features 형제 import, foundation → 상위 import, `engine/*` 형제 import를 막는 `forbidden` 규칙을 추가한다([npm](https://www.npmjs.com/package/dependency-cruiser)) |
| 편집기 즉시 피드백 | **eslint-plugin-boundaries**(7.2.0, MIT) | `boundaries/dependencies`를 `default: "disallow"`로 두고, Nx 태그와 같은 정책을 `policies`로 옮긴다([npm](https://www.npmjs.com/package/eslint-plugin-boundaries)) |
| 컴파일 단계 | TypeScript project references | 순환하면 "Project references may not form a circular graph" 오류가 난다([npm typescript](https://www.npmjs.com/package/typescript)). 네이티브 TS 7.x(7.0.2, 2026-07-08)의 project references 지원은 미확인이므로 6.x로 시작해 검증 후 올린다 |
| 미사용·누수 | knip(6.40.0, ISC), madge(8.0.0, MIT, 시각화) | 미사용 export와 의존성을 탐지한다. 그래프 이미지를 아키텍처 문서에 자동 첨부한다([npm knip](https://www.npmjs.com/package/knip), [npm madge](https://www.npmjs.com/package/madge)) |
| 언어 경계 | contracts가 생성한 JSON Schema + 계약 테스트 | align-worker(Python)는 TS 코드를 import하지 않는다. Python 내부 계층 검사 도구는 미조사다 |

Turborepo(2.11.7)의 `turbo boundaries`도 태그 기반 allow/deny를 지원하지만 Experimental이다. Turborepo 문서는 project references를 권하지 않는다([npm turbo](https://www.npmjs.com/package/turbo)). 그래서 Nx를 기본으로 권고한다. Turborepo를 고르면 dependency-cruiser를 필수 게이트로 유지한다(10절 미결 사항).

```jsonc
// Nx depConstraints 발췌(제안)
[
  { "sourceTag": "type:foundation", "onlyDependOnLibsWithTags": ["type:foundation"] },
  { "sourceTag": "type:engine",     "onlyDependOnLibsWithTags": ["type:engine", "type:foundation"] },
  { "sourceTag": "owner:lyrics",    "onlyDependOnLibsWithTags": ["owner:lyrics", "type:foundation"] },
  { "sourceTag": "scope:web",       "notDependOnLibsWithTags": ["scope:server"] },
  { "sourceTag": "scope:server",    "notDependOnLibsWithTags": ["scope:web"] }
]
```

`type:engine`끼리의 형제 import는 Nx 태그만으로 막기 어렵다. 그래서 dependency-cruiser의 경로 규칙("`engine/<A>` → `engine/<B>` 금지, 단 render-runtime 루트 → 하위는 허용")으로 보완한다.

### 6.5 배포 단위와 데이터 흐름

배포 단위는 앱 하나가 하나씩이다.

| 배포 단위 | 내용 |
|---|---|
| 브라우저 studio | 단일 페이지 에디터 |
| 브라우저 site | SSR 커뮤니티 |
| api | 작업 큐 포함 |
| render-worker | GPU 인스턴스. 없으면 SwiftShader CPU 폴백으로 크게 느려진다 |
| media-worker | 트랜스코드·패키징·검사 |
| align-worker | Python. 분리·정렬에 GPU가 유리하다(추정) |

오브젝트 저장소와 CDN 단가는 미확인이다. 렌더 결과물은 클라이언트가 렌더한 MP4를 청크로 업로드하면, media-worker가 IRIS 검사와 HLS 패키징을 하고, moderation이 판정을 기록한 뒤 community에 공개되며, 마지막으로 publish 작업 큐가 외부 업로드를 하는 순서로 흐른다.

**studio와 site는 서로 다른 오리진에 둔다(제안).** cross-origin isolation(COOP/COEP)은 ffmpeg.wasm 멀티스레드, ONNX Runtime Web 스레드, demucs-web에 필요하다. 그런데 이를 켜면 외부 이미지와 임베드에 영향을 준다([web.dev](https://web.dev/articles/webassembly-threads?authuser=0), 노트 04 §1). MVP는 WASM 스레드를 쓰지 않으므로 지금은 필요 없다(추정). 오리진을 나눠 두면 나중에 studio에서만 격리를 켤 수 있고, 외부 임베드가 많은 site는 영향을 받지 않는다.

## 7. 단계별 로드맵: 각 단계는 범위표와 종료 기준으로 닫힌다

**MVP 정의(제안).** MVP는 Phase 1의 출시물로, 데스크톱 브라우저 한 화면에서 다음 흐름을 끝까지 마칠 수 있는 최소 제품이다. 사용자는 외부 엔진(VOCALOID6 Editor, Piapro Studio NT2, SV Studio 2 등)으로 만든 미쿠·테토 곡의 오디오와 선택적 프로젝트 파일을 올리고, 자동 가사 타이밍·PSD 컷아웃 캐릭터·5모음 립싱크·P0 효과 12종·템플릿 4종으로 1080p30 MV를 만든다. 3중 점멸 검사를 통과한 작품은 플랫폼에 공개되어 좋아요·유효 조회수·댓글을 받고, MP4 다운로드, YouTube 업로드(감사 전에는 비공개), 니코니코 투고 어시스트로 밖에 내보내진다. 기간은 엔지니어 6–8명을 가정한 **추정**이며, 노트 04의 2D 리그 공수 추정(런타임 3–6인월, 에디터 6–12인월)을 반영했다.

**범위 통제 규칙(제안).** 구현 중 범위가 늘어나는 것을 다음 네 규칙으로 막는다.

| 규칙 | 내용 |
|---|---|
| 범위표가 계약 | 진행 중에 들어온 새 요청은 다음 Phase 백로그로만 들어간다 |
| 교환만 허용 | 현재 Phase에 꼭 넣어야 하면 같은 크기의 항목을 빼야 하고, 제품 책임자 승인과 ADR 기록이 필요하다 |
| 종료 기준 우선 | 종료 기준을 채우지 못하면 다음 Phase 기능에 착수하지 않는다 |
| 승인 지연은 폴백으로 | 외부 승인(감사·App Review)이 늦어지면 범위를 줄이지 않고 폴백 경로(다운로드·비공개 업로드)를 유지한다 |

### Phase 0: 기술 검증과 승인 착수(6–8주, 추정)

| 구분 | 내용 |
|---|---|
| 포함 | ① 브라우저 렌더 스파이크: WebCodecs + Mediabunny로 4분 1080p30 MP4를 만든다(Chrome·Edge, Firefox 130+, Safari 26+). ② 같은 프로젝트를 headless Chromium에서 렌더해 골든 프레임과 비교한다. ③ .svp·.vpr·.ppsf(NT)·.ustx·MIDI 파서와 템포 환산, 가사 타이밍. ④ PSD → FK 리그 → 5모음 립싱크 프로토타입. ⑤ 점멸 미터와 IRIS의 판정 대조. ⑥ 모노레포 골격과 경계 도구 CI(6.4). ⑦ OSS 라이선스 감사 파이프라인. ⑧ Google 브랜드·민감 범위 검증 신청과 YouTube API 감사 준비·제출. ⑨ 크립톤 문의 발송(서비스명, 캐릭터 사용, 테토 상업 창구). ⑩ 11절 검증 항목 중 Phase 0 항목 |
| 제외 | 사용자용 UI 완성도, 커뮤니티 기능, 외부 업로드 구현, 디자인 시스템 확장 |
| 종료 기준 | 다음을 모두 충족해야 한다(제안). (a) 4분 1080p30 테스트 프로젝트를 세 브라우저 계열에서 렌더한다. (b) 같은 백엔드 계열에서 클라이언트와 서버의 골든 프레임 차이가 허용 범위 안이다(예: PSNR 40dB 이상). (c) 형식별 샘플 10개 이상에서 노트 onset 오차 0 tick, 초 환산 오차 1프레임 이하다. (d) IRIS가 실패로 판정한 테스트 클립을 미터가 모두 경고한다. (e) 클라이언트 번들에 GPL·AGPL 0건, 의존성 순환 0건이다. (f) Google 검증 신청과 크립톤 문의를 마쳤다 |

### Phase 1: MVP(4–6개월, 추정)

| 구분 | 내용 |
|---|---|
| 포함 | 4절의 **P0 전부**. 수집(E1), 오디오 동기(E2), 가사·타이포(E3), PSD 컷아웃·FK·립싱크(E4), 합성 엔진·P0 효과 12종·트랜지션 20종·카메라(E5), 템플릿 4종과 필수 커스터마이즈(E6), 점멸 3중 검사·경고 카드·권리·AI 신고·신고 처리(E7), 클라이언트 렌더 1080p30·썸네일(E8), 작품 페이지·좋아요·저장·유효 조회수·댓글·신규/인기 피드·부모 작품 링크(E9), 다운로드·YouTube 업로드·니코니코 어시스트·작업 큐(E10), 계정·토큰 금고·약관·라이선스 감사(E11) |
| 지원 환경 | 데스크톱 Chrome·Edge 최신, Firefox 130+, Safari 26+. 미지원 환경에서는 편집을 막고 시청만 허용한다 |
| 제외 | 서버 렌더와 4K, 9:16 자동 리프레임, 메시 변형·IK·스프링, VRM, 오디오만 있을 때의 강제 정렬과 음원 분리, TikTok·Instagram·X·Bilibili, fork와 소재 패키지, 시간 동기 코멘트, 부문별 랭킹, 이벤트, 수익화, 커버곡, 모바일 편집, Live2D·Spine, 가창 합성 |
| 종료 기준(출시 판정) | 다음을 모두 충족해야 한다(제안). ① 처음 쓰는 크리에이터 5명 중 4명이 오디오 + 프로젝트 파일로 3–4분 듀엣 MV를 60분 안에 완성한다. ② 지원 브라우저에서 테스트 프로젝트 렌더 성공률 95% 이상, 레퍼런스 노트북에서 1080p 미리보기 30fps를 낸다. ③ P0 효과가 같은 입력과 같은 백엔드에서 동일한 프레임 해시를 낸다. ④ 공개 작품 100%에 IRIS 기록이 있고, 실패는 규칙대로 처리된다. ⑤ 봇 트래픽 시험에서 유효 조회 규칙이 부풀림을 막는다. ⑥ YouTube 업로드 E2E(비공개 포함)와 니코니코 어시스트가 동작하고, 감사 신청이 제출돼 있다. ⑦ 신고 접수부터 비공개 처리까지 24시간 이내다. ⑧ CI에서 순환 0, 경계 위반 0, 라이선스 감사 통과다 |

### Phase 2: 숏폼과 리그 고도화(3–4개월, 추정)

| 구분 | 내용 |
|---|---|
| 포함 | 9:16 자동 리프레이밍·하이라이트 추출과 세로 프리셋(E8-3·4), 서버 렌더와 4K(E8-2), TikTok 초안 경로(감사 후 Direct Post), Instagram Reels(App Review 후), 감사를 통과하면 YouTube 공개 업로드, 메시 변형·가중치·IK·스프링·눈 깜빡임(E4-5~8), 오디오만 있을 때 강제 정렬과 음원 분리(E2-5, E3-3), 다운비트·구간 분석(E2-3), 루비, 라우드니스, 밈 콜라주·리미널 배경 팩, P1 화면 효과(RGB 분리, 데이터모시풍, 픽셀 소트, 픽셀화), AI 신고 필터와 허위 신고 제재, 급상승(운영 승인 게이트)과 부문별 랭킹, 시간 동기 코멘트, 크리에이터 대시보드와 외부 통계 동기화, 팔로우 |
| 제외 | 3D(VRM), fork와 소재 패키지, 이벤트, X, Bilibili 연동, 수익화, 커버 |
| 종료 기준 | 다음을 모두 충족해야 한다(제안). ① 세로 프리셋 출력이 Shorts 자동 분류 조건(세로·3분 이하)을 충족하고, 커버·리믹스 플래그 작품은 60초 이하로 제한된다. ② TikTok·Instagram 승인 상태가 확정된다(승인 또는 폴백 유지 결정). ③ 오디오만 있는 업로드의 줄 시작 타이밍 오차 목표를 Phase 0 측정치로 정하고 달성한다(합성 보컬 정확도는 미확인이라 목표값을 미리 정하지 않는다). ④ 서버 렌더와 클라이언트 렌더의 골든 프레임 일치를 유지한다 |

### Phase 3: 2차 창작, 3D, 이벤트(4–6개월, 추정)

| 구분 | 내용 |
|---|---|
| 포함 | 계보 그래프(유형 엣지, 불변, 삭제된 원작 노드 보존), fork·재사용 허가 플래그, 소재 패키지(무음 MV·오프보컬·레이어 PNG), 라이선스 호환성 검사, 녹음 지문 대조(E7-10), VRM 3D 레이어와 모델 라이선스 게이트, 우고메모풍 스프라이트 교체, 모티프 팩(최면·레트로 블록·어안), 이벤트(규칙 사전 공지·부문 태그·이의 제기), Bilibili 투고 어시스트, X 비용 게이팅 게시, 역할별 협업 크레딧, SV2 Pro 스크립트 배포 |
| 제외 | 커버(포괄계약 전), 수익 분배, Live2D·Spine, 가창 합성, MMD |
| 종료 기준 | 다음을 모두 충족해야 한다(제안). ① 계보 무결성 테스트를 통과한다(자식이 부모 표기를 지울 수 없고, 부모를 삭제해도 노드가 남는다). ② 비영리·개변 금지 자산이 수익화·fork 경로에서 100% 차단된다. ③ 이벤트를 1회 운영하고, 규칙을 사전 공지했으며, 이의 제기를 기한 안에 처리한다. ④ VRM 업로드의 라이선스 메타가 수익화·리믹스 제한에 반영된다 |

### Phase 4: 계약과 수요가 확인되면 여는 조건부 확장

각 항목은 시작 조건이 충족될 때만 착수한다(제안).

| 확장 | 시작 조건 | 근거 |
|---|---|---|
| 기존 관리곡 커버 허용, 본가 재현 템플릿, 원곡 메타데이터 강제 | JASRAC·NexTone(일본)과 KOMCA 등(한국) 포괄계약 체결 | 포괄계약은 서비스 단위([JASRAC](https://www.jasrac.or.jp/information/topics/20/ugc.html)) |
| 공식 미쿠·테토 리그·템플릿·마케팅 | 크립톤 상업 라이선스(테토 창구 포함) | PCL·EULA·2024-12 성명 |
| 플랫폼 내 가창 합성 | Yamaha·Crypton·Dreamtonics/AHS와 B2B 계약(옵션 D). 또는 상용 허용 보이스와 자체 학습 보코더(옵션 C) | 2.1 통합 옵션표, 보코더 CC BY-NC-SA |
| Live2D 모델 임포트 플러그인 | Expandable Application 심사·출판 계약 | [Live2D](https://www.live2d.com/en/sdk/license/expandable/) |
| 수익 분배(子ども手当형), 응원 포인트, 함께 보기, MMD | 수익 모델 확정, 커뮤니티 규모 | [ニコニコ 奨励プログラム](https://blog.nicovideo.jp/niconews/172165.html), [Kiite](https://staff.aist.go.jp/m.goto/PAPER/SIGMUS202109tsukuda.pdf) |

## 8. 외부 업로드 전략: YouTube는 승인 경로가 일정을 정하고, 니코니코는 어시스트로 간다

### 8.1 공통 구조와 UX 원칙

**작업 처리 구조(제안).** 업로드는 브라우저가 아니라 백엔드 작업 큐에서 한다. 렌더가 끝나 서버에 있는 파일을 기준으로 플랫폼별 게시 작업을 만들고, 작업마다 "업로드 중 → 처리 중 → 게시됨 / 실패 / **비공개 잠김**" 상태를 추적한다. 쿼터와 레이트 리밋은 플랫폼마다 따로 센다. YouTube는 프로젝트 단위로 하루 100건, TikTok은 사용자 토큰당 분당 6회 init이다. 게시 결과(영상 ID와 URL)는 작품 페이지에 연결한다(노트 05 §10).

**UX 원칙.** YouTube RMF와 TikTok Content Sharing Guidelines의 공통분모를 따른다. 사용자가 제목·설명·공개 범위를 직접 정하고([RMF](https://developers.google.com/youtube/terms/required-minimum-functionality)), 상호작용 설정과 상업 콘텐츠 공개는 기본값을 꺼 둔다([TikTok](https://developers.tiktok.com/docs/en/content-sharing-guidelines)). 여기에 AI·합성 콘텐츠 공개 토글, 게시 전 미리보기, 플랫폼 약관 동의 문구를 더한다(제안).

**일괄 자동 업로드는 만들지 않는다.** 같은 템플릿에 가사만 바꾼 영상을 대량으로 올리거나 Shorts 변형을 연속 업로드하면 YPP "inauthentic content"로 평가될 위험이 크다(추정, [Plagiarism Today](https://www.plagiarismtoday.com/2025/07/08/youtube-targets-inauthentic-content/)). 그래서 업로드마다 사용자가 직접 확인하게 한다.

### 8.2 YouTube: 감사와 쿼터 제약을 출시 계획에 넣는다

**승인 경로.**

| 단계 | 내용 | 기간 | 감사 전 동작 |
|---|---|---|---|
| ① 브랜드 검증 | OAuth 동의 화면 | 2–3 영업일([Google Cloud FAQ](https://support.google.com/cloud/answer/13463817?hl=en)) | 미검증 앱 경고 화면, 신규 사용자 100명 상한([Unverified apps](https://support.google.com/cloud/answer/7454865?hl=en)) |
| ② 민감 범위 검증 | `youtube.upload`. CASA는 restricted 범위에만 요구된다([Google](https://developers.google.com/identity/protocols/oauth2/production-readiness/restricted-scope-verification)) | 10 영업일(같은 FAQ). 2차 출처는 2–4주 | 위와 같다 |
| ③ YouTube API Services 감사 | "Audit and Quota Extension Form" 하나로 감사와 쿼터 확장을 신청한다([양식](https://support.google.com/youtube/contact/yt_api_form?hl=fa)) | **미확인** | 2020-07-28 이후 생성된 프로젝트의 업로드는 비공개로 잠긴다. API 파라미터로 우회할 수 없다([Videos: insert](https://developers.google.com/youtube/v3/docs/videos/insert), [Ayrshare](https://www.ayrshare.com/solutions/google-api-error-403-unverified-app-how-to-fix-the-audit-pipeline/)) |

감사 기간을 알 수 없으므로 신청은 Phase 0에 시작한다(7절). 그때까지 공개 게시 경로는 두 가지다(제안). 하나는 MP4 다운로드와 메타데이터 복사 뒤 YouTube Studio 업로드를 안내하는 경로다. 다른 하나는 감사를 마친 통합 게시 API 사업자를 경유하는 경로인데, 이 경우 사용자 토큰을 제3자가 보관하므로 개인정보 처리 위탁 고지와 약관 검토가 필요하다([Ayrshare](https://www.ayrshare.com/docs/apis/post/social-networks/youtube)).

**쿼터 산술(계산, 2026-06-01 이후 기준)**

| 항목 | 수치 | 출처 |
|---|---|---|
| `videos.insert` | "Video Uploads" 버킷, 호출당 1 unit, **프로젝트당 하루 100회**. 사용자별이 아니라 플랫폼 전체 합산이다(추정) | [Quota Calculator](https://developers.google.com/youtube/v3/determine_quota_cost) |
| 그 밖의 호출 | 기본 하루 10,000 units 버킷. `thumbnails.set` 약 50, `captions.insert` 400 | 같은 출처 |
| 하루 100건 업로드에 모두 썸네일을 붙이면 | 5,000 units | 계산 |
| 남는 5,000 units로 가능한 자막 업로드 | 하루 12건 | 계산 |

그래서 가사는 설명란에 넣는 것을 기본으로 하고, 자막 업로드는 선택 기능으로 두며 하루 한도를 건다(제안). 하루 100건 넘는 업로드가 예상되면 그 전에 증설을 신청한다. 다만 Video Uploads 버킷 증설 절차와, 2026-06 이후 썸네일·자막 단가가 바뀌었는지는 **미확인**이다.

**업로드 구현.** resumable 프로토콜은 세션을 시작하고 응답 `Location`의 세션 URI를 저장한 뒤 청크를 보내는 방식이다. 마지막을 뺀 청크는 256KB의 배수여야 하고(예: 8 MiB), 끊기면 서버가 돌려준 `308 Resume Incomplete`와 `Range`를 보고 이어서 보낸다([Resumable Uploads](https://developers.google.com/youtube/v3/guides/using_resumable_upload_protocol)). 필드는 다음과 같이 다룬다([Videos](https://developers.google.com/youtube/v3/docs/videos)).

| 필드 | 처리(제안) |
|---|---|
| `status.privacyStatus` | 사용자가 명시적으로 고르게 하고, 기본값을 강요하지 않는다 |
| `status.publishAt` | 예약 공개용이다. `private`일 때만 설정할 수 있다 |
| `status.selfDeclaredMadeForKids` | 사용자가 선택한다 |
| `status.containsSyntheticMedia` | 2024-10-30 추가된 필드다. 애니메이션 MV는 대개 공개 의무 대상이 아니므로 기본값은 false로 두고 툴팁으로 설명한다([Google Blog](https://blog.google/intl/en-in/products/platforms/how-were-helping-creators-disclose-altered-or-synthetic-content/)) |

YouTube 통계를 커뮤니티에 표시하려면 30일 안에 갱신하거나 삭제하는 동기화 작업을 돌려야 한다([Developer Policies](https://developers.google.com/youtube/terms/developer-policies)).

**Shorts와 Content ID.** 정사각형·세로형이면서 3분 이하인 영상은 자동으로 Shorts가 된다. 1분을 넘는 Shorts에 Content ID claim이 걸리면 전 세계에서 차단된다([YouTube Help](https://support.google.com/youtube/answer/15424877?hl=en)). 그래서 "커버·리믹스" 플래그가 켜진 작품은 세로 출력을 60초 이하로 제한한다. 배급사를 통해 자기 곡을 Content ID에 등록한 사용자에게는 본인 채널 허용 목록을 확인하라고 안내한다. 다만 이 안내를 뒷받침하는 검증된 출처는 없다(추정).

**인코딩.** YouTube 권장 사양은 moov atom을 앞쪽에 둔(faststart) Edit List 없는 MP4, H.264 High·progressive·연속 B프레임 2개·closed GOP·CABAC·VBR·4:2:0, 1080p 기준 24–30fps 8 Mbps·48–60fps 12 Mbps, 오디오 AAC-LC다([YouTube Help](https://support.google.com/youtube/answer/1722171?hl=en)). WebCodecs 인코더 설정으로 B프레임 수와 closed GOP를 직접 지정할 수 있는지는 **미확인**이다. 그래서 공개 게시용 최종본을 media-worker의 FFmpeg로 다시 인코딩하는 경로를 둔다(제안).

### 8.3 플랫폼별 접근

| 플랫폼 | 방식과 Phase | 승인·제약 | 구현 포인트(제안) | 폴백 |
|---|---|---|---|---|
| YouTube(가로) | API 직접 업로드. Phase 1에는 비공개, 감사 통과 후 공개 | 8.2 | 백엔드 resumable, RMF 필드, 점멸 경고 문구 자동 삽입 | 다운로드 + Studio 안내 |
| YouTube Shorts | 세로 프리셋(Phase 2) | 3분 이하·세로면 자동 분류. 커버는 60초 이하 | 9:16 리프레이밍, 하이라이트 추출 | 같음 |
| TikTok | Phase 2. 초안(Upload) 경로를 먼저 열고, 감사 후 Direct Post | 감사 전 Direct Post는 `SELF_ONLY`, 24시간 5명, 비공개 계정만 가능([Direct Post](https://developers.tiktok.com/docs/en/content-posting-api-reference-direct-post)). Upload 경로에도 감사가 필요한지는 미확인 | `creator_info` 조회로 공개 범위 옵션과 `max_video_post_duration_sec`를 표시([Creator Info](https://developers.tiktok.com/doc/content-posting-api-reference-query-creator-info)). 상호작용 토글은 기본 꺼짐, 상업 콘텐츠 공개 토글, "Music Usage Confirmation" 문구, `is_aigc` 토글. 청크는 5–64MB, 최대 4GB([Media Transfer](https://developers.tiktok.com/docs/en/content-posting-api-media-transfer-guide)). init 분당 6회, 크리에이터당 하루 약 15건(2차, [PostZen](https://www.postzen.dev/blog/tiktok-api)) | 다운로드 |
| Instagram Reels | Phase 2. App Review 후 | 프로페셔널 계정만, 24시간 100건([Meta](https://developers.facebook.com/docs/instagram-platform/content-publishing/)). 권한은 Advanced Access(2차) | 컨테이너 생성 → 게시 2단계. 공개 URL이 필요하므로 단기 서명 URL로 전달하고 게시 후 만료시킨다. 개인 계정 사용자에게 계정 전환을 안내한다 | 다운로드 |
| X | Phase 3. 비용 게이팅 | 게시 $0.015, URL 포함 $0.20([X](https://devcommunity.x.com/t/x-api-pricing-update-owned-reads-now-0-001-other-changes-effective-april-20-2026/263025)). 미디어 업로드 과금은 불명확하다([포럼](https://devcommunity.x.com/t/undocumented-postcreate-usage-for-media-uploads-please-clarify-pay-per-use-billing/274646)) | 본문에 URL을 기본으로 넣지 않는다. 140초 이하 클립. 사용자별 일일 한도와 유료 플랜 게이팅. 월 1만 건 기준 비용은 URL 없이 약 $150–300, URL 포함 시 약 $2,000 이상(추정, 노트 05) | 웹 인텐트(텍스트·링크만, API 비용 없음) |
| 니코니코 | Phase 1. 투고 어시스트 | 공식 업로드 API 없음([ニコニコインフォ](https://blog.nicovideo.jp/niconews/182541.html)). 1개당 6GB | ① MP4(6GB 이하)·썸네일 다운로드 ② 제목·설명·태그 복사 ③ 투고 페이지 열기 ④ sm 번호를 입력받아 작품 페이지에 연결. 기본 태그는 `VOCALOID`, `初音ミク`, `重音テトSV`(SV판)·`重音テト`(UTAU판), `VOCALOIDオリジナル曲`. ボカコレ 기간에는 부문 태그를 1개만 고르게 하고 "태그 잠금" 체크리스트를 띄운다([ボカコレ FAQ](https://vocaloid-collection.jp/faq/)). 다음 회차는 2027-02-19~23([X](https://x.com/the_voca_colle/status/2090559508703768657)) | — |
| Bilibili | Phase 3. 투고 어시스트(중국어 제목·简介·태그 템플릿) | 개방 플랫폼 arcopen은 권한 신청이 필요하다. 자격 요건은 미확인([demo](https://github.com/bilibili-openplatform/demo)) | 수요가 확인되면 개방 플랫폼을 신청한다 | — |

**비공식 API는 니코니코와 Bilibili 모두 영구 제외한다.** 니코니코 업로드 API는 이미 한 번 바뀌어 예전 방식이 막혔고([Zenn](https://zenn.dev/negima1072/articles/howto-upload-nicovideo-by-api)), Bilibili 비공식 API 문서 프로젝트는 법적 경고를 받고 중단됐다(2.5).

### 8.4 내보내기 프리셋

| 프리셋 | 해상도·비율 | 비디오 | 오디오 | 길이·용량 가드 | 용도 |
|---|---|---|---|---|---|
| 가로 마스터 | 1920×1080(옵션 3840×2160) | H.264 High, 원본 fps, 1080p30 8Mbps 이상, 60fps 12Mbps 이상, 4K30 35–45Mbps | AAC-LC 48kHz 스테레오(비트레이트 추정 320–384kbps) | 6GB 이하(니코니코 상한) | YouTube 본편, 니코니코 |
| 세로 숏폼 | 1080×1920(9:16) | H.264 High, 30fps, 목표 8–12Mbps, 25Mbps 이하 | AAC-LC 128kbps 이상 | 3분 이하(커버는 1분 이하), 300MB 이하, TikTok 크리에이터별 최대 길이 | Shorts, TikTok, Reels |
| X 보수형 | 1280×720 / 720×1280 / 720×720 | H.264 High, 30·60fps | AAC-LC(HE-AAC 불가) | 140초 이하, 512MB 이하([X Media Best Practices](https://developer.x.com/en/docs/x-api/v1/media/upload-media/uploading-media/media-best-practices)) | X |

라우드니스 목표 −14 LUFS 안팎과 True Peak −1 dBTP는 업계 통설일 뿐 공식 근거가 **미확인**이다. 그래서 강제하지 않고 측정값을 표시만 한다(제안).

### 8.5 메타데이터 템플릿

제목 형식에는 확인된 관례가 두 가지 있다. YouTube식 「ボカロP - 曲名 feat. 歌声」(예: 「ピノキオピー - 超主人公 feat. 初音ミク」, [bilibili 전재](https://www.bilibili.com/video/av642081587))와 니코니코식 「【歌声】曲名【オリジナル曲】」(예: GYARI의 【重音テトSV】 표기 곡, [Last.fm](https://www.last.fm/music/GYARI/_/%E3%80%90%E9%87%8D%E9%9F%B3%E3%83%86%E3%83%88SV%E3%80%91%E3%83%89%E3%83%AA%E3%83%AB%E7%84%A1%E5%8F%8C%E3%80%82%EF%BD%9E%E7%95%B0%E4%B8%96%E7%95%8C%E8%BB%A2%E7%94%9F%E3%81%97%E3%81%A6%E7%A5%9E%E3%81%8B%E3%82%89%E3%83%89%E3%83%AA%E3%83%AB%E3%82%92%E6%8E%88%E3%81%8B%E3%81%A3%E3%81%9F%E7%A7%81%E3%81%AF%E3%80%81%E7%8E%8B%E5%A5%B3%E3%82%92%E5%8A%A9%E3%81%91%E3%81%9F%E3%82%8A%E3%83%8B%E3%82%BB%E5%8B%87%E8%80%85%E3%82%92%E8%BF%BD%E6%94%BE%E3%81%97%E3%81%9F%E3%82%8A%E9%AD%94%E7%8E%8B%E3%82%92%E8%A8%8E%E4%BC%90%E3%81%97%E3%81%A6%E6%9C%80%E5%BC%B7%E3%81%AB%E3%81%AA%E3%81%A3%E3%81%9F%E3%81%AE%E3%81%A7%E3%82%B9%E3%83%AD%E3%83%BC%E3%83%A9%E3%82%A4%E3%83%95%E3%82%92%E9%80%81%E3%82%8A%E3%81%BE%E3%81%99%EF%BD%9E%E2%80%BB%E3%81%93%E3%82%8C%E3%81%AF%E9%9F%B3%E6%A5%BD%E5%8B%95%E7%94%BB%E3%81%A7%E3%81%99%E3%80%90%E3%82%AA%E3%83%AA%E3%82%B8%E3%83%8A%E3%83%AB%E6%9B%B2%E3%80%91/+albums))다. 플랫폼은 이 형식을 프리셋으로 제공하고, 듀엣은 「初音ミク・重音テトSV」처럼 가수를 나란히 적게 한다. 설명란 크레딧 블록의 실제 예는 수집하지 못했으므로, Music & Lyrics·Illust·Movie·Vocal 크레딧, 캐릭터 권리 표기, 점멸 경고, 원작(부모 작품) 링크로 이뤄진 블록은 업계 관례에 기반한 **추정** 템플릿이다. X에서는 URL을 넣으면 건당 비용이 $0.20으로 오르므로 플랫폼 링크를 뺀 변형을 쓴다.

### 8.6 자체 감사 vs 통합 API 사업자

| 전략 | 장점 | 단점 |
|---|---|---|
| (A) 자체 감사 | API 비용이 거의 없고 UX를 직접 통제한다 | 승인에 수주에서 수개월이 걸린다 |
| (B) 통합 API 사업자 | 감사를 마친 앱을 써서 즉시 공개 게시할 수 있다(예: [Late](https://getlate.dev/tiktok), [bundle.social](https://bundle.social/tiktok-content-posting-api)) | 건당·월 과금이 붙고, 사용자 토큰을 제3자가 보관한다 |

이 계획서는 핵심 무대인 YouTube에 대해 Phase 0부터 자체 감사를 진행하라고 권고한다(제안). TikTok·Instagram의 감사 기간 동안 (B)를 임시로 쓸지는 사업 판단에 맡기며(10절), (B)에서 (A)로 옮기면 사용자가 계정을 다시 연동해야 한다는 비용을 함께 고려한다. CapCut·Canva·Buffer가 같은 문제를 어떻게 풀었는지는 **미확인**이다.

## 9. 리스크와 대응: 가장 큰 위험은 기술보다 라이선스와 승인 일정이다

가능성과 영향은 근거를 바탕으로 매긴 **추정** 등급(상·중·하)이다. 대응은 모두 제안이다.

| 분류 | 리스크 | 근거 | 가능성 / 영향 | 대응 |
|---|---|---|---|---|
| 라이선스 | 서비스명·광고에 「VOCALOID」「ボカロ」「初音ミク」를 쓰면 상표 문제가 생긴다 | [V4X EULA](https://ec.crypton.co.jp/download/pdf/eula_MIKUV4X.pdf) | 중 / 상 | 중립 명칭을 쓴다. 저장소 이름 "vocaloid-video-platform"은 내부 코드명으로만 쓴다. 크립톤과 협의한다 |
| 라이선스 | 운영사가 미쿠·테토를 마스코트·광고·기본 리그로 쓰면 PCL "비영리·무상"을 벗어난다 | [PCL](https://piapro.jp/license/pcl), [2024-12-04 성명](https://xexeq.jp/blogs/media/topics28818) | 중 / 상 | 오리지널 캐릭터를 기본으로 쓰고, 미쿠·테토는 사용자 업로드 자산으로만 다룬다. 공식 리그는 Phase 4 계약 이후로 미룬다 |
| 라이선스 | 비영리 조건 자산이나 Lite 에디션 출력이 수익화 경로에 섞인다 | [piapro 라이선스](https://blog.piapro.net/2011/03/post-437.html), Lite판 수익화 불가([テト Q&A](https://kasaneteto.jp/guidelines/faq.html), 검색 요약) | 중 / 중 | 자산별 권리 메타데이터를 둔다. 사용 에디션은 자기신고를 받는다(추정). 계보 기반 호환성 검사로 수익화 버튼을 막는다 |
| 라이선스 | 업로드된 보컬이 AI 학습에 쓰이면 학습 금지 조항을 위반한다 | [Dreamtonics Terms](https://dreamtonics.com/terms/), [Vocoflex EULA](https://dreamtonics.com/vocoflex-eula/) | 중 / 상 | 약관에 학습·스크래핑 금지를 넣고, 운영사도 학습에 쓰지 않는다. 대량 다운로드를 제한한다 |
| 라이선스 | copyleft·비상업 구성요소가 섞여 들어온다 | 2.4 플래그 표([@ffmpeg/core](https://www.npmjs.com/package/@ffmpeg/core), [@theatre/studio](https://www.npmjs.com/package/@theatre/studio), [madmom](https://pypi.org/project/madmom/), [vocoders](https://openvpi.github.io/vocoders/)) | 상 / 상 | CI 라이선스 허용 목록을 둔다(클라이언트 GPL·AGPL 0). 모델 가중치 라이선스도 같은 게이트로 검사한다 |
| 라이선스 | 상용 SDK 비용과 조건이 바뀐다(Remotion 5.0 예고, Spine·Live2D 구조) | [Remotion](https://www.npmjs.com/package/remotion), [Live2D](https://www.live2d.com/en/sdk/license/expandable/) | 중 / 중 | 자체 엔진을 쓰고, 라이선스가 걸린 런타임은 빌드에서 뺄 수 있는 플러그인으로 격리한다 |
| 라이선스 | 커버곡을 호스팅해 저작권 침해 신고를 받는다 | [JASRAC UGC 목록](https://www.jasrac.or.jp/information/topics/20/ugc.html) | 상(커버 허용 시) / 상 | 포괄계약 전에는 오리지널곡만 받는다. 신고 처리 절차(103조)를 둔다 |
| 라이선스 | 사용자 모델(VRM·PMX)이 배포 규약을 위반한다 | VRM 메타 필드([three-vrm](https://www.npmjs.com/package/@pixiv/three-vrm)) | 중 / 중 | 메타 파싱 게이트를 두고 업로드 시 규약 확인을 받는다 |
| 승인 | YouTube 감사가 늦어지거나 거절된다 | 기간 미확인, 감사 전 비공개 잠금([Videos: insert](https://developers.google.com/youtube/v3/docs/videos/insert)) | 중 / 상 | Phase 0에 신청한다. 다운로드·Studio 안내를 폴백으로 두고, 통합 사업자는 선택지로 둔다 |
| 승인 | 하루 100회 업로드 버킷이 병목이 된다 | [Quota Calculator](https://developers.google.com/youtube/v3/determine_quota_cost) | 중(성장 시) / 중 | 플랫폼 전체 카운터를 둔다. 미리 증설을 신청한다. 한도를 넘으면 다운로드 업로드를 안내한다 |
| 승인 | TikTok 감사나 Meta App Review가 늦어진다 | [TikTok](https://developers.tiktok.com/doc/content-posting-api-get-started), 2차 출처 | 중 / 중 | 초안 경로와 다운로드를 유지한다 |
| 승인 | 플랫폼 정책이 갑자기 바뀐다(쿼터 개편 2회, X 가격 개정) | [Revision History](https://developers.google.com/youtube/v3/revision_history), [X](https://devcommunity.x.com/t/x-api-pricing-update-owned-reads-now-0-001-other-changes-effective-april-20-2026/263025) | 상 / 중 | 커넥터를 격리하고 기능 플래그로 켜고 끈다. 비용 알림을 둔다 |
| 비용 | 서버 렌더 GPU 비용(단가 미확인). GPU가 없으면 CPU 폴백이 매우 느리다 | 노트 04 §8 | 중 / 중 | 클라이언트 렌더를 기본으로 한다. 서버 렌더는 폴백·4K·유료 기능으로 한정한다 |
| 비용 | 저장·이그레스·CDN 비용(단가 미확인) | 노트 04 §8 Gaps | 중 / 중 | Phase 1 종료 전에 매니지드 서비스와 자체 파이프라인의 손익분기를 계산한다(11절) |
| 비용 | X 건당 과금이 사용자 수에 비례해 늘어난다 | [X](https://devcommunity.x.com/t/announcing-the-launch-of-x-api-pay-per-use-pricing/256476) | 상(X 개방 시) / 중 | 유료 게이팅, URL 없는 본문, 일일 한도 |
| 기술 | 브라우저별 코덱 지원이 다르다(AAC 인코더 부재 등) | [BCD](https://www.npmjs.com/package/@mdn/browser-compat-data), [@mediabunny/aac-encoder](https://www.npmjs.com/package/@mediabunny/aac-encoder) | 상 / 중 | `isConfigSupported` 탐지, WASM AAC 인코더(MPL-2.0. 내장 libavcodec 라이선스는 미확인), 서버 폴백 |
| 기술 | WebGPU 공백(Firefox Linux·Android·Intel Mac) | [BCD](https://www.npmjs.com/package/@mdn/browser-compat-data) | 상 / 하 | WebGL2 폴백을 필수로 둔다 |
| 기술 | 미리보기와 결과물이 다르다(GPU·백엔드 차이) | 노트 04 §2 | 중 / 상 | 같은 기기·같은 백엔드로 내보낸다. 골든 프레임 회귀 테스트, 결정성 규칙 |
| 기술 | 4K·장시간 렌더에서 메모리가 고갈된다 | 4K RGBA 약 33.2MB/프레임(계산) | 중 / 중 | 1080p를 기본으로 한다. 스트리밍 인코딩과 백프레셔. 4K는 서버로 보낸다 |
| 기술 | 합성 보컬에서 정렬·립싱크 정확도를 알 수 없다 | 실측 자료 없음(노트 04 §7) | 상 / 중 | 프로젝트 파일 경로를 우선한다. 보정 UI를 두고 Phase 0에 측정한다 |
| 기술 | SV2 .svp 스키마가 공식 문서 없이 바뀐다 | 노트 01 §7 Gaps | 중 / 중 | 파서를 격리하고 샘플 회귀 테스트를 둔다. 실패하면 가사 텍스트 경로로 폴백한다 |
| 기술 | 2D 리그 자체 구현 공수가 크다(런타임 3–6인월, 에디터 6–12인월, 추정) | 노트 04 §4 | 상 / 중 | MVP는 FK와 스프라이트 교체까지만 한다. 메시와 IK는 Phase 2로 미룬다 |
| 안전 | 점멸 검사가 오탐·미탐을 낸다. IRIS는 인증 도구가 아니다 | [IRIS README](https://github.com/electronicarts/IRIS/blob/main/README.md) | 중 / 상 | 3중 장치, 이의 시 사람 검토, 면책 문구, 규칙 버전 기록 |
| 커뮤니티 | 조회수·좋아요를 조작한다 | [PeerTube 경고](https://github.com/Chocobozzz/PeerTube/blob/develop/config/default.yaml), [ニコニコ 2019](https://ch.nicovideo.jp/nicotalk/blomaga/ar1740969) | 상 / 중 | 유효 조회 규칙, 이상치 보류, 운영 승인 게이트, 계산 원칙만 공개 |
| 커뮤니티 | 생성형 AI 사용을 둘러싼 논란 | [X 2024-02](https://x.com/TomoyaKinoshita/status/1760123788212183356), [pixiv](https://www.itmedia.co.jp/aiplus/article/2602/18/1260218132/) | 상 / 중 | 자산별 신고와 필터를 둔다. 음성합성과 생성 AI를 라벨에서 구분한다 |
| 커뮤니티 | 이벤트 규칙 적용 논란 | [ボカコレ 2026 夏](https://www.itmedia.co.jp/news/article/2608/25/2000000733/) | 중 / 중 | 규칙을 사전 공지하고 당사자에게 통지하며 이의 제기 절차를 둔다 |
| 시장 | 미쿠×테토 붐이 꺾이거나 다른 보이스로 옮겨 간다 | 2026-09 주간 1위가 UTAU カロガド 곡([Yahoo!](https://news.yahoo.co.jp/articles/7e91c10cb2afbf68989d27bc1b22bd453925cd7e)) | 중 / 중 | 데이터 모델을 "가수 N·캐릭터 N"으로 일반화한다. 템플릿을 특정 캐릭터에 묶지 않는다 |
| 시장 | 한국에서 '테토' 검색어가 성격 유형 밈('테토녀')과 섞인다 | [나무위키](https://namu.wiki/w/%ED%85%8C%ED%86%A0-%EC%97%90%EA%B2%90%20%EC%84%B1%EA%B2%A9%20%EC%9C%A0%ED%98%95) | 중 / 하 | 공식 표기(重音テト, Kasane Teto) 기반 태그 정규화(추정) |

## 10. 사용자가 결정해야 할 미결 사항

권고는 이 계획서의 제안이다. 최종 결정은 사업 주체가 내린다.

| # | 결정 사항 | 선택지 | 권고(제안) | 결정 시점 |
|---|---|---|---|---|
| Q1 | 서비스 명칭과 브랜딩 | 「VOCALOID」「ボカロ」를 포함 / 중립 명칭 | 중립 명칭. 상표 조항 때문이다(2.6) | Phase 0 |
| Q2 | 주 시장과 운영 법인 소재지 | 일본 / 한국 / 글로벌 | 핵심 청취층은 일본(YouTube·니코니코)이고 UI는 일본어·한국어·영어다. 법인 소재지에 따라 부가통신 신고, 情プラ法, 外部送信規律, 포괄계약 상대 단체가 달라진다 | Phase 0 |
| Q3 | 수익 모델 | 무료 / 광고 / 구독 / 유료 기능(4K·서버 렌더·X 게시) | MVP는 무료로 연다. 광고는 PCL·테토 상업 해석을 크립톤과 확인한 뒤 붙인다 | Phase 1 종료 전 |
| Q4 | 크립톤 상업 라이선스 협의 범위 | 하지 않음 / 서비스명·마케팅만 / 공식 리그·템플릿 포함 | Phase 0에 문의를 보내고, 계약은 수요를 확인한 뒤 맺는다 | Phase 0(문의) |
| Q5 | 커버(歌ってみた) 허용 시점 | 출시부터 외부 임베드만 / 포괄계약 후 직접 호스팅 | 포괄계약 전에는 직접 호스팅을 금지한다 | Phase 3 종료 전 |
| Q6 | 점멸 검사 실패 작품의 처리 | 공개 차단 / 경고 카드 + 클릭 재생으로 허용 / 경고만 | flash·pattern failure는 기본 차단한다. 예외 신청 시 사람이 검토하고, 허용하면 경고 카드를 붙이고 추천에서 뺀다 | Phase 1 착수 전 |
| Q7 | 스트로브 기본 상한 | 2Hz 기본·3Hz 하드캡 / 3Hz 기본 | 2Hz 기본. IRIS extended failure를 피하기 위해서다 | Phase 1 착수 전 |
| Q8 | 크로스포스트 전략 | 자체 감사만 / 통합 사업자 임시 사용 / 혼합 | YouTube는 자체 감사, TikTok·Instagram은 사업 판단 | Phase 0 |
| Q9 | 플랫폼 내 가창 합성(옵션 C·D) | 하지 않음 / B2B 협상 / 상용 허용 보이스로 자체 스택 | 협상만 병행하고 구현은 Phase 4 조건부 | Phase 2 |
| Q10 | 3D 지원 범위 | VRM만 / VRM + MMD | VRM만(P1). MMD는 수요를 확인한 뒤 | Phase 2 종료 |
| Q11 | 모노레포 도구 | Nx / Turborepo | Nx. `turbo boundaries`가 Experimental이기 때문이다 | Phase 0 |
| Q12 | 지원 환경 하한, 모바일 편집 | 데스크톱 최신만 / 구형 Safari는 서버 렌더 / 모바일 편집 | 데스크톱 전용 편집. 미지원 환경은 시청만 허용 | Phase 0 |
| Q13 | 서버 렌더 인프라 | GPU 클라우드 / CPU(SwiftShader) / 외부 서비스 | 소규모 GPU 풀 + 큐. 단가는 견적 후 결정 | Phase 1 종료 |
| Q14 | 생성 AI 콘텐츠 정책 | 금지 / 신고 후 허용(필터 제공) / 무제한 | 신고 후 허용한다. 비표시 필터와 이벤트별 규칙을 둔다 | Phase 1 착수 전 |
| Q15 | 랭킹·조회수 산식 공개 범위 | 원칙만 / 가중치까지 | 원칙(쓰는 신호의 종류)만 공개한다. 니코니코 방식이다 | Phase 1 |
| Q16 | 기본 오리지널 캐릭터 | 자체 제작 / 공모 / 없음 | 듀엣 템플릿 체험용 오리지널 2인을 자체 제작한다. PCL 밖에서 마케팅할 수 있다 | Phase 1 |
| Q17 | DM·비공개 메시지 | 넣음 / 넣지 않음 | 넣지 않는다. 일본에서 届出 대상이 될 수 있다(노트 06 §8) | Phase 1 |

## 11. 미확인 항목과 검증 계획

| 영역 | 미확인 항목 | 영향 | 검증 방법(제안) | 시점 |
|---|---|---|---|---|
| 엔진 | Piapro Studio NT2의 ARA 지원, 트랙별 스템 내보내기, MusicXML | 업로드 산출물 안내 | 라이선스를 구매해 직접 검증 | Phase 0 |
| 엔진 | SV2 .svp 스키마 전체, .vpr `phonemePositions`의 기본 저장 여부 | 파서, 립싱크 정확도 | 샘플 프로젝트 수집과 회귀 픽스처 구축 | Phase 0 |
| 엔진 | NT·V6 EULA 원문, AHS·TWINDRILL의 AI 학습 조항, Dreamtonics 샘플 팩 조항 | 약관, 소재 패키지 기능 | 원문 열람과 법무 검토 | Phase 0 |
| 엔진 | V6 중국어 지원(출처 불일치), SV2 Basic/Pro 스크립트 차이, 테토 SV2 보컬 모드 수(7 vs 4) | 다국어, 옵션 B | 공식 페이지 확인 | Phase 1 |
| 엔진 | Yamaha Music Connect API의 가창 합성 포함 여부, NetVOCALOID 현황 | 옵션 D | 법인 문의 | Phase 2 |
| 트렌드 | 2026-10 현재 조회수, VocaDB 테토 사용곡 추이, TikTok 해시태그 사용 수, 커버 포맷 분포 | 템플릿 우선순위 | VocaDB API·YouTube Data API 직접 조회 | Phase 0 |
| MV 분석 | 팔레트, 가사 타이포 스타일, 편집 템포, 점멸 유무(**영상 미시청**) | 효과 카탈로그 우선순위 | 상위 20편을 시청하고 프레임 샘플링 통계 + IRIS로 점멸 빈도 측정 | Phase 0 |
| MV 분석 | 색 대비·음색 대비가 인기 요인이라는 추정 | 듀엣 템플릿 디자인 | 크리에이터 인터뷰, 시청자 설문 | Phase 1 |
| 웹 기술 | 브라우저×OS×코덱 인코딩 매트릭스, WebCodecs의 B프레임·closed GOP 제어, 탭 메모리 상한, 백그라운드 탭 스로틀링 | 내보내기 신뢰성 | 실기 테스트 매트릭스 | Phase 0 |
| 웹 기술 | Mediabunny WASM 인코더의 내장 libavcodec 라이선스, Shadertoy 기본 라이선스, OFL RFN, 源ノ明朝 라이선스, PSD 파서 라이브러리 | 라이선스 게이트 | 소스·원문 확인 | Phase 0 |
| 웹 기술 | 합성 보컬에서의 Whisper·WhisperX·MFA 정렬 정확도, 일본어 wav2vec2·MFA 모델과 beat-this 가중치 라이선스 | E3-3, E2-3 | 미쿠·테토 스템 벤치마크 | Phase 0–2 |
| 웹 기술 | 매니지드 비디오(Mux·Cloudflare Stream·Bunny 등)와 S3·R2 이그레스 단가, GPU headless Chromium 운영 | 비용 모델 | 벤더 견적과 PoC | Phase 1 |
| 웹 기술 | Spine의 "사용자 저작형 에디터"에 대한 입장, Live2D Expandable 수수료 | Phase 4 | 서면 문의 | Phase 3 |
| 배포 | YouTube 감사 기간, Video Uploads 증설 절차, `captions.insert` 스코프와 자막 포맷, 맞춤 썸네일 자격, 라우드니스·오디오 비트레이트 | 출시 일정, 프리셋 | 신청하면서 확인, 공식 레퍼런스 | Phase 0 |
| 배포 | TikTok 공식 영상 사양·스코프 이름·감사 기간, Upload 경로의 감사 필요 여부, Instagram App Review 기간 | Phase 2 일정 | 개발자 포털 확인 | Phase 1 |
| 배포 | X `tweet_video` 상한(140초 vs 20분), 미디어 업로드 과금 | Phase 3 비용 | 샌드박스 실측 | Phase 2 |
| 배포 | 니코니코 권장 인코딩·태그 개수 한도·최대 길이, ボカコレ 허용 엔진 범위, Bilibili 신청 자격 | 어시스트 정확도 | 공식 FAQ, 문의 | Phase 0 / Phase 2 |
| 권리 | JASRAC·NexTone·KOMCA 사용료율·절차·동기화(MV) 범위 | Phase 4 | 단체 문의 | Phase 3 |
| 권리 | 크립톤 상업 라이선스 절차·비용, 생성 AI 관련 가이드라인, 개인 수익화 개정 연도 | Q3·Q4 | 공식 문의 | Phase 0 |
| 권리 | 한국 영비법의 UGC 적용, 개인정보 국외 이전, 만 14세 미만 동의, 일본 APPI | 운영 법무 | 법률 검토 | Phase 1 |
| 권리 | ボカコレ2026冬 REMIX 1위 생성 AI 사례(저신뢰 출처) | AI 정책 | 공식 발표와 대조 | Phase 1 |
| 안전 | ITU-R BT.1702 원문, 일본 방송 점멸 가이드라인 원문, PEAT·Harding 현황 | 점멸 기준의 정합성 | 원문 열람·구매 | Phase 0 |
| 안전 | IRIS의 4분 MV 처리 시간, 화면 면적 처리 방식 | 처리 비용, 판정 해석 | 벤치마크 | Phase 0 |
| 지표 | YouTube 조회수 정의·검증 원문, 2025년 Shorts 집계 변경 | 지표 설계 참고 | Help 원문 | Phase 1 |

## 맺음말: 이미 있는 데이터를 움직임으로 바꾸는 플랫폼이 이긴다

이 플랫폼의 경쟁력은 목소리를 만드는 데서 나오지 않고, 크리에이터가 이미 갖고 있지만 버려지는 데이터를 움직임으로 바꾸는 데서 나온다. .svp·.vpr·.ppsf에는 노트 하나하나의 시작 시각과 가사가 이미 들어 있는데, 지금의 MV 공정은 그 정보를 AE와 AviUtl에서 손으로 다시 찍는다. 프로젝트 파일을 가사 타이밍과 입 모양으로 바로 바꾸는 일은 엔진 SDK 없이도 할 수 있고, 외주 14–50일을 하루 안으로 줄이겠다는 목표(제안)에 이르는 가장 짧은 길로 본다(추정). 같은 맥락에서 점멸 예산은 규제 대응 비용이 아니라 이 장르에서 특히 의미가 큰 차별점이다. BPM 170의 8분음표 스트로브, 적색 전면 점멸, 고대비 회전 줄무늬라는 장르의 대표 연출이 각각 WCAG 3회 규칙, 적색 플래시 정의, IRIS 패턴 판정과 정면으로 부딪치기 때문이다(추정).

배포 측면의 함의도 분명하다. YouTube 업로드에는 프로젝트당 하루 100건 상한과 감사 전 비공개 잠금이 있으므로, 플랫폼이 커질수록 성장 자체가 쿼터 문제가 된다. 따라서 플랫폼 안의 시청·반응·계보 경험은 외부 업로드로 가는 통로에 그치지 말고 그 자체로 머물 이유가 돼야 한다. Billboard JAPAN 니코니코 차트가 2차 창작 수를 지표로 센다는 사실이 이 방향, 즉 "파생작이 쌓이는 곳"을 만드는 쪽에 힘을 실어 준다.

## 12. 참고문헌

본문 인용 가운데 주요 출처를 영역별로 모았다. 각 노트의 원 출처 목록은 `docs/research_notes/보컬로이드 MV 제작 플랫폼 계획서/`에 있다.

**A. 엔진·보이스뱅크·포맷**

- Crypton, 初音ミク V6 발매(2026-04-14): https://www.crypton.co.jp/cfm/news/2026/04/14miku_v6
- gamebiz, 初音ミク V6 가격: https://gamebiz.jp/news/421198
- 島村楽器, 初音ミク V6 신제품 안내: https://info.shimamura.co.jp/digital/newitem/2026/02/164674
- SONICWIRE, 初音ミク NT (Ver.2) 정식 릴리스: https://sonicwire.com/news/blog/2025/03/nt-ver-2
- SONICWIRE, 初音ミク NT 특설 페이지: https://sonicwire.com/product/virtualsinger/special/mikunt
- Crypton, 初音ミク V4X EULA: https://ec.crypton.co.jp/download/pdf/eula_MIKUV4X.pdf
- Crypton FAQ 364: https://ec.crypton.co.jp/support/faq/364
- VOCALOID 업데이트 공지: https://www.vocaloid.com/en/news/support_64/
- VOCALOID 비즈니스: https://www.vocaloid.com/business/
- gamebiz, VOCALOID SDK for Unity(2015): https://gamebiz.jp/news/154379
- NetVOCALOID: https://vocaloid-fan.com/product/netvocaloid.html
- Yamaha, Yamaha Music Connect API: https://www.yamaha.com/ja/news_release/2024/24100701/
- Attack Magazine, Synthesizer V Studio 2 Pro 출시: https://www.attackmagazine.com/news/dreamtonics-announces-the-release-of-synthesizer-v-studio-2-pro/
- Sound On Sound, SV Studio 2 Pro 리뷰: https://www.soundonsound.com/node/4933228
- Dreamtonics, SV2 플러그인 문서: https://sv2.docs.dreamtonics.com/en/plugins
- Dreamtonics, Scripting Manual: https://resource.dreamtonics.com/scripting/
- GitHub Dreamtonics/svstudio-scripts: https://github.com/Dreamtonics/svstudio-scripts
- AHS, Synthesizer V EULA: https://www.ah-soft.com/synth-v/eula_e.html
- Dreamtonics, Statement on Business Activities: https://dreamtonics.com/statement-on-business-activities-related-to-synthesizer-v/
- Dreamtonics, Terms: https://dreamtonics.com/terms/
- Dreamtonics, Vocoflex EULA: https://dreamtonics.com/vocoflex-eula/
- AHS, Synthesizer V AI 重音テト 보도자료(2023): https://www.ah-soft.com/press/synth-v/20230403.html
- AHS, SV2 重音テト 보도자료: https://www.ah-soft.com/press/synth-v/20251030.html
- AHS, SV2 重音テト 제품: https://www.ah-soft.com/synth-v/teto2/
- 重音テト 가이드라인: https://kasaneteto.jp/guidelines/
- 重音テト 規約Q&A: https://kasaneteto.jp/guidelines/faq.html
- 重音テト キャラクター利用規約: https://kasaneteto.jp/guidelines/character.html
- OpenUtau SVP.cs: https://github.com/stakira/OpenUtau/blob/HEAD/OpenUtau.Core/Format/SVP.cs
- OpenUtau Formats.cs: https://github.com/stakira/OpenUtau/blob/HEAD/OpenUtau.Core/Format/Formats.cs
- OpenUtau LICENSE: https://github.com/stakira/OpenUtau/blob/HEAD/LICENSE.txt
- OpenUtau USTX 형식: https://github.com/stakira/OpenUtau/wiki/USTX-file-format
- UtaFormatix3 README: https://github.com/sdercolin/utaformatix3/blob/HEAD/README.md
- UtaFormatix3 Vpr.kt: https://github.com/sdercolin/utaformatix3/blob/HEAD/core/src/main/kotlin/core/io/Vpr.kt
- UtaFormatix3 Ppsf.kt: https://github.com/sdercolin/utaformatix3/blob/HEAD/core/src/main/kotlin/core/io/Ppsf.kt
- LibreSVIP 형식 일람: https://github.com/SoulMelody/LibreSVIP/blob/HEAD/docs/project_formats.md
- LibreSVIP ASS 플러그인: https://github.com/SoulMelody/LibreSVIP/tree/HEAD/libresvip/plugins/ass
- DiffSinger LICENSE: https://github.com/openvpi/DiffSinger/blob/HEAD/LICENSE
- DiffSinger README: https://github.com/openvpi/DiffSinger/blob/HEAD/README.md
- DiffSinger Community Vocoders: https://openvpi.github.io/vocoders/
- Wikipedia, Kasane Teto: https://en.wikipedia.org/wiki/Kasane_Teto

**B. 트렌드 데이터**

- ja.wikipedia 「メズマライザー」: https://ja.wikipedia.org/wiki/%E3%83%A1%E3%82%BA%E3%83%9E%E3%83%A9%E3%82%A4%E3%82%B6%E3%83%BC
- en.wikipedia "Mesmerizer (song)": https://en.wikipedia.org/wiki/Mesmerizer_%28song%29
- ja.wikipedia 「テトリス (柊マグネタイトの曲)」: https://ja.wikipedia.org/wiki/%E3%83%86%E3%83%88%E3%83%AA%E3%82%B9_%28%E6%9F%8A%E3%83%9E%E3%82%B0%E3%83%8D%E3%82%BF%E3%82%A4%E3%83%88%E3%81%AE%E6%9B%B2%29
- ja.wikipedia 「オーバーライド (曲)」: https://ja.wikipedia.org/wiki/%E3%82%AA%E3%83%BC%E3%83%90%E3%83%BC%E3%83%A9%E3%82%A4%E3%83%89_%28%E6%9B%B2%29
- ja.wikipedia 「人マニア」: https://ja.wikipedia.org/wiki/%E4%BA%BA%E3%83%9E%E3%83%8B%E3%82%A2
- ja.wikipedia 「ダイダイダイダイダイキライ」: https://ja.wikipedia.org/wiki/%E3%83%80%E3%82%A4%E3%83%80%E3%82%A4%E3%83%80%E3%82%A4%E3%83%80%E3%82%A4%E3%83%80%E3%82%A4%E3%82%AD%E3%83%A9%E3%82%A4
- Billboard JAPAN 2024 연간 니코니코 VOCALOID SONGS: https://www.billboard-japan.com/d_news/detail/144219
- Billboard JAPAN 2025 연간: https://www.billboard-japan.com/d_news/detail/156167
- Billboard JAPAN 2026 상반기: https://www.billboard-japan.com/d_news/detail/162077
- Billboard JAPAN 2025 연간 칼럼: https://www.billboard-japan.com/special/detail/5057
- 初音ミクWiki ボカコレ2026冬: https://w.atwiki.jp/hmiku/pages/71031.html
- KAI-YOU 「ブレインロット」: https://kai-you.net/article/95094
- Real Sound, 重音テトの日 특집(2024-10): https://realsound.jp/2024/10/post-1803284.html
- VocaDB 「T氏の話を信じるな」: https://vocadb.net/S/805591
- channel, MV 재이용 정책(2024-05-05): https://x.com/x_cast_x/status/1787051821565145369
- Know Your Meme, Mesmerizer: https://knowyourmeme.com/memes/mesmerizer-by-hatsune-miku-kasane-teto/photos
- ニコニコ動画 태그 「オーバーライド(吉田夜世)」: https://www.nicovideo.jp/tag/%E3%82%AA%E3%83%BC%E3%83%90%E3%83%BC%E3%83%A9%E3%82%A4%E3%83%89%28%E5%90%89%E7%94%B0%E5%A4%9C%E4%B8%96%29
- piapro blog, マジカルミライ2026 애프터리포트: https://blog.piapro.net/2026/09/b2609041.html
- Yahoo!ニュース(Billboard JAPAN), 「デビットビット」: https://news.yahoo.co.jp/articles/7e91c10cb2afbf68989d27bc1b22bd453925cd7e
- インフィニットループ, Desktop Mate 重音テト DLC: https://www.infiniteloop.co.jp/pr-blog/2025/08/desktop-mate-dlc-kasane-teto-release/

**C. MV 비주얼·제작 관행**

- Billboard JAPAN, 吉田夜世 인터뷰: https://www.billboard-japan.com/special/detail/4683
- KOTORA JOURNAL, 「メズマライザー」 고찰: https://www.kotora.jp/c/113950-2/
- Real Sound, 原口沙輔 인터뷰(2024-02): https://realsound.jp/tech/2024/02/post-1576883.html
- ダ・ヴィンチWeb, 「テトリス」: https://ddnavi.com/article/d1454757/a/
- pixiv百科事典, 「ダイダイダイダイダイキライ」: https://dic.pixiv.net/a/%E3%83%80%E3%82%A4%E3%83%80%E3%82%A4%E3%83%80%E3%82%A4%E3%83%80%E3%82%A4%E3%83%80%E3%82%A4%E3%82%AD%E3%83%A9%E3%82%A4
- OTOIRO, 「モニタリング」: https://otoiro.co.jp/topics/104605/
- Patreon, 「PPPP」 크레딧: https://www.patreon.com/posts/mv-tak-pppp-feat-141557760
- Vocaloid Lyrics Wiki, PPPP: https://vocaloidlyrics.miraheze.org/wiki/PPPP/TAK
- VocaDB, flashing lights warning 태그: https://vocadb.net/T/2913/flashing-lights-warning
- Nehan@MV制作, 점멸 표기 호소: https://x.com/Na_yu_ta_ta/status/1759983307624955983
- nanika.design, 보카로 MV 효과·플러그인: https://nanika.design/blog/1651/
- FLAPPER, 블록 노이즈: https://seguimiii.com/aviutl-tech/anmblocknoise
- FLAPPER, 디스플레이스먼트 글리치: https://seguimiii.com/aviutl-tech/displacementmapdeglitch
- FLAPPER, 全自動リリックモーション: https://seguimiii.com/aviutl-tech/autolyricmotion
- いからげ, 보카로 180곡 폰트 분석: https://note.com/ika_rage/n/n31662be9d918
- グローバルゲート, AE 리릭 비디오: https://www.globalgate.co.jp/blog/maiking-lyric-video-using-aftereffects
- AKETAMA, AviUtl 노래방 자막: https://aketama.work/aviutl-karaoke
- kanoの缶詰知識, 가사 입력 도구: https://eternallways.com/aviutltool
- GIGAZINE, AviUtl ExEdit2 beta1: https://www.gigazine.net/news/20250708-aviutl-exedit2-beta1/
- 無印かげひと, MV 제작 기간: https://note.com/kagehito_muji/n/nea1037033d4f
- Lancers, ボカロ: https://www.lancers.jp/menu/tag/%E3%83%9C%E3%82%AB%E3%83%AD
- 歌ってみた動画の依頼: https://utattemitatukurikata.com/mvirai/
- koharuoto, 1장 그림 + 가사: https://koharuoto.net/blog-article332

**D. 웹 기술 스택(npm·PyPI·MDN BCD, 2026-10-07 조회 기준)**

- MDN `@mdn/browser-compat-data` 8.1.4: https://www.npmjs.com/package/@mdn/browser-compat-data
- WebKit, Safari 26.0 기능: https://webkit.org/blog/17333/webkit-features-in-safari-26-0/
- Mediabunny: https://www.npmjs.com/package/mediabunny
- mp4-muxer(deprecated): https://www.npmjs.com/package/mp4-muxer
- @mediabunny/aac-encoder: https://www.npmjs.com/package/@mediabunny/aac-encoder
- @ffmpeg/core: https://www.npmjs.com/package/@ffmpeg/core
- DEV, FFmpeg를 브라우저로: https://dev.to/baojian_yuan/moving-ffmpeg-to-the-browser-how-i-saved-100-on-server-costs-using-webassembly-4l9f
- pixi.js: https://www.npmjs.com/package/pixi.js
- PixiJS v8 출시 블로그: https://pixijs.com/blog/pixi-v8-launches
- three: https://www.npmjs.com/package/three
- @pixiv/three-vrm: https://www.npmjs.com/package/@pixiv/three-vrm
- babylon-mmd: https://www.npmjs.com/package/babylon-mmd
- gl-transitions: https://www.npmjs.com/package/gl-transitions
- lygia: https://www.npmjs.com/package/lygia
- remotion(LICENSE): https://www.npmjs.com/package/remotion
- Remotion License & Pricing: https://www.remotion.dev/docs/license/pricing
- @theatre/studio: https://www.npmjs.com/package/@theatre/studio
- etro: https://www.npmjs.com/package/etro
- @diffusionstudio/core: https://www.npmjs.com/package/@diffusionstudio/core
- @esotericsoftware/spine-core(LICENSE): https://www.npmjs.com/package/@esotericsoftware/spine-core
- Live2D, Expandable Applications: https://www.live2d.com/en/sdk/license/expandable/
- Rive, $9/mo plan: https://rive.app/blog/rive-s-new-9-mo-plan
- @fontsource/noto-sans-jp: https://www.npmjs.com/package/@fontsource/noto-sans-jp
- subset-font: https://www.npmjs.com/package/subset-font
- troika-three-text: https://www.npmjs.com/package/troika-three-text
- harfbuzzjs: https://www.npmjs.com/package/harfbuzzjs
- budoux: https://www.npmjs.com/package/budoux
- kuroshiro: https://www.npmjs.com/package/kuroshiro
- web-audio-beat-detector: https://www.npmjs.com/package/web-audio-beat-detector
- loudness-worklet: https://www.npmjs.com/package/loudness-worklet
- demucs(PyPI): https://pypi.org/project/demucs/
- demucs-web: https://www.npmjs.com/package/demucs-web
- whisperx(PyPI): https://pypi.org/project/whisperx/
- whisper-timestamped(PyPI): https://pypi.org/project/whisper-timestamped/
- ctc-forced-aligner(PyPI): https://pypi.org/project/ctc-forced-aligner/
- pyopenjtalk(PyPI): https://pypi.org/project/pyopenjtalk/
- rhubarb-lip-sync: https://www.npmjs.com/package/rhubarb-lip-sync
- beat-this(PyPI): https://pypi.org/project/beat-this/
- allin1(PyPI): https://pypi.org/project/allin1/
- playwright: https://www.npmjs.com/package/playwright
- @remotion/renderer(GL 옵션): https://www.npmjs.com/package/@remotion/renderer
- shaka-packager: https://www.npmjs.com/package/shaka-packager
- hls.js: https://www.npmjs.com/package/hls.js
- OpenTimelineIO(PyPI): https://pypi.org/project/OpenTimelineIO/
- untitled-pixi-live2d-engine(플러그인 등록 선례): https://www.npmjs.com/package/untitled-pixi-live2d-engine
- @nx/eslint-plugin: https://www.npmjs.com/package/@nx/eslint-plugin
- dependency-cruiser: https://www.npmjs.com/package/dependency-cruiser
- eslint-plugin-boundaries: https://www.npmjs.com/package/eslint-plugin-boundaries
- turbo: https://www.npmjs.com/package/turbo
- typescript: https://www.npmjs.com/package/typescript
- knip: https://www.npmjs.com/package/knip
- madge: https://www.npmjs.com/package/madge
- web.dev, WebAssembly threads: https://web.dev/articles/webassembly-threads?authuser=0
- MDN, Origin private file system: https://developer.mozilla.org/en-US/docs/Web/API/File_System_API/Origin_private_file_system

**E. 배포 API**

- YouTube Data API Revision History: https://developers.google.com/youtube/v3/revision_history
- YouTube Quota Calculator: https://developers.google.com/youtube/v3/determine_quota_cost
- YouTube Videos: insert: https://developers.google.com/youtube/v3/docs/videos/insert
- YouTube Videos resource: https://developers.google.com/youtube/v3/docs/videos
- YouTube Resumable Uploads: https://developers.google.com/youtube/v3/guides/using_resumable_upload_protocol
- YouTube API Services Developer Policies: https://developers.google.com/youtube/terms/developer-policies
- YouTube Required Minimum Functionality: https://developers.google.com/youtube/terms/required-minimum-functionality
- YouTube API Services Audit and Quota Extension Form: https://support.google.com/youtube/contact/yt_api_form?hl=fa
- Google, Restricted scope verification: https://developers.google.com/identity/protocols/oauth2/production-readiness/restricted-scope-verification
- Google Cloud Console Help, 검증 FAQ: https://support.google.com/cloud/answer/13463817?hl=en
- Google Cloud Console Help, Unverified apps: https://support.google.com/cloud/answer/7454865?hl=en
- YouTube Help, 3분 Shorts: https://support.google.com/youtube/answer/15424877?hl=en
- YouTube Help, 권장 업로드 인코딩: https://support.google.com/youtube/answer/1722171?hl=en
- Plagiarism Today, YouTube inauthentic content(2025-07-08): https://www.plagiarismtoday.com/2025/07/08/youtube-targets-inauthentic-content/
- Google Blog, A/S 콘텐츠 공개: https://blog.google/intl/en-in/products/platforms/how-were-helping-creators-disclose-altered-or-synthetic-content/
- Ayrshare, 미검증 앱 403과 감사: https://www.ayrshare.com/solutions/google-api-error-403-unverified-app-how-to-fix-the-audit-pipeline/
- TikTok Content Posting API: https://developers.tiktok.com/products/content-posting-api
- TikTok Direct Post API reference: https://developers.tiktok.com/docs/en/content-posting-api-reference-direct-post
- TikTok Query Creator Info: https://developers.tiktok.com/doc/content-posting-api-reference-query-creator-info
- TikTok Media Transfer Guide: https://developers.tiktok.com/docs/en/content-posting-api-media-transfer-guide
- TikTok Content Sharing Guidelines: https://developers.tiktok.com/docs/en/content-sharing-guidelines
- Meta, Instagram Content Publishing: https://developers.facebook.com/docs/instagram-platform/content-publishing/
- Meta, IG User Media: https://developers.facebook.com/docs/instagram-platform/instagram-graph-api/reference/ig-user/media/
- X Developers, Pay-Per-Use 출시: https://devcommunity.x.com/t/announcing-the-launch-of-x-api-pay-per-use-pricing/256476
- X Developers, 2026-04-20 가격 개정: https://devcommunity.x.com/t/x-api-pricing-update-owned-reads-now-0-001-other-changes-effective-april-20-2026/263025
- X, Media Best Practices: https://developer.x.com/en/docs/x-api/v1/media/upload-media/uploading-media/media-best-practices
- ニコニコインフォ, 일부 비공개 API 제공 종료: https://blog.nicovideo.jp/niconews/182541.html
- ニコニコインフォ, 업로드 용량 6GB: https://blog.nicovideo.jp/niconews/233213.html
- ボカコレ FAQ: https://vocaloid-collection.jp/faq/
- bilibili-openplatform/demo: https://github.com/bilibili-openplatform/demo

**F. 권리·커뮤니티·안전**

- ピアプロ・キャラクター・ライセンス(PCL): https://piapro.jp/license/pcl
- piapro blog, オリジナルライセンス: https://blog.piapro.net/2011/03/post-437.html
- XEXEQ, 크립톤 2024-12-04 성명: https://xexeq.jp/blogs/media/topics28818
- KAI-YOU, 미쿠 YouTube 수익화 가이드라인 개정: https://kai-you.net/article/80543
- 重音テトおふぃしゃる(TWINDRILL), 2023-06-29 게시물: https://x.com/twindrill_teto/status/1674403130287751168
- JASRAC, UGC 서비스 이용허락 목록: https://www.jasrac.or.jp/information/topics/20/ugc.html
- 국가법령정보센터, 저작권법 제103조: https://www.law.go.kr/LSW/lsLawLinkInfo.do?lsJoLnkSeq=900605727&lsId=000798&chrClsCd=010202&print=print
- 찾기쉬운 생활법령, 정보의 삭제요청 및 임시조치: https://easylaw.go.kr/CSP/CnpClsMain.laf?csmSeq=293&ccfNo=2&cciNo=1&cnpClsNo=1
- 総務省, 令和7年版 情報通信白書(情プラ法): https://www.soumu.go.jp/johotsusintokei/whitepaper/ja/r07/html/nd123210.html
- Chromaprint LICENSE: https://github.com/acoustid/chromaprint/blob/master/LICENSE.md
- Audible Magic, UGC 저작권 준수: https://www.audiblemagic.com/?p=6226
- ニコニコインフォ, クリエイター奨励プログラムガイド: https://blog.nicovideo.jp/niconews/172165.html
- ニコニコ窓口, 신 랭킹 상세(2019): https://ch.nicovideo.jp/nicotalk/blomaga/ar1740969
- ITmedia NEWS, ボカコレ 2026 夏 랭킹 제외 논란: https://www.itmedia.co.jp/news/article/2608/25/2000000733/
- PeerTube config/default.yaml: https://github.com/Chocobozzz/PeerTube/blob/develop/config/default.yaml
- PeerTube hot 알고리즘: https://github.com/Chocobozzz/PeerTube/blob/develop/server/core/models/video/sql/video/videos-id-list-query-builder.ts
- Mastodon trends/statuses.rb: https://github.com/mastodon/mastodon/blob/main/app/models/trends/statuses.rb
- bilibili 积分&硬币规则: https://www.bilibili.com/html/point.html
- 産総研 後藤ら, Kiite Cafe(SIGMUS 2021): https://staff.aist.go.jp/m.goto/PAPER/SIGMUS202109tsukuda.pdf
- VocaDB SongType.cs: https://github.com/VocaDB/vocadb/blob/main/VocaDbModel/Domain/Songs/SongType.cs
- W3C WCAG 2.3.1: https://github.com/w3c/wcag/blob/main/guidelines/sc/20/three-flashes-or-below-threshold.html
- W3C WCAG, general flash and red flash thresholds: https://github.com/w3c/wcag/blob/main/guidelines/terms/20/general-flash-and-red-flash-thresholds.html
- Electronic Arts IRIS README: https://github.com/electronicarts/IRIS/blob/main/README.md
- Electronic Arts IRIS LICENSE: https://github.com/electronicarts/IRIS/blob/main/LICENSE.txt
- ITmedia AI+, pixiv 가이드라인 개정(2026-03-18 시행): https://www.itmedia.co.jp/aiplus/article/2602/18/1260218132/
- X, AI 일러스트 사용 보카로P 비판(2024-02-21): https://x.com/TomoyaKinoshita/status/1760123788212183356
