# NEXUS — Roblox Studio 연동 Luau 개발 도구 (Windows)

NEXUS에서 Luau 코드를 쓰고 → 열려 있는 Roblox Studio place에 적용하고 → Studio 안에서 Play·Run 테스트를 실행한 뒤 → 결과와 오류를 NEXUS Console에서 확인하는 데스크톱 프로그램입니다. 2.1부터는 같은 Test 버튼으로 **게시한 내 게임을 일반 Roblox Player 에서** 테스트할 수도 있어요 (아래 2.1).

```
NEXUS (Windows 앱)  ⇄  127.0.0.1:34873  ⇄  NEXUS Connector (Studio 플러그인)  ⇄  Roblox Studio 의 place
```

- **연동 방식:** Roblox가 공식으로 제공하는 플러그인 API를 씁니다.
  - `HttpService`(로컬 요청), `ScriptEditorService`, `ChangeHistoryService`, `StudioTestService`, `LogService`
  - 파일 구조는 Rojo와 호환돼요.
- **접근 범위:** 내 Studio에 열려 있는 place, 그리고 (2.0부터) **내 API 키로 접근할 수 있는 내 게임**만 다룹니다. 다른 사람의 게임에는 접속하지 않아요. 클라이언트 주입이나 보안 우회 기능도 없습니다.

---

## 1. 설치와 실행

### 바로 실행 (권장)
1. `NEXUS-Portable-1.0.0.exe`를 받아서 실행합니다. 설치가 필요 없어요.
2. 서명되지 않은 프로그램이라 Windows SmartScreen이 경고할 수 있어요. **추가 정보 → 실행**을 누르세요.

### 소스에서 실행 / 직접 빌드
Node.js 20 이상이 필요해요.
```
npm install
npm start          # 개발 모드로 실행
npm run dist       # Windows 에서: 설치 프로그램(NEXUS-Setup) + 포터블 EXE 생성
```

### 필요한 것
- Windows 10/11
- Roblox Studio (설치 + 로그인)

---

## 2. 처음 연결하기 (한 번만)

1. NEXUS 상단의 **Connect Studio** → **설치**를 누릅니다.
   - `%LOCALAPPDATA%\Roblox\Plugins\NexusConnector.lua`에 플러그인이 복사돼요.
2. Roblox Studio를 엽니다. NEXUS의 **Studio 실행** 버튼을 써도 돼요. 이미 열려 있었다면 다시 시작하세요.
3. Studio 상단 **플러그인** 탭 → **NEXUS** 버튼 → **NEXUS에 연결**을 누릅니다.
   - 처음에는 Studio가 **HTTP 요청 허용**을 물어요. `127.0.0.1`(내 컴퓨터)을 허용하세요.
4. NEXUS에 **연결 요청** 창이 뜨면 place 이름을 확인하고 **허용**을 누릅니다.
   - 내 place이거나 개발 권한이 있는 place일 때만 허용하세요.

연결되면 상단 표시줄에 초록 점과 place 이름이 보여요.

---

## 3. 작업 흐름

| 단계 | NEXUS 에서 | 일어나는 일 |
|---|---|---|
| 1 | **Open Project** / 새 프로젝트 | 내 컴퓨터 폴더를 프로젝트로 엽니다. 기존 Rojo 프로젝트 폴더도 열려요. |
| 2 | **Connect Studio** | Studio 플러그인과 연결 |
| 3 | 코드 작성 · **Save** (Ctrl+S) | `src/` 아래 `.luau` 파일로 저장 |
| 4 | **Apply to Studio** (Ctrl+Shift+A) | Studio의 현재 스크립트와 비교한 **미리보기(diff)** 를 보여 줌 → 고른 파일만, **원본을 백업한 뒤** 적용 |
| 5 | Studio에서 확인 | 탐색기에 스크립트가 생기거나 바뀜. **Studio에서 보기** 버튼으로 해당 스크립트를 Studio 편집기에 바로 열 수 있어요. |
| 6 | **Test in Studio** (F5) | Studio의 **Play** 또는 **Run** 테스트가 실제로 시작돼요. 게임 화면은 Studio 창에서 봅니다. |
| 7 | Console | 테스트 중 Studio의 서버·클라이언트 출력, 경고, 오류가 시간과 함께 들어와요. 오류 위치를 누르면 해당 파일·줄로 이동해요. |
| 8 | **Stop Test** (Shift+F5) | Studio 테스트를 종료하고 NEXUS로 돌아와 수정 |

- **테스트 전 적용:** Test in Studio를 누를 때 Studio에 아직 적용하지 않은 변경이 있으면 먼저 적용할지 물어봐요. 설정에서 바꿀 수 있어요.
- **기존 place에서 시작:** 탐색기의 **Studio에서 가져오기** 버튼으로 기존 스크립트를 내 프로젝트로 불러올 수 있어요.

---

## 4. 안전장치 (기존 프로젝트를 함부로 덮어쓰지 않기)

- **미리보기 필수:** Studio에 쓰기 전에 항상 파일별 변경 내용을 보여 주고, 내가 체크한 파일만 적용해요.
- **자동 백업:**
  - 적용하기 직전 Studio 쪽 원본을 `프로젝트/.nexus/backups/<시간>-apply/`에 저장해요.
  - 기록 · 백업 패널에서 **이 적용 전으로 Studio 되돌리기**를 누르면 그 시점으로 돌아가요.
- **Studio 되돌리기 (Ctrl+Z):** 적용은 Studio의 실행 취소 기록 하나로 묶여요. Studio에서 Ctrl+Z로 한 번에 되돌릴 수 있어요.
- **충돌 보호:** Studio에 같은 이름의 다른 종류 개체(예: Part, 종류가 다른 스크립트)가 있으면 덮어쓰지 않고 건너뛰어요.
- **그사이 바뀐 내용 보호:** 미리보기 이후 Studio에서 그 스크립트가 바뀌었다면 적용을 멈추고 다시 미리보기 하라고 알려요.
- **다른 place 경고:** 프로젝트가 처음 적용한 place와 지금 Studio에 열린 place가 다르면 경고하고, 확인란을 체크해야 적용돼요.
- **삭제는 Studio에 반영 안 함:** NEXUS에서 파일을 지우거나 이름을 바꿔도 Studio의 스크립트는 지우지 않아요. 지운 파일은 `.nexus/trash`로 옮겨져서 되살릴 수 있어요.
- **가져오기 전 백업:** Studio에서 가져올 때 내 파일이 덮어써지면, 그 파일도 먼저 백업해요.

---

## 5. 파일 규칙 (Rojo와 같음)

