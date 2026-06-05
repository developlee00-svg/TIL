# GitHub Spec Kit 으로 로그인 화면 만들기 실습
## 주제: 로그인 화면 만들기 (GitHub Copilot Free + VS Code)

> **이 실습은 간단한 로그인 기능을 만드는 실습이다.**  
> 1차시(SDD 방법론) → 2차시(Spec Kit 가이드) → 실습으로 이어지는 강의이며
> **도구는 GitHub Copilot Free 를 사용**하여 SDD 방법론이 어떻게 동작하는지 실습한다.

**목표**: VS Code 환경에서 SDD 전체 흐름을 Copilot 으로 체험하고, 로그인 화면을 완성한다  
**사용 도구**: VS Code (로컬 또는 Codespace), GitHub Copilot Free, specify CLI  
**결과물**: HTML + CSS + JavaScript 기반 로그인 화면 (명세 변경 + 3가지 기능 포함)

---

## 목차

1. [사전 확인 — Copilot Free 무료 한도](#사전-확인--copilot-free-무료-한도)
2. [사전 확인 — Copilot 연동 시 파일 위치](#사전-확인--copilot-연동-시-파일-위치)
3. [실습 전 준비 사항](#실습-전-준비-사항)
4. [실습 개요](#실습-개요)
5. [Part 1. VS Code 환경 준비](#part-1-vs-code-환경-준비)
6. [Part 2. specify CLI 설치](#part-2-specify-cli-설치)
7. [Part 3. 프로젝트 초기화](#part-3-프로젝트-초기화)
8. [Part 4. Copilot 채팅창 설정](#part-4-copilot-채팅창-설정)
9. [Part 5. 기본 로그인 화면 구현](#part-5-sdd-실습-1단계--기본-로그인-화면)
   - Step 4. Checklist (specify 후 — 요구사항·UX 점검)
   - Step 9. Checklist (implement 후 — 품질 검증)
10. [Part 6. 결과물 실행 및 테스트](#part-6-결과물-실행-및-테스트)
11. [Part 7. 기존 명세(001) 변경 대처](#part-7-sdd--기존-명세001-변경-대처)
    - 나쁜 방법 vs 올바른 방법 비교
    - spec.md 수정 → clarify → checklist → analyze → plan → tasks → implement
12. [Part 8. 로그인 횟수 제한 추가](#part-8-sdd-실습-2단계--로그인-횟수-제한-추가)
    - Step 3. Checklist (specify 후 — UX·보안 점검)
13. [Part 9. 아이디 저장 기능 추가](#part-9-sdd-실습-3단계--아이디-저장-기능-추가)
    - Step 3. Checklist (specify 후 — 보안·UX 점검)
14. [Part 10. 전체 산출물 구조 확인](#part-10-전체-산출물-구조-확인)
15. [3차시(Gemini)와의 차이점 요약](#3차시gemini와의-차이점-요약)
16. [트러블슈팅](#트러블슈팅)
17. [전체 명령어 요약표](#전체-명령어-요약표)

---

## 사전 확인 — Copilot Free 무료 한도

GitHub Copilot Free 는 GitHub 계정만 있으면 별도 결제 없이 바로 사용 가능하다.  
단, 아래 월간 사용 한도가 있으므로 실습 중 신중하게 진행해야 한다.

| 기능 | 월간 무료 한도 | 이번 실습 예상 사용량 |
|---|---|---|
| 코드 완성 (인라인 제안) | 2,000회 | 약 200~400회 |
| 채팅 메시지 | 50회 | 약 30회 (기능 3개 × 9단계 + checklist) |

> **채팅 한도 절약 수칙**
>
> - 각 단계 입력 전 내용을 충분히 검토하고 한 번에 정확하게 입력한다
> - 오류 발생 시 전체 재요청 대신 해당 부분만 수정 요청한다
> - 채팅창에서 잡담성 질문은 삼간다
> - 실습 외 다른 프로젝트에서 Copilot 사용을 줄인다

---

## 사전 확인 — Copilot 연동 시 파일 위치

`specify init` 실행 시 에이전트별로 생성되는 폴더 경로가 다르다.  
이 실습은 **GitHub Copilot** 을 사용하므로 아래 경로에 명령어 파일이 생성된다.

| 에이전트 | 명령어 파일 위치 | 파일 형식 |
|---|---|---|
| GitHub Copilot | `.github/prompts/` | `.prompt.md` |
| Gemini CLI | `.gemini/commands/` | `.toml` |
| Claude Code | `.claude/commands/` | `.md` |

> **Copilot의 경우** `.github/prompts/` 경로가 **올바른 경로**이다.  
> VS Code 는 `.github/prompts/*.prompt.md` 파일을 자동으로 인식하여  
> Copilot 채팅창에서 `/speckit.*` 명령어로 노출한다.

---

## 실습 전 준비 사항

| 필요 계정 | 가입 주소 | 비고 |
|---|---|---|
| GitHub 계정 | https://github.com/signup | Copilot Free 인증 및 Codespace 에 사용 |

> Google 계정(Gemini)이나 Anthropic 계정(Claude)은 필요하지 않다.  
> GitHub 계정 하나로 Codespaces 와 Copilot Free 를 모두 사용할 수 있다.

**브라우저**: Chrome 또는 Edge 권장

---

## 실습 개요

Spec-Driven Development(SDD)는 코드를 먼저 짜지 않는다.  
**무엇을, 왜 만드는지** 먼저 정의하고, AI 에이전트가 구현하도록 지시하는 방법론이다.

### SDD 전체 흐름

```
constitution → specify → clarify → checklist → plan → tasks → analyze → implement
(원칙 설정)  (요구사항) (모호함 제거) (명세점검)  (기술설계) (작업목록) (최종점검)  (코드생성)
   1회만        ①         ②            ③         ④        ⑤          ⑥          ⑦
```

> **checklist 위치가 중요한 이유**  
> `/speckit.checklist` 는 **spec.md 의 완성도**를 점검하는 명령이다.  
> `clarify` 로 모호함을 제거한 직후, `plan` 으로 넘어가기 전에 실행해야  
> 명세의 구멍을 설계 단계에서 발견할 수 있다.  
> implement 이후에 발견하면 코드까지 다시 작성해야 하는 상황이 발생한다.

> **constitution.md 가 테스트와 연결되는 원리**  
> `constitution.md` 에 테스트 원칙을 명시하면, `/speckit.checklist testing` 실행 시  
> Copilot 이 constitution.md 를 참조하여 **테스트 기준을 자동으로 반영**한다.  
>
> ```
> constitution.md 에 테스트 원칙 명시
>       ↓
> implement 완료 후 /speckit.checklist testing 실행
>       ↓
> Copilot 이 constitution.md 의 테스트 원칙 + spec.md 의 수용 기준을 동시에 참조
>       ↓
> 프로젝트 원칙(단일 파일, 한국어 주석, 모바일 지원 등)에 맞는 테스트 항목 자동 생성
> ```

### 기능을 추가할 때의 흐름

`constitution` 은 프로젝트 전체에 적용되는 원칙이므로 **최초 1회만** 작성하면 된다.  
기능이 추가될 때마다 `specify` 부터 `implement` 까지 새 번호로 다시 진행한다.

```
[최초 구현]  constitution → specify(001) → clarify → checklist → plan → tasks → analyze → implement
[기능 추가]               → specify(002) → clarify → checklist → plan → tasks → analyze → implement
[기능 추가]               → specify(003) → clarify → checklist → plan → tasks → analyze → implement
```

### 기존 명세를 수정할 때의 흐름

이미 구현이 완료된 기능의 **요구사항이 변경**되는 경우, SDD 에서는 아래 순서를 따른다.

```
[명세 수정]  spec.md 직접 수정 → clarify → checklist → analyze → plan(재실행) → tasks(재실행) → implement
                  ↑
          코드가 아닌 명세부터 수정하는 것이 SDD 의 핵심 원칙이다
```

> **왜 코드가 아닌 spec.md 부터 수정하는가?**  
> SDD 에서 **명세(spec.md)가 진실의 근원**이다.  
> 코드를 먼저 수정하면 명세와 코드가 어긋나는 순간부터 문서는 거짓 문서가 된다.  
> 이후 팀원이 spec.md 를 보고 잘못된 방향으로 개발하는 연쇄 오류가 발생한다.

### 이번 실습에서 다루는 시나리오

| 파트 | 시나리오 | SDD 번호 |
|---|---|---|
| Part 5 | 기본 로그인 화면 구현 (아이디/비밀번호 검증) | 001 최초 구현 |
| Part 7 | 001 명세 변경 — 로그인 성공 후 동작 추가 | 001 명세 수정 |
| Part 8 | 로그인 횟수 제한 + 카운트다운 기능 추가 | 002 신규 추가 |
| Part 9 | 아이디 저장 (localStorage) 기능 추가 | 003 신규 추가 |

---

## Part 1. VS Code 환경 준비

### 환경 선택

이 실습은 아래 두 가지 환경 중 하나에서 진행할 수 있다.

| 환경 | 설명 | 권장 대상 |
|---|---|---|
| **GitHub Codespace** (권장) | 브라우저에서 VS Code 를 실행. 별도 설치 없음 | 설치 환경이 제한된 경우 |
| **로컬 VS Code** | 내 컴퓨터에 VS Code 를 설치 | 빠른 응답속도 원하는 경우 |

---

### 1-A. GitHub Codespace 환경으로 진행하는 경우

> 3차시와 동일한 Codespace 를 재사용할 수 있다. 단, 새로운 실습이므로 별도 레포지토리 생성을 권장한다.

**Codespace 생성:**

1. https://github.com 에 로그인한다.
2. 오른쪽 상단 `+` 버튼 → `New repository` 선택.
3. Repository name: `login-copilot-lab`, `Add a README file` 체크 후 생성.
4. 생성된 레포지토리에서 초록색 `Code` → `Codespaces` → `Create codespace on main` 클릭.
5. 브라우저에서 VS Code 화면이 열릴 때까지 1~2분 기다린다.

터미널이 보이지 않으면 상단 메뉴에서 `Terminal → New Terminal` 을 선택한다.

**현재 위치 확인:**

```bash
pwd
```

예상 출력:

```
/workspaces/login-copilot-lab
```

---

### 1-B. 로컬 VS Code 환경으로 진행하는 경우

**VS Code 설치:**

https://code.visualstudio.com 에서 운영체제에 맞는 설치 파일을 다운로드하여 설치한다.

**작업 폴더 생성:**

```bash
# Windows (PowerShell)
mkdir C:\Users\%USERNAME%\login-copilot-lab
cd C:\Users\%USERNAME%\login-copilot-lab

# macOS / Linux
mkdir ~/login-copilot-lab
cd ~/login-copilot-lab
```

---

### 1-2. GitHub Copilot 활성화

두 환경 모두 아래 방법으로 Copilot 을 활성화한다.

1. VS Code 우측 하단 계정 아이콘을 클릭한다.
2. `Sign in with GitHub` 를 선택한다.
3. 브라우저에서 GitHub 계정으로 로그인한다.
4. VS Code 하단 상태바에서 Copilot 아이콘이 활성화(흰색)되면 정상이다.

> Copilot 아이콘에 X 표시가 있으면 로그인이 필요한 상태이다.

> **확인 포인트 ✅**  
> 터미널이 열리고, Copilot 아이콘이 활성화되면 Part 1 완료.

---

## Part 2. specify CLI 설치

Copilot 은 VS Code 에 내장되어 있으므로 별도 CLI 설치가 필요 없다.  
`specify CLI` 만 설치하면 된다.

### 2-1. uv 설치 (Python 패키지 관리자)

운영체제에 맞는 명령어를 사용한다.

**Codespace / macOS / Linux:**

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
source ~/.bashrc
```

**Windows (PowerShell):**

```powershell
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

> Windows 에서 PowerShell 실행 후 새 터미널 창을 열어야 `uv` 명령이 인식된다.

설치 확인:

```bash
uv --version
```

예상 출력:

```
uv 0.x.x
```

### 2-2. specify CLI 설치

```bash
uv tool install specify-cli --from git+https://github.com/github/spec-kit.git
```

설치 확인:

```bash
specify --version
```

예상 출력:

```
specify 0.x.x
```

> **확인 포인트 ✅**  
> `uv` 와 `specify` 버전이 모두 출력되면 Part 2 완료.

---

## Part 3. 프로젝트 초기화

### 3-1. specify 프로젝트 생성

작업 폴더(`login-copilot-lab` 또는 `/workspaces/login-copilot-lab`) 에서 아래를 실행한다.

```bash
specify init login-project --integration copilot
```

실행 중 에이전트 선택 화면이 나오면 `copilot` 을 선택한다.

### 3-2. 프로젝트 폴더로 이동

```bash
cd login-project
```

이동 후 현재 위치 확인:

```bash
pwd
```

예상 출력 (Codespace 기준):

```
/workspaces/login-copilot-lab/login-project
```

> **중요**: 이후 모든 터미널 명령어는 반드시 `login-project` 폴더 안에서 실행해야 한다.

### 3-3. 생성된 구조 확인

```bash
ls -la
```

예상 출력:

```
.github/
.specify/
README.md
```

Copilot 명령어 파일 확인:

```bash
ls .github/prompts/
```

아래 파일들이 모두 보이면 정상이다.

```
speckit.analyze.prompt.md
speckit.checklist.prompt.md
speckit.clarify.prompt.md
speckit.constitution.prompt.md
speckit.implement.prompt.md
speckit.plan.prompt.md
speckit.specify.prompt.md
speckit.tasks.prompt.md
```

`.specify` 내부도 확인한다.

```bash
ls .specify/
```

예상 출력:

```
memory/   specs/   templates/
```

### 3-4. VS Code 에서 프로젝트 폴더 열기 (중요)

`/speckit.*` slash 명령어가 동작하려면 VS Code 가 `login-project` 폴더를 **루트**로 인식해야 한다.

```bash
code .
```

또는 VS Code 상단 메뉴에서 `File → Open Folder` → `login-project` 폴더를 선택한다.

VS Code 가 `login-project` 를 루트로 열면 좌측 탐색기에 `.github/`, `.specify/` 폴더가 보인다.

> **확인 포인트 ✅**  
> 좌측 탐색기에 `.github/` 와 `.specify/` 가 보이면 Part 3 완료.

---

## Part 4. Copilot 채팅창 설정 

### 4-1. Copilot Chat 패널 열기

아래 방법 중 하나로 Copilot Chat 을 연다.

| 방법 | 단축키 / 경로 |
|---|---|
| 키보드 단축키 (Windows/Linux) | `Ctrl + Alt + I` |
| 키보드 단축키 (Mac) | `Cmd + Option + I` |
| 상단 메뉴 | `View → GitHub Copilot Chat` |
| VS Code 우측 상단 아이콘 | Copilot 아이콘 클릭 → `Open Chat` |

### 4-2. Agent 모드 확인

Copilot Chat 패널 하단 입력창 옆 드롭다운에서 모드를 확인한다.

```
[ Ask ]  [ Edit ]  [ Agent ]
```

반드시 `Agent` 를 선택해야 한다.

> **Agent 모드를 선택해야 하는 이유**  
> `Ask` 모드: 질문과 답변만 가능, 파일 생성 불가  
> `Edit` 모드: 선택한 파일만 수정 가능  
> `Agent` 모드: 파일 생성·수정·명령 실행 모두 가능 → **SDD 실습에 필수**

### 4-3. slash 명령어 확인

Copilot Chat 입력창에 `/` 를 입력하면 사용 가능한 명령어 목록이 나타난다.

```
/speckit.constitution
/speckit.specify
/speckit.clarify
/speckit.checklist
/speckit.plan
/speckit.tasks
/speckit.analyze
/speckit.implement
```

위 목록이 보이면 준비가 완료된 것이다.  
목록이 보이지 않으면 [트러블슈팅 — Copilot Chat 에서 명령어가 보이지 않는다](#copilot-chat-에서-speckit-명령어가-보이지-않는다) 를 확인한다.

> **확인 포인트 ✅**  
> `/speckit.*` 명령어 목록이 보이면 Part 4 완료. 실습 시작 가능.

---

## Part 5. SDD 실습 1단계 — 기본 로그인 화면

이제부터 모든 `/speckit.*` 명령어는 터미널이 아닌 **Copilot Chat 패널** 에 입력한다.  
Copilot 이 파일 생성 또는 수정을 제안하면 반드시 **`Accept`** 버튼을 클릭하여 적용한다.

### 실습 목표

HTML + CSS + JavaScript 로 구성된 로그인 화면을 SDD 9단계를 거쳐 만든다.

copilot-instructions.md 에 "모든 AI 응답(설명/요약/진행 보고/결과 보고/산출물)은 한국어로 작성합니다."를 추가한다.

---

### Step 1. Constitution — 프로젝트 원칙 설정

> **[SDD 기반 원칙]** — `constitution.md` 는 프로젝트 전체에 적용되는 규칙이다.  
> **이 단계는 프로젝트에서 1회만 실행한다.**
>
> **테스트 원칙도 여기에 함께 작성하는 이유**  
> constitution.md 는 Copilot 이 모든 명령에서 **항상 참조**하는 배경 지식이다.  
> 테스트 기준을 여기에 명시하면, 나중에 `/speckit.checklist testing` 을 실행할 때  
> Copilot 이 "이 프로젝트의 테스트 방식은 이렇다"를 이미 알고 있는 상태에서 체크리스트를 만든다.

**Copilot Chat** 에 아래를 입력한다.

```
/speckit.constitution
이 프로젝트는 교육용 웹 로그인 화면입니다.

[개발 원칙]
- HTML, CSS, JavaScript 만 사용한다 (외부 프레임워크 사용 금지)
- 하나의 index.html 파일로 구성한다
- 코드에 한국어 주석을 달아 초심자가 이해할 수 있도록 한다
- 모바일과 데스크탑 모두에서 보기 좋게 만든다
- 불필요하게 복잡한 구조는 피하고 단순하게 작성한다
- 모든 로그 및 AI 와의 대화는 한국어로 한다

[테스트 원칙]
- implement 완료 후 반드시 /speckit.checklist testing 으로 품질을 검증한다
- 테스트는 브라우저에서 직접 시나리오를 실행하는 방식으로 진행한다 (자동화 테스트 도구 미사용)
- 각 수용 기준(Given-When-Then)에 대응하는 테스트 시나리오를 1:1 로 검증한다
- 정상 흐름(Happy Path)과 오류 흐름(Error Path)을 모두 테스트한다
- 모바일 화면(375px 기준)에서도 동작을 확인한다
- constitution.md 의 개발 원칙 준수 여부(단일 파일, 한국어 주석, 외부 라이브러리 미사용)도 테스트 항목에 포함한다
```

Copilot 이 파일 생성을 제안하면 `Accept` 를 클릭한다.

**터미널** 에서 저장 여부를 확인한다.

```bash
cat .specify/memory/constitution.md
```

> **확인 포인트 ✅**  
> `constitution.md` 에 `[개발 원칙]` 과 `[테스트 원칙]` 섹션이 **모두** 포함되어 있는가?  
> 이후 `/speckit.checklist testing` 실행 시 이 원칙이 체크리스트에 자동 반영된다.

---

### Step 2. Specify 001 — 요구사항 정의

> **[SDD 1단계: Specify]** — "무엇을, 왜 만드는가"를 정의하는 단계다.  
> 기술 구현 방법은 적지 않는다. 사용자 관점의 요구사항만 정의한다.

**Copilot Chat** 에 아래를 입력한다.

```
/speckit.specify
사용자가 아이디와 비밀번호를 입력하여 로그인하는 화면을 만들어줘.

[기능 요구사항]
- 아이디 입력 필드가 있다
- 비밀번호 입력 필드가 있다 (입력 내용은 별표로 가려진다)
- 로그인 버튼이 있다
- 아이디 또는 비밀번호가 비어 있으면 "아이디와 비밀번호를 입력하세요" 메시지를 표시한다
- 아이디가 'admin', 비밀번호가 '1234' 이면 "로그인 성공!" 메시지를 표시한다
- 아이디나 비밀번호가 틀리면 "아이디 또는 비밀번호가 올바르지 않습니다" 메시지를 표시한다
- 비밀번호 보이기/숨기기 버튼이 있다
```

파일 생성 제안이 뜨면 `Accept` 를 클릭한다.

**터미널** 에서 확인한다.

```bash
ls .specify/specs/
# 예상 출력: 001-login-page/

cat .specify/specs/001-login-page/spec.md
```

---

### Step 3. Clarify — 모호한 부분 명확하게

> **[SDD 1단계: Specify 보완]** — 명세의 모호한 부분을 미리 정의하여 구현 단계에서의 수정 비용을 줄인다.

**Copilot Chat** 에 아래를 입력한다.

```
/speckit.clarify
다음을 명확히 해줘:
- 화면 배경은 밝은 회색 계열로 한다
- 로그인 폼은 화면 정중앙에 카드 형태로 배치한다
- 오류 메시지는 빨간색으로 표시한다
- 성공 메시지는 초록색으로 표시한다
- 로그인 버튼은 파란색으로 한다
- 페이지 제목은 "로그인" 으로 한다
- Enter 키를 눌러도 로그인이 시도되도록 한다
```

파일 수정 제안이 뜨면 `Accept` 를 클릭한다.

---

### Step 4. Checklist — 명세 완성도 점검 (specify 후)

> **[SDD 1단계: Specify 최종 검증]** — `clarify` 로 모호함을 제거한 직후, `plan` 으로 넘어가기 전에  
> spec.md 에 빠진 항목이 없는지 도메인별로 점검한다.  
> **이 단계에서 발견된 구멍은 수정 비용이 가장 작다.** `plan` 이후에 발견하면 계획도 같이 고쳐야 한다.

#### 4-1. 기본 요구사항 체크리스트

**Copilot Chat** 에 아래를 입력한다.

```
/speckit.checklist
체크리스트를 기준으로 spec.md 를 정밀 분석하여 결과를 보고해 줘.
누락된 항목이 있으면 어떻게 수정해야 하는지 알려줘.
결과를 .specify/specs/001-login-page/checklists/requirements.md 로 저장해줘.
```

**터미널** 에서 저장 여부를 확인한다.

```bash
cat .specify/specs/001-login-page/checklists/requirements.md
```

#### 4-2. UX 관점 체크리스트

**Copilot Chat** 에 아래를 입력한다.

```
/speckit.checklist ux
로그인 화면의 UX 관점에서 spec.md 를 점검해줘.
빠진 UX 요구사항이 있으면 무엇을 추가해야 하는지 알려줘.
결과를 .specify/specs/001-login-page/checklists/ux.md 로 저장해줘.
```

**터미널** 에서 확인한다.

```bash
cat .specify/specs/001-login-page/checklists/ux.md
```

#### 4-3. 체크리스트 결과 처리

`[ ]` (미통과) 항목이 있으면 다음과 같이 처리한다.

```
[미통과 항목 발견 시 처리 순서]

1. Copilot Chat 에서 수정 방법을 물어본다
   예) "CHK003 항목 — spec.md 에 어떤 내용을 추가해야 해?"

2. spec.md 를 직접 수정하거나 Copilot 에게 수정을 요청한다

3. 체크리스트를 재실행하여 통과 여부를 확인한다
   예) "/speckit.checklist ux"

4. 모든 항목 통과 → Step 5 (Plan) 로 진행
```

> **확인 포인트 ✅**  
> `checklists/requirements.md`, `checklists/ux.md` 생성 확인,  
> 미통과 항목 spec.md 보완 완료 → Step 5 로 진행.

---

### Step 5. Plan — 기술 설계

> **[SDD 2단계: Plan]** — "어떻게 만들 것인가"를 기술적으로 설계하는 단계다.  
> `constitution.md` 의 원칙(단일 HTML 파일, 외부 라이브러리 금지)을 Copilot 이 자동으로 반영한다.

**Copilot Chat** 에 아래를 입력한다.

```
/speckit.plan
단일 HTML 파일(index.html) 하나로 모든 것을 구현한다.
CSS는 style 태그 안에 작성한다.
JavaScript는 script 태그 안에 작성한다.
외부 라이브러리나 CDN을 사용하지 않는다.
Flexbox를 사용하여 중앙 정렬한다.
```

파일 생성 제안이 뜨면 `Accept` 를 클릭한다.

**터미널** 에서 확인한다.

```bash
cat .specify/specs/001-login-page/plan.md
```

---

### Step 6. Tasks — 작업 목록 생성

> **[SDD 3단계: Task]** — `plan.md` 를 바탕으로 즉시 실행 가능한 작업 목록을 만든다.

**Copilot Chat** 에 아래를 입력한다.

```
/speckit.tasks
```

파일 생성 제안이 뜨면 `Accept` 를 클릭한다.

**터미널** 에서 확인한다.

```bash
cat .specify/specs/001-login-page/tasks.md
```

예상 출력:

```
[ ] Task 1: HTML 기본 구조 및 폼 요소 작성
[ ] Task 2: CSS로 전체 레이아웃 및 카드 스타일 작성
[ ] Task 3: 입력 필드 및 버튼 스타일 작성
[ ] Task 4: JavaScript 유효성 검사 로직 작성
[ ] Task 5: 비밀번호 보이기/숨기기 기능 작성
[ ] Task 6: Enter 키 이벤트 처리 작성
[ ] Task 7: 성공/오류 메시지 표시 기능 작성
```

---

### Step 7. Analyze — 최종 점검

> **[SDD 2단계: Plan 검증]** — `spec.md`, `plan.md`, `tasks.md`, `constitution.md` 간 불일치를 자동으로 탐지한다.

**Copilot Chat** 에 아래를 입력한다.

```
/speckit.analyze
```

"이상 없음" 또는 "검토 완료" 메시지가 나오면 다음 단계로 진행한다.  
불일치가 발견되면 Copilot 이 안내하는 항목을 수정한 후 `/speckit.analyze` 를 재실행한다.

---

### Step 8. Implement — 코드 생성

> **[SDD 4단계: Implement]** — 사양을 코드로 번역하는 단계다.  
> `spec.md` 의 수용 기준이 코드에 1:1 로 반영되는지 이후 테스트에서 확인한다.

**Copilot Chat** 에 아래를 입력한다.

```
/speckit.implement
```

Copilot 이 파일 생성을 제안하면 `Accept` 버튼을 클릭한다.  
여러 파일이 생성될 경우 각각 `Accept` 를 클릭하거나 `Accept All` 을 클릭한다.

생성 완료 후 **터미널** 에서 확인한다.

```bash
ls -la
# index.html 이 생성되어 있어야 한다

cat index.html
```

---

### Step 9. Checklist — 구현 품질 검증 (implement 후)

> **[품질 게이트]** — `implement` 완료 후 생성된 코드가 `spec.md` 의 모든 수용 기준을 충족하는지  
> 테스트 시나리오 관점에서 점검한다.
>
> **constitution.md 테스트 원칙이 여기서 작동한다**  
> Step 1 에서 constitution.md 에 `[테스트 원칙]` 을 작성해 두었기 때문에,  
> Copilot 은 체크리스트를 만들 때 **두 가지를 동시에 참조**한다.  
> - `spec.md` → "이 기능이 무엇을 해야 하는가" (수용 기준)  
> - `constitution.md` → "이 프로젝트는 어떻게 테스트해야 하는가" (테스트 원칙)

#### 9-1. constitution 기반 테스트 체크리스트 실행

**Copilot Chat** 에 아래를 입력한다.

```
/speckit.checklist testing
생성된 index.html 이 spec.md 의 모든 수용 기준을 충족하는지
테스트 시나리오 관점에서 점검해줘.
constitution.md 의 [테스트 원칙] 도 함께 반영해서 체크리스트를 만들어줘.
누락되거나 구현되지 않은 기능이 있으면 알려줘.
결과를 .specify/specs/001-login-page/checklists/testing.md 로 저장해줘.
```

**터미널** 에서 확인한다.

```bash
cat .specify/specs/001-login-page/checklists/testing.md
```

#### 9-2. 브라우저에서 직접 시나리오 실행

체크리스트를 보면서 브라우저에서 **순서대로** 각 항목을 직접 실행하여 체크한다.

**수용 기준 검증 순서:**

```
1. admin/1234 입력 → 로그인 버튼 클릭         → 로그인 성공! 확인
2. 빈 칸 상태로 제출                          → 안내 메시지 확인
3. 틀린 정보(admin/0000) 입력 후 제출         → 오류 메시지 확인
4. 비밀번호 필드에 값 입력 후 👁 클릭         → 비밀번호 보이기 확인
5. 다시 👁 클릭                              → 비밀번호 숨기기 확인
6. admin/1234 입력 후 Enter 키               → Enter 로그인 확인
```

**constitution 원칙 검증 순서:**

```
7. VS Code 에서 index.html 열어 <style> 태그 확인    → 외부 CSS 없음 확인
8. <script src=...> 또는 CDN 링크 없는지 확인        → 외부 라이브러리 없음 확인
9. 한국어 주석 존재 여부 확인                        → 주석 확인
10. 브라우저 폭 375px 로 줄인 후 화면 확인            → 모바일 확인
    (Chrome DevTools → 모바일 아이콘 클릭)
```

#### 9-3. 미통과 항목 처리

```
[ ] 항목 발견 시:

1. 수용 기준 미통과:
   Copilot Chat 에서 수정 요청
   예) "index.html 에서 로그인 성공 후 입력 필드가 초기화되지 않아.
        spec.md 수용 기준에 맞게 수정해줘."

2. constitution 원칙 미통과:
   Copilot Chat 에서 수정 요청
   예) "index.html 에 외부 라이브러리가 포함되어 있어.
        constitution.md 의 외부 프레임워크 금지 원칙에 맞게 제거해줘."

3. Ctrl+Shift+R (강제 새로고침) 후 해당 항목 재확인

4. 모든 항목 통과 → Part 6 으로 진행
```

> **확인 포인트 ✅**  
> `checklists/testing.md` 생성 확인,  
> 수용 기준 항목 + constitution 원칙 항목 모두 체크 완료 → Part 5 완료.

---

## Part 6. 결과물 실행 및 테스트

### 6-1. 브라우저에서 열기

**방법 1: Live Server 확장 프로그램 (권장)**

1. VS Code 좌측 사이드바에서 확장 프로그램 아이콘(네모 4개)을 클릭한다.
2. 검색창에 `Live Server` 를 입력한다.
3. `Live Server` (작성자: Ritwick Dey) 를 설치한다.
4. 파일 탐색기에서 `index.html` 을 우클릭한다.
5. `Open with Live Server` 를 선택한다.

**방법 2: Python 내장 서버**

터미널에서 아래를 실행한다.

```bash
python3 -m http.server 8080
```

VS Code 하단 `포트(PORTS)` 탭에서 8080 포트의 지구본 아이콘을 클릭한다.

> **방법 2 사용 시 주의**: 서버를 실행한 터미널 창은 닫지 않는다.  
> 이후 파트에서 기능을 추가한 후에는 브라우저에서 `Ctrl+Shift+R` (강제 새로고침) 으로 반영한다.

### 6-2. 기본 동작 테스트

| 시나리오 | 아이디 | 비밀번호 | 예상 결과 |
|---|---|---|---|
| 빈 칸 제출 | (비워둠) | (비워둠) | "아이디와 비밀번호를 입력하세요" |
| 로그인 성공 | admin | 1234 | "로그인 성공!" (초록색) |
| 로그인 실패 | admin | 0000 | "아이디 또는 비밀번호가 올바르지 않습니다" (빨간색) |
| 비밀번호 보이기 | 아무 값 | 아무 값 | 비밀번호 텍스트가 보임 |
| Enter 키 로그인 | admin | 1234 | Enter 키로도 로그인 성공 |

### 6-3. 예상 결과물 화면 구성

```
+------------------------------------------+
|                                          |
|   +----------------------------------+   |
|   |           로그인                 |   |
|   |                                  |   |
|   |  아이디                          |   |
|   |  [                          ]    |   |
|   |                                  |   |
|   |  비밀번호                        |   |
|   |  [                        👁]    |   |
|   |                                  |   |
|   |  [        로그인 버튼        ]   |   |
|   |                                  |   |
|   |  ← 메시지 표시 영역 (빨강/초록)  |   |
|   +----------------------------------+   |
|                                          |
+------------------------------------------+
  배경: 밝은 회색     카드: 흰색 + 그림자
```

> **Copilot 과 Gemini 결과물 비교 포인트**  
> SDD 명령어와 요구사항이 동일하더라도 AI 모델에 따라 코드 스타일이 달라진다.  
> 이것이 SDD 의 강점이다: **어떤 AI 를 써도 명세가 일관되면 결과물도 일관된다.**

---

## Part 7. SDD — 기존 명세(001) 변경 대처

### 이 파트에서 배우는 것

실제 개발에서 요구사항은 항상 변한다.  
이 파트는 **이미 완성된 기능(001)의 요구사항이 바뀔 때** SDD 에서 어떻게 대처하는지를 체험한다.

### 변경 시나리오

> **기획자 요청**:  
> "로그인 성공 후 '로그인 성공!' 메시지를 2초 동안 보여준 뒤,  
> '환영합니다, admin 님!' 으로 교체하고 비밀번호 입력 필드를 초기화해 주세요."

### 나쁜 방법 vs 올바른 SDD 방법

```
[나쁜 방법 — 코드만 바꾸는 경우]
  index.html 수정 → 배포
       ↓
  spec.md 는 여전히 "로그인 성공!" 만 표시하라고 적혀 있음
  → 6개월 후 신규 팀원이 spec.md 를 보고 잘못된 방향으로 개발
  → 명세와 코드가 어긋나는 기술 부채 누적

[올바른 SDD 방법 — 명세부터 수정]
  spec.md 수정 → clarify → checklist → analyze
                             ↓
                   plan/tasks 영향 범위 자동 탐지 → 수정 → implement
```

> **핵심 원칙**  
> SDD 에서 **spec.md 가 진실의 근원**이다.  
> 변경은 반드시 spec.md 수정부터 시작하고, 코드는 마지막에 수정한다.

---

### Step 1. spec.md 직접 수정

**터미널** 또는 VS Code 파일 탐색기에서 `.specify/specs/001-login-page/spec.md` 를 열어 수정한다.

**수정 전 — 기존 수용 기준 (예시):**

```markdown
## 수용 기준

Given: 사용자가 올바른 아이디(admin)와 비밀번호(1234)를 입력했을 때
When:  로그인 버튼을 클릭하면
Then:  "로그인 성공!" 초록색 메시지가 표시된다
```

**수정 후 — 변경된 수용 기준:**

```markdown
## 수용 기준

Given: 사용자가 올바른 아이디(admin)와 비밀번호(1234)를 입력했을 때
When:  로그인 버튼을 클릭하면
Then:  "로그인 성공!" 초록색 메시지가 2초 동안 표시된다
Then:  2초 후 메시지가 "환영합니다, admin 님!" 으로 교체된다
Then:  비밀번호 입력 필드가 초기화된다 (아이디 필드는 유지)

## 변경 이력
- 2026-06-03: 로그인 성공 후 환영 메시지 및 필드 초기화 요구사항 추가 (기획팀 요청)
```

> **변경 이력 기록의 중요성**  
> spec.md 에 변경 이력을 남기면 나중에 "왜 이렇게 바뀌었는가"를 추적할 수 있다.

---

### Step 2. Clarify — 변경된 명세 보완

**Copilot Chat** 에 아래를 입력한다.

```
/speckit.clarify
spec.md 가 수정되었어.
로그인 성공 후 동작 변경에 관해 다음을 명확히 해줘:
- 2초 타이머는 setTimeout 을 사용한다
- 메시지 교체 시 애니메이션(페이드) 효과는 사용하지 않는다
- 비밀번호 필드만 초기화하고 아이디 필드는 그대로 둔다
- 이미 타이머가 동작 중이면 새 타이머 무시 (중복 실행 방지)
```

---

### Step 3. Checklist — 변경된 명세 점검

**Copilot Chat** 에 아래를 입력한다.

```
/speckit.checklist
spec.md 가 수정되었어.
변경된 "로그인 성공 후 동작" 부분을 중심으로 누락된 요구사항이 없는지 점검해줘.
```

`[ ]` 항목이 있으면 spec.md 에 추가한 후 재실행한다.

---

### Step 4. Analyze — 변경 영향 범위 탐지

**Copilot Chat** 에 아래를 입력한다.

```
/speckit.analyze
spec.md 가 수정되었어.
변경된 내용이 plan.md 및 tasks.md 와 일치하는지 분석해줘.
```

불일치가 발견되면 Copilot 이 안내하는 파일(plan.md, tasks.md)을 수정한 후 재실행한다.

---

### Step 5. Plan 재실행 — 계획 업데이트

**Copilot Chat** 에 아래를 입력한다.

```
/speckit.plan
spec.md 가 수정되었어.
기존 index.html 에 다음 변경 사항을 추가하는 계획을 세워줘:
- 로그인 성공 시 setTimeout(2000) 으로 2초 대기
- 2초 후 메시지를 "환영합니다, admin 님!" 으로 교체
- 비밀번호 입력 필드 값을 빈 문자열로 초기화
- 타이머 중복 실행 방지
```

---

### Step 6. Tasks 재실행 — 작업 목록 업데이트

**Copilot Chat** 에 아래를 입력한다.

```
/speckit.tasks
spec.md 와 plan.md 가 수정되었어.
변경된 내용을 반영하여 tasks.md 를 업데이트해줘.
기존 Task 는 유지하고 변경에 필요한 Task 만 추가해줘.
```

---

### Step 7. Implement — 변경 코드 반영

**Copilot Chat** 에 아래를 입력한다.

```
/speckit.implement
spec.md 가 수정되었어.
기존 index.html 에 로그인 성공 후 2초 대기 + 메시지 교체 + 비밀번호 초기화 기능을 추가해줘.
기존 코드 구조는 최대한 유지하고, 변경된 부분에는 한국어 주석으로 변경 이유를 달아줘.
```

파일 수정 제안이 뜨면 `Accept` 를 클릭한다.

---

### 7-2. 변경 결과 확인

브라우저에서 `Ctrl+Shift+R` (강제 새로고침) 을 누른다.

| 시나리오 | 예상 결과 |
|---|---|
| admin/1234 로 로그인 | "로그인 성공!" 초록색 메시지 표시 |
| 2초 대기 | 메시지가 "환영합니다, admin 님!" 으로 교체 |
| 비밀번호 필드 확인 | 자동으로 비워짐 (아이디 필드는 'admin' 유지) |
| 버튼 재클릭 (2초 내) | 타이머 중복 실행 없음 |
| 틀린 정보 로그인 | 오류 메시지는 기존과 동일 |

#### constitution 테스트 원칙 기반 재검증

**Copilot Chat** 에 아래를 입력한다.

```
/speckit.checklist testing
spec.md 가 수정되었어.
변경된 "로그인 성공 후 동작" 부분을 중심으로
constitution.md 의 [테스트 원칙] 을 반영하여 테스트 항목을 추가해줘.
기존 testing.md 에 새 항목만 추가해줘 (기존 항목은 삭제하지 마).
```

### 7-3. 이 파트에서 배운 것 정리

```
[핵심 정리]

1. 요구사항 변경 → 반드시 spec.md 부터 수정
   코드 먼저 수정 = 명세와 코드 불일치 = 기술 부채

2. /speckit.analyze 가 변경 영향 범위를 자동으로 탐지
   어떤 파일(plan.md, tasks.md)을 수정해야 하는지 알려줌

3. 변경 이력은 spec.md 에 기록
   6개월 후 "왜 이렇게 바뀌었는가"를 추적 가능

4. constitution.md 의 [테스트 원칙] 이 명세 변경 후에도 적용된다
   변경된 부분만 /speckit.checklist testing 으로 재검증
   기존 testing.md 에 새 항목 누적 → 회귀 테스트 이력 자동 보존

5. SDD 변경 흐름:
   spec.md 수정 → clarify → checklist → analyze → plan → tasks → implement
   → checklist testing (변경분 재검증)
```

> **확인 포인트 ✅**  
> 변경된 동작 테스트 통과, testing.md 에 변경분 항목 추가 확인 → Part 7 완료.

---

## Part 8. SDD 실습 2단계 — 로그인 횟수 제한 추가

`constitution.md` 는 다시 실행하지 않는다. `specify` 부터 새 번호(002)로 진행한다.

```
이미 완료: constitution → specify(001) → ... → implement  [명세 수정 완료]
지금부터:               → specify(002) → clarify → checklist → plan → tasks → analyze → implement
```

---

### Step 1. Specify 002

> **[SDD 1단계: Specify]**

**Copilot Chat** 에 아래를 입력한다.

```
/speckit.specify
기존 로그인 화면에 로그인 횟수 제한 기능을 추가해줘.

[기능 요구사항]
- 로그인 실패 횟수를 화면에 표시한다 (예: "로그인 시도: 1/3")
- 로그인 실패를 3회 반복하면 로그인 버튼을 비활성화한다
- 버튼이 비활성화되면 "5초 후 다시 시도하세요" 메시지를 표시한다
- 5초가 지나면 실패 횟수가 초기화되고 버튼이 다시 활성화된다
- 카운트다운 숫자가 화면에 표시된다 (5, 4, 3, 2, 1)
```

파일 생성 제안이 뜨면 `Accept` 를 클릭한다.

**터미널** 에서 확인한다.

```bash
ls .specify/specs/
# 예상 출력: 001-login-page/   002-login-attempts/
```

---

### Step 2. Clarify

**Copilot Chat** 에 아래를 입력한다.

```
/speckit.clarify
다음을 명확히 해줘:
- 실패 횟수 표시는 로그인 버튼 위에 작게 표시한다
- 버튼 비활성화 상태에서는 버튼 색상이 회색으로 바뀐다
- 카운트다운 메시지는 빨간색으로 표시한다
- 로그인 성공 시에는 실패 횟수가 초기화된다
- 페이지를 새로고침하면 횟수가 초기화된다 (세션 유지 불필요)
```

---

### Step 3. Checklist — 명세 완성도 점검

> **[SDD 1단계: Specify 최종 검증]** — 횟수 제한 기능은 **UX** 와 **보안** 이 교차하는 영역이다.  
> 두 관점에서 모두 점검하여 구현 전에 빠진 요구사항을 찾는다.

#### 3-1. UX 관점 체크리스트

**Copilot Chat** 에 아래를 입력한다.

```
/speckit.checklist ux
로그인 횟수 제한 기능의 UX 요구사항이 spec.md 에 완전히 정의되어 있는지 점검해줘.
카운트다운, 버튼 상태 변화, 메시지 표시 등을 중심으로 확인해줘.
결과를 .specify/specs/002-login-attempts/checklists/ux.md 로 저장해줘.
```

**터미널** 에서 확인한다.

```bash
cat .specify/specs/002-login-attempts/checklists/ux.md
```

#### 3-2. 보안 관점 체크리스트

**Copilot Chat** 에 아래를 입력한다.

```
/speckit.checklist security
로그인 횟수 제한 기능의 보안 요구사항이 spec.md 에 충분히 정의되어 있는지 점검해줘.
횟수 초기화 조건, 우회 가능성 등을 중심으로 확인해줘.
결과를 .specify/specs/002-login-attempts/checklists/security.md 로 저장해줘.
```

**터미널** 에서 확인한다.

```bash
cat .specify/specs/002-login-attempts/checklists/security.md
```

#### 3-3. 미통과 항목 처리

```
[ ] 항목 발견 시:

1. Copilot Chat 에서 수정 방법 확인
   예) "CHK004 - 카운트다운 후 버튼 재활성화 안내를 spec.md 에 추가해줘"

2. 체크리스트 재실행으로 통과 여부 확인
   예) "/speckit.checklist ux"

3. 모든 항목 통과 → Step 4 (Plan) 로 진행
```

> **확인 포인트 ✅**  
> `checklists/ux.md`, `checklists/security.md` 생성 확인,  
> 미통과 항목 spec.md 보완 완료 → Step 4 로 진행.

---

### Step 4. Plan

**Copilot Chat** 에 아래를 입력한다.

```
/speckit.plan
기존 index.html 파일을 수정하여 기능을 추가한다.
JavaScript 의 setTimeout 을 사용하여 카운트다운을 구현한다.
실패 횟수는 JavaScript 변수로 관리한다.
별도 파일을 만들지 않고 index.html 안에서 모두 처리한다.
```

---

### Step 5. Tasks

```
/speckit.tasks
```

---

### Step 6. Analyze

```
/speckit.analyze
```

---

### Step 7. Implement

```
/speckit.implement
```

파일 수정 제안이 뜨면 `Accept` 를 클릭한다.

---

### 8-2. 동작 테스트

브라우저에서 `Ctrl+Shift+R` (강제 새로고침) 을 누른다.

| 시나리오 | 예상 결과 |
|---|---|
| 틀린 비밀번호 1회 입력 | "로그인 시도: 1/3" 표시 |
| 틀린 비밀번호 3회 입력 | 버튼 비활성화(회색), 카운트다운 시작 |
| 카운트다운 진행 | "5초 후 다시 시도하세요 (5, 4, 3, 2, 1)" 빨간색 표시 |
| 5초 대기 | 버튼 재활성화(파란색), 횟수 초기화 |
| 올바른 정보로 로그인 | "로그인 성공!" → 2초 후 "환영합니다, admin 님!" (Part 7 변경 유지) |

#### constitution 테스트 원칙 기반 검증

**Copilot Chat** 에 아래를 입력한다.

```
/speckit.checklist testing
횟수 제한 기능(002)이 구현된 index.html 을
constitution.md 의 [테스트 원칙] 과 spec.md 의 수용 기준에 맞게 검증해줘.
정상 흐름·오류 흐름·constitution 원칙 준수를 모두 포함해줘.
결과를 .specify/specs/002-login-attempts/checklists/testing.md 로 저장해줘.
```

**터미널** 에서 확인한다.

```bash
cat .specify/specs/002-login-attempts/checklists/testing.md
```

> **확인 포인트 ✅**  
> `checklists/testing.md` 생성 확인, 모든 항목 체크 완료 → Part 8 완료.

---

## Part 9. SDD 실습 3단계 — 아이디 저장 기능 추가

### Step 1. Specify 003

**Copilot Chat** 에 아래를 입력한다.

```
/speckit.specify
기존 로그인 화면에 아이디 저장 기능을 추가해줘.

[기능 요구사항]
- 아이디 입력 필드 아래에 "아이디 저장" 체크박스가 있다
- 체크박스를 체크하고 로그인 성공하면 아이디가 브라우저에 저장된다
- 다음에 페이지를 열면 저장된 아이디가 자동으로 입력 필드에 채워진다
- 저장된 아이디가 있으면 체크박스도 자동으로 체크된 상태로 표시된다
- 체크박스를 해제하고 로그인하면 저장된 아이디가 삭제된다
```

파일 생성 제안이 뜨면 `Accept` 를 클릭한다.

**터미널** 에서 확인한다.

```bash
ls .specify/specs/
# 예상 출력: 001-login-page/   002-login-attempts/   003-save-id/
```

---

### Step 2. Clarify

```
/speckit.clarify
다음을 명확히 해줘:
- 아이디 저장에는 localStorage 를 사용한다
- 비밀번호는 저장하지 않는다
- 체크박스는 아이디 입력 필드와 비밀번호 입력 필드 사이에 배치한다
- 저장된 아이디가 있을 때는 비밀번호 입력 필드에 자동으로 포커스가 이동한다
```

---

### Step 3. Checklist — 명세 완성도 점검

> **[SDD 1단계: Specify 최종 검증]** — 아이디 저장 기능은 **브라우저 저장소(localStorage)** 를 사용하므로  
> **보안** 과 **UX** 관점의 점검이 모두 필요하다.

#### 3-1. 보안 관점 체크리스트

**Copilot Chat** 에 아래를 입력한다.

```
/speckit.checklist security
아이디 저장 기능의 보안 요구사항이 spec.md 에 충분히 정의되어 있는지 점검해줘.
localStorage 사용, 비밀번호 미저장 원칙 등을 중심으로 확인해줘.
결과를 .specify/specs/003-save-id/checklists/security.md 로 저장해줘.
```

**터미널** 에서 확인한다.

```bash
cat .specify/specs/003-save-id/checklists/security.md
```

#### 3-2. UX 관점 체크리스트

**Copilot Chat** 에 아래를 입력한다.

```
/speckit.checklist ux
아이디 저장 기능의 UX 요구사항이 spec.md 에 충분히 정의되어 있는지 점검해줘.
체크박스 위치, 자동 입력 동작, 포커스 이동 등을 중심으로 확인해줘.
결과를 .specify/specs/003-save-id/checklists/ux.md 로 저장해줘.
```

**터미널** 에서 확인한다.

```bash
cat .specify/specs/003-save-id/checklists/ux.md
```

#### 3-3. 미통과 항목 처리

```
[ ] 항목 발견 시:

1. 중요도 판단
   - 보안 관련 항목 → 반드시 spec.md 에 추가
   - UX 세부사항 → 팀 논의 후 추가 여부 결정

2. spec.md 수정 후 체크리스트 재실행
   예) "/speckit.checklist security"

3. 모든 항목 통과 → Step 4 (Plan) 로 진행
```

> **확인 포인트 ✅**  
> `checklists/security.md`, `checklists/ux.md` 생성 확인,  
> 미통과 항목 spec.md 보완 완료 → Step 4 로 진행.

---

### Step 4. Plan

```
/speckit.plan
localStorage 를 사용하여 아이디를 브라우저에 저장한다.
저장 키 이름은 'saved_username' 으로 한다.
페이지 로드 시 localStorage 를 확인하여 저장된 아이디를 불러온다.
별도 파일 없이 index.html 안에서 처리한다.
```

---

### Step 5. Tasks

```
/speckit.tasks
```

---

### Step 6. Analyze

```
/speckit.analyze
```

---

### Step 7. Implement

```
/speckit.implement
```

파일 수정 제안이 뜨면 `Accept` 를 클릭한다.

---

### 9-2. 동작 테스트

브라우저에서 `Ctrl+Shift+R` (강제 새로고침) 으로 변경 사항을 반영한다.

| 시나리오 | 예상 결과 |
|---|---|
| 체크박스 체크 후 로그인 성공 | 아이디가 localStorage 에 저장됨 |
| 페이지 새로고침 | 아이디 자동 입력, 체크박스 자동 체크 |
| 저장된 아이디 상태에서 비밀번호 필드 | 자동으로 포커스 이동 |
| 체크박스 해제 후 로그인 | localStorage 에서 아이디 삭제됨 |
| 페이지 새로고침 | 아이디 입력 필드가 비어 있음 |

**localStorage 저장 확인 방법 (브라우저 개발자 도구):**

```
1. 브라우저에서 F12 (개발자 도구) 를 연다.
2. Application 탭 → Storage → Local Storage → 현재 주소를 클릭한다.
3. 'saved_username' 키에 'admin' 값이 저장되어 있는지 확인한다.
```

#### constitution 테스트 원칙 기반 검증

**Copilot Chat** 에 아래를 입력한다.

```
/speckit.checklist testing
아이디 저장 기능(003)이 구현된 index.html 을
constitution.md 의 [테스트 원칙] 과 spec.md 의 수용 기준에 맞게 검증해줘.
localStorage 저장·삭제·자동 입력 흐름, constitution 원칙 준수를 모두 포함해줘.
결과를 .specify/specs/003-save-id/checklists/testing.md 로 저장해줘.
```

**터미널** 에서 확인한다.

```bash
cat .specify/specs/003-save-id/checklists/testing.md
```

> **확인 포인트 ✅**  
> `checklists/testing.md` 생성 확인, 모든 항목 체크 완료 → Part 9 완료.

---

## Part 10. 전체 산출물 구조 확인

실습이 모두 끝나면 **터미널** 에서 전체 구조를 확인한다.

```bash
find . -not -path '*/\.*' | sort
```

예상 디렉터리 구조:

```
login-project/
├── .github/
│   └── prompts/
│       ├── speckit.analyze.prompt.md
│       ├── speckit.checklist.prompt.md
│       ├── speckit.clarify.prompt.md
│       ├── speckit.constitution.prompt.md
│       ├── speckit.implement.prompt.md
│       ├── speckit.plan.prompt.md
│       ├── speckit.specify.prompt.md
│       └── speckit.tasks.prompt.md
├── .specify/
│   ├── memory/
│   │   └── constitution.md              ← [개발 원칙] + [테스트 원칙] (1회 작성)
│   └── specs/
│       ├── 001-login-page/
│       │   ├── spec.md                  ← 기본 로그인 요구사항 (Part 7 에서 수정됨)
│       │   ├── plan.md                  ← Part 7 에서 재생성됨
│       │   ├── tasks.md                 ← Part 7 에서 재생성됨
│       │   └── checklists/
│       │       ├── requirements.md      ← Step 4-1 (specify 후)
│       │       ├── ux.md                ← Step 4-2 (specify 후)
│       │       └── testing.md           ← Step 9 + Part 7 변경분 누적
│       ├── 002-login-attempts/
│       │   ├── spec.md
│       │   ├── plan.md
│       │   ├── tasks.md
│       │   └── checklists/
│       │       ├── ux.md
│       │       ├── security.md
│       │       └── testing.md
│       └── 003-save-id/
│           ├── spec.md
│           ├── plan.md
│           ├── tasks.md
│           └── checklists/
│               ├── security.md
│               ├── ux.md
│               └── testing.md
└── index.html                           ← 최종 결과물 (명세 수정 + 3가지 기능 포함)
```

### constitution.md 최종 확인

```bash
cat .specify/memory/constitution.md
```

반드시 아래 두 섹션이 모두 포함되어 있어야 한다.

```
✅ [개발 원칙] 섹션
✅ [테스트 원칙] 섹션
```

### 최종 완성 기능 전체 테스트

```
기본 로그인 (001 — Part 7 에서 수정된 버전)
[ ] 빈 칸 제출 시 안내 메시지 표시
[ ] admin/1234 로 로그인 성공 → "로그인 성공!" 표시
[ ] 2초 후 "환영합니다, admin 님!" 으로 메시지 교체
[ ] 비밀번호 필드 자동 초기화 (아이디 필드는 유지)
[ ] 틀린 정보 입력 시 오류 메시지 표시
[ ] 비밀번호 보이기/숨기기 동작
[ ] Enter 키로 로그인 가능

로그인 횟수 제한 (002)
[ ] 실패 시 "로그인 시도: N/3" 표시
[ ] 3회 실패 시 버튼 비활성화 (회색)
[ ] 카운트다운 5→1 표시
[ ] 5초 후 버튼 재활성화 및 횟수 초기화

아이디 저장 (003)
[ ] 체크박스 체크 후 로그인 성공 시 아이디 저장
[ ] 새로고침 후 아이디 자동 입력 및 체크박스 자동 체크
[ ] 체크박스 해제 후 로그인 시 아이디 삭제
```

### .specify/specs 산출물 전체 확인

```bash
find .specify/specs -name "*.md" | sort
```

예상 출력:

```
.specify/specs/001-login-page/checklists/requirements.md
.specify/specs/001-login-page/checklists/testing.md
.specify/specs/001-login-page/checklists/ux.md
.specify/specs/001-login-page/plan.md
.specify/specs/001-login-page/spec.md
.specify/specs/001-login-page/tasks.md
.specify/specs/002-login-attempts/checklists/security.md
.specify/specs/002-login-attempts/checklists/testing.md
.specify/specs/002-login-attempts/checklists/ux.md
.specify/specs/002-login-attempts/plan.md
.specify/specs/002-login-attempts/spec.md
.specify/specs/002-login-attempts/tasks.md
.specify/specs/003-save-id/checklists/security.md
.specify/specs/003-save-id/checklists/testing.md
.specify/specs/003-save-id/checklists/ux.md
.specify/specs/003-save-id/plan.md
.specify/specs/003-save-id/spec.md
.specify/specs/003-save-id/tasks.md
```

> **확인 포인트 ✅**  
> 아래 세 가지가 모두 확인되면 실습 완주다.
> 1. 각 기능 폴더 아래 `checklists/testing.md` 파일이 존재한다
> 2. 001 의 `spec.md` 에 Part 7 의 변경 이력이 기록되어 있다
> 3. `constitution.md` 에 `[테스트 원칙]` 섹션이 포함되어 있다

### 실습 전체 흐름 복습

```
[최초 구현 001]
constitution(개발원칙 + 테스트원칙)
    → specify(001) → clarify → checklist(requirements·ux) →
    plan → tasks → analyze → implement
    → checklist testing(constitution 기반)

[명세 수정 — Part 7]
spec.md 수정 → clarify → checklist → analyze →
plan(재실행) → tasks(재실행) → implement
    → checklist testing(변경분 추가 — testing.md 에 누적)

[기능 추가 002]
specify(002) → clarify → checklist(ux+security) →
plan → tasks → analyze → implement
    → checklist testing(constitution 기반)

[기능 추가 003]
specify(003) → clarify → checklist(security+ux) →
plan → tasks → analyze → implement
    → checklist testing(constitution 기반)
```

---

## 실습 종료 후 Codespace 정지 방법

Codespace 환경을 사용했다면, 정지하지 않으면 무료 시간(월 60시간)이 계속 소모된다.

**방법 1 (VS Code 화면에서)**

화면 왼쪽 하단의 `>< Codespaces` 를 클릭한다.  
`Stop Current Codespace` 를 선택한다.

**방법 2 (GitHub 웹에서)**

https://github.com/codespaces 에 접속한다.  
본인 Codespace 오른쪽 `...` 메뉴에서 `Stop codespace` 를 선택한다.

---

## 3차시(Gemini)와의 차이점 요약

| 항목 | 3차시: Gemini CLI | 4차시: Copilot Free |
|---|---|---|
| 필요 계정 | GitHub + Google | GitHub 만 |
| 추가 CLI 설치 | gemini CLI 설치 필요 | 불필요 (VS Code 내장) |
| 채팅창 위치 | 터미널 (별도 창) | VS Code 패널 (오른쪽) |
| 명령어 파일 위치 | `.gemini/commands/*.toml` | `.github/prompts/*.prompt.md` |
| 코드 반영 방식 | 자동 저장 | `Accept` 버튼 클릭으로 승인 |
| 월간 채팅 한도 | 넉넉함 (Google 정책에 따름) | 50회 (절약 필요) |
| 초기 인증 방법 | 브라우저 Google 로그인 팝업 | VS Code GitHub 로그인 |
| SDD 명령어 체계 | `/speckit.*` 동일 | `/speckit.*` 동일 |

> **핵심 메시지**  
> AI 도구가 달라도 SDD 명령어(`/speckit.*`)와 아티팩트 구조(`spec.md`, `plan.md`, `tasks.md`)는 동일하다.  
> 방법론(SDD)이 도구에 종속되지 않음을 직접 체험하는 것이 이번 실습의 핵심이다.

---

## 트러블슈팅

### Copilot Chat 에서 `/speckit.*` 명령어가 보이지 않는다

VS Code 가 `login-project` 폴더를 루트로 열었는지 확인한다.

```bash
pwd
# .../login-project 가 출력되어야 한다

code .
```

`.github/prompts/` 폴더가 실제로 존재하는지 확인한다.

```bash
ls .github/prompts/
```

폴더가 없으면 `specify init` 을 다시 실행한다.

```bash
cd ..
specify init login-project --integration copilot --force
cd login-project
code .
```

---

### Agent 모드가 보이지 않는다

Copilot Chat 패널 하단 드롭다운에서 `Ask / Edit / Agent` 중 `Agent` 를 선택한다.  
`Agent` 항목 자체가 보이지 않으면 VS Code 를 최신 버전으로 업데이트한다.

```
VS Code 버전 확인: Help → About → Version
최소 요구 버전: VS Code 1.90 이상
```

---

### Copilot Chat 이 열리지 않는다

VS Code 하단 상태바에서 Copilot 아이콘 상태를 확인한다.  
아이콘에 X 표시가 있으면 GitHub 계정 로그인이 필요한 것이다.

```
1. VS Code 좌측 하단 계정 아이콘 클릭
2. Sign in with GitHub 선택
3. 브라우저에서 GitHub 계정 로그인
```

---

### specify 명령어를 찾을 수 없다

**Codespace / macOS / Linux:**

```bash
source ~/.bashrc
specify --version
```

그래도 안 되면 재설치한다.

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
source ~/.bashrc
uv tool install specify-cli --from git+https://github.com/github/spec-kit.git
```

**Windows (PowerShell):**

새 PowerShell 창을 열고 아래를 실행한다.

```powershell
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
uv tool install specify-cli --from git+https://github.com/github/spec-kit.git
```

---

### 파일 생성 후 `Accept` 버튼이 눌리지 않는다

파일 탐색기에서 직접 파일을 확인한다.  
이미 생성되어 있으면 `Accept` 없이 진행해도 무방하다.

```bash
ls -la
cat index.html | head -20
```

---

### checklists/ 폴더가 생성되지 않는다

`/speckit.checklist` 실행 후 파일이 생성되지 않는 경우, Copilot 에게 직접 저장을 요청한다.

```
/speckit.checklist ux
결과를 .specify/specs/001-login-page/checklists/ux.md 파일로 저장해줘.
```

또는 터미널에서 폴더를 직접 생성한 후 재실행한다.

```bash
mkdir -p .specify/specs/001-login-page/checklists
```

---

### 채팅 한도 50회가 부족하다

Copilot 에게 직접 아티팩트를 읽어 구현하도록 요청한다.

```
(Copilot Chat 에서)
지금까지 생성된 spec.md, plan.md, tasks.md 파일을 읽고
index.html 을 직접 작성해줘.
```

또는 채팅 대신 VS Code 의 인라인 코드 완성 기능을 활용한다.  
(인라인 제안은 코드 완성 카운터에서 차감되며 한도가 더 넉넉하다.)

---

### constitution.md 에 [테스트 원칙] 이 없다

Step 1 에서 `[테스트 원칙]` 섹션이 빠진 경우, VS Code 파일 탐색기에서  
`.specify/memory/constitution.md` 를 직접 열어 아래 내용을 파일 끝에 추가한다.

```markdown
[테스트 원칙]
- implement 완료 후 반드시 /speckit.checklist testing 으로 품질을 검증한다
- 테스트는 브라우저에서 직접 시나리오를 실행하는 방식으로 진행한다 (자동화 테스트 도구 미사용)
- 각 수용 기준(Given-When-Then)에 대응하는 테스트 시나리오를 1:1 로 검증한다
- 정상 흐름(Happy Path)과 오류 흐름(Error Path)을 모두 테스트한다
- 모바일 화면(375px 기준)에서도 동작을 확인한다
- constitution.md 의 개발 원칙 준수 여부(단일 파일, 한국어 주석, 외부 라이브러리 미사용)도 테스트 항목에 포함한다
```

저장 후 확인한다.

```bash
cat .specify/memory/constitution.md
```

---

### /speckit.checklist testing 결과가 너무 일반적이다 (constitution 원칙이 반영 안 됨)

constitution.md 의 `[테스트 원칙]` 을 명시적으로 언급하여 재실행한다.

```
/speckit.checklist testing
constitution.md 의 [테스트 원칙] 과 spec.md 의 수용 기준을 모두 참조해서
테스트 체크리스트를 다시 만들어줘.
단일 파일 원칙, 한국어 주석, 모바일 375px 확인 항목을 반드시 포함해줘.
```

---

### Part 7 에서 spec.md 수정 후 plan.md 가 자동으로 바뀌지 않는다

`/speckit.plan` 을 명시적으로 재실행해야 한다.  
`/speckit.analyze` 는 불일치를 탐지만 하고 자동으로 파일을 수정하지 않는다.

```
[올바른 순서]
spec.md 수정 → /speckit.analyze (불일치 확인) → /speckit.plan (재실행) → /speckit.tasks (재실행)
```

---

### 브라우저에서 화면이 열리지 않는다 (Codespace 포트 포워딩)

VS Code 하단 `포트(PORTS)` 탭을 클릭한다.  
8080 포트의 지구본 아이콘을 클릭한다.  
목록에 없으면 `포트 추가` 버튼을 클릭하고 `8080` 을 입력한다.  
포트 가시성(Visibility) 이 `Private` 이면 `Public` 으로 변경한다.

---

### index.html 이 생성되지 않았다

Copilot Chat 에서 직접 요청한다.

```
(Copilot Chat 에서)
/speckit.implement 명령을 실행했지만 index.html 이 생성되지 않았어.
.specify/specs/001-login-page/ 폴더의 spec.md, plan.md, tasks.md 를 참고해서
index.html 을 현재 폴더(login-project/)에 생성해줘.
```

---

## 전체 명령어 요약표

| 단계 | 위치 | 명령어 또는 작업 |
|---|---|---|
| uv 설치 (Linux/Mac) | 터미널 | `curl -LsSf https://astral.sh/uv/install.sh \| sh && source ~/.bashrc` |
| uv 설치 (Windows) | PowerShell | `powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 \| iex"` |
| specify 설치 | 터미널 | `uv tool install specify-cli --from git+https://github.com/github/spec-kit.git` |
| 프로젝트 생성 | 터미널 | `specify init login-project --integration copilot` |
| 폴더 이동 | 터미널 | `cd login-project` |
| VS Code 재시작 | 터미널 | `code .` |
| 명령어 파일 확인 | 터미널 | `ls .github/prompts/` |
| Copilot Chat 열기 | VS Code | `Ctrl + Alt + I` |
| Agent 모드 선택 | Copilot Chat | 하단 드롭다운에서 `Agent` 선택 |
| 원칙+테스트원칙 설정 (1회만) | Copilot Chat | `/speckit.constitution` |
| constitution 확인 | 터미널 | `cat .specify/memory/constitution.md` |
| 요구사항 정의 | Copilot Chat | `/speckit.specify` |
| 모호함 제거 | Copilot Chat | `/speckit.clarify` |
| 명세 점검 — 기본 | Copilot Chat | `/speckit.checklist` |
| 명세 점검 — UX | Copilot Chat | `/speckit.checklist ux` |
| 명세 점검 — 보안 | Copilot Chat | `/speckit.checklist security` |
| 구현 후 품질 검증 (constitution 기반) | Copilot Chat | `/speckit.checklist testing` |
| 기술 설계 | Copilot Chat | `/speckit.plan` |
| 작업 목록 | Copilot Chat | `/speckit.tasks` |
| 일관성 점검 | Copilot Chat | `/speckit.analyze` |
| 코드 생성 | Copilot Chat | `/speckit.implement` |
| 서버 실행 | 터미널 | `python3 -m http.server 8080` |
| 강제 새로고침 | 브라우저 | `Ctrl+Shift+R` |

**체크리스트 실행 시점 요약:**

| 시점 | 도메인 | 참조 기준 | 이유 |
|---|---|---|---|
| clarify 직후 / plan 이전 | (기본), ux | spec.md | 명세 완성도 점검 — 수정 비용 최저 |
| clarify 직후 / plan 이전 | security | spec.md + constitution.md | 보안 요구사항 사전 검증 |
| spec.md 수정 직후 | (기본) | 변경된 spec.md | 변경된 명세의 새 구멍 점검 |
| implement 직후 | **testing** | **spec.md + constitution.md [테스트 원칙]** | **코드가 명세와 프로젝트 원칙을 모두 충족하는지 검증** |

**명세 변경 시 명령어 순서:**

```
spec.md 수정 (직접 편집)
    → /speckit.clarify   (변경 내용 보완)
    → /speckit.checklist (변경된 명세 점검)
    → /speckit.analyze   (plan.md·tasks.md 불일치 탐지)
    → /speckit.plan      (plan.md 재생성)
    → /speckit.tasks     (tasks.md 재생성)
    → /speckit.implement (코드 반영)
    → /speckit.checklist testing (변경분 재검증)
```
---

*본 실습 자료는 GitHub Spec Kit 공식 문서를 기반으로 작성되었습니다.*  
*참고: https://github.com/github/spec-kit*  
*GitHub Copilot Free 기준: 월 50회 채팅, 2,000회 코드 완성 (2026년 6월 기준)*
