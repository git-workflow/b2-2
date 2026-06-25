# 실전 Git 협업 워크플로우

> Git 협업의 핵심인 브랜치 전략, PR 기반 코드 리뷰, 충돌 해결, 트러블슈팅을 실습하는 팀 프로젝트입니다.

**저장소**: [https://github.com/git-workflow/b2-2](https://github.com/git-workflow/b2-2)

---

## 팀원

| 이름 | GitHub ID |
|------|-----------|
| 김한규 | @hkk-cody |
| 권창범 | @7eerup |
| 서예영 | @s-yeyeong |

---

## 프로젝트 구조

```text
├── README.md
├── SUBMISSION.md                  # 제출물 인덱스 (팀원별 Issue·PR 링크)
├── docs/
│   ├── CONTRIBUTING.md            # 기여 가이드 (브랜치·커밋·PR·리뷰 규칙)
│   ├── conflict-resolution.md     # 충돌 해결 기록
│   ├── troubleshooting-log.md     # 트러블슈팅 기록
│   ├── rebase-history.md          # [보너스] rebase 히스토리 정리
│   ├── codeowners.md              # [보너스] CODEOWNERS 설명
│   └── learning_notes/            # 팀원별 학습 노트
├── images/                        # 트러블슈팅 스크린샷
└── .github/
    ├── CODEOWNERS                 # [보너스] 리뷰어 자동 지정
    ├── ISSUE_TEMPLATE/            # 이슈 템플릿
    └── pull_request_template.md   # PR 템플릿
```

---

## 주요 문서

| 문서 | 설명 |
|------|------|
| [SUBMISSION.md](SUBMISSION.md) | 제출물 인덱스 — 팀원별 담당 Issue 및 PR 링크 |
| [CONTRIBUTING.md](docs/CONTRIBUTING.md) | 브랜치 전략, 커밋 메시지 컨벤션, PR 및 코드 리뷰 규칙 |
| [conflict-resolution.md](docs/conflict-resolution.md) | 충돌 발생 상황 및 해결 과정 기록 |
| [troubleshooting-log.md](docs/troubleshooting-log.md) | amend, reset, revert, stash 시나리오 해결 기록 |

---

## 학습 노트

| 팀원 | 노트 | 주제 |
|------|------|------|
| 김한규 | [hkk-notes.md](docs/learning_notes/hkk-notes.md) | 실전 Git 협업 워크플로우 — 사전 학습 종합 가이드 |
| 권창범 | [kcb-notes.md](docs/learning_notes/kcb-notes.md) | Git Workflow — GitHub Flow 학습 정리 |
| 서예영 | [syy-notes.md](docs/learning_notes/syy-notes.md) | GitHub 협업 워크플로우 개념 정리 노트 |

---

## 보너스 과제

- **히스토리 정리**: `git rebase -i`를 활용한 커밋 squash/reword → [rebase-history.md](docs/rebase-history.md)
- **리뷰어 자동 지정**: `.github/CODEOWNERS` 설정 → [codeowners.md](docs/codeowners.md)