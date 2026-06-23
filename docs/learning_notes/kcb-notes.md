# Git Workflow

## 1. GitHub Flow 학습 정리

GitHub Flow는 `main` 브랜치를 항상 안정적인 상태로 유지하고, 새로운 작업은 별도 브랜치에서 진행한 뒤 Pull Request를 통해 병합하는 협업 방식이다.

### 브랜치 구조

* `main`: 항상 안정적인 상태를 유지하는 브랜치
* `feature/*`: 기능, 문서, 수정 작업을 진행하는 브랜치
* `docs/*`: 문서 작업을 진행하는 브랜치

### GitHub Flow를 사용하는 이유

1. main 브랜치에 직접 수정하지 않아 안정성을 유지할 수 있다.
2. 작업 단위별로 브랜치를 분리해 충돌과 실수를 줄일 수 있다.
3. Pull Request를 통해 팀원의 리뷰를 받고 병합할 수 있다.

---

## 2. Issue 기반 작업

작업을 시작하기 전에 Issue를 생성하면 작업 목적과 범위를 명확히 할 수 있다.

### 진행 흐름

1. Issue 생성
2. 작업 브랜치 생성
3. 파일 수정
4. Commit
5. Push
6. Pull Request 생성
7. PR 본문에 `Closes #이슈번호` 작성
8. 리뷰 후 main 병합

---

## 3. Pull Request 학습 정리

Pull Request는 단순히 코드를 병합하는 기능이 아니라, 팀원이 변경 내용을 확인하고 리뷰할 수 있게 하는 협업 절차이다.

### PR 본문에 포함할 내용

* What: 무엇을 변경했는가
* Why: 왜 변경했는가
* How: 어떻게 확인했는가
* Closes #이슈번호

---

## 4. Commit Message Convention

커밋 메시지는 변경 내용을 빠르게 파악할 수 있도록 작성해야 한다.

### 사용 규칙

* `feat:` 새로운 기능 추가
* `fix:` 버그 수정
* `docs:` 문서 수정
* `refactor:` 코드 구조 개선
* `chore:` 기타 설정 또는 보조 작업

### 좋은 예시

```bash
docs: add personal learning notes
feat: add string utility function
fix: correct branch naming rule
```

### 좋지 않은 예시

```bash
update
fix
wip
final
temp
```

단독으로 `fix`만 작성하면 무엇을 수정했는지 알 수 없기 때문에 사용하지 않는다.

---

## 5. 충돌 해결 학습 정리

Git 충돌은 같은 파일의 같은 부분을 서로 다르게 수정했을 때 발생한다.

### 충돌 마커 의미

```txt
<<<<<<< HEAD
현재 브랜치 내용
=======
병합하려는 브랜치 내용
>>>>>>> feature/example
```

### 해결 흐름

1. 충돌 파일 확인
2. 충돌 마커 확인
3. 유지할 내용 선택 또는 두 내용 모두 반영
4. 충돌 마커 제거
5. 저장 후 commit
6. `docs/conflict-resolution.md`에 기록

---

## 6. Git Troubleshooting 학습 정리

### git commit --amend

최근 커밋 메시지나 내용을 수정할 때 사용한다.

```bash
git commit --amend -m "docs: update learning notes"
```

### git reset --soft HEAD~1

최근 커밋을 취소하지만 변경 파일은 유지한다.

```bash
git reset --soft HEAD~1
```

### git revert

이미 원격 저장소에 push된 커밋을 안전하게 되돌릴 때 사용한다.

```bash
git revert <commit_hash>
```

### git stash / git stash pop

작업 중인 내용을 잠시 보관하고 다른 브랜치로 이동할 때 사용한다.

```bash
git stash
git stash pop
```

---

## 7. 배운 점

이번 Git 협업 실습을 통해 브랜치를 나누는 이유, PR을 통한 코드 리뷰의 필요성, 충돌이 발생하는 원리와 해결 방법을 이해했다.

특히 main 브랜치에 직접 push하지 않고, Issue와 PR을 연결해 작업 흐름을 기록하는 것이 협업에서 중요하다는 점을 배웠다.
