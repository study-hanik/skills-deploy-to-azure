<!--
  <<< Author notes: Step 3 >>>
  Start this step by acknowledging the previous step.
  Define terms and link to docs.github.com.
-->

## Step 3: 레이블 기반으로 환경 구축하기

_잘하셨습니다! :heart:_

GitHub Actions는 클라우드에 구애받지 않으므로 어떤 클라우드든 작동합니다. 이 실습에서는 Azure에 배포하는 방법을 보여드리겠습니다.

**_Azure 리소스_란?** Azure에서 리소스는 Azure가 관리하는 엔티티입니다. 이 실습에서는 다음 Azure 리소스를 사용합니다:

- [웹 앱](https://docs.microsoft.com/en-us/azure/app-service/overview)은 Azure에 애플리케이션을 배포하는 방법입니다.
- [리소스 그룹](https://docs.microsoft.com/en-us/azure/azure-resource-manager/management/overview#resource-groups)은 웹 앱과 가상 머신(VM) 같은 리소스의 모음입니다.
- [App Service 플랜](https://docs.microsoft.com/en-us/azure/app-service/overview-hosting-plans)은 웹 앱을 실행하고 요금을 관리합니다(우리 앱은 무료로 실행됩니다).

GitHub Actions의 힘으로 워크플로우 파일을 통해 이러한 리소스를 생성, 구성, 정리할 수 있습니다.

### :keyboard: 활동 1: 개인 접근 토큰(PAT) 설정하기

개인 접근 토큰(PAT)은 GitHub 인증 시 비밀번호 대신 사용하는 방법입니다. 워크플로우가 새로 빌드된 이미지를 레지스트리에 푸시한 후 웹 앱이 컨테이너 이미지를 풀할 수 있도록 PAT를 사용합니다.

1. 새 브라우저 탭을 열고, 이 탭에서 지침을 읽으면서 두 번째 탭에서 단계를 수행하세요.
2. `repo`와 `write:packages` 범위로 개인 접근 토큰을 생성하세요. 자세한 내용은 ["개인 접근 토큰 만들기"](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token)를 참조하세요.
3. 토큰을 생성하면 워크플로우 내에서 사용할 수 있도록 시크릿에 저장해야 합니다. `CR_PAT`라는 이름의 새 저장소 시크릿을 만들고 PAT 토큰을 값으로 붙여넣으세요.
4. 이것으로 워크플로우 설정을 진행할 수 있습니다.

**Azure 환경 구성하기**

Azure 환경에 성공적으로 배포하려면:

1. 저장소 페이지의 **Code** 탭 왼쪽 상단의 브랜치 드롭다운을 클릭하여 `azure-configuration`이라는 새 브랜치를 만드세요.
2. 새 `azure-configuration` 브랜치에서 `.github/workflows` 디렉토리로 이동하여 **Add file**을 클릭하고 `spinup-destroy.yml`이라는 새 파일을 만드세요. 다음 내용을 복사하여 붙여넣으세요:
    ```yaml
    name: Configure Azure environment

    on:
      pull_request:
        types: [labeled]

    env:
      IMAGE_REGISTRY_URL: ghcr.io
      AZURE_RESOURCE_GROUP: cd-with-actions
      AZURE_APP_PLAN: actions-ttt-deployment
      AZURE_LOCATION: '"East US"'
      ###############################################
      ### <username>을 GitHub 사용자명으로 교체하세요 ###
      ###############################################
      AZURE_WEBAPP_NAME: <username>-ttt-app

    jobs:
      setup-up-azure-resources:
        runs-on: ubuntu-latest
        if: contains(github.event.pull_request.labels.*.name, 'spin up environment')
        steps:
          - name: Checkout repository
            uses: actions/checkout@v4

          - name: Azure login
            uses: azure/login@v2
            with:
              creds: ${{ secrets.AZURE_CREDENTIALS }}

          - name: Create Azure resource group
            if: success()
            run: |
              az group create --location ${{env.AZURE_LOCATION}} --name ${{env.AZURE_RESOURCE_GROUP}} --subscription ${{secrets.AZURE_SUBSCRIPTION_ID}}

          - name: Create Azure app service plan
            if: success()
            run: |
              az appservice plan create --resource-group ${{env.AZURE_RESOURCE_GROUP}} --name ${{env.AZURE_APP_PLAN}} --is-linux --sku F1 --subscription ${{secrets.AZURE_SUBSCRIPTION_ID}}

          - name: Create webapp resource
            if: success()
            run: |
              az webapp create --resource-group ${{ env.AZURE_RESOURCE_GROUP }} --plan ${{ env.AZURE_APP_PLAN }} --name ${{ env.AZURE_WEBAPP_NAME }}  --deployment-container-image-name nginx --subscription ${{secrets.AZURE_SUBSCRIPTION_ID}}

          - name: Configure webapp to use GHCR
            if: success()
            run: |
              az webapp config container set --docker-custom-image-name nginx --docker-registry-server-password ${{secrets.CR_PAT}} --docker-registry-server-url https://${{env.IMAGE_REGISTRY_URL}} --docker-registry-server-user ${{github.actor}} --name ${{ env.AZURE_WEBAPP_NAME }} --resource-group ${{ env.AZURE_RESOURCE_GROUP }} --subscription ${{secrets.AZURE_SUBSCRIPTION_ID}}

      destroy-azure-resources:
        runs-on: ubuntu-latest

        if: contains(github.event.pull_request.labels.*.name, 'destroy environment')

        steps:
          - name: Checkout repository
            uses: actions/checkout@v4

          - name: Azure login
            uses: azure/login@v2
            with:
              creds: ${{ secrets.AZURE_CREDENTIALS }}

          - name: Destroy Azure environment
            if: success()
            run: |
              az group delete --name ${{env.AZURE_RESOURCE_GROUP}} --subscription ${{secrets.AZURE_SUBSCRIPTION_ID}} --yes
    ```
1. **Commit changes...**를 클릭하고 `azure-configuration 브랜치에 직접 커밋`을 선택한 후 **Commit changes**를 클릭하세요.
1. 저장소의 Pull requests 탭으로 이동하세요.
1. `azure-configuration` 브랜치가 있는 노란색 배너에서 **Compare & pull request**를 클릭하세요.
1. Pull request 제목을 `Added spinup-destroy.yml workflow`로 설정하고 `Create pull request`를 클릭하세요.

아래에서 핵심 기능을 다루고, 풀 리퀘스트에 레이블을 적용하여 워크플로우를 사용해 보겠습니다.

이 새 워크플로우에는 두 가지 작업이 있습니다:

1. **Azure 리소스 설정**은 풀 리퀘스트에 "spin up environment"라는 레이블이 포함된 경우 실행됩니다.
2. **Azure 리소스 정리**는 풀 리퀘스트에 "destroy environment"라는 레이블이 포함된 경우 실행됩니다.

각 작업 외에도 몇 가지 전역 환경 변수가 있습니다:

- `AZURE_RESOURCE_GROUP`, `AZURE_APP_PLAN`, `AZURE_WEBAPP_NAME`은 리소스 그룹, 앱 서비스 플랜, 웹 앱의 이름으로, 여러 단계와 워크플로우에서 참조합니다.
- `AZURE_LOCATION`은 앱이 최종적으로 배포될 데이터 센터의 [지역](https://azure.microsoft.com/en-us/global-infrastructure/regions/)을 지정합니다.

**Azure 리소스 설정**

첫 번째 작업은 다음과 같이 Azure 리소스를 설정합니다:

1. [`azure/login`](https://github.com/Azure/login) 액션으로 Azure 계정에 로그인합니다. 이전에 만든 `AZURE_CREDENTIALS` 시크릿이 인증에 사용됩니다.
1. GitHub 호스팅 러너에 [사전 설치](https://help.github.com/en/actions/reference/software-installed-on-github-hosted-runners)된 Azure CLI에서 [`az group create`](https://docs.microsoft.com/en-us/cli/azure/group?view=azure-cli-latest#az-group-create)를 실행하여 [Azure 리소스 그룹](https://docs.microsoft.com/en-us/azure/azure-resource-manager/management/overview#resource-groups)을 생성합니다.
1. Azure CLI에서 [`az appservice plan create`](https://docs.microsoft.com/en-us/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create)를 실행하여 [App Service 플랜](https://docs.microsoft.com/en-us/azure/app-service/overview-hosting-plans)을 생성합니다.
1. Azure CLI에서 [`az webapp create`](https://docs.microsoft.com/en-us/cli/azure/webapp?view=azure-cli-latest#az-webapp-create)를 실행하여 [웹 앱](https://docs.microsoft.com/en-us/azure/app-service/overview)을 생성합니다.
1. [`az webapp config`](https://docs.microsoft.com/en-us/cli/azure/webapp/config?view=azure-cli-latest)를 사용하여 새로 만든 웹 앱이 [GitHub Packages](https://help.github.com/en/packages/publishing-and-managing-packages/about-github-packages)를 사용하도록 구성합니다. Azure는 자체 [Azure Container Registry](https://docs.microsoft.com/en-us/azure/container-registry/), [DockerHub](https://docs.docker.com/docker-hub/) 또는 커스텀(프라이빗) 레지스트리를 사용하도록 구성할 수 있습니다. 이 경우 GitHub Packages를 커스텀 레지스트리로 구성합니다.

**Azure 리소스 정리**

두 번째 작업은 무료 시간을 소진하거나 요금이 발생하지 않도록 Azure 리소스를 정리합니다:

1. [`azure/login`](https://github.com/Azure/login) 액션으로 Azure 계정에 로그인합니다. 이전에 만든 `AZURE_CREDENTIALS` 시크릿이 인증에 사용됩니다.
1. Azure CLI에서 [`az group delete`](https://docs.microsoft.com/en-us/cli/azure/group?view=azure-cli-latest#az-group-delete)를 사용하여 이전에 만든 리소스 그룹을 삭제합니다.

### :keyboard: 활동 2: 레이블을 적용하여 리소스 생성하기

1. 열린 풀 리퀘스트에서 `spinup-destroy.yml` 파일을 편집하고 `<username>` 자리 표시자를 모두 GitHub 사용자명으로 교체하세요. 이 변경 사항을 `azure-configuration` 브랜치에 직접 커밋하세요.
1. 풀 리퀘스트로 돌아가서 `spin up environment` 레이블을 생성하고 열린 풀 리퀘스트에 적용하세요.
1. GitHub Actions 워크플로우가 실행되어 Azure 환경을 구축할 때까지 기다리세요. Actions 탭이나 풀 리퀘스트 병합 박스에서 진행 상황을 확인할 수 있습니다.
1. 약 20초 기다린 후 이 페이지(지침을 따르고 있는 페이지)를 새로고침하세요. [GitHub Actions](https://docs.github.com/en/actions)가 자동으로 다음 단계로 업데이트됩니다.
