# 커뮤니티·권리·안전 레이어 조사 노트 (보컬로이드/Synthesizer V MV 제작·공유 플랫폼)

작성 기준일: 2026-10-07. 법률 자문이 아니라 1차 출처를 요약한 것이다. 표기 규칙은 다음과 같다. **미확인**은 출처로 확인하지 못한 항목, **추정**은 인용한 사실에서 끌어낸 추론, **(2차)**는 비공식 해설·블로그·집계 사이트, **(검색 요약 기준)**은 검색 도구 요약만 보고 원문 문장과 대조하지 못한 항목이다.

조사 방법: WebSearch를 28회 실행한 뒤 이 턴의 공용 검색 한도(에이전트 전체 200회)가 소진되어 더 검색할 수 없었다. 그래서 공개 GitHub 저장소를 git으로 직접 읽어 1차 출처로 썼다. 대상은 W3C WCAG, EA IRIS, Chromaprint, PeerTube, Mastodon, Reddit(아카이브), Arc/HN(anarki), VocaDB이며, 모두 각 기본 브랜치 HEAD를 2026-10-07에 조회했다. 검색 한도 때문에 조사하지 못한 항목은 각 절의 Gaps에 적었다. YouTube 조회수 정의 원문, TikTok·nana·Smule, ITU-R BT.1702 원문, NHK·민방련 가이드라인, Ofcom, PEAT 라이선스, 니코니코 AI 정책 등이 여기에 해당한다.

---

## 1. 비교 플랫폼의 기능, 이 문화에서 중요한 이유, 우리 플랫폼에 주는 교훈

### Takeaway
보카로 문화를 떠받치는 플랫폼 기능은 다섯 가지다. ① 파생작 계보와 원작자에게 돌아가는 보상(니코니코 コンテンツツリー와 "子ども手当"), ② 원작자가 고르는 재사용 조건(피아프로 라이선스 조건), ③ 부문을 나눈 이벤트 랭킹(ボカコレ TOP100/ROOKIE/REMIX), ④ 비용이 드는 희소한 지지 신호(빌리빌리 코인), ⑤ "함께 듣기"(Kiite Cafe)다. 교훈은 다음과 같다. 계보·라이선스·동의 상태를 1급 데이터로 다룬다. 랭킹 입력 신호는 조작에 강한 것으로 제한한다(니코니코 2019). 규칙은 미리 공지하고 일관되게 적용한다(ボカコレ 2026 夏 논란).

