<header>

<!--
  <<< Author notes: Course header >>>
  Include a 1280x640 image, course title in sentence case, and a concise description in emphasis.
  In your repository settings: enable template repository, add your 1280x640 social image, auto delete head branches.
  Add your open source license, GitHub uses MIT license.
-->

# Azure에 배포하기

_GitHub Actions와 Microsoft Azure를 사용하여 두 가지 배포 워크플로우를 생성합니다._

</header>

<!--
  <<< Author notes: Step 1 >>>
  Choose 3-5 steps for your course.
  The first step is always the hardest, so pick something easy!
  Link to docs.github.com for further explanations.
  Encourage users to open new tabs for steps!
-->

## Step 1: 레이블 기반으로 작업 트리거하기

_실습에 오신 것을 환영합니다 :tada:_

![Screen Shot 2022-06-07 at 4 01 43 PM](https://user-images.githubusercontent.com/6351798/172490466-00f27580-8906-471f-ae83-ef3b6370df7d.png)

"지속적으로" 전달하는 데는 많은 요소가 필요합니다. 문화와 행동부터 특정 자동화까지 다양합니다. 이 실습에서는 자동화의 배포 부분에 집중하겠습니다.

GitHub Actions 워크플로우에서 `on` 단계는 워크플로우가 실행되는 원인을 정의합니다. 이 경우, 풀 리퀘스트에 특정 레이블이 적용될 때 워크플로우가 다른 작업을 실행하도록 하려 합니다.

레이블을 여러 작업의 트리거로 사용할 것입니다:

- "spin up environment" 레이블을 풀 리퀘스트에 적용하면, Azure 환경에 리소스를 설정하고 싶다는 것을 GitHub Actions에 알립니다.
- "stage" 레이블을 풀 리퀘스트에 적용하면, 스테이징 환경에 애플리케이션을 배포하고 싶다는 표시입니다.
- "destroy environment" 레이블을 풀 리퀘스트에 적용하면, Azure 계정에서 실행 중인 리소스를 정리합니다.

### :keyboard: 활동 1: `GITHUB_TOKEN` 권한 구성하기

각 워크플로우 실행 시작 시, GitHub는 워크플로우에서 사용할 고유한 `GITHUB_TOKEN` 시크릿을 자동으로 생성합니다. 이 토큰이 이 실습에 필요한 권한을 갖고 있는지 확인해야 합니다.

1. 새 브라우저 탭을 열고, 이 탭에서 지침을 읽으면서 두 번째 탭에서 단계를 수행하세요.
1. Settings > Actions > General로 이동하세요. **Workflow permissions** 아래에서 `GITHUB_TOKEN`에 **Read and write permissions**이 활성화되어 있는지 확인하세요. 워크플로우가 컨테이너 레지스트리에 이미지를 업로드하려면 이 설정이 필요합니다.

### :keyboard: 활동 2: 레이블 기반 트리거 구성하기

지금은 스테이징에 집중하겠습니다. 환경 구축과 정리는 이후 단계에서 다룹니다.

1. **Actions** 탭으로 이동하세요.
1. **New workflow**를 클릭하세요.
1. "simple workflow"를 검색하고 **Configure**를 클릭하세요.
1. 워크플로우 이름을 `deploy-staging.yml`로 지정하세요.
1. 이 파일의 내용을 편집하여 모든 트리거와 작업을 제거하세요.
1. 파일 내용을 편집하여 **stage**라는 레이블이 있을 때 `build` 작업을 필터링하는 조건을 추가하세요. 결과 파일은 다음과 같아야 합니다:
   ```yaml
   name: Stage the app

   on:
     pull_request:
       types: [labeled]

   jobs:
     build:
       runs-on: ubuntu-latest

       if: contains(github.event.pull_request.labels.*.name, 'stage')
   ```
1. **Start commit**을 클릭하고 `staging-workflow`라는 새 브랜치를 만드세요.
1. **Propose changes**를 클릭하세요.
1. **Create pull request**를 클릭하세요.
1. 약 20초 기다린 후 이 페이지(지침을 따르고 있는 페이지)를 새로고침하세요. [GitHub Actions](https://docs.github.com/en/actions)가 자동으로 다음 단계로 업데이트됩니다.

<footer>

<!--
  <<< Author notes: Footer >>>
  Add a link to get support, GitHub status page, code of conduct, license link.
-->

---

> **참고**: 이 실습은 [skills/deploy-to-azure](https://github.com/skills/deploy-to-azure)를 기반으로 한글화하고, [🏆 GitHub Skills Workshop Dashboard](https://github-skills.studydev.com)와 연계되어 있습니다.

도움 받기: [토론 게시판에 글 올리기](https://github.com/orgs/skills/discussions/categories/deploy-to-azure) &bull; [GitHub 상태 페이지 확인](https://www.githubstatus.com/)

&copy; 2023 GitHub &bull; [Code of Conduct](https://www.contributor-covenant.org/version/2/1/code_of_conduct/code_of_conduct.md) &bull; [MIT License](https://gh.io/mit)

</footer>
