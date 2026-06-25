# CODEOWNERS Task

## 목적

CODEOWNERS를 사용하여 저장소의 기본 리뷰어를 지정하고, Pull Request 생성 시 책임 리뷰어를 자동으로 지정할 수 있도록 설정하였다.

## 생성 파일

* `.github/CODEOWNERS`

## 설정 내용

```text
# 기본 리뷰어
* @s-yeyeong @7eerup @hkk-cody
```

## 적용 결과

* 저장소의 모든 파일에 대해 기본 리뷰어를 지정하였다.
* PR 생성 시 CODEOWNERS 규칙에 따라 리뷰어 지정이 가능하다.

## 배운 점

* Organization 권한과 CODEOWNERS는 서로 다른 기능이다.
* Organization 권한은 저장소 접근 및 작업 권한을 관리한다.
* CODEOWNERS는 파일 또는 폴더별 책임 리뷰어를 지정하는 기능이다.
* CODEOWNERS를 사용하면 협업 과정에서 리뷰 대상을 명확하게 관리할 수 있다.
