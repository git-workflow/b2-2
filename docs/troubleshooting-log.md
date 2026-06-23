# Troubleshooting Log

## 시나리오: `git stash`, `git stash pop`

### 상황
- branch가 main이 아닌 다른 branch에서 분기해서 생긴 문제
- git reflog 확인 결과, 해당 브랜치가 main이 아닌 다른 브랜치(docs/4-add-new-docs)에서 잘못 분기되었음을 파악

### 시도한 명령/절차
- 작업하던 내용을 `git stash`로 임시 저장
- 새로 생성한 파일이 있는 경우엔 `-u`옵션을 사용해야함
- `git status`: 현재 문제 상황 파악.

- `git reflog`: 이전 기록을 확인하여 브랜치 분기가 잘못 나누어져 있는 원인 확인.

- `git stash`: 작업 내용을 임시 저장하려 했으나, 새로 생성된 문서(Untracked)만 있어서 아무것도 저장되지 않음.

- `git stash -u`: 새로 생성한 문서 파일들까지 전부 포함하기 위해 -u 옵션을 사용하여 성공적으로 임시 보관.

- `git switch main`: 올바른 분기의 기준이 되는 메인 브랜치로 이동.

- `git switch [새 브랜치명]`: 메인에서 제대로 파생된 새로운 브랜치(docs/14-stash-troubleshooting)로 전환.

- `git stash pop`: 새 브랜치에서 임시 보관해 두었던 작업을 다시 꺼내어 적용 시도.


### 결과
- 무엇이 어떻게 해결됐는지
- 주의할 점(특히 원격 히스토리/협업 영향)

- 에러 발생 및 원인: git stash pop 실행 시 error: could not restore untracked files from stash 에러 발생. 가져온 Untracked 파일과 대상 브랜치에 이미 존재하는 파일 이름이 겹쳐서 Git이 안전을 위해 덮어쓰기를 차단함.

- 해결 과정: 에러가 발생하여 stash 내역은 목록에서 삭제되지 않고 안전하게 보존됨(The stash entry is kept...). 충돌이 발생한 파일을 수동으로 정리한 뒤 다시 git add하여 문제 해결.

- 주의할 점: stash -u로 새로운 파일까지 한 번에 묶어서 옮길 때, 타겟 브랜치에 동일한 경로/이름의 파일이 있으면 충돌 에러가 발생할 수 있으므로 주의 요망.

### 왜 이 방법을 선택했는가(Why)
- reset 대신 revert를 선택한 이유 등

- 의미 없는 커밋 방지: 작업이 마무리되지 않은 상태에서 억지로 WIP(Work In Progress) 등 무의미한 커밋을 히스토리에 남기지 않기 위함.

- 작업물 유실 방지: 변경 사항을 그대로 둔 채 브랜치를 이동(switch)하면 파일 구조의 차이로 인해 작업물이 꼬이거나 날아갈 위험이 큼.

- 결론: 미완성 작업을 가장 안전하게 캡슐화하여 보관했다가 꺼낼 수 있는 stash 방식이 최적이라고 판단. 특히 새로 생성된 문서를 함께 옮기기 위해 -u 옵션 사용이 필수적이었음.


