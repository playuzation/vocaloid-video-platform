# 웹 기술 스택·라이브러리·라이선스 조사 — 브라우저 올인원 MV 스튜디오 + 서버 렌더링/트랜스코딩/스트리밍 백엔드 (기준일 2026-10-07)

> **조사 방법과 한계 (보고서 작성자 필독)**
> - WebSearch를 약 20회 수행한 시점에 "이번 턴의 공유 웹 검색 예산(200회, 동시에 실행 중인 모든 에이전트가 공유) 소진"으로 추가 검색이 차단되었다. WebFetch는 egress 정책으로 차단되었고(예: `www.live2d.com` → EGRESS_BLOCKED, 재시도하지 않음), GitHub 커넥터는 이 세션의 저장소로만 제한되어 외부 저장소의 LICENSE를 직접 읽지 못했다.
> - 보완책으로, 접근 가능한 1차 데이터를 직접 검증했다. (1) **npm 레지스트리**: `npm view`(license·version·time.modified·deprecated)와 `npm pack`으로 받은 tarball 안의 LICENSE/README/dist 코드. (2) **PyPI JSON API**와 wheel/sdist 안의 LICENSE·소스. (3) **MDN `@mdn/browser-compat-data` 8.1.4**(npm 게시 2026-10-01): MDN 호환성 표의 원천 데이터로, 브라우저 지원 버전의 근거로 썼다(이하 "BCD").
> - 표기: **[라이선스 플래그]** = 상업 SaaS에 문제 소지가 있음(AGPL/GPL, 좌석제·매출 기준, 비상업 모델 가중치, 워터마크 등). "미확인" = 출처를 확보하지 못함. "추정" = 근거로부터의 추론. "(검색 스니펫)" = 검색 결과 요약에만 근거하고 원문은 열람하지 못함.
> - 범위 밖(다른 조사자 담당): 가창 합성 엔진, MV 시각 트렌드, 외부 플랫폼 업로드 API, 커뮤니티/법무.
> - 매니지드 비디오 서비스 단가(§8)와 기업 엔지니어링 블로그 기반 레퍼런스 아키텍처(§9)는 검색 예산이 바닥나 대부분 미확인으로 남았다. 후속 검색이 필요하다.

**상업 SaaS 관점 라이선스 플래그 총괄** (상세 근거는 각 절의 Cited Findings)

