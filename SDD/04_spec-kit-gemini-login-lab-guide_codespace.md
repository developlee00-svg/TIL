# GitHub Spec Kit 실습 가이드 — 3차시
## 주제: 로그인 화면 만들기 + 명세 수정 + 기능 추가

> **이 실습은 3차시입니다.**  
> 1차시(SDD 방법론) → 2차시(Spec Kit 가이드) 에서 배운 개념을 직접 실행합니다.  
> 명령어의 의미를 먼저 이해한 상태에서 실습을 진행하면 훨씬 효과적입니다.

**목표**: GitHub Codespace 환경에서 SDD 전체 흐름을 체험하고, 로그인 화면을 완성한다  
**사용 도구**: GitHub Codespaces, Gemini CLI, specify CLI  
**결과물**: HTML + CSS + JavaScript 기반 로그인 화면 (명세 변경 + 3가지 기능 포함)

---

## 목차

1. [사전 확인 — Gemini CLI 경로 안내](#사전-확인--gemini-cli-경로-안내)
2. [실습 전 준비 사항](#실습-전-준비-사항)
3. [실습 개요](#실습-개요)
4. [Part 1. Codespace 생성](#part-1-codespace-생성-약-15분)
5. [Part 2. 도구 설치](#part-2-도구-설치-약-10분)
6. [Part 3. 프로젝트 초기화](#part-3-프로젝트-초기화-약-10분)
7. [Part 4. 기본 로그인 화면 구현](#part-4-sdd-실습-1단계--기본-로그인-화면-약-65분)
   - Step 4. Checklist (specify 후 — UX·요구사항 점검)
   - Step 9. Checklist (implement 후 — 품질 검증)
8. [Part 5. 결과물 실행 및 테스트](#part-5-결과물-실행-및-테스트-약-15분)
9. [Part 6. 기존 명세(001) 변경 대처](#part-6-sdd--기존-명세001-변경-대처-약-30분)
   - 나쁜 방법 vs 올바른 방법 비교
   - Step 1. spec.md 직접 수정
   - Step 2~7. clarify → checklist → analyze → plan → tasks → implement
10. [Part 7. 로그인 횟수 제한 추가](#part-7-sdd-실습-2단계--로그인-횟수-제한-추가-약-30분)
    - Step 3. Checklist (specify 후 — UX·보안 점검)
11. [Part 8. 아이디 저장 기능 추가](#part-8-sdd-실습-3단계--아이디-저장-기능-추가-약-30분)
    - Step 3. Checklist (specify 후 — 보안 점검)
12. [Part 9. 전체 산출물 구조 확인](#part-9-전체-산출물-구조-확인)
13. [트러블슈팅](#트러블슈팅)
14. [전체 명령어 요약표](#전체-명령어-요약표)
15. [실습 진행 시간 배분](#실습-진행-시간-배분)

---

## 사전 확인 — Gemini CLI 경로 안내

`specify init` 실행 시 AI 에이전트별로 생성되는 폴더 경로가 다르다.  
이 실습은 **Gemini CLI** 를 사용하므로 아래 경로를 확인한다.

| 에이전트 | 명령어 파일 위치 | 파일 형식 |
|---|---|---|
| Gemini CLI | `.gemini/commands/` | `.toml` |
| GitHub Copilot | `.github/prompts/` | `.prompt.md` |
| Claude Code | `.claude/commands/` | `.md` |

> **주의**:  
> Gemini 연동 시 반드시 `.gemini/commands/` 를 확인해야 한다.

---

## 실습 전 준비 사항

| 필요 계정 | 가입 주소 | 비고 |
|---|---|---|
| GitHub 계정 | https://github.com/signup | 없는 경우 신규 가입 필요 |
| Google 계정 | https://accounts.google.com | Gemini CLI 인증에 사용 |

**브라우저**: Chrome 또는 Edge 권장 (팝업 허용 필수)

---

## 실습 개요

SDD(Spec-Driven Development)는 코드를 먼저 짜지 않는다.  
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
> Gemini 가 constitution.md 를 참조하여 **테스트 기준을 자동으로 반영**한다.  
> 즉, constitution → checklist(testing) 는 "우리 프로젝트의 품질 기준으로 코드를 검증하라"는 명령이다.  
>
> ```
> constitution.md 에 테스트 원칙 명시
>       ↓
> implement 완료 후 /speckit.checklist testing 실행
>       ↓
> Gemini 가 constitution.md 의 테스트 원칙 + spec.md 의 수용 기준을 동시에 참조
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
| Part 4 | 기본 로그인 화면 구현 (아이디/비밀번호 검증) | 001 최초 구현 |
| Part 6 | 001 명세 변경 — 로그인 성공 후 동작 추가 | 001 명세 수정 |
| Part 7 | 로그인 횟수 제한 + 카운트다운 기능 추가 | 002 신규 추가 |
| Part 8 | 아이디 저장 (localStorage) 기능 추가 | 003 신규 추가 |

---

## Part 1. Codespace 생성

### 1-1. 빈 레포지토리 만들기

1. https://github.com 에 로그인한다.
2. 오른쪽 상단 `+` 버튼 클릭 후 `New repository` 를 선택한다.
3. 아래와 같이 입력한다.

| 항목 | 입력값 |
|---|---|
| Repository name | `login-page-lab` |
| Visibility | Public 또는 Private 선택 |
| Add a README file | **반드시 체크** (체크 없으면 Codespace 생성 불가) |

4. `Create repository` 버튼을 클릭한다.

### 1-2. Codespace 실행

1. 생성된 레포지토리 페이지에서 초록색 `Code` 버튼을 클릭한다.
2. `Codespaces` 탭을 선택한다.
3. `Create codespace on main` 을 클릭한다.
4. 브라우저에서 VS Code 화면이 열릴 때까지 1~2분 기다린다.

터미널이 보이지 않으면 상단 메뉴에서 `Terminal → New Terminal` 을 선택한다.

### 1-3. 현재 위치 확인

```bash
pwd
```

예상 출력:

```
/workspaces/login-page-lab
```

> **확인 포인트 **  
> `/workspaces/login-page-lab` 이 출력되면 Part 1 완료.  

---

## Part 2. 도구 설치

### 2-1. uv 설치

`uv` 는 Python 패키지를 빠르게 설치하는 도구다. `specify CLI` 설치에 필요하다.

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
source ~/.bashrc
```

설치 확인:

```bash
uv --version
```

예상 출력:

```
uv 0.x.x
```

### 2-2. specify CLI 설치

`specify CLI` 는 SDD 명령어(`/speckit.*`)를 실행해 주는 핵심 도구다.

```bash
uv tool install specify-cli --from git+https://github.com/github/spec-kit.git
```

확인:

```bash
specify --version
```

예상 출력:

```
specify 0.x.x
```

### 2-3. Gemini CLI 설치

Gemini CLI 는 AI와 터미널에서 대화할 수 있는 Google의 도구다.  
설치를 위해 `npm` (Node.js 패키지 관리자)이 필요하다.

```bash
node --version
```

예상 출력 (`v18` 이상):

```
v20.x.x
```

> Codespace 기본 이미지에는 Node.js 가 포함되어 있어 별도 설치가 불필요하다.

```bash
npm install -g @google/gemini-cli
```

확인:

```bash
gemini --version
```

> **확인 포인트 **  
> `uv`, `specify`, `gemini` 세 가지 모두 버전이 출력되면 Part 2 완료.

---

## Part 3. 프로젝트 초기화

### 3-1. specify 프로젝트 생성

현재 위치(`/workspaces/login-page-lab`) 에서 실행한다.

```bash
specify init login-project --integration gemini
```

실행 중 에이전트 선택 화면이 나오면 `gemini` 를 선택한다.

### 3-2. 프로젝트 폴더로 이동

```bash
cd login-project
```

이동 후 현재 위치 확인:

```bash
pwd
```

예상 출력:

```
/workspaces/login-page-lab/login-project
```

> **중요**: 이후 모든 터미널 명령어와 `gemini` 실행은 반드시 이 폴더(`login-project`) 안에서 실행해야 한다.

### 3-3. 생성된 구조 확인

```bash
ls -la
```

예상 출력:

```
.gemini/
.specify/
GEMINI.md
README.md
```

Gemini 명령어 파일 확인:

```bash
ls .gemini/commands/
```

아래 파일들이 모두 보이면 정상이다.

```
speckit.analyze.toml
speckit.checklist.toml
speckit.clarify.toml
speckit.constitution.toml
speckit.implement.toml
speckit.plan.toml
speckit.specify.toml
speckit.tasks.toml
```

> 파일 형식이 `.toml` 인 것을 확인한다. Gemini CLI 통합은 TOML 형식을 사용한다.

`.specify` 내부도 확인한다.

```bash
ls .specify/
```

예상 출력:

```
memory/   specs/   templates/
```

### 3-4. GEMINI.md 확인

`specify init` 실행 시 Gemini 컨텍스트 파일도 자동 생성된다.

```bash
cat GEMINI.md
```

이 파일은 Gemini CLI 가 프로젝트를 이해하는 데 사용하는 전역 설정 파일이다.  
`constitution.md` 처럼 Gemini 가 항상 참조하는 배경 지식이라고 생각하면 된다.

### 3-5. Gemini CLI 실행

반드시 `login-project` 폴더 안에서 실행한다.

```bash
gemini
```

처음 실행 시 브라우저 팝업이 열리며 Google 계정 인증을 요청한다.  
로그인하면 아래와 같이 채팅창이 열린다.

```
Gemini CLI  v0.x.x
Type /help for help, /exit to quit

>
```

> **이 채팅창에서 `/speckit.*` 명령어를 입력한다.**  
> 터미널 명령어(`bash`, `ls` 등)와 Gemini 채팅 명령어는 서로 다른 창이다.  
> 헷갈리면: 프롬프트가 `>` 이면 Gemini 채팅창, `$` 이면 터미널이다.

> **확인 포인트 ✅**  
> `>` 프롬프트가 보이면 Part 3 완료.

---

## Part 4. SDD 실습 1단계 — 기본 로그인 화면

### 실습 목표

HTML + CSS + JavaScript 로 구성된 로그인 화면을 SDD 7단계를 거쳐 만든다.  
각 단계가 1차시·2차시에서 배운 SDD 어느 단계인지 확인하면서 진행한다.

---

### Step 1. Constitution — 프로젝트 원칙 설정

> **[SDD 기반 원칙]** — `constitution.md` 는 프로젝트 전체에 적용되는 규칙이다.  
> 2차시에서 배운 `constitution.md` 를 지금 직접 작성한다.  
>
> **테스트 원칙도 여기에 함께 작성하는 이유**  
> constitution.md 는 Gemini 가 모든 명령에서 **항상 참조**하는 배경 지식이다.  
> 테스트 기준을 여기에 명시하면, 나중에 `/speckit.checklist testing` 을 실행할 때  
> Gemini 가 "이 프로젝트의 테스트 방식은 이렇다"를 이미 알고 있는 상태에서 체크리스트를 만든다.  
> 즉, 테스트 원칙을 매번 반복해서 입력할 필요 없이 constitution.md 에 한 번만 적으면 된다.

**Gemini 채팅창** (`>` 프롬프트) 에 아래를 입력한다.

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

**터미널 새 탭** 에서 저장 여부를 확인한다.

```bash
cat .specify/memory/constitution.md
```

테스트 원칙을 포함한 원칙 내용이 저장되어 있으면 정상이다.

> **확인 포인트 ✅**  
> `constitution.md` 에 `[테스트 원칙]` 섹션이 포함되어 있는가?  
> 이후 `/speckit.checklist testing` 실행 시 이 원칙이 체크리스트에 자동 반영된다.

---

### Step 2. Specify 001 — 요구사항 정의

> **[SDD 1단계: Specify]** — "무엇을, 왜 만드는가"를 정의하는 단계다.  
> 1차시에서 배운 사용자 스토리·수용 기준·엣지 케이스가 이 단계에서 생성된다.

**Gemini 채팅창** 에 아래를 입력한다.

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

**터미널** 에서 생성 여부를 확인한다.

```bash
ls .specify/specs/
# 예상 출력: 001-login-page/

cat .specify/specs/001-login-page/spec.md
```

---

### Step 3. Clarify — 모호한 부분 명확하게

> **[SDD 1단계: Specify 보완]** — AI가 추가로 물어볼 수 있는 모호한 부분을 미리 명확히 한다.  
> 2차시에서 "구현 중에 발견하면 수정 비용이 10배"라고 배운 바로 그 단계다.

**Gemini 채팅창** 에 아래를 입력한다.

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

---

### Step 4. Checklist — 명세 완성도 점검 (specify 후)

> **[SDD 1단계: Specify 최종 검증]** — `clarify` 로 모호함을 제거한 직후, `plan` 으로 넘어가기 전에  
> spec.md 에 빠진 항목이 없는지 도메인별로 점검한다.  
> **이 단계에서 발견된 구멍은 수정 비용이 가장 작다.** `plan` 이후에 발견하면 계획도 같이 고쳐야 한다.

#### 4-1. 기본 요구사항 체크리스트

**Gemini 채팅창** 에 아래를 입력한다.

```
/speckit.checklist
체크리스트를 기준으로 spec.md 를 정밀 분석하여 결과를 보고해 줘.
누락된 항목이 있으면 어떻게 수정해야 하는지 알려줘.
```

**터미널** 에서 저장 여부를 확인한다.

```bash
cat .specify/specs/001-login-page/checklists/requirements.md
```

예상 출력 (일부):

```markdown
# 요구사항 완성도 체크리스트: 기본 로그인 화면

## 요구사항 완전성
- [x] CHK001 - 모든 사용자 스토리에 수용 기준(AC)이 존재하는가?
- [x] CHK002 - 비기능 요구사항에 측정 가능한 수치가 포함되어 있는가?
- [ ] CHK003 - 모든 오류 시나리오에 구체적인 메시지가 정의되어 있는가?
              → "비밀번호 보이기/숨기기" 실패 시 처리 미정의 → spec.md 보완 필요

## 명확성
- [x] CHK004 - "적절히", "빠르게" 같은 모호한 표현이 없는가?
- [x] CHK005 - 화면 레이아웃 설명이 구체적인가? (색상, 위치, 크기)

## 기능 준비도
- [x] CHK006 - 이 명세만 보고 plan.md 를 작성할 수 있는가?
- [x] CHK007 - constitution.md 원칙을 위반하는 항목이 없는가?
```

#### 4-2. UX 관점 체크리스트

**Gemini 채팅창** 에 아래를 입력한다.

```
/speckit.checklist ux
로그인 화면의 UX 관점에서 spec.md 를 점검해줘.
빠진 UX 요구사항이 있으면 무엇을 추가해야 하는지 알려줘.
```

**터미널** 에서 저장 여부를 확인한다.

```bash
cat .specify/specs/001-login-page/checklists/ux.md
```

예상 출력 (일부):

```markdown
# UX 체크리스트: 기본 로그인 화면

## 사용자 흐름
- [x] CHK001 - 로그인 성공 후 이동 또는 피드백이 명확히 정의되어 있는가?
- [x] CHK002 - 로그인 실패 후 사용자가 다시 시도할 수 있는 흐름이 명시되어 있는가?
- [ ] CHK003 - 로그인 버튼 클릭 중(처리 중) 상태의 UI 피드백이 명세에 있는가?
              → 로딩 스피너 또는 버튼 비활성화 명시 필요 → spec.md 보완 필요

## 오류 메시지
- [x] CHK004 - 빈 칸 제출 오류 메시지가 구체적으로 정의되어 있는가?
- [x] CHK005 - 틀린 정보 입력 오류 메시지가 구체적으로 정의되어 있는가?
- [x] CHK006 - 오류 메시지 색상(빨간색)이 명시되어 있는가?

## 접근성
- [x] CHK007 - 키보드(Enter 키)로만 로그인 완료가 가능한가?
- [ ] CHK008 - 비밀번호 보이기/숨기기 버튼에 대한 설명이 명세에 있는가?
              → 버튼 위치(오른쪽 끝, 눈 아이콘) 명시 권장

## 반응형
- [x] CHK009 - 모바일 화면 지원이 constitution.md 에 명시되어 있는가?
```

#### 4-3. 체크리스트 결과 처리

`[ ]` (미통과) 항목이 있으면 다음과 같이 처리한다.

```
[미통과 항목 발견 시 처리 순서]

1. Gemini 채팅창에서 수정 방법을 물어본다
   예) "CHK003 항목 — spec.md 에 어떤 내용을 추가해야 해?"

2. spec.md 를 직접 수정하거나 Gemini 에게 수정을 요청한다
   예) "/speckit.specify 에 로딩 중 버튼 비활성화 요구사항을 추가해줘"

3. 체크리스트를 재실행하여 통과 여부를 확인한다
   예) "/speckit.checklist ux"

4. 모든 항목 통과 → Step 5 (Plan) 로 진행
```

> **확인 포인트 ✅**  
> `checklists/requirements.md`, `checklists/ux.md` 생성 확인,  
> 모든 항목 통과 또는 미통과 항목 spec.md 보완 완료 → Step 5 로 진행.

---

### Step 5. Plan — 기술 설계

> **[SDD 2단계: Plan]** — "어떻게 만들 것인가"를 기술적으로 설계하는 단계다.  
> `constitution.md` 의 원칙(단일 HTML 파일, 외부 라이브러리 금지)을 AI가 자동으로 반영한다.

**Gemini 채팅창** 에 아래를 입력한다.

```
/speckit.plan
단일 HTML 파일(index.html) 하나로 모든 것을 구현한다.
CSS는 style 태그 안에 작성한다.
JavaScript는 script 태그 안에 작성한다.
외부 라이브러리나 CDN을 사용하지 않는다.
Flexbox를 사용하여 중앙 정렬한다.
```

**터미널** 에서 확인한다.

```bash
cat .specify/specs/001-login-page/plan.md
```

---

### Step 6. Tasks — 작업 목록 생성

> **[SDD 3단계: Task]** — `plan.md` 를 바탕으로 개발자(또는 AI)가 즉시 실행할 수 있는 작업 목록을 만든다.

**Gemini 채팅창** 에 아래를 입력한다.

```
/speckit.tasks
```

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

> **[SDD 2단계: Plan 검증]** — `spec.md`, `plan.md`, `tasks.md` 간 불일치를 자동으로 탐지한다.  
> "All checks passed" 가 나올 때까지 불일치를 수정한 후 다음 단계로 넘어간다.

**Gemini 채팅창** 에 아래를 입력한다.

```
/speckit.analyze
```

"문제 없음" 또는 "All checks passed" 메시지가 나오면 다음 단계로 진행한다.  
불일치가 발견되면 Gemini 가 안내하는 수정 사항을 `spec.md` 또는 `plan.md` 에 반영한 후 재실행한다.

---

### Step 8. Implement — 코드 생성

> **[SDD 4단계: Implement]** — 사양을 코드로 번역하는 단계다.  
> `spec.md` 의 수용 기준이 코드에 1:1 로 반영되는지 이후 테스트에서 확인한다.

**Gemini 채팅창** 에 아래를 입력한다.

```
/speckit.implement
```

생성 완료 후 **터미널** 에서 파일 확인한다.

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
> Gemini 는 체크리스트를 만들 때 **두 가지를 동시에 참조**한다.  
> - `spec.md` → "이 기능이 무엇을 해야 하는가" (수용 기준)  
> - `constitution.md` → "이 프로젝트는 어떻게 테스트해야 하는가" (테스트 원칙)

#### 9-1. constitution 기반 테스트 체크리스트 실행

**Gemini 채팅창** 에 아래를 입력한다.

```
/speckit.checklist testing
생성된 index.html 이 spec.md 의 모든 수용 기준을 충족하는지
테스트 시나리오 관점에서 점검해줘.
constitution.md 의 [테스트 원칙] 도 함께 반영해서 체크리스트를 만들어줘.
누락되거나 구현되지 않은 기능이 있으면 알려줘.
```

**터미널** 에서 저장 여부를 확인한다.

```bash
cat .specify/specs/001-login-page/checklists/testing.md
```

예상 출력 (일부):

```markdown
# 테스트 체크리스트: 기본 로그인 화면 (implement 후)
# constitution.md [테스트 원칙] 기반 생성

## 수용 기준 검증 (spec.md 기반)

### 정상 흐름 (Happy Path)
- [ ] CHK001 - admin/1234 입력 → "로그인 성공!" 초록색 메시지가 표시되는가?
              [spec.md §수용기준 AC-001]
- [ ] CHK002 - 로그인 성공 후 입력 필드가 초기화되는가?
              [spec.md §수용기준 AC-002]

### 오류 흐름 (Error Path)
- [ ] CHK003 - 빈 칸 제출 → "아이디와 비밀번호를 입력하세요" 빨간색 메시지가 표시되는가?
              [spec.md §수용기준 AC-003]
- [ ] CHK004 - 틀린 정보 입력 → "아이디 또는 비밀번호가 올바르지 않습니다" 메시지가 표시되는가?
              [spec.md §수용기준 AC-004]

### 기능 동작
- [ ] CHK005 - 비밀번호 보이기(👁) 버튼 클릭 시 비밀번호 텍스트가 보이는가?
              [spec.md §기능요구사항]
- [ ] CHK006 - 다시 클릭 시 비밀번호가 가려지는가?
              [spec.md §기능요구사항]
- [ ] CHK007 - Enter 키 입력 시 로그인이 시도되는가?
              [spec.md §기능요구사항]

## constitution.md 개발 원칙 준수 검증

### 단일 파일 원칙
- [ ] CHK008 - CSS 가 `<style>` 태그 안에 작성되어 있는가? (외부 .css 파일 없음)
              [constitution.md §개발원칙 - 단일 파일]
- [ ] CHK009 - JavaScript 가 `<script>` 태그 안에 작성되어 있는가? (외부 .js 파일 없음)
              [constitution.md §개발원칙 - 단일 파일]
- [ ] CHK010 - CDN 링크나 외부 라이브러리 `<script>` 가 없는가?
              [constitution.md §개발원칙 - 외부 프레임워크 금지]

### 코드 품질 원칙
- [ ] CHK011 - 주요 함수와 로직에 한국어 주석이 달려 있는가?
              [constitution.md §개발원칙 - 한국어 주석]
- [ ] CHK012 - 불필요하게 복잡한 구조(중첩 클래스, 과도한 추상화)가 없는가?
              [constitution.md §개발원칙 - 단순하게 작성]

## 반응형 검증

- [ ] CHK013 - 브라우저 폭을 375px 로 줄였을 때 로그인 카드가 잘리지 않는가?
              [constitution.md §테스트원칙 - 모바일 375px 확인]
- [ ] CHK014 - 모바일에서 입력 필드와 버튼이 정상적으로 탭 이동되는가?
              [constitution.md §테스트원칙 - 모바일 동작 확인]
```

#### 9-2. 브라우저에서 직접 시나리오 실행

> constitution.md 테스트 원칙: "브라우저에서 직접 시나리오를 실행하는 방식으로 진행한다"

체크리스트를 보면서 브라우저에서 **순서대로** 각 항목을 직접 실행하여 체크한다.

**수용 기준 검증 순서**:

```
1. admin/1234 입력 → 로그인 버튼 클릭         → CHK001 확인
2. 빈 칸 상태로 제출                          → CHK003 확인
3. 틀린 정보(admin/0000) 입력 후 제출         → CHK004 확인
4. 비밀번호 필드에 값 입력 후 👁 클릭         → CHK005 확인
5. 다시 👁 클릭                              → CHK006 확인
6. admin/1234 입력 후 Enter 키               → CHK007 확인
```

**constitution 원칙 검증 순서**:

```
7. VS Code 에서 index.html 열어 <style> 태그 확인    → CHK008·009 확인
8. <script src=...> 또는 CDN 링크 없는지 확인        → CHK010 확인
9. 한국어 주석 존재 여부 확인                        → CHK011 확인
10. 브라우저 폭 375px 로 줄인 후 화면 확인            → CHK013·014 확인
    (Chrome DevTools → 모바일 아이콘 클릭)
```

#### 9-3. 미통과 항목 처리

```
[ ] 항목 발견 시:

1. 수용 기준 미통과(CHK001~007):
   Gemini 채팅창에 수정 요청
   예) "index.html 에서 CHK002 - 로그인 성공 후 입력 필드가 초기화되지 않아.
        spec.md 수용 기준에 맞게 수정해줘."

2. constitution 원칙 미통과(CHK008~014):
   Gemini 채팅창에 수정 요청
   예) "index.html 에 외부 라이브러리가 포함되어 있어.
        constitution.md 의 외부 프레임워크 금지 원칙에 맞게 제거해줘."

3. Ctrl+Shift+R (강제 새로고침) 후 해당 항목 재확인

4. 모든 항목 통과 → Part 5 로 진행
```

> **확인 포인트 ✅**  
> `checklists/testing.md` 생성 확인 (`cat .specify/specs/001-login-page/checklists/testing.md`)  
> 수용 기준 항목(CHK001~007) + constitution 원칙 항목(CHK008~014) 모두 체크 완료 → Part 4 완료.

---

## Part 5. 결과물 실행 및 테스트

### 5-1. 브라우저에서 열기

**방법 1: Live Server 확장 프로그램 (권장)**

1. VS Code 왼쪽 사이드바에서 확장 프로그램 아이콘(네모 4개)을 클릭한다.
2. 검색창에 `Live Server` 를 입력한다.
3. `Live Server` (작성자: Ritwick Dey) 를 설치한다.
4. 파일 탐색기에서 `index.html` 을 우클릭한다.
5. `Open with Live Server` 를 선택한다.

**방법 2: Python 내장 서버**

```bash
python3 -m http.server 8080
```

VS Code 하단 `포트(PORTS)` 탭에서 8080 포트의 지구본 아이콘을 클릭한다.

> **방법 2 사용 시 주의**: 서버를 실행한 터미널은 종료하지 않는다.  
> 이후 파트에서 기능이 추가되면 브라우저에서 `Ctrl+Shift+R` (강제 새로고침) 으로 변경 사항을 확인한다.

### 5-2. 기본 동작 테스트

아래 시나리오를 직접 실행하여 결과를 확인한다.

| 시나리오 | 아이디 | 비밀번호 | 예상 결과 |
|---|---|---|---|
| 빈 칸 제출 | (비워둠) | (비워둠) | "아이디와 비밀번호를 입력하세요" |
| 로그인 성공 | admin | 1234 | "로그인 성공!" (초록색) |
| 로그인 실패 | admin | 0000 | "아이디 또는 비밀번호가 올바르지 않습니다" (빨간색) |
| 비밀번호 보이기 | 아무 값 | 아무 값 | 비밀번호 텍스트가 보임 |
| Enter 키 로그인 | admin | 1234 | Enter 키로도 로그인 성공 |

### 5-3. 예상 결과물 화면 구성

AI 가 생성한 `index.html` 은 아래와 유사한 구조여야 한다.

```
+------------------------------------------+
|                                          |
|   +----------------------------------+   |
|   |          로그인                  |   |
|   |                                  |   |
|   |  아이디                          |   |
|   |  [                          ]    |   |
|   |                                  |   |
|   |  비밀번호                        |   |
|   |  [                        👁]    |   |
|   |                                  |   |
|   |  [        로그인 버튼        ]   |   |
|   |                                  |   |
|   |  ← 메시지 표시 영역              |   |
|   +----------------------------------+   |
|                                          |
+------------------------------------------+
  배경: 밝은 회색     카드: 흰색 + 그림자
```

---

## Part 6. SDD — 기존 명세(001) 변경 대처

### 이 파트에서 배우는 것

실제 개발에서 요구사항은 항상 변한다.  
이 파트는 **이미 완성된 기능(001)의 요구사항이 바뀔 때** SDD 에서 어떻게 대처하는지를 체험한다.

### 변경 시나리오

> **기획자 요청**:  
> "로그인 성공 후 '로그인 성공!' 메시지를 2초 동안 보여준 뒤,  
> '환영합니다, admin 님!' 으로 교체하고 비밀번호 입력 필드를 초기화해 주세요."

이 변경은 작아 보이지만 다음을 모두 수정해야 한다.

- `spec.md` — 새 수용 기준 추가
- `plan.md` — `setTimeout` 로직 추가 계획
- `tasks.md` — 새 Task 항목 추가
- `index.html` — 실제 코드 변경

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

> **핵심 원칙 복습**  
> SDD 에서 **spec.md 가 진실의 근원**이다.  
> 변경은 반드시 spec.md 수정부터 시작하고, 코드는 마지막에 수정한다.

---

### Step 1. spec.md 직접 수정

> **[SDD 1단계: Specify 수정]** — 변경 요구사항을 명세에 반영한다.

**터미널** 에서 spec.md 를 연다.

```bash
nano .specify/specs/001-login-page/spec.md
```

또는 VS Code 파일 탐색기에서 `.specify/specs/001-login-page/spec.md` 를 직접 클릭하여 편집한다.

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
- 2026-05-25: 로그인 성공 후 환영 메시지 및 필드 초기화 요구사항 추가 (기획팀 요청)
```

> **변경 이력 기록의 중요성**  
> spec.md 에 변경 이력을 남기면 나중에 "왜 이렇게 바뀌었는가"를 추적할 수 있다.  
> Git 커밋 메시지에도 동일한 이유를 적는 것이 좋다.

---

### Step 2. Clarify — 변경된 명세 보완

> **[SDD 1단계: Specify 보완]** — 변경된 요구사항에 새로운 모호함이 없는지 확인한다.

**Gemini 채팅창** 에 아래를 입력한다.

```
/speckit.clarify
spec.md 가 수정되었어.
로그인 성공 후 동작 변경에 관해 다음을 명확히 해줘:
- 2초 타이머는 setTimeout 을 사용한다
- 메시지 교체 시 애니메이션(페이드) 효과는 사용하지 않는다
- 비밀번호 필드만 초기화하고 아이디 필드는 그대로 둔다
- 이미 입력된 비밀번호가 없는 경우에도 초기화 로직은 실행된다
```

---

### Step 3. Checklist — 변경된 명세 점검

> **[SDD 1단계: Specify 최종 검증]** — 변경된 spec.md 에 새로운 구멍이 없는지 다시 점검한다.

**Gemini 채팅창** 에 아래를 입력한다.

```
/speckit.checklist
spec.md 가 수정되었어.
변경된 "로그인 성공 후 동작" 부분을 중심으로 누락된 요구사항이 없는지 점검해줘.
```

예상 출력 (일부):

```markdown
# 변경 명세 점검 결과

## 변경된 수용 기준 검토
- [x] CHK001 - 2초 타이머 동작이 수용 기준에 명확히 정의되어 있는가?
- [x] CHK002 - 메시지 교체 조건이 명확히 정의되어 있는가?
- [x] CHK003 - 초기화 대상 필드(비밀번호)가 명시되어 있는가?
- [ ] CHK004 - 2초 대기 중 사용자가 다시 로그인 버튼을 클릭하면 어떻게 되는가?
              → 타이머 중복 실행 방지 정책 미정의 → spec.md 에 추가 필요

## 기존 수용 기준과의 충돌 검토
- [x] CHK005 - 변경된 수용 기준이 기존 오류 시나리오 수용 기준과 충돌하지 않는가?
- [x] CHK006 - constitution.md 원칙(단순하게 작성)을 위반하지 않는가?
```

`[ ]` 항목이 있으면 spec.md 에 추가한 후 재실행한다.

---

### Step 4. Analyze — 변경 영향 범위 탐지

> **[SDD 핵심]** — spec.md 가 변경되면 `plan.md`, `tasks.md` 와의 불일치가 발생한다.  
> `/speckit.analyze` 가 어떤 부분을 수정해야 하는지 자동으로 탐지해 준다.

**Gemini 채팅창** 에 아래를 입력한다.

```
/speckit.analyze
spec.md 가 수정되었어.
변경된 내용이 plan.md 및 tasks.md 와 일치하는지 분석해줘.
```

예상 출력:

```
⚠ [불일치 1] 구현 계획 누락
   spec.md: "2초 후 메시지 교체" 수용 기준 추가됨
   plan.md: setTimeout 관련 구현 계획 없음
   → plan.md 에 타이머 로직 추가 필요

⚠ [불일치 2] 작업 목록 누락
   spec.md: "비밀번호 필드 초기화" 수용 기준 추가됨
   tasks.md: 해당 Task 항목 없음
   → tasks.md 에 Task 추가 필요

→ plan.md 와 tasks.md 를 수정한 후 /speckit.analyze 를 재실행하세요.
```

---

### Step 5. Plan 재실행 — 계획 업데이트

> **[SDD 2단계: Plan 업데이트]** — 변경된 spec.md 를 기반으로 plan.md 를 다시 생성한다.

**Gemini 채팅창** 에 아래를 입력한다.

```
/speckit.plan
spec.md 가 수정되었어.
기존 index.html 에 다음 변경 사항을 추가하는 계획을 세워줘:
- 로그인 성공 시 setTimeout(2000) 으로 2초 대기
- 2초 후 메시지를 "환영합니다, admin 님!" 으로 교체
- 비밀번호 입력 필드 값을 빈 문자열로 초기화
- 타이머 중복 실행 방지 (이미 타이머가 동작 중이면 새 타이머 무시)
```

---

### Step 6. Tasks 재실행 — 작업 목록 업데이트

> **[SDD 3단계: Task 업데이트]** — 변경된 plan.md 를 기반으로 tasks.md 를 업데이트한다.

**Gemini 채팅창** 에 아래를 입력한다.

```
/speckit.tasks
spec.md 와 plan.md 가 수정되었어.
변경된 내용을 반영하여 tasks.md 를 업데이트해줘.
기존 Task 는 유지하고 변경에 필요한 Task 만 추가해줘.
```

---

### Step 7. Implement — 변경 코드 반영

> **[SDD 4단계: Implement]** — 수정된 명세를 코드에 반영한다.

**Gemini 채팅창** 에 아래를 입력한다.

```
/speckit.implement
spec.md 가 수정되었어.
기존 index.html 에 로그인 성공 후 2초 대기 + 메시지 교체 + 비밀번호 초기화 기능을 추가해줘.
기존 코드 구조는 최대한 유지하고, 변경된 부분에는 한국어 주석으로 변경 이유를 달아줘.
```

---

### 6-2. 변경 결과 확인

Live Server 를 사용 중이면 파일 저장 시 자동 새로고침된다.  
Python 서버를 사용 중이면 브라우저에서 `Ctrl+Shift+R` (강제 새로고침) 을 누른다.

| 시나리오 | 예상 결과 |
|---|---|
| admin/1234 로 로그인 | "로그인 성공!" 초록색 메시지 표시 |
| 2초 대기 | 메시지가 "환영합니다, admin 님!" 으로 교체 |
| 비밀번호 필드 확인 | 자동으로 비워짐 (아이디 필드는 'admin' 유지) |
| 버튼 재클릭 (2초 내) | 타이머 중복 실행 없음 |
| 틀린 정보 로그인 | 오류 메시지는 기존과 동일 (변경 없음) |

#### constitution 테스트 원칙 기반 재검증

명세가 변경되었으므로, constitution.md 의 테스트 원칙에 따라 **변경된 기능만 다시** 검증한다.

**Gemini 채팅창** 에 아래를 입력한다.

```
/speckit.checklist testing
spec.md 가 수정되었어.
변경된 "로그인 성공 후 동작" 부분을 중심으로
constitution.md 의 [테스트 원칙] 을 반영하여 테스트 항목을 추가해줘.
기존 testing.md 에 새 항목만 추가해줘 (기존 항목은 삭제하지 마).
```

예상 추가 항목 (일부):

```markdown
# 테스트 체크리스트 — 명세 변경(001 v2) 추가 항목
# constitution.md [테스트 원칙] 기반, 2026-05-25 추가

## 변경된 수용 기준 검증 (Happy Path)
- [ ] CHK015 - admin/1234 로그인 성공 후 "로그인 성공!" 이 2초간 표시되는가?
               [spec.md §수용기준 변경분 — 2초 타이머]
- [ ] CHK016 - 2초 후 메시지가 "환영합니다, admin 님!" 으로 교체되는가?
               [spec.md §수용기준 변경분 — 메시지 교체]
- [ ] CHK017 - 비밀번호 입력 필드가 자동 초기화되는가? (아이디 필드 값은 유지)
               [spec.md §수용기준 변경분 — 필드 초기화]

## 변경된 수용 기준 검증 (Error Path)
- [ ] CHK018 - 2초 대기 중 로그인 버튼을 다시 클릭해도 타이머 중복 실행이 없는가?
               [spec.md §수용기준 변경분 — 타이머 중복 방지]
- [ ] CHK019 - 기존 오류 시나리오(CHK003·004)가 변경 후에도 동일하게 동작하는가?
               [회귀 테스트 — 기존 기능 유지 확인]

## constitution 원칙 재확인
- [ ] CHK020 - 변경된 코드에도 한국어 주석으로 변경 이유가 달려 있는가?
               [constitution.md §개발원칙 - 한국어 주석]
```

> **확인 포인트 ✅**  
> 변경된 동작 테스트 통과, `testing.md` 에 변경분 항목 추가 확인, Git 커밋 완료 → Part 6 완료.

### 6-3. Git 커밋으로 변경 이력 남기기

변경이 완료되면 Git 커밋으로 명세·코드 변경을 함께 기록한다.

```bash
git add .specify/specs/001-login-page/spec.md
git add .specify/specs/001-login-page/plan.md
git add .specify/specs/001-login-page/tasks.md
git add index.html
git commit -m "feat(001): 로그인 성공 후 환영 메시지 + 필드 초기화 추가

- spec.md: 2초 후 메시지 교체 수용 기준 추가
- plan.md: setTimeout 로직 구현 계획 추가
- tasks.md: 관련 Task 항목 추가
- index.html: setTimeout, 메시지 교체, 비밀번호 초기화 구현
변경 요청: 기획팀 (2026-05-25)"
```

> **커밋 메시지에서 보이는 것**  
> 명세(spec.md)와 코드(index.html)가 **항상 함께** 커밋된다.  
> 이것이 SDD 에서 명세와 코드가 동기화되는 방식이다.

### 6-4. 이 파트에서 배운 것 정리

```
[핵심 정리]

1. 요구사항 변경 → 반드시 spec.md 부터 수정
   코드 먼저 수정 = 명세와 코드 불일치 = 기술 부채

2. /speckit.analyze 가 변경 영향 범위를 자동으로 탐지
   어떤 파일(plan.md, tasks.md)을 수정해야 하는지 알려줌

3. 변경 이력은 spec.md 와 Git 커밋 메시지에 모두 기록
   6개월 후 "왜 이렇게 바뀌었는가"를 추적 가능

4. constitution.md 의 [테스트 원칙] 이 명세 변경 후에도 적용된다
   변경된 부분만 targeted 하게 /speckit.checklist testing 으로 재검증
   기존 testing.md 에 새 항목 누적 → 회귀 테스트 이력 자동 보존

5. SDD 변경 흐름:
   spec.md 수정 → clarify → checklist → analyze → plan → tasks → implement
   → checklist testing (변경분 재검증) → git commit
```

> **확인 포인트 ✅**  
> 변경된 동작 테스트 통과, Git 커밋 완료 → Part 6 완료.

---

## Part 7. SDD 실습 2단계 — 로그인 횟수 제한 추가

### 기능 추가 흐름 설명

`constitution.md` 는 프로젝트 전체에 적용되는 원칙이므로 다시 작성하지 않는다.  
`specify` 부터 `implement` 까지 새 번호(002)로 진행한다.

```
이미 완료: constitution → specify(001) → ... → implement  [명세 수정 완료]
지금부터:               → specify(002) → clarify → checklist → plan → tasks → analyze → implement
```

---

### Step 1. Specify 002 — 로그인 횟수 제한 요구사항 정의

> **[SDD 1단계: Specify]**

**Gemini 채팅창** 에 아래를 입력한다.

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

**터미널** 에서 확인한다.

```bash
ls .specify/specs/
# 예상 출력: 001-login-page/   002-login-attempts/

cat .specify/specs/002-login-attempts/spec.md
```

---

### Step 2. Clarify

> **[SDD 1단계: Specify 보완]**

**Gemini 채팅창** 에 아래를 입력한다.

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

**Gemini 채팅창** 에 아래를 입력한다.

```
/speckit.checklist ux
로그인 횟수 제한 기능의 UX 요구사항이 spec.md 에 완전히 정의되어 있는지 점검해줘.
카운트다운, 버튼 상태 변화, 메시지 표시 등을 중심으로 확인해줘.
```

**터미널** 에서 확인한다.

```bash
cat .specify/specs/002-login-attempts/checklists/ux.md
```

예상 출력 (일부):

```markdown
# UX 체크리스트: 로그인 횟수 제한

## 사용자 피드백
- [x] CHK001 - 실패 횟수가 화면에 표시되는가? ("로그인 시도: N/3" 형식)
- [x] CHK002 - 버튼 비활성화 시 색상 변화(파란색→회색)가 명세에 있는가?
- [x] CHK003 - 카운트다운 숫자가 화면에 표시됨이 명세에 있는가? (5, 4, 3, 2, 1)
- [ ] CHK004 - 카운트다운 완료 후 버튼 재활성화 시 사용자에게 안내 메시지가 있는가?
              → "다시 시도할 수 있습니다" 또는 버튼 색상 복원 명시 권장

## 오류 흐름
- [x] CHK005 - 3회 실패 시 표시되는 안내 메시지가 구체적으로 정의되어 있는가?
- [x] CHK006 - 카운트다운 메시지 색상(빨간색)이 명시되어 있는가?
- [ ] CHK007 - 버튼 비활성화 중 아이디/비밀번호 입력 필드는 어떻게 되는가?
              → 필드 활성 여부 명시 필요

## 성공 흐름
- [x] CHK008 - 로그인 성공 시 실패 횟수 초기화가 명세에 있는가?
```

#### 3-2. 보안 관점 체크리스트

**Gemini 채팅창** 에 아래를 입력한다.

```
/speckit.checklist security
로그인 횟수 제한 기능의 보안 요구사항이 spec.md 에 충분히 정의되어 있는지 점검해줘.
횟수 초기화 조건, 우회 가능성 등을 중심으로 확인해줘.
```

**터미널** 에서 확인한다.

```bash
cat .specify/specs/002-login-attempts/checklists/security.md
```

예상 출력 (일부):

```markdown
# 보안 체크리스트: 로그인 횟수 제한

## 잠금 메커니즘
- [x] CHK001 - 실패 허용 횟수(3회)가 명세에 명시되어 있는가?
- [x] CHK002 - 잠금 해제 조건(5초 경과 후 자동)이 명세에 있는가?
- [ ] CHK003 - 페이지 새로고침으로 잠금이 우회 가능한지 여부가 명세에 있는가?
              → "새로고침 시 초기화됨(교육용)" 명시 권장 (의도된 동작임을 명확히)

## 횟수 관리
- [x] CHK004 - 실패 횟수 초기화 조건(성공 시, 5초 후)이 모두 명세에 있는가?
- [ ] CHK005 - 실패 횟수를 클라이언트(JS 변수)에서만 관리하는 제약이 명시되어 있는가?
              → constitution.md 의 "단일 HTML 파일" 원칙과 일관성 확인 필요
```

#### 3-3. 미통과 항목 처리

```
[ ] 항목 발견 시:

1. Gemini 채팅창에서 수정 방법 확인
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

> **[SDD 2단계: Plan]**

**Gemini 채팅창** 에 아래를 입력한다.

```
/speckit.plan
기존 index.html 파일을 수정하여 기능을 추가한다.
JavaScript 의 setInterval 또는 setTimeout 을 사용하여 카운트다운을 구현한다.
실패 횟수는 JavaScript 변수로 관리한다.
별도 파일을 만들지 않고 index.html 안에서 모두 처리한다.
```

---

### Step 5. Tasks

> **[SDD 3단계: Task]**

**Gemini 채팅창** 에 아래를 입력한다.

```
/speckit.tasks
```

---

### Step 6. Analyze

> **[SDD 2단계: Plan 검증]**

**Gemini 채팅창** 에 아래를 입력한다.

```
/speckit.analyze
```

---

### Step 7. Implement

> **[SDD 4단계: Implement]**

**Gemini 채팅창** 에 아래를 입력한다.

```
/speckit.implement
```

---

### 7-2. 동작 테스트

서버가 이미 실행 중이면 브라우저에서 `Ctrl+Shift+R` (강제 새로고침) 을 눌러 변경 사항을 반영한다.  
서버가 꺼져 있으면 터미널에서 `python3 -m http.server 8080` 을 다시 실행한다.

| 시나리오 | 예상 결과 |
|---|---|
| 틀린 비밀번호 1회 입력 | "로그인 시도: 1/3" 표시 |
| 틀린 비밀번호 3회 입력 | 버튼 비활성화(회색), 카운트다운 시작 |
| 카운트다운 진행 | "5초 후 다시 시도하세요 (5, 4, 3, 2, 1)" 빨간색 표시 |
| 5초 대기 | 버튼 재활성화(파란색), 횟수 초기화 |
| 올바른 정보로 로그인 | "로그인 성공!" → 2초 후 "환영합니다, admin 님!" (Part 6 변경 유지) |

#### constitution 테스트 원칙 기반 검증

**Gemini 채팅창** 에 아래를 입력한다.

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

예상 출력 (일부):

```markdown
# 테스트 체크리스트: 로그인 횟수 제한 (implement 후)
# constitution.md [테스트 원칙] 기반 생성

## 수용 기준 검증

### 정상 흐름
- [ ] CHK001 - 로그인 실패 1회 시 "로그인 시도: 1/3" 이 표시되는가?
- [ ] CHK002 - 로그인 실패 3회 시 버튼이 회색으로 비활성화되는가?
- [ ] CHK003 - 카운트다운이 5→4→3→2→1 순서로 빨간색 표시되는가?
- [ ] CHK004 - 5초 후 버튼이 파란색으로 재활성화되고 횟수가 초기화되는가?
- [ ] CHK005 - 로그인 성공 시 실패 횟수가 즉시 초기화되는가?

### 오류 흐름
- [ ] CHK006 - 버튼 비활성화 중 Enter 키를 눌러도 로그인이 시도되지 않는가?
- [ ] CHK007 - 페이지 새로고침 시 실패 횟수가 0으로 초기화되는가?

## constitution 원칙 검증
- [ ] CHK008 - 횟수 제한 관련 코드에 한국어 주석이 달려 있는가?
              [constitution.md §테스트원칙]
- [ ] CHK009 - 브라우저 폭 375px 에서 카운트다운 메시지가 잘리지 않는가?
              [constitution.md §테스트원칙 - 모바일 확인]
```

> **확인 포인트 ✅**  
> `checklists/testing.md` 생성 확인, 모든 항목 체크 완료 → Part 7 완료.

---

## Part 8. SDD 실습 3단계 — 아이디 저장 기능 추가

### Step 1. Specify 003 — 아이디 저장 요구사항 정의

> **[SDD 1단계: Specify]**

**Gemini 채팅창** 에 아래를 입력한다.

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

**터미널** 에서 확인한다.

```bash
ls .specify/specs/
# 예상 출력: 001-login-page/   002-login-attempts/   003-save-id/
```

---

### Step 2. Clarify

> **[SDD 1단계: Specify 보완]**

**Gemini 채팅창** 에 아래를 입력한다.

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

**Gemini 채팅창** 에 아래를 입력한다.

```
/speckit.checklist security
아이디 저장 기능의 보안 요구사항이 spec.md 에 충분히 정의되어 있는지 점검해줘.
localStorage 사용, 비밀번호 미저장 원칙 등을 중심으로 확인해줘.
```

**터미널** 에서 확인한다.

```bash
cat .specify/specs/003-save-id/checklists/security.md
```

예상 출력 (일부):

```markdown
# 보안 체크리스트: 아이디 저장 기능

## 저장 데이터 범위
- [x] CHK001 - 저장 대상이 아이디(username)만임이 명시되어 있는가?
- [x] CHK002 - 비밀번호는 저장하지 않는다는 제약이 명세에 있는가?
- [ ] CHK003 - localStorage 에 저장된 값이 평문 저장됨에 대한 교육적 주의사항이 있는가?
              → "교육용 예제; 실제 서비스에서는 암호화 필요" 명시 권장

## 저장/삭제 조건
- [x] CHK004 - 저장 조건(체크박스 체크 + 로그인 성공)이 명확히 정의되어 있는가?
- [x] CHK005 - 삭제 조건(체크박스 해제 + 로그인)이 명확히 정의되어 있는가?
- [ ] CHK006 - 로그인 실패 시 저장된 아이디를 유지하는지 삭제하는지 명세에 있는가?
              → 현재 미정의 → "실패 시 저장된 아이디 유지" 명시 필요

## localStorage 키 관리
- [x] CHK007 - localStorage 저장 키 이름('saved_username')이 명세에 정의되어 있는가?
```

#### 3-2. UX 관점 체크리스트

**Gemini 채팅창** 에 아래를 입력한다.

```
/speckit.checklist ux
아이디 저장 기능의 UX 요구사항이 spec.md 에 충분히 정의되어 있는지 점검해줘.
체크박스 위치, 자동 입력 동작, 포커스 이동 등을 중심으로 확인해줘.
```

**터미널** 에서 확인한다.

```bash
cat .specify/specs/003-save-id/checklists/ux.md
```

예상 출력 (일부):

```markdown
# UX 체크리스트: 아이디 저장 기능

## 체크박스 UI
- [x] CHK001 - 체크박스 위치(아이디-비밀번호 필드 사이)가 명세에 있는가?
- [ ] CHK002 - 체크박스 레이블 텍스트("아이디 저장")가 명세에 있는가?
              → 명시되어 있으나 레이블 스타일(색상, 크기) 미정의

## 자동 입력 동작
- [x] CHK003 - 페이지 로드 시 저장된 아이디가 입력 필드에 자동 입력됨이 명세에 있는가?
- [x] CHK004 - 저장된 아이디가 있을 때 체크박스가 자동 체크됨이 명세에 있는가?
- [x] CHK005 - 저장된 아이디가 있을 때 비밀번호 필드에 포커스 이동이 명세에 있는가?

## 엣지 케이스
- [ ] CHK006 - 저장된 아이디가 없는 경우(첫 방문)의 초기 화면 상태가 명세에 있는가?
              → 체크박스 미체크 상태, 아이디 필드 비어있음 → 명시 권장
- [ ] CHK007 - 아이디를 수동으로 지우고 로그인하면 저장된 아이디가 어떻게 되는가?
              → 명세에 없음 → "체크박스 체크 여부에 따라 처리" 명시 필요
```

#### 3-3. 미통과 항목 처리

```
[ ] 항목 발견 시:

1. 중요도 판단
   - 보안 관련(CHK003, CHK006) → 반드시 spec.md 에 추가
   - UX 세부사항(CHK002) → 팀 논의 후 추가 여부 결정

2. spec.md 수정 후 체크리스트 재실행
   예) "/speckit.checklist security"

3. 모든 항목 통과 → Step 4 (Plan) 로 진행
```

> **확인 포인트 ✅**  
> `checklists/security.md`, `checklists/ux.md` 생성 확인,  
> 미통과 항목 spec.md 보완 완료 → Step 4 로 진행.

---

### Step 4. Plan

> **[SDD 2단계: Plan]**

**Gemini 채팅창** 에 아래를 입력한다.

```
/speckit.plan
localStorage 를 사용하여 아이디를 브라우저에 저장한다.
저장 키 이름은 'saved_username' 으로 한다.
페이지 로드 시 localStorage 를 확인하여 저장된 아이디를 불러온다.
별도 파일 없이 index.html 안에서 처리한다.
```

---

### Step 5. Tasks

> **[SDD 3단계: Task]**

**Gemini 채팅창** 에 아래를 입력한다.

```
/speckit.tasks
```

---

### Step 6. Analyze

> **[SDD 2단계: Plan 검증]**

**Gemini 채팅창** 에 아래를 입력한다.

```
/speckit.analyze
```

---

### Step 7. Implement

> **[SDD 4단계: Implement]**

**Gemini 채팅창** 에 아래를 입력한다.

```
/speckit.implement
```

---

### 8-2. 동작 테스트

서버가 이미 실행 중이면 브라우저에서 `Ctrl+Shift+R` (강제 새로고침) 으로 변경 사항을 반영한다.

| 시나리오 | 예상 결과 |
|---|---|
| 체크박스 체크 후 로그인 성공 | 아이디가 localStorage 에 저장됨 |
| 페이지 새로고침 | 아이디 자동 입력, 체크박스 자동 체크 |
| 저장된 아이디 상태에서 비밀번호 필드 | 자동으로 포커스 이동 |
| 체크박스 해제 후 로그인 | localStorage 에서 아이디 삭제됨 |
| 페이지 새로고침 | 아이디 입력 필드가 비어 있음 |

**localStorage 저장 확인 방법** (브라우저 개발자 도구):

```
1. 브라우저에서 F12 (개발자 도구) 를 연다.
2. Application 탭 → Storage → Local Storage → 현재 주소를 클릭한다.
3. 'saved_username' 키에 'admin' 값이 저장되어 있는지 확인한다.
```

#### constitution 테스트 원칙 기반 검증

**Gemini 채팅창** 에 아래를 입력한다.

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

예상 출력 (일부):

```markdown
# 테스트 체크리스트: 아이디 저장 기능 (implement 후)
# constitution.md [테스트 원칙] 기반 생성

## 수용 기준 검증

### 저장 흐름 (Happy Path)
- [ ] CHK001 - 체크박스 체크 후 로그인 성공 시 localStorage 에 'saved_username' 키가 생성되는가?
               (F12 → Application → Local Storage 에서 확인)
- [ ] CHK002 - 페이지 새로고침 후 아이디 입력 필드에 저장된 값이 자동으로 채워지는가?
- [ ] CHK003 - 페이지 새로고침 후 체크박스가 자동으로 체크된 상태인가?
- [ ] CHK004 - 저장된 아이디가 있을 때 비밀번호 필드로 포커스가 자동 이동하는가?

### 삭제 흐름 (Error Path)
- [ ] CHK005 - 체크박스 해제 후 로그인 시 localStorage 에서 'saved_username' 이 삭제되는가?
- [ ] CHK006 - 삭제 후 페이지 새로고침 시 아이디 입력 필드가 비어있는가?

### 엣지 케이스
- [ ] CHK007 - 처음 방문(저장값 없음) 시 체크박스가 미체크 상태이고 아이디 필드가 비어있는가?

## constitution 원칙 검증
- [ ] CHK008 - 비밀번호가 localStorage 에 저장되지 않는가?
               (constitution.md §테스트원칙, spec.md §보안원칙)
- [ ] CHK009 - localStorage 관련 코드에 한국어 주석이 달려 있는가?
               (constitution.md §개발원칙 - 한국어 주석)
- [ ] CHK010 - 브라우저 폭 375px 에서 체크박스가 레이블과 함께 정상 표시되는가?
               (constitution.md §테스트원칙 - 모바일 확인)
```

> **확인 포인트 ✅**  
> `checklists/testing.md` 생성 확인, 모든 항목 체크 완료 → Part 8 완료.

---

## Part 9. 전체 산출물 구조 확인

실습이 모두 끝나면 **터미널** 에서 전체 구조를 확인한다.

```bash
find . -not -path '*/\.*' | sort
```

또는 tree 명령어로 확인한다 (설치되어 있는 경우):

```bash
tree -a -I 'node_modules'
```

예상 디렉터리 구조:

```
login-project/
├── .gemini/
│   └── commands/
│       ├── speckit.analyze.toml
│       ├── speckit.checklist.toml
│       ├── speckit.clarify.toml
│       ├── speckit.constitution.toml
│       ├── speckit.implement.toml
│       ├── speckit.plan.toml
│       ├── speckit.specify.toml
│       └── speckit.tasks.toml
├── .specify/
│   ├── memory/
│   │   └── constitution.md              ← [개발 원칙] + [테스트 원칙] (1회 작성)
│   └── specs/
│       ├── 001-login-page/
│       │   ├── spec.md                  ← 기본 로그인 요구사항 (Part 6 에서 수정됨)
│       │   ├── plan.md                  ← Part 6 에서 재생성됨
│       │   ├── tasks.md                 ← Part 6 에서 재생성됨
│       │   └── checklists/
│       │       ├── requirements.md      ← Step 4-1 (specify 후)
│       │       ├── ux.md                ← Step 4-2 (specify 후)
│       │       └── testing.md           ← Step 9 + Part 6 변경분 누적
│       ├── 002-login-attempts/
│       │   ├── spec.md
│       │   ├── plan.md
│       │   ├── tasks.md
│       │   └── checklists/
│       │       ├── ux.md
│       │       ├── security.md
│       │       └── testing.md           ← 7-2 constitution 기반 검증 결과
│       └── 003-save-id/
│           ├── spec.md
│           ├── plan.md
│           ├── tasks.md
│           └── checklists/
│               ├── security.md
│               ├── ux.md
│               └── testing.md           ← 8-2 constitution 기반 검증 결과
├── GEMINI.md
└── index.html                           ← 최종 결과물 (명세 수정 + 3가지 기능 포함)
```

### constitution.md 최종 확인

모든 실습이 끝난 후 constitution.md 가 온전한지 확인한다.

```bash
cat .specify/memory/constitution.md
```

반드시 아래 두 섹션이 모두 포함되어 있어야 한다.

```
✅ [개발 원칙] 섹션 — 단일 파일, 한국어 주석, 외부 라이브러리 금지, 모바일 지원 등
✅ [테스트 원칙] 섹션 — implement 후 체크리스트, 브라우저 직접 테스트, 수용 기준 1:1 검증 등
```

> constitution.md 에 [테스트 원칙] 이 없으면, 이 파일에 직접 추가한다.
>
> ```bash
> nano .specify/memory/constitution.md
> ```

### 최종 완성 기능 전체 테스트

```
기본 로그인 (001 — Part 6 에서 수정된 버전)
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
> 2. 001 의 `spec.md` 에 Part 6 의 변경 이력이 기록되어 있다
> 3. `constitution.md` 에 `[테스트 원칙]` 섹션이 포함되어 있다

### 실습 전체 흐름 복습

```
[최초 구현 001]
constitution(개발원칙 + 테스트원칙)
    → specify(001) → clarify → checklist(specify 후: requirements·ux) →
    plan → tasks → analyze → implement
    → checklist testing(constitution 기반: 수용기준 + 원칙 준수 검증)

[명세 수정 — Part 6]
spec.md 수정 → clarify → checklist → analyze →
plan(재실행) → tasks(재실행) → implement
    → checklist testing(변경분 추가 — testing.md 에 누적) → Git 커밋

[기능 추가 002]
specify(002) → clarify → checklist(ux+security) →
plan → tasks → analyze → implement
    → checklist testing(constitution 기반)

[기능 추가 003]
specify(003) → clarify → checklist(security+ux) →
plan → tasks → analyze → implement
    → checklist testing(constitution 기반)
```

> **constitution.md [테스트 원칙] 의 역할 요약**
>
> | 단계 | constitution.md [테스트 원칙] 의 역할 |
> |---|---|
> | `/speckit.checklist testing` | 체크리스트 항목 기준 제공 (수용기준 + 원칙 준수) |
> | 브라우저 직접 테스트 | "브라우저 시나리오 방식" 기준 제공 |
> | 모바일 확인 | 375px 기준 명시 |
> | 명세 변경 후 재검증 | 변경분만 targeted 재검증 기준 제공 |
> | 회귀 테스트 | testing.md 에 누적 → 이전 시나리오 재실행 기준 제공 |

---

## 실습 종료 후 Codespace 정지 방법

정지하지 않으면 무료 시간(월 60시간)이 계속 소모된다.  
**실습이 끝나면 반드시 Codespace 를 정지한다.**

**방법 1 (VS Code 화면에서)**

화면 왼쪽 하단의 `>< Codespaces` 를 클릭한다.  
`Stop Current Codespace` 를 선택한다.

**방법 2 (GitHub 웹에서)**

https://github.com/codespaces 에 접속한다.  
본인 Codespace 오른쪽 `...` 메뉴에서 `Stop codespace` 를 선택한다.

---

## 트러블슈팅

### specify 명령어를 찾을 수 없다 (`command not found`)

```bash
source ~/.bashrc
specify --version
```

그래도 안 되면 다시 설치한다.

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
source ~/.bashrc
uv tool install specify-cli --from git+https://github.com/github/spec-kit.git
```

---

### .gemini/commands/ 폴더가 없다

`specify init` 이 gemini 연동 없이 실행된 것이다.  
아래 명령어로 연동을 추가한다.

```bash
specify integration install gemini
ls .gemini/commands/
```

---

### slash 명령어 (`/speckit.*`) 가 동작하지 않는다

Gemini 를 반드시 `login-project` 폴더 안에서 실행했는지 확인한다.

```bash
# 채팅창 종료
/exit

# 위치 확인
pwd
# .../login-project 가 출력되어야 한다

# 다시 실행
gemini
```

---

### Gemini 인증 팝업이 뜨지 않는다

Chrome 주소창 오른쪽의 팝업 차단 아이콘을 클릭하여 **팝업 허용** 으로 변경한다.

---

### Gemini API 사용량 초과 오류 (429 또는 Quota Exceeded)

```
Error: Gemini API quota exceeded. Please try again later.
```

무료 계정은 분당 요청 수에 제한이 있다. 1~2분 기다린 후 다시 시도한다.  
반복 발생 시 강사에게 문의한다.

---

### index.html 이 생성되지 않았다

Gemini 채팅창에서 직접 요청한다.

```
/speckit.implement 명령을 실행했지만 index.html 이 생성되지 않았어.
.specify/specs/001-login-page/ 폴더의 spec.md, plan.md, tasks.md 를 참고해서
index.html 을 현재 폴더(login-project/)에 생성해줘.
```

---

### 브라우저에서 화면이 열리지 않는다 (Codespace 포트 포워딩 문제)

VS Code 하단 `포트(PORTS)` 탭을 클릭한다.  
8080 포트의 지구본 아이콘을 클릭한다.  
목록에 없으면 `포트 추가` 버튼을 클릭하고 `8080` 을 입력한다.  
포트 가시성(Visibility) 이 `Private` 이면 `Public` 으로 변경한다.

---

### npm 명령어를 찾을 수 없다 (`npm: command not found`)

```bash
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt-get install -y nodejs
node --version
npm --version
```

---

### constitution.md 내용이 비어 있다

```bash
cat .specify/memory/constitution.md
```

내용이 없으면 Gemini 채팅창에서 다시 입력하거나, 터미널에서 직접 수정한다.

```bash
nano .specify/memory/constitution.md
```

---

### checklists/ 폴더가 생성되지 않는다

`/speckit.checklist` 실행 후 파일이 생성되지 않는 경우, Gemini 에게 직접 저장을 요청한다.

```
/speckit.checklist ux
결과를 .specify/specs/001-login-page/checklists/ux.md 파일로 저장해줘.
```

또는 터미널에서 폴더를 직접 생성한 후 재실행한다.

```bash
mkdir -p .specify/specs/001-login-page/checklists
```

---

### /speckit.checklist 실행 시 "명령어를 찾을 수 없다"

`.gemini/commands/` 폴더에 `speckit.checklist.toml` 이 있는지 확인한다.

```bash
ls .gemini/commands/ | grep checklist
```

파일이 없으면 연동을 다시 추가한다.

```bash
specify integration install gemini
ls .gemini/commands/
```

---

### Part 6 에서 spec.md 수정 후 plan.md 가 자동으로 바뀌지 않는다

`/speckit.plan` 을 명시적으로 재실행해야 한다.  
`/speckit.analyze` 는 불일치를 탐지만 하고 자동으로 파일을 수정하지 않는다.

```
[올바른 순서]
spec.md 수정 → /speckit.analyze (불일치 확인) → /speckit.plan (재실행) → /speckit.tasks (재실행)
```

---

### constitution.md 에 [테스트 원칙] 이 없다

Step 1 에서 `/speckit.constitution` 입력 시 `[테스트 원칙]` 섹션이 빠진 경우다.  
터미널에서 직접 추가한다.

```bash
nano .specify/memory/constitution.md
```

아래 내용을 파일 끝에 추가한다.

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

## 전체 명령어 요약표

| 단계 | 위치 | 명령어 |
|---|---|---|
| uv 설치 | 터미널 | `curl -LsSf https://astral.sh/uv/install.sh \| sh && source ~/.bashrc` |
| specify 설치 | 터미널 | `uv tool install specify-cli --from git+https://github.com/github/spec-kit.git` |
| Gemini CLI 설치 | 터미널 | `npm install -g @google/gemini-cli` |
| 프로젝트 생성 | 터미널 | `specify init login-project --integration gemini` |
| 폴더 이동 | 터미널 | `cd login-project` |
| 명령어 파일 확인 | 터미널 | `ls .gemini/commands/` |
| 채팅창 실행 | 터미널 | `gemini` |
| 원칙+테스트원칙 설정 (1회만) | Gemini 채팅 | `/speckit.constitution` |
| constitution 확인 | 터미널 | `cat .specify/memory/constitution.md` |
| 요구사항 정의 | Gemini 채팅 | `/speckit.specify` |
| 모호함 제거 | Gemini 채팅 | `/speckit.clarify` |
| 명세 점검 — 기본 | Gemini 채팅 | `/speckit.checklist` |
| 명세 점검 — UX | Gemini 채팅 | `/speckit.checklist ux` |
| 명세 점검 — 보안 | Gemini 채팅 | `/speckit.checklist security` |
| 구현 후 품질 검증 (constitution 기반) | Gemini 채팅 | `/speckit.checklist testing` |
| 기술 설계 | Gemini 채팅 | `/speckit.plan` |
| 작업 목록 | Gemini 채팅 | `/speckit.tasks` |
| 일관성 점검 | Gemini 채팅 | `/speckit.analyze` |
| 코드 생성 | Gemini 채팅 | `/speckit.implement` |
| 서버 실행 | 터미널 | `python3 -m http.server 8080` |
| 강제 새로고침 | 브라우저 | `Ctrl+Shift+R` |

**체크리스트 실행 시점 요약**:

| 시점 | 도메인 | 참조 기준 | 이유 |
|---|---|---|---|
| clarify 직후 / plan 이전 | (기본), ux | spec.md | 명세 완성도 점검 — 수정 비용 최저 |
| clarify 직후 / plan 이전 | security | spec.md + constitution.md | 보안 요구사항 사전 검증 |
| spec.md 수정 직후 | (기본) | 변경된 spec.md | 변경된 명세의 새 구멍 점검 |
| implement 직후 | **testing** | **spec.md + constitution.md [테스트 원칙]** | **코드가 명세와 프로젝트 원칙을 모두 충족하는지 검증** |

> **`/speckit.checklist testing` 이 다른 도메인과 다른 점**  
> `ux`, `security` 는 spec.md 만 참조하지만,  
> `testing` 은 **spec.md + constitution.md [테스트 원칙]** 을 동시에 참조한다.  
> 그래서 constitution.md 에 테스트 원칙을 잘 작성할수록 체크리스트 품질이 높아진다.

**명세 변경 시 명령어 순서**:

```
spec.md 수정 (직접 편집)
    → /speckit.clarify   (변경 내용 보완)
    → /speckit.checklist (변경된 명세 점검)
    → /speckit.analyze   (plan.md·tasks.md 불일치 탐지)
    → /speckit.plan      (plan.md 재생성)
    → /speckit.tasks     (tasks.md 재생성)
    → /speckit.implement (코드 반영)
    → git commit         (명세+코드 함께 커밋)
```

---

*본 실습 자료는 GitHub Spec Kit 공식 문서를 기반으로 작성되었습니다.*  
*참고: https://github.com/github/spec-kit*  
*명령어 파일 경로 기준: Spec Kit 0.8.5 이상 / Gemini CLI 통합*
