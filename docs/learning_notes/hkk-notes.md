# 실전 Git 협업 워크플로우 — 사전 학습 종합 가이드

> 이 문서는 [git_collaboration_workflow.md](file:///Users/hankkim/Desktop/codyssey/B2-2/git_collaboration_workflow.md) 과제를 수행하기 위해 반드시 알아야 할 Git 핵심 개념과 명령어를 정리한 종합 가이드입니다.

---

## 목차

1. [Git의 내부 구조 이해](#1-git의-내부-구조-이해)
2. [브랜치(Branch) 완전 정복](#2-브랜치branch-완전-정복)
3. [GitHub Flow — 브랜치 전략](#3-github-flow--브랜치-전략)
4. [원격 저장소와 협업 기초](#4-원격-저장소와-협업-기초)
5. [Issue와 PR(Pull Request) 기반 협업](#5-issue와-prpull-request-기반-협업)
6. [코드 리뷰 실전 가이드](#6-코드-리뷰-실전-가이드)
7. [커밋 메시지 컨벤션](#7-커밋-메시지-컨벤션)
8. [충돌(Conflict) 해결 완전 가이드](#8-충돌conflict-해결-완전-가이드)
9. [Git 트러블슈팅 4대 명령어](#9-git-트러블슈팅-4대-명령어)
10. [Branch Protection Rule 설정](#10-branch-protection-rule-설정)
11. [(보너스) `git rebase -i`로 히스토리 정리](#11-보너스-git-rebase--i로-히스토리-정리)
12. [(보너스) `.github/CODEOWNERS` 설정](#12-보너스-githubcodeowners-설정)
13. [과제 수행 체크리스트](#13-과제-수행-체크리스트)

---

## 1. Git의 내부 구조 이해

### 1.1 Git이 데이터를 저장하는 방식

Git은 파일의 "스냅샷(snapshot)"을 저장합니다. 파일이 변경되면 변경된 파일 전체의 스냅샷을 새로 저장하고, 변경되지 않은 파일은 이전 스냅샷에 대한 링크만 저장합니다.

```text
커밋 A ──→ 커밋 B ──→ 커밋 C
(스냅샷1)  (스냅샷2)  (스냅샷3)
```

### 1.2 Git의 세 가지 영역

Git에서 파일은 항상 다음 세 가지 영역 중 하나에 존재합니다.

| 영역 | 설명 | 관련 명령어 |
|---|---|---|
| **Working Directory** (작업 디렉토리) | 실제로 파일을 편집하는 공간 | 일반적인 파일 편집 |
| **Staging Area** (스테이징 영역) | 다음 커밋에 포함시킬 변경사항을 모아두는 공간 | `git add` |
| **Repository** (저장소) | 커밋된 스냅샷이 영구 저장되는 공간 | `git commit` |

```text
Working Directory → (git add) → Staging Area → (git commit) → Repository
```

> [!TIP]
> **왜 Staging Area가 필요한가?**
> 10개 파일을 수정했지만 그 중 3개만 하나의 논리적 단위로 커밋하고 싶을 때, Staging Area가 있으면 원하는 파일만 골라서 커밋할 수 있습니다.

### 1.3 커밋(Commit)이란?

커밋은 특정 시점의 프로젝트 전체 스냅샷입니다. 각 커밋은 다음 정보를 포함합니다:

- **고유 해시값** (예: `a1b2c3d`): SHA-1 해시로 생성된 40자리 문자열 (보통 앞 7자리만 사용)
- **부모 커밋 포인터**: 바로 이전 커밋을 가리킴 (병합 커밋은 부모가 2개)
- **작성자/커미터 정보**: 이름, 이메일, 시간
- **커밋 메시지**: 변경 내용 설명
- **트리 객체**: 해당 시점의 파일/디렉토리 구조 스냅샷

```text
커밋 a1b2c3d
├── parent: 이전 커밋 해시
├── author: "홍길동 <hong@email.com>"
├── message: "feat: 로그인 기능 추가"
└── tree: (파일 구조 스냅샷)
```

---

## 2. 브랜치(Branch) 완전 정복

### 2.1 브랜치란?

> [!IMPORTANT]
> **브랜치 = 특정 커밋을 가리키는 "포인터"**
> 브랜치는 별도의 복사본이 아닙니다. 단지 40바이트짜리 해시값을 담은 파일일 뿐입니다. 이것이 Git 브랜치가 가볍고 빠른 이유입니다.

```text
                    main (포인터)
                      ↓
  A ── B ── C ── D ── E
            ↑
       feature/login (포인터)
            │
            └── F ── G
```

위 그림에서:
- `main`은 커밋 E를 가리키는 포인터
- `feature/login`은 커밋 C에서 분기하여 커밋 G를 가리키는 포인터
- 새로운 커밋을 하면 현재 브랜치 포인터가 새 커밋으로 이동

### 2.2 HEAD란?

`HEAD`는 **"지금 내가 어떤 브랜치(또는 커밋)에서 작업 중인지"**를 가리키는 특수 포인터입니다.

```text
HEAD → main → 커밋 E
```

`git checkout feature/login`을 실행하면:
```text
HEAD → feature/login → 커밋 G
```

### 2.3 왜 브랜치를 나눠서 작업하는가?

| 이유 | 설명 |
|---|---|
| **독립적 작업** | 각자 다른 기능을 동시에 개발해도 서로 영향 없음 |
| **안전한 실험** | 실험 코드가 main을 오염시키지 않음 |
| **코드 리뷰 단위** | 기능 단위로 PR을 생성하여 리뷰 가능 |
| **롤백 용이** | 문제가 있으면 해당 브랜치만 삭제하면 됨 |

### 2.4 브랜치 핵심 명령어

```bash
# 브랜치 목록 보기
git branch              # 로컬 브랜치 목록
git branch -r           # 원격 브랜치 목록
git branch -a           # 모든 브랜치 목록

# 브랜치 생성 및 이동
git branch feature/login          # 브랜치 생성 (이동은 안 함)
git checkout feature/login        # 해당 브랜치로 이동
git checkout -b feature/login     # 생성 + 이동을 한 번에 (★ 가장 많이 사용)
git switch feature/login          # checkout 대신 사용 가능 (Git 2.23+)
git switch -c feature/login       # 생성 + 이동 (Git 2.23+)

# 브랜치 삭제
git branch -d feature/login       # 병합 완료된 브랜치 삭제
git branch -D feature/login       # 강제 삭제 (병합 여부 무관)

# 브랜치 병합
git checkout main                 # main으로 이동 후
git merge feature/login           # feature/login을 main에 병합
```

---

## 3. GitHub Flow — 브랜치 전략

### 3.1 GitHub Flow란?

GitHub Flow는 가장 단순하고 널리 사용되는 브랜치 전략입니다. 규칙이 간단해서 소규모 팀이나 학습 프로젝트에 적합합니다.

**핵심 원칙: 브랜치가 딱 2종류만 존재합니다.**

```text
main ────●────●────●────●────●────●──→ (항상 배포 가능한 상태)
          \        /    \        /
           ●──●──●      ●──●──●
         feature/A     feature/B
```

| 브랜치 | 역할 |
|---|---|
| `main` | 항상 안정적이고 배포 가능한 상태를 유지 |
| `feature/*` | 새로운 기능/수정을 위한 작업 브랜치 (작업 완료 후 삭제) |

### 3.2 GitHub Flow의 작업 흐름 (6단계)

```text
1. main에서 feature 브랜치 생성
2. feature 브랜치에서 작업 및 커밋
3. GitHub에 push
4. Pull Request(PR) 생성
5. 코드 리뷰 → 승인
6. main에 병합 → feature 브랜치 삭제
```

**실제 명령어 흐름:**

```bash
# 1단계: main을 최신 상태로 업데이트 후 feature 브랜치 생성
git checkout main
git pull origin main
git checkout -b feature/kim-add-utils

# 2단계: 작업 후 커밋
# ... 코드 작성 ...
git add src/utils.py
git commit -m "feat: add string utility functions"

# 3단계: 원격에 push
git push origin feature/kim-add-utils

# 4단계: GitHub 웹에서 PR 생성
# 5단계: 팀원이 코드 리뷰 후 승인
# 6단계: GitHub에서 Merge 버튼 클릭 → feature 브랜치 삭제
```

### 3.3 GitHub Flow vs Git Flow 비교

| 항목 | GitHub Flow | Git Flow |
|---|---|---|
| **브랜치 수** | 2종류 (main, feature) | 5종류 (main, develop, feature, release, hotfix) |
| **복잡도** | 단순 | 복잡 |
| **적합한 상황** | CI/CD 기반, 소규모 팀, 웹 서비스 | 명확한 릴리스 주기가 있는 대규모 프로젝트 |
| **학습 난이도** | 낮음 | 높음 |

> [!TIP]
> **과제에서 GitHub Flow를 선택한 이유 (CONTRIBUTING.md에 쓸 수 있는 예시):**
> 1. 팀 규모가 작고 별도의 릴리스 주기가 없어 단순한 전략이 적합하다.
> 2. main 브랜치 하나만 관리하면 되므로 브랜치 혼란을 줄일 수 있다.
> 3. PR 기반 병합으로 자연스럽게 코드 리뷰 문화를 적용할 수 있다.

---

## 4. 원격 저장소와 협업 기초

### 4.1 로컬 vs 원격

```text
[내 컴퓨터 (Local)]              [GitHub (Remote: origin)]
      │                                   │
   git push ──────────────────────────→   │
      │                                   │
   git pull  ←──────────────────────────  │
   git fetch ←──────────────────────────  │
```

| 명령어 | 설명 |
|---|---|
| `git clone <url>` | 원격 저장소를 로컬에 복제 |
| `git remote -v` | 연결된 원격 저장소 확인 |
| `git push origin <branch>` | 로컬 브랜치를 원격에 업로드 |
| `git fetch origin` | 원격의 최신 정보를 가져오기만 함 (병합 X) |
| `git pull origin <branch>` | fetch + merge를 한 번에 수행 |

### 4.2 fork vs clone vs Collaborator

이 과제에서는 **Collaborator 방식** 또는 **Organization 방식**을 사용합니다.

| 방식 | 설명 | 이 과제에서의 역할 |
|---|---|---|
| **Clone** | 원격 저장소를 로컬에 복제 | 모든 팀원이 수행 |
| **Collaborator** | 저장소 소유자가 팀원에게 push 권한을 부여 | 옵션 B |
| **Organization** | 팀 단위 저장소를 생성하여 공동 관리 | 옵션 A (권장) |
| **Fork** | 다른 사람의 저장소를 내 계정에 복사 | 이 과제에서는 사용하지 않음 |

---

## 5. Issue와 PR(Pull Request) 기반 협업

### 5.1 GitHub Issue란?

Issue는 **해야 할 작업(할 일)을 추적하는 티켓**입니다. 버그 리포트, 기능 요청, 작업 단위 등을 Issue로 관리합니다.

**Issue 작성 예시:**
```
제목: feat: 문자열 유틸 함수 추가
본문:
- [ ] capitalize_words() 함수 구현
- [ ] reverse_string() 함수 구현
- [ ] README에 사용법 추가

담당자: @kim
라벨: enhancement
```

### 5.2 Pull Request(PR)란?

PR은 **"내 브랜치의 변경사항을 main에 합쳐주세요"라는 요청**입니다. 단순 병합 요청이 아니라, **코드 리뷰의 단위**이기도 합니다.

**PR의 핵심 기능:**
1. **변경사항 시각화**: 어떤 파일이 어떻게 바뀌었는지 diff로 확인
2. **코드 리뷰**: 팀원이 코드를 검토하고 피드백
3. **토론**: 라인별로 질문/답변 가능
4. **이슈 연동**: PR 병합 시 연결된 이슈 자동 종료

### 5.3 Issue와 PR 연동하기

PR 본문에 아래 키워드를 사용하면 PR이 병합될 때 해당 이슈가 자동으로 닫힙니다:

```markdown
Closes #3
Fixes #5
Resolves #7
```

**전체 흐름 예시:**
```text
1. Issue #3 생성: "feat: 수학 유틸 함수 추가"
2. feature/kim-math-utils 브랜치 생성
3. 작업 후 push
4. PR 생성 (본문에 "Closes #3" 포함)
5. 코드 리뷰 → 승인 → 병합
6. Issue #3이 자동으로 닫힘
```

### 5.4 PR 본문 작성법 (과제 기준)

과제에서 요구하는 PR 본문의 필수 항목:

```markdown
## 연결 이슈
- Closes #3

## 변경 사항 (What)
- src/math_utils.py에 add(), subtract() 함수 추가
- README.md에 math_utils 사용법 섹션 추가

## 변경 이유 (Why)
- 팀 프로젝트의 유틸 함수 모음 중 수학 관련 함수 담당

## 테스트/검증 (How)
- [x] 로컬에서 Python 인터프리터로 각 함수 실행 테스트 완료
- [x] main 브랜치와 충돌 없음 확인
```

---

## 6. 코드 리뷰 실전 가이드

### 6.1 코드 리뷰란?

코드 리뷰는 팀원이 작성한 코드를 다른 팀원이 검토하는 과정입니다. 단순히 "통과/실패"를 판단하는 것이 아니라, **코드 품질 향상과 지식 공유**가 목적입니다.

### 6.2 좋은 리뷰 vs 나쁜 리뷰

**❌ 과제에서 인정되지 않는 리뷰:**
```
LGTM 👍
좋아요!
잘 했어요
```

**✅ 과제에서 인정되는 리뷰 (실질적 코멘트):**
```
[src/utils.py 15번 라인]
이 함수에서 빈 문자열이 입력되면 어떻게 되나요?
빈 문자열 체크를 추가하면 더 안전할 것 같습니다.

예시:
  if not text:
      return ""
```

```
[src/math_utils.py 8번 라인]
divide() 함수에서 0으로 나누는 경우에 대한 처리가 없습니다.
ZeroDivisionError가 발생할 수 있으므로 예외 처리를 추가하면 좋겠습니다.
```

### 6.3 리뷰 상호작용 예시

과제에서는 리뷰어와 작성자 간 **최소 1회 이상의 상호작용**이 기록에 남아야 합니다.

```text
[리뷰어 - 김]    : "이 변수명 `x`가 의미가 불분명합니다. `input_text`처럼 명확한 이름이 좋겠습니다."
[작성자 - 박]    : "좋은 지적입니다. `input_text`로 변경하고 커밋했습니다. (commit: a1b2c3d)"
[리뷰어 - 김]    : Approve ✅
```

### 6.4 GitHub에서 코드 리뷰하는 방법

1. PR 페이지에서 **"Files changed"** 탭 클릭
2. 코드 라인 위에 마우스를 올리면 **"+"** 버튼이 나타남
3. **"+"** 버튼을 클릭하여 해당 라인에 코멘트 작성
4. 모든 리뷰를 작성한 후 **"Review changes"** 버튼 클릭
5. **"Approve"**(승인), **"Request changes"**(수정 요청), **"Comment"**(의견만) 중 선택

---

## 7. 커밋 메시지 컨벤션

### 7.1 Conventional Commits 형식

```text
<type>: <subject>

[optional body]
```

### 7.2 Type 종류

| Type | 설명 | 예시 |
|---|---|---|
| `feat` | 새로운 기능 추가 | `feat: add string reverse function` |
| `fix` | 버그 수정 | `fix: handle empty string in capitalize()` |
| `docs` | 문서 변경 | `docs: add contributing guide` |
| `refactor` | 코드 리팩토링 (기능 변경 없음) | `refactor: rename variable for clarity` |
| `style` | 코드 스타일 변경 (포맷, 세미콜론 등) | `style: fix indentation in utils.py` |
| `test` | 테스트 관련 | `test: add unit test for add function` |
| `chore` | 빌드/설정 관련 변경 | `chore: update .gitignore` |

### 7.3 좋은 커밋 메시지 vs 나쁜 커밋 메시지

| ❌ 나쁜 예시 | ✅ 좋은 예시 | 이유 |
|---|---|---|
| `update` | `docs: update README with usage examples` | 무엇을 업데이트했는지 명확 |
| `fix` | `fix: prevent division by zero in divide()` | 어떤 버그를 고쳤는지 명확 |
| `wip` | `feat: add basic capitalize function (WIP)` | 작업 중이더라도 내용을 설명 |
| `final` | `docs: finalize contributing guide draft` | 무엇을 마무리했는지 명확 |
| `edit file` | `refactor: rename utils to string_utils` | 어떤 파일을 왜 수정했는지 명확 |

---

## 8. 충돌(Conflict) 해결 완전 가이드

### 8.1 충돌은 왜 발생하는가?

두 명 이상이 **같은 파일의 같은 부분**을 서로 다르게 수정한 후 병합하려고 할 때 발생합니다. Git은 어느 쪽을 선택해야 할지 판단할 수 없으므로 사람에게 결정을 맡깁니다.

**충돌 발생 시나리오:**
```text
  A (원본: x = 1)
  ├── B (김: x = 2로 수정) ← feature/kim
  └── C (박: x = 3으로 수정) ← feature/park

김이 먼저 main에 병합 성공
박이 병합하려고 하면 → 충돌 발생!
(Git: "x가 2인지 3인지 모르겠어요. 직접 정해주세요.")
```

### 8.2 충돌 마커 읽는 법

충돌이 발생하면 Git이 파일에 **충돌 마커**를 삽입합니다:

```text
<<<<<<< HEAD
x = 2  ← 현재 브랜치(내 코드)의 내용
=======
x = 3  ← 병합하려는 브랜치(상대방 코드)의 내용
>>>>>>> feature/park
```

| 마커 | 의미 |
|---|---|
| `<<<<<<< HEAD` | 현재 브랜치(HEAD)의 변경 내용 시작 |
| `=======` | 두 변경 사항의 경계 |
| `>>>>>>> feature/park` | 병합 대상 브랜치의 변경 내용 끝 |

### 8.3 충돌 해결 3가지 전략

**전략 1: 내 코드만 유지 (Choose Mine)**
```python
x = 2
```

**전략 2: 상대방 코드만 유지 (Choose Theirs)**
```python
x = 3
```

**전략 3: 양쪽 모두 유지하거나 새롭게 작성 (Keep Both / Refactor)**
```python
x_kim = 2
x_park = 3
```

### 8.4 충돌 해결 실전 절차

```bash
# 1. main을 최신으로 업데이트
git checkout main
git pull origin main

# 2. 내 feature 브랜치로 이동
git checkout feature/park

# 3. main의 변경사항을 내 브랜치에 병합 시도
git merge main
# → 충돌 발생 시 "CONFLICT" 메시지 출력

# 4. 충돌 파일 확인
git status
# → "both modified: src/utils.py" 등의 메시지

# 5. 충돌 파일 열어서 마커 확인 및 수정
# → <<<<<<< / ======= / >>>>>>> 마커를 모두 제거하고 원하는 코드로 정리

# 6. 해결 완료 후 스테이징 및 커밋
git add src/utils.py
git commit -m "fix: resolve merge conflict in utils.py"

# 7. 원격에 push
git push origin feature/park
```

### 8.5 과제에서 요구하는 "비자명 충돌" 만들기

**방법 1: 같은 hunk(인접 라인) 수정**
```bash
# 팀원 A: feature/a 브랜치에서 utils.py의 5~7번 줄 수정
# 팀원 B: feature/b 브랜치에서 utils.py의 5~7번 줄을 다르게 수정
# 팀원 A가 먼저 PR 병합 → 팀원 B가 PR 올리면 충돌 발생
```

**방법 2: 파일 이동/삭제 + 내용 수정**
```bash
# 팀원 A: feature/a에서 utils.py를 string_utils.py로 이름 변경
git mv src/utils.py src/string_utils.py

# 팀원 B: feature/b에서 utils.py의 내용 수정
# (A의 이름 변경 사실을 모른 채 원래 파일을 편집)

# A가 먼저 병합 → B가 병합 시도 시 충돌/이슈 발생
```

> [!WARNING]
> 충돌 해결 후 반드시 `docs/conflict-resolution.md`에 상황, 마커 내용, 해결 과정, 결과, 배운 점을 기록해야 합니다.

---

## 9. Git 트러블슈팅 4대 명령어

### 9.1 `git commit --amend` — 최근 커밋 수정

**상황:** 커밋 메시지에 오타가 있거나, 파일을 빠뜨렸을 때

```bash
# 커밋 메시지만 수정
git commit --amend -m "docs: add contributing guide (오타 수정)"

# 파일을 빠뜨린 경우 (파일 추가 후 amend)
git add forgotten_file.py
git commit --amend --no-edit    # 메시지는 유지하고 파일만 추가
```

> [!CAUTION]
> **이미 push한 커밋에 amend를 사용하면 안 됩니다!** amend는 커밋 해시를 변경하므로, 원격에 push된 커밋을 amend하면 팀원의 히스토리와 충돌합니다. push 전에만 사용하세요.

```text
[안전] 로컬에서만 작업 중 → amend 사용 가능
[위험] 이미 push한 커밋 → amend 사용 금지 → revert 사용
```

### 9.2 `git reset --soft HEAD~1` — 로컬 커밋 취소

**상황:** 방금 한 커밋을 취소하고 싶지만, 변경한 코드는 유지하고 싶을 때

```bash
git reset --soft HEAD~1
```

**실행 결과:**
- 가장 최근 커밋 1개가 사라짐
- 해당 커밋의 변경사항은 **Staging Area에 그대로 남아 있음**
- 코드를 수정한 후 다시 커밋 가능

**reset의 3가지 모드 비교:**

| 모드 | 커밋 | Staging Area | Working Directory | 용도 |
|---|---|---|---|---|
| `--soft` | ❌ 취소 | ✅ 유지 | ✅ 유지 | 커밋만 취소, 코드 수정 후 다시 커밋 |
| `--mixed` (기본값) | ❌ 취소 | ❌ 취소 | ✅ 유지 | 커밋 + 스테이징 취소, 다시 add부터 |
| `--hard` | ❌ 취소 | ❌ 취소 | ❌ 삭제 | 모두 삭제 (⚠️ 복구 불가) |

> [!CAUTION]
> **`git reset`은 push 전에만 사용해야 합니다!** 이미 push한 커밋을 reset하면 원격 히스토리와 달라져서 문제가 생깁니다. push 후에는 `git revert`를 사용하세요.

### 9.3 `git revert` — 원격에 push된 커밋 취소

**상황:** 이미 원격에 push한 커밋을 되돌려야 할 때

```bash
# 특정 커밋을 되돌리는 새로운 커밋 생성
git revert <커밋해시>

# 예시
git revert a1b2c3d
# → 에디터가 열리면 revert 커밋 메시지 확인 후 저장

git push origin main
```

**revert vs reset 핵심 차이:**

```text
[reset]  A ── B ── C  →  A ── B     (C를 삭제 — 히스토리 변조)
[revert] A ── B ── C  →  A ── B ── C ── C'  (C를 되돌리는 새 커밋 C' 추가 — 히스토리 보존)
```

| 항목 | `reset` | `revert` |
|---|---|---|
| **히스토리** | 삭제 (변조됨) | 보존 (새 커밋 추가) |
| **push 후 사용** | ❌ 위험 (금지) | ✅ 안전 |
| **팀 협업** | 다른 팀원에게 영향 줌 | 영향 없음 |
| **사용 시점** | 로컬에서만 | 언제든지 가능 |

### 9.4 `git stash` / `git stash pop` — 작업 임시 보관

**상황:** 작업 중인데 급히 다른 브랜치로 전환해야 할 때

```bash
# 현재 작업 중인 변경사항 임시 보관
git stash
# → Working Directory가 깨끗해짐

# 다른 브랜치로 전환하여 긴급 작업 수행
git checkout main
# ... 긴급 작업 ...
git checkout feature/my-work

# 보관해둔 작업 복원
git stash pop
# → 이전 작업 내용이 다시 Working Directory에 나타남
```

**stash 관련 명령어:**

```bash
git stash              # 변경사항 임시 보관
git stash list         # 보관된 작업 목록 확인
git stash pop          # 가장 최근 보관 작업 복원 + 목록에서 삭제
git stash apply        # 복원하되 목록에서 삭제하지 않음
git stash drop         # 보관 작업 삭제
git stash clear        # 모든 보관 작업 삭제
```

> [!TIP]
> `stash pop` vs `stash apply`:
> - `pop` = 복원 + 목록에서 제거 (일회성 복원)
> - `apply` = 복원만 (목록에 계속 유지, 여러 브랜치에 같은 변경 적용할 때 유용)

### 9.5 4대 명령어 요약 비교

| 상황 | 사용할 명령어 | push 전/후 |
|---|---|---|
| 커밋 메시지 오타 수정 | `git commit --amend` | push **전**에만 |
| 방금 한 커밋 취소 (코드 유지) | `git reset --soft HEAD~1` | push **전**에만 |
| 이미 push한 커밋 되돌리기 | `git revert <hash>` | push **후**에도 안전 |
| 작업 중 긴급 브랜치 전환 | `git stash` / `git stash pop` | 언제든 |

---

## 10. Branch Protection Rule 설정

### 10.1 설정 방법 (GitHub 웹)

1. 저장소 → **Settings** → **Branches**
2. **"Add branch protection rule"** 클릭
3. **Branch name pattern**: `main` 입력
4. 아래 옵션 체크:
   - ✅ **Require a pull request before merging** (PR을 통한 병합만 허용)
     - ✅ **Require approvals**: 1 (최소 1명 승인)
   - ✅ **Do not allow bypassing the above settings** (관리자도 예외 없음 — 선택)
5. **"Create"** 버튼 클릭

### 10.2 설정 후 동작

```text
[직접 push 시도]
$ git push origin main
→ ❌ 거부됨 (remote: error: protected branch)

[올바른 흐름]
feature 브랜치에서 작업 → push → PR 생성 → 리뷰 승인 → Merge
```

---

> [!NOTE]
> **이 가이드를 활용하는 팁:**
> - 과제 수행 전에 한 번 전체를 읽어보세요.
> - 실제 작업 중에는 해당 섹션을 참고하며 진행하세요.
> - 특히 **충돌 해결(8장)**과 **트러블슈팅 4대 명령어(9장)**는 실습 직전에 한 번 더 정독하는 것을 권장합니다.
