<!--
  <<< Author notes: Step 4 >>>
  Start this step by acknowledging the previous step.
  Define terms and link to docs.github.com.
-->

## Step 4: 레이블 기반으로 스테이징 환경에 배포하기

_잘하셨습니다, 워크플로우를 사용하여 Azure 환경을 구축했습니다 :dancer:_

이제 적절한 구성과 워크플로우 파일이 준비되었으니, 액션을 테스트해 봅시다! 이 단계에서는 게임에 약간의 변경이 있습니다. 풀 리퀘스트에 적절한 레이블을 추가하면 배포를 확인할 수 있습니다!

1. 이전 `azure-configuration` 브랜치를 만들 때와 같은 방법으로 `main`에서 `staging-test`라는 새 브랜치를 만드세요.
1. `.github/workflows/deploy-staging.yml` 파일을 편집하고 모든 `<username>`을 GitHub 사용자명으로 교체하세요.
1. 해당 변경 사항을 새 `staging-test` 브랜치에 커밋하세요.
1. Pull requests 탭으로 이동하면 `staging-test` 브랜치의 노란색 배너에서 `Compare & pull request`를 클릭하세요. 풀 리퀘스트가 열리면 `Create pull request`를 클릭하세요.

### :keyboard: 활동 1: 풀 리퀘스트에 적절한 레이블 추가하기

1. 이 저장소의 `GITHUB_TOKEN`이 **Workflow permissions**에서 읽기 및 쓰기 권한을 가지고 있는지 확인하세요. [자세히 알아보기](https://docs.github.com/en/actions/security-guides/automatic-token-authentication#modifying-the-permissions-for-the-github_token). 이는 워크플로우가 컨테이너 레지스트리에 이미지를 업로드하는 데 필요합니다.
1. `stage` 레이블을 생성하고 열린 풀 리퀘스트에 적용하세요.
1. GitHub Actions 워크플로우가 실행되어 Azure 환경에 애플리케이션이 배포될 때까지 기다리세요. Actions 탭이나 풀 리퀘스트 병합 박스에서 진행 상황을 확인할 수 있습니다. 배포에 조금 시간이 걸릴 수 있지만 올바른 작업을 하고 있습니다. 배포가 성공하면 각 실행에 녹색 체크 표시가 나타나고 배포 URL을 확인할 수 있습니다. 게임을 플레이해 보세요!
1. 약 20초 기다린 후 이 페이지(지침을 따르고 있는 페이지)를 새로고침하세요. [GitHub Actions](https://docs.github.com/en/actions)가 자동으로 다음 단계로 업데이트됩니다.
