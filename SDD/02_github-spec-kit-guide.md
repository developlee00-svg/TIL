# GitHub Spec Kit 실습 가이드

> **1차시 복습**: SDD의 4단계(Specify → Plan → Task → Implement) 이론을 학습했습니다.  
> 이번 시간에는 GitHub Spec Kit 도구에 대해서 학습합니다.

---

## 📌 목차

1. [2차시 학습 목표](#1-2차시-학습-목표)
2. [GitHub Spec Kit이란?](#2-github-spec-kit이란)
3. [프로젝트 디렉터리 구조](#3-프로젝트-디렉터리-구조)
4. [설치 및 초기 설정](#4-설치-및-초기-설정)
5. [GitHub Copilot 연동 방법](#5-github-copilot-연동-방법)
6. [핵심 워크플로 명령어](#6-핵심-워크플로-명령어)
7. [선택적 품질 향상 명령어 — checklist](#7-선택적-품질-향상-명령어--checklist)
8. [implement 후 테스트 전략](#8-implement-후-테스트-전략)
9. [전체 개발 워크플로 실습 — 로그인 화면 만들기](#9-전체-개발-워크플로-실습--로그인-화면-만들기)
10. [요구사항이 바뀔 때 대처법](#10-요구사항이-바뀔-때-대처법)
11. [팀 협업 시 활용 팁](#11-팀-협업-시-활용-팁)
12. [자주 묻는 질문 FAQ](#12-자주-묻는-질문-faq)
13. [핵심 정리 및 명령어 요약](#13-핵심-정리-및-명령어-요약)

---

## 1. 2차시 학습 목표

| 영역 | 학습 목표 |
|---|---|
| **설치** | GitHub Spec Kit을 VS Code에 설치하고 초기화할 수 있다 |
| **적용** | `/speckit.*` 명령어로 SDD 4단계를 실습할 수 있다 |
| **검증** | 구현 후 명세-코드 일치 여부를 도구로 검증할 수 있다 |
| **협업** | 팀 프로젝트에서 constitution.md를 중심으로 협업 규칙을 수립할 수 있다 |
| **변경 대응** | 요구사항 변경 시 SDD 흐름에 따라 체계적으로 대처할 수 있다 |

---

## 2. GitHub Spec Kit이란?

**GitHub Spec Kit**은 1차시에서 배운 SDD 방법론을 **실제 도구로 실천**할 수 있게 해주는 VS Code 확장 프로그램입니다.

GitHub Copilot AI와 연동하여 다음을 자동으로 지원합니다.

- 기능 요구사항 문서(spec.md) 자동 생성
- 누락된 요구사항 발견 및 명확화 질문
- 구현 계획(plan.md) 수립
- 작업(tasks.md) 목록 생성
- 아티팩트 간 일관성 검사

### 1차시 SDD 단계와 명령어 대응표

| SDD 단계 | 역할 | Spec Kit 명령어 |
|----------|------|----------------|
| 1단계: Specify (지정) | 무엇을 만들지 정의 | `/speckit.specify` + `/speckit.clarify` + `/speckit.checklist` |
| 2단계: Plan (계획) | 어떻게 만들지 설계 | `/speckit.plan` + `/speckit.analyze` |
| 3단계: Task (작업) | 실행 가능한 작업으로 분해 | `/speckit.tasks` |
| 4단계: Implement (구현) | 사양 기반으로 코드 작성 | `/speckit.implement` |
| 검증 단계 | 구현 결과가 명세를 충족하는지 확인 | `/speckit.checklist testing` + `/speckit.analyze` 재실행 |

---

## 3. 프로젝트 디렉터리 구조

GitHub Spec Kit은 다음과 같은 **일관된 폴더 구조**를 사용합니다.

```
my-project/
│
├── .github/                        # GitHub 관련 설정
│   ├── agents/                     # AI 에이전트 설정
│   └── prompts/                    # AI 프롬프트 템플릿 (자동 관리)
│
├── .specify/                       # Spec Kit 내부 설정
│   ├── memory/
│   │   └── constitution.md         # 프로젝트 원칙 및 제약 조건
│   ├── scripts/                    # 자동화 스크립트
│   └── templates/                  # 명세 템플릿
│
├── SourceCode/                     # 실제 구현 코드
│   └── ...
│
└── specs/                          # 모든 명세 아티팩트 모음
    ├── 001-document-upload/        # 기능 001: 문서 업로드
    │   ├── spec.md                 # 기능 명세 (무엇을?)
    │   ├── plan.md                 # 구현 계획 (어떻게?)
    │   ├── tasks.md                # 작업 목록 (언제, 누가?)
    │   └── checklists/             # 도메인별 품질 체크리스트
    │       ├── requirements.md     # /speckit.specify 시 자동 생성
    │       ├── ux.md               # /speckit.checklist ux 결과
    │       ├── security.md         # /speckit.checklist security 결과
    │       └── testing.md          # /speckit.checklist testing 결과
    └── 002-user-authentication/    # 기능 002: 사용자 인증
        ├── spec.md
        ├── plan.md
        └── tasks.md
```

### 왜 이런 구조인가?

| 설계 원칙 | 이유 |
|---|---|
| 코드와 명세 분리 | 코드와 문서가 뒤섞이지 않아 관리가 쉬움 |
| 기능별 번호 부여 | 개발 순서와 우선순위를 한눈에 파악 가능 |
| 기능별 독립 폴더 | 여러 팀원이 동시에 작업해도 Git 충돌 없음 |
| specs/ 디렉터리 분리 | 사양이 코드와 동등한 수준의 관리 대상임을 명시 |

---

## 4. 설치 및 초기 설정

### 사전 준비물

- [ ] VS Code 설치 (https://code.visualstudio.com)
- [ ] GitHub 계정
- [ ] GitHub Copilot 구독 (학생은 GitHub Education으로 무료 사용 가능)
- [ ] GitHub Spec Kit 확장 프로그램 설치

> **GitHub Education 무료 혜택**
> 재학 중인 학생은 [https://education.github.com](https://education.github.com) 에서
> 학교 이메일로 신청하면 GitHub Copilot을 무료로 사용할 수 있습니다.

### 초기화 명령어

VS Code 터미널에서 프로젝트 루트 디렉터리로 이동한 후 실행합니다.

```bash
# GitHub Copilot 연동으로 초기화
specify init my-project --ai copilot
```

이 명령어를 실행하면:
1. `.github/prompts/` 디렉터리가 자동 생성됩니다
2. AI 프롬프트 템플릿이 자동으로 설치됩니다
3. VS Code가 `/speckit.*` 명령을 인식하도록 설정됩니다
4. `specs/` 디렉터리와 기본 구조가 생성됩니다

> **주의**: 초기화는 프로젝트 루트 디렉터리에서 실행해야 합니다.

### 로컬 VS Code 환경에서 실습하는 경우

```
1. VS Code 실행 후 프로젝트 폴더 열기
2. 터미널에서 specify init 명령어 실행
3. Extensions 탭에서 GitHub Copilot 확장 설치 확인
4. Copilot Chat 창 열기: Ctrl+Shift+I (Mac: Cmd+Shift+I)
```

### GitHub Codespaces 환경에서 실습하는 경우

별도 설치 없이 브라우저에서 바로 실습할 수 있습니다.

```
1. GitHub 저장소에서 [Code] → [Codespaces] → [Create codespace on main] 클릭
2. Codespaces가 열리면 VS Code 웹 환경이 자동으로 구동됩니다
3. 터미널에서 specify init 명령어를 실행합니다
4. 좌측 Extensions 탭에서 GitHub Copilot 확장이 설치되어 있는지 확인합니다
```

---

## 5. GitHub Copilot 연동 방법

초기화 후, VS Code에서 **GitHub Copilot 채팅** 창을 열고 `/speckit.` 명령어를 입력하면 AI가 즉시 반응합니다.

```
1. VS Code 실행
2. Ctrl+Shift+I (Mac: Cmd+Shift+I) 또는 View > GitHub Copilot Chat으로 채팅 창 열기
3. 채팅 창에 /speckit.specify 입력
4. 기능 설명 입력 → AI가 명세 생성
```

### AI가 자동으로 처리하는 것 vs 직접 해야 하는 것

| 자동 처리 (AI가 알아서) | 직접 입력 (개발자가 해야 함) |
|---|---|
| 프롬프트 템플릿 로딩 | 기능 설명 제공 |
| constitution.md 참조 삽입 | AI의 명확화 질문에 답변 |
| 명세 형식(Given/When/Then) 관리 | 생성된 명세 검토 및 수정 |
| 아티팩트 파일 저장 | 팀원과 명세 내용 합의 |

---

## 6. 핵심 워크플로 명령어

### 전체 워크플로 순서

```
/speckit.specify → /speckit.clarify → /speckit.checklist → /speckit.plan → /speckit.analyze → /speckit.tasks → /speckit.implement → [테스트 검증]
      ①                 ②                    ③                  ④                 ⑤                  ⑥                  ⑦                    ⑧
  명세 생성         간격 분석          품질 체크리스트        계획 수립         일관성 검사         작업 생성           코드 구현           구현 결과 검증
  [SDD 1단계]      [SDD 1단계]        [SDD 1단계]          [SDD 2단계]       [SDD 2단계]        [SDD 3단계]        [SDD 4단계]        [검증 단계 → 8절]
```

> ③ `/speckit.checklist`에서 미완성 항목이 발견되면, spec.md 보완 후 재실행합니다.
> ⑤ `/speckit.analyze`에서 불일치가 발견되면, 수정 후 재실행합니다.

---

### ① `/speckit.specify` — 기능 명세 생성

**역할**: 기능 설명을 입력하면 완전한 명세 문서(spec.md)를 자동 생성합니다.

**생성되는 내용**:
- 사용자 스토리 (User Stories)
- 수용 기준 (Acceptance Criteria)
- 기능 요구사항 (Functional Requirements)
- 비기능 요구사항 (Non-Functional Requirements)
- 엣지 케이스 (Edge Cases)

**사용 예시**:

```
[사용자 입력]
/speckit.specify
과제 제출 기능을 만들려고 합니다. 학생이 PDF 파일을 업로드하고
교수가 확인할 수 있어야 합니다.

[AI 출력 — spec.md 생성]

## 사용자 스토리
- 학생으로서, PDF 파일(최대 10MB)을 업로드하고 싶다.
  왜냐하면 교수님께 과제를 제출해야 하기 때문이다.
- 교수로서, 제출된 과제 목록을 확인하고 싶다.
  왜냐하면 학생 제출 현황을 파악해야 하기 때문이다.

## 수용 기준
Given: 학생이 로그인 상태일 때
When:  PDF 파일을 선택하고 제출 버튼을 클릭하면
Then:  업로드 완료 메시지와 제출 시각이 표시된다
...
```

---

### ② `/speckit.clarify` — 명세 간격 분석 및 명확화

**역할**: 명세의 모호한 부분, 누락된 세부사항, 미처리된 엣지 케이스를 찾아 질문합니다.

**AI가 생성하는 질문 예시**:

```
[AI 출력]
명세를 분석한 결과 다음 항목이 명확하지 않습니다.

Q1. 파일 업로드 시 최대 동시 업로드 수는 몇 개인가요?
    a) 1개 (순차 처리)
    b) 최대 5개 동시 업로드
    c) 제한 없음

Q2. 네트워크 오류로 업로드가 중단될 경우:
    a) 처음부터 다시 업로드
    b) 이어받기(Resume) 지원
    c) 오류 메시지만 표시

Q3. 이미 같은 파일명이 존재할 때:
    a) 덮어쓰기
    b) 파일명에 번호 추가 (과제_1.pdf, 과제_2.pdf)
    c) 오류 메시지 표시
```

> **이 단계가 중요한 이유**
> 명세 단계에서 1시간 투자 = 구현 단계에서 10시간 절약

---

### ③ `/speckit.plan` — 구현 계획 수립

**역할**: spec.md를 바탕으로 기술적 구현 계획(plan.md)을 생성합니다.
이때 constitution.md에 정의된 기술 스택 원칙을 자동으로 참조합니다.

**생성되는 내용**: 아키텍처 설계, 데이터베이스 스키마, API 엔드포인트 설계, 기술 스택 선택 이유, 단계별 구현 순서

**예시 출력 (plan.md 일부)**:

```markdown
# 구현 계획: 과제 제출 기능 (plan.md)

## 아키텍처
- Frontend: React + TypeScript
- Backend:  FastAPI (Python)
- Database: Azure SQL Database  ← constitution.md 원칙 반영
- Storage:  Azure Blob Storage

## API 설계
POST /api/assignments/upload   - 과제 파일 업로드
GET  /api/assignments          - 과제 목록 조회 (교수용)

## 구현 순서
1. Azure Blob Storage 컨테이너 설정
2. DB 스키마 생성 (assignments 테이블)
3. 업로드 API 구현 및 유효성 검사
4. React 업로드 UI 컴포넌트 개발
```

---

### ④ `/speckit.analyze` — 아티팩트 일관성 검사

**역할**: spec.md, plan.md, tasks.md, constitution.md 간의 **불일치를 자동으로 발견**합니다.

**발견 가능한 불일치 예시**:

```
[AI 분석 결과]

⚠ [불일치 1] 데이터베이스
   plan.md는 PostgreSQL 사용을 제안하지만,
   constitution.md는 Azure SQL Database를 요구합니다.
   → plan.md 수정 필요

⚠ [불일치 2] 감사 로그
   spec.md는 모든 파일 접근에 감사 로그를 요구하지만,
   plan.md에 로깅 구현 계획이 없습니다.
   → plan.md에 로깅 섹션 추가 필요
```

불일치를 모두 수정한 후 재실행합니다.

```
[재실행 결과]
✅ spec.md ↔ plan.md: 모든 요구사항 대응 확인
✅ plan.md ↔ tasks.md: 모든 계획 항목 작업화 확인
✅ plan.md ↔ constitution.md: 기술 스택 원칙 준수 확인
→ 다음 단계(/speckit.tasks)로 진행 가능합니다.
```

---

### ⑤ `/speckit.tasks` — 작업 목록 생성

**역할**: 구현 계획을 **개발자(또는 AI 에이전트)가 바로 실행할 수 있는 구체적인 작업 목록**으로 변환합니다.

**예시 출력 (tasks.md)**:

```markdown
# 작업 목록: 과제 제출 기능 (tasks.md)

## Sprint 1 — 기반 설정 (2일)
- [ ] Azure Blob Storage 컨테이너 생성 및 접근 권한 설정
- [ ] Azure SQL Database assignments 테이블 스키마 생성
- [ ] FastAPI 프로젝트 초기 설정 및 의존성 설치

## Sprint 2 — 핵심 기능 구현 (3일)
- [ ] 파일 업로드 API 구현 (POST /api/assignments/upload)
- [ ] 파일 크기(10MB) 및 형식(PDF) 유효성 검사 로직
- [ ] 과제 목록 조회 API 구현 (GET /api/assignments)
- [ ] React 파일 업로드 UI 컴포넌트 개발

## Sprint 3 — 테스트 및 마무리 (1일)
- [ ] 단위 테스트 작성 (커버리지 80% 이상)
- [ ] 통합 테스트 작성 (업로드 → 목록 조회 시나리오)
```

---

### ⑥ `/speckit.implement` — 사양 기반 코드 구현

**역할**: tasks.md의 작업 목록을 하나씩 실제 코드로 구현하도록 AI가 지원합니다.
단순히 코드를 생성하는 것이 아니라, **spec.md의 수용 기준을 코드에 그대로 반영**합니다.

**사용 방법**:

```
[사용자 입력]
/speckit.implement
Sprint 1의 첫 번째 작업부터 시작해 주세요.
```

**AI 지원 예시 (FastAPI 업로드 API)**:

```python
# Implements: User Story - 학생 과제 제출
# Acceptance Criteria: AC-001-1, AC-001-2 (파일 크기 및 형식 검증)
# Task: TASK-002 (파일 업로드 API 구현)

from fastapi import APIRouter, UploadFile, File, HTTPException
from typing import Annotated

router = APIRouter()

MAX_FILE_SIZE = 10 * 1024 * 1024  # 10MB
ALLOWED_TYPES = {"application/pdf"}

@router.post("/api/assignments/upload")
async def upload_assignment(
    file: Annotated[UploadFile, File(description="제출할 PDF 파일")]
):
    # AC-001-2: PDF 형식만 허용
    if file.content_type not in ALLOWED_TYPES:
        raise HTTPException(status_code=400, detail="PDF 파일만 업로드 가능합니다.")

    # AC-001-1: 10MB 이하만 허용
    contents = await file.read()
    if len(contents) > MAX_FILE_SIZE:
        raise HTTPException(status_code=400, detail="파일 크기는 10MB 이하여야 합니다.")

    saved_url = await save_to_blob_storage(file.filename, contents)

    return {
        "message": "업로드가 완료되었습니다.",
        "filename": file.filename,
        "submitted_at": datetime.utcnow().isoformat()
    }
```

> **핵심 포인트**
> 코드 주석에 `Implements`, `Acceptance Criteria`, `Task` 참조 정보가 포함됩니다.
> 이것이 1차시에서 배운 **추적성(Traceability)** 입니다.
> 나중에 버그가 생겼을 때 "이 코드는 어떤 요구사항에서 나왔는가"를 즉시 추적할 수 있습니다.

---

## 7. 선택적 품질 향상 명령어 — checklist

### `/speckit.checklist` — 명세 완성도 점검

명세의 완성도를 특정 **도메인(영역) 관점**에서 검증하는 체크리스트를 자동 생성합니다.

> **`/speckit.analyze`와의 차이**
>
> | 명령어 | 목적 | 비유 |
> |---|---|---|
> | `/speckit.checklist` | 하나의 명세를 도메인 관점에서 품질 점검 | 문서 작성 품질 검사 |
> | `/speckit.analyze` | 여러 문서 간 불일치 탐지 | 문서들 간 교차 검증 |

---

### 지원 도메인 종류

| 도메인 키워드 | 파일명 | 주요 검사 내용 |
|---|---|---|
| `ux` | `checklists/ux.md` | 사용자 흐름, 오류 메시지, 빈 상태, 모바일 반응형 등 |
| `security` | `checklists/security.md` | 인증·인가, 입력값 검증, 암호화, 데이터 보호 등 |
| `testing` | `checklists/testing.md` | 테스트 시나리오 커버리지, 엣지 케이스, 경계값 등 |
| `performance` | `checklists/performance.md` | 응답 시간, 부하 처리, 캐싱, 페이지네이션 등 |
| `accessibility` | `checklists/accessibility.md` | WCAG 기준, 키보드 탐색, 스크린 리더 지원 등 |
| `api` | `checklists/api.md` | 엔드포인트 정의, 에러 코드, 버전 관리, rate limit 등 |
| _(키워드 없음)_ | `checklists/requirements.md` | 전반적인 요구사항 완성도 (자동 생성) |

---

### 사용 방법

```
/speckit.checklist ux          # UX 관점 체크리스트 생성
/speckit.checklist security    # 보안 관점 체크리스트 생성
/speckit.checklist testing     # 테스트 시나리오 체크리스트 생성
/speckit.checklist performance # 성능 요구사항 체크리스트 생성
/speckit.checklist api         # API 설계 체크리스트 생성
```

> **실행 시점 권장**
> - `/speckit.clarify` 이후, `/speckit.plan` **이전**에 실행 → 설계 전 명세 완성도 확인
> - `/speckit.implement` **이후**에 `testing` 도메인으로 실행 → 구현 결과 검증

---

### 도메인별 체크리스트 생성 예시

#### `/speckit.checklist ux` — UX 체크리스트

```markdown
# UX 체크리스트: 로그인 화면 (checklists/ux.md)

## 사용자 흐름 (User Flow)
- [ ] CHK001 - 로그인 성공 후 이동 경로(/dashboard)가 명확히 정의되어 있는가?
- [ ] CHK002 - 로그인 실패 후 사용자가 다시 시도할 수 있는 흐름이 명시되어 있는가?
- [ ] CHK003 - 이미 로그인된 상태에서 로그인 페이지 접근 시 처리 방법이 정의되어 있는가?

## 오류 처리 및 메시지 (Error Handling)
- [ ] CHK004 - 모든 입력 오류에 구체적인 메시지가 정의되어 있는가?
- [ ] CHK005 - 오류 메시지가 사용자 친화적 언어로 작성되어 있는가?
  ("오류 코드 401" (X) → "비밀번호가 틀렸습니다" (O))

## 피드백 (Feedback)
- [ ] CHK006 - 로그인 처리 중(로딩) 상태의 UI 표현이 명시되어 있는가?
- [ ] CHK007 - 로그인 성공 후 사용자에게 주는 피드백이 정의되어 있는가?

---
총 N개 항목 | "아니오" 항목은 spec.md에 보완 후 다음 단계로 진행하세요.
```

#### `/speckit.checklist security` — 보안 체크리스트

```markdown
# 보안 체크리스트: 로그인 화면 (checklists/security.md)

## 인증 (Authentication)
- [ ] CHK001 - 비밀번호가 HTTPS로만 전송됨이 명시되어 있는가?
- [ ] CHK002 - 비밀번호가 서버 측에서 bcrypt 등으로 해싱됨이 명시되어 있는가?
- [ ] CHK003 - JWT 토큰 만료 시간이 정의되어 있는가?

## 브루트 포스 방지 (Brute Force Prevention)
- [ ] CHK004 - 로그인 실패 횟수 제한이 정의되어 있는가? (예: 5회)
- [ ] CHK005 - 계정 잠금 기간 및 자동 해제 방식이 명시되어 있는가?

## 입력값 검증 (Input Validation)
- [ ] CHK006 - 이메일 형식 유효성 검사가 클라이언트와 서버 양쪽에 명시되어 있는가?
- [ ] CHK007 - SQL Injection, XSS 등 입력값 공격 방어 요구사항이 명시되어 있는가?
```

#### `/speckit.checklist testing` — 테스트 체크리스트

```markdown
# 테스트 체크리스트: 로그인 화면 (checklists/testing.md)

## 단위 테스트 시나리오 (Unit Test Scenarios)
- [ ] CHK001 - 올바른 이메일/비밀번호로 로그인 성공 시나리오가 명세에 있는가?
- [ ] CHK002 - 잘못된 비밀번호 입력 시 오류 응답 시나리오가 명세에 있는가?
- [ ] CHK003 - 빈 입력값으로 제출 시 처리 시나리오가 명세에 있는가?

## 경계값 테스트 (Boundary Testing)
- [ ] CHK004 - 로그인 실패 4회(잠금 직전)와 5회(잠금) 케이스가 명세에 있는가?
- [ ] CHK005 - 계정 잠금 상태에서 로그인 시도 시 처리 시나리오가 있는가?

## 통합 테스트 (Integration Test)
- [ ] CHK006 - 로그인 성공 → 대시보드 이동 흐름 전체가 명세에 커버되는가?
```

---

### 체크리스트 결과 활용 방법

```
1. 체크리스트 실행
        ↓
2. "아니오" 항목 발견
        ↓
3. spec.md 해당 항목 보완
        ↓
4. 체크리스트 재실행
        ↓
5. 모든 항목 "예" 확인
        ↓
6. 다음 단계로 진행
```

---

## 8. implement 후 테스트 전략

### `/speckit.test` 는 공식 명령어인가?

> **결론: GitHub Spec Kit 공식 명령어에 `/speckit.test` 는 현재 존재하지 않습니다.**

공식 명령어는 아래 8개가 전부입니다.

| 명령어 | 역할 |
|---|---|
| `/speckit.constitution` | 프로젝트 원칙 설정 |
| `/speckit.specify` | 요구사항 정의 |
| `/speckit.clarify` | 모호함 제거 |
| `/speckit.checklist` | 명세 완성도 점검 |
| `/speckit.plan` | 기술 설계 |
| `/speckit.tasks` | 작업 목록 생성 |
| `/speckit.analyze` | 문서 간 일관성 점검 |
| `/speckit.implement` | 코드 생성 |

> **왜 별도 테스트 명령어가 없는가?**
> Spec Kit의 설계 철학은 **"테스트 기준은 명세(spec.md)의 수용 기준에서 이미 정의된다"** 는 것입니다.
> `constitution.md`에 테스트 원칙을 선언해 두면 implement 시 테스트 코드가 함께 생성됩니다.
>
> GitHub Spec Kit Issues #1745에서 커뮤니티가 `/speckit.verify` 추가를 요청 중 (2026년 5월 기준).

---

### 방법 1: `/speckit.checklist testing` 활용 (권장)

implement 완료 후 가장 직접적인 방법입니다.

```
[사용자 입력]
/speckit.checklist testing
생성된 코드가 spec.md 의 모든 수용 기준을 충족하는지
테스트 시나리오 관점에서 점검해줘.
```

**AI 출력 예시 (implement 후 실행)**:

```markdown
# 테스트 검증 결과: 로그인 화면 (implement 후)

## 수용 기준 대비 구현 상태

### ✅ 구현 완료
- AC-login-1: 로그인 성공 → 대시보드 이동 ✅
- AC-login-2: 잘못된 비밀번호 → 오류 메시지 표시 ✅
- AC-login-3: 빈 입력 → 검증 메시지 구현됨 ✅

### ❌ 미구현 / 불일치 발견
- AC-login-4: 5회 실패 → 계정 잠금
  현재 코드: 실패 횟수 카운트 없음
  → 수정 필요

### 권장 조치
1. Redis 기반 실패 횟수 카운터 구현 추가
2. 로그인 버튼에 로딩 상태 및 disabled 처리 추가
```

**미구현 항목 발견 시 처리**:

```
1. Copilot Chat에 수정 요청
   예) "AC-login-4 — 로그인 5회 실패 시 계정 잠금 로직을 login.py에 추가해줘"
2. 코드 수정 후 브라우저에서 직접 확인
3. /speckit.checklist testing 재실행 → 전체 통과 확인
4. Git 커밋: "fix: 로그인 5회 실패 잠금 로직 추가 (AC-login-4)"
```

---

### 방법 2: `/speckit.analyze` 재실행

implement 이후 **명세-코드 드리프트(Spec Drift)** 를 탐지합니다.

```
[사용자 입력]
/speckit.analyze
implement 이후 spec.md 의 수용 기준이 코드에 모두 반영되었는지 확인해줘.

[예상 출력]
✅ spec.md ↔ plan.md: 모든 요구사항 대응 확인
⚠ [명세-코드 드리프트 감지]
   spec.md AC-login-4: "5회 실패 시 계정 잠금" 수용 기준 존재
   코드: 실패 횟수 카운터 로직 없음
   → 구현 누락 가능성 — 코드 확인 및 보완 필요
```

> **방법 1 vs 방법 2**
> 방법 1은 **테스트 시나리오 관점**에서 "어떤 케이스가 빠졌는가"를 찾습니다.
> 방법 2는 **문서 간 일관성 관점**에서 "명세와 코드가 어긋난 곳이 있는가"를 찾습니다.
> 중요한 기능이라면 두 방법을 모두 실행하는 것이 가장 안전합니다.

---

### 방법 3: `constitution.md` 에 테스트 원칙 명시 (근본적 해결책)

방법 1, 2는 구현 후 사후 검증이지만, 방법 3은 **구현 중에 테스트 코드가 자동 생성**되도록 하는 근본적 해결책입니다.

```
[사용자 입력]
/speckit.constitution
기존 원칙에 아래 테스트 원칙을 추가해줘:

## 테스트 원칙
- 모든 기능 구현 시 단위 테스트(Unit Test)를 함께 작성한다
- 테스트 커버리지는 80% 이상을 유지한다
- 각 수용 기준(Acceptance Criteria)에 대응하는 테스트 케이스를 반드시 포함한다
- 테스트 파일명은 구현 파일명에 _test 또는 .test를 붙여 작성한다
```

**constitution.md 적용 후 implement 결과**:

```python
# [테스트 코드] test_login.py  ← constitution.md 원칙에 따라 자동 생성
# Test Cases: AC-login-1 (성공), AC-login-2 (실패), AC-login-3 (잠금)

import pytest

class TestLogin:
    def test_login_success(self, client):
        """AC-login-1: 올바른 이메일/비밀번호로 로그인 성공"""
        response = client.post("/api/auth/login", json={
            "email": "user@example.com",
            "password": "correct_password"
        })
        assert response.status_code == 200
        assert "accessToken" in response.json()

    def test_login_wrong_password(self, client):
        """AC-login-2: 잘못된 비밀번호 → 401 오류"""
        response = client.post("/api/auth/login", json={
            "email": "user@example.com",
            "password": "wrong_password"
        })
        assert response.status_code == 401

    def test_login_account_locked(self, client, redis_mock):
        """AC-login-3: 5회 실패 → 423 잠금"""
        redis_mock.set("login_fail:user@example.com", 5)
        response = client.post("/api/auth/login", json={
            "email": "user@example.com",
            "password": "any_password"
        })
        assert response.status_code == 423
```

---

### 세 가지 방법 비교

| 구분 | 방법 1: `/speckit.checklist testing` | 방법 2: `/speckit.analyze` 재실행 | 방법 3: `constitution.md` 테스트 원칙 |
|---|---|---|---|
| 실행 시점 | implement 완료 후 | implement 완료 후 | 프로젝트 시작 시 (1회) |
| 주요 역할 | 수용 기준 충족 여부 점검 | 명세-코드 드리프트 탐지 | implement 시 테스트 코드 자동 생성 |
| 난이도 | 쉬움 | 쉬움 | 보통 |
| 추천 대상 | 개별 기능 완료 후 빠른 점검 | 변경 후 일관성 확인 | 모든 팀 프로젝트 |

---

## 9. 전체 개발 워크플로 실습 — 로그인 화면 만들기

### Step 1: 명세 생성 — `/speckit.specify`

```
[사용자 입력]
/speckit.specify
이메일과 비밀번호로 로그인할 수 있는 화면이 필요합니다.
로그인 실패 시 오류 메시지를 보여주고,
성공 시 대시보드 페이지로 이동해야 합니다.
```

**AI가 생성하는 spec.md**:

```markdown
# 기능 명세: 로그인 화면 (spec.md)

## 사용자 스토리
- 사용자로서, 이메일과 비밀번호를 입력하여 서비스에 로그인하고 싶다.
  왜냐하면 내 개인화된 대시보드에 접근해야 하기 때문이다.

## 기능 요구사항
- 이메일 입력 필드 (텍스트)
- 비밀번호 입력 필드 (마스킹 처리)
- 로그인 버튼
- 로그인 성공 시 → 대시보드 페이지(/dashboard)로 이동
- 로그인 실패 시 → 오류 메시지 표시 (페이지 이동 없음)

## 수용 기준 (Acceptance Criteria)

Given: 사용자가 로그인 페이지에 있을 때
When:  올바른 이메일과 비밀번호를 입력하고 로그인 버튼을 클릭하면
Then:  대시보드 페이지로 이동하고 환영 메시지가 표시된다

Given: 사용자가 로그인 페이지에 있을 때
When:  잘못된 비밀번호를 입력하고 로그인 버튼을 클릭하면
Then:  "이메일 또는 비밀번호가 올바르지 않습니다" 메시지가 표시된다

Given: 사용자가 로그인 페이지에 있을 때
When:  아무것도 입력하지 않고 로그인 버튼을 클릭하면
Then:  "이메일과 비밀번호를 입력해 주세요" 메시지가 표시된다

## 비기능 요구사항
- 로그인 API 응답 시간: 2초 이내
- 비밀번호는 평문으로 전송 금지 (HTTPS 필수)
- 로그인 실패 5회 이상 시 계정 잠금 (보안)
- 모바일 화면(375px 이상)에서도 정상 표시

## 엣지 케이스
- 비밀번호 5회 연속 실패 → 계정 잠금 안내 및 이메일 발송
- 이미 로그인된 상태에서 로그인 페이지 접근 → 대시보드로 자동 리다이렉트
```

---

### Step 2: 명확화 질문 답변 — `/speckit.clarify`

```
[AI 출력]
Q1. "비밀번호 찾기" 링크가 필요한가요?
    a) 로그인 화면에 포함
    b) 이번 기능 범위에서 제외

Q2. "로그인 상태 유지" (자동 로그인) 체크박스가 필요한가요?
    a) 필요함 (세션 유지 기간: ___일)
    b) 불필요함

Q3. 소셜 로그인(Google, Kakao 등) 버튼을 포함해야 하나요?
    a) 포함
    b) 이번 범위에서는 이메일/비밀번호만

Q4. 계정 잠금 해제 방법은 무엇인가요?
    a) 일정 시간(예: 30분) 경과 후 자동 해제
    b) 이메일 인증 후 해제
```

**팀의 답변 적용 예시**:
```
Q1 → a) 비밀번호 찾기 링크 포함
Q2 → a) 로그인 상태 유지 7일
Q3 → b) 이메일/비밀번호만 (소셜 로그인은 002 기능으로 분리)
Q4 → a) 30분 후 자동 해제 + 이메일 알림
```

> **이 단계의 핵심**
> Q2의 "로그인 상태 유지"는 처음 명세에 없었지만 AI가 발굴했습니다.
> 구현 중에 이 질문이 나왔다면 이미 절반 완성된 코드를 수정해야 했을 것입니다.

---

### Step 2.5: 명세 품질 점검 — `/speckit.checklist ux`

```
[AI가 생성하는 checklists/ux.md 일부]

- [x] CHK001 - 로그인 성공 후 /dashboard 이동 경로가 명확히 정의되어 있는가? ✅
- [x] CHK002 - 실패 후 재시도 흐름이 명시되어 있는가? ✅
- [ ] CHK003 - 로딩 중 버튼 비활성화 또는 스피너 표시가 명세에 있는가? ❌ → 보완 필요
- [ ] CHK004 - 태블릿(768px) 지원 여부가 정의되어 있는가? ❌ → 보완 필요
```

**❌ 항목 발견 시 spec.md 보완 → 재실행 → 전체 통과 후 다음 단계 진행**

---

### Step 3: 구현 계획 수립 — `/speckit.plan`

```markdown
# 구현 계획: 로그인 화면 (plan.md)

## 기술 스택
- Frontend: React + TypeScript
- Backend API: FastAPI (Python)
- 인증 방식: JWT (Access Token + Refresh Token)
- 데이터베이스: Azure SQL Database  ← constitution.md 반영

## API 설계
POST /api/auth/login
  요청:  { email: string, password: string, rememberMe: boolean }
  성공(200): { accessToken, refreshToken, user: { id, name } }
  실패(401): { message: "이메일 또는 비밀번호가 올바르지 않습니다" }
  잠금(423): { message: "계정이 잠겼습니다. 30분 후 다시 시도하세요" }

## 보안 처리
- HTTPS 전송 필수
- 비밀번호 bcrypt 해싱 (서버 측)
- 로그인 실패 횟수 Redis에 저장 (TTL: 30분)
- JWT 만료시간: accessToken 1시간, refreshToken 7일
```

---

### Step 4: 일관성 검사 — `/speckit.analyze`

```
[AI 출력]
⚠ [불일치 1] 이메일 알림
   spec.md: "30분 후 자동 해제 + 이메일 알림"
   plan.md: Redis TTL로 자동 해제만 언급, 이메일 알림 로직 없음
   → plan.md에 이메일 발송 서비스 연동 계획 추가 필요

⚠ [불일치 2] 모바일 반응형
   spec.md: 375px 이상 지원 명시
   plan.md: 반응형 CSS 처리 계획 없음
   → plan.md에 반응형 처리 명시 필요
```

수정 후 재실행 → ✅ 모든 항목 일치 확인 → Step 5로 진행

---

### Step 5: 작업 목록 생성 — `/speckit.tasks`

```markdown
# 작업 목록: 로그인 화면 (tasks.md)

## Sprint 1 — 백엔드 기반 구축 (2일)
- [ ] users 테이블 스키마 확인 및 DB 마이그레이션 스크립트 작성
- [ ] POST /api/auth/login 엔드포인트 구현
- [ ] bcrypt 비밀번호 검증 로직 구현
- [ ] JWT 발급 및 검증 유틸 구현
- [ ] Redis 연동 — 로그인 실패 횟수 카운터 구현 (TTL 30분)
- [ ] 계정 잠금 시 이메일 알림 발송 연동

## Sprint 2 — 프론트엔드 구현 (2일)
- [ ] LoginForm 컴포넌트 마크업 및 스타일링
- [ ] 이메일 형식 유효성 검사 구현
- [ ] 비밀번호 표시/숨김 토글 버튼 구현
- [ ] "로그인 상태 유지" 체크박스 구현 (7일 토큰 저장)
- [ ] 로그인 API 연동 (Axios)
- [ ] 모바일 반응형 CSS 적용 (375px 이상)

## Sprint 3 — 테스트 및 마무리 (1일)
- [ ] 단위 테스트: 로그인 API (성공/실패/잠금 케이스)
- [ ] E2E 테스트: 로그인 성공 → 대시보드 이동
- [ ] E2E 테스트: 5회 실패 → 잠금 메시지 확인
```

---

### Step 6: 구현 시작 — `/speckit.implement`

```python
# Implements: User Story - 이메일/비밀번호 로그인
# Acceptance Criteria: AC-login-1, AC-login-2, AC-login-3
# Task: TASK-002 (POST /api/auth/login 엔드포인트 구현)

from fastapi import APIRouter, HTTPException
from pydantic import BaseModel, EmailStr
import bcrypt
import redis

router = APIRouter()
r = redis.Redis()

class LoginRequest(BaseModel):
    email: EmailStr
    password: str
    rememberMe: bool = False

@router.post("/api/auth/login")
def login(req: LoginRequest):
    fail_key = f"login_fail:{req.email}"
    fail_count = int(r.get(fail_key) or 0)

    # AC-login-3: 5회 실패 시 계정 잠금
    if fail_count >= 5:
        raise HTTPException(423, "계정이 잠겼습니다. 30분 후 다시 시도하세요.")

    user = get_user_by_email(req.email)

    # AC-login-2: 이메일 또는 비밀번호 불일치
    if not user or not bcrypt.checkpw(req.password.encode(), user.password_hash):
        r.incr(fail_key)
        r.expire(fail_key, 1800)  # 30분 TTL
        raise HTTPException(401, "이메일 또는 비밀번호가 올바르지 않습니다.")

    # AC-login-1: 로그인 성공
    r.delete(fail_key)
    token = create_jwt(user.id, days=7 if req.rememberMe else 1)
    return {
        "accessToken": token,
        "user": {"id": user.id, "name": user.name}
    }
```

---

### Step 7: 구현 후 테스트 검증

**7-1. `/speckit.checklist testing` 실행**

```
[AI 출력]
✅ AC-login-1: 로그인 성공 → 대시보드 이동 구현됨
✅ AC-login-2: 잘못된 비밀번호 → 401 오류 구현됨
✅ AC-login-3: 5회 실패 → 423 잠금 구현됨
❌ AC-login-4: 로딩 중 버튼 비활성화 — 프론트엔드 수정 필요

→ AC-login-4 수정 후 재실행하세요.
```

**7-2. `/speckit.analyze` 재실행 → 전체 통과 확인**

**7-3. Git 커밋**

```bash
git add specs/001-login/checklists/testing.md
git add src/api/login.py
git add tests/test_login.py
git commit -m "feat(001): 로그인 API 구현 + 테스트 검증 완료

- AC-login-1~4 수용 기준 모두 충족
- /speckit.checklist testing 전체 통과
- /speckit.analyze 명세-코드 일관성 확인"
```

---

## 10. 요구사항이 바뀔 때 대처법

### 변경 대처 순서

```
1. spec.md 수정      (무엇이 변경되었는가? → 사양이 진실의 근원)
        ↓
2. /speckit.clarify  (변경된 명세에 새로운 모호함 확인)
        ↓
3. /speckit.checklist (변경된 명세의 새로운 구멍 점검)
        ↓
4. /speckit.plan 재실행   (변경된 명세 기반으로 계획 재수립)
        ↓
5. /speckit.tasks 재실행  (변경된 계획으로 작업 목록 재생성)
        ↓
6. /speckit.analyze       (새로운 불일치 최종 확인)
        ↓
7. 구현 코드 업데이트
        ↓
8. /speckit.checklist testing (재검증)
```

> **왜 spec.md부터 수정하는가?**
> SDD의 핵심 원칙은 "사양이 진실의 근원"입니다.
> 코드를 먼저 수정하면 사양과 코드가 어긋나는 순간부터 문서는 거짓 문서가 됩니다.

### 나쁜 방법 vs 좋은 방법

| 나쁜 방법 | 좋은 방법 |
|---|---|
| 코드만 바꾸고 문서는 그대로 둠 | spec.md부터 순서대로 업데이트 |
| Slack 메시지로만 변경 공유 | 아티팩트에 공식 반영 후 공유 |
| 기억에 의존해서 변경 사항 추적 | Git 커밋으로 변경 이력 관리 |
| 변경 후 테스트 검증 생략 | `/speckit.checklist testing` 재실행 |

### 변경 요구사항 예시

```
[상황] 출시 3일 전, 기획자가 요청했습니다.
"로그인 실패 잠금 기준을 5회에서 3회로 바꿔주세요."

[나쁜 방법]
개발자가 코드에서 숫자 5를 3으로만 바꿈
→ spec.md에는 여전히 "5회"로 적혀 있음
→ 6개월 후 신규 팀원이 spec.md를 보고 혼란

[좋은 방법]
1. spec.md의 "5회" → "3회"로 수정
2. /speckit.analyze 실행 → 불일치 탐지
3. 영향받는 부분만 수정
4. /speckit.checklist testing 재실행
5. Git 커밋: "feat: 계정 잠금 기준 5회→3회 변경 (spec/plan/code/test 모두 반영)"
```

---

## 11. 팀 협업 시 활용 팁

### 기능 병렬 개발

```
팀원 A: specs/001-login/ 작업
팀원 B: specs/002-dashboard/ 작업    ← Git 충돌 없음!
팀원 C: specs/003-notification/ 작업
```

각 기능이 독립된 폴더를 가지므로 **Git 충돌(conflict) 없이 병렬 작업**이 가능합니다.

### 코드 리뷰 시 활용

PR(Pull Request)을 올릴 때 명세 아티팩트도 함께 포함합니다.

```
## PR 설명 예시

## 변경 사항
- 로그인 화면 구현 (specs/001-login/ 참조)

## 명세 연결
- spec.md: 모든 수용 기준 달성 확인
- /speckit.analyze: 불일치 없음 확인
- /speckit.checklist testing: 전체 통과 (checklists/testing.md 참조)

## 테스트
- 단위 테스트 커버리지: 85%
- E2E 테스트: 로그인 성공/실패/잠금 시나리오 모두 통과
```

### constitution.md 공동 관리

```
constitution.md 변경 프로세스:

1. 변경 제안 → GitHub Issue 등록
2. 팀 미팅에서 찬반 논의
3. 합의 후 PR로 변경
4. 팀 전원 리뷰 승인 후 머지
5. 기존 기능의 plan.md에 영향 있으면 /speckit.analyze로 확인
```

---

## 12. 자주 묻는 질문 FAQ

**Q: GitHub Copilot 없이 사용할 수 있나요?**
A: 현재 이 가이드는 Copilot 연동 기준입니다. Claude, Gemini 등 다른 AI 도구를 사용하는 경우, 각 도구의 공식 Spec Kit 연동 문서를 참고하세요. 명령어 체계는 동일하고 AI 모델만 교체됩니다.

**Q: `/speckit.checklist`는 반드시 실행해야 하나요?**
A: 선택 사항이지만, 팀 프로젝트나 보안·UX가 중요한 기능이라면 강력히 권장합니다. 실행 비용(수 분)보다 구현 후 발견했을 때의 수정 비용(수 시간)이 훨씬 큽니다.

**Q: 체크리스트에서 "아니오"가 많이 나왔습니다. 처음부터 다시 해야 하나요?**
A: 아닙니다. "아니오" 항목에 해당하는 내용만 spec.md에 보완하고, 체크리스트를 재실행하면 됩니다. 이 반복 과정이 명세를 점진적으로 완성해 가는 SDD의 핵심입니다.

**Q: constitution.md는 언제 만들어야 하나요?**
A: 프로젝트 시작 직후, 첫 번째 기능 명세(`/speckit.specify`) 실행 **전에** 만드세요. constitution.md가 없으면 AI가 기능마다 다른 기술 스택을 제안할 수 있습니다.

**Q: AI가 생성한 명세를 그대로 사용해도 되나요?**
A: AI의 출력은 **초안(Draft)**으로 보고, 반드시 팀원과 검토 후 사용하세요. AI는 도메인 지식(업무 특성, 조직 규칙 등)을 완벽히 알 수 없으므로 검토가 필수입니다.

**Q: spec.md, plan.md, tasks.md는 직접 수정해도 되나요?**
A: 당연히 됩니다. AI가 생성한 파일은 출발점입니다. 수정 후에는 반드시 `/speckit.analyze`를 실행하여 다른 아티팩트와의 일관성을 확인하세요.

**Q: 명세를 영어로 작성해야 하나요?**
A: 한국어로 작성해도 됩니다. 다만 팀 내 언어를 통일할 것을 권장합니다. 여러 언어가 섞이면 `/speckit.analyze` 검색 정확도가 낮아질 수 있습니다.

**Q: 작은 프로젝트에도 이 도구가 필요한가요?**
A: 기능이 2~3개 이상이거나 팀 프로젝트라면 적극 권장합니다. 개인 프로젝트라도 향후 포트폴리오로 공개할 경우, 명세 아티팩트가 있으면 프로젝트의 완성도와 신뢰성이 크게 높아집니다.

---

## 13. 핵심 정리 및 명령어 요약

### 명령어 요약

| 명령어 | SDD 단계 | 역할 |
|---|---|---|
| `/speckit.specify` | 1단계: Specify | 기능 설명 → spec.md 자동 생성 |
| `/speckit.clarify` | 1단계: Specify | 모호한 요구사항을 AI가 질문으로 발굴 |
| `/speckit.checklist [도메인]` | 1단계: Specify / 검증 | 도메인별 명세 완성도 점검 + 구현 후 검증 |
| `/speckit.plan` | 2단계: Plan | spec.md → plan.md 자동 생성 |
| `/speckit.analyze` | 2단계: Plan / 검증 | 문서 간 불일치 탐지 + 명세-코드 드리프트 탐지 |
| `/speckit.tasks` | 3단계: Task | plan.md → tasks.md 자동 생성 |
| `/speckit.implement` | 4단계: Implement | 작업 목록 기반 코드 구현 지원 |

### 체크리스트 도메인 키워드

| 키워드 | 파일 | 주요 점검 영역 | 권장 실행 시점 |
|---|---|---|---|
| `ux` | `checklists/ux.md` | 사용자 흐름, 오류 메시지, 반응형 | clarify 후, plan 전 |
| `security` | `checklists/security.md` | 인증, 인가, 암호화, 입력값 검증 | clarify 후, plan 전 |
| `testing` | `checklists/testing.md` | 시나리오, 경계값, 엣지 케이스 | clarify 후 + implement 후 |
| `performance` | `checklists/performance.md` | 응답 시간, 부하, 캐싱 | clarify 후, plan 전 |
| `api` | `checklists/api.md` | 엔드포인트, 에러 코드, rate limit | clarify 후, plan 전 |
| _(없음)_ | `checklists/requirements.md` | 전반적인 요구사항 완성도 | specify 시 자동 생성 |

### implement 후 테스트 방법 요약

| 방법 | 명령어 | 역할 | 추천 상황 |
|---|---|---|---|
| 방법 1 | `/speckit.checklist testing` | 수용 기준 충족 여부 점검 | 기능 완료 후 빠른 검증 |
| 방법 2 | `/speckit.analyze` 재실행 | 명세-코드 드리프트 탐지 | 변경 반영 후 일관성 확인 |
| 방법 3 | `constitution.md` 테스트 원칙 | implement 시 테스트 코드 자동 생성 | 모든 팀 프로젝트 (프로젝트 시작 시 1회) |

### 아티팩트 요약

| 파일 | 한 줄 요약 |
|---|---|
| `constitution.md` | 프로젝트 전체 기술 원칙 (변하지 않는 규칙) |
| `spec.md` | 기능이 무엇인지, 왜 필요한지 정의 |
| `plan.md` | 어떻게 만들지 기술적으로 설계 |
| `tasks.md` | 누가, 무엇을, 언제 할지 구체적 작업 목록 |
| `checklists/*.md` | 도메인별 명세 품질 검사 결과 및 구현 후 검증 결과 |

### 전체 흐름 한눈에 보기

```
프로젝트 시작
    │
    ├── constitution.md 작성 (팀 기술 원칙 + 테스트 원칙 합의)
    │
    └── 기능별 반복:
        │
        ├── /speckit.specify      →  spec.md 생성
        ├── /speckit.clarify      →  모호함 해소 → spec.md 보완
        ├── /speckit.checklist    →  도메인별 품질 점검 → spec.md 보완
        │   (ux / security / testing / api 등)
        ├── /speckit.plan         →  plan.md 생성
        ├── /speckit.analyze      →  불일치 탐지 → 수정 → 재검사
        ├── /speckit.tasks        →  tasks.md 생성
        ├── /speckit.implement    →  코드 구현
        │
        └── [구현 후 검증]
            ├── /speckit.checklist testing  →  수용 기준 충족 확인
            ├── /speckit.analyze (재실행)   →  명세-코드 드리프트 점검
            └── Git 커밋 (코드 + 테스트 + 체크리스트 함께)
```

---

## 참고 자료

- [GitHub Spec Kit 공식 저장소](https://github.com/github/spec-kit)
- [GitHub Copilot 공식 문서](https://docs.github.com/copilot)
- [GitHub Education (학생 무료 혜택)](https://education.github.com)
- [VS Code 다운로드](https://code.visualstudio.com)
- [GitHub Codespaces 소개](https://github.com/features/codespaces)
- [GitHub Spec Kit Issues #1745 — /speckit.verify 추가 요청](https://github.com/github/spec-kit/issues/1745)

---
