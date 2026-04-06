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
  <<< Author notes: Course start >>>
  Include start button, a note about Actions minutes,
  and tell the learner why they should take the course.
-->

## 환영합니다

GitHub Actions와 Microsoft Azure를 사용하여 두 가지 배포 워크플로우를 생성합니다.

- **대상**: 개발자, DevOps 엔지니어, GitHub 초보 사용자, 학생, 팀
- **배울 내용**: GitHub Actions와 Microsoft Azure를 사용하여 지속적 배포(Continuous Delivery)를 가능하게 하는 워크플로우를 만드는 방법을 배웁니다.
- **만들 것**: 레이블 기반으로 스테이징에 배포하는 첫 번째 워크플로우와 main에 병합 시 프로덕션에 배포하는 두 번째 워크플로우를 만듭니다.
- **사전 요구사항**: GitHub, GitHub Actions, GitHub Actions를 사용한 지속적 통합(CI)에 익숙해야 합니다.
- **소요 시간**: 이 실습은 2시간 이내에 완료할 수 있습니다.

이 실습에서 다음을 수행합니다:

1. 작업(job) 구성하기
2. Azure 환경 설정하기
3. 환경 구축하기
4. 스테이징에 배포하기
5. 프로덕션에 배포하기
6. 환경 정리하기

### 이 실습을 시작하는 방법

<!-- For start course, run in JavaScript:
'https://github.com/new?' + new URLSearchParams({
  template_owner: 'skills-kr',
  template_name: 'deploy-to-azure',
  owner: '@me',
  name: 'skills-deploy-to-azure',
  description: 'My clone repository',
  visibility: 'public',
}).toString()
-->

[![start-course](https://user-images.githubusercontent.com/1221423/235727646-4a590299-ffe5-480d-8cd5-8194ea184546.svg)](https://github.com/new?template_owner=skills-kr&template_name=deploy-to-azure&owner=%40me&name=skills-deploy-to-azure&description=My+clone+repository&visibility=public)

1. **실습 시작** 버튼을 우클릭하여 새 탭에서 링크를 여세요.
2. 새 탭에서 대부분의 입력값이 자동으로 채워집니다.
   - 소유자(owner)는 개인 계정 또는 조직(organization)을 선택하세요.
   - 비공개 저장소는 [Actions 사용 시간](https://docs.github.com/en/billing/managing-billing-for-github-actions/about-billing-for-github-actions)이 소모되므로 공개 저장소를 만드는 것을 권장합니다.
   - 아래로 스크롤하여 양식 하단의 **Create repository** 버튼을 클릭하세요.
3. 새 저장소가 생성된 후 약 20초 기다린 다음 페이지를 새로고침하세요. 새 저장소의 README에 있는 단계별 지침을 따르세요.

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
