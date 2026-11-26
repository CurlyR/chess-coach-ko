# 개발 소스 인수인계 (v1.3.10)

집에서 개발할 수 있는 원본 소스와 검수 배치, 재현 검사입니다. 실행 배포 파일은 저장소 루트의 coaches-ko.user.js이며, 이 소스 묶음은 별도로 압축을 풀어 사용합니다.

## 시작

1. 이 저장소의 chess-coach-ko-source-v1.3.10.zip을 내려받아 빈 작업 폴더에 풉니다.
2. Node.js와 npm을 준비합니다. 검수 배치 재생성이나 미리보기 서버에는 Python 3도 필요합니다.
3. 압축을 푼 폴더에서 아래 명령을 실행합니다.

```powershell
npm run setup
npm run build
npm run test:reported
python coach-corpus/translation/preview-server.py
```

마지막 서버 실행 후 http://127.0.0.1:8877/game/123456789 에서 두 코치와 말풍선 전환을 확인합니다. 이미 해당 포트가 사용 중이면 기존 서버인지 먼저 확인하세요. setup은 잠긴 의존성 버전으로 jsdom을 설치합니다. node_modules와 Python 가상환경은 포함하지 않았습니다.

## 주요 파일

- coach-corpus/translation/build-userscript.cjs: 사전 병합, 자동 검사, release 폴더 생성.
- userscript-runtime.js / reporter-runtime.js / context-runtime.js: 표시, 기록, 화면 식별.
- Magnus_coach.ko.jsonl / Hikaru_coach.ko.jsonl: 번역 소스. 빌드에 필요한 필드만 포함.
- reviewed-*.json, reviewed-puzzle-families.cjs: 검수한 번역과 기물별 변형.
- common-review/: 완료된 10,593문구의 검수 배치, 치환기, 기존 승인 보존 기준.
- test-*.cjs: 표시, 코치 식별, 문장 조합, 오류 기록, 회귀 검사.
- collector/receiver.gs: 개인 값이 없는 Apps Script 수신기 템플릿. 실제 수신 서버에 그대로 덮어쓰지 마세요.

common-review 배치를 바꾸었다면 python coach-corpus/translation/common-review/apply.py로 결과를 재생성합니다. 이 명령은 진행 파일의 배포 완료 표시를 초기화합니다. 기존 10,593문구 목록은 완료됐으므로 새 신고와 실제 재현에 집중하세요.

## 개인정보와 테스트 입력

received-*.json과 observations.json이라는 이름은 기존 빌드 호환을 위해 유지했습니다. 내용은 코치 원문만 남긴 재현 자료입니다. 원래 사건 시각, 계정, 상대 이름, 대국 URL, 시트 ID, 연결 비밀번호는 넣지 않았습니다. receiver.gs의 __PRIVATE_SHEET_ID__, __PRIVATE_TOKEN__은 테스트용 자리표시자이며 실제 연결값이 아닙니다. 실제 개인 연결은 기존 비공개 시트를 통해 설정합니다.

## 수정과 배포

새 번역을 승인 사전에 넣고 빌드와 재현 검사를 통과시킵니다. 버전은 build-userscript.cjs, userscript-runtime.js 및 README에서 함께 갱신합니다. name과 namespace는 유지하세요. 생성된 release/coaches-ko.user.js, release/coaches-ko.meta.js, release/README.md만 저장소 루트의 배포 파일로 교체합니다. 공개 raw 파일과 로컬 결과의 해시가 같은지 확인하세요. 사용자 브라우저에는 업데이트 확인 후 새로고침이 필요합니다.

소스를 수정한 다음에는 이 압축파일도 새 버전으로 갱신하거나, 압축을 푼 소스 트리를 Git으로 관리해 주세요. 저장소 루트의 배포 파일만 고치면 다음 원본 빌드에서 변경이 사라질 수 있습니다.

현재 소스는 공개 v1.3.10 사용자 스크립트와 동일한 바이트를 재생성하는 것을 확인하도록 준비했습니다. 배포 사전에 있는 문구가 영어로 나오면 구버전 실행, 중복 설치, DOM 탐지, 코치 식별을 먼저 확인합니다. “Can you find a way to win my queen?”는 이미 두 코치 사전에 있습니다.

이 파일은 개발 PC 전체 백업이 아닙니다. 원시 대규모 수집 자료, 로컬 모델, 기계번역 작업 DB, 개인 오류 로그와 자격 증명은 제외했습니다. 기존 두 코치 사전 수정, 빌드, 검수 배치 재생성, 테스트에는 포함된 소스를 사용합니다.

## 문장 조각과 로딩 문구 (v1.3.10)

fragment-assets.json에는 두 코치의 공개 원문에서 추린 조합용 키만 들어 있습니다. fragment-families-source.json과 fragment-families-ko.txt는 312개 행동 유형의 원문과 한국어입니다. 기물은 순서가 있는 자리표시자로 구분합니다. build-fragments.cjs가 조각 사전을 생성하며 build-userscript.cjs에서 자동 실행됩니다. 앞부분 44종과 코치별 행동 조각을 정확하게 연결하고, 검수된 여러 문장과도 합칩니다. 모르는 나머지는 숨기지 않습니다.

loading-phrases-source.json과 loading-phrases-ko.txt는 별도 리뷰 로딩 목록 58개입니다. reviewed-loading-overrides.json이 배포 사전에 들어갑니다. 원문에 있는 역사·통계 주장의 사실 검증을 의미하지는 않습니다. 새 조합 검사는 npm run build에 포함됩니다.


v1.3.10는 /daily 및 날짜별 일일 퍼즐 경로를 지원합니다. 실제 DOM으로 번역·기존 한국어·댓글 보존을 검사하는 test-daily.cjs가 빌드 검사에 포함됩니다. 일일 퍼즐의 확인된 solved 상태를 오류 기록에 보존합니다.


v1.3.10: test-nov26.cjs checks 230 reported/context/mixed-locale texts for both coaches.