### Cited Findings
**니코니코(ニコニコ)**
- クリエイター奨励プログラム: 등록 작품의 시청 수, 코멘트 수, 받은 기프트 pt, ニコニ広告 pt 등에 따라 "クリエイター奨励スコア"가 쌓이고, 이를 ニコニコポイント·Amazonギフト券·현금으로 교환한다 — [ニコニコインフォ「クリエイター奨励プログラムガイド」](https://blog.nicovideo.jp/niconews/172165.html)
- コンテンツツリー: 이용했거나 영향을 받은 니코니코 작품을 "親作品"으로 등록하도록 요구한다. 親作品 크리에이터는 "子ども手当"를 받으며, 공식 가이드는 이를 "リスペクトを表明しつつそのクリエイターを応援"하는 장치로 설명한다 — [同ガイド](https://blog.nicovideo.jp/niconews/172165.html). 子ども手当는 子作品의 "作品パワー"에 따라 분배되고 子作品 몫에서 떼어 가는 구조가 아니라는 해설도 있다 — [コンテンツツリー機能を活用しよう](https://tyc.rei-yumesaki.net/about/terms/niconico/tree/) (2차)
- 親作品 등록은 동영상에만 해당하지 않는다. ニコ生ゲーム을 親作品으로 일괄 등록하는 기능이 공지된 바 있다 — [ニコニコインフォ](https://blog.nicovideo.jp/niconews/218454.html)
- 2019년 6월 랭킹 개편으로 장르별·태그별 랭킹이 생겼다. 산출에는 "「再生数」「コメント数」「マイリスト数」だけを利用"하고 "アクセス時のさまざまな情報も加味して計算することで、ランキング工作をしづらくします"라고 공지했다 — [ニコニコ窓口「新ランキング（ジャンルランキング）の詳細」](https://ch.nicovideo.jp/nicotalk/blomaga/ar1740969), [ねとらぼ 2019-06-27](https://nlab.itmedia.co.jp/nl/articles/1906/27/news095.html). 사용자가 직접 정의하는 "カスタムランキング"도 함께 추가됐다 — [ドワンゴ](https://dwango.co.jp/news/4681241107293516508/)
- 이전 総合ポイント 식(커뮤니티 위키 기준)은 「総合ポイント＝再生数＋(コメント数×補正値)＋マイリスト数×15＋ニコニ広告宣伝ポイント×0.3」이었다 — [ニコニコ大百科「総合ポイント」](https://dic.nicovideo.jp/a/%E7%B7%8F%E5%90%88%E3%83%9D%E3%82%A4%E3%83%B3%E3%83%88) (2차). 과거에는 유료 홍보 포인트가 랭킹에 일부 들어갔으나, 2019년 공지의 "3요소만 사용" 원칙에서는 빠진 셈이다.
- 週刊ニコニコランキング 식(커뮤니티 위키 기준)은 「再生数×補正値A＋コメント数×補正値B＋マイリスト登録数×補正値C×40＋いいね数×10」이다 — [ニコニコ大百科](https://dic.nicovideo.jp/a/%E9%80%B1%E5%88%8A%E3%83%8B%E3%82%B3%E3%83%8B%E3%82%B3%E3%83%A9%E3%83%B3%E3%82%AD%E3%83%B3%E3%82%B0) (2차)

**The VOCALOID Collection(ボカコレ, 드왕고)**
- 랭킹은 세 부문이다. "TOP100"(VOCALOID 등 音声合成ソフトウェア를 쓴 오리지널곡), "ROOKIE"(ボカロP 데뷔 2년 이내 한정), "REMIX"(ボカロ曲のREMIX). 집계는 니코니코 동영상 랭킹 구조(재생·좋아요·코멘트 등)를 쓴다 — [ボカコレ FAQ(ランキング)](https://vocaloid-collection.jp/faq/?category=ranking), [ルーキー](https://vocaloid-collection.jp/ranking/rookie/) (가중치 세부는 검색 요약 기준, 미확인)
- 2026 Summer(8/20–24)에는 『プロセカ』 콜라보와 "ニコニコ20周年記念として名曲12曲のStemデータを配布"(REMIX 기획)가 진행됐다 — [ドワンゴ](https://dwango.co.jp/news/5071407746646016/), [PR TIMES](https://prtimes.jp/main/html/rd/p/000000944.000096446.html), [Real Sound 2026-06](https://realsound.jp/tech/2026/06/post-2432719.html)
- 2026 Summer 랭킹 제외 논란: 奏의 「ツァイトガイスト」가 MV에서 닌텐도 3DS를 "意図的・主体的なメイン演出"으로 썼다는 이유로 '규약 위반' 처리되어 랭킹에서 빠졌고, "基準あいまい"라는 반발이 일었다. 운영 측은 8/24 X 성명에서 규약 적용 기준을 엄격하게 바꿨다고 설명하면서, 사전 주지와 당사자 통지·설명이 부족했던 점을 사과했다 — [ITmedia NEWS 2026-08-25](https://www.itmedia.co.jp/news/article/2608/25/2000000733/), [KAI-YOU](https://kai-you.net/article/96340), [KAI-YOU via Yahoo!ニュース](https://news.yahoo.co.jp/articles/31b8d98b7d4797a4c8d664ea9aa6e741234cdfd2)

**피아프로(piapro, 크립톤)**
- 투고 작품마다 "ライセンス条件"을 붙이고, 이 조건을 지키면 투고자 외의 피아프로 회원도 작품을 자유롭게 쓸 수 있는 구조다(세부는 2절) — [piapro blog 2011-03](https://blog.piapro.net/2011/03/post-437.html), [piapro intro](https://piapro.jp/intro/)

**빌리빌리(哔哩哔哩)**
- "三连"은 点赞(좋아요)+投币(코인)+收藏(즐겨찾기)이며, 좋아요 버튼을 길게 누르면 세 동작이 한 번에 처리되는 "一键三连"이 된다 — [135editor](https://www.135editor.com/essences/8098.html) (2차)
- 코인은 Lv1 이상이면서 휴대폰 인증을 한 사용자가 로그인할 때 하루 1개씩 받는다(일반 사용자는 연 365개 수준). 영상 하나에 1~2개까지만 던질 수 있고 자기 영상에는 불가하다. 1코인을 받으면 UP主에게 경험치 1과 0.1코인이 돌아가며, 정산은 매일 05시다 — [bilibili 积分&硬币规则](https://www.bilibili.com/html/point.html), [135editor](https://www.135editor.com/essences/8098.html) (검색 요약 기준)

**Kiite / VocaDB(발견)**
- Kiite Cafe(2020-05 공개)에서는 온라인 참가자들이 같은 곡을 동시에 들으며 실시간으로 교류한다. 사용자의 "いいね" 반응이 시각화되고, 참가자 취향에 맞는 곡이 선곡된다. 대상은 니코니코에 올라온 약 40만 곡의 VOCALOID 곡이다 — [産総研 後藤ら, SIGMUS 2021-09](https://staff.aist.go.jp/m.goto/PAPER/SIGMUS202109tsukuda.pdf). VOCALOID News는 Kiite를 "Crypton's newest music service"로 소개했다 — [VOCALOID News](https://www.vocaloidnews.net/lets-discover-kiite-cryptons-newest-music-service/) (운영 주체 관계는 미확인)
- VocaDB는 이벤트 단위로도 DB를 만든다(예: ボカコレ 2026 Winter 이벤트 항목) — [VocaDB](https://vocadb.net/E/10117/the-vocaloid-collection-2026-w). 곡 유형 열거형은 Original, Remaster, Remix, Cover, Arrangement, Instrumental, Mashup, MusicPV, DramaPV, Live, Illustration, Other 등이며 코드 라이선스는 MIT다 — [VocaDB SongType.cs](https://github.com/VocaDB/vocadb/blob/main/VocaDbModel/Domain/Songs/SongType.cs), [License.txt](https://github.com/VocaDB/vocadb/blob/main/License.txt)

**BandLab (2차 출처만 확보)**
- 프로젝트는 private / public / forkable 세 단계로 구분된다("A public project is not the same as a forkable project"). fork는 독립 사본을 만들어 자기 버전으로 다시 게시하되 원작 attribution을 유지하는 기능으로 설명된다 — [Jack Righteous BandLab guide](https://jackrighteous.com/blogs/bandlab-guides/bandlab-sounds-loops-forking-ai-music-release-checklist), [Audeobox](https://www.audeobox.com/learn/bandlab/bandlab-collaboration-features/) (2차; BandLab 공식 헬프 미확인)

**YouTube Shorts Remix**
- 2021년 Shorts에 기존 YouTube 영상의 오디오를 재사용하는 기능이 도입됐다 — [Tubefilter 2021-06-07](https://tubefilter.com/2021/06/07/youtube-shorts-repurpose-audio-global-rollout/)
- 일반 영상은 기본적으로 Remix가 허용되어 있고, Studio에서 영상별로 "Shorts remixing" 허용 여부를 정한다 — [RouteNote](https://routenote.com/blog/?p=91318). 여러 영상을 한꺼번에 "Don't allow sampling"으로 바꾸는 기능도 있다 — [Social Media Today](https://www.socialmediatoday.com/news/youtube-adds-new-control-options-for-shorts-remixes-tests-shorts-analytics/). Shorts 자체는 remix opt-out이 안 되며, AI remix 시험과 관련해 opt-out하면 기존 remix 자격도 함께 빠진다는 보도가 있다 — [PPC Land](https://ppc.land/youtube-tests-ai-that-turns-someone-elses-short-into-a-brand-new-video/) (2차, 시점별 정책 차이 미확인)

### Inferences
- 계보, 보상 분배, 리스펙트 표명을 하나로 묶은 コンテンツツリー는 보카로 2차 창작 생태계의 핵심 인프라다. 우리 플랫폼에서도 "부모 작품 등록"을 1급 데이터로 둬야 한다. 보상 분배는 수익 모델이 생긴 뒤 단계적으로 도입한다. (추정)
- 랭킹은 ① 부문을 나누고(오리지널/리믹스/신인, ボカコレ), ② 입력 신호를 제한하며(니코니코 2019), ③ 유료 홍보를 유기적 랭킹과 분리한다(니코니코가 2019년에 산출 요소를 3개로 줄인 것과 일치). (추정)
- 이벤트 규칙(제3자 상품·로고 사용, AI 사용 등)은 미리 문서화해 공지하고, 제외할 때는 당사자에게 통지하고 이의 제기 절차를 둔다. ボカコレ 2026 夏 논란이 반면교사다. (추정)
- 희소 자원형 지지(빌리빌리 코인)는 강한 신호지만 경제를 설계하는 부담이 있다. MVP는 좋아요 + 저장(マイリスト형 큐레이션) + 공유로 시작하고, 이후 "응원 포인트"를 검토한다. (추정)
- "함께 보기/듣기"(Kiite Cafe), 시간 동기 코멘트(弾幕)는 이 문화에서 공동 시청 경험의 핵심이다. 오버레이 ON/OFF, 밀도 제한, NG 필터를 함께 제공해야 한다. (추정. 弾幕 관련 1차 자료는 확보하지 못함)
- 공개 여부와 fork/리믹스 허용 여부는 분리한다(BandLab). 기본값은 YouTube처럼 "기본 허용"이 아니라 원작자가 명시적으로 허용하는 쪽이 보카로 관행(使用許可 문화)에 맞다. (추정)

### Gaps
- 니코니코 코멘트(弾幕) 사양과 밀도 제어·NG 기능, ニコニコ広告 현행 사양, 2024년 사이버공격 복구 이후 랭킹 사양: 검색 한도로 조사하지 못했다.
- 빌리빌리 탄막 품질 관리(정회원 "答题" 시험 등): 조사하지 못했다(배경지식상 존재하나 미확인).
- TikTok Duet/Stitch 권한 설정, nana·Smule의 콜라보(합창·듀엣) 구조와 음악 라이선스: 조사하지 못했다.
- BandLab 공식 문서(Fork 사양, 배급 서비스, 이용자 수): 1차 출처를 확보하지 못했다. 2차 블로그의 "fork된 트랙 참여도 25%↑" 주장은 근거가 불명확해 쓰지 않았다.
- piapro 블로그 「niconicoでのクリエイター奨励プログラムのご利用につきまして」(2015-09)는 존재만 확인했고 내용은 확인하지 못했다 — [piapro blog](https://blog.piapro.net/2015/09/h1509161-1.html)

---

## 2. 협업·리믹스: 2차 창작의 크레딧·라이선스 관행과 "fork/자산 재사용" 기능 요건

### Takeaway
보카로 씬의 2차 창작은 세 가지 축으로 돌아간다. ① 원작자가 공개한 オフボーカル·스템·일러스트를, ② 원작자가 정한 조건(피아프로: "非営利" 필수, 성명 표시·개변 금지 선택, 커스텀 "オリジナルライセンス")과 캐릭터 라이선스(PCL)에 따라 쓰고, ③ 플랫폼 계보(コンテンツツリー)로 크레딧과 보상을 되돌린다. 플랫폼 포괄계약은 작사·작곡 권리만 처리하므로 원반(마스터)·オフボーカル 사용과 편곡(翻案)은 따로 허락을 받아야 한다. 따라서 fork 기능에는 자산별 권리 유형과 라이선스, 공개 여부와 fork 허용의 분리, 불변 계보, 크레딧 전파, 상업 이용 차단 로직이 필요하다.

### Cited Findings
- 피아프로 라이선스 조건: "非営利目的に限ります"는 반드시 붙고, 「氏名を必ず表示する」「改変を許さない」 두 가지를 고를 수 있다. 2011년 3월 신기능 "オリジナルライセンス"는 투고자 본인만 작품 페이지의 「作品情報の編集」에서 바꾸거나 새로 붙일 수 있다 — [piapro blog 2011-03「新機能『オリジナルライセンス』について」](https://blog.piapro.net/2011/03/post-437.html)
- PCL이 허락하는 이용은 비영리이면서 무상인 이용, 2차 창작물의 창작·이용, 제3자 지적재산권을 침해하지 않는 이용 등이다 — [ピアプロ・キャラクター・ライセンスの概要(2009)](https://blog.piapro.net/2009/05/post-223.html), [PCL summary](https://piapro.jp/license/pcl/summary), [PCL 全文](https://piapro.jp/license/pcl)
- 歌ってみた의 권리 구조: 플랫폼 포괄계약은 관리곡을 "歌うこと"를 허락하며, 플랫폼이 사용료를 내므로 개인은 따로 절차를 밟지 않는다. 반면 시판 CD·배信 음원·카라오케 음원을 쓰는 것은 특별히 허락받지 않았다면 原盤権 침해다. 쓸 수 있는 것은 직접 만든 반주, ボカロP 본인이 공개한 オフボーカル, "利用OK"라고 명기된 음원이다. 편곡·替え歌(翻案)는 포괄계약 대상이 아니어서 개별 허락이 필요하다 — [nayutas](https://nayutas.net/school/kitasenju/blog/86453/), [WACCA MUSIC SCHOOL](https://wacca-music.co.jp/course/voice-training/vocaloid/blog/11745/), [SETLINK](https://setlink.jp/blog/copyright-guide-for-utawaku) (모두 2차 해설). 원반권 일반론은 [法律事務所コラム「改めて原盤権 －配信での音源利用は進むのか－」(寺内康介)](https://www.kottolaw.com/column/211129.html) 참조
- 한국도 구조가 같다. 플랫폼 포괄계약은 신탁단체가 관리하는 권리(작사·작곡)에 관한 것이어서 원곡 음원이나 공식 MR까지 당연히 허락된다고 보기 어렵다 — [studionol](https://studionol.co.kr/ko/stories/copyright-cover1), [로톡](https://www.lawtalk.co.kr/posts/167886) (2차)
- 니코니코 コンテンツツリー는 이용하거나 영향을 받은 작품을 親作品으로 등록하는 계보 장치이고, 子ども手当로 보상을 되돌린다(1절) — [ニコニコインフォ](https://blog.nicovideo.jp/niconews/172165.html)
- 공식이 리믹스를 진흥한 사례: ボカコレ 2026 Summer에서 운영 측이 명곡 12곡의 Stem 데이터를 공식 배포했다 — [ドワンゴ](https://dwango.co.jp/news/5071407746646016/)
- 계보 유형 데이터 모델 참고: VocaDB의 SongType(Original/Remaster/Remix/Cover/Arrangement/Instrumental/Mashup/MusicPV/DramaPV/Live/Illustration/Other) — [VocaDB SongType.cs](https://github.com/VocaDB/vocadb/blob/main/VocaDbModel/Domain/Songs/SongType.cs)
- BandLab은 public과 forkable을 별도 설정으로 두고, fork본에는 원작 attribution이 붙는다 — [Jack Righteous](https://jackrighteous.com/blogs/bandlab-guides/bandlab-sounds-loops-forking-ai-music-release-checklist) (2차)
- 重音テト 공식(TWINDRILL, 2023-06-29): "公式で把握や管理できないものを勝手に重音テト、それに付随するものとして再配布される事は収集がつかなくなるので絶対にお止めください" — [X @twindrill_teto](https://x.com/twindrill_teto/status/1674403130287751168)
- 크립톤은 「初音ミク」 등 2차 창작물의 다운로드 판매를 "原則として許諾していない"고 설명했다(2012-02) — [ITmedia NEWS 2012-02-22](https://www.itmedia.co.jp/news/articles/1202/22/news116.html)

### Inferences
"fork / 이 자산 재사용" 기능의 요건 초안이다. 모두 추정.
1. **자산 단위 권리 메타데이터**: 자산 유형(작곡·작사 / 원반·オフボーカル·스템 / 보컬 렌더 / 일러스트 / 영상 / 캐릭터 리그·모션) × 권리자 × 라이선스(피아프로식 비영리, 성명 표시, 개변 금지, 커스텀 조건 텍스트, 또는 CC 계열) × 외부 출처 URL(piapro 등). 업로드할 때 "이 자산을 쓸 권리가 있다"는 확인과 근거 링크를 받는다.
2. **허용 플래그 분리**: 공개(visibility), fork 허용, 영상 리믹스 허용, 음원 재사용 허용, 歌ってみた 허용을 각각 따로 둔다(BandLab·YouTube 사례). 보카로 관행에 맞게 기본값은 "허용 안 함"이다.
3. **계보 그래프**: 부모와 자식을 잇는 DAG로, 엣지에 유형(cover/remix/arrangement/instrumental 사용/MV/illustration 사용, VocaDB 참고)을 붙인다. 게시된 뒤에는 자식이 부모 표기를 지울 수 없게 한다. 부모가 삭제되어도 "삭제된 원작" 노드는 남긴다.
4. **크레딧 전파**: 자식 작품 페이지와 YouTube 내보내기 설명란에 상위 계보 크레딧(곡·가사·일러스트·캐릭터 권리 표기, 예: "© Crypton Future Media, INC. www.piapro.net", 테토 3자 표기)을 자동으로 넣는다.
5. **라이선스 호환성 검사**: 계보에 비영리 조건이나 PCL 캐릭터가 있으면 수익화·상업 배포 버튼을 비활성화하거나 경고한다. "개변 금지" 자산은 fork 대상에서 뺀다.
6. **使用許可 문구의 구조화**: 보카로P들이 관행적으로 내거는 "歌ってみた自由", "オフボーカル使用可(クレジット必須)" 같은 허락을 체크박스와 템플릿으로 표준화한다.
7. **캐릭터 레이어 분리**: 곡의 라이선스와 별개로 캐릭터(PCL·테토 규약) 조건이 계보 전체에 전파된다.

### Gaps
- 주요 보카로P들의 실제 使用許可 문구, オフボーカル 배포 조건(piapro·BOOTH·개인 사이트) 사례를 1차로 수집하지 못했다.
- 2026년 piapro 라이선스 UI 현황(オリジナルライセンス가 남아 있는지 등)을 확인하지 못했다.
- 니코니코 コンテンツツリー의 세부 규칙(등록 가능한 수, 부적절한 등록 처리, 子ども手当 비율)을 확인하지 못했다.

---

## 3. 지표 무결성: "조회"의 정의·검증, 좋아요 의미, 랭킹·트렌딩 공식, 조작 방지

### Takeaway
오픈소스 플랫폼 코드에는 바로 가져다 쓸 수 있는 패턴이 있다. 최소 시청 시간을 채워야 조회로 세고(PeerTube 10초), 같은 시청자는 일정 시간 안에 다시 세지 않으며(1시간), 서버에서 버퍼링해 늦게 반영하고(30분), 클라이언트가 보낸 세션 ID는 위조될 수 있다고 경고한다. 랭킹은 상호작용을 로그 스케일로 넣고 시간 기반 기본점을 더하거나(Reddit·PeerTube "hot"), 다항식으로 감쇠시키거나(HN gravity 1.8), 기준선 대비 이상치를 고유 계정 수로 재고 반감기 감쇠·자격 필터·운영자 검토를 거치게 한다(Mastodon). 니코니코는 랭킹 입력을 재생·코멘트·マイリスト와 접속 정보로 제한해 조작을 어렵게 했고, 빌리빌리는 희소한 코인을 쓴다.

### Cited Findings
**조회수 정의·검증 예: PeerTube(develop 브랜치, 2026-10-06 커밋)** — [config/default.yaml](https://github.com/Chocobozzz/PeerTube/blob/develop/config/default.yaml)
- `count_view_after: '10 seconds'`: "Minimum amount of time the viewer has to watch the video before PeerTube adds a view"
- `view_expiration: '1 hour'`: "How long does it take to count again a view from the same user"(중복 제거 창)
- `local_buffer_update_interval: '30 minutes'`: "PeerTube buffers local video views before updating and federating the video"(지연 집계)
- `trust_viewer_session_id: true`: "Since this can be spoofed by users to create fake views, you have the option to disable this feature. If disabled, PeerTube will use the IP address to track the same user"
- `watching_interval`: 익명·로그인 모두 5초마다 "is watching" 하트비트
- 트렌딩 알고리즘은 `'hot'`(“Adaptation of Reddit's 'Hot' algorithm”, 기본값), `'most-viewed'`(최근 `interval_days: 7`일 조회수), `'most-liked'`다.
- PeerTube hot 점수 식: `LOG(GREATEST(1, likes-1))*150 + LOG(GREATEST(1, dislikes-1))*(-150) + LOG(views+1)*16 + LOG(GREATEST(1, comments))*100 + (EXTRACT(epoch FROM publishedAt) - 1446156582)/47000`. 주석에는 "weights and base score are in number of half-days", "all comments are counted, regardless of being written by the video author or not", "This algorithm gives little chance for an old video to have a good score"(TODO)라고 적혀 있다 — [videos-id-list-query-builder.ts](https://github.com/Chocobozzz/PeerTube/blob/develop/server/core/models/video/sql/video/videos-id-list-query-builder.ts)

**Reddit(2017년 아카이브 코드, CPAL 1.0)** — [r2/r2/lib/db/_sorts.pyx](https://github.com/reddit-archive/reddit/blob/master/r2/r2/lib/db/_sorts.pyx)
- `hot = round(sign(s)*log10(max(|s|,1)) + (t - 1134028003)/45000, 7)`, 여기서 s = ups − downs. 득표가 10배가 되면 45,000초(12.5시간) 늦게 올라온 글과 같은 점수가 된다.
- `confidence` 정렬은 Wilson score 하한(z = 1.281551565545, 80% 신뢰)을 쓴다. `controversy = (ups+downs)^(balance)`.

**Hacker News(Arc `news.arc`, 커뮤니티 저장소 anarki)** — [apps/news/news.arc](https://github.com/arclanguage/anarki/blob/master/apps/news/news.arc)
- `gravity* 1.8`, `timebase* 120`. `frontpage-rank = ((score-1)^0.8) / (((item-age + 120)/60)^1.8) × 감점계수`이며, item-age는 분 단위로 추정된다. 감점계수는 story/poll이 아닌 항목 .5, URL 없는 글 `nourl-factor* .4`, `lightweight-factor* .3`, `contro-factor`(댓글 수가 20을 넘으면 min(1, (score/댓글 수)^2))다.

**Mastodon(main, 2026-10-07 커밋)**
- 게시물 트렌드: observed = 부스트 + 즐겨찾기 수, expected = 1이다. observed가 `threshold: 5` 미만이면 0점이고, 아니면 `(observed-expected)^2/expected`다. 이 점수는 `score_halflife: 1.hour`로 지수 감쇠하고 `decay_threshold: 0.3` 미만이면 빠진다. 자격 판단에는 공개 범위, 계정 discoverable 여부, silenced 여부(`opted_into_trends?`), 민감 콘텐츠 판정(`sensitive_content?`), 언어 유효성이 들어간다. 운영자가 승인(`allowed`/trendable)한 항목만 공개 트렌드에 오르며, `review_threshold: 3` 순위권에 근접하면 검토 요청이 간다 — [app/models/trends/statuses.rb](https://github.com/mastodon/mastodon/blob/main/app/models/trends/statuses.rb)
- 태그 트렌드: expected = 전날 그 태그를 쓴 고유 계정 수, observed = 당일 고유 계정 수이며 식은 같다. 최대 점수는 반감기 4시간, 쿨다운 2일이다 — [app/models/trends/tags.rb](https://github.com/mastodon/mastodon/blob/main/app/models/trends/tags.rb)

**니코니코·빌리빌리**
- 니코니코는 2019년부터 랭킹에 재생·코멘트·マイリスト만 쓰고 접속 정보를 더해 "ランキング工作をしづらく"했다 — [ニコニコ窓口](https://ch.nicovideo.jp/nicotalk/blomaga/ar1740969)
- 빌리빌리 코인은 하루 1개만 생기고 영상당 최대 2개, 자기 영상에는 줄 수 없다. 공급이 제한된 지지 신호다 — [bilibili 积分&硬币规则](https://www.bilibili.com/html/point.html)

### Inferences
모두 추정.
- **"재생"과 "유효 조회"를 분리**한다. 재생 시작은 모두 이벤트로 기록하되, 공개 조회수는 ① 최소 시청 시간(PeerTube 10초를 출발점으로, 3~4분짜리 MV라면 10~30초 또는 길이 대비 비율), ② 같은 시청자 재집계 금지 창(1시간~24시간), ③ 서버 버퍼링 후 지연 반영, ④ 봇 UA·데이터센터 IP·헤드리스 패턴 필터를 거친 값만 쓴다.
- **클라이언트 식별자를 신뢰하지 않는다**(PeerTube 경고). 로그인 사용자를 우선 집계하고, 익명은 IP와 기기 신호를 조합한 보수적 집계를 한다. 내보낸 YouTube 조회수와 자체 조회수는 섞지 않고 따로 표시한다.
- **반응의 의미를 분리**한다. 좋아요(1인 1회, 취소 가능), 저장(マイリスト형 공개 큐레이션), 희소 응원(코인형, 후순위). 자기 작품에 한 상호작용과 작성자 댓글은 점수에서 뺀다(PeerTube는 작성자 댓글도 센다는 점을 개선).
- **랭킹 3종**: "인기(hot)"는 로그 스케일 + 게시 시각 기본점(Reddit·PeerTube) 또는 HN식 다항 감쇠로 계산한다. "급상승"은 Mastodon식으로 기준선 대비 이상치를 고유 계정 수로 재고 반감기를 적용하며, 운영 검토 게이트를 둔다. "부문별"은 오리지널/리믹스/신인(ボカコレ)으로 나눈다. 유료 홍보는 반영하지 않는다(니코니코 2019).
- **투명성과 내성의 균형**: 니코니코처럼 계산 원칙(사용하는 신호 종류)은 공개하고 가중치와 탐지 신호는 비공개로 둔다. 이상 급증은 랭킹 반영을 늦추고 보류한다.

### Gaps
- YouTube의 공식 조회수 정의·검증(Help "How engagement metrics are counted"), 2025년 Shorts 조회수 산정 변경과 "engaged views", 가짜 참여(fake engagement) 정책 원문은 검색 한도로 조사하지 못했다. 배경지식상 YouTube는 검증 후 조회수를 늦게 반영하고 2025년 3월 Shorts 집계 방식을 바꿨으나 미확인이며, 본문 사용 전 검증이 필요하다.
- 니코니코 재생수 집계 기준(중복·새로고침 처리)과 2024년 이후 랭킹 세부를 확인하지 못했다.
- 탄막·코멘트 도배 대응, 빌리빌리 播放量 산정과 刷量 대응, 학술 연구(탄막, 조회 조작 탐지)를 조사하지 못했다.
- HN 실제 운영 알고리즘은 공개 Arc 코드와 다를 수 있다(추정). Reddit 코드는 2017년 아카이브본이다.

---

## 4. 저작권: 오리지널 vs 커버, 포괄 이용허락, 원반·MR 허락, 노티스-앤-테이크다운, 오디오 핑거프린팅

### Takeaway
JASRAC·NexTone·KOMCA의 UGC 포괄 이용허락은 서비스(사업자) 단위 계약이다. 그러니 신규 플랫폼이 기존곡 커버(歌ってみた 등)를 호스팅하려면 자체 계약이 필요해 보이며(추정), 그 계약도 작사·작곡 권리만 처리한다. 원반·オフボーカル·MR과 편곡은 별도 허락 대상이다. 한국에서는 저작권법 103조가 "소명 → 즉시 중단 + 통보 → 재개 요구 → 재개" 절차를 정하고 102조가 책임 제한을 준다. 104조 필터링 의무는 P2P·웹하드형 "특수한 유형"에 적용된다. 일본은 2025-04-01 시행된 情報流通プラットフォーム対処法이 지정된 대규모 사업자에게만 신청 창구, 원칙 7일 내 통지, 투명성 의무를 지운다. 제3자가 쓸 수 있는 식별 도구는 Chromaprint/AcoustID(LGPL 2.1, 상업 API는 유료)와 Audible Magic(상용)이 확인됐다.

### Cited Findings
**일본: 포괄계약**
- JASRAC는 「利用許諾契約を締結しているUGCサービスの一覧」을 공개한다(최종 갱신 2026-08-21). 예로 Avvy, アメーバアプリ, AWAラウンジ, 17LIVE, IRIAM, Instagram, うたスキ動画 등이 있다 — [JASRAC](https://www.jasrac.or.jp/information/topics/20/ugc.html). 이 목록은 2014년에 처음 공개됐다 — [マイナビニュース 2014-03-19](https://news.mynavi.jp/techplus/article/20140319-a110/). UGC 이용 안내 — [JASRAC「YouTubeなどの動画投稿（共有）サービスでの音楽利用」](https://www.jasrac.or.jp/users/internet/ugc/)
- NexTone 관리곡도 UGC 사이트와 포괄계약을 맺는다. 사업자가 사용료를 내므로 이용자는 별도 절차 없이 업로드할 수 있다 — [red-iguana-studio(2026)](https://red-iguana-studio.com/jasrac-nextone-search/) (2차)
- 계약 여부는 서비스마다 다르다. Facebook이 JASRAC와 포괄계약을 맺었다는 사실이 따로 화제가 된 적이 있다 — [Yahoo!ニュース エキスパート(栗原潔)](https://news.yahoo.co.jp/expert/articles/4ef48f4e361df2d2fd451859068ef1461483ba45). X에 동영상을 올리려면 JASRAC 등의 허락이 필요하다는 해설도 있다 — [インターブックス note](https://note.com/interbooksjp/n/n9ad55b593a45?hl=en) (2차, 시점 미확인)
- 소규모 사업자가 JASRAC/NexTone 허락을 취득했다고 공지한 사례 — [ともしょう](https://www.tomoshou.jp/post/jasrac-nextone-license) (서비스 성격과 조건은 미확인)
- 커버에서의 원반·편곡 문제는 2절 참조. 포괄계약은 "歌うこと"만 다루고, 원반 사용과 翻案은 별도다.

**한국: 포괄계약**
- KOMCA는 유튜브와 포괄계약을 맺어 등록 저작물의 사용료를 대리 징수하고, 메타 등 UGC 플랫폼과도 이용계약을 맺었다. 개인이 직접 가창·연주한 커버는 그 범위 안에서 처리되지만, 원곡 음원과 공식 MR은 해당하지 않는다 — [studionol](https://studionol.co.kr/ko/stories/copyright-cover1), [로톡](https://www.lawtalk.co.kr/posts/167886) (2차)

**한국: 노티스-앤-테이크다운(저작권법)**
- 제103조: ①권리주장자는 사실을 소명해 복제·전송 중단을 요구할 수 있다. ②OSP는 "즉시" 중단하고 권리주장자에게 통보해야 하며, "제102조제1항제3호의 온라인서비스제공자"는 복제·전송자에게도 통보해야 한다. ③복제·전송자가 정당한 권리임을 소명해 재개를 요구하면, 재개 요구 사실과 재개 예정일을 권리주장자에게 지체 없이 통보하고 그 예정일에 재개해야 한다 — [국가법령정보센터 제103조](https://www.law.go.kr/LSW/lsLawLinkInfo.do?lsJoLnkSeq=900605727&lsId=000798&chrClsCd=010202&print=print), [CaseNote 제103조](https://casenote.kr/%EB%B2%95%EB%A0%B9/%EC%A0%80%EC%9E%91%EA%B6%8C%EB%B2%95/%EC%A0%9C103%EC%A1%B0)
- 제102조: OSP가 침해 사실을 알고 복제·전송을 방지하거나 중단한 경우 책임이 제한된다는 취지 — [찾기쉬운 생활법령 「음악저작물 이용 > 저작권 침해 구제」](https://www.easylaw.go.kr/CSP/CnpClsMainBtr.laf?popMenu=ov&csmSeq=705&ccfNo=3&cciNo=2&cnpClsNo=3) (검색 요약 기준, 현행 조문 세부 요건은 미확인)
- 제104조: "다른 사람들 상호 간에 컴퓨터를 이용하여 저작물등을 전송하도록 하는 것을 주된 목적"으로 하는 OSP는 "특수한 유형"으로, 권리자가 요청하면 불법 전송을 차단하는 기술적 조치를 해야 한다. 범위는 문체부 장관이 고시하고, 시행령 46조 1항은 "저작물의 제호 등과 특징을 비교하여 침해물을 인식할 수 있는 기술적인 조치"를 규정한다. 대상은 P2P·웹하드 등이다 — [헌재 2009헌바56 결정](https://www.law.go.kr/LSW/detcInfoP.do?mode=1&detcSeq=14973), [KCI 논문](https://www.kci.go.kr/kciportal/ci/sereArticleSearch/ciSereArtiView.kci?sereArticleSearchBean.artiId=ART002099473), [웹하드 기술적 조치 가이드라인(안)](https://www.korea.kr/archive/expDocView.do?docId=30460)

**일본: 情報流通プラットフォーム対処法(情プラ法)**
- 2025-04-01 시행. プロバイダ責任制限法을 개정하고 이름을 바꾼 법이다 — [総務省 令和7年版 情報通信白書](https://www.soumu.go.jp/johotsusintokei/whitepaper/ja/r07/html/nd123210.html), [契約ウォッチ](https://keiyaku-watch.jp/media/hourei/joho-platform-2024/), [総務省 違法・有害情報への対応](https://www.soumu.go.jp/main_sosiki/joho_tsusin/d_syohi/ihoyugai.html)
- 大規模特定電気通信役務提供者 지정 요건은 (국내) 월간 발신자 수 1,000만 이상 또는 월간 延べ 발신 수 200만 이상 등이며, 삭제가 기술적으로 가능하고 권리침해 가능성이 낮은 서비스가 아니어야 한다. 의무는 삭제 신청 창구의 설치·공표(온라인, 신청자에게 과도한 부담이 없을 것), 신청자에게 원칙적으로 7일 이내 통지, 운용 상황 투명화(삭제 기준 공표 등)다. 2025년 4월에 Google LLC, LINEヤフー, Meta Platforms, TikTok Pte. 등이 지정됐다 — [北浜法律事務所](https://www.kitahama.or.jp/topics/latest-00002/), [森大輔法律事務所](https://moridaisukelawoffices.com/page-2039/page-3437), [総務省 白書](https://www.soumu.go.jp/johotsusintokei/whitepaper/ja/r07/html/nd123210.html) (수치·지정 목록은 검색 요약 기준)

**오디오 핑거프린팅(YouTube Content ID 대안)**
- Chromaprint 라이선스: "Chromaprint's own source code is licensed under the MIT license, but we include some parts of the FFmpeg library, which is licensed under the LGPL 2.1 license. As a whole, Chromaprint should be therefore considered to be licensed under the LGPL 2.1 license." 바이너리로 배포할 때는 외부 FFT 라이브러리의 라이선스도 고려해야 한다 — [chromaprint LICENSE.md(master, 2026-07-28 커밋)](https://github.com/acoustid/chromaprint/blob/master/LICENSE.md)
- AcoustID 지문 DB는 비상업 용도로 무료이고, 상업 이용은 AcoustID OÜ의 유료 플랜을 거친다 — [acoustid.biz(보관본)](https://web-archive.nli.org.il/National_Library/mp_/https://acoustid.biz), [APIs.io AcoustID](https://apis.io/providers/acoustid/) (가격 미확인)
- Audible Magic은 Facebook, Instagram, Twitch, Dailymotion, SoundCloud, Vimeo, ShareChat 등 UGC 사이트가 쓴다. 업로드 시점(ingest)에 통합되어 저작물 매칭과 비즈니스 룰을 통지한다 — [Audible Magic「Copyright Compliance for UGC Platforms」](https://www.audiblemagic.com/?p=6226). 2019-09에 나온 UGC Music Rights Platform(UMRP)은 음악 식별, 직접 협상한 라이선스로의 클리어, 로열티 정산·보고 대행을 묶은 턴키 서비스다 — [Hypebot 2019-09](https://www.hypebot.com/hypebot/2019/09/audible-magic-monetizes-music-on-user-generated-content.html). 권리자는 자기 콘텐츠를 등록한다 — [Audible Magic support](https://support.audiblemagic.com/why-register-your-content-with-audible-magic)
- Pex 등을 비교한 목록 — [Third Chair 블로그](https://usethirdchair.com/blog/top-content-id-companies-for-music-rights-teams) (내용 미검토)

### Inferences
모두 추정.
- **포괄계약 전 단계 정책**: JASRAC·NexTone(일본), KOMCA 및 기타 신탁단체(한국)와 계약하기 전에는 오리지널곡과 권리자가 허락한 자산만 받고, 기존 관리곡 커버(歌ってみた)는 막거나 "외부 YouTube 링크 임베드만" 허용한다. 계약은 국가별·서비스별로 따로 필요할 것으로 보인다(JASRAC 목록이 서비스 단위).
- **오리지널곡의 함정**: 작곡자 본인이 JASRAC·NexTone에 신탁한 곡이라면 본인이 올리는 경우에도 단체 허락 대상일 수 있다. 업로드 때 "신탁 여부"를 묻는 것이 좋다(신탁 계약 조건은 미확인).
- **크로스포스팅**: YouTube로 내보낸 사본은 YouTube의 계약과 Content ID가 처리하지만, 우리 서버에서 스트리밍되는 사본의 책임은 우리에게 있다.
- **탐지의 한계**: Chromaprint 같은 녹음 지문은 원반·オフボーカル을 그대로 쓴 경우는 잡지만, 다시 연주·가창한 커버는 잡기 어렵다. 그러니 커버는 원곡 선택을 메타데이터로 강제해 라이선스 카탈로그와 대조하고, 신고 처리로 보완한다. 허락된 오프보컬과 원반을 자체 지문 DB에 등록하면 "허락된 사용"을 확인하는 데도 쓸 수 있다.
- **라이선스 실무**: Chromaprint는 LGPL 2.1이므로 동적 링크와 소스 고지 의무를 지킨다. AcoustID 공용 API를 상업적으로 쓰려면 유료 계약이 필요하다. 처음에는 Chromaprint와 자체 DB로 가고, 규모가 커지면 Audible Magic 같은 상용 서비스를 검토한다.
- **처리 흐름**: 한국 103조 절차를 기본 노티스-앤-테이크다운으로 구현한다(소명 접수 → 즉시 비공개 → 양측 통지 → 업로더 재개 요구 → 재개 예정일 통지 → 재개). 반복 침해자 정책도 둔다. 일본의 소규모 플랫폼은 대규모 지정 대상이 아니지만 신청 창구, 처리 기준 공개, 7일 내 응답을 모범 관행으로 먼저 채택한다.
- 104조 "특수한 유형"(P2P·웹하드형)에 스트리밍 중심 UGC MV 플랫폼은 해당하지 않을 가능성이 높다. 다만 원본 파일 다운로드 기능을 넣으면 재검토해야 한다.

### Gaps
- JASRAC·NexTone·KOMCA의 UGC 사용료율, 최소 보증금, 소규모 사업자 계약 절차, MV(동기화) 범위 포함 여부를 조사하지 못했다.
- 함께하는음악저작인협회(KOSCAP) 계약 필요 여부를 조사하지 못했다.
- 현행 저작권법 102조의 서비스 유형별 세부 요건(반복 침해자 정책, 표준 기술조치 등) 원문과, 문체부 고시상 "특수한 유형" 범위에 UGC 영상 서비스가 들어가는지를 확인하지 못했다.
- 일본 情プラ法에 남은 책임제한 조항(구 プロ責法 3조 상당)의 요건, 2026년 기준 지정 사업자 목록(드왕고·X 등 추가 여부)을 확인하지 못했다.
- Pex, ACRCloud, BMAT 등 상용 식별 서비스의 가격과 커버(재연주) 탐지 능력을 조사하지 못했다.
- YouTube 고객센터 「어떤 콘텐츠를 수익 창출에 사용할 수 있나요?」는 링크만 확인하고 내용은 검토하지 못했다 — [YouTube Help(KR)](https://support.google.com/youtube/answer/2490020?hl=ko)

---

## 5. 캐릭터 라이선스: PCL·크립톤 가이드라인·重音テト, 플랫폼의 캐릭터 사용과 리그·템플릿 배포

### Takeaway
크립톤 캐릭터(初音ミク 등)는 PCL에 따라 누구나 쓸 수 있지만 "비영리이면서 무상"인 2차 창작에 한한다. 영리 목적 이용, 그리고 비영리라도 대가를 받는 이용은 PCL 밖이다. 크립톤은 개인용 가이드라인을 개정해 개인의 YouTube 수익화를 허용했으나(6월 1일자, 연도 미확인) 제3자 권리 콘텐츠가 들어간 영상은 제외했다. 2024-12-04에는 선전·광고 목적 이용 등이 늘고 있다며 경고를 냈다. 重音テト는 PCL을 준용해 고친 규약을 쓴다. 비상업 이용은 법인·개인 모두 무료이고, 상업 이용은 사전 연락이 필요하며 창구는 크립톤에 위탁되어 있다. 개인의 곡 판매나 BOOTH 굿즈는 상업 이용으로 보지 않는다. 우리 플랫폼(법인)이 미쿠·테토를 UI, 마케팅, 템플릿, "공식처럼 보이는 리그"에 쓰는 것은 상업 라이선스가 필요한 영역일 가능성이 높다(추정).

### Cited Findings
**크립톤(初音ミク 등)**
- PCL: 피아프로 캐릭터의 2차 창작물을 영리 목적으로 이용할 수 없고, 비영리라도 대가를 받거나 보수를 받고 이용할 수 없다 — [PCL 全文](https://piapro.jp/license/pcl), [PCL 解説(2条〜3条)](https://blog.piapro.net/2009/06/23.html)
- キャラクター利用のガイドライン: 영리 목적이 아니고 무상으로 배포한다면 2차 창작이 허용된다. 예시로 홈페이지·SNS 공개, 그림·피규어 배포, 코스프레 이벤트 참가가 있다 — [piapro キャラクター利用のガイドライン](https://piapro.jp/license/character_guideline), [piapro blog 2009](https://blog.piapro.net/2009/05/post-216.html)
- 2차 창작물의 다운로드 판매는 원칙적으로 허락하지 않는다(2012) — [ITmedia NEWS](https://www.itmedia.co.jp/news/articles/1202/22/news116.html)
- 동인 판매: 취미 범위이면서 원재료비 정도의 이익만 나는 경우 ピアプロ・リンク에 신청해 수리되면 활동이 인정된다 — [virtual-voice-cafe 해설](https://ameblo.jp/virtual-voice-cafe/entry-12818161351.html) (2차)
- YouTube 수익화: 6월 1일자로 初音ミク를 쓴 개인의 YouTube 파트너 프로그램 수익화가 해금됐다. 단, 크립톤 외 제3자가 권리를 가진 콘텐츠가 들어간 영상은 대상이 아니다 — [KAI-YOU「初音ミクを使用したYouTube動画の収益化が認められる 個人向けガイドラインに改訂」](https://kai-you.net/article/80543) (개정 연도 미확인)
- 2024-12-04 크립톤 성명: SNS 등에서 가이드라인을 넘는 이용이 늘고 있다며 PCL에 따른 적정 이용을 호소했다. 비영리 창작은 계속 자유지만 선전·광고 목적 이용과 타인 권리 침해 등은 금지 사항으로 명확히 했다 — [XEXEQ](https://xexeq.jp/blogs/media/topics28818), [おたくま経済新聞(キングソフト経由) 2024-12-06](https://home.kingsoft.jp/news/amusing/otakuma/2024120604.html)
- 크립톤은 「利用可能キャラクター一覧」 페이지를 운영한다 — [Crypton](https://www.crypton.co.jp/cfm/available_characters). PCL이 만들어진 배경을 크립톤이 발표한 자료(Internet Week 2014)도 있다 — [JPNIC PDF](https://www.nic.ad.jp/ja/materials/iw/2014/proceedings/s12/s12-hishiyama.pdf) (내용 미검토)

**重音テト(TWINDRILL)**
- 권리 표기는 비주얼 디자인 "線", 음성 제작 "小山乃舞世", 공식 운영 "TWINDRILL" 3자 연명이다. 캐릭터 이용규약은 PCL을 準用해 테토의 사정에 맞게 고친 것이다 — [重音テト キャラクター利用規約](https://kasaneteto.jp/guidelines/character.html), [ガイドライン](https://kasaneteto.jp/guidelines/)
- 비상업 활동 범위(영리 목적이 아닌 동인 활동 포함)라면 법인·개인을 가리지 않고 무료로 쓸 수 있다. 상업 이용은 사전 연락이 필요하며 창구는 크립톤 퓨처 미디어에 위탁되어 있다. 개인이 음악 배信 서비스에서 곡을 유료로 팔거나, 동인 CD를 만들어 배포·판매하거나, BOOTH에서 굿즈를 파는 것은 상업 이용에 해당하지 않는다 — [キャラクター利用規約](https://kasaneteto.jp/guidelines/character.html), [Character Terms of Use(EN)](https://kasaneteto.jp/guideline/ctu.html), [版権申請フォーム](https://kasaneteto.jp/guidelines/request.html)
- 상업 제품의 권리를 지키려고 상표화했으며, 동인 이용은 종전대로다 — [ねとらぼ](https://nlab.itmedia.co.jp/cont/articles/3268868/)
- 2023-06-29 공식 게시물: 공식이 파악·관리할 수 없는 것을 "重音テト"로 무단 재배포하지 말 것, 그리고 "Synthesizer V AI 重音テトを使用したAI学習はDreamtonics社の利用規約違反" — [X @twindrill_teto](https://x.com/twindrill_teto/status/1674403130287751168)
- 공식 서클 개요 — [ニコニコ大百科「重音テト公式サークル『ツインドリル』」](https://dic.nicovideo.jp/a/%E9%87%8D%E9%9F%B3%E3%83%86%E3%83%88%E5%85%AC%E5%BC%8F%E3%82%B5%E3%83%BC%E3%82%AF%E3%83%AB%E3%80%8E%E3%83%84%E3%82%A4%E3%83%B3%E3%83%89%E3%83%AA%E3%83%AB%E3%80%8F) (2차). 음원 이용규약은 별도이며 이 노트의 범위 밖이다 — [音源利用規約](https://kasaneteto.jp/guidelines/voice.html)

### Inferences
모두 추정.
- **플랫폼 운영자의 사용**: 법인이 광고·구독 수익을 내는 서비스에서 미쿠를 UI 마스코트, 랜딩·광고 소재, 기본 템플릿 썸네일, "공식 미쿠 리그" 형태로 쓰면 PCL의 "비영리이면서 무상"을 넘어선다. 2024-12 성명이 명시한 "선전·광고 목적 이용" 금지에도 걸린다. 크립톤과 상업 라이선스(상품화·서비스 이용) 계약이 필요하다고 봐야 한다.
- **테토**: 상업 창구가 크립톤에 위탁되어 있으므로, 미쿠×테토를 함께 쓰는 라이선스도 크립톤을 단일 창구로 협의할 수 있을 가능성이 있다. 테토 규약은 개인의 판매 활동을 더 넓게 허용하지만(곡 판매·BOOTH), 플랫폼 법인의 서비스 이용은 별개다.
- **"공식처럼 보이는" 리그·템플릿 배포**: TWINDRILL은 비공식 산출물을 "重音テト"로 재배포하지 말라고 했고 크립톤은 공식 오인을 경계한다. 따라서 라이선스 없이 "Miku/Teto 공식 리그" 같은 표기나 배포는 피해야 한다. 라이선스를 받기 전에는 사용자가 업로드한 팬 메이드 모델(각 모델의 배포 조건 준수)을 비상업 플래그와 함께 공유하게 하거나, 오리지널 캐릭터 리그를 기본으로 제공한다.
- **사용자 작품 수익화**: 크립톤 개인 가이드라인상 개인의 YPP 수익화는 허용되지만, 제3자 권리 콘텐츠(예: 남의 일러스트·모델·원반)가 들어가면 대상에서 빠진다. 계보와 자산 정보를 근거로 내보내기 전에 경고를 띄운다.
- **권리 표기 자동화**: 캐릭터를 쓴 작품에는 크립톤과 테토 권리 표기를 자동으로 넣고, "公式" 오인을 막는 문구를 둔다.

### Gaps
- 크립톤의 생성AI 관련 공식 가이드라인(AI로 만든 미쿠 이미지·영상, 학습 이용)을 이번 검색에서 확인하지 못했다. 2024-12 성명이 AI를 직접 언급했는지도 미확인이다.
- 법인 상업 라이선스 절차·비용, 상표(「初音ミク」) 사용 규정, KEI 등 공식 일러스트의 사용 범위를 조사하지 못했다.
- KAI-YOU가 보도한 수익화 개정의 연도를 확인하지 못했다.
- 테토 규약 중 3D 모델·리그 배포, AI 생성 테토 이미지에 관한 조항을 확인하지 못했다.
- 다른 서비스가 크립톤 라이선스를 받아 "플랫폼 제공 캐릭터 리그"를 낸 선례를 조사하지 못했다.

---

## 6. 생성형 AI: 보카로 커뮤니티의 태도, 플랫폼 정책, 라벨링·필터 요구

### Takeaway
보카로 문화에서 MV 일러스트는 곡의 정체성이다. 그래서 MV에 AI 생성 일러스트를 쓰면 반발이 일어났다(2024년 이후 사례). pixiv는 2026-03-18부터 AI 설정 허위 신고와 대량 투고를 명시적으로 금지하고 위반 의심 작품을 기본 비표시로 하는 등 "자기 신고 + 필터 + 제재" 모델을 강화했다. YouTube는 실제처럼 보이는 합성·변형 콘텐츠에 공개를 의무화했고(2024-03 토글), 2025-07 대량생산형 "inauthentic content" 기준을 강화했다. 이 문화의 창작자에게 필요한 것은 자산별 AI 사용 신고, AI 작품 표시·비표시 필터, 허위 신고 제재, 그리고 이벤트 규칙의 사전 명시다(추정).

### Cited Findings
- 2024-02-21 X 게시물 "AIイラストを使って制作したボカロPが叩かれている" — [X 投稿](https://x.com/TomoyaKinoshita/status/1760123788212183356). 같은 검색 결과 요약에는 보카로에서 AI 일러스트 사용에 "相当数の忌避感"이 생겼다는 내용, 보카로 투고 이벤트 중 AI 이미지 사용이 논란이 되어 "AIイラストを使うボカロPは全イラストレーターの敵" 같은 극단적 의견도 나왔다는 내용이 있다 (검색 요약 기준. 정확한 출처 문장은 미확인)
- 보카로 문화는 일러스트와 함께 성장해 유명곡을 일러스트로 알아볼 정도이고, 그래서 AI 이미지 사용에 반발이 생긴다는 논지 — [きお/Keogh note「自作曲動画へのAI生成イラストの使用についての考え」](https://note.com/keogh116/n/n6667982f3c88) (개인 의견, 2차)
- ボカロP ねじ式은 음악 제작에서 창작을 AI에 맡기지 않겠다고 했고, AI 음악과 비AI 음악은 공존할 것이라고 봤다 — [CPRA「生成AIについて○○さんに聞いてみた ―ボカロP編―」](https://www.cpra.jp/cpra_article/article/000791.html)
- ボカコレ2026冬 REMIX 랭킹 1위곡이 생성AI로 만들어진 사실이 드러나 투고자가 사퇴했고 랭킹에서 빠졌다는 보도가 있다 — [menuguildsystem](https://www.menuguildsystem.com/work-won-first-place-at-bocacole-2026-was-created-using-generative-ai/) (저신뢰 집계 사이트, 미확인. 공식 발표 대조 필요)
- pixiv는 AI 생성 작품을 다루는 기능을 내놓았고 — [pixiv News(id=8733)](https://www.pixiv.net/info.php?id=8733), 앱 검색 옵션에도 AI 생성 작품 설정을 추가했다 — [pixiv News(id=10239)](https://www.pixiv.net/info.php?id=10239). 사용자는 검색 옵션에서 "AI生成作品"을 표시/비표시로 고를 수 있고, 사용자 설정에서 상시 비표시로 할 수 있다 — [aiteller](https://aiteller.jp/blog/news-pixiv-ai-guideline-2026) (2차)
- pixiv 가이드라인 개정(2026-03-18 시행): AI 생성 작품 설정의 허위 신고, 선전 목적 대량 투고, 작품 내용과 맞지 않는 투고 정보(연령 제한·오리지널 작품·AI 생성 작품 설정·장르·태그 등) 설정을 금지한다. 검색 기본값으로 "違反疑い作品を表示しない"를 데스크톱·모바일·앱에 제공한다 — [ITmedia AI+ 2026-02-18](https://www.itmedia.co.jp/aiplus/article/2602/18/1260218132/), [aiteller](https://aiteller.jp/blog/news-pixiv-ai-guideline-2026)
- 플랫폼별 생성AI 대응 정리 — [生成AI問題まとめwiki](https://w.atwiki.jp/genai_problem/pages/27.html) (2차)
- YouTube: 2023-11-14 발표, 2024-03 Studio 토글 시행. 시청자가 실제로 착각할 수 있는 AI 생성·변형 미디어(실존 인물이 하지 않은 말·행동, AI 음성 등)는 공개 설정이 필요하다. 라벨은 설명란에 붙고, 민감한 주제는 플레이어에도 표시된다. YouTube가 자체 탐지, C2PA 메타데이터, 수동 검토로 라벨을 직접 붙일 수 있고, 위반하면 광고 제한·수익 중단·연령 제한·삭제가 가능하다 — [AIR Media-Tech](https://air.io/en/youtube-glossary/what-is-youtubes-ai-content-disclosure-policy), [PPC Land](https://ppc.land/youtube-introduces-mandatory-disclosure-for-ai-content/), [Jasper](https://www.jasper.ai/blog/youtube-synthetic-content-disclosures) (모두 2차)
- YouTube는 2025-07 YPP "inauthentic content" 기준(대량생산·반복형 콘텐츠)을 개정했다 — [Influencer Marketing Hub](https://influencermarketinghub.com/youtube-inauthentic-content) (2차)
- TWINDRILL: SV AI 重音テト를 써서 AI 학습을 하는 것은 Dreamtonics 이용규약 위반이다(2023-06-29) — [X @twindrill_teto](https://x.com/twindrill_teto/status/1674403130287751168)

### Inferences
모두 추정.
- **자산별 AI 사용 신고**: 작곡, 작사, 보컬(음성합성), 일러스트, 영상·모션마다 "사람 제작 / AI 보조 / AI 생성"을 고르게 한다. 공개 라벨, 검색·피드의 표시/비표시 필터, 허위 신고 제재(비표시·랭킹 제외)를 함께 둔다. pixiv 모델을 따른 것이다.
- **"음성합성"과 "생성AI"를 구분**한다. Synthesizer V AI처럼 음성합성 자체가 AI 기반이므로, 보카로 문화에서 이미 받아들여진 "음성합성 소프트웨어 사용"과 논쟁적인 "생성AI로 곡·가사·그림 생성"을 라벨에서 구별해야 한다. 구별하지 않으면 모든 작품에 AI 라벨이 붙는 문제가 생긴다.
- **이벤트·랭킹 규칙**: AI 허용 범위를 미리 명시하고 일관되게 적용한다. ボカコレ 2026 夏의 규칙 적용 논란과 2026 冬 AI 사례(미확인)를 반면교사로 삼는다.
- **YouTube 내보내기**: 실사로 오인될 수 있는 합성 영상이면 "변형·합성 콘텐츠" 공개 설정이 필요하다고 안내한다. 생성 도구가 C2PA 메타데이터를 만들면 보존한다.
- **학습 이용**: 플랫폼이 사용자 작품을 AI 학습에 쓰지 않는다는 점을 약관에 명시하고, 작품 단위로 "AI 학습 불허" 표시를 두는 것을 검토한다(커뮤니티 요구에 관한 정량 출처는 없음).

### Gaps
- 니코니코(ドワンゴ)의 생성AI 관련 공식 정책(태그, 랭킹·奨励プログラム 취급, ボカコレ 규약의 AI 조항 원문)을 조사하지 못했다.
- 크립톤과 TWINDRILL의 AI 생성 캐릭터 이미지 정책을 확인하지 못했다.
- 창작자·시청자 대상 설문(라벨·필터 선호) 같은 정량 데이터를 조사하지 못했다.
- YouTube 공식 헬프 원문(공개 의무 범위, inauthentic content 정의)은 2차 출처로만 확인했다.

---

## 7. 광과민성 발작 안전: 기준, 자동 분석 도구, 경고 방식

### Takeaway
WCAG 2.3.1(Level A)은 1초에 3회를 넘는 점멸을 금지한다. 일반 플래시와 적색 플래시 임계값(10° 시야의 25%, 1024×768 기준 341×256 px, 87,296 CSS px²)보다 작으면 예외다. Understanding 문서는 SDR·HDR 영상에 ITU-R BT.1702의 휘도 정의(20 cd/m², 160 cd/m², Michelson 1/17)를 인용하고, 참고 도구로 Harding FPA와 Trace Center PEAT를 든다. EA IRIS는 3-clause BSD 형태 오픈소스(의존성 FFmpeg는 LGPL 2.1)로, 휘도·적색 플래시와 공간 패턴을 탐지하며 명시적인 실패 기준이 있다. 다만 "certify" 용도는 아니다. 강한 점멸 연출이 많은 MV 장르 특성상(추정) 업로드·내보내기 시 자동 분석, 에디터 단계 제한, 시청 전 경고 구조가 필요하다.

### Cited Findings
- WCAG 2.3.1 Three Flashes or Below Threshold(Level A): "Web pages do not contain anything that flashes more than three times in any one second period, or the flash is below the general flash and red flash thresholds." 비간섭 요건(Conformance Requirement 5)에 따라 페이지의 모든 콘텐츠가 이를 충족해야 한다 — [W3C WCAG 소스 guidelines/sc/20](https://github.com/w3c/wcag/blob/main/guidelines/sc/20/three-flashes-or-below-threshold.html) (공개본은 w3.org/WAI/WCAG22, 2026-10-04 커밋 기준)
- 임계값 정의: 1초 안에 일반 플래시 3회 이하 및/또는 적색 플래시 3회 이하, 또는 동시에 일어나는 플래시 면적의 합이 10° 시야 안에서 0.006 steradian(10° 시야의 25%) 이하. 일반 플래시는 최대 상대휘도(1.0)의 10% 이상인 상반된 변화의 쌍이면서 어두운 쪽의 상대휘도가 0.80 미만인 경우다. 적색 플래시는 포화 적색이 관련된 상반 전이의 쌍이며, WCAG 2.2 작업 정의로는 한쪽 상태가 R/(R+G+B) ≥ 0.8이고 CIE 1976 UCS 색도 차가 0.2를 넘는 경우다(ISO 9241-391 인용). 1024×768 화면에서 341×256 px 사각형이 10° 시야의 근사치다. 0.1° 미만의 미세하고 균형 잡힌 패턴(화이트 노이즈 등)은 예외다. 점멸이 1초에 3회 이하이면 도구 없이 통과한다 — [WCAG 용어 정의 general flash and red flash thresholds](https://github.com/w3c/wcag/blob/main/guidelines/terms/20/general-flash-and-red-flash-thresholds.html)
- Understanding 2.3.1: CSS 픽셀로 바꾸면 341×256 = 87,296 CSS px² 면적이다. sRGB가 아닌 색공간의 영상은 다이내믹 레인지가 가장 높은 버전을 테스트한다. 업계 표준 정의(ITU-R BT.1702)에서 일반 플래시는 휘도가 20 cd/m² 이상 변하고 어두운 쪽이 160 cd/m² 미만인 경우(SDR·HDR 공통)이며, HDR에서 어두운 쪽이 160 cd/m² 이상이면 Michelson 대비 ≥ 1/17이다. 참고 자료로 "Harding FPA Web Site", "Trace Center Photosensitive Epilepsy Analysis Tool (PEAT)", Epilepsy Foundation을 든다 — [WCAG Understanding 2.3.1 소스](https://github.com/w3c/wcag/blob/main/understanding/20/three-flashes-or-below-threshold.html)
- EA IRIS: "identify video footage that could potentially cause photosensitive epileptic risks"를 위한 크로스플랫폼 C++ 라이브러리다. 휘도 플래시, 적색 포화 플래시, 공간 패턴을 탐지한다. 기준은 어느 1초든 3회 초과면 flash failure, 연속 5초 동안 초당 2~3회면 extended flash failure, 유해 패턴이 0.5초 이상이면 pattern failure다. 프레임별 CSV/JSON을 출력하며(전이 판정 임계: 평균 휘도 차 누적 ±0.1, 적색 ±20), 기본 설정은 "publicly available guidelines" 기반이다. README는 "IRIS is not intended to guarantee, certify or otherwise validate visual content's compliance with legal, regulatory or other requirements"라고 밝힌다 — [IRIS README](https://github.com/electronicarts/IRIS/blob/main/README.md)
- IRIS 라이선스: "Copyright (c) 2023 Electronic Arts Inc.", 3-clause BSD 형태(재배포 시 저작권 고지 유지, EA 이름으로 보증·홍보 금지) — [LICENSE.txt](https://github.com/electronicarts/IRIS/blob/main/LICENSE.txt). 사용하는 오픈소스는 FFmpeg(LGPL 2.1), nlohmann-json(MIT) 등이다 — [NOTICE.txt](https://github.com/electronicarts/IRIS/blob/main/NOTICE.txt). 마지막 커밋은 2025-01-14다(git 조회).

### Inferences
모두 추정.
- **서버 분석**: 렌더 완료와 내보내기 시점에 IRIS를 돌린다. flash failure나 pattern failure가 나오면 공개 전에 경고하고 해당 구간을 타임라인에 표시한다. extended failure는 주의로 표시한다. BSD 형태라 상업 서비스에도 쓸 수 있지만, FFmpeg LGPL 고지와 링크 방식은 지켜야 한다.
- **에디터 단계의 예방**: 스트로브·플래시 이펙트 프리셋은 기본 3Hz 이하로 하고 면적을 제한한다. 전면 적색 점멸은 경고한다. 미리보기에서 실시간 경고를 띄운다. "안전 모드" 프리셋을 둔다.
- **시청자 보호**: 자동 "点滅注意/점멸 주의" 라벨, 재생 전 경고 카드(클릭해야 재생), 계정 설정의 "점멸 완화 모드"(문제 구간 밝기 감쇠·건너뛰기), `prefers-reduced-motion` 존중 기능을 둔다.
- **크로스포스팅**: YouTube 등으로 내보낼 때 영상 첫머리의 경고 카드와 설명란 경고 문구를 자동으로 넣는 옵션을 둔다.
- WCAG는 웹 페이지 기준이지만, UGC 영상이 우리 페이지 안의 콘텐츠로 재생되므로 접근성 적합성 문제가 생긴다. 경고와 옵트인 재생으로 위험을 줄인다.

### Gaps
- ITU-R BT.1702 원문(최신 개정판 번호, 적색·패턴 기준 세부)을 조사하지 못했다.
- 일본 NHK·민방련 「アニメーション等の映像手法に関するガイドライン」 원문을 조사하지 못했다. 배경지식상 1997년 12월 ポケモン 사건 이후 1998년에 제정됐으나 미확인이다.
- Ofcom 방송 코드의 플래시 관련 규칙과 가이던스, 영국 Online Safety Act 2023의 이른바 "epilepsy trolling"(점멸 이미지 전송) 범죄 조항을 조사하지 못했다(배경지식, 미확인).
- PEAT(무료, Windows용으로 알려짐)와 Harding FPA(상용)의 라이선스·가격·현재 제공 상태를 확인하지 못했다. 그 밖의 도구도 조사하지 못했다.
- YouTube·니코니코 등의 점멸 경고 기능, OS 수준 기능(예: Apple "Dim Flashing Lights")을 조사하지 못했다.

---

## 8. 플랫폼 운영자 의무(한국·일본, 요약)

### Takeaway
한국에서는 자본금 1억원 이하 소규모 부가통신사업이면 신고가 면제되고, 자본금이 1억원을 넘으면 1개월 안에 신고해야 한다. 권리침해 정보는 정보통신망법 44조의2에 따라 삭제하거나 30일 이내 임시조치를 한다. 일평균 이용자 10만 명 이상 또는 매출 10억원 이상이면서 청소년유해매체물을 제공·매개하면 청소년보호책임자를 지정해야 한다. 개인정보는 안전성 확보조치 기준을 따른다. 일본에서는 회선 설비가 없는 웹·앱 서비스는 대체로 届出가 필요 없지만, 2023-06-16부터 동영상 배信 서비스 등에 外部送信規律(쿠키·SDK 송신 공표)이 적용되고, 대규모가 되면 情プラ法 지정 의무가 생긴다.

### Cited Findings
**한국**
- 부가통신사업 신고 면제: 전기통신사업법 22조 4항 1호의 "소규모 부가통신사업"은 인터넷으로 부가통신역무를 제공하는 자본금 1억원 이하 사업자다(시행령 30조). 신고가 면제된 사업자는 자본금이 1억원을 넘으면 그 사유가 생긴 날부터 1개월 안에 신고해야 한다 — [전기통신사업법 시행령 제30조(LBox)](https://lbox.kr/v2/statute/%EC%A0%84%EA%B8%B0%ED%86%B5%EC%8B%A0%EC%82%AC%EC%97%85%EB%B2%95%EC%8B%9C%ED%96%89%EB%A0%B9/%EB%B3%B8%EB%AC%B8%20%3E%20%EC%A0%9C2%EC%9E%A5%20%3E%20%EC%A0%9C30%EC%A1%B0?statuteName=%EC%A0%84%EA%B8%B0%ED%86%B5%EC%8B%A0%EC%82%AC%EC%97%85%EB%B2%95%20%EC%8B%9C%ED%96%89%EB%A0%B9&statuteType=%EB%8C%80%ED%86%B5%EB%A0%B9%EB%A0%B9&effectiveDate=2024-12-27&proclamationNumber=%EC%A0%9C%2035038%ED%98%B8&proclamationDate=2024-12-03&revisionType=%ED%83%80%EB%B2%95%EA%B0%9C%EC%A0%95), [방송통신사업 등록관리시스템(CRMS) 부가통신](https://www.crms.go.kr/lay1/S1T54C59/contents.do), [cafe24 도움말](https://support.cafe24.com/hc/ko/articles/8471135139097-%EB%B6%80%EA%B0%80-%ED%86%B5%EC%8B%A0%EC%82%AC%EC%97%85%EC%9E%90-%EC%8B%A0%EA%B3%A0%EB%8A%94-%EB%88%84%EA%B0%80-%EC%96%B4%EB%96%BB%EA%B2%8C-%ED%95%B4%EC%95%BC-%ED%95%98%EB%82%98%EC%9A%94)
- 정보통신망법 44조의2: 사생활 침해·명예훼손 등 권리를 침해당한 사람이 소명해 삭제나 반박 게재를 요청하면, 정보통신서비스 제공자는 지체 없이 삭제·임시조치 등을 하고 즉시 신청인과 게재자에게 알려야 한다. 권리 침해 여부를 판단하기 어렵거나 다툼이 예상되면 접근을 임시로 차단할 수 있으며 기간은 30일 이내다 — [찾기쉬운 생활법령 「정보의 삭제요청 및 임시조치」](https://easylaw.go.kr/CSP/CnpClsMain.laf?csmSeq=293&ccfNo=2&cciNo=1&cnpClsNo=1), [CaseNote 제44조의2](https://casenote.kr/%EB%B2%95%EB%A0%B9/%EC%A0%95%EB%B3%B4%ED%86%B5%EC%8B%A0%EB%A7%9D_%EC%9D%B4%EC%9A%A9%EC%B4%89%EC%A7%84_%EB%B0%8F_%EC%A0%95%EB%B3%B4%EB%B3%B4%ED%98%B8_%EB%93%B1%EC%97%90_%EA%B4%80%ED%95%9C_%EB%B2%95%EB%A5%A0/%EC%A0%9C44%EC%A1%B0%EC%9D%982). 30일 임시조치 조항은 합헌이다 — [법률신문](https://www.lawtimes.co.kr/news/166260), [헌재 결정례](https://www.law.go.kr/detcInfoP.do?mode=1&detcSeq=19778)
- 청소년보호책임자(정보통신망법 42조의3): 일평균 이용자 10만 명 이상이거나 매출액 10억원 이상인 정보통신서비스 제공자가 청소년유해매체물을 제공하거나 매개하면 지정해야 한다. 임원이나 관련 부서장 중에서 지정하며, 유해정보 차단·관리와 보호계획 수립을 맡는다 — [국가법령정보센터 조문](https://www.law.go.kr/LSW//lsLawLinkInfo.do?chrClsCd=010202&lsJoLnkSeq=1000924527&lsId=000030&print=print), [이데일리](https://edaily.co.kr/News/Read?mediaCodeNo=257&newsId=01846646619312896), [KCC-2024-19 연구보고서](https://www.kmcc.go.kr/download.do?fileSeq=61323). 2026년 현재 규제기관 사이트(kmcc.go.kr) 명칭은 "방송미디어통신위원회"이며, 텔레그램에 청소년보호책임자 지정을 요청한 보도자료가 있다 — [kmcc 보도자료](https://www.kmcc.go.kr/user.do?boardId=1113&boardSeq=63385&cp=1&dc=K05030000&mode=view&page=A05030000) (기관 개편 시점 미확인)
- 개인정보: 개인정보보호위원회 「개인정보의 안전성 확보조치 기준 안내서」는 소셜 로그인을 인증수단의 예로 들고, 암호화할 때 솔트값 추가 등을 고려하라고 한다 — [김앤장 뉴스레터](https://www.kimchang.com/ko/insights/detail.kc?sch_section=4&idx=30601)

**일본**
- 電気通信事業法: 회선 설비를 두지 않고 웹사이트·앱으로 제공하는 이른바 "第三号事業"은 일반적으로 등록·届出가 필요 없다. 개정법으로 일부(ドメイン名·検索情報·媒介相当 電気通信役務)는 届出 대상이 됐다 — [総務省 電気通信事業参入マニュアル［追補版］(2023-01-30 改定)](https://www.soumu.go.jp/main_content/000477428.pdf), [OneAsia「改正電気通信事業法の概要」](https://oneasia.legal/11241) (검색 요약 기준)
- 外部送信規律(2023-06-16 시행): 이용자 정보를 외부로 보낼 때(쿠키·태그·SDK) 보내는 내용과 송신처를 통지하거나 공표해야 한다. 동영상 배信 서비스 등 "이용자 이익에 큰 영향"을 주는 서비스는 第三号 사업자도 대상이다 — [森・濱田松本 外部送信規律ガイダンス 第2版(2024-04)](https://www.morihamada.com/sites/default/files/people/pdf/20240510-035859.pdf), [総務省 資料(2023-02)](https://www.soumu.go.jp/main_content/000862755.pdf), [賢誠総合法律事務所](https://k-itlegal.com/column/detail/%E6%94%B9%E6%AD%A3%E9%9B%BB%E6%B0%97%E9%80%9A%E4%BF%A1%E4%BA%8B%E6%A5%AD%E6%B3%952023%E5%B9%B46%E6%9C%8816%E6%97%A5%E6%96%BD%E8%A1%8C%E3%81%A7%E5%B0%8E%E5%85%A5%E3%81%95%E3%82%8C%E3%81%9F%E3%80%8C/), [水町雅子 判断フローチャート(2024.10改訂)](https://www.mizu-machi.com/wp-content/uploads/2025/05/230419gaibusoushinkiritsu.pdf)
- 情プラ法(대규모 지정 요건과 의무)은 4절 참조.

### Inferences
모두 추정.
- 한국 법인을 자본금 1억원 이하로 시작하면 부가통신사업 신고가 면제된다. 증자하면 1개월 안에 신고해야 한다는 점을 법무 체크리스트에 넣는다.
- 정보통신망법 44조의2 임시조치(명예훼손·사생활)와 저작권법 103조 중단 절차(저작권)를 하나의 신고 처리 시스템으로 묶되, 유형별로 처리 기한·통지 대상·재개 절차를 다르게 템플릿화한다.
- 성인 지향 콘텐츠를 허용하지 않는 정책으로 시작하면 청소년유해매체물 표시와 연령 확인 부담이 줄어든다. 일평균 이용자 10만 명에 가까워지기 전에 청소년보호책임자와 정책을 준비한다.
- YouTube 업로드용 OAuth 토큰(특히 refresh token)은 인증정보로 취급한다. 암호화해 저장하고, 최소 스코프만 받고, 연동을 해제하면 즉시 폐기하고, 접근을 기록한다. 법령에 토큰을 직접 다루는 조항은 확인하지 못했으며 일반 안전조치 기준을 준용하는 것으로 본다.
- 일본 사용자를 대상으로 분석 SDK나 광고 태그를 쓰면 外部送信 공표 페이지를 둔다. DM이나 비공개 메시지 기능을 넣으면 "타인의 통신 매개"로 届出가 필요해질 수 있으므로 넣기 전에 검토한다(매뉴얼 원문 세부는 미확인).

### Gaps
- 한국 영상물 등급분류(영비법)가 UGC에 적용되는지, 청소년유해매체물 표시(정보통신망법 42조)와 광고 제한(42조의2)의 세부를 조사하지 못했다.
- 부가통신사업 무신고 벌칙 수준은 검색 요약에 언급됐으나 조문으로 확인하지 못했다.
- 개인정보 국외 이전(해외 클라우드), 만 14세 미만 아동의 법정대리인 동의, 토큰을 개인정보로 볼지에 대한 감독기관 해석을 조사하지 못했다.
- 일본 個人情報保護法(APPI)의 의무, 青少年インターネット環境整備法, 届出 필요 여부(메시지 기능 유무에 따른 판단) 원문을 확인하지 못했다.
