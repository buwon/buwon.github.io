# main 브랜치 보호 설정 가이드

## 개요
이 문서는 `main` 브랜치를 보호하여 저장소 소유자만 커밋 및 푸시할 수 있도록 설정하는 방법을 설명합니다.

## 추가된 파일
- `.github/CODEOWNERS` - @buwon을 모든 파일의 소유자로 정의하여 변경 승인 필요
- `BRANCH_PROTECTION.md` - 영문 상세 설정 가이드

## GitHub 설정에서 브랜치 보호 규칙 구성하기

`main` 브랜치를 완전히 보호하려면 GitHub 설정에서 브랜치 보호 규칙을 구성해야 합니다:

### 브랜치 보호 설정 단계:

1. GitHub에서 저장소로 이동: https://github.com/buwon/buwon.github.io
2. **Settings**(설정) 클릭 (저장소 설정)
3. 왼쪽 사이드바에서 **Branches**(브랜치) 클릭
4. "Branch protection rules"에서 **Add rule**(규칙 추가) 클릭
5. "Branch name pattern"에 `main` 입력
6. 다음 설정 활성화:

   #### 필수 설정:
   - ✅ **Require a pull request before merging**(병합 전 풀 리퀘스트 필요)
     - ✅ Require approvals: 1 (승인 1개 필요)
     - ✅ Require review from Code Owners (코드 소유자의 리뷰 필요)
     - ✅ Dismiss stale pull request approvals when new commits are pushed
   
   - ✅ **Require status checks to pass before merging**(병합 전 상태 검사 통과 필요)
     - ✅ Require branches to be up to date before merging
     - 상태 검사 추가: `build` (Pages 워크플로우에서)
   
   - ✅ **Require conversation resolution before merging**(병합 전 대화 해결 필요)
   
   - ✅ **Do not allow bypassing the above settings**(위 설정 우회 허용 안 함)
   
   - ✅ **Restrict who can push to matching branches**(브랜치에 푸시할 수 있는 사람 제한)
     - `buwon` 추가 (저장소 소유자)
     - 본인만 main에 직접 푸시 가능
   
   - ✅ **Block force pushes**(강제 푸시 차단) - 히스토리 보호를 위해 권장

7. **Create**(생성) 또는 **Save changes**(변경 사항 저장) 클릭

## 결과

이 설정을 적용하면:
- 저장소 소유자를 제외한 모든 사람의 `main` 브랜치 직접 푸시가 차단됩니다
- 모든 변경 사항은 풀 리퀘스트를 통해야 합니다
- 풀 리퀘스트는 @buwon의 승인이 필요합니다 (CODEOWNERS 파일에 정의)
- 병합 전 상태 검사가 통과해야 합니다
- 필요시 @buwon만 제한 우회 가능합니다

## 대안: GitHub Rulesets 사용 (더 나은 제어를 위한 권장 방법)

GitHub은 이제 브랜치 보호 규칙의 더 강력한 대안으로 Rulesets를 제공합니다:

1. **Settings** → **Rules** → **Rulesets**로 이동
2. **New ruleset** → **New branch ruleset** 클릭
3. 이름: "main 브랜치 보호"
4. **Enforcement status**를 **Active**로 설정
5. 대상 추가: **Include default branch** 또는 패턴 `main` 추가
6. 규칙 구성:
   - **Restrict creations** - 브랜치 및 태그 생성 차단
   - **Restrict updates** - 강제 푸시 차단
   - **Restrict deletions** - 브랜치 삭제 차단
   - **Require pull request** - 병합 전 PR 리뷰 필요
   - **Require status checks** - CI 통과 필요
   - **Require code owner review** - CODEOWNERS 적용
   - **Block force pushes** - 우회 목록을 제외한 강제 푸시 방지
7. **Bypass list**: 필요시 우회를 허용할 사용자로 본인(buwon) 추가
8. **Create** 클릭

## 테스트

보호가 제대로 작동하는지 확인:
1. 다른 인증된 GitHub 계정에서 main에 직접 푸시 시도 - 실패해야 함
2. 기능 브랜치에서 풀 리퀘스트 생성 - 작동해야 함
3. 승인 없이 PR 병합 시도 - 차단되어야 함
4. @buwon의 승인 받기 - PR이 병합 가능해져야 함

## 참고사항

- 이 저장소의 CODEOWNERS 파일은 모든 변경 사항에 대해 승인이 필요하도록 합니다
- 브랜치 보호 규칙은 GitHub 저장소 수준에서 구성되며, 코드에서는 구성되지 않습니다
- 이러한 설정은 main 브랜치에 대한 여러 보호 계층을 제공합니다
- 더 자세한 영문 가이드는 `BRANCH_PROTECTION.md` 파일을 참조하세요