| 대상 | 확인된 라이선스·조건 | SaaS 영향 (추정 포함) |
|---|---|---|
| `@ffmpeg/core`, `@ffmpeg/core-mt` (FFmpeg.wasm 코어) | GPL-2.0-or-later ([npm](https://www.npmjs.com/package/@ffmpeg/core)) | 브라우저로 내려보내면 "배포"에 해당해 GPL 의무가 생긴다(추정). 피하는 편이 낫다 |
| `ffmpeg-static` | GPL-3.0-or-later ([npm](https://www.npmjs.com/package/ffmpeg-static)) | 서버 내부 실행만 하면 통상 배포가 아니다(추정, 법무 확인 필요) |
| `web-demuxer` | 래퍼는 MIT이지만 `lib/`는 FFmpeg 파생 LGPL ([npm](https://www.npmjs.com/package/web-demuxer)) | LGPL 고지와 교체 가능성 등 의무 |
| `@theatre/studio` | AGPL-3.0-only ([npm](https://www.npmjs.com/package/@theatre/studio)) | 최종 사용자에게 studio UI를 제공하면 AGPL 소스 공개 의무(추정). `@theatre/core`는 Apache-2.0 |
| `etro` | GPL-3.0 ([npm](https://www.npmjs.com/package/etro)) | 클라이언트 번들에 넣으면 GPL |
| `essentia.js` / `essentia` | AGPL-3.0 / AGPL-3.0-only ([npm](https://www.npmjs.com/package/essentia.js), [PyPI](https://pypi.org/project/essentia/)) | 피한다 |
| aubio(`aubiojs`의 기반) | aubio는 GPLv3+ ([PyPI](https://pypi.org/project/aubio/)). aubiojs 래퍼 파일만 MIT | 결합 시 GPL(추정) |
| `madmom` | "BSD, CC BY-NC-SA", "Free for non-commercial use" ([PyPI](https://pypi.org/project/madmom/)) | 모델 가중치 상업 사용 불가 |
| `whisper-timestamped` | 메타데이터는 "GPLv3"인데 wheel에 동봉된 LICENSE는 **AGPL-3.0** ([PyPI](https://pypi.org/project/whisper-timestamped/)) | 피한다 |
| `ctc-forced-aligner` (PyPI판, Deskpai) | 수정분 DOSL-1.0, 기본 MMS 모델은 CC-BY-NC 4.0 ([PyPI](https://pypi.org/project/ctc-forced-aligner/)) | 기본 모델 상업 사용 불가 |
| `BeatNet` | CC BY 4.0 ([PyPI](https://pypi.org/project/BeatNet/)) | 저작자 표시 의무 |
| LYGIA 셰이더 라이브러리 | Prosperity License & Patron License ([npm](https://www.npmjs.com/package/lygia)) | 상업 사용에는 Patron(후원/기여) 라이선스가 필요 |
| Remotion | Remotion License. 직원 4명 이상 영리기업은 Company License, 에디터·자동화 용도는 렌더당 $0.01, 월 최소 $100 ([npm LICENSE](https://www.npmjs.com/package/remotion), [가격](https://www.remotion.dev/docs/license/pricing)) | 렌더 수에 비례하는 비용, 5.0에서 라이선스 변경 예고 |
| Diffusion Studio Core | MPL-2.0 + "Made with Diffusion Studio" 워터마크, 제거는 유료 키 ([npm](https://www.npmjs.com/package/@diffusionstudio/core)) | 유료 키 필요 |
| Spine Runtimes | Spine Editor 라이선스 보유가 전제. 그 외에는 "사용자마다 각자 Spine Editor 라이선스" ([npm LICENSE](https://www.npmjs.com/package/@esotericsoftware/spine-core)) | 사용자가 직접 리깅·저작하는 구조와 충돌 소지 |
| Live2D Cubism SDK | Cubism Core는 Live2D 독점 라이선스. 사용자 모델 임포트 앱은 "Expandable Application" 심사와 특별 계약 대상 ([Live2D](https://www.live2d.com/en/sdk/license/expandable/)) | 계약·심사·수수료 |
| npm `live2dcubismcore` | npm 표기는 ISC이지만 파일 헤더는 Live2D 독점 라이선스 ([npm](https://www.npmjs.com/package/live2dcubismcore)) | 표기를 믿으면 안 됨 |
| Rive | 런타임 MIT. 에디터 내보내기는 유료 좌석(Cadet $9/좌석/월~) ([Rive](https://rive.app/blog/rive-s-new-9-mo-plan)) | 사용자 저작 시 사용자 측 비용 |
| Mediabunny | MPL-2.0(파일 단위 약한 copyleft) ([npm](https://www.npmjs.com/package/mediabunny)) | 수정한 파일만 공개 의무. 상업 사용 문제없음 |

---

## 1. 브라우저 미디어 파이프라인: WebCodecs 지원, 코덱 가용성, MP4/WebM 먹싱, FFmpeg.wasm, 장시간·4K 렌더 한계, OPFS/File System Access

### Takeaway
2026-10 기준 WebCodecs의 Video·Audio Encoder/Decoder는 Chrome/Edge 94+, Firefox 130+(데스크톱), Safari 26+(macOS·iOS)에서 모두 제공된다. 그래서 "브라우저에서 인코딩해 MP4로 저장"하는 경로가 주요 데스크톱 브라우저 전부에서 성립한다. 예외는 Firefox for Android(미지원)와 Safari 16.4–18.x(비디오만 지원)이며, 코덱 단위 지원은 브라우저·OS·하드웨어마다 다르므로 런타임 탐지와 서버 렌더 폴백이 필수다. 먹싱은 MPL-2.0의 Mediabunny가 사실상 표준이다(mp4-muxer/webm-muxer는 2025-07에 Mediabunny로 대체되어 deprecated). FFmpeg.wasm은 코어가 GPL이고 네이티브보다 수 배 느려 보조 수단으로만 적합하다.

### Cited Findings

**WebCodecs 지원 버전 (BCD 8.1.4)**

| 인터페이스 | Chrome/Edge | Firefox | Safari | Chrome Android | Firefox Android | Safari iOS |
|---|---|---|---|---|---|---|
| `VideoEncoder` / `VideoDecoder` | 94 | 130 | 16.4 | 94 | 미지원 | 16.4 |
| `AudioEncoder` / `AudioDecoder` | 94 | 130 | **26** | 94 | 미지원 | **26** |
| `VideoFrame` | 94 | 130 | 16.4 | 94 | 130 | 16.4 |
| `ImageDecoder` | 94 | 133 | preview만 | 94 | 133 | 미지원 |
| `VideoEncoder.isConfigSupported()` | 94 | 130 | 16.4 | 94 | 미지원 | 16.4 |

- 위 표의 출처: [@mdn/browser-compat-data 8.1.4 (api.VideoEncoder / AudioEncoder / VideoFrame / ImageDecoder)](https://www.npmjs.com/package/@mdn/browser-compat-data)
- Safari 26.0이 WebCodecs에 `AudioEncoder`와 `AudioDecoder`를 추가했다 — [WebKit Features in Safari 26.0](https://webkit.org/blog/17333/webkit-features-in-safari-26-0/) (검색 스니펫)
- Safari 16.4–18.7은 비디오 인터페이스만 제공했고, 오디오까지 맞춘 완전한 지원은 Safari 26부터다. AAC 인코딩은 AudioEncoder를 지원하는 Safari 26+에서 지원된다 — [TestMu AI: WebCodecs Browser Support](https://www.testmuai.com/learning-hub/webcodecs-browser-support/) (2차 자료, 검색 스니펫)
- Safari 27(2026)에서 WebAssembly JSPI, `ReadableStream` 비동기 반복 등이 추가되었다. WebCodecs 관련 신규 항목은 BCD에서 찾지 못했다 — [BCD 8.1.4](https://www.npmjs.com/package/@mdn/browser-compat-data)

**코덱 가용성**
- Chrome은 M130에서 WebCodecs HEVC 인코딩을 출시했으나 하드웨어 지원이 있어야 동작한다 — [blink-dev Intent to Ship: H26x Codec support updates](https://groups.google.com/a/chromium.org/g/blink-dev/c/YJ1QijNiHeM/m/3EhMjMVwAgAJ) (검색 스니펫)
- HEVC Main 인코딩 상한은 macOS 4096×2304·120fps, Windows 1920×1088·30fps다. 같은 문서가 `--enable-features=PlatformHEVCEncoderSupport` 플래그가 필요하다고도 적어, "M130 정식 출시"와 **상충**한다(작성 시점 차이로 추정) — [StaZhu/enable-chromium-hevc-hardware-decoding](https://fr.github.com/StaZhu/enable-chromium-hevc-hardware-decoding) (검색 스니펫)
- HEVC 코덱 문자열은 `hev1.` 또는 `hvc1.` 접두사에 점으로 구분된 4개 필드를 붙인다 — [W3C HEVC (H.265) WebCodecs Registration](https://www.w3.org/TR/webcodecs-hevc-codec-registration/)
- AV1 인코딩은 데스크톱 브라우저에서는 강하지만 Safari와 Android에서는 제한적이라는 2차 자료가 있다. 같은 자료는 Firefox 130 데스크톱이 H.264/H.265/AV1/VP8/VP9 및 AAC/Opus/FLAC를 처리한다고 주장하나 1차 확인은 못 했다 — [TestMu AI](https://www.testmuai.com/learning-hub/webcodecs-browser-support/) (신뢰도 낮음)
- Firefox 130에서 H.264 프레임 디코딩이 "not supported"로 실패한 버그 보고가 있었다 — [Bugzilla 1918769](https://bugzilla.mozilla.org/show_bug.cgi?id=1918769)
- Mediabunny는 브라우저에 없는 코덱을 채우는 WASM 인코더 확장을 따로 배포한다. `@mediabunny/aac-encoder`(libavcodec 기반, 첫 게시 2026-03-04), `@mediabunny/mp3-encoder`(LAME 기반, 2025-08-10), `@mediabunny/flac-encoder`(libFLAC 기반, 2026-03-04), `@mediabunny/ac3`(AC-3/E-AC-3, 2026-02-12)이며 모두 MPL-2.0, v1.61.3이다 — [npm @mediabunny/aac-encoder](https://www.npmjs.com/package/@mediabunny/aac-encoder), [npm @mediabunny/mp3-encoder](https://www.npmjs.com/package/@mediabunny/mp3-encoder)

**MP4/WebM 먹싱·미디어 툴킷**
- **Mediabunny**: MPL-2.0, 최신 1.61.3(2026-10-05), 1.0.0(2025-07-02) → 1.20.0(2025-09-25) → 1.40.0(2026-03-19) → 1.61.3(2026-10-05)으로 빠르게 릴리스 중이다 — [npm mediabunny](https://www.npmjs.com/package/mediabunny)
- Mediabunny README에 따르면 MP4, MOV, WebM, MKV, HLS, WAVE, MP3, Ogg, ADTS, FLAC, MPEG-TS를 읽고 쓰며, 25종 이상의 코덱을 WebCodecs로 하드웨어 가속한다. 임의 크기 파일을 스트리밍 I/O로 처리하고, 트리셰이킹 시 최소 5kB(gzip)이며, `@mediabunny/server`로 Node·Bun·Deno에서도 동작한다 — [npm mediabunny README](https://www.npmjs.com/package/mediabunny), [mediabunny.dev](https://mediabunny.dev/guide/introduction)
- `@mediabunny/server`는 "NodeAV 기반으로 서버 환경에 비디오·오디오 디코더/인코더를 추가"하며 2026-05-13에 처음 게시되었다 — [npm @mediabunny/server](https://www.npmjs.com/package/@mediabunny/server)
- MPL-2.0 의무: 상업·비공개 프로젝트에서 자유롭게 쓸 수 있다. 다만 Mediabunny 소스 파일을 수정해 배포하면 그 수정분을 MPL-2.0으로 공개해야 하며, 라이선스 헤더 제거와 상표 사용은 금지된다 — [npm mediabunny README §License](https://www.npmjs.com/package/mediabunny)
- 후원사(README): Gold는 Remotion, Gling AI, Diffusion Studio, Kino, Screen Studio, Tella. Bronze는 ElevenLabs, React Video Editor — [npm mediabunny README](https://www.npmjs.com/package/mediabunny)
- `mp4-muxer` 5.2.2와 `webm-muxer` 5.1.4(MIT, 2025-07-02 수정)는 npm에서 "This library is superseded by Mediabunny. Please migrate to it."로 deprecated 처리되었다 — [npm mp4-muxer](https://www.npmjs.com/package/mp4-muxer), [npm webm-muxer](https://www.npmjs.com/package/webm-muxer), [MIGRATION-GUIDE](https://raw.githubusercontent.com/Vanilagy/mp4-muxer/HEAD/MIGRATION-GUIDE.md)
- Mediabunny의 MP4 먹서는 mp4-muxer에서 출발해 다중 트랙, 더 많은 코덱, .mov, 자막 트랙, 파이프라이닝·백프레셔를 지원하도록 확장되었다 — [Korben: MediaBunny](https://korben.info/en/mediabunny-video-processing-browser.html) (검색 스니펫)
- 업계에서 다루는 주제다: SVTA 컨퍼런스 세션 "Performant and accessible client side media processing with Mediabunny" — [SVTA University](https://university.svta.org/conference-proceedin/performant-and-accessible-client-side-media-processing-with-mediabunny/)
- 기타: `mp4box` 2.4.1은 BSD-3-Clause(2026-06-19)다 — [npm mp4box](https://www.npmjs.com/package/mp4box). `web-demuxer` 4.0.0은 래퍼가 MIT이나 README에 "The `lib/` directory contains FFmpeg-derived code under the LGPL License"라고 적혀 있다 **[라이선스 플래그: LGPL]** — [npm web-demuxer](https://www.npmjs.com/package/web-demuxer). `@remotion/webcodecs`와 `@remotion/media-parser`는 Remotion License다 — [npm @remotion/webcodecs](https://www.npmjs.com/package/@remotion/webcodecs)

**FFmpeg.wasm**
- `@ffmpeg/ffmpeg` 0.12.15(래퍼)는 MIT이지만 `@ffmpeg/core`와 `@ffmpeg/core-mt` 0.12.10은 **GPL-2.0-or-later**이며 마지막 갱신은 2025-04다 **[라이선스 플래그]** — [npm @ffmpeg/core](https://www.npmjs.com/package/@ffmpeg/core), [npm @ffmpeg/ffmpeg](https://www.npmjs.com/package/@ffmpeg/ffmpeg)
- 성능 예: 100MB WAV를 MP3로 변환할 때 M1 Mac 네이티브 FFmpeg는 약 0.8초, 브라우저 WASM은 약 4.5초가 걸렸다 — [DEV: Moving FFmpeg to the browser](https://dev.to/baojian_yuan/moving-ffmpeg-to-the-browser-how-i-saved-100-on-server-costs-using-webassembly-4l9f)
- ffmpeg.wasm 문서는 멀티스레드 코어(core-mt)를 써도 네이티브보다 상당히 느리다고 안내한다 — [ffmpeg.wasm docs](https://docsearch.algolia.com/mcp/docs/repo/ffmpegwasm/ffmpeg.wasm) (검색 스니펫)
- 멀티스레드 WASM에는 SharedArrayBuffer가 필요하고, 이를 쓰려면 COOP/COEP 헤더로 cross-origin isolation을 켜야 한다 — [web.dev: WebAssembly threads](https://web.dev/articles/webassembly-threads?authuser=0)
- ffmpeg.wasm은 파일을 MEMFS(메모리)에 올리므로 2GB를 넘는 파일은 기기 RAM에 따라 탭이 죽거나 할당에 실패할 수 있다 — [DEV](https://dev.to/baojian_yuan/moving-ffmpeg-to-the-browser-how-i-saved-100-on-server-costs-using-webassembly-4l9f)
- WASM memory64는 Chrome 133, Firefox 134이며 Safari는 preview뿐이다(iOS 미지원). threads·atomics는 Chrome 74, Firefox 79, Safari 15.2다 — [BCD 8.1.4 webassembly.memory64 / threads-and-atomics](https://www.npmjs.com/package/@mdn/browser-compat-data)

**대용량 처리, OPFS, File System Access**
- 저장소 쿼터: Chrome은 브라우저 전체가 디스크의 80%, 오리진 하나가 60%까지 쓸 수 있고 시크릿 모드는 약 5%다. Firefox는 여유 공간의 50%, eTLD+1 그룹당 2GB다. Safari 17은 1GB 한도를 없애고 디스크 총량 기반으로 바꿨다 — [web.dev: Storage for the web](https://web.dev/storage-for-the-web?hl=ja) (검색 스니펫. 문서가 오래되어 Firefox 수치가 낡았을 수 있음)
- `createSyncAccessHandle()`은 Web Worker에서만 쓸 수 있고, OPFS도 다른 오리진 스토리지와 같은 쿼터를 적용받는다(`navigator.storage.estimate()`로 확인) — [MDN: Origin private file system](https://developer.mozilla.org/en-US/docs/Web/API/File_System_API/Origin_private_file_system)
- BCD 기준: `FileSystemSyncAccessHandle`은 Chrome 102 / Firefox 111 / Safari 15.2. `FileSystemFileHandle.createWritable`과 `FileSystemWritableFileStream`은 Chrome 86 / Firefox 111 / **Safari 26**. `showSaveFilePicker`와 `showDirectoryPicker`는 **Chromium 계열만**(Chrome/Edge 86, Chrome Android 132) 지원하고 실험 기능으로 표시된다 — [BCD 8.1.4](https://www.npmjs.com/package/@mdn/browser-compat-data)
- `OffscreenCanvas`는 Chrome 69 / Firefox 105 / Safari 16.4다 — [BCD 8.1.4](https://www.npmjs.com/package/@mdn/browser-compat-data)

### Inferences
- 기본 내보내기 프로필(추정): YouTube/SNS 호환성을 고려해 MP4 + H.264(avc1) + AAC-LC로 한다. AV1/VP9는 옵션, HEVC는 하드웨어가 있을 때만 켜는 선택 경로로 둔다. AAC 인코더가 없는 환경에서는 Opus(WebM)를 쓰거나 `@mediabunny/aac-encoder`(WASM)로 대체한다.
- 지원 판정은 브라우저 이름이 아니라 `VideoEncoder/AudioEncoder.isConfigSupported()`로 해상도·프레임레이트·코덱별로 런타임에 탐지한다. 실패하면 서버 렌더로 넘긴다(대상: Firefox Android, Safari 26 미만의 오디오, HEVC 미지원 기기 등).
- 메모리 산술(추정): 4K RGBA 프레임 1장은 3840×2160×4 ≈ 33.2MB이고, 4K60은 원시 프레임 기준 초당 약 2GB, 1080p 프레임은 약 8.3MB다. 따라서 원시 프레임을 쌓아 두면 안 된다. `encodeQueueSize`로 백프레셔를 걸고 `VideoFrame.close()`를 철저히 호출하며, 인코딩된 청크는 Mediabunny의 스트리밍 타깃으로 OPFS에 바로 기록해야 한다. 4분 MV를 1080p30으로 만들면 7,200프레임, 4K60이면 14,400프레임이다.
- wasm32의 주소 공간 한계(최대 4GiB)와 Safari의 memory64 미지원 때문에 FFmpeg.wasm·MEMFS 기반 처리는 장시간·4K 작업에 맞지 않는다(추정). WebCodecs(하드웨어)와 Mediabunny(스트리밍) 조합을 기본 경로로 삼는다.
- GPL(추정): `@ffmpeg/core`를 브라우저에 전송하면 사용자에게 배포하는 것이므로 GPL 의무가 그 구성요소에 붙는다. 같은 번들에서 결합되면 파생저작물 논쟁도 생길 수 있어 기본 경로에서 빼는 편이 안전하다. Mediabunny WASM 확장(libavcodec·LAME 내장)의 내장 라이브러리 라이선스는 별도로 확인해야 한다.
- cross-origin isolation(COOP/COEP)은 ffmpeg.wasm MT, ONNX Runtime Web 스레드(§7 demucs-web), Diffusion Studio(§3, COEP `credentialless`)에 공통으로 필요하다. 외부 이미지·임베드에 영향을 주므로 앱 셸 설계 초기에 헤더 정책을 정해야 한다(추정).
- 대용량 저장(추정): 렌더 결과는 OPFS에 쓰고 업로드는 청크(멀티파트) 단위로 한다. "사용자 디스크에 직접 저장"은 Chromium에서만 `showSaveFilePicker`로 가능하고 Firefox/Safari는 Blob 다운로드로 처리해야 한다.

### Gaps
- 브라우저×OS×코덱별 **인코딩** 지원 매트릭스는 1차 출처로 확인하지 못했다(예: Firefox AudioEncoder의 AAC 인코딩 여부, Safari의 AV1/VP9 인코딩, Chrome HEVC 인코딩의 Linux 지원). `@mediabunny/aac-encoder`가 있다는 사실은 일부 브라우저에 AAC 인코더가 없다는 방증이지만(추정), 어느 브라우저인지는 미확인이다.
- 탭/렌더러 프로세스별 메모리 상한(데스크톱 Chrome, iOS Safari)은 미확인이다.
- 백그라운드 탭에서 렌더가 스로틀링되는지, 그 영향은 어떤지 미확인이다.
- Mediabunny WASM 인코더 확장에 들어간 libavcodec 빌드 구성(LGPL/GPL)과 AAC·HEVC 특허 풀 문제는 미확인이며 법무 범위다.

---

## 2. 렌더링: WebGL2 vs WebGPU 출시 현황, PixiJS v8·pixi-filters·Three.js·Babylon.js·CanvasKit, 트랜지션/이펙트 셰이더 라이선스, 결정적(frame-accurate) 출력

### Takeaway
WebGPU는 2026년 현재 4대 엔진이 모두 기본 활성화했지만 플랫폼 공백이 크다. Firefox는 Windows와 Apple Silicon macOS만 지원하고 Linux·Android·Intel Mac은 미지원이며, Chrome Linux는 일부 GPU만 지원하고, Firefox Android는 미지원이다. 따라서 WebGL2 폴백은 2026년에도 필수다. 2D 합성에는 WebGPU와 WebGL2를 한 API로 다루는 PixiJS v8(MIT)이 가장 직접적이고, 3D에는 three.js(MIT, r186에 WebGPURenderer/TSL 포함)와 Babylon.js(Apache-2.0)가 적합하다. 모두 상업 사용에 문제가 없다. 셰이더 컬렉션 중 gl-transitions는 안전하지만(125개 중 123개 MIT) LYGIA는 상업 라이선스가 필요하다.

### Cited Findings

**WebGPU / WebGL2 출시 현황**

| 엔진 | WebGPU 기본 활성 (BCD 8.1.4 `api.GPU`) |
|---|---|
| Chrome/Edge | 113(ChromeOS·macOS·Windows), 144부터 Linux(Intel Gen12+ GPU만), Chrome Android 121 |
| Firefox | 141(Windows). macOS Tahoe + Apple Silicon은 145부터, 이전 macOS의 Apple Silicon은 147부터. **Intel Mac 미지원(bug 2004105), Linux 미지원(bug 2006676)**, service worker 컨텍스트 제외, Firefox Android 미지원 |
| Safari | 26(macOS Tahoe 26), iOS/iPadOS 26 |

- 위 표의 출처: [BCD 8.1.4 api.GPU / GPUCanvasContext](https://www.npmjs.com/package/@mdn/browser-compat-data)
- Chrome 144 Beta는 Linux Intel Gen12+를 지원하고, Wayland의 NVIDIA 지원은 Chrome 147이 목표이며, 그 밖의 GPU는 커맨드라인 플래그가 필요하다 — [gpuweb Implementation Status](https://github.com/gpuweb/gpuweb/wiki/Implementation-Status) (검색 스니펫)
- Firefox: Windows는 v141(2025-07), macOS Tahoe 26 ARM64는 v145(2025-11-11)부터 안정 지원. Linux·Android·Intel Mac은 "2026년 중 추적"이다 — [Utsubo: Frontier Web APIs 2026](https://www.utsubo.com/blog/frontier-web-apis-2026-production-ready) (검색 스니펫). 반면 BCD(2026-10-01 게시)에는 여전히 Linux 미지원으로 기록되어 있어 **아직 출시되지 않은 것으로 판단**된다.
- "모든 주요 브라우저가 WebGPU를 출시"했다는 보도 — [WebGPU.com News](https://www.webgpu.com/news/webgpu-hits-critical-mass-all-major-browsers/)
- Safari 26은 macOS Tahoe 26, iOS 26, iPadOS 26, visionOS 26에서 WebGPU를 기본 활성화하며, Apple 플랫폼에서 "WebGL을 대체하는" 선호 API로 안내한다 — [WebKit: News from WWDC25](https://webkit.org/blog/16993/news-from-wwdc25-web-technology-coming-this-fall-in-safari-26-beta/), [Cinevva (2025-09-15)](https://app.cinevva.com/news/2025-09-15-safari-webgpu) (검색 스니펫)
- Safari 26.2에서 `texture-formats-tier1` 기능이, Safari 27에서 `maxStorageBuffersInFragmentStage` 등 GPUSupportedLimits 항목이 추가되었다 — [BCD 8.1.4](https://www.npmjs.com/package/@mdn/browser-compat-data)
- WebGL2는 Chrome 56, Edge 79, Firefox 51, Safari 15부터 지원해 사실상 보편적이다 — [BCD 8.1.4 api.WebGL2RenderingContext](https://www.npmjs.com/package/@mdn/browser-compat-data)

**렌더링 라이브러리 (npm, 2026-10-07 조회)**
- `pixi.js` 8.22.0은 MIT(2026-10-01), v8.0.0은 2024-03-05에 출시되었다 — [npm pixi.js](https://www.npmjs.com/package/pixi.js)
- PixiJS v8은 WebGPU를 "애드온이 아닌 핵심 패러다임"으로 통합했다. Bunnymark에서 스프라이트 10만 개 기준 CPU 약 15ms(v7은 약 50ms), GPU 약 2ms(v7은 약 9ms)다 — [PixiJS blog: v8 launches](https://pixijs.com/blog/pixi-v8-launches) (검색 스니펫)
- PixiJS v8은 WebGL2, WebGPU, 실험적 Canvas2D 폴백을 지원하고 필터는 GLSL/WGSL 커스텀 셰이더로 작성할 수 있다. 필터·마스크·블렌드 모드처럼 배치가 자주 끊기는 장면에서는 WebGPU가 유리할 수 있으나, CPU 병목 때문에 항상 빠르지는 않다 — [Abratabia: PixiJS](https://abratabia.com/pixijs/) (검색 스니펫)
- `pixi-filters` 6.1.5는 MIT(2025-11-29)다 — [npm pixi-filters](https://www.npmjs.com/package/pixi-filters)
- `three` 0.186.1은 MIT(2026-09-24)이며, 패키지에 `build/three.webgpu.js`, `build/three.webgpu.nodes.js`, `build/three.tsl.js`가 들어 있다(tarball 확인) — [npm three](https://www.npmjs.com/package/three)
- `@babylonjs/core` 9.29.0은 Apache-2.0(2026-10-01)이다 — [npm @babylonjs/core](https://www.npmjs.com/package/@babylonjs/core)
- `canvaskit-wasm` 0.42.0은 BSD-3-Clause(2026-08-18)다 — [npm canvaskit-wasm](https://www.npmjs.com/package/canvaskit-wasm)
- `postprocessing`(pmndrs) 6.39.5는 Zlib, `@react-three/fiber` 9.8.1은 MIT다 — [npm postprocessing](https://www.npmjs.com/package/postprocessing), [npm @react-three/fiber](https://www.npmjs.com/package/@react-three/fiber)

**트랜지션/이펙트 셰이더 컬렉션**
- `gl-transitions` 1.71.0은 MIT(2026-06-22)다. 패키지 데이터의 트랜지션별 `license` 필드를 집계하면 **125개 중 MIT 123, BSD 3 Clause 1, BSD 2 Clause 1**이다 — [npm gl-transitions](https://www.npmjs.com/package/gl-transitions)
- LYGIA 1.4.1의 npm license 필드는 "Prosperity License & Patron License"다. 후원자와 기여자는 Patron License로 Prosperity의 비상업 조건을 면제받고, 특정 버전용 영구 상업 라이선스도 구매할 수 있다. 색상 mixbox 함수는 Secret Weapons의 별도 비상업 라이선스다 **[라이선스 플래그]** — [npm lygia](https://www.npmjs.com/package/lygia), [Socket: lygia](https://socket.dev/npm/package/lygia) (검색 스니펫)
- `interactive-shader-format`(ISF 렌더러) 2.8.1은 ISC지만 2022년 이후 갱신이 없다 — [npm interactive-shader-format](https://www.npmjs.com/package/interactive-shader-format)

**결정적 출력 관련 API**
- `HTMLVideoElement.requestVideoFrameCallback`은 Chrome 83, Firefox 132, Safari 15.4다 — [BCD 8.1.4](https://www.npmjs.com/package/@mdn/browser-compat-data)
- Remotion의 브라우저 렌더러는 지원하는 HTML 요소를 일부로 제한한다. 미리보기용 DOM과 내보내기 렌더 경로가 다르면 결과가 어긋날 수 있음을 보여 주는 사례다 — [Remotion: Client-side rendering](https://remotion.dev/docs/client-side-rendering/) (검색 스니펫)

### Inferences
- 렌더 백엔드 전략(추정): PixiJS v8을 2D 합성 코어로 쓰고 WebGPU를 우선 시도하되 WebGL2로 폴백한다. 셰이더는 WebGPU와 WebGL2 양쪽으로 컴파일 가능한 형태로 작성한다(PixiJS의 GLSL/WGSL 이중 작성, 3D 레이어는 three의 TSL). "미리보기 == 내보내기"를 보장하려면 **같은 기기·같은 백엔드·같은 코드 경로**로 내보내야 한다. WebGPU와 WebGL2, 또는 GPU 벤더 사이의 픽셀 단위 일치는 보장할 수 없다(정밀도, sRGB, MSAA 차이).
- 프레임 정확성 설계(추정):
  1. 시간은 벽시계가 아니라 유리수 시간(프레임 인덱스/fps)으로 다룬다(OTIO의 RationalTime 개념과 같음, §3).
  2. 모든 시각 요소는 "프레임 번호의 순수 함수"로 계산한다.
  3. 물리(스프링 본 등)는 고정 스텝과 시드 고정 RNG로 돌린다.
  4. 소스 영상은 `<video>.currentTime` 탐색 대신 WebCodecs `VideoDecoder`로 정확한 프레임을 얻는다. 미리보기 동기화에는 `requestVideoFrameCallback`을 보조로 쓴다.
  5. 폰트는 렌더 전에 `document.fonts.load()`로 모두 로드한다(§6).
  6. 오디오 믹스다운은 `OfflineAudioContext`로 결정적으로 렌더한다(§7).
- 서버 렌더도 같은 웹 엔진을 headless Chromium에서 돌리면 브라우저 간 차이를 줄일 수 있다(§8, 추정).
- 셰이더 출처 관리: gl-transitions는 트랜지션마다 author/license 메타데이터가 있어 플러그인 매니페스트에 그대로 옮길 수 있다(추정). Shadertoy 등 출처가 불명확한 코드는 반입을 금지하는 정책이 필요하다(아래 Gaps).

### Gaps
- Shadertoy의 기본 라이선스(통상 CC BY-NC-SA 3.0으로 알려짐)는 **미확인**이다. 검색 예산 소진으로 확인하지 못했다.
- Chrome Linux(전체 GPU)와 Firefox Linux/Android의 WebGPU 출시 예정일은 미확인이다.
- PixiJS v8 WebGPU 렌더러의 필터 동등성과 버그 현황, 1080p/4K 다층 필터 체인의 실측 성능은 확보한 자료가 없다.
- CanvasKit의 번들 크기(WASM 수 MB로 알려짐)는 미확인이다.

---

## 3. 상업 SaaS에 임베드 가능한 비디오 에디터 프레임워크/엔진과 타임라인 교환 포맷(OpenTimelineIO)

### Takeaway
"사용자가 편집하고 렌더하는" SaaS에서 Remotion을 쓰려면, 직원이 4명 이상일 경우 Company License의 "Automators" 모델(렌더당 $0.01, 월 최소 $100)이 적용된다. 브라우저 렌더러(`@remotion/web-renderer`)는 아직 실험적 알파다. 대안 중 Motion Canvas(MIT)는 2025-02 이후 npm 게시가 멈췄고, Revideo(MIT)는 2인 팀이다. Theatre.js는 studio가 AGPL이고 2024-05 이후 공개 릴리스가 없다. Etro는 GPL-3.0, Diffusion Studio Core는 MPL-2.0이지만 워터마크 제거가 유료다. OpenCut은 MIT이며 2026년에 재작성 중이다. 결론적으로 Mediabunny와 PixiJS/three 위에 자체 타임라인·합성기를 구축하고, OTIO(Apache-2.0)는 교환용 어댑터로 쓰는 구성이 라이선스 리스크가 가장 낮다(추정).

### Cited Findings

**Remotion**
- `remotion` 4.0.534의 LICENSE.md 원문 요지: 무료 대상은 개인, **직원 3명 이하** 영리기업, 비영리, 평가 목적(아직 상업적으로 쓰지 않는 경우)이다. 그 밖의 영리 조직은 Company License가 필요하다. Remotion을 복제·수정해 자체 파생물을 판매·임대·재라이선스하는 것은 금지된다. 또 "Remotion 5.0에서 라이선스가 약간 바뀐다"(PR #3750)고 예고한다 — [npm remotion (LICENSE.md)](https://www.npmjs.com/package/remotion)
- 가격: "Creators"는 좌석당 월 $25(최소 없음, 자동화 없이 자체 영상 제작). "Automators"는 **렌더당 $0.01, 월 최소 $100**이며 "video editors, prompt-to-video tools, automated video pipelines, embedding the Remotion Player" 같은 용도가 해당한다. 둘을 함께 쓰면 합산 월 최소 $100, Enterprise는 월 최소 $500이다 — [Remotion: License & Pricing](https://www.remotion.dev/docs/license/pricing), [remotion.pro/license](https://www.remotion.pro/license) (검색 스니펫)
- v5.0 이용약관 페이지가 이미 있다 — [Remotion Terms (v5.0)](https://www.remotion.dev/docs/terms). remotion.pro는 라이선스 대시보드(좌석·렌더·청구·텔레메트리)와 Editor Starter 등을 파는 스토어를 운영한다 — [Remotion Pro](https://companies.remotion.dev/) (검색 스니펫)
- `@remotion/web-renderer`는 실험적 알파다. FFmpeg 대신 WebCodecs와 Mediabunny로 인코딩하고, 일부 HTML 요소만 지원하며, 번들링이 필요 없다. 지원 브라우저는 Chrome 94+, Firefox 130+, Safari 26+이고 API는 `renderMediaOnWeb()`/`renderStillOnWeb()`이다 — [Remotion: Client-side rendering](https://remotion.dev/docs/client-side-rendering/), [renderMediaOnWeb](https://www.remotion.dev/docs/web-renderer/render-media-on-web) (검색 스니펫)
- `@remotion/web-renderer` 4.0.534의 package.json은 `mediabunny` 1.56.1에 의존한다 — [npm @remotion/web-renderer](https://www.npmjs.com/package/@remotion/web-renderer)
- `@remotion/renderer` 4.0.534의 dist에 정의된 OpenGL 렌더러 옵션은 `swangle`, `angle`, `egl`, `swiftshader`, `vulkan`, `angle-egl`이다 — [npm @remotion/renderer](https://www.npmjs.com/package/@remotion/renderer)
- `@remotion/lambda`와 `@remotion/renderer`는 "SEE LICENSE IN LICENSE.md"이고 Lambda 함수 zip(`remotionlambda-arm64.zip`)을 포함한다. `@remotion/lambda-client`와 `@remotion/serverless`는 npm 메타데이터상 "UNLICENSED"로 표기되어 있다 — [npm @remotion/lambda](https://www.npmjs.com/package/@remotion/lambda), [npm @remotion/lambda-client](https://www.npmjs.com/package/@remotion/lambda-client)

**Motion Canvas / Revideo / Theatre.js / Etro**
- `@motion-canvas/core` 3.17.2는 MIT이고 npm 마지막 수정은 **2025-02-16**이다 — [npm @motion-canvas/core](https://www.npmjs.com/package/@motion-canvas/core)
- `@revideo/core` 0.11.0은 MIT(2026-07-10)다 — [npm @revideo/core](https://www.npmjs.com/package/@revideo/core). Revideo는 Motion Canvas를 포크한 프로그래매틱 영상 프레임워크로, TypeScript 템플릿, 렌더 API, MP4 내보내기 전 미리보기용 플레이어 컴포넌트를 제공한다 — [docs.re.video](https://docs.re.video/), [YC Launch](https://www.ycombinator.com/launches/Kq1-revideo-create-videos-with-code). YC S23, 베를린, 팀 2명이며 창업자는 Midrender도 운영한다 — [YC: Revideo](https://www.ycombinator.com/companies/revideo), [YC: Midrender](https://www.ycombinator.com/companies/midrender) (검색 스니펫)
- Theatre.js: `@theatre/core` 0.7.2는 Apache-2.0, `@theatre/studio` 0.7.2는 **AGPL-3.0-only**(tarball LICENSE 확인)이며 둘 다 2024-05-19 이후 새 버전이 없다 **[라이선스 플래그]** — [npm @theatre/core](https://www.npmjs.com/package/@theatre/core), [npm @theatre/studio](https://www.npmjs.com/package/@theatre/studio)
- studio는 디자인·개발 단계에만 쓰고 최종 번들에는 core만 포함하므로 Apache만 적용된다는 설명이 있다 — [npm @theatre/studio](https://npmjs.com/package/@theatre/studio) (검색 스니펫). 개발은 빠른 반복을 위해 "일시적으로 비공개 저장소로 이동"했다고 한다 — [Theatre README 사본](https://github.com/gitforkedio/theatre) (검색 스니펫)
- `etro` 0.14.1은 **GPL-3.0**(2026-08-12)이다 **[라이선스 플래그]** — [npm etro](https://www.npmjs.com/package/etro)

**오픈소스·상용 웹 에디터/엔진 (2025–26)**
- OpenCut은 MIT다. opencut.app은 아직 Next.js 기반 "classic" 에디터로 돌아가고, GitHub 기본 브랜치는 plugin-first 아키텍처, GPU 합성·효과·마스크를 담당하는 공유 Rust 코어, GPUI 데스크톱 앱, MCP/headless API 계획을 담은 전면 재작성판이다 — [mer.vin (2026-07)](https://mer.vin/2026/07/opencut-explained-open-source-capcut-alternative-classic-editor-vs-ground-up-rewrite/). GitHub 스타는 45,000개 이상이다 — [The Menon Lab](https://themenonlab.blog/blog/opencut-open-source-capcut-alternative-video-editor) (검색 스니펫)
- Diffusion Studio Core 4.0.3은 MPL-2.0(2025-11-30)이다. README에 따르면 "Made with Diffusion Studio" 워터마크를 유지하면 무료이고, 제거하려면 일회성 유료 라이선스 키(오프라인 서명 검증, 타 조직과 공유 금지)가 필요하다. COOP/COEP(COEP `credentialless`)가 필요하고 Mediabunny 위에 구축되었으며, 서버 렌더가 필요하면 Remotion을 쓰라고 권한다 **[라이선스 플래그: 워터마크/유료 키]** — [npm @diffusionstudio/core](https://www.npmjs.com/package/@diffusionstudio/core)
- WebAV(`@webav/av-cliper` 1.2.8, LICENSE는 MIT, 2026-01-10)는 WebCodecs 기반으로 비디오·오디오·이미지·텍스트를 애니메이션과 함께 합성하며, `Combinator` 출력은 현재 MP4 바이너리 스트림만 지원한다 — [npm @webav/av-cliper](https://www.npmjs.com/package/@webav/av-cliper)

**OpenTimelineIO**
- 공식 OpenTimelineIO(Python/C++)는 0.18.1, Apache 2.0(PyPI 업로드 2025-11-09)이다 — [PyPI OpenTimelineIO](https://pypi.org/project/OpenTimelineIO/)
- npm `opentimelineio` 0.1.0(2025-10-19)은 공식 바인딩이 아니라 제3자 순수 TS 구현인 "otio.js"(fifteen42, Apache-2.0)다. Timeline/Track/Clip/Transition/Marker 스키마, RationalTime/TimeRange/TimeTransform, OTIO JSON 읽기·쓰기를 지원하고 의존성이 없다 — [npm opentimelineio](https://www.npmjs.com/package/opentimelineio)

### Inferences
- Remotion 비용 산술(추정): Automators 기준 월 1만 렌더면 $100(최소액), 10만이면 $1,000, 100만이면 $10,000에 AWS Lambda 인프라비가 더해진다. 커뮤니티형 MV 플랫폼은 렌더(미리보기용 썸네일 렌더 포함 여부에 따라) 수가 사용자 수에 비례하므로 단가 구조가 불리할 수 있다. 사용자 브라우저에서 `@remotion/web-renderer`로 렌더한 것도 과금 대상인지는 미확인이다(Player 임베드가 Automators 정의에 포함되므로 과금 대상일 가능성이 높다고 추정). 5.0에서 라이선스가 바뀐다고 예고되어 있어 종속 리스크도 있다.
- 권장(추정): 미디어 I/O는 Mediabunny(MPL-2.0), 렌더링은 PixiJS v8/three(MIT)로 하고 타임라인·키프레임·합성 그래프는 자체 구현한다. Remotion/Revideo/Motion Canvas에서 "프레임 = 시간의 순수 함수" 설계는 차용하되 의존하지는 않는다. Theatre.js는 core(Apache-2.0)만 키프레임 시퀀서 후보로 쓸 수 있고, 사용자에게 노출하는 편집 UI로 studio(AGPL)를 쓰는 것은 비공개 SaaS에서 불가하다고 봐야 한다.
- OTIO 활용(추정): 내부 모델은 리그·가사·립싱크 등 MV 전용 데이터 때문에 자체 스키마로 두고, OTIO는 NLE(Premiere/Resolve 등)와 주고받는 가져오기·내보내기 어댑터로 한정한다. JS 구현은 성숙도가 낮으므로(0.1.0, 제3자) 서버에서 공식 Python OTIO로 변환하는 방식도 고려한다.
- OpenCut의 재작성 방향(Rust 코어 + plugin-first)은 우리 모듈 구조(§10)의 참고 사례로 쓸 수 있다.

### Gaps
- Remotion 5.0 라이선스 변경 내용(PR #3750)은 미확인이다.
- `@remotion/lambda-client` 등이 "UNLICENSED"로 표기된 이유(실제 적용 라이선스)는 미확인이다.
- Motion Canvas v4 로드맵과 Revideo의 지속 가능성은 확인한 자료가 없다.
- 기타 상용·오픈 SDK(IMG.LY CE.SDK, Rendley, Twick, designcombo react-video-editor, Omniclip 등)는 검색 예산 소진으로 조사하지 못했다.

---

## 4. 2D 스켈레탈 애니메이션: Spine, Live2D Cubism SDK for Web, DragonBones, Rive, Lottie/dotLottie, Inochi2D, 자체 구현

### Takeaway
Spine 런타임은 "우리 회사가 Spine Editor 라이선스를 보유하고 통합"하면 배포할 수 있다. 그러나 런타임 라이선스 문구상 그 외 경우에는 "사용자마다 각자 Spine Editor 라이선스"가 필요하다. 사용자가 우리 에디터 안에서 Spine 데이터를 만들게 하는 구조는 라이선스와 충돌할 소지가 크다(Esoteric 확인 필요). Live2D는 사용자가 모델을 임포트하는 앱이 "Expandable Application"에 해당해, 소규모 사업자도 예외 없이 사전 심사와 특별 출판 계약을 거쳐야 한다. DragonBones는 사실상 방치되었고, Rive는 런타임이 MIT지만 내보내기에 유료 좌석이 필요하며, Inochi2D(BSD-2)는 성숙한 웹 런타임이 없다. 따라서 사용자 리깅이 핵심이라면 자체 본·메시 변형·IK 시스템을 만드는 것이 라이선스상 가장 깨끗하다(추정).

### Cited Findings

**Spine**
- `@esotericsoftware/spine-core` 4.3.13(2026-08-30)에 동봉된 "Spine Runtimes License Agreement, Last updated April 5, 2025" 원문: "Integration of the Spine Runtimes into software or otherwise creating derivative works of the Spine Runtimes is permitted under the terms and conditions of Section 2 of the Spine Editor License Agreement … Otherwise, it is permitted to integrate the Spine Runtimes into software … provided that **each user of the Products must obtain their own Spine Editor license** and redistribution of the Products in any form must include this license and copyright notice." — [npm @esotericsoftware/spine-core (LICENSE)](https://www.npmjs.com/package/@esotericsoftware/spine-core), [Spine Runtimes License](https://en.esotericsoftware.com/spine-runtimes-license)
- Spine Editor License 2조: Spine Runtimes는 Spine Editor가 내보낸 데이터를 로드·조작·렌더하는 라이브러리이며, Exhibit A(런타임 라이선스)를 지키면 제품에 통합해 판매·배포할 수 있다 — [Spine Editor License Agreement](http://en.esotericsoftware.com/spine-editor-license) (검색 스니펫)
- Spine 라이선스가 없는 다른 사람에게 런타임 포함 소프트웨어를 배포하려면 "통합 시점에 Spine 라이선스가 필요하고, 그 뒤에는 자유롭게 배포할 수 있다. 단, 다른 사람이 그것을 수정하거나 새 소프트웨어를 만드는 데 쓰지 않아야 한다" — [Spine: Our new licensing explained](https://en.esotericsoftware.com/blog/Our-new-licensing-explained) (검색 스니펫, 원문 위치 미확인)
- Spine Editor(Essential/Professional)는 지정 사용자(named user) 1인당 라이선스다 — [Spine: Purchase](https://esotericsoftware.com/spine-purchase) (검색 스니펫)
- 공식 웹 런타임 4.3.13: `@esotericsoftware/spine-pixi-v8`, `spine-webgl`, `spine-threejs`, `spine-player`. 구 `pixi-spine` 4.0.6은 "SEE SPINE-LICENSE"다 — [npm @esotericsoftware/spine-pixi-v8](https://www.npmjs.com/package/@esotericsoftware/spine-pixi-v8), [npm pixi-spine](https://www.npmjs.com/package/pixi-spine)

**Live2D Cubism SDK for Web**
- 사업 규모 구분: "General User"는 연 매출 1,000만 엔 미만인 개인·학생·단체·법인이다. Small-Scale도 1,000만 엔 미만이고 Middle-Scale은 1억 엔 미만, Large-Scale은 1억 엔 이상이다 — [Live2D Help: 사업 규모 판정](https://help.live2d.com/en/sdk/sdk_007/), [SDK Release License](https://www.live2d.com/en/sdk/license/) (검색 스니펫)
- **Expandable Application**은 출시 전에 심사·승인을 받고 특별 출판 라이선스 계약(Publication License Agreement)을 맺어야 한다. 이 요건은 비확장형 앱에서는 보통 면제되는 General User와 Small-Scale Enterprise를 포함한 **모든 퍼블리셔**에 적용된다 — [Live2D: A. Expandable Applications](https://www.live2d.com/en/sdk/license/expandable/), [신청 양식](https://www.live2d.jp/eng/application-publication-license/form/) (검색 스니펫. 원문 페이지는 egress 차단으로 열람 실패)
- Cubism Core(`live2dcubismcore.min.js`) 파일 헤더: "Live2D Cubism Core (C) 2019 Live2D Inc. All rights reserved. This file is licensed pursuant to the license agreement below. This file corresponds to the 'Redistributable Code' in the agreement. https://www.live2d.com/eula/live2d-proprietary-software-license-agreement_en.html". 이 파일을 재배포한 npm `live2dcubismcore` 1.0.2의 메타데이터는 "ISC"로 **잘못 표기**되어 있다 **[라이선스 플래그]** — [npm live2dcubismcore](https://www.npmjs.com/package/live2dcubismcore), [Live2D Proprietary Software License](https://www.live2d.com/eula/live2d-proprietary-software-license-agreement_en.html)
- `pixi-live2d-display` 0.4.0(MIT, 2023-12)은 README상 "Live2D integration for PixiJS v6"(peerDependency `pixi.js ^6.4.2`)이며, Cubism Core를 따로 포함해야 동작한다 — [npm pixi-live2d-display](https://www.npmjs.com/package/pixi-live2d-display). `untitled-pixi-live2d-engine` 1.4.0(MIT, 2026-09-20)은 PixiJS v8 Render Pipe로 Cubism 2/3/4/5 모델을 지원하며 `extensions.add(Live2DPlugin)`로 등록한다 — [npm untitled-pixi-live2d-engine](https://www.npmjs.com/package/untitled-pixi-live2d-engine)

**DragonBones / Rive / Lottie / Inochi2D**
- DragonBones 에디터는 사실상 중단되었다. 개발사가 수년간 응답이 없고 다운로드 링크도 동작하지 않으며, Egret은 DragonBones와 라이브러리를 오래전에 방치했다. SGGames 팀 등이 커뮤니티 차원에서 현대화를 시도 중이다 — [AlternativeTo](https://alternativeto.net/software/dragonbones/about), [Defold forum](https://forum.defold.com/t/dragon-bones-4-9-5-supports-spine-3-3/3630) (검색 스니펫)
- 커뮤니티 런타임: `dragonbones-pixijs` 1.0.5는 ISC(2025-05, Pixi v8용 README)다. `pixi-dragonbones` 5.7.0-1은 MIT지만 2022년이 마지막이다 — [unpkg README](https://unpkg.com/dragonbones-pixijs@1.0.5/README.md), [npm dragonbones-pixijs](https://www.npmjs.com/package/dragonbones-pixijs)
- Rive: 런타임은 MIT 오픈소스로 런타임 비용이 없고, 내보낸 파일은 플랜이 바뀌어도 계속 동작하지만 "내보내기 자체는 유료 플랜 기능"이 된다. 요금은 Free $0, Cadet $9, Voyager $32, Enterprise $120(좌석/월)이고 2025-10에 Cadet 플랜이 도입되었다("free to create, $9/month to ship") — [Rive blog: $9/mo plan](https://rive.app/blog/rive-s-new-9-mo-plan), [VijayTalksAI](https://vijaytalksai.com/rive-pricing-explained/) (검색 스니펫)
- `@rive-app/canvas`와 `@rive-app/webgl2` 2.44.0은 MIT(2026-09-30)다 — [npm @rive-app/canvas](https://www.npmjs.com/package/@rive-app/canvas)
- `lottie-web` 5.13.0은 MIT(2025-05-21 수정), `@lottiefiles/dotlottie-web` 0.81.0은 MIT(2026-10-06)다 — [npm lottie-web](https://www.npmjs.com/package/lottie-web), [npm @lottiefiles/dotlottie-web](https://www.npmjs.com/package/@lottiefiles/dotlottie-web)
- Inochi2D는 BSD-2-Clause 오픈 툴킷으로, 리깅용 Inochi2D Creator, 스트리밍용 Session, SDK로 구성된다. v0.9는 WASM/WebGL/WebGPU를 통한 웹 지원을 목표로 한다 — [NLnet: Inochi2D](https://nlnet.nl/project/Inochi2D/), [LinuxLinks](https://linuxlinks.com/inochi-creator-create-edit-inochi2d-puppets) (검색 스니펫). npm에서는 `inochi2d`, `inochi2d-web`, `@inochi2d/core`를 찾을 수 없다(404) — [npm registry 조회 2026-10-07 (`npm view inochi2d` → E404)](https://registry.npmjs.org/inochi2d)

### Inferences
- Spine(추정):
  - (a) 사용자가 Spine Editor로 만든 export 데이터를 업로드하고 우리 플레이어가 재생하는 구조는, 우리가 Spine 라이선스를 보유하고 런타임을 통합했다면 Section 2 범위로 보인다.
  - (b) 그 데이터를 만드는 사용자는 각자 Spine Editor 좌석이 필요하다. 보컬로이드 MV 제작자층에게 비용 장벽이 된다.
  - (c) 우리 브라우저 에디터에서 사용자가 Spine 호환 리그를 만들고 Spine 런타임으로 재생하는 구조는 "새 소프트웨어를 만드는 데 사용"하거나 에디터를 대체하는 것으로 해석될 위험이 있다. **Esoteric에 서면 확인이 필요**하다.
- Live2D(추정): 커뮤니티 플랫폼에서 사용자가 자기 .moc3 모델을 올리는 순간 Expandable Application이 된다. 회사 규모와 무관하게 심사·특별 계약을 거쳐야 하며 수수료 구조는 미확인이다. 따라서 Live2D 지원은 출시 범위에서 빼거나, 별도 계약을 전제로 한 "선택 플러그인"으로 분리하는 것이 안전하다. Cubism Core(독점 바이너리)는 우리 번들에 넣지 말고 Live2D 배포 경로를 따른다.
- 자체 구현 범위(추정, 근거 출처 없음):
  - **런타임**: 본 계층(FK), GPU 선형 블렌드 스키닝(메시 가중치), IK(2본 해석해 + CCD/FABRIK), 키프레임 곡선(베지어), 드로우 오더, 슬롯·어태치먼트 교체, 메시 변형 키, 클리핑 마스크, 스프링 물리(결정적 고정 스텝). 숙련 그래픽 엔지니어 기준 약 3–6인월.
  - **에디터**: PSD 레이어 임포트, 메시 삼각분할, 가중치 페인팅, IK 제약 UI, 도프시트·커브 에디터, 자동 리깅 템플릿(입 모양 5모음, 눈 깜빡임). 별도로 약 6–12인월 이상.
  - PixiJS v8 Mesh/Render Pipe 확장 위에 올리면 2D 합성과 통합하기 쉽다.
- Inochi2D 포맷(BSD-2)은 오픈 교환 포맷 후보지만 웹 런타임을 직접 구현해야 하므로, 자체 포맷과 비교한 실익은 제한적이다(추정).
- Rive와 Lottie는 사용자 리깅 대상이라기보다 UI 애니메이션이나 운영자가 제작하는 스톡 이펙트·스티커 에셋 재생용으로 적합하다(추정).

### Gaps
- Live2D Expandable Application의 수수료 금액, 심사 기준, 모델 이용 조건은 원문 열람 차단으로 미확인이다.
- Spine FAQ나 포럼에서 "사용자 저작형 에디터"에 대한 Esoteric의 공식 입장은 미확인이다(추가 검색 차단).
- Rive 런타임으로 사용자가 만든 .riv를 업로드받아 재생하는 SaaS 모델에 대한 Rive 측 조건은 미확인이다.
- DragonBones 포맷과 원 런타임(DragonBonesJS)의 라이선스는 미확인이다.

---

## 5. 3D 옵션: @pixiv/three-vrm(VRM), 브라우저 MMD(three.js MMDLoader 제거 여부, babylon-mmd 등), VMD 모션 임포트

### Takeaway
VRM은 `@pixiv/three-vrm`(MIT, 활발히 유지)이 표준 경로다. VRM 메타데이터에 상업 이용·재배포·개변 허용 같은 라이선스 필드가 들어 있어, 모델별 이용 조건을 UI에서 강제할 수 있다. three.js 본체의 MMD 모듈(MMDLoader/MMDAnimationHelper/MMDPhysics)은 npm tarball로 확인한 결과 **r171이 마지막 포함 버전이고 r172(2024-12-31)에서 제거**되었다. 현재 MMD(PMX/PMD + VMD)를 브라우저에서 제대로 다루는 선택지는 `babylon-mmd`(MIT, Bullet WASM 물리·IK·WebGPU 지원)이고, three.js용으로는 커뮤니티 베타(`@moeru/three-mmd`)가 있다.

### Cited Findings
- `@pixiv/three-vrm` 3.5.5는 MIT(2026-07-09)이고, `@pixiv/three-vrm-animation`(VRM Animation, VRMA)과 `@pixiv/three-vrm-materials-mtoon`은 3.5.5, MIT(2026-09-25)다 — [npm @pixiv/three-vrm](https://www.npmjs.com/package/@pixiv/three-vrm), [npm @pixiv/three-vrm-animation](https://www.npmjs.com/package/@pixiv/three-vrm-animation)
- three-vrm 소스의 표정 프리셋에는 립싱크용 `aa`, `ih`, `ou`, `ee`, `oh`가 정의되어 있다(dist 확인) — [npm @pixiv/three-vrm](https://www.npmjs.com/package/@pixiv/three-vrm)
- three-vrm 소스에 있는 VRM 메타 라이선스 필드: `avatarPermission`, `commercialUsage`, `allowRedistribution`, `modification`, `creditNotation`, `allowExcessivelyViolentUsage`, `allowExcessivelySexualUsage`, `allowPoliticalOrReligiousUsage`, `allowAntisocialOrHateUsage`, `licenseUrl` — [npm @pixiv/three-vrm](https://www.npmjs.com/package/@pixiv/three-vrm)
- three.js MMD 제거를 tarball로 검증한 결과: `examples/jsm/loaders/MMDLoader.js`, `animation/MMDAnimationHelper.js`, `animation/MMDPhysics.js` 3개 파일이 three@0.171.0(r171, 2024-11-29)에는 있고, three@0.172.0(r172, 2024-12-31), 0.173.0, 0.186.1에는 없다 — [npm three](https://www.npmjs.com/package/three)
- three.js 포럼 설명: MMD 코드베이스가 커서 보통의 10리비전 deprecation 규칙보다 짧은 유예로 제거했다. 원작자가 외부 저장소에서 유지할 계획이었으나 완료하지 못해, r171에 머물거나 직접 관리하라고 안내했다. 포럼 인용문("r172 would be the last release with MMD loader")은 **tarball 증거(r171이 마지막)와 상충**한다 — [three.js forum: MMD modules disappeared](https://discourse.threejs.org/t/mmd-modules-disappeared-after-update/87548) (검색 스니펫)
- `babylon-mmd` 1.3.0(MIT, 2026-07-23) README: PMX/PMD 모델과 VMD/VPD 애니메이션을 로드하고 물리·IK·모프 런타임을 제공한다. WASM 기반 멀티스레드 Bullet 물리와 MMD IK, WebGPU를 지원하며 PMX 2.1은 지원 계획이 없다 — [npm babylon-mmd](https://www.npmjs.com/package/babylon-mmd)
- three.js용 대안: `@moeru/three-mmd` 0.2.0-beta.2(MIT, 2026-09-05, "Use MMD on Three.js"), `three-mmd-loader` 0.0.11(2022, 정체), 파서 `mmd-parser` 1.1.2(MIT) — [npm @moeru/three-mmd](https://www.npmjs.com/package/@moeru/three-mmd), [npm three-mmd-loader](https://www.npmjs.com/package/three-mmd-loader), [npm mmd-parser](https://www.npmjs.com/package/mmd-parser)

### Inferences
- 3D 레이어는 VRM 우선(추정): three-vrm, VRMA, MToon 조합은 모두 MIT이고 유지보수도 활발하다. VRM 메타의 `commercialUsage`/`allowRedistribution`/`modification`을 업로드할 때 파싱해 수익화·리믹스·배포 기능을 자동으로 제한하는 "모델 라이선스 게이트"를 둘 수 있다.
- MMD를 지원해야 한다면 Babylon.js 기반 3D 레이어(babylon-mmd)가 기능 완성도에서 앞선다(추정). 2D(PixiJS)와 3D(Babylon 또는 three)를 섞을 때는 3D를 렌더 텍스처로 그리고 2D 합성기에서 하나의 레이어로 합치는 구조가 경계를 단순하게 만든다. 엔진 두 개를 동시에 싣는 번들·메모리 비용은 감수해야 한다.
- 내보내기 결정성: MMD/VRM 스프링·Bullet 물리는 고정 스텝(예: 1/fps의 정수 분할)과 동일 초기 상태로 프레임 순차 시뮬레이션을 해야 한다. 임의 프레임으로 탐색할 때는 처음부터 다시 시뮬레이션하거나 캐시해야 한다(추정).
- VRM 표정 `aa/ih/ou/ee/oh`는 일본어 모음 あ/い/う/え/お에 1:1로 대응해(§7) 일본어 립싱크와 궁합이 좋다(추정).

### Gaps
- VMD 모션을 VRM 휴머노이드로 리타깃하는 브라우저 라이브러리의 현황과 라이선스는 미확인이다.
- MMD·VRM 모델의 개별 이용 규약(모델마다 다름)은 이 조사 범위 밖(법무)이다.
- `@moeru/three-mmd`의 물리 지원 수준은 미확인이다.

---

## 6. 가사·키네틱 타이포그래피: 일본어 웹폰트(OFL), 서브셋·로딩 전략, GPU 텍스트(MSDF, troika-three-text), 세로쓰기·루비

### Takeaway
요청한 일본어 폰트(Noto Sans JP, M PLUS 계열, Dela Gothic One, DotGothic16, Zen Maru Gothic)와 Reggae One은 모두 OFL-1.1로 확인되었고 Kosugi Maru는 Apache-2.0이어서, 상업 SaaS에 번들·임베드해도 된다. 일본어 폰트는 크기 때문에 UI에는 unicode-range 분할 서브셋(Fontsource Noto Sans JP 400은 120개 조각, 합계 약 2.77MB)을 쓰고, 내보내기 렌더에는 "프로젝트 가사 글자만" 동적으로 서브셋하는 2단 전략이 적합하다. CSS는 세로쓰기·루비를 모든 주요 브라우저에서 지원하지만, Canvas·WebGL에는 세로쓰기 API가 없다. troika-three-text도 세로 방향 옵션이 없으므로 GPU 경로의 세로쓰기와 루비는 자체 레이아웃 엔진(HarfBuzz 셰이핑)으로 구현해야 한다(추정).

### Cited Findings
- Fontsource 패키지 라이선스(npm, 5.3.0): `@fontsource/noto-sans-jp`, `m-plus-1p`, `m-plus-rounded-1c`, `dela-gothic-one`, `dotgothic16`, `zen-maru-gothic`, `reggae-one`, `@fontsource-variable/noto-sans-jp`는 OFL-1.1, `@fontsource/kosugi-maru`는 Apache-2.0 — [npm @fontsource/noto-sans-jp](https://www.npmjs.com/package/@fontsource/noto-sans-jp), [npm @fontsource/dela-gothic-one](https://www.npmjs.com/package/@fontsource/dela-gothic-one), [npm @fontsource/dotgothic16](https://www.npmjs.com/package/@fontsource/dotgothic16), [npm @fontsource/zen-maru-gothic](https://www.npmjs.com/package/@fontsource/zen-maru-gothic)
- `@fontsource/noto-sans-jp` 5.3.0을 분석한 결과: 파일 2,319개, 압축 해제 약 80.4MB. `400.css`에 `unicode-range`가 있는 `@font-face` 블록 124개가 있고, 400-normal 번호 서브셋 woff2는 120개, 합계 약 2.77MB(평균 약 23KB)다 — [npm @fontsource/noto-sans-jp](https://www.npmjs.com/package/@fontsource/noto-sans-jp)
- `@font-face` `unicode-range`는 Chrome 1, Firefox 36, Safari 3.1부터 지원한다 — [BCD 8.1.4](https://www.npmjs.com/package/@mdn/browser-compat-data)
- 런타임 서브셋·셰이핑 도구: `subset-font` 2.9.0(BSD-3-Clause, harfbuzz hb-subset의 WASM 빌드로 TTF/WOFF/WOFF2 서브셋 생성), `harfbuzzjs` 1.6.3(MIT), `opentype.js` 2.0.0(MIT), `fontkit` 2.0.4(MIT) — [npm subset-font](https://www.npmjs.com/package/subset-font), [npm harfbuzzjs](https://www.npmjs.com/package/harfbuzzjs), [npm opentype.js](https://www.npmjs.com/package/opentype.js)
- `troika-three-text` 0.52.5(MIT, 2026-07-24) README: 미리 만든 SDF 텍스처 없이 Typr로 .ttf/.otf/.woff를 직접 파싱해 쓰이는 글리프의 SDF 아틀라스를 즉석 생성한다. `gpuAccelerateSDF`로 WebGL 가속 생성을 하고 안 되면 Web Worker JS로 폴백한다. 글리프가 없으면 unicode-font-resolver로 대체 폰트를 찾으며, 기본으로 jsDelivr CDN에서 받고 자체 호스팅도 가능하다. `direction`은 auto/ltr/rtl만 있다 — [npm troika-three-text](https://www.npmjs.com/package/troika-three-text)
- MSDF: `three-msdf-text-utils` 1.5.0(ISC, MSDF·비트맵 폰트, 애니메이션용 속성, WebGPU 지원), `msdf-bmfont-xml` 2.8.0(MIT, 사전 아틀라스 생성) — [npm three-msdf-text-utils](https://www.npmjs.com/package/three-msdf-text-utils), [npm msdf-bmfont-xml](https://www.npmjs.com/package/msdf-bmfont-xml)
- CSS 세로쓰기·루비(BCD 8.1.4): `writing-mode`는 Chrome 48, Firefox 41, Safari 10.1. `text-combine-upright`(縦中横)는 Chrome 48, Firefox 48, Safari 15.4. `<ruby>`는 Chrome 5, Firefox 38, Safari 5. 접두사 없는 `ruby-position`은 Chrome 84, Firefox 38, Safari 18.2 — [BCD 8.1.4](https://www.npmjs.com/package/@mdn/browser-compat-data)
- Canvas 2D: `letterSpacing`은 Chrome 99, Firefox 115, Safari 18.4. `lang`은 Chrome 136, Firefox 151이고 Safari는 미지원이다. 세로쓰기 관련 Canvas 속성은 BCD에서 찾지 못했다 — [BCD 8.1.4](https://www.npmjs.com/package/@mdn/browser-compat-data)
- 일본어 텍스트 처리: `budoux` 0.9.3(Apache-2.0, 문절 단위 줄바꿈), `kuroshiro` 1.2.1(MIT, 한자→후리가나), `kuromoji` 0.1.2(Apache-2.0, 형태소 분석, 2022 이후 정체), `wanakana` 5.3.1(MIT, 가나 변환) — [npm budoux](https://www.npmjs.com/package/budoux), [npm kuroshiro](https://www.npmjs.com/package/kuroshiro), [npm kuromoji](https://www.npmjs.com/package/kuromoji), [npm wanakana](https://www.npmjs.com/package/wanakana)

### Inferences
- 로딩 전략(추정):
  - **편집 UI(DOM)**: Fontsource/Google Fonts식 unicode-range 분할로 화면에 나온 글자의 조각만 받는다.
  - **GPU 가사 렌더**: 프로젝트 가사(+루비) 문자 집합으로 `subset-font`(hb-subset WASM)를 돌려 수십~수백 KB짜리 서브셋을 만들고, 이를 프로젝트 에셋으로 고정한다. 서버 렌더에도 같은 파일을 쓰면 결정적이다.
  - 렌더 전에 `document.fonts.load()` 또는 FontFace 로드가 끝났는지 확인한다.
- GPU 텍스트(추정): 키네틱 타이포(글자별 변형·디졸브·외곽선·글로우)는 SDF/MSDF 쿼드를 글리프 단위로 다룰 때 가장 유연하다. 일본어는 글리프 수가 많아 사전 아틀라스는 비효율적이므로, troika 방식(필요한 글리프만 동적 SDF 생성)이나 PixiJS 동적 비트맵 폰트가 적합하다. 오프라인 PSD 품질의 굵은 디스플레이 폰트(Dela Gothic One 등)는 SDF 해상도(`sdfGlyphSize`)를 높여야 할 수 있다.
- 세로쓰기·루비(추정): 셰이핑은 HarfBuzz(`vert`/`vrt2` 기능으로 세로 대체 글리프)에 맡기고, 열 배치·회전(라틴·숫자), 縦中横, 루비 위치 계산(모노/그룹 루비, 친문자 폭 초과 시 처리)은 자체 레이아웃 코드로 짠다. 한자의 읽기는 사용자 입력을 우선하고, kuroshiro/kuromoji로 초안을 자동 생성한다. 줄바꿈 후보는 BudouX로 구한다.
- OFL(추정): 폰트를 앱에 번들·서브셋해 배포할 수 있지만 폰트 단독 판매는 금지된다. 서브셋을 "수정 버전"으로 보는 해석과 예약 폰트명(RFN) 조항은 폰트별로 확인해야 한다(아래 Gaps).

### Gaps
- 각 폰트의 OFL 예약 폰트명(RFN) 유무와, 서브셋 배포 시 RFN 관련 이름 변경 의무는 미확인이다.
- PixiJS v8의 동적 MSDF와 CJK 대량 글리프 성능은 확보한 자료가 없다.
- HTML-in-Canvas처럼 DOM 텍스트를 캔버스에 직접 그리는 제안의 브라우저 출시 상태는 미확인이다.
- troika-three-text의 세로쓰기 미지원은 README 옵션 목록을 근거로 한 추정이며, 공식 이슈는 확인하지 못했다.

---

## 7. 오디오: Web Audio/AudioWorklet 동기 재생, 비트·템포·온셋, 라우드니스(EBU R128/LUFS), 음원 분리(Demucs), 일본어 가사 강제 정렬, 립싱크(Rhubarb), 가나·음소→입 모양(あいうえお) 매핑

### Takeaway
재생 동기화와 오프라인 믹스다운에 필요한 Web Audio 기능(AudioWorklet, OfflineAudioContext, outputLatency/getOutputTimestamp)은 모든 주요 브라우저에서 쓸 수 있다. 분석 라이브러리는 라이선스 편차가 크다. 허용형으로는 web-audio-beat-detector(MIT), realtime-bpm-analyzer(Apache-2.0), beat-this(MIT), loudness-worklet(MIT, BS.1770-5), Demucs(MIT), WhisperX(BSD-2), MFA(MIT)가 있다. 반면 essentia.js(AGPL), aubio(GPL), madmom 모델(비상업), whisper-timestamped(실제 LICENSE는 AGPL), MMS 정렬 모델(CC-BY-NC)은 상업 SaaS에서 배제해야 한다. 보컬로이드/SynthV 프로젝트 파일이 있으면 음표 타이밍과 가나 가사로 립싱크를 직접 생성하는 편이 ASR 정렬보다 정확하고 저렴하다(추정).

### Cited Findings

**재생·동기화 API (BCD 8.1.4)**
- `AudioWorklet`은 Chrome 66, Edge 79, Firefox 76, Safari 14.1, iOS 14.5다 — [BCD 8.1.4](https://www.npmjs.com/package/@mdn/browser-compat-data)
- 접두사 없는 `OfflineAudioContext`는 Chrome 35, Firefox 25, Safari 14.1이다 — [BCD 8.1.4](https://www.npmjs.com/package/@mdn/browser-compat-data)
- `AudioContext.outputLatency`는 Chrome 102, Firefox 70, **Safari 18.4**. `AudioContext.getOutputTimestamp()`는 Chrome 57, Firefox 70, Safari 14.1이다 — [BCD 8.1.4](https://www.npmjs.com/package/@mdn/browser-compat-data)

**비트·템포·온셋**
- `web-audio-beat-detector` 8.2.39(MIT, 2026-08-26): `analyze(AudioBuffer)`는 템포를, `guess()`는 `{bpm, offset, tempo}`(첫 비트 오프셋 포함)를 돌려준다. Joe Sullivan의 Web Audio 비트 검출 기법을 기반으로 한다 — [npm web-audio-beat-detector](https://www.npmjs.com/package/web-audio-beat-detector)
- `realtime-bpm-analyzer` 5.0.15는 Apache-2.0(2026-06-18), `meyda` 5.6.3(오디오 특징 추출)은 MIT(2024-04)다 — [npm realtime-bpm-analyzer](https://www.npmjs.com/package/realtime-bpm-analyzer), [npm meyda](https://www.npmjs.com/package/meyda)
- `aubiojs` 0.2.1(2022)은 npm 메타데이터에 license가 없다. 동봉 license 파일은 MIT 형식이고 README는 "based on aubio"라고 적는다 — [npm aubiojs](https://www.npmjs.com/package/aubiojs). 그런데 aubio 본체(PyPI 0.4.9)는 "GNU/GPL version 3", 분류자 GPLv3+다 **[라이선스 플래그]** — [PyPI aubio](https://pypi.org/project/aubio/)
- `essentia.js` 0.1.3은 **AGPL-3.0**(2022-05 이후 정체), Python `essentia`는 **AGPL-3.0-only**(2026-05 업로드)다 **[라이선스 플래그]** — [npm essentia.js](https://www.npmjs.com/package/essentia.js), [PyPI essentia](https://pypi.org/project/essentia/)
- `madmom` 0.16.1의 라이선스는 "BSD, CC BY-NC-SA"이고 분류자는 "Free for non-commercial use"다(2018 이후 정체) **[라이선스 플래그]** — [PyPI madmom](https://pypi.org/project/madmom/)
- `beat-this` 1.1.0은 MIT(2026-04-14). `BeatNet` 1.1.3은 wheel LICENSE가 CC BY 4.0. `allin1` 1.1.0(음악 구조 분석)은 MIT이고 demucs, natten 등에 의존한다. `librosa` 1.0.0은 ISC(2026-08-11)다 — [PyPI beat-this](https://pypi.org/project/beat-this/), [PyPI BeatNet](https://pypi.org/project/BeatNet/), [PyPI allin1](https://pypi.org/project/allin1/), [PyPI librosa](https://pypi.org/project/librosa/)

**라우드니스**
- `loudness-worklet` 2.1.0(MIT, 2026-10-01): ITU-R BS.1770-5 기반 AudioWorkletProcessor로 Momentary/Short-term/Integrated 라우드니스, LRA, True-Peak를 측정한다. 라이브 입력과 `OfflineAudioContext` 오프라인 분석을 모두 지원하며, `decodeAudioData()` 리샘플링이 True-Peak 정확도에 주는 영향도 주의사항으로 적어 둔다 — [npm loudness-worklet](https://www.npmjs.com/package/loudness-worklet)
- `ebur128-wasm` 3.0.0은 Apache-2.0(2023), `lufs` 0.5.25는 MIT, 서버 측 `pyloudnorm` 0.2.0은 MIT다 — [npm ebur128-wasm](https://www.npmjs.com/package/ebur128-wasm), [npm lufs](https://www.npmjs.com/package/lufs), [PyPI pyloudnorm](https://pypi.org/project/pyloudnorm/)

**음원 분리**
- `demucs` 4.1.0은 MIT(PyPI 2026-07-11)이고 저장소는 `adefossez/demucs`로 옮겨졌다. v4 Hybrid Transformer Demucs는 MUSDB HQ 테스트에서 SDR 9.00dB, sparse attention과 소스별 파인튜닝(htdemucs_ft)으로 9.20dB를 낸다 — [PyPI demucs](https://pypi.org/project/demucs/)
- `demucs-web` 1.0.2(MIT, 2025-12-01): ONNX Runtime Web의 WebGPU/WASM 가속으로 브라우저에서 HTDemucs 4트랙(drums/bass/other/vocals) 분리를 한다. 모델은 약 172MB, 입력은 44.1kHz 스테레오이며 SharedArrayBuffer 때문에 COOP/COEP 헤더가 필요하다 — [npm demucs-web](https://www.npmjs.com/package/demucs-web)
- `onnxruntime-web` 1.30.0은 MIT(2026-09-18)다. 기타 Python 분리 도구로 `audio-separator` 0.47.0(MIT, 모델별 라이선스는 미확인), `spleeter` 2.4.2(MIT), `openunmix` 1.3.0(MIT)이 있다 — [npm onnxruntime-web](https://www.npmjs.com/package/onnxruntime-web), [PyPI audio-separator](https://pypi.org/project/audio-separator/), [PyPI spleeter](https://pypi.org/project/spleeter/)

**강제 정렬(프로젝트 파일이 없을 때)**
- `whisperx` 3.8.6은 BSD-2-Clause(2026-05-25)다. 소스(`alignment.py`)를 보면 일본어 정렬 기본 모델은 `jonatasgrosman/wav2vec2-large-xlsr-53-japanese`이고, `ja`는 공백 없는 언어로 처리된다 — [PyPI whisperx](https://pypi.org/project/whisperx/)
- `whisper-timestamped` 1.15.9: PyPI 메타데이터는 "License: GPLv3"인데 **wheel에 동봉된 LICENSE 파일은 GNU AFFERO GPL v3**라 서로 맞지 않는다. 보수적으로 AGPL로 간주해야 한다 **[라이선스 플래그]** — [PyPI whisper-timestamped](https://pypi.org/project/whisper-timestamped/)
- `montreal-forced-aligner` 3.4.2는 MIT(2026-08-20)다 — [PyPI montreal-forced-aligner](https://pypi.org/project/montreal-forced-aligner/)
- `ctc-forced-aligner` 1.0.2(PyPI판은 Deskpai 포크) README: 수정분은 DOSL-1.0이고, 기본 `MMS_FA` 모델과 ONNX 가중치는 **CC-BY-NC 4.0**이라 "상업 용도에는 다른 모델을 쓰라"고 명시한다 **[라이선스 플래그]** — [PyPI ctc-forced-aligner](https://pypi.org/project/ctc-forced-aligner/)
- `stable-ts` 2.19.1(MIT), `faster-whisper` 1.2.1(MIT), `nemo-toolkit` 3.0.0(Apache-2.0), `pyannote.audio` 4.0.7(LICENSE MIT), `torchaudio` 2.11.0(BSD), `pyopenjtalk` 0.4.1(MIT, 일본어 G2P) — [PyPI stable-ts](https://pypi.org/project/stable-ts/), [PyPI faster-whisper](https://pypi.org/project/faster-whisper/), [PyPI nemo-toolkit](https://pypi.org/project/nemo-toolkit/), [PyPI pyopenjtalk](https://pypi.org/project/pyopenjtalk/)
- 브라우저 내 ASR: `@huggingface/transformers` 4.3.1(Apache-2.0)의 Whisper 파이프라인은 `return_timestamps: "word"`(cross-attention alignment heads 기반 `_extract_token_timestamps`)를 지원하고, `device: 'webgpu'`로 GPU 실행이 가능하다(기본은 WASM CPU) — [npm @huggingface/transformers](https://www.npmjs.com/package/@huggingface/transformers)

**립싱크**
- Rhubarb Lip Sync(npm 래퍼 `rhubarb-lip-sync`의 README 사본, MIT): 기본 PocketSphinx 인식기는 영어만 인식한다. "Phonetic" 인식기는 단어 대신 개별 음과 음절을 인식해 정확도는 낮지만 **언어 독립적**이므로 비영어 녹음에 쓰라고 안내한다. 출력은 TSV/XML/JSON 입 모양 타임라인이며, 마지막 줄은 `extendedShapes` 설정에 따라 X 또는 A(닫힌 입)다 — [npm rhubarb-lip-sync](https://www.npmjs.com/package/rhubarb-lip-sync)
- 이 npm 래퍼는 CLI 명령(FFmpeg 포함)을 실행하는 구조라 서버 측에서만 쓸 수 있다(index.js 확인) — [npm rhubarb-lip-sync](https://www.npmjs.com/package/rhubarb-lip-sync)
- `wawa-lipsync` 0.0.2(MIT, 2025-11)는 Web Audio `AnalyserNode`로 실시간 비짐(viseme)을 추정한다. `pixi-live2d-display-lipsyncpatch` 0.5.0은 MIT다 — [npm wawa-lipsync](https://www.npmjs.com/package/wawa-lipsync), [npm pixi-live2d-display-lipsyncpatch](https://www.npmjs.com/package/pixi-live2d-display-lipsyncpatch)
- VRM 표정 프리셋 `aa/ih/ou/ee/oh`(§5) — [npm @pixiv/three-vrm](https://www.npmjs.com/package/@pixiv/three-vrm)

### Inferences
- 동기 재생(추정): 미리보기는 AudioContext의 오디오 시계를 마스터로 삼고 `getOutputTimestamp()`와 `outputLatency`로 화면 시간을 보정한다. 내보내기는 `OfflineAudioContext`로 믹스다운한 PCM을 AudioEncoder 또는 Mediabunny로 인코딩해 결정성을 확보한다. 라우드니스(예: YouTube 기준 −14 LUFS 부근으로 알려짐, 미확인)는 loudness-worklet으로 같은 오프라인 그래프에서 측정한다.
- 립싱크 1순위, 프로젝트 파일이 있을 때(추정): VOCALOID/Synthesizer V/UTAU 프로젝트의 음표 시작·길이와 가사(가나)를 직접 읽어 다음처럼 매핑한다.
  - あ段→`aa`, い段→`ih`, う段→`ou`, え段→`ee`, お段→`oh`
  - ん·っ·쉼표→닫힘
  - ま/ば/ぱ행 자음 시작→짧은 닫힘 프레임 삽입
  - 장음(ー)→직전 모음 유지
  - 같은 매핑을 Live2D(입 개폐·입 모양 파라미터)와 자체 2D 리그의 5모음 블렌드에 공유한다. 한자 가사는 kuromoji/pyopenjtalk로 읽기를 구하되 사용자가 고칠 수 있게 한다.
- 립싱크 2순위, 오디오만 있을 때 서버 파이프라인(추정): Demucs로 보컬 스템을 분리한 뒤 faster-whisper나 WhisperX로 단어·문자 타임스탬프를 구하거나, 가사 텍스트가 있으면 MFA·WhisperX 정렬을 쓴다. 이어서 pyopenjtalk로 음소/가나를 만들고 위 매핑을 적용한다.
  - 라이선스상 안전한 사슬: demucs(MIT) → faster-whisper(MIT) → whisperX(BSD-2, 단 일본어 wav2vec2 모델 라이선스 미확인) → pyopenjtalk(MIT)
  - 배제 대상: whisper-timestamped(AGPL), MMS_FA(CC-BY-NC), madmom 모델(NC)
  - 가창은 발화보다 모음이 길고 음정 변화가 커서, 발화용으로 학습된 정렬 모델은 정확도가 떨어질 것으로 예상한다. 사용자 보정 UI가 필수다.
- Rhubarb(추정): 일본어에는 Phonetic 모드만 쓸 수 있고, 입 모양 세트가 영어 기반(Preston Blair 계열)이라 5모음 매핑보다 부정확할 가능성이 높다. 보조나 폴백으로 둔다.
- 비트 분석 분담(추정): 클라이언트는 web-audio-beat-detector로 빠른 BPM·첫 비트를 구하고, 서버는 beat-this(MIT)나 allin1(MIT, 구조 분석)로 다운비트와 구간(인트로/후렴)을 정밀 분석해 자동 컷·이펙트 동기화에 쓴다.
- 브라우저 Demucs는 172MB 모델 다운로드와 WebGPU 의존 때문에 일반 사용자 기본 경로로는 무겁다. 서버 처리를 기본으로 하고 브라우저 처리는 옵션으로 두는 편이 현실적이다(추정).

### Gaps
- Julius segmentation-kit의 라이선스와 유지 상태는 출처를 확보하지 못해 미확인이다.
- `jonatasgrosman/wav2vec2-large-xlsr-53-japanese` 모델 카드 라이선스, MFA 일본어 사전·음향 모델 라이선스, beat-this 가중치 라이선스는 미확인이다.
- 보컬로이드·SynthV 합성 보컬에 대한 Whisper/WhisperX/MFA 정렬 정확도 실측 자료는 찾지 못했다.
- Rhubarb 본가(DanielSWolf/rhubarb-lip-sync)의 최신 릴리스와 바이너리 라이선스(MIT로 알려짐)는 npm 래퍼 README 외 1차 확인을 하지 못했다.
- 브라우저 AudioWorklet의 장시간 재생 드리프트 실측 자료는 없다.

---

## 8. 백엔드: 서버 측 렌더링(GPU headless Chromium vs 네이티브), FFmpeg 트랜스코딩, HLS/CMAF 패키징, 매니지드 비디오 서비스 가격, 오브젝트 스토리지 이그레스

### Takeaway
서버 렌더는 "브라우저와 같은 웹 렌더 엔진을 headless Chromium(Puppeteer/Playwright, Apache-2.0)으로 GPU(ANGLE/EGL/Vulkan) 또는 SwiftShader CPU에서 돌리고, 인코딩은 FFmpeg나 Mediabunny 서버판으로 하는" 구성이 미리보기와 내보내기의 일치성 면에서 유리하다. Remotion도 같은 계열 GL 옵션을 제공한다. 패키징은 Shaka Packager(BSD-3)로 하고, 재생은 hls.js/Shaka Player/Video.js(모두 Apache-2.0)로 한다. 서버에서 GPL FFmpeg 빌드를 쓰는 것은 배포가 아니어서 통상 허용된다(추정). 매니지드 서비스(Mux, Cloudflare Stream, MediaConvert, Bunny)와 S3/R2 이그레스 단가는 이번 세션에서 검증하지 못했다.

### Cited Findings
- `puppeteer` 25.12.0은 Apache-2.0(2026-09-23), `playwright` 1.63.0은 Apache-2.0(2026-10-07)이다 — [npm puppeteer](https://www.npmjs.com/package/puppeteer), [npm playwright](https://www.npmjs.com/package/playwright)
- Remotion 서버 렌더(`@remotion/renderer`)는 FFmpeg로 인코딩하고 브라우저 렌더는 WebCodecs와 Mediabunny를 쓴다 — [Remotion: Client-side rendering](https://remotion.dev/docs/client-side-rendering/) (검색 스니펫). headless Chromium GL 백엔드 옵션은 `swangle`, `angle`, `egl`, `swiftshader`, `vulkan`, `angle-egl`이다 — [npm @remotion/renderer](https://www.npmjs.com/package/@remotion/renderer)
- FFmpeg 배포 패키지별 라이선스: `ffmpeg-static` 5.3.0은 **GPL-3.0-or-later**(2025-11-14, GPL 빌드 동봉), `@ffmpeg-installer/ffmpeg` 1.1.0은 LGPL-2.1(2022 이후 정체), `@ffmpeg/core`는 GPL-2.0-or-later다 **[라이선스 플래그: 바이너리 재배포 시]** — [npm ffmpeg-static](https://www.npmjs.com/package/ffmpeg-static), [npm @ffmpeg-installer/ffmpeg](https://www.npmjs.com/package/@ffmpeg-installer/ffmpeg)
- Node FFmpeg 래퍼 `fluent-ffmpeg` 2.1.3은 npm에서 "Package no longer supported."로 deprecated 처리되었다(2025-05-22) — [npm fluent-ffmpeg](https://www.npmjs.com/package/fluent-ffmpeg)
- `@mediabunny/server`는 NodeAV 기반으로 Node/Bun/Deno에서 인코더·디코더를 제공한다 — [npm @mediabunny/server](https://www.npmjs.com/package/@mediabunny/server). Mediabunny는 HLS와 MPEG-TS 쓰기도 지원한다(README) — [npm mediabunny](https://www.npmjs.com/package/mediabunny)
- `shaka-packager` npm 3.9.3(2026-07-27)에 동봉된 LICENSE는 "Copyright 2014, Google LLC" BSD-3 형식이다 — [npm shaka-packager](https://www.npmjs.com/package/shaka-packager)
- 플레이어: `hls.js` 1.7.3, `shaka-player` 5.2.12, `video.js` 8.24.1은 모두 Apache-2.0이다. `@mux/mux-player` 3.14.0은 MIT다. `@mux/mux-node` 15.5.0은 "renamed to @mux/ts"로 deprecated되었다(2026-10-02) — [npm hls.js](https://www.npmjs.com/package/hls.js), [npm shaka-player](https://www.npmjs.com/package/shaka-player), [npm video.js](https://www.npmjs.com/package/video.js), [npm @mux/mux-node](https://www.npmjs.com/package/@mux/mux-node)

### Inferences
- 2단 렌더 구조(추정):
  - **(1) 클라이언트 렌더**: WebCodecs 지원 기기에서 1080p 이하를 기본으로 한다. 서버 비용이 0이다.
  - **(2) 서버 렌더 큐**: 미지원 기기, 4K·장시간, "공개 게시용 최종본"(일관 품질, 워터마크·라우드니스 정규화)을 맡는다. 서버 렌더러는 같은 렌더 패키지를 headless Chromium에서 프레임 순차로 돌리고, 프레임을 WebCodecs(Chromium 내부) 또는 파이프로 FFmpeg에 넘긴다.
  - GPU 인스턴스가 없으면 SwiftShader/swangle(CPU)로 돌아가 크게 느려진다.
- 트랜스코딩·패키징(추정): 업로드 원본을 메자닌으로 보관하고, FFmpeg로 H.264 ABR 래더(예: 360p–1080p, 필요 시 AV1 추가)를 만든 뒤 Shaka Packager로 HLS/DASH(CMAF fMP4)를 패키징한다. 커뮤니티 시청 트래픽이 커지면 매니지드 서비스(Mux/Cloudflare Stream/Bunny Stream)와 자체 파이프라인+CDN의 손익분기를 계산해야 한다.
- GPL(추정): 서버에서만 실행하는 GPL FFmpeg는 사용자에게 배포되지 않아 GPL 의무가 통상 발생하지 않는다(AGPL과 다름). 다만 ffmpeg-static 같은 GPL 빌드를 데스크톱 앱 등으로 재배포하면 의무가 생긴다. libx264/libx265의 상용 라이선스 필요 여부는 법무 확인 대상이다.

### Gaps
- **매니지드 비디오 서비스 단가(2025–26)는 검색 예산 소진으로 전부 미확인**이다. 아래는 학습 데이터 기억에 의존한 참고치이며 **인용 금지, 벤더 페이지 재확인 필요**(미확인):
  - Cloudflare Stream: 저장 1,000분당 월 약 $5, 전송 1,000분당 약 $1, 인코딩 무료(미확인)
  - Cloudflare R2: 저장 GB-월 약 $0.015, **이그레스 무료**(미확인)
  - AWS S3 Standard: 저장 GB-월 약 $0.023, 인터넷 이그레스 첫 10TB 구간 GB당 약 $0.09, 월 100GB 무료(미확인)
  - Bunny Stream: 저장 GB-월 약 $0.01(리전별), CDN 전송 GB당 $0.005~, 인코딩 무료(미확인)
  - Mux: 인코딩·저장·전송을 분 단위로 과금하는 구조로 알려져 있으나 2025–26 단가는 미확인
  - AWS Elemental MediaConvert: 출력 정규화 분(normalized minute) 기준으로 tier·해상도·코덱별 과금. 단가 미확인
- GPU headless Chromium의 클라우드 운영(예: GPU 인스턴스에서의 ANGLE/Vulkan 안정성, AWS Lambda에 GPU가 없음)과 프레임당 렌더 속도 실측은 미확인이다.
- Bento4(GPL/상용 이중 라이선스로 알려짐)와 GPAC(LGPL로 알려짐)의 라이선스는 미확인이다.
- Remotion Lambda의 렌더당 실제 AWS 비용은 미확인이다.

---

## 9. 레퍼런스 아키텍처: Clipchamp, CapCut web, Canva video, Kapwing, Descript, Figma, BandLab 등 (미리보기 vs 내보내기, WebCodecs/WebGPU 채택, 클라우드 렌더)

### Takeaway
요청된 기업 엔지니어링 블로그(Clipchamp, CapCut, Canva, Kapwing, Descript, Figma, BandLab)는 검색 예산이 바닥나 **한 건도 1차 확인하지 못했다**. 대신 확인 가능한 오픈소스·상용 엔진들은 모두 같은 방향으로 수렴하고 있다. 브라우저에서는 WebCodecs + Mediabunny + GPU 합성기로 미리보기와 클라이언트 내보내기를 처리하고, 서버에서는 headless Chromium + FFmpeg(또는 Lambda 분산)로 렌더한다.

### Cited Findings
- Remotion은 같은 컴포지션을 서버에서는 `@remotion/renderer`(headless Chromium + FFmpeg, Lambda 분산)로, 브라우저에서는 `@remotion/web-renderer`(WebCodecs + Mediabunny, 알파)로 렌더하는 이원 구조다 — [Remotion: Client-side rendering](https://remotion.dev/docs/client-side-rendering/), [npm @remotion/web-renderer](https://www.npmjs.com/package/@remotion/web-renderer)
- Diffusion Studio Core는 "비디오에 최적화된 게임 엔진" 같은 브라우저 엔진으로, 인터랙티브 미리보기와 렌더를 같은 엔진에서 처리하며 Mediabunny 위에 구축되었다. 서버 렌더가 필요하면 Remotion을 권한다 — [npm @diffusionstudio/core](https://www.npmjs.com/package/@diffusionstudio/core)
- OpenCut은 Next.js 웹 에디터에서 출발해 공유 Rust 코어(GPU 합성·효과·마스크)와 plugin-first 구조, headless API 계획으로 재작성 중이다 — [mer.vin](https://mer.vin/2026/07/opencut-explained-open-source-capcut-alternative-classic-editor-vs-ground-up-rewrite/)
- Mediabunny 후원사에 Remotion, Gling AI, Diffusion Studio, Kino, Screen Studio, Tella, ElevenLabs, React Video Editor가 있다. 브라우저 미디어 처리 업계에서 공통 기반으로 채택되고 있다는 신호다 — [npm mediabunny README](https://www.npmjs.com/package/mediabunny)
- Revideo는 웹 에디터에 임베드할 미리보기 플레이어와 렌더 API(MP4)를 분리해 제공한다 — [docs.re.video](https://docs.re.video/)

### Inferences
- 공통 패턴(추정): 미리보기와 내보내기가 **같은 장면 그래프·같은 렌더 코드**를 쓰고, 차이는 "실시간 시계 + 프레임 드롭 허용"(미리보기)과 "프레임 인덱스 순차 + 드롭 불허"(내보내기)뿐이 되도록 설계한다. 서버 렌더는 그 코드를 headless Chromium에서 실행하는 "같은 엔진, 다른 호스트" 방식으로 둔다.

### Gaps
- 다음은 모두 **미확인**이며 후속 검색으로 원문을 찾아야 한다(아래 괄호는 학습 데이터 기반 기억일 뿐, 인용 금지):
  - Clipchamp(WebCodecs 초기 도입 사례로 알려짐)
  - CapCut web(WASM 기반 엔진으로 알려짐)
  - Canva video(브라우저 인코딩 도입 여부)
  - Kapwing(클라이언트 측 내보내기 전환 여부)
  - Descript 웹앱(WebCodecs/WASM 사용 여부)
  - Figma(2025년경 WebGPU 렌더러 전환 발표로 기억됨)
  - BandLab(웹 DAW의 Web Audio/AudioWorklet 구조)
- 각 사의 미리보기와 내보내기 불일치 대응, 클라우드 렌더 비용 구조는 미확인이다.

---

## 10. 트리형·순환 없는 모노레포 아키텍처 도구: Nx enforce-module-boundaries, dependency-cruiser, eslint-plugin-boundaries, madge, TypeScript project references, pnpm workspaces/Turborepo, 플러그인(의존성 역전) 패턴

### Takeaway
2026년 현재 순환 금지와 계층 방향 강제에 쓸 도구는 모두 MIT/ISC/Apache로 라이선스 문제가 없다. 강제 지점별 역할은 다음과 같다.
- **패키지 그래프**: Nx `depConstraints`(태그) 또는 Turborepo `boundaries`(태그, 실험적)
- **파일·모듈 그래프 순환**: dependency-cruiser `no-circular`(CI 게이트)
- **편집기 즉시 피드백**: eslint-plugin-boundaries v7의 `boundaries/dependencies` 정책 또는 Nx ESLint 규칙
- **컴파일 단계**: TypeScript project references가 순환을 컴파일 오류로 막는다. 다만 Turborepo 문서는 project references 사용을 권하지 않는다.

"트리형"은 실무적으로 단방향 계층 DAG로 구현한다(공유 core에 여러 패키지가 의존하므로 엄밀한 트리는 불가). 효과·익스포터는 core의 플러그인 API에만 의존하고 앱(composition root)이 등록하는 의존성 역전 구조로 둔다.

### Cited Findings
- **Nx**: `nx` 23.2.1은 MIT(23.0.0은 2026-06-16)다. `@nx/eslint-plugin` 23.2.1의 `enforce-module-boundaries` 규칙 소스(dist)에서 확인한 옵션은 `depConstraints`, `onlyDependOnLibsWithTags`, `notDependOnLibsWithTags`, `allowCircularSelfDependency`, `ignoredCircularDependencies`, `enforceBuildableLibDependency`, `bannedExternalImports`, `allowedExternalImports`, `banTransitiveDependencies`, `checkNestedExternalImports`이며, 순환 시 "Circular dependency between …" 오류를 낸다 — [npm @nx/eslint-plugin](https://www.npmjs.com/package/@nx/eslint-plugin), [npm nx](https://www.npmjs.com/package/nx)
- **dependency-cruiser**: 18.5.0은 MIT(2026-09-30)다.
  - `configs/recommended.cjs`의 forbidden 규칙은 `no-orphans`, `no-circular`, `no-deprecated-core`, `no-duplicate-dependency-types`, `no-non-package-json`, `not-to-deprecated`, `not-to-unresolvable`이다.
  - `no-circular`는 `to: { circular: true }`, severity `error`이고, 주석으로 "dependency inversion을 쓰거나 모듈 단일 책임을 확인하라"고 권한다.
  - 설정 스키마는 `forbidden`, `allowed`(allowedSeverity), `required`, `reachable`, `viaOnly`, `pathNot` 등을 지원한다.
  - 출처 — [npm dependency-cruiser](https://www.npmjs.com/package/dependency-cruiser)
- **eslint-plugin-boundaries**: 7.2.0은 MIT(2026-08-09)다. 설정의 `boundaries/elements`(경로 패턴별 요소 타입)와 `boundaries/files`(파일 카테고리)로 요소를 정의하고, 규칙 `boundaries/dependencies`에 `default: "disallow"`와 `policies`(from/allow/disallow, 요소 타입·파일 카테고리)를 건다(README 예시). dist에는 레거시 규칙명(`element-types`, `entry-point`, `external`, `no-ignored` 등)도 남아 있다 — [npm eslint-plugin-boundaries](https://www.npmjs.com/package/eslint-plugin-boundaries)
- **madge**: 8.0.0은 MIT로 2024-08-05가 마지막 수정이다(갱신 둔화). 모듈 의존 그래프 시각화, 순환 탐지(`.circular()`), Graphviz는 이미지 출력에만 필요하다 — [npm madge](https://www.npmjs.com/package/madge)
- **Sheriff**: `@softarc/sheriff-core`와 `@softarc/eslint-plugin-sheriff` 0.20.0은 MIT(2026-09-29)이며 "Sheriff enforces module boundaries and dependency rules in TypeScript"다 — [npm @softarc/sheriff-core](https://www.npmjs.com/package/@softarc/sheriff-core)
- `eslint-plugin-import-x` 4.17.1(MIT)과 `knip` 6.40.0(ISC, 미사용 export·의존성 탐지)은 보조 도구다 — [npm eslint-plugin-import-x](https://www.npmjs.com/package/eslint-plugin-import-x), [npm knip](https://www.npmjs.com/package/knip)
- **Turborepo**: `turbo` 2.11.7은 MIT(2026-10-02)다.
  - 패키지에 동봉된 문서에 따르면 `turbo boundaries`는 **Experimental**이며 두 가지 위반을 잡는다. (1) 패키지 디렉터리 밖 파일 import, (2) package.json에 선언하지 않은 패키지 import.
  - 패키지에 태그를 달고 `boundaries.tags.<tag>.dependencies/dependents`에 `allow`/`deny` 목록을 지정할 수 있다(schema.json).
  - Turborepo는 패키지·태스크 관계를 DAG로 모델링한다.
  - 같은 문서는 "You likely don't need TypeScript Project References"라며, 설정 지점과 캐시 계층이 하나 더 생겨 문제를 일으킬 수 있으니 Turborepo에서는 피하라고 권한다.
  - 출처 — [npm turbo (docs/reference/boundaries.mdx, schema.json, guides/tools/typescript.mdx)](https://www.npmjs.com/package/turbo)
- **TypeScript**: `typescript` 5.9.2의 진단 메시지에 "Project references may not form a circular graph. Cycle detected: {0}"가 있다. 6.0.2는 2026-03-23, **7.0.2는 2026-07-08**에 게시되었고, 7.x는 플랫폼별 네이티브 바이너리(`@typescript/typescript-linux-x64` 등)를 optionalDependencies로 배포한다 — [npm typescript](https://www.npmjs.com/package/typescript)
- **pnpm**: 12.9.1은 MIT다(10.0.0은 2025-01-07, 11.0.0은 2026-04-28, 12.0.0은 2026-08-26) — [npm pnpm](https://www.npmjs.com/package/pnpm)
- **플러그인 등록 선례**:
  - PixiJS v8 확장 시스템: `extensions.add(Live2DPlugin)`로 커스텀 Render Pipe를 등록한다 — [npm untitled-pixi-live2d-engine](https://www.npmjs.com/package/untitled-pixi-live2d-engine)
  - Mediabunny는 코덱을 별도 확장 패키지(aac/mp3/flac encoder)로 주입한다 — [npm @mediabunny/aac-encoder](https://www.npmjs.com/package/@mediabunny/aac-encoder)
  - OpenCut 재작성판은 plugin-first 아키텍처다 — [mer.vin](https://mer.vin/2026/07/opencut-explained-open-source-capcut-alternative-classic-editor-vs-ground-up-rewrite/)

### Inferences
- 권장 패키지 계층(추정, 위에서 아래로만 의존):
  ```
  apps/web-studio, apps/render-worker(server), apps/api       (type:app — composition root, 플러그인 등록)
    └ features/* (editor-timeline, lyric-editor, rig-editor …)  (type:feature)
        └ engine/* (render-pixi, render-3d, audio-engine, media-io[Mediabunny]) (type:engine)
            └ plugin-api (Effect/Transition/Exporter/Importer/RigRuntime/Analyzer 인터페이스 + Registry) (type:api)
                └ core/* (time[RationalTime], project-model[스키마], math, events) (type:core)
  plugins/effect-*, plugins/transition-gl-*, plugins/exporter-mp4, plugins/importer-otio, plugins/rig-spine(옵션), plugins/rig-live2d(옵션)
    → plugin-api (+ 필요한 engine의 공개 API)만 의존. plugin→plugin, plugin→feature/app 금지
  ```
- 강제 수단 조합(추정):
  1. pnpm workspaces와 Turborepo(또는 Nx)로 패키지 그래프·태스크 캐시를 관리한다.
  2. 패키지 수준 규칙은 Nx `depConstraints`로 건다(예: `type:core`는 `type:core`만, `type:plugin`은 `type:api`·`type:engine`만, `scope:server` 패키지는 `scope:web` 금지). Turborepo를 쓰면 `boundaries.tags`의 allow/deny로 건다. 실험적 기능이므로 CI에서 dependency-cruiser를 병행한다.
  3. 파일 수준 순환은 dependency-cruiser `no-circular`를 error로 두고 CI 필수 체크로 막는다. 패키지 내부 계층(예: `src/domain` → `src/infra` 금지)도 `forbidden` 규칙으로 막는다.
  4. 편집기 피드백은 eslint-plugin-boundaries `boundaries/dependencies`(default disallow)로 준다.
  5. project references는 순환을 컴파일 오류로 막는 장점이 있지만 Turborepo가 권하지 않는다. 그래서 Turborepo를 쓰면 "패키지별 tsc --noEmit + 경계 린트"로, Nx를 쓰면 Nx 방식을 따른다.
- 플러그인(의존성 역전) 규칙(추정):
  - `plugin-api`는 인터페이스, 매니페스트 스키마(id, version, 라이선스, 결정성 보장 여부, 지원 백엔드 WebGPU/WebGL2/server), 레지스트리만 둔다.
  - 구현은 플러그인 패키지에 두고, 등록은 앱의 composition root에서 한다(지연 로딩은 동적 import).
  - 익스포터(MP4/WebM/GIF/이미지 시퀀스)도 같은 인터페이스를 써서 클라이언트와 서버 렌더 워커가 공유한다.
  - Spine/Live2D처럼 라이선스가 걸린 런타임은 별도 플러그인 패키지로 격리해 계약 상태에 따라 빌드에서 넣고 뺄 수 있게 한다(§4).
- 엄밀한 의미의 "트리"(공유 노드 없음)는 공유 core 때문에 성립하지 않는다. "형제 간 import 금지 + 하향 단방향 + 순환 0"을 트리형 계층의 운영 정의로 삼는 것이 현실적이다(추정).

### Gaps
- pnpm의 워크스페이스 순환 금지 설정(예: `disallow-workspace-cycles`) 존재 여부는 이번 세션에서 확인하지 못했다(pnpm 12 패키지 CHANGELOG에서 미발견).
- TypeScript 7(네이티브)의 project references 완전 지원 여부, 그리고 Nx·Turborepo와의 호환성은 미확인이다.
- Nx와 Turborepo의 대규모 TS 모노레포 성능 비교, Sheriff의 태그·depRules 문법 세부는 1차 자료를 확보하지 못했다.
- `turbo boundaries`가 정식(GA)으로 전환될 시점은 미확인이다(문서상 Experimental, RFC 진행 중).
