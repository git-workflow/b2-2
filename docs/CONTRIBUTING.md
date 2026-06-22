# Contributing Guide

## 브랜치 전략
- main
- feature/*

GitHub Flow를 선택한 이유:
1. main을 항상 안정적으로 유지하기 위해
2. 작업 단위를 분리하기 위해
3. PR 기반 리뷰를 수행하기 위해

## 브랜치 네이밍 규칙

feature/<이슈번호>-<작업내용>

예시
docs/3-git-study
docs/7-add-git-note
feature/12-login-api
fix/15-validation-error

## 커밋 메시지 규칙

feat: 새로운 기능
fix: 버그 수정
docs: 문서 수정
refactor: 리팩토링
chore: 기타 작업

예시
docs: add contributing guide
feat: add math utility function
fix: resolve login validation error

금지
update
temp
wip
final

## PR 규칙

필수 포함

What
Why
How
Closes #Issue번호

## 코드 리뷰 규칙

LGTM만 작성 금지

반드시
- 질문
- 개선 제안
- 리스크 지적

중 하나 포함

## 충돌 대응

충돌 발생
→ 팀 공유
→ 해결
→ conflict-resolution.md 기록