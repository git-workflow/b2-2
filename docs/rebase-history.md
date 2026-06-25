# Git Rebase History Task


## Git Rebase History Note


## 목적

`git rebase -i`를 사용하여 여러 개의 커밋을 하나의 커밋으로 정리하는 방법을 실습하였다.

## 실습 과정

### 1. 첫 번째 커밋 생성

```bash
echo "# Rebase History Bonus Task" > docs/rebase-history.md
git add docs/rebase-history.md
git commit -m "docs: add rebase history note"
```

* `docs/rebase-history.md` 파일을 생성하였다.
* 첫 번째 커밋을 생성하였다.

### 2. 두 번째 커밋 생성

```bash
echo "\n## Practice\nInteractive rebase practice." >> docs/rebase-history.md
git add docs/rebase-history.md
git commit -m "docs: update rebase history note"
```

* 문서 내용을 추가하였다.
* 두 번째 커밋을 생성하였다.

### 3. 정리 전 히스토리 확인

```bash
git log --oneline -3
```

```text
94c8cad docs: add rebase history note
6df80e4 docs: update rebase history note
```

### 4. Interactive Rebase 실행

```bash
git rebase -i HEAD~2
```

편집기에서 다음과 같이 수정하였다.

```text
pick 94c8cad docs: add rebase history note
squash 6df80e4 docs: update rebase history note
```

* 첫 번째 커밋은 유지하였다.
* 두 번째 커밋을 첫 번째 커밋에 합쳤다.

### 5. 정리 후 히스토리 확인

```bash
git log --oneline -3
```

```text
422dacb docs: add rebase history note
```

## 결과

* 두 개의 커밋을 하나의 커밋으로 정리하였다.
* 커밋 히스토리를 간결하게 유지할 수 있었다.

## 배운 점

* `git rebase -i`는 개인 브랜치의 커밋 히스토리를 정리할 때 유용하다.
* `squash`를 사용하면 여러 개의 커밋을 하나로 합칠 수 있다.
* 이미 다른 사람과 공유한 브랜치에서는 `rebase` 사용에 주의해야 한다.
