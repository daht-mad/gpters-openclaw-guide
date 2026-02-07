# 🪟 Windows에서 Docker로 OpenClaw 설치하기

> **작성일**: 2026년 2월 7일  
> **대상**: 비개발자도 따라할 수 있는 단계별 가이드

---

## ⚡ Quick Start (5분 안에 시작하기)

> WSL2와 Docker가 이미 설치되어 있다면 바로 시작!

**Ubuntu (WSL) 터미널에서:**

```bash
# 1. OpenClaw 다운로드
cd ~ && git clone https://github.com/openclaw/openclaw.git && cd openclaw

# 2. 설치 스크립트 실행
./docker-setup.sh

# 3. 대시보드 접속 URL 확인
docker compose run --rm openclaw-cli dashboard --no-open

# 4. 브라우저에서 접속
# http://localhost:18789
```

**⚠️ "pairing required" 에러가 나면?** → [디바이스 승인](#디바이스-승인-pairing-required-에러-해결) 섹션 참고

---

## 📋 목차

1. [OpenClaw란?](#1-openclaw란)
2. [OpenClaw로 할 수 있는 것들](#2-openclaw로-할-수-있는-것들)
3. [사전 준비](#3-사전-준비)
4. [WSL2 설치](#4-wsl2-설치)
5. [Docker Desktop 설치](#5-docker-desktop-설치)
6. [OpenClaw 설치](#6-openclaw-설치)
7. [대시보드 접속](#7-대시보드-접속)
8. [AI 모델 추가하기 (선택)](#8-ai-모델-추가하기-선택)
9. [메신저 연동 (선택)](#9-메신저-연동-선택)
10. [문제 해결](#10-문제-해결)
11. [유용한 명령어](#11-유용한-명령어)
12. [네이티브로 전환하기 (선택)](#12-네이티브로-전환하기-선택)

---

## 1. OpenClaw란?

**OpenClaw**(오픈클로)는 **개인용 AI 어시스턴트**입니다.

### 할 수 있는 것들
- 💬 WhatsApp, Telegram, Discord, Slack 등으로 AI와 대화
- 📁 파일 읽기/쓰기/관리
- 🌐 웹 검색 및 브라우저 자동화
- 🔧 다양한 작업 자동 수행

### 왜 Docker로 설치하나요?
- ✅ 내 컴퓨터 시스템에 영향 없음
- ✅ 언제든 깨끗하게 삭제 가능
- ✅ 설치/업데이트가 간편함

---

## 2. OpenClaw로 할 수 있는 것들

설치 후 OpenClaw에게 어떤 작업을 시킬 수 있는지 알아봅시다.

### 🔧 기본 도구 (설치 즉시 사용 가능)

| 도구 | 설명 | Docker 지원 |
|------|------|-------------|
| **exec/bash** | 셸 명령 실행 | ✅ 컨테이너 내에서 |
| **파일 작업** | 읽기/쓰기/편집 | ✅ 마운트된 폴더만 |
| **message** | 메신저 메시지 전송 | ✅ |
| **cron** | 예약 작업 등록 | ✅ |
| **sessions** | 세션 관리/서브에이전트 | ✅ |

### 🌐 웹 검색 (설정 필요)

웹에서 정보를 검색해서 답변합니다.

**설정 방법 (Ubuntu 터미널에서):**
```bash
# Brave Search API 키 설정
docker compose run --rm openclaw-cli configure --section web
```

또는 `~/.openclaw/openclaw.json` 직접 수정:
```json
{
  "tools": {
    "web": {
      "search": {
        "provider": "brave",
        "brave": {
          "apiKey": "BSA..."
        }
      }
    }
  }
}
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

### 🖥️ 브라우저 자동화 (Docker 제한 있음)

웹사이트를 직접 조작합니다.

| 기능 | Docker | 네이티브 |
|------|--------|----------|
| 헤드리스 브라우저 | ✅ (화면 안 보임) | ✅ |
| 실제 브라우저 창 | ❌ | ✅ |
| 스크린샷 | ✅ | ✅ |
| 클릭/입력/스크롤 | ✅ | ✅ |

**Docker에서 브라우저 사용하기:**

1. 먼저 Playwright 브라우저 설치 (Ubuntu 터미널):
```bash
docker compose run --rm openclaw-cli \
  node /app/node_modules/playwright-core/cli.js install chromium
```

2. 브라우저 시작:
```bash
docker compose run --rm openclaw-cli browser start --headless
```

**사용 예시 (쿠팡에서 맥북 검색):**

> "쿠팡에서 맥북 에어 M3 검색해서 가격 비교해줘"

AI가 수행하는 단계:
1. `browser start` - 브라우저 시작
2. `browser navigate https://www.coupang.com` - 쿠팡 접속
3. `browser snapshot` - 페이지 구조 파악
4. `browser act type [검색창] "맥북 에어 M3"` - 검색어 입력
5. `browser act click [검색버튼]` - 검색 실행
6. `browser snapshot` - 결과 페이지 분석
7. 가격/리뷰 정보 추출 및 정리

> ⚠️ **Docker 한계**: 화면이 안 보여서 진행 상황 확인이 어렵습니다.
> 실제 브라우저 창을 보면서 작업하려면 [네이티브 설치](#12-네이티브로-전환하기-선택)를 권장합니다.

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

### ⚠️ Docker 환경 제한사항

| 제한 | 이유 | 해결책 |
|------|------|--------|
| 브라우저 창 안 보임 | 헤드리스만 지원 | 네이티브 설치 |
| 데스크탑 파일 접근 제한 | 컨테이너 격리 | 볼륨 마운트 추가 |
| 시스템 앱 제어 불가 | 보안 격리 | 네이티브 설치 |
| 음성 호출(Voice Wake) | 마이크 접근 불가 | 네이티브 설치 |

**더 많은 기능이 필요하면?** → [네이티브로 전환하기](#12-네이티브로-전환하기-선택)

---

## 3. 사전 준비

### 시스템 요구사항

| 항목 | 요구사항 | 확인 방법 |
|------|----------|----------|
| OS | Windows 10 (버전 2004+) 또는 11 | `Win + R` → `winver` |
| RAM | 최소 8GB (16GB 권장) | 작업 관리자 → 성능 탭 |
| 저장공간 | 최소 10GB 여유 | 파일 탐색기 → 내 PC |
| 가상화 | 활성화 필요 | 아래 확인 방법 참고 |

### 가상화 확인하기

1. `Ctrl + Shift + Esc` → 작업 관리자
2. 성능 탭 → CPU 선택
3. 오른쪽 하단: **"가상화: 사용"** 확인

> ⚠️ "사용 안 함"이면 BIOS에서 활성화 필요 ([FAQ 참고](#q1-가상화-사용-안-함이-나와요))

---

## 3. WSL2 설치

> **WSL2란?** Windows에서 Linux를 실행하는 기능. Docker가 작동하려면 필요합니다.

### Step 1: PowerShell 관리자 권한 실행

1. 시작 메뉴에서 "PowerShell" 검색
2. **마우스 오른쪽 클릭** → "관리자 권한으로 실행"

### Step 2: WSL 설치

```powershell
wsl --install
```

### Step 3: 재시작

컴퓨터를 **재시작**합니다.

### Step 4: Ubuntu 설정

재시작 후 Ubuntu 창이 자동으로 열립니다:
1. **사용자 이름**: 영어 소문자 (예: `myname`)
2. **비밀번호**: 입력해도 화면에 안 보이는 게 정상!

### Step 5: 설치 확인

```powershell
wsl --version
```

`WSL 버전: 2.x.x`가 나오면 성공!

---

## 4. Docker Desktop 설치

### Step 1: 다운로드

👉 https://www.docker.com/products/docker-desktop/

**"Download for Windows"** 클릭

### Step 2: 설치

1. `Docker Desktop Installer.exe` 실행
2. 다음 옵션 체크 확인:
   - ✅ Use WSL 2 instead of Hyper-V
   - ✅ Add shortcut to desktop
3. "Ok" → 설치 완료 후 **컴퓨터 재시작**

### Step 3: 첫 실행

1. 바탕화면 Docker Desktop 아이콘 더블클릭
2. 서비스 약관 동의 → "Accept"
3. 왼쪽 하단 🟢 "Engine running" 확인

### Step 4: 설치 확인

```powershell
docker --version
docker run hello-world
```

"Hello from Docker!" 메시지가 나오면 성공!

---

## 5. OpenClaw 설치

> ⚠️ **중요**: OpenClaw 설치는 **Ubuntu (WSL)** 터미널에서 진행합니다!

### Step 1: Ubuntu 터미널 열기

시작 메뉴에서 "Ubuntu" 검색 → 실행

### Step 2: OpenClaw 다운로드

```bash
cd ~
git clone https://github.com/openclaw/openclaw.git
cd openclaw
```

### Step 3: 설치 스크립트 실행

```bash
./docker-setup.sh
```

> ⚠️ "permission denied" 오류 시:
> ```bash
> chmod +x docker-setup.sh && ./docker-setup.sh
> ```

### Step 4: 설정 마법사 응답

| 질문 | 추천 답변 |
|------|----------|
| Onboarding mode | `manual` |
| Gateway type | `Local gateway (this machine)` |
| Model provider | 본인 구독에 맞게 선택 |
| Tailscale | `No` (나중에 설정 가능) |

**API 키 발급:**
- OpenAI: https://platform.openai.com/api-keys
- Anthropic: https://console.anthropic.com/settings/keys

### Step 5: 설치 확인

```bash
docker ps
```

`openclaw-gateway`가 보이면 성공! ✅

---

## 6. 대시보드 접속

### Step 1: 대시보드 URL 확인

```bash
docker compose run --rm openclaw-cli dashboard --no-open
```

### Step 2: 브라우저에서 접속

```
http://localhost:18789
```

### 디바이스 승인 (pairing required 에러 해결)

대시보드 접속 시 "pairing required" 에러가 나면 디바이스 승인이 필요합니다.

**왜 이런 에러가 나나요?**
- 보안을 위해 새 디바이스(브라우저)가 접속하면 승인이 필요합니다
- 대시보드에 여러 번 접속 시도하면 pending 요청이 여러 개 쌓일 수 있습니다

**해결 방법 (Ubuntu 터미널에서):**

```bash
# 1. 대기 중인 디바이스 목록 확인
docker exec openclaw-openclaw-gateway-1 node dist/index.js devices list
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
docker exec openclaw-openclaw-gateway-1 node dist/index.js devices approve abc123-def456-...
```

> 💡 pending 요청이 여러 개라면 모두 승인하거나, 가장 최근 것만 승인하세요.

승인 후 브라우저 새로고침하면 대시보드가 정상적으로 표시됩니다!

---

## 7. AI 모델 추가하기 (선택)

기본 설치 시 선택한 모델 외에 다른 AI 모델을 추가할 수 있습니다.

### 🔐 Anthropic (Claude Pro/Max 구독자)

Claude 구독을 사용하려면 **setup-token** 방식으로 인증합니다.

> ⚠️ **중요**: Anthropic Console에서는 토큰을 발급받을 수 없습니다!  
> **Claude Code CLI**에서만 발급 가능합니다.

**Step 1: Claude Code CLI 설치** (Windows PowerShell 또는 WSL 터미널에서)

```bash
# Claude Code CLI 설치 확인
claude --version

# 설치 안 되어 있으면:
npm install -g @anthropic-ai/claude-code
```

**Step 2: setup-token 생성** (로컬 터미널에서, Docker 아님!)

```bash
claude setup-token
```

- 브라우저가 열리고 Anthropic 로그인 요청
- 로그인 후 **토큰 문자열** 출력
- 이 토큰을 복사!

**Step 3: OpenClaw에 토큰 등록** (WSL Ubuntu 터미널에서)

```bash
cd ~/openclaw
docker compose run --rm openclaw-cli models auth paste-token --provider anthropic
```

프롬프트가 나오면 복사한 토큰을 붙여넣기

**Step 4: 재시작**

```bash
docker compose down && docker compose up -d
```

---

### 🤖 OpenAI Codex (ChatGPT Plus/Pro 구독자)

OpenAI Codex는 OAuth로 인증합니다.

```bash
docker compose run --rm openclaw-cli models auth login --provider openai-codex
```

브라우저가 열리면 ChatGPT 계정으로 로그인하세요.

> 💡 Docker에서는 브라우저 콜백이 안 될 수 있습니다.  
> 로그인 후 리다이렉트된 URL을 복사해서 터미널에 붙여넣으세요.

---

### 🔑 API 키 방식 (유료 API 사용자)

구독이 아닌 API 키를 사용하려면:

```bash
docker compose run --rm openclaw-cli configure --section model
```

또는 설정 파일 직접 수정: `~/.openclaw/openclaw.json`

```json
{
  "agent": {
    "model": "anthropic/claude-opus-4-6"
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

### 📋 지원 모델 요약

| Provider | 인증 방식 | 추천 대상 |
|----------|----------|----------|
| **Anthropic** | setup-token | Claude Pro/Max 구독자 |
| **OpenAI Codex** | OAuth | ChatGPT Plus/Pro 구독자 |
| **Anthropic API** | API 키 | API 직접 결제 사용자 |
| **OpenAI API** | API 키 | API 직접 결제 사용자 |

---

## 8. 메신저 연동 (선택)

휴대폰에서 AI와 대화하고 싶다면 메신저를 연동하세요.

> 💡 나중에 추가하려면:
> ```bash
> docker compose run --rm openclaw-cli onboard
> ```

### 📱 Telegram (가장 쉬움!)

**1. 봇 만들기:**
1. Telegram에서 @BotFather 검색
2. `/newbot` 입력
3. 봇 이름, 사용자명 입력 (예: `myopenclaw_bot`)
4. **API 토큰** 받기

**2. OpenClaw에 연결 (Ubuntu 터미널):**
```bash
cd ~/openclaw
docker compose run --rm openclaw-cli channels add --channel telegram --token "토큰"
```

**3. 페어링 승인:**
1. Telegram에서 봇에게 메시지 전송
2. 봇이 페어링 코드 전송 (예: `ABC123`)
3. Ubuntu 터미널에서:
```bash
docker compose run --rm openclaw-cli pairing approve telegram ABC123
```

### 📱 WhatsApp

```bash
docker compose run --rm openclaw-cli channels login
```
QR 코드 스캔하세요.

### 💬 Discord

```bash
docker compose run --rm openclaw-cli channels add --channel discord --token "봇_토큰"
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
> 이 설정이 빠지면 메시지를 보내도 아무 반응이 없습니다.

**5. App Home 설정 (중요!):**
- App Home → Show Tabs 섹션:
  - ✅ **Messages Tab** 켜기
  - ✅ "Allow users to send Slash commands and messages from the messages tab" 체크

> ⚠️ 이 설정 안 하면 Slack에서 "이 앱으로 메시지를 보내는 기능이 꺼져 있습니다" 에러 발생!

**6. 앱 설치:**
- Install to Workspace → Bot Token 복사 (`xoxb-`로 시작)

**7. OpenClaw에 연결:**
```bash
docker compose run --rm openclaw-cli channels add --channel slack \
  --bot-token "xoxb-..." \
  --app-token "xapp-..."
```

**8. 페어링 (봇과 사용자 연결):**

페어링은 Slack 사용자와 OpenClaw를 연결하는 보안 과정입니다.

**Step 1**: Slack에서 봇에게 DM 보내기
- Slack 앱 열기 → 왼쪽 사이드바 "앱" 섹션에서 방금 만든 봇 찾기
- 봇 클릭 → 아무 메시지나 보내기 (예: "안녕")

**Step 2**: 페어링 코드 확인
- 봇이 자동으로 페어링 코드를 답장합니다
- 예시: `FP845SQ8` (8자리 영숫자)

**Step 3**: Ubuntu 터미널에서 페어링 승인
```bash
docker compose run --rm openclaw-cli pairing approve slack FP845SQ8
```
> `FP845SQ8`을 실제 받은 코드로 교체하세요!

성공하면 다음 메시지가 출력됩니다:
```
Approved pairing request for slack
```

**Step 4**: 확인
- Slack에서 봇에게 다시 메시지 보내기
- 이제 AI가 정상적으로 응답합니다! 🎉

> 💡 **페어링 코드가 안 오나요?**
> 1. Event Subscriptions에 `message.im`이 있는지 확인
> 2. App Home에서 Messages Tab이 켜져 있는지 확인
> 3. 로그 확인: `docker compose logs -f --tail=50`

---

## 9. 문제 해결

### 🔴 Q1: "가상화: 사용 안 함"이 나와요

BIOS에서 가상화 활성화 필요:
1. 컴퓨터 재시작
2. 시작 시 `F2`, `F10`, `Del`, `Esc` 중 하나로 BIOS 진입
3. Advanced → "Intel Virtualization Technology" 또는 "AMD-V" → **Enabled**
4. 저장 후 재시작

### 🔴 Cannot connect to Docker daemon

1. Docker Desktop이 실행 중인지 확인 (작업 표시줄에서 Docker 아이콘)
2. Docker Desktop 재시작

### 🔴 포트 충돌 (18789 already in use)

```powershell
# PowerShell에서
netstat -ano | findstr :18789
taskkill /PID [프로세스ID] /F
```

### 🔴 토큰 불일치 에러

`.env` 파일과 설정 파일의 토큰이 다르면 발생합니다.

```bash
# Ubuntu에서 토큰 확인
cat .env | grep TOKEN
cat ~/.openclaw/openclaw.json | jq '.gateway.auth.token'

# 토큰이 다르면 .env 파일 수정 후:
docker compose down && docker compose up -d
```

### 🔴 permission denied

```bash
sudo chown -R $USER:$USER ~/.openclaw
```

### 🔴 WSL 관련 오류

```powershell
# PowerShell에서
wsl --update
wsl --shutdown
```

### 🔴 전체 초기화 (최후의 수단)

```bash
# Ubuntu에서
cd ~/openclaw
docker compose down -v
docker rmi openclaw:local
rm -rf ~/.openclaw
cd ~
rm -rf openclaw
git clone https://github.com/openclaw/openclaw.git
cd openclaw
./docker-setup.sh
```

---

## 10. 유용한 명령어

### 자주 쓰는 명령어

| 명령어 | 설명 |
|--------|------|
| `docker ps` | 실행 중인 컨테이너 |
| `docker compose up -d` | OpenClaw 시작 |
| `docker compose down` | OpenClaw 중지 |
| `docker compose logs -f` | 실시간 로그 |
| `docker compose restart` | 재시작 |

### 디바이스 관리

```bash
# 디바이스 목록 확인
docker exec openclaw-openclaw-gateway-1 node dist/index.js devices list

# 디바이스 승인
docker exec openclaw-openclaw-gateway-1 node dist/index.js devices approve [request-id]
```

### OpenClaw CLI

```bash
# Gateway 상태
docker compose run --rm openclaw-cli gateway status

# 헬스 체크
docker compose run --rm openclaw-cli doctor

# 대시보드 URL
docker compose run --rm openclaw-cli dashboard --no-open
```

### 정리 명령어

```bash
docker image prune      # 미사용 이미지 삭제
docker system prune     # 미사용 리소스 삭제
docker system prune -a  # 모든 것 삭제 (주의!)
```

---

## ✅ 설치 체크리스트

- [ ] WSL2 설치됨 (`wsl --version` 작동)
- [ ] Docker Desktop 설치됨 (작업 표시줄에 🐳 아이콘)
- [ ] `docker --version` 작동
- [ ] OpenClaw 폴더: `~/openclaw` (Ubuntu에서)
- [ ] `docker ps`에서 openclaw-gateway 보임
- [ ] http://localhost:18789 접속 가능

**모두 체크되면 설치 완료! 🎉**

---

## 11. 네이티브로 전환하기 (선택)

Docker 대신 네이티브 설치로 전환하면 더 많은 기능을 사용할 수 있습니다.

### 네이티브의 장점

| 기능 | Docker | 네이티브 |
|------|--------|----------|
| 브라우저 자동화 | 헤드리스만 | ✅ 실제 창 보임 |
| 파일 접근 | 마운트 필요 | ✅ 바로 가능 |
| 시스템 제어 | ❌ | ✅ 일부 가능 |

### 전환 방법 (Ubuntu 터미널에서)

```bash
# 1. Docker 중지
cd ~/openclaw
docker compose down

# 2. Node.js 22 설치 (없다면)
curl -fsSL https://deb.nodesource.com/setup_22.x | sudo -E bash -
sudo apt-get install -y nodejs

# 3. 네이티브 설치
npm install -g openclaw@latest

# 4. 온보딩 (기존 설정 유지됨)
openclaw onboard --install-daemon
```

### 설정은 그대로 유지됩니다!

| 항목 | 위치 | 전환 시 |
|------|------|---------|
| 설정 파일 | `~/.openclaw/openclaw.json` | ✅ 그대로 |
| 세션 기록 | `~/.openclaw/sessions/` | ✅ 그대로 |
| 스킬 | `~/.openclaw/workspace/skills/` | ✅ 그대로 |
| Slack 연결 | 설정 파일에 저장 | ✅ 그대로 |

> 💡 Docker와 네이티브 모두 같은 `~/.openclaw/` 폴더를 사용해서 전환이 쉽습니다!

---

## 📚 추가 리소스

- **공식 문서**: https://docs.openclaw.ai
- **GitHub**: https://github.com/openclaw/openclaw
- **Discord**: https://discord.gg/clawd
- **문제 해결**: https://docs.openclaw.ai/gateway/troubleshooting
