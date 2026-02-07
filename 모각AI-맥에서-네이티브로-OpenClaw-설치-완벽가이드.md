# 🍎 맥에서 네이티브로 OpenClaw 설치하기

> **작성일**: 2026년 2월 7일  
> **대상**: 비개발자도 따라할 수 있는 단계별 가이드

---

## ⚡ Quick Start (5분 안에 시작하기)

> Node.js가 이미 설치되어 있다면 바로 시작!

```bash
# 1. OpenClaw 설치
npm install -g openclaw@latest

# 2. 초기 설정 (API 키 등록)
openclaw onboard

# 3. Gateway 설치 및 시작
openclaw gateway install
openclaw gateway start

# 4. 대시보드 열기
openclaw dashboard
```

**⚠️ "pairing required" 에러가 나면?** → [디바이스 승인](#6-대시보드-접속) 섹션 참고

---

## 📋 목차

1. [OpenClaw란?](#1-openclaw란)
2. [네이티브 vs Docker](#2-네이티브-vs-docker)
3. [OpenClaw로 할 수 있는 것들](#3-openclaw로-할-수-있는-것들)
4. [사전 준비](#4-사전-준비)
5. [OpenClaw 설치](#5-openclaw-설치)
6. [대시보드 접속](#6-대시보드-접속)
7. [AI 모델 추가하기 (선택)](#7-ai-모델-추가하기-선택)
8. [메신저 연동 (선택)](#8-메신저-연동-선택)
9. [브라우저 자동화 설정](#9-브라우저-자동화-설정)
10. [원격 접속 설정 (다른 기기에서 연결)](#10-원격-접속-설정-다른-기기에서-연결)
11. [문제 해결](#11-문제-해결)
12. [유용한 명령어](#12-유용한-명령어)

---

## 1. OpenClaw란?

**OpenClaw**(오픈클로)는 **개인용 AI 어시스턴트**입니다.

### 할 수 있는 것들
- 💬 WhatsApp, Telegram, Discord, Slack 등으로 AI와 대화
- 📁 파일 읽기/쓰기/관리
- 🌐 웹 검색 및 브라우저 자동화
- 🔧 다양한 작업 자동 수행

---

## 2. 네이티브 vs Docker

### 왜 네이티브로 설치하나요?

| 기능 | Docker | 네이티브 |
|------|--------|----------|
| 브라우저 자동화 | 헤드리스만 (화면 안 보임) | ✅ **실제 창 보임** |
| 파일 접근 | 마운트된 폴더만 | ✅ **전체 접근** |
| 시스템 제어 | ❌ | ✅ **앱 실행, 알림 등** |
| Voice Wake | ❌ | ✅ **음성 호출** |
| 설치 복잡도 | Docker 필요 | Node.js만 필요 |
| 격리 | ✅ 컨테이너 격리 | 시스템에 직접 설치 |

**추천**: 브라우저 자동화를 많이 쓸 예정이라면 **네이티브** 추천!

---

## 3. OpenClaw로 할 수 있는 것들

### 🔧 기본 도구 (설치 즉시 사용 가능)

| 도구 | 설명 |
|------|------|
| **exec/bash** | 셸 명령 실행 |
| **파일 작업** | 읽기/쓰기/편집 (전체 파일시스템) |
| **message** | 메신저 메시지 전송 |
| **cron** | 예약 작업 등록 |
| **sessions** | 세션 관리/서브에이전트 |

### 🌐 웹 검색 (설정 필요)

웹에서 정보를 검색해서 답변합니다.

**설정 방법:**
```bash
openclaw configure --section web
```

**API 키 발급:**
1. https://brave.com/search/api/ 접속
2. 계정 생성 → "Data for Search" 플랜 선택
3. API 키 생성 (무료 티어 있음!)

**사용 예시:**
> "오늘 서울 날씨 어때?"  
> "테슬라 주가 알려줘"  
> "맥북 에어 M4 리뷰 찾아줘"

### 🌍 웹 페이지 가져오기 (기본 활성화)

URL에서 내용을 추출합니다.

**사용 예시:**
> "https://news.ycombinator.com 이 페이지 요약해줘"  
> "이 블로그 글 읽고 핵심 정리해줘: [URL]"

### 🖥️ 브라우저 자동화 (네이티브 강점!)

웹사이트를 **실제 브라우저 창으로** 조작합니다. 진행 상황을 눈으로 볼 수 있어요!

**사용 예시:**
> "쿠팡에서 맥북 에어 M3 검색해서 가격 비교해줘"

AI가 실제로 브라우저를 열고:
1. 쿠팡 접속
2. 검색어 입력
3. 결과 페이지 분석
4. 가격/리뷰 정보 정리

**설정 방법은** [브라우저 자동화 설정](#9-브라우저-자동화-설정) 참고

### 📊 실제 활용 예시

**정보 수집:**
> "오늘 IT 뉴스 5개 정리해줘"  
> "파이썬 3.12 새 기능 뭐가 있어?"

**가격 비교:**
> "에어팟 프로 최저가 찾아줘"  
> "아이패드 에어 vs 프로 스펙 비교해줘"

**자동화:**
> "매일 아침 8시에 날씨 알려줘" (cron 사용)  
> "이 폴더의 PDF 파일들 목록 정리해줘"

**코드 작업:**
> "이 Python 스크립트 실행해줘"  
> "package.json 읽고 의존성 설명해줘"

---

## 4. 사전 준비

### 시스템 요구사항

| 항목 | 요구사항 |
|------|----------|
| OS | macOS 12 (Monterey) 이상 |
| RAM | 최소 8GB (16GB 권장) |
| 저장공간 | 최소 5GB 여유 |
| Node.js | 22 이상 |

### Node.js 설치

**방법 1: 공식 설치 (가장 쉬움)**

👉 https://nodejs.org/ 접속 → **LTS 버전** 다운로드 → 설치

**방법 2: Homebrew (개발자 추천)**

```bash
# Homebrew 없으면 먼저 설치
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Node.js 설치
brew install node@22
```

**방법 3: nvm (여러 버전 관리)**

```bash
# nvm 설치
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash

# 터미널 재시작 후
nvm install 22
nvm use 22
```

### 설치 확인

```bash
node --version   # v22.x.x 이상
npm --version    # 10.x.x 이상
```

---

## 5. OpenClaw 설치

### Step 1: OpenClaw 설치

```bash
npm install -g openclaw@latest
```

### Step 2: 설치 확인

```bash
openclaw --version
```

`OpenClaw 2026.x.x` 형태로 나오면 성공!

### Step 3: 초기 설정

```bash
openclaw onboard
```

설정 마법사가 시작됩니다:

| 질문 | 추천 답변 |
|------|----------|
| Model provider | 본인 구독에 맞게 선택 |
| API key | 해당 서비스 API 키 입력 |
| Install daemon? | `Yes` (자동 시작) |

**API 키 발급:**
- OpenAI: https://platform.openai.com/api-keys
- Anthropic: https://console.anthropic.com/settings/keys

### Step 4: Gateway 설치 및 시작

```bash
# Gateway 서비스 설치 (자동 시작 등록)
openclaw gateway install

# Gateway 시작
openclaw gateway start
```

### Step 5: 상태 확인

```bash
openclaw gateway status
```

`Runtime: running` 또는 `RPC probe: success`가 보이면 성공! ✅

---

## 6. 대시보드 접속

### Step 1: 대시보드 열기

```bash
openclaw dashboard
```

브라우저가 자동으로 열립니다. (보통 http://localhost:18789)

### 디바이스 승인 (pairing required 에러 해결)

대시보드 접속 시 "pairing required" 에러가 나면 디바이스 승인이 필요합니다.

**해결 방법:**

```bash
# 1. 대기 중인 디바이스 목록 확인
openclaw devices list
```

출력 예시:
```
Pending pairing requests:
  - Request ID: abc123-def456-...
    Device: dashboard
    Created: 2026-02-07T12:00:00Z
```

```bash
# 2. 디바이스 승인 (Request ID 복사해서 사용)
openclaw devices approve abc123-def456-...
```

승인 후 브라우저 새로고침하면 대시보드가 정상적으로 표시됩니다!

---

## 7. AI 모델 추가하기 (선택)

### 🔐 Anthropic (Claude Pro/Max 구독자)

Claude 구독을 사용하려면 **setup-token** 방식으로 인증합니다.

> ⚠️ **중요**: Anthropic Console에서는 토큰을 발급받을 수 없습니다!  
> **Claude Code CLI**에서만 발급 가능합니다.

**Step 1: Claude Code CLI 설치**

```bash
npm install -g @anthropic-ai/claude-code
# 또는
brew install claude-code
```

**Step 2: setup-token 생성**

```bash
claude setup-token
```

- 브라우저가 열리고 Anthropic 로그인 요청
- 로그인 후 **토큰 문자열** 출력
- 이 토큰을 복사!

**Step 3: OpenClaw에 토큰 등록**

```bash
openclaw models auth paste-token --provider anthropic
```

프롬프트가 나오면 복사한 토큰을 붙여넣기

**Step 4: Gateway 재시작**

```bash
openclaw gateway restart
```

---

### 🤖 OpenAI Codex (ChatGPT Plus/Pro 구독자)

```bash
openclaw models auth login --provider openai-codex
```

브라우저가 열리면 ChatGPT 계정으로 로그인하세요.

---

### 🔑 API 키 방식 (유료 API 사용자)

```bash
openclaw configure --section model
```

또는 설정 파일 직접 수정: `~/.openclaw/openclaw.json`

```json
{
  "agent": {
    "model": "anthropic/claude-sonnet-4-20250514"
  },
  "models": {
    "providers": {
      "anthropic": {
        "apiKey": "sk-ant-api03-xxxx..."
      }
    }
  }
}
```

---

## 8. 메신저 연동 (선택)

휴대폰에서 AI와 대화하고 싶다면 메신저를 연동하세요.

### 📱 Telegram (가장 쉬움!)

**1. 봇 만들기:**
1. Telegram에서 @BotFather 검색
2. `/newbot` 입력
3. 봇 이름, 사용자명 입력 (예: `myopenclaw_bot`)
4. **API 토큰** 받기

**2. OpenClaw에 연결:**
```bash
openclaw channels add --channel telegram --token "토큰"
```

**3. 페어링 승인:**
1. Telegram에서 봇에게 메시지 전송
2. 봇이 페어링 코드 전송 (예: `ABC123`)
3. 터미널에서:
```bash
openclaw pairing approve telegram ABC123
```

### 📱 WhatsApp

```bash
openclaw channels login
```
QR 코드 스캔하세요.

### 💬 Discord

```bash
openclaw channels add --channel discord --token "봇_토큰"
```

### 💼 Slack

> 📋 **Slack 연결 체크리스트**
> - [ ] Slack 앱 생성
> - [ ] Socket Mode 활성화 + App Token 생성
> - [ ] Bot Token Scopes 설정
> - [ ] Event Subscriptions에 `message.im` 추가 ⭐
> - [ ] App Home Messages Tab 활성화 ⭐
> - [ ] 워크스페이스에 앱 설치
> - [ ] OpenClaw에 채널 추가
> - [ ] 봇에게 DM → 페어링 코드 받기 → 승인
>
> ⭐ 표시된 항목은 빠뜨리기 쉽지만 필수입니다!

**1. Slack 앱 만들기:**
- https://api.slack.com/apps → Create New App → From scratch

**2. Socket Mode 활성화:**
- Socket Mode → Enable → App Token 생성 (`xapp-`로 시작)

**3. 권한 설정:**
- OAuth & Permissions → Bot Token Scopes:
  - `chat:write`, `im:history`, `im:read`, `im:write`, `users:read`

**4. Event Subscriptions (필수!):**
- Enable Events → **ON**
- Subscribe to bot events → **Add Bot User Event** 클릭
- `message.im` 추가 (DM 수신용)

> ⚠️ **중요**: `message.im` 이벤트가 없으면 봇이 DM을 받을 수 없습니다!

**5. App Home 설정 (중요!):**
- App Home → Show Tabs 섹션:
  - ✅ **Messages Tab** 켜기
  - ✅ "Allow users to send Slash commands and messages from the messages tab" 체크

> ⚠️ 이 설정 안 하면 "이 앱으로 메시지를 보내는 기능이 꺼져 있습니다" 에러 발생!

**6. 앱 설치:**
- Install to Workspace → Bot Token 복사 (`xoxb-`로 시작)

**7. OpenClaw에 연결:**
```bash
openclaw channels add --channel slack \
  --bot-token "xoxb-..." \
  --app-token "xapp-..."
```

**8. 페어링:**
1. Slack에서 봇에게 DM 보내기
2. 봇이 페어링 코드 답장 (예: `FP845SQ8`)
3. 터미널에서:
```bash
openclaw pairing approve slack FP845SQ8
```

성공 메시지:
```
Approved pairing request for slack
```

---

## 9. 브라우저 자동화 설정

네이티브의 가장 큰 장점! 실제 브라우저 창을 보면서 AI가 작업하는 걸 볼 수 있어요.

### 🔄 두 가지 브라우저 드라이버

OpenClaw는 **두 가지 방식**으로 브라우저를 제어할 수 있습니다:

| 구분 | Chrome 확장 프로그램 | OpenClaw 관리형 (Playwright) |
|------|---------------------|------------------------------|
| **브라우저** | 내가 쓰던 Chrome 그대로 | 새로운 격리된 브라우저 |
| **로그인 상태** | ✅ 유지됨 (쿠키, 세션) | ❌ 새로 로그인 필요 |
| **북마크/확장** | ✅ 그대로 사용 | ❌ 없음 (깨끗한 상태) |
| **설치** | Chrome 확장 설치 필요 | 자동 (추가 설치 없음) |
| **보안** | ⚠️ 내 세션 접근 가능 | ✅ 격리된 환경 |
| **사용 사례** | 로그인된 상태로 작업 | 자동화/스크래핑/테스트 |
| **프로필 이름** | `chrome` | `openclaw` |

**요약:**
- **확장 프로그램**: 로그인된 내 Chrome에서 바로 작업하고 싶을 때
- **OpenClaw 관리형**: 깨끗한 환경에서 자동화할 때

---

### 🧩 방법 1: Chrome 확장 프로그램 (기존 Chrome 제어)

내가 사용하는 Chrome 브라우저의 탭을 OpenClaw가 제어합니다.

#### Step 1: 확장 프로그램 설치

```bash
# 확장 프로그램 파일 설치
openclaw browser extension install

# 설치 경로 확인
openclaw browser extension path
```

출력 예시:
```
/Users/username/.openclaw/browser/extension
```

#### Step 2: Chrome에 확장 프로그램 로드

1. Chrome에서 `chrome://extensions` 접속
2. 우측 상단 **개발자 모드** 켜기
3. **압축해제된 확장 프로그램을 로드합니다** 클릭
4. 위에서 확인한 경로 선택 (`~/.openclaw/browser/extension`)
5. 주소창 옆에 OpenClaw 아이콘 📌 고정 (권장)

#### Step 3: 탭에 연결하기

1. 제어하고 싶은 **웹 페이지 탭**을 엽니다
2. OpenClaw **확장 프로그램 아이콘** 클릭
3. 아이콘에 **`ON`** 배지가 표시되면 연결 완료!

**배지 의미:**
| 배지 | 상태 |
|------|------|
| `ON` | ✅ 연결됨, 제어 가능 |
| `…` | ⏳ 연결 중 |
| `!` | ❌ Gateway 미실행 (`openclaw gateway start` 실행) |

#### Step 4: 브라우저 제어하기

```bash
# 연결된 탭 목록 확인
openclaw browser --browser-profile chrome tabs

# 페이지 스냅샷 (AI가 볼 수 있는 형태)
openclaw browser --browser-profile chrome snapshot

# 스크린샷
openclaw browser --browser-profile chrome screenshot
```

#### 연결 해제

확장 프로그램 아이콘을 다시 클릭하면 연결이 해제됩니다.

---

### 🦞 방법 2: OpenClaw 관리형 브라우저 (Playwright)

OpenClaw가 별도의 격리된 브라우저를 실행합니다.

#### Step 1: 브라우저 시작

```bash
# 브라우저 시작 (새 창이 열림)
openclaw browser --browser-profile openclaw start

# 상태 확인
openclaw browser --browser-profile openclaw status
```

주황색 테두리의 새 Chrome 창이 열립니다!

#### Step 2: 브라우저 제어하기

```bash
# URL 열기
openclaw browser --browser-profile openclaw open https://www.google.com

# 페이지 스냅샷
openclaw browser --browser-profile openclaw snapshot

# 스크린샷
openclaw browser --browser-profile openclaw screenshot

# 열린 탭 목록
openclaw browser --browser-profile openclaw tabs
```

#### Step 3: 브라우저 종료

```bash
openclaw browser --browser-profile openclaw stop
```

#### 다른 브라우저 사용하기 (Brave 등)

```bash
# Brave 브라우저 경로 설정
openclaw config set browser.executablePath "/Applications/Brave Browser.app/Contents/MacOS/Brave Browser"

# 이제 openclaw 브라우저 시작 시 Brave가 열림
openclaw browser --browser-profile openclaw start
```

---

### ⚙️ 설정 (선택)

`~/.openclaw/openclaw.json`에서 브라우저 설정:

```json
{
  "browser": {
    "enabled": true,
    "defaultProfile": "openclaw",
    "headless": false,
    "profiles": {
      "openclaw": {
        "cdpPort": 18800,
        "driver": "openclaw"
      },
      "chrome": {
        "driver": "extension"
      }
    }
  }
}
```

| 설정 | 설명 |
|------|------|
| `defaultProfile` | 기본 프로필 (`openclaw` 또는 `chrome`) |
| `headless: false` | 실제 브라우저 창 표시 (기본값) |
| `headless: true` | 창 없이 백그라운드 실행 |
| `executablePath` | 사용할 브라우저 경로 (Brave 등) |

---

### 🎮 AI에게 브라우저 조작 시키기

설정이 완료되면 AI에게 브라우저 작업을 시킬 수 있습니다!

Slack이나 대시보드에서 AI에게:

> "네이버에서 '맥북 프로' 검색해서 뉴스 탭의 최신 기사 3개 정리해줘"

AI가 실제로:
1. 브라우저 창 열기
2. 네이버 접속
3. 검색어 입력
4. 뉴스 탭 클릭
5. 기사 내용 읽고 정리

**모든 과정을 실시간으로 볼 수 있어요!**

**다른 활용 예시:**
> "네이버 로그인해서 메일 확인해줘" (확장 프로그램 사용 시 - 이미 로그인됨)
> "이 웹사이트 스크린샷 찍어줘"
> "쿠팡에서 에어팟 프로 가격 비교해줘"

---

### 🔧 주요 브라우저 명령어

```bash
# === 기본 제어 ===
openclaw browser start                    # 브라우저 시작
openclaw browser stop                     # 브라우저 종료
openclaw browser status                   # 상태 확인
openclaw browser tabs                     # 열린 탭 목록

# === 탐색 ===
openclaw browser open <url>               # 새 탭에서 URL 열기
openclaw browser navigate <url>           # 현재 탭에서 이동
openclaw browser snapshot                 # 페이지 구조 분석 (AI용)
openclaw browser screenshot               # 스크린샷 저장

# === 상호작용 (ref는 snapshot에서 확인) ===
openclaw browser click <ref>              # 요소 클릭
openclaw browser type <ref> "텍스트"       # 텍스트 입력
openclaw browser press Enter              # 키 입력

# === 확장 프로그램 ===
openclaw browser extension install        # 확장 프로그램 설치
openclaw browser extension path           # 설치 경로 확인
openclaw browser extension uninstall      # 확장 프로그램 삭제

# === 프로필 지정 ===
openclaw browser --browser-profile chrome ...     # 확장 프로그램 사용
openclaw browser --browser-profile openclaw ...   # 관리형 브라우저 사용
```

---

### ⚠️ 브라우저 문제 해결

#### "Chrome extension relay is running, but no tab is connected"

Chrome에서 확장 프로그램 아이콘을 클릭해서 탭에 연결하세요.

#### 확장 프로그램 아이콘에 `!` 표시

Gateway가 실행되지 않았습니다:
```bash
openclaw gateway start
```

#### "Browser disabled" 에러

브라우저 기능이 비활성화되어 있습니다:
```bash
openclaw config set browser.enabled true
```

#### OpenClaw 업데이트 후 확장 프로그램 오류

확장 프로그램을 다시 설치하세요:
```bash
openclaw browser extension install
```
그 후 `chrome://extensions`에서 확장 프로그램 새로고침 (🔄 버튼)

#### "Playwright is not available" 에러

```bash
npx playwright install chromium
```

---

### 🔒 보안 참고사항

**Chrome 확장 프로그램 사용 시:**
- ⚠️ 로그인된 세션에 접근 가능
- 💡 민감한 작업은 별도 Chrome 프로필 권장
- 💡 Gateway를 공개 네트워크에 노출하지 마세요

**OpenClaw 관리형 브라우저:**
- ✅ 개인 브라우저와 완전히 격리
- ✅ 별도 사용자 데이터 디렉토리 사용
- ✅ 보안이 중요한 자동화에 권장

---

## 10. 원격 접속 설정 (다른 기기에서 연결)

내 맥에서 실행 중인 OpenClaw Gateway에 다른 기기(아이폰, 아이패드, 다른 맥 등)에서 접속하는 방법입니다.

### Gateway 토큰 확인/생성

```bash
# 현재 토큰 확인
openclaw config get gateway.auth.token

# 토큰이 없으면 생성
openclaw doctor --generate-gateway-token
```

토큰은 `~/.openclaw/openclaw.json`의 `gateway.auth.token`에 저장됩니다.

### 방법 1: 같은 네트워크 (집/사무실)

같은 Wi-Fi에 연결된 기기에서 접속:

**Step 1: Gateway를 외부에서 접근 가능하게 설정**

```bash
# 설정 파일 수정
openclaw configure --section gateway
```

또는 직접 수정 (`~/.openclaw/openclaw.json`):
```json
{
  "gateway": {
    "bind": "0.0.0.0",
    "port": 18789,
    "auth": {
      "mode": "token",
      "token": "your-secret-token-here"
    }
  }
}
```

> ⚠️ `bind: "0.0.0.0"`으로 설정하면 네트워크의 다른 기기에서 접근 가능해집니다.

**Step 2: Gateway 재시작**

```bash
openclaw gateway restart
```

**Step 3: 내 맥의 IP 확인**

```bash
ipconfig getifaddr en0
# 예: 192.168.0.10
```

**Step 4: 다른 기기에서 접속**

브라우저에서: `http://192.168.0.10:18789`

토큰 입력 프롬프트가 나오면 위에서 확인한 토큰 입력.

### 방법 2: 외부 네트워크 (Tailscale 추천)

집 밖에서도 접속하려면 **Tailscale** 사용을 권장합니다.

**Step 1: Tailscale 설치**

👉 https://tailscale.com/download

맥과 접속할 기기 모두에 설치하고 같은 계정으로 로그인.

**Step 2: OpenClaw Tailscale 설정**

```bash
openclaw configure --section tailscale
```

또는 설정 파일:
```json
{
  "gateway": {
    "tailscale": {
      "enabled": true,
      "mode": "serve"
    }
  }
}
```

**Step 3: Gateway 재시작**

```bash
openclaw gateway restart
```

**Step 4: Tailscale IP로 접속**

```bash
# Tailscale IP 확인
tailscale ip -4
# 예: 100.100.100.100
```

다른 기기에서: `http://100.100.100.100:18789`

### 방법 3: CLI로 원격 Gateway에 연결

다른 맥에서 원격 Gateway에 CLI로 연결:

```bash
# 원격 Gateway 설정
openclaw config set gateway.remote.url wss://192.168.0.10:18789
openclaw config set gateway.remote.token "your-secret-token"

# 또는 명령어로 직접 연결
openclaw chat --url wss://192.168.0.10:18789 --token "your-secret-token"
```

### 보안 주의사항

| 항목 | 권장 |
|------|------|
| 토큰 | 충분히 길고 랜덤하게 (32자 이상) |
| 네트워크 | Tailscale 사용 권장 |
| 공개 인터넷 노출 | ❌ 비권장 (VPN/Tailscale 사용) |
| 포트 포워딩 | ❌ 비권장 |

> 💡 **가장 안전한 방법**: Tailscale로 연결하면 토큰 없이도 Tailscale 인증으로 보호됩니다.

---

## 11. 문제 해결

### 🔴 node: command not found

Node.js가 설치되지 않았거나 PATH에 없습니다.

```bash
# Node.js 재설치
brew install node@22

# 또는 PATH 확인
echo $PATH
```

### 🔴 permission denied (npm install -g)

```bash
# npm 권한 수정
sudo chown -R $(whoami) $(npm config get prefix)/{lib/node_modules,bin,share}

# 다시 설치
npm install -g openclaw@latest
```

또는 sudo 사용 (비추천):
```bash
sudo npm install -g openclaw@latest
```

### 🔴 Gateway가 시작되지 않음

```bash
# 상태 확인
openclaw gateway status

# 로그 확인
cat /tmp/openclaw/openclaw-$(date +%Y-%m-%d).log

# 재설치
openclaw gateway uninstall
openclaw gateway install
openclaw gateway start
```

### 🔴 포트 충돌 (18789 already in use)

```bash
# 사용 중인 프로세스 확인
lsof -i :18789

# 프로세스 종료
kill -9 [PID]

# Gateway 재시작
openclaw gateway restart
```

### 🔴 Config version mismatch 경고

```
Config was last written by a newer OpenClaw (2026.x.x); current version is ...
```

OpenClaw 업데이트:
```bash
npm install -g openclaw@latest
openclaw gateway restart
```

### 🔴 브라우저가 안 열림

```bash
# Playwright 브라우저 설치
npx playwright install chromium

# 또는 시스템 Chrome 사용 설정
openclaw configure --section browser
```

### 🔴 전체 초기화 (최후의 수단)

```bash
# Gateway 중지 및 제거
openclaw gateway stop
openclaw gateway uninstall

# 설정 백업 후 삭제
cp -r ~/.openclaw ~/.openclaw.backup
rm -rf ~/.openclaw

# 재설치
npm uninstall -g openclaw
npm install -g openclaw@latest
openclaw onboard
```

---

## 12. 유용한 명령어

### 자주 쓰는 명령어

| 명령어 | 설명 |
|--------|------|
| `openclaw chat` | 터미널에서 AI와 대화 |
| `openclaw dashboard` | 웹 대시보드 열기 |
| `openclaw gateway status` | Gateway 상태 확인 |
| `openclaw gateway start` | Gateway 시작 |
| `openclaw gateway stop` | Gateway 중지 |
| `openclaw gateway restart` | Gateway 재시작 |

### 디바이스 관리

```bash
openclaw devices list              # 디바이스 목록
openclaw devices approve [id]      # 디바이스 승인
```

### 채널 관리

```bash
openclaw channels list             # 연결된 채널 목록
openclaw channels add --channel telegram --token "..."
```

### 설정

```bash
openclaw configure                 # 전체 설정
openclaw configure --section model # 모델 설정만
openclaw configure --section web   # 웹 검색 설정만
```

### 진단

```bash
openclaw doctor                    # 문제 진단
openclaw doctor --repair           # 자동 수정 시도
```

### 브라우저

```bash
openclaw browser status            # 브라우저 상태
openclaw browser start             # 브라우저 시작
openclaw browser stop              # 브라우저 종료
openclaw browser screenshot        # 스크린샷
```

---

## ✅ 설치 체크리스트

- [ ] Node.js 22 이상 설치됨 (`node --version`)
- [ ] OpenClaw 설치됨 (`openclaw --version`)
- [ ] Gateway 실행 중 (`openclaw gateway status`)
- [ ] 대시보드 접속 가능 (`openclaw dashboard`)
- [ ] 디바이스 승인 완료

**모두 체크되면 설치 완료! 🎉**

---

## 📚 추가 리소스

- **공식 문서**: https://docs.openclaw.ai
- **GitHub**: https://github.com/openclaw/openclaw
- **문제 해결**: https://docs.openclaw.ai/gateway/troubleshooting
