<!--
  <<< Author notes: Step 5 >>>
  Start this step by acknowledging the previous step.
  Define terms and link to docs.github.com.
-->

## Step 5: 레이블 기반으로 프로덕션 환경에 배포하기

_배포 완료! :ship:_

### 잘하셨습니다

이전과 마찬가지로 `main`에서 `production-deployment-workflow`라는 새 브랜치를 만드세요. `.github/workflows` 디렉토리에 `deploy-prod.yml`이라는 새 파일을 추가하세요. 이 새 워크플로우는 `main`에 대한 커밋을 전용으로 처리하고 `prod`에 대한 배포를 관리합니다.

**지속적 배포**(CD)는 많은 동작과 더 구체적인 개념을 포함하는 개념입니다. 그 중 하나는 **프로덕션에서 테스트**입니다. 이는 프로젝트와 회사에 따라 다른 의미를 가질 수 있으며, "CD를 하고 있다"거나 "하지 않고 있다"고 말하는 엄격한 규칙은 아닙니다.

우리의 경우, 프로덕션 환경을 스테이징 환경과 정확히 동일하게 맞출 수 있습니다. 이렇게 하면 프로덕션에 배포한 후 예상치 못한 문제가 발생할 가능성을 최소화합니다.

### :keyboard: 활동 1: 프로덕션 배포 워크플로우에 트리거 추가하기

다음 내용을 파일에 복사하여 붙여넣고, `<username>` 자리 표시자를 모두 GitHub 사용자명으로 교체하세요. 스테이징 워크플로우와 크게 달라진 점은 없지만, 트리거가 다르고 레이블로 필터링하지 않는다는 점이 다릅니다.

```yaml
name: Deploy to production

on:
  push:
    branches:
      - main

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
          images: ${{env.IMAGE_REGISTRY_URL}}/${{ github.repository }}/${{env.DOCKER_IMAGE_NAME}}:${{github.sha}}

      - name: Azure logout via Azure CLI
        uses: azure/CLI@v2
        with:
          inlineScript: |
            az logout
            az cache purge
            az account clear
```

1. 모든 `<username>`을 GitHub 사용자명으로 업데이트하세요.
1. 변경 사항을 `production-deployment-workflow` 브랜치에 커밋하세요.
1. Pull requests 탭으로 이동하여 `production-deployment-workflow` 브랜치의 **Compare & pull request**를 클릭하고 풀 리퀘스트를 생성하세요.

좋습니다! 사용한 구문은 GitHub Actions에 main 브랜치에 커밋이 이루어졌을 때만 해당 워크플로우를 실행하도록 지시합니다. 이제 이 워크플로우를 실행하여 프로덕션에 배포할 수 있습니다!

### :keyboard: 활동 2: 풀 리퀘스트 병합하기

1. 이제 풀 리퀘스트를 [병합](https://docs.github.com/en/get-started/quickstart/github-glossary#merge)할 수 있습니다!
1. **Merge pull request**를 클릭하고 이 탭을 열어둔 상태로 유지하세요. 다음 단계에서 닫힌 풀 리퀘스트에 레이블을 적용할 것입니다.
1. 이제 패키지가 GitHub Container Registry에 게시되고 배포가 완료될 때까지 기다리면 됩니다.
1. 약 20초 기다린 후 이 페이지(지침을 따르고 있는 페이지)를 새로고침하세요. [GitHub Actions](https://docs.github.com/en/actions)가 자동으로 다음 단계로 업데이트됩니다.