```
프로젝트/
├─ nexus.project.json          프로젝트 정보, 연결된 place
├─ default.project.json       Rojo 구성 (자동 생성)
├─ src/
│  ├─ ServerScriptService/Main.server.luau           → Script
│  ├─ StarterPlayer/StarterPlayerScripts/UI.client.luau → LocalScript
│  ├─ ReplicatedStorage/Shared/Config.luau          → ModuleScript (Shared 폴더 안)
│  └─ ServerScriptService/Game/init.server.luau     → 자식이 있는 Script "Game"
└─ .nexus/  backups/ · trash/ · history.json
```

`src/` 다음 첫 폴더는 Studio의 서비스 이름이어야 해요:
`ServerScriptService`, `ReplicatedStorage`, `ReplicatedFirst`, `ServerStorage`, `StarterPlayer/StarterPlayerScripts`, `StarterPlayer/StarterCharacterScripts`, `StarterGui`, `StarterPack`, `Workspace` 등

---

## 6. 기능 목록

- **상단 표시줄:** 프로젝트 이름, Studio 연결 상태(연결됨 / 승인 대기 / 테스트 중)와 place 이름, Connect Studio, Apply to Studio, Test in Studio(Play/Run 선택), Stop Test
- **왼쪽 사이드바:**
  - 탐색기: 서비스별 트리, New Script, 새 폴더, 파일 열기, Studio에서 가져오기, 이름 변경, 삭제
  - 프로젝트 전체 검색: 정규식, 대소문자 구분
  - 기록 · 백업: 적용, 가져오기, 테스트 기록과 되돌리기
- **가운데 편집기:**
  - Luau 문법 강조, 줄 번호, 여러 탭, 저장 표시
  - 자동 완성(Ctrl+Space): 키워드, 라이브러리, Roblox 서비스·메서드·Enum, 코드 틀
  - 자동 들여쓰기·내어쓰기, 괄호 자동 닫기
  - 찾기·바꾸기, 들여쓰기 정리, 주석
  - 오류 표시: 실제 Luau 컴파일러 기준, 줄 번호 옆 표시와 밑줄
- **오른쪽 정보 패널:** 프로젝트, 연결된 place, Studio 연결·플러그인 상태, Studio 실행, 테스트 상태(오류·경고 수), 최근 작업
- **하단 Console:**
  - 적용 결과, 테스트 출력(서버/클라이언트 구분), 오류, 경고, 실행 기록, 시간 표시
  - 로그 검색, 필터, 지우기, 자동 스크롤
- **Settings:**
  - 테마, 글자 크기, 줄 간격, 탭/공백, 자동 완성, 괄호 자동 닫기
  - 테스트 전 적용 방식, Studio 경로
  - 플러그인 재설치, 자동 연결을 허용한 place 관리
- **Studio 실행:**
  - 설치된 Studio 자동 탐색
  - 공식 명령줄 옵션으로 place 열기: `--task EditPlace --placeId … --universeId …`
  - `.rbxl` 파일 열기

---

---

## AI 스크립트 (무료 · 내 PC)

왼쪽 사이드바의 **반짝이 아이콘**(또는 Ctrl+I)을 누르면 AI 패널이 열려요. 원하는 기능을 한국어로 적으면 AI가 Luau 스크립트를 만들어 줘요.

