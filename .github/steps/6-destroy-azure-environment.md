<!--
  <<< Author notes: Step 6 >>>
  Start this step by acknowledging the previous step.
  Define terms and link to docs.github.com.
-->

## Step 6: 프로덕션 배포

_잘하셨습니다! :sparkle:_

훌륭합니다, 해냈습니다! 메인 저장소 페이지의 계정 **Packages** 섹션에서 컨테이너 이미지를 확인할 수 있습니다. 스테이징 URL과 마찬가지로 Actions 로그에서 배포 URL을 확인할 수 있습니다.

### 클라우드 환경

이 과정 동안 리소스를 생성했는데, 방치하면 클라우드 제공업체에서 요금이 발생하거나 무료 시간을 소진할 수 있습니다. 프로덕션에서 애플리케이션을 확인한 후, 더 많은 학습을 위해 시간을 아끼도록 환경을 정리합시다!

### :keyboard: 활동 1: 실행 중인 리소스를 정리하여 요금이 발생하지 않도록 하기

1. 병합된 `production-deployment-workflow` 풀 리퀘스트에 `destroy environment` 레이블을 생성하고 적용하세요. 풀 리퀘스트 탭을 이미 닫은 경우 **Pull requests**를 클릭한 다음 **Closed** 필터를 클릭하여 병합된 풀 리퀘스트를 볼 수 있습니다.

적절한 레이블을 적용했으므로 GitHub Actions 워크플로우가 완료될 때까지 기다립시다. 완료되면 앱의 URL을 방문하거나 Azure 포털에 로그인하여 실행되지 않고 있는지 확인하여 환경이 정리되었는지 확인할 수 있습니다.

2. 약 20초 기다린 후 이 페이지(지침을 따르고 있는 페이지)를 새로고침하세요. [GitHub Actions](https://docs.github.com/en/actions)가 자동으로 다음 단계로 업데이트됩니다.
