<!--
  <<< Author notes: Step 2 >>>
  Start this step by acknowledging the previous step.
  Define terms and link to docs.github.com.
-->

## Step 2: Azure 환경 설정하기

_시작이 좋습니다 :gear:_

### 레이블 기반 작업 트리거 잘하셨습니다

이 워크플로우의 단계를 자세히 다루지는 않겠지만, 사용하는 액션에 익숙해지는 것이 좋습니다:

- [`actions/checkout`](https://github.com/actions/checkout)
- [`actions/upload-artifact`](https://github.com/actions/upload-artifact)
- [`actions/download-artifact`](https://github.com/actions/download-artifact)
- [`docker/login-action`](https://github.com/docker/login-action)
- [`docker/build-push-action`](https://github.com/docker/build-push-action)
- [`azure/login`](https://github.com/Azure/login)
- [`azure/webapps-deploy`](https://github.com/Azure/webapps-deploy)

### :keyboard: 활동 1: 자격 증명을 GitHub 시크릿에 저장하고 워크플로우 설정 완료하기

1.  새 탭에서 Azure 계정이 없다면 [Azure 계정을 생성](https://azure.microsoft.com/en-us/free/)하세요. 회사를 통해 Azure 계정을 만든 경우 필요한 리소스에 접근하는 데 문제가 생길 수 있습니다 -- 개인용으로 새 계정을 만드는 것을 권장합니다.
    > **참고**: Azure 계정을 만들려면 신용카드가 필요할 수 있습니다. 학생이라면 Azure 접근을 위해 [Student Developer Pack](https://education.github.com/pack)을 활용할 수도 있습니다. Azure 계정 없이 실습을 계속하려면 Skills가 계속 응답하지만 배포는 작동하지 않습니다.
1.  Azure Portal에서 [새 구독을 생성](https://docs.microsoft.com/en-us/azure/cost-management-billing/manage/create-subscription)하세요.
    > **참고**: 구독은 "종량제(Pay as you go)"로 구성해야 하며 결제 정보를 입력해야 합니다. 이 실습은 무료 플랜에서 몇 분만 사용하지만 Azure는 결제 정보를 요구합니다.
1.  머신에 [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest)를 설치하세요.
1.  터미널에서 다음을 실행하세요:
    ```shell
    az login
    ```
1.  대화형 인증 프롬프트에서 방금 선택한 구독을 선택하세요. 구독 ID 값을 안전한 곳에 복사하세요. 이것을 `AZURE_SUBSCRIPTION_ID`라고 부르겠습니다. 예시:
    ```shell
    No     Subscription name    Subscription ID                       Tenant
    -----  -------------------  ------------------------------------  -----------------
    [1] *  some-subscription    f****a09-****-4d1c-98**-f**********c  Default Directory
    ```
1.  터미널에서 아래 명령을 실행하세요.

    ```shell
    az ad sp create-for-rbac --name "GitHub-Actions" --role contributor \
     --scopes /subscriptions/{subscription-id} \
     --sdk-auth

        # {subscription-id}를 AZURE_SUBSCRIPTION_ID에 저장한 것과 동일한 id로 교체하세요.
    ```

    > **참고**: `\` 문자는 Unix 기반 시스템에서 줄 바꿈으로 사용됩니다. Windows 기반 시스템에서는 `\` 문자가 이 명령을 실패하게 합니다. Windows를 사용하는 경우 이 명령을 한 줄에 입력하세요.

1.  명령 응답의 전체 내용을 복사하세요. 이것을 `AZURE_CREDENTIALS`라고 부르겠습니다. 예시:
    ```shell
    {
      "clientId": "<GUID>",
      "clientSecret": "<GUID>",
      "subscriptionId": "<GUID>",
      "tenantId": "<GUID>",
      (...)
    }
    ```
1.  GitHub로 돌아가서 이 저장소의 Settings 탭에서 **Secrets and variables > Actions**를 클릭하세요.
1.  **New repository secret**을 클릭하세요.
1.  새 시크릿의 이름을 **AZURE_SUBSCRIPTION_ID**로 지정하고 첫 번째 명령의 `id:` 필드 값을 붙여넣으세요.
1.  **Add secret**을 클릭하세요.
1.  **New repository secret**을 다시 클릭하세요.
1.  두 번째 시크릿의 이름을 **AZURE_CREDENTIALS**로 지정하고 두 번째 터미널 명령의 전체 내용을 붙여넣으세요.
1.  **Add secret**을 클릭하세요.
1.  Pull requests 탭으로 돌아가서 풀 리퀘스트의 **Files Changed** 탭으로 이동하세요. `.github/workflows/deploy-staging.yml` 파일을 찾아서 편집하여 새로운 액션을 사용하세요. 전체 워크플로우 파일은 다음과 같아야 합니다:
    ```yaml
    name: Deploy to staging

    on:
      pull_request:
        types: [labeled]

    env:
      IMAGE_REGISTRY_URL: ghcr.io
      ###############################################
      ### <username>을 GitHub 사용자명으로 교체하세요 ###
      ###############################################
      DOCKER_IMAGE_NAME: <username>-azure-ttt
      AZURE_WEBAPP_NAME: <username>-ttt-app
      ###############################################

    jobs:
      build:
        if: contains(github.event.pull_request.labels.*.name, 'stage')

        runs-on: ubuntu-latest

        steps:
          - uses: actions/checkout@v4
          - uses: actions/setup-node@v4
            with:
              node-version: 16
          - name: npm install and build webpack
            run: |
              npm install
              npm run build
          - uses: actions/upload-artifact@v4
            with:
              name: webpack artifacts
              path: public/

      Build-Docker-Image:
        runs-on: ubuntu-latest
        needs: build
        name: Build image and store in GitHub Container Registry
        steps:
          - name: Checkout
            uses: actions/checkout@v4

          - name: Download built artifact
            uses: actions/download-artifact@v4
            with:
              name: webpack artifacts
              path: public

          - name: Log in to GHCR
            uses: docker/login-action@v3
            with:
              registry: ${{ env.IMAGE_REGISTRY_URL }}
              username: ${{ github.actor }}
              password: ${{ secrets.CR_PAT }}

          - name: Extract metadata (tags, labels) for Docker
            id: meta
            uses: docker/metadata-action@v5
            with:
              images: ${{env.IMAGE_REGISTRY_URL}}/${{ github.repository }}/${{env.DOCKER_IMAGE_NAME}}
              tags: |
                type=sha,format=long,prefix=

          - name: Build and push Docker image
            uses: docker/build-push-action@v5
            with:
              context: .
              push: true
              tags: ${{ steps.meta.outputs.tags }}
              labels: ${{ steps.meta.outputs.labels }}

      Deploy-to-Azure:
        runs-on: ubuntu-latest
        needs: Build-Docker-Image
        name: Deploy app container to Azure
        steps:
          - name: "Login via Azure CLI"
            uses: azure/login@v2
            with:
              creds: ${{ secrets.AZURE_CREDENTIALS }}

          - uses: azure/docker-login@v1
            with:
              login-server: ${{env.IMAGE_REGISTRY_URL}}
              username: ${{ github.actor }}
              password: ${{ secrets.CR_PAT }}

          - name: Deploy web app container
            uses: azure/webapps-deploy@v3
            with:
              app-name: ${{env.AZURE_WEBAPP_NAME}}
              images: ${{env.IMAGE_REGISTRY_URL}}/${{ github.repository }}/${{env.DOCKER_IMAGE_NAME}}:${{ github.sha }}

          - name: Azure logout via Azure CLI
            uses: azure/CLI@v2
            with:
              inlineScript: |
                az logout
                az cache purge
                az account clear
    ```
1. 파일을 편집한 후 **Commit changes...**를 클릭하고 `staging-workflow` 브랜치에 커밋하세요.
1. 약 20초 기다린 후 이 페이지(지침을 따르고 있는 페이지)를 새로고침하세요. [GitHub Actions](https://docs.github.com/en/actions)가 자동으로 다음 단계로 업데이트됩니다.