- **비용 없음:** 클라우드 AI가 아니라 **내 PC에서 돌아가는 무료 AI(Ollama)**를 써서 크레딧이나 요금이 들지 않아요.
- **처음 한 번만 준비:**
  1. **Ollama 받기**를 눌러 Ollama를 설치해요. (https://ollama.com/download)
  2. 모델을 골라 **모델 받기**를 눌러요. 처음 한 번만 받으면 인터넷 없이도 써요.
     - 보통(추천): `qwen2.5-coder:7b`, 4.7GB, 그래픽카드 메모리 8GB 이상
     - 가벼움: `qwen2.5-coder:3b`, 1.9GB
     - 똑똑함: `qwen2.5-coder:14b`, 9GB
- **쓰는 법:**
  1. 기능을 적고 **스크립트 만들기**(Ctrl+Enter)를 눌러요.
  2. 결과를 보고 **새 스크립트로 저장**을 누르면 AI가 고른 종류와 위치로 자동 저장돼요.
  3. **Apply to Studio**로 Studio에 넣어요.
- **결과 버튼:**
  - **이어서 요청:** "속도 더 빠르게"처럼 앞 결과를 고칠 수 있어요.
  - **지금 열린 파일 참고:** 현재 파일을 보고 그에 맞게 고쳐 줘요.
  - **AI에게 오류 고치게 하기:** 만든 코드에 구문 오류가 있으면 보여요. 누르면 다시 고쳐 줘요.
- **한계:**
  - 내 PC의 작은 모델이라 Claude 같은 큰 AI보다 실수가 많아요. 꼭 Studio에서 테스트하세요.
  - 실행기(익스플로잇) 코드는 만들지 않아요. 섞여 나오면 경고를 보여 줘요.

---

## 2.1 Universal Runtime Testing (Studio + 일반 Roblox Player 를 한 버튼으로)

2.0 의 Player 통로 위에, **Test 버튼 하나로 Studio 와 Player 를 오가며 테스트**하도록 묶었어요. Studio 연결(127.0.0.1:34873 · 플러그인 1.3.0)은 그대로예요.

```
              ┌─ Test in Studio (Play · Run · 여러 명) ── 플러그인 ⇄ 127.0.0.1:34873 ──┐
 NEXUS 편집기 ─┤                                                                       ├─▶ Runtime Logs · Test Results (같은 화면 · 같은 실행 기록)
              └─ Test in Player ── Open Cloud 메시지 ▶ 내 게임 서버 ▶ 내 클라이언트 ────┘
                  ◀ DataStore(상태 · 결과 · 로그)  ◀ 이 PC 의 Roblox 로그(내 클라이언트 출력, 실험적)
```

### 조사: 일반 Roblox Player 와 통신하는 방법 비교
| 방법 | 방향 | 속도 (예상) | 공식 여부 | NEXUS |
|---|---|---|---|---|
| Open Cloud Messaging → `MessagingService` | NEXUS → 게임 서버 | 약 1초 | 공식 | **명령 · 테스트 이름 보내기** |
| DataStore ← Open Cloud | 게임 서버 → NEXUS | 몇 초 | 공식 | **상태 · 결과 · 서버 로그** |
| Open Cloud Assets API (`PATCH /assets/v1/assets/{placeId}`) | NEXUS → 내 place | 게시 한 번 | 공식 | **스크립트 테스트 place 게시 (실험적)** |
| 내 PC 의 Roblox 로그 파일 (`[FLog::Output]` 줄) | 내 클라이언트 → NEXUS | 약 1초 | 비공식(읽기만 · BloxstrapRPC 가 쓰는 방식) | **내 게임일 때만 내 클라이언트 출력 (실험적)** |
| 딥링크 `roblox://placeId=…` · 프로세스 목록 | NEXUS → Player | 즉시 | 공식 / 내 PC | 실행 · 실행 감지 |
| `127.0.0.1` HTTP · WebSocket | — | — | Player 에서는 불가 (클라이언트는 HttpService 금지, 서버는 Roblox 데이터센터) | Studio 에서만 |
| 게임 서버 `HttpService` → 내 PC | 게임 서버 → NEXUS | 빠름 | 공식이지만 내 PC 를 인터넷에 열어야 함 | 만들지 않음 (위험) |
| 임의 Luau 를 라이브 게임에 보내 실행 (`loadstring`) | — | — | 키가 새면 백도어 | **만들지 않음** → 대신 "등록된 테스트를 이름으로 실행" |
| Player 프로세스에 코드 넣기 · 메모리 변조 · 보안 우회 | — | — | 금지 | **만들지 않음** (남의 게임에서는 실행 감지 · place 번호까지만) |

**Studio 와 Player 의 런타임 차이:** Studio 는 플러그인이 있어서 코드 적용 · 실시간 로그 · Play/Stop 을 바로 할 수 있어요. Player 는 플러그인이 없고 게임이 Roblox 서버에서 돌아서, 코드는 **게시**로만 들어가고 NEXUS 는 게시된 게임 안의 테스트 클라이언트와 Open Cloud 로 이야기해요. `MessagingService` · `DataStore` 는 라이브 서버에서 그대로 동작하지만 Studio 테스트에서는 NEXUS 의 명령이 닿지 않아요(그래서 Studio 에서는 자동 실행 + `]` 패널).

### 새로 된 것
| 기능 | 어디서 | 설명 |
|---|---|---|
| **테스트 환경 전환** | 상단 Test 버튼의 ▾ · 오른쪽 "테스트" 카드 | Play · Run · 여러 명 · **Roblox Player**. 고른 환경은 프로젝트별로 기억. F5 · Stop(Shift+F5)도 고른 환경을 따라가요 |
| **Test in Player** | 상단 버튼 / Player Connection | 실행 기록을 열고 → 이미 내 게임에 들어가 있으면 그 세션에 연결, 아니면 Roblox Player 실행 → 세션을 3분까지 기다림 → 로그 · 결과가 Runtime Logs · Test Results 에 쌓임 |
| **테스트 끝내기** | Stop Test | 게임에 `end` 를 보내 **개발자 본인의 접속만** 끊어요(`Player:Kick`). Roblox 프로그램을 강제로 끄지 않아요. 세션이 끝나면 실행 기록이 닫히고 `.nexus/runs` 에 저장 |
| **스크립트 테스트 (NexusTest)** | 내 스크립트 + Player Runtime 카드 | 아래 참고. 서버 · 클라이언트 테스트를 **이름으로** 실행 요청하고 통과/실패 · 이유 · 걸린 시간을 받아요 |
| **스크립트 테스트 place 게시** (실험적) | Player Connection | Studio 없이: 바닥 + 프로젝트 스크립트만 든 `.rbxlx` 를 만들어 내 place 에 게시 |
| **내 클라이언트 출력 바로 읽기** (실험적) | Player Runtime 카드의 체크 | 들어간 place 가 내 게임으로 확인됐을 때만, 이 PC 의 Roblox 로그에서 print · 경고 · 오류를 약 1초 간격으로 읽어요 (F9 콘솔과 같은 내용) |
| 결과 확인 시간 | 결과 표 | 명령을 보낸 뒤 결과를 확인하기까지 걸린 **실제 시간**을 표시 |

### 스크립트 테스트 쓰는 법
```lua
-- 서버 Script 에서 등록하면 서버 테스트, LocalScript 에서 등록하면 클라이언트 테스트
local NexusTest = require(game:GetService("ReplicatedStorage").Systems.NexusTest)

NexusTest.register("기본 걷기 속도는 16", function(t)
	local player = game:GetService("Players"):GetPlayers()[1]
	local humanoid = player.Character:WaitForChild("Humanoid", 5)
	t.check(humanoid ~= nil, "Humanoid 를 찾지 못했어요")   -- 조건 확인
	t.equal(humanoid.WalkSpeed, 16)                          -- 값 비교
	-- t.near(값, 기대, 허용오차) · t.log("메모") · return false, "이유" 도 가능
end)
```
- **Studio:** Test in Studio 를 누르면 3~4초 뒤 자동 실행 → Test Results 의 "기능 테스트 결과" 표 (`LiveTestConfig.AUTO_RUN_TESTS_IN_STUDIO`)
- **Player:** Test in Player 가 연결되면 자동 실행(끌 수 있음) · 카드에서 한 개씩 다시 실행
- **안전:** NEXUS 는 테스트 **이름만** 보내요. 실행되는 함수는 내가 게시한 코드뿐이고, 서버가 확인한 개발자 세션에서만 돌아요. 이름 1~40바이트 · 40개까지 · 한 개 10초 제한
- 오류로 실패하면 `스크립트:줄` 이 보이는 오류 로그도 남아서 Runtime Logs 에서 그 줄로 바로 갈 수 있어요

### 스크립트 테스트 place 게시 (실험적)
- **무엇을:** `src/<Service>/…` 의 스크립트 전부 + 바닥(Baseplate) + 시작 지점(SpawnLocation) + 조명. **맵 · 모델은 없어요** → 그런 테스트는 Studio 에서 게시한 place 로 하세요.
- **어디에:** Player Runtime 카드의 "대상 place". 테스트 전용 experience 를 하나 만들어 쓰는 것을 권해요(비공개로). Studio 에 연결했던 내 게임 place 를 고르면 한 번 더 경고해요.
- **안전장치:** 게시 전 확인 창 · 올린 파일을 `.nexus/published/` 에 5개까지 보관 · 이전 버전은 Roblox Creator Hub 의 버전 기록에서 되돌릴 수 있어요.
- **권한:** API 키에 **assets 의 Read · Write** 를 더하세요.

### 한계 · 검증
- ✓ 서버 스크립트 + NexusTest 원본을 Luau VM(가짜 Roblox 서비스)에서 실행: **66개** (권한 · 허용 목록 · 테스트 통과/실패/오류/시간 초과 · 이름 규칙 · 끝내기 등)
- ✓ 메인 모듈: **28개** (명령 허용 목록 · 새 로그/결과만 알리기 · 게시 요청 형식 · 로그 줄 읽기) · Open Cloud: 15개
- ✓ place 파일: **13개** — 실제 **Rojo 7.4.4** 가 만든 place 와 트리가 같고, 실제 rbx-dom 파서(**Lune 0.8.9**)가 읽음
- ✓ 앱 전체: **38개** (환경 전환 → 게시 → Test in Player → 스크립트 테스트 → Stop → 실행 기록) + 2.0 흐름 30개 — 실제 NEXUS ↔ 가짜 Open Cloud ↔ 실제 서버 스크립트
- ✗ **실제 Roblox 서버 · 실제 Open Cloud · 실제 Roblox Player 에서는 실행하지 못했어요.** 특히 ① place 게시 API 의 실제 응답 ② Roblox 로그 줄 형식(`[FLog::Output]`, 추정) ③ 화면 쪽 테스트 클라이언트(LocalScript)의 실제 동작은 첫 실행에서 확인이 필요해요.
- 2.0 에서 라이브 테스트 클라이언트를 이미 넣었다면: Script Library 에서 지우고 다시 추가해야 새 명령(`test` · `end`)과 NexusTest 가 들어가요 (내가 고친 파일은 덮어쓰지 않아요).
- 결과는 여전히 몇 초 늦어요. 로그는 최근 80줄 · 한 줄 300바이트(결과 줄은 600)까지예요.

## 2.0 Player Runtime (Roblox Player 로 연 내 게임 테스트)

Studio 연결(127.0.0.1:34873 · `NexusConnector.lua` 1.3.0 · 승인 · 요청 대기열)은 **한 줄도 바꾸지 않았어요.** Player 쪽은 완전히 따로 만든 두 번째 통로예요.

```
[Studio 개발]   NEXUS ⇄ 127.0.0.1:34873 ⇄ Studio 플러그인 ⇄ 편집 중인 place            (실시간, 기존 그대로)
[Player 테스트] NEXUS ──Open Cloud 메시지──▶ 내 게임 서버(Script) ──RemoteEvent──▶ 테스트 클라이언트(LocalScript)
                NEXUS ◀──Open Cloud DataStore 읽기── 내 게임 서버가 남긴 상태 · 결과 · 로그   (몇 초 늦음)
```

### 먼저 조사한 것: Roblox Player 와 공식으로 통신할 수 있는 방법
| 방법 | Player(라이브)에서 | NEXUS 가 쓰는가 |
|---|---|---|
| `127.0.0.1` HTTP (Studio 플러그인 방식) | ✗ 클라이언트(LocalScript)는 `HttpService` 요청을 못 보내고, 라이브 게임 서버는 Roblox 데이터센터에 있어서 내 PC 에 닿지 않아요 | Studio 에서만 (기존) |
| 게임 서버의 `HttpService` → 내 PC | ✗ 내 PC 를 인터넷에 여는 서버·터널이 필요해요. 안전하지 않아서 만들지 않았어요 | 안 씀 |
| **Open Cloud Messaging** `POST /cloud/v2/universes/{id}:publishMessage` | ✓ 내 API 키로 **내 게임의 라이브 서버**에 메시지(1,024자) 전달 → `MessagingService:SubscribeAsync` | **명령 보내기** |
| **Open Cloud DataStore** `GET …/data-stores/{name}/entries/{id}` | ✓ 게임 서버가 저장한 값을 밖에서 읽기 | **상태 · 결과 · 로그 받기** |
| `RemoteEvent` / `RemoteFunction` | ✓ 게임 안에서 서버 ↔ 클라이언트 | 서버 → 테스트 클라이언트 명령, 클라이언트 → 서버 응답·로그 |
| 딥링크 `roblox://placeId=…&launchData=…` | ✓ 실행과 짧은 시작 값(200바이트, 믿을 수 없는 값) | 실행 (1.6) |
| 프로세스 목록 · Roblox 로그 파일 | △ 실행 여부, 들어간 place 번호 | 실행 감지 (1.6) |
| Player 프로세스에 코드 넣기 · 메모리 읽기/쓰기 · 보안 우회 | ✗ 공식 방법이 아니고 금지 | **만들지 않음** |

**Studio 와 Player 의 차이:** Studio 는 플러그인이 내 PC 안에서 NEXUS 와 바로 이야기해서 코드 적용·실시간 로그가 돼요. Player 는 플러그인이 없고 게임이 Roblox 서버에서 돌아요. 그래서 **코드는 Studio 에 적용 → 게시(Publish)** 로만 라이브에 들어가고, NEXUS 는 게시된 게임 안의 테스트 클라이언트에게 **정해진 명령**을 보내고 결과를 읽어요. 기존 통신 구조에서 그대로 다시 쓴 것은 결과 형식(`[NexusResult]`)·Runtime Logs·Test Results 화면·Script Library·API 키 저장이에요.

### 요청하신 기능별 결과
| 기능 | 결과 | 방법 |
|---|---|---|
| Roblox Player 실행 감지 | ✓ | 프로세스 목록 (1.6) |
| 실행 중인 게임 확인 | △ | Roblox 로그의 place 번호 + 라이브 세션의 `placeId` 가 내 프로젝트의 place 와 같은지 표시 ("내 게임?") |
| 게임 세션 상태 표시 | ✓ | 게임 서버가 `NexusLive/session_<UserId>` 에 7초마다(변화 있을 때) · 30초마다(신호) 기록 → 연결됨 / 신호 없음 / 끝남 |
| NEXUS ↔ 테스트 게임 상태 전달 | ✓ | 속도 · 점프 · 비행 · 카메라 · 체력 · place 버전 · 서버가 잰 최고 속도/높이 |
| 테스트 명령 전달 | ✓ | Open Cloud 메시지 (주제 `NexusLiveTest`) |
| 테스트 결과 수신 | ✓ | 명령마다 id → 결과 표에서 "응답 대기 → 성공/실패" |
| 오류 및 로그 수집 | ✓ | 서버 출력 전체 + **내(개발자) 클라이언트** 출력 → 최근 80줄 → Runtime Logs 에 `LIVE` 표시 |
| 임의 Luau 코드를 라이브 게임에 보내 실행 | ✗ 만들지 않음 | 게임 안에서 임의 코드를 실행하는 통로는 키가 새면 그대로 백도어가 돼요. **대신:** NEXUS 에서 코드 작성 → Apply to Studio → 게시 → 테스트 명령으로 확인 |
| 다른 서버에 있는 다른 사람 대상 명령 | ✗ | 명령은 서버가 확인한 **개발자 본인**에게만 적용돼요 |

### 게임 안 테스트 클라이언트 (Script Library → 라이브 테스트 클라이언트)
파일 3개: `ReplicatedStorage/Systems/LiveTestConfig` · `ServerScriptService/Systems/LiveTest/LiveTestServer` · `StarterPlayerScripts/Systems/LiveTest/LiveTestClient`

| 테스트 | 명령 | 누가 실행 | 결과로 돌아오는 것 |
|---|---|---|---|
| 이동 속도 | `speed <숫자>` | 서버 (`Humanoid.WalkSpeed`, 0~`MAX_SPEED` 120) | 적용된 값 + 서버가 잰 최고 속도 |
| 점프력 | `jump <숫자>` | 서버 (`JumpPower`, 0~`MAX_JUMP` 200) | 적용된 값 + 서버가 잰 최고 높이 |
| 비행 | `fly 켬/끔` | 클라이언트 (LinearVelocity · WASD/Space/Ctrl) | 클라이언트 확인 `active` |
| 대시 | `dash` | 클라이언트 | **서버가 잰** 0.6초 동안 이동 거리 (3 이상이면 성공) |
| 카메라 | `camera 기본/어깨/위/1인칭` | 클라이언트 | `mode` · `cameraType` · `fov` |
| UI | `ui <글자>` | 클라이언트 (화면 위 배너 3초) | `shown` · 화면 크기 |
| 그 밖에 | `heal` `respawn` `reset` `ping` `report` | 서버 | |

- **개발자 인증:** 서버가 판단해요 — 게임 주인 · `ALLOWED_USER_IDS` · 그룹 게임이면 `MIN_GROUP_RANK`(기본 254) 이상 · Studio 테스트. 통과한 사람에게만 세션·패널이 생기고, 나머지는 아무것도 안 보여요.
- **서버가 막는 것:** 목록에 없는 명령 · 범위 밖 값(잘라냄) · 120초 넘게 오래된 명령 · 같은 id 두 번 · 1,024자 넘는 메시지 · 다른 사람의 응답/로그 · 너무 잦은 요청.
- **NEXUS 가 막는 것:** 목록에 없는 명령과 형식이 다른 값은 아예 보내지 않아요 (`live:send` 허용 목록).
- 게임 안에서도 `]` 키 패널로 같은 테스트를 할 수 있어요 (같은 서버 검사를 거쳐요).

### 쓰는 순서
1. Script Library → **라이브 테스트 클라이언트** 추가 → Apply to Studio → Studio Play 로 `]` 패널 확인
2. Studio 에서 **게시(Publish)** — 직접 (게임 설정 → Security 의 "Enable Studio Access to API Services" 는 Studio 테스트에서 기록까지 보고 싶을 때만)
3. create.roblox.com → Credentials → API 키: **이 게임에만** `universe-messaging-service:publish`, `universe-datastores.objects:list`, `universe-datastores.objects:read`
4. NEXUS → Player Connection → "Open Cloud 연결" 에 키 붙여넣기 (Windows 사용자 암호화로 이 PC 에만 저장) · universe ID 는 Studio 연결 시 자동, 없으면 직접 입력(프로젝트별 저장)
5. **Launch Roblox** → 게임에 들어가면 약 10초 안에 "Player Runtime" 카드에 세션이 떠요 → 명령 버튼 → 결과 표 · Runtime Logs

### 한계 (숨기지 않고)
- 실시간이 아니에요. 게임 서버는 기록을 7초 간격으로 묶어서 저장하고(`WRITE_INTERVAL`) NEXUS 는 3~6초마다 읽어서, 결과가 **몇 초~십몇 초** 늦게 보여요 (실제 Roblox 서버에서 잰 값이 아니라 설정값으로 계산한 예상이에요).
- 로그는 최근 80줄 · 한 줄 300바이트까지. 아주 빨리 쏟아지는 출력은 일부가 빠질 수 있어요. 전체 로그는 게임 안 F9 나 Studio 테스트(Runtime Logs)를 쓰세요.
- 클라이언트 로그는 **개발자 본인 기기** 것만 와요. 다른 플레이어의 클라이언트 로그는 모으지 않아요.
- Open Cloud 요청 수 제한이 있어요. 자동 읽기는 6초마다(대기 중인 명령이 있으면 3초마다)이고 "자동" 을 끌 수 있어요.
- API 키가 새면 그 키의 권한(이 게임에 테스트 명령 보내기 · DataStore 읽기)만큼 쓸 수 있어요. 그래서 명령 목록을 고정했고, 대상은 서버가 확인한 개발자뿐이에요.

### 검증한 것 / 못 한 것
- ✓ 서버 스크립트 원본을 Luau VM(가짜 Roblox 서비스)에서 실행: 39개 검사 (권한 · 허용 목록 · 값 제한 · 중복/오래된 명령 · 응답 정리 · 서버 측정 · 로그 제한 · 한글 자르기)
- ✓ Open Cloud 모듈: 15개 검사 (보내는 주소 · 헤더 · 본문 · 오류 안내 — 가짜 https)
- ✓ 앱 전체: 30개 검사 — 실제 NEXUS ↔ 가짜 Open Cloud ↔ **실제 서버 스크립트** 를 이어서 버튼 → 명령 → 게임 상태 변화 → 결과 표 → Runtime Logs 까지
- ✗ **실제 Roblox 서버 · 실제 Open Cloud · 실제 Player 에서는 실행해 보지 못했어요.** 테스트 클라이언트(LocalScript)는 컴파일 검사만 했어요(화면·비행·카메라는 실제 엔진이 필요). 처음 쓸 때 문제가 생기면 Runtime Logs 와 게임 안 F9 내용을 알려 주세요.

## 1.7 내 게임 기능 테스트: 걷기 속도 (Studio + 라이브)

**Script Library → 걷기 속도 테스트** (개발자 전용, 외부 코드 주입 없음 — 내 게임 안의 Script/LocalScript)

| 항목 | 내용 |
|---|---|
| 기본 속도 | `SpeedTestConfig.DEFAULT_SPEED = 32` (끄면 16) · 범위 8~100 · −/+ 4씩 |
| ON/OFF · 조절 | 화면 왼쪽 아래 패널. 요청은 RemoteFunction 으로 서버에 가고, **서버가 권한·범위를 확인한 뒤** Humanoid.WalkSpeed 를 바꿔요 |
| 누가 | Studio 테스트에서는 누구나 · 라이브 게임에서는 게임 주인, `ALLOWED_USER_IDS`, 그룹 `MIN_GROUP_RANK` 이상만 (다른 사람에겐 패널도 안 생김) |
| 실제로 작동하는지 | **서버가 직접** 캐릭터의 수평 속도를 재요 (HumanoidRootPart, 바닥에 있을 때). [결과 기록] → 목표의 85% 이상이면 통과 |
| NEXUS 에서 결과 (Studio) | 게임이 `print("[NexusResult] {json}")` → 플러그인 → **Test Results 의 "기능 테스트 결과" 표**, Runtime Testing 카드, Runtime Logs |
| NEXUS 에서 결과 (라이브 Player) | 게임이 DataStore `NexusTestResults` 에 저장 → NEXUS 가 **Roblox Open Cloud v2 (공식 API)** 로 읽음 (Player Connection → 라이브 게임 테스트 결과) |

**라이브 결과를 보려면 (한 번만):** create.roblox.com → Credentials → API 키 만들기 → 이 게임에 `universe-datastores.objects:list`, `universe-datastores.objects:read` 만 주기 → NEXUS 에 붙여넣기. 키는 Windows 사용자 암호화(DPAPI)로 `%APPDATA%\NEXUS\opencloud.key` 에만 저장돼요. universe ID 는 게시한 place 를 Studio 에서 NEXUS 에 연결하면 자동으로 채워져요.

**순서:** 추가 → Apply to Studio → Test in Studio(Play)로 걸어 보고 [결과 기록] → NEXUS Test Results 확인 → Studio 에서 게시 → Player Connection → Launch Roblox → 라이브에서 [결과 기록] → NEXUS 에서 "결과 불러오기"

**할 수 없는 것:** 라이브 게임과 실시간 연결(그래서 DataStore 를 거쳐요, 몇 초 늦음) · 남의 게임에서 속도 바꾸기(내 게임 서버 스크립트만 바꿀 수 있어요).

그 밖에: 실행기용 코드(`game:HttpGet`, `loadstring` 등)를 문제 탭·테스트 전 확인에서 경고 · 스크립트 0개인 place 에서 Script Explorer 가 "비교 안 됨" 이라고 잘못 말하던 것 고침.

## 1.6 Player Companion (일반 Roblox Player)

Studio 연결(127.0.0.1:34873, 플러그인, 승인, 대기열, Script Explorer, Apply, Play/Run, Runtime Logs)은 **그대로** 두고, 왼쪽 메뉴에 **Player Connection** 을 따로 추가했어요.

### Roblox 가 공식으로 허용하는 범위 (먼저 확인한 것)
| 기능 | 가능 | 방법 / 이유 |
|---|---|---|
| Roblox Player 실행 | ✓ | Roblox 공식 딥링크 `roblox://placeId=<id>&launchData=<data>` (웹사이트의 "플레이" 와 같은 경로) |
| launchData 넘기기 | ✓ | 200바이트까지, URL 에 그대로 보여서 **권한 확인에는 못 씀** → 게임 서버가 다시 확인 |
| 실행 중 / 종료 감지 | ✓ | 내 PC 의 프로그램 목록(`tasklist`)에서 `RobloxPlayerBeta.exe` 를 3초마다 확인 |
| 들어간 place 번호 | △ | 내 PC 의 Roblox 로그 파일에서 `place <번호>` 만 읽음 (공식 API 아님 · IP 같은 정보는 읽지도 저장하지도 않음) |
| 실행 오류 알림 | ✓ | 링크를 못 열 때, 45초 안에 실행되지 않을 때 |
| Player 안의 게임과 **직접** 통신 | ✗ | 클라이언트는 HttpService 를 못 쓰고, 라이브 게임 서버는 Roblox 서버에서 돌아서 내 PC(127.0.0.1)에 닿지 않아요. → **2.0 부터 Open Cloud 를 거쳐서** 내 게임과 명령·결과를 주고받아요 (위 2.0) |
| Player 의 print/오류를 NEXUS 로 받기 | △ | 2.0 부터 **내 게임에 라이브 테스트 클라이언트를 넣으면** 서버·내 클라이언트 로그(최근 80줄)가 몇 초 늦게 와요. 그 밖에는 게임 안 F9 |
| Roblox 강제 종료 · 클라이언트 변조 · 다른 사람 게임에 코드 넣기 | ✗ | 하지 않아요 |

### Player Connection 화면
실행 상태(실행 중 시간·프로세스 수) · **Launch Roblox** · 게임 페이지 · Roblox 홈 · 지금 실행 정보(NEXUS 에서 실행했는지, 들어간 place) · 최근 실행 기록(실행/종료/실패, `%APPDATA%\NEXUS\player-history.json`) · 오류 · 설치/roblox:// 등록 상태 · 가능/불가능 표.
place ID 는 열린 프로젝트에 연결된 **게시된** place 가 기본이에요 (저장만 한 로컬 place 는 Player 로 열 수 없어요).

### 내 게임 전용 개발자 테스트 메뉴 (Script Library → 개발자 테스트 메뉴)
- **누가 쓸 수 있나:** Studio 테스트에서는 누구나 · 라이브 게임에서는 **게임 주인**, `DevMenuConfig.ALLOWED_USER_IDS` 의 UserId, 그룹 게임이면 `MIN_GROUP_RANK` 이상만
- **보안:** 권한은 서버의 표로 판단하고, 메뉴의 모든 요청(RemoteFunction)을 서버가 매번 다시 확인 · 값 범위 제한 · 2초에 20번까지만
- **기능:** 시스템별 켜기/끄기(비행·달리기·대시·다단 점프·카메라·전투·충격파·HUD — 서버가 `NexusOff_<id>` 속성을 정함) · 걷기 속도 · 점프 힘 · 체력 채우기 · 다시 태어나기 · FPS/핑/place 표시 · ` 키로 열기
- **흐름:** 추가 → Apply to Studio → Studio Play 로 확인 → Studio 에서 **게시(Publish)** → Player Connection 의 Launch Roblox ("메뉴 열고 시작")
- 1.6 의 시스템 템플릿은 메뉴의 켜기/끄기를 따라요. 예전에 넣은 시스템 파일은 바뀌지 않아요 (필요하면 지우고 다시 추가)

## 1.5 Studio 실행 환경 (Runtime Integration)
- 여러 명 테스트: `StudioTestService:ExecuteMultiplayerTestAsync(1~8명)` · 테스트 중 플레이어 추가 `AddPlayers` (테스트 서버에서 호출)
- 실행 위치 검사(LocalScript 가 StarterPlayer 바로 아래 등) · 코드 검사(서버 Script 의 LocalPlayer 등) · **위치 옮기기**(Studio 쪽도 Ctrl+Z 가능한 이동)
- Studio Connection · Runtime Testing 패널, AI 머리줄(`- @kind`) 자동 고침
- 공식 API 확인: ExecutePlayModeAsync · ExecuteRunModeAsync · ExecuteMultiplayerTestAsync · EndTest · GetTestArgs · AddPlayers 사용. LeaveTest/CanLeaveTest 는 클라이언트 전용이라 NEXUS 에서 쓸 수 없음 (클라이언트는 NEXUS 와 통신 불가)

## 1.4 Studio 연동 확장 (Advanced Studio Integration)

| 영역 | 기능 |
|---|---|
| 왼쪽 메뉴 | **Dashboard** · **Editor** · **Project Explorer**(내 파일) · **Script Explorer**(Studio 의 스크립트) · **Script Library**(게임 시스템) · 검색 · AI · 기록 · Settings |
| 여러 Studio | Studio 를 여러 개 연결해도 구분해서 보여주고 **적용 대상**을 고를 수 있어요. 열린 프로젝트의 place 가 자동으로 대상이 돼요 |
| Script Explorer | Studio 에 실제로 있는 스크립트를 서비스·폴더별로 보여주고, 내 파일과 **내용까지 비교**(같음 / 다름 / Studio 에만 / NEXUS 에만). 가져오기·이 파일만 적용·비교·Studio 에서 열기 |
| Script Library | 비행 · 달리기 · 대시 · 다단 점프 · 어깨 시점 카메라 · 근접 전투 · 충격파 능력 · HUD. 시스템마다 **추가 / 이 시스템만 적용 / 켜기·끄기 / 제거**. 켜기·끄기는 스크립트 맨 위 `local ENABLED` 값을 바꾸는 실제 코드 변경이라 적용해야 Studio 에 반영돼요 |
| 하단 | **Console**(앱 기록) · **Runtime Logs**(테스트 중 print·경고·오류 실시간, 서버/클라이언트·종류 거르기, 검색, 지우기, 자동 스크롤, 시작·종료 표시) · **Test Results**(실행 기록) · 문제 |
| 오른쪽 | 프로젝트 · Studio 연결(적용 대상) · **선택한 스크립트**(종류, 실행 위치, Studio 경로, 적용 상태, 문법, 소속 시스템) · 테스트 · 최근 작업 |
| 테스트 | 테스트 안내(NEXUS 가 하는 일 / Studio 에서 직접 할 일), 테스트 서버 미연결 안내, **고친 뒤 다시 테스트**, 첫 오류 위치로 |
| 보안 | 세션 키(플러그인 1.2.0), 허용된 명령·이벤트만 처리(편집/테스트 서버 구분), 승인 대기 요청 수 제한, 허용된 서비스 밖 적용 차단, 브라우저 요청 차단 |
| 안정성 | 설정과 nexus.project.json 을 바꿀 때마다 `.bak` 보관, 깨지면 자동 복구, 연결 포트를 못 열면 5초마다 다시 시도 |

시스템 키: 비행 F · 대시 Q · 충격파 E · 카메라 V · 달리기 Shift · 공격 마우스 왼쪽. 근접 전투는 Studio 테스트 때만 연습용 더미가 생겨요.

**플러그인 1.2.0 으로 다시 설치하고 Studio 를 다시 켜야** 세션 키와 Script Explorer 내용 비교가 동작해요. 1.1.0 플러그인도 연결은 되지만 "비교 안 됨"으로 보여요.

## 1.3 작업 공간 (Workspace Evolution)

| 영역 | 기능 | 저장 위치 |
|---|---|---|
| 작업 공간 | 열린 탭·커서·스크롤·접힌 폴더·사이드 패널·하단 탭을 프로젝트마다 기억하고 다시 열 때 복원 | `.nexus/workspace.json` |
| 탭 | Ctrl+Tab / Ctrl+Shift+Tab, Ctrl+1~9, Ctrl+PageUp/Down, 끌어서 순서 바꾸기, 오른쪽 클릭(다른 탭 닫기·저장된 탭 닫기·비교·기록) | |
| 찾기 | **빠른 열기 Ctrl+P** (오타 허용, `이름:줄` 로 줄 이동), **줄 이동 Ctrl+G**, 탐색기 이름 거르기, 열린 파일 위치 보기, 모두 접기 | |
| 탐색기 표시 | ● 주황 = 마지막으로 Studio 에 적용한 뒤 바뀐 파일, ○ = 아직 적용 안 한 파일 | `.nexus/applied/` |
| 실행 | 상단 실행 상태(진행 시간·오류·경고), Run/Stop, **실행 기록 탭**(실제로 받은 서버·클라이언트 출력만 저장, 최근 100개) | `.nexus/runs/` |
| 오류 | 오류 줄의 **상세**: 메시지·코드 위치(앞뒤 3줄)·호출 스택(클릭하면 파일로)·복사·AI 에게 물어보기 | |
| 문제 탭 | 모든 스크립트를 내 PC 의 Luau 컴파일러로 검사 (Ctrl+Shift+M) | |
| 테스트 전 확인 | 저장 안 한 파일 · 문법 오류 · Studio 와 다른 파일 · 연결된 place 를 보여주고 고르게 함 ("다음부터 묻지 않기" 가능) | |
| 연결 | 편집/테스트 서버 연결 상태와 마지막 응답, **응답 확인(핑)**, **다시 연결**, 끊김 감지(약 4초), 이 프로젝트가 연결했던 place 목록, 다른 place 경고, 플러그인 버전 확인 | `.nexus/connections.json` |
| 자동 저장 | 끄기 / 입력 멈추면 / 다른 곳 클릭 시 (상태바에서 바로 바꾸기) | |
| 작업 복구 | 저장 안 한 내용을 3초마다 따로 저장 → 비정상 종료 뒤 열면 비교해서 되살리기 | `.nexus/recovery/` |
| 변경 비교 | 지금 편집 내용 ↔ 저장된 파일 / 마지막 적용 / **지금 Studio** (Studio 는 읽기만) | |
| 최근 변경 | 저장할 때마다 바뀌기 전 내용 보관(파일마다 30개), 비교 후 되돌리기 | `.nexus/file-history/` |

**테스트 연결 개선:** 1.2 까지는 테스트 서버의 플러그인이 place 이름이 다르면(예: 저장 안 한 place) 승인되지 않아 출력이 안 오고 Stop 도 안 됐어요. 1.3 은 NEXUS 가 테스트를 시작할 때 토큰을 넘기고(`StudioTestService:GetTestArgs`), Studio 의 Play 버튼으로 시작한 테스트도 허용된 편집 연결에 묶어요. **플러그인 1.1.0 으로 다시 설치하고 Studio 를 다시 켜야** 적용돼요.

## 7. 구현할 수 없거나 제한이 있는 부분 (숨기지 않고 정리)

| 항목 | 이유 | 대체 방법 |
|---|---|---|
| NEXUS 창 안에 Studio 게임 화면 표시 | Roblox는 Studio 화면을 다른 프로그램에 넣는 공식 방법을 제공하지 않아요 | 테스트는 **실제 Studio 창**에서 돌아가고, 결과·오류만 NEXUS Console로 가져와요 |
| 공개 서버(라이브 게임)에 코드 적용 | 공식 지원 범위가 아니고 보안상 하면 안 돼요 | Studio에서 테스트한 뒤 Studio의 **게시(Publish)** 기능을 쓰세요 |
| Studio 실행부터 Play까지 완전 자동화 | Studio 로그인과 place 열기는 사용자가 해야 하고, 플러그인은 Studio가 켜진 뒤에만 동작해요 | NEXUS의 Studio 실행 버튼 → 플러그인 자동 재연결(한 번 허용한 place는 바로 연결) |
| 플러그인 HTTP 허용 자동 처리 | Studio가 보안상 사용자에게 직접 묻습니다 | 처음 한 번만 `127.0.0.1` 허용 |
| Team Create(공동 편집) place | 스크립트 변경이 Studio의 **초안(Drafts)** 으로 들어갈 수 있어요 | 적용 후 Studio에서 초안을 커밋하세요 |
| 클라이언트 출력 수집 | Studio에서 클라이언트 쪽은 HTTP를 쓸 수 없어요 | 테스트 중에만 `ReplicatedStorage.__NexusLogRelay`(저장되지 않는 임시 RemoteEvent)로 서버 플러그인에 넘겨서 전달해요 |
| 설치 프로그램(NSIS) 빌드 | 이번 빌드 환경(Linux)에서는 설치 프로그램 제작에 wine이 필요해요 | 포터블 EXE를 드려요. Windows에서 `npm run dist`를 실행하면 설치 프로그램도 만들어져요 |
| 코드 서명 | 인증서가 필요해요 | SmartScreen 경고 시 **추가 정보 → 실행** |
| macOS | 이번 목표는 Windows예요 | 소스는 macOS에서도 실행돼요. 플러그인은 Studio Plugins 폴더에 직접 복사하세요 |
| NEXUS 에서 테스트 멈추기 | Studio 는 테스트 서버 안에서만 `EndTest` 를 허용해요 | 테스트 서버 플러그인이 연결되지 않았으면 Studio 에서 Stop(Shift+F5). 실행 기록에 그 이유가 표시돼요 |
| 실행 중에만 나는 오류를 미리 찾기 | 문제 탭은 문법(컴파일)만 검사해요. nil 접근 같은 오류는 실행해야 알 수 있어요 | 테스트 후 실행 기록·오류 상세에서 확인 |
| 지금 Studio 와 비교 | Studio 가 연결돼 있어야 해요 | 연결이 없으면 "마지막 적용" 과 비교 |
| 여러 플레이어(Clients and Servers) 테스트 시작 | StudioTestService 는 Play·Run 만 지원해요 | Studio 의 Test 탭 → Clients and Servers → Start. 출력은 Runtime Logs 로 들어와요 |
| Studio 에서 스크립트 지우기 | 플러그인은 덮어쓰기·새로 만들기만 해요 (실수로 지우는 일 방지) | 시스템 "끄기" 후 적용하거나 Studio 에서 직접 지우기 |

### 검증 범위
이 빌드 환경에는 Windows와 Roblox Studio가 없어서, 아래 방식으로 확인했어요.
- **Studio 플러그인:** `NexusConnector.lua` 원본을 실제 Luau 컴파일러로 검사했어요. 그다음 Studio API를 흉내 낸 환경에서 실제로 실행했어요.
- **연결과 적용:** 연결 승인, 미리보기 diff, 충돌 보호, 백업, 적용(ChangeHistory 기록), 스캔을 확인했어요.
- **테스트:** Play 시작/종료, 서버 로그 전달, Stop → `EndTest`, Studio에서 스크립트 열기를 확인했어요.
- **데스크톱 앱:** Electron 앱 전체를 띄워서 승인 → 수정 → 미리보기 → 적용 → 테스트 → Console 오류 링크까지 자동 테스트했어요.
- **1.3:** 테스트 서버 토큰 연결(이름이 달라도)·Play 버튼 테스트 연결·스택 묶기·Stop·끊김 감지·재연결을 플러그인 원본으로 확인했고, 화면 자동 테스트 31개 항목(복구 파일, 변경 기록, 적용 표시, Studio 비교, 문제 탭, 테스트 전 확인, 실행 기록 저장, 오류 상세, 빠른 열기, 탭 전환, 재연결, 강제 종료 후 복구·작업 공간 복원)이 모두 통과했어요.

**실제 Studio에서의 첫 실행은 사용자 PC에서 확인이 필요해요.** 문제가 생기면 Console 내용과 Studio 출력 창 메시지를 알려 주세요.

---

## 8. 소스 구조

```
nexus-studio/
├─ package.json                 Electron 33 · electron-builder 설정
├─ plugin/NexusConnector.lua     Roblox Studio 플러그인 (EXE 안에도 포함)
├─ build/                       아이콘, EXE 아이콘·버전 정보 처리(afterPack.js)
└─ src/
   ├─ main/main.js              창, 설정, IPC, Studio 실행, 플러그인 설치
   ├─ main/bridge.js            127.0.0.1 전용 HTTP 브리지 (플러그인 long-poll)
   ├─ main/studio.js            스냅샷, 미리보기 diff, 백업 후 적용
   ├─ main/project.js           프로젝트 폴더, 파일 ↔ Studio 경로 변환, 백업·기록·검색
   ├─ main/preload.js           안전한 IPC 통로 (contextIsolation)
   ├─ main/systems.js           게임 시스템 라이브러리 (템플릿: main/systems/*.luau — livetest.* 가 2.0 의 게임 안 테스트 클라이언트)
   ├─ main/player.js            Roblox Player 실행 · 실행 감지 (딥링크, 프로세스 목록)
   ├─ main/opencloud.js         Roblox Open Cloud: 메시지 보내기 · DataStore 읽기 · place 게시 · API 키 암호화 저장
   ├─ main/live.js              Live Monitor: 명령 허용 목록 · 세션 읽기 · 새 로그/결과만 알리기
   ├─ main/placefile.js         프로젝트 → 스크립트 테스트 place 파일(.rbxlx)
   └─ renderer/                 화면 (index.html, css, js)
      ├─ js/app.js              프로젝트·Studio·Console·설정 화면 로직
      ├─ js/workspace.js        실행 기록·문제·비교·변경 기록·빠른 열기·오류 상세·복구
      ├─ js/panels.js           Dashboard·Script Explorer·Script Library·Runtime Logs·선택 스크립트·Studio/Player Connection·Player Runtime
      ├─ js/runcheck.js         실행 위치 · 흔한 실수 검사
      ├─ js/editor.js           코드 편집기
      ├─ js/luau.js             문법 강조·블록 검사·들여쓰기 정리
      ├─ js/editor-assist.js    자동 완성
      └─ js/luau-runtime.js     Luau 컴파일러(WebAssembly) — 오류 표시용
```

### 사용한 라이브러리
- **Electron** (MIT) — 데스크톱 앱
- **diff / jsdiff** (BSD-3-Clause) — 적용 전 미리보기
- **Luau** (MIT, Roblox) + **luau-web** (MIT) — 편집기 오류 검사용 실제 Luau 컴파일러
- **electron-builder** (MIT), **resedit** (MIT) — Windows EXE 빌드
