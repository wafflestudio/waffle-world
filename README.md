# waffle-world
## [K8S] everything in wafflestudio-cluster

wafflestudio-cluster 의 **선언적 관리**와 **GitOps** 를 위한 repository 입니다.

cluster 의 **현 상태를 정확하고 투명**하게 파악하며, **누가 언제 어떤 변경을 적용했는지 기록**하는 것을 목표로 합니다.

## Structures of Directory
  ### [`/apps/*`](https://github.com/wafflestudio/waffle-world/tree/main/apps)
  - Argo CD 로 관리하는 서비스 Application 들의 manifest 파일들이 위치합니다. [admin](https://github.com/orgs/wafflestudio/teams/waffle-k8s-admin) 도 `kubectl` 등의 도구로 수동 적용하지 않습니다.
  - [App Of Apps Pattern](https://argo-cd.readthedocs.io/en/stable/operator-manual/cluster-bootstrapping/#app-of-apps-pattern) 을 적용하여, [autodeploy-app](https://github.com/wafflestudio/waffle-world/blob/main/apps/templates/autodeploy-app.yaml) 에 의해 대부분의 Argo CD Application 이 관리됩니다.
  - 대부분 서비스가 [`/apps/templates/*`](https://github.com/wafflestudio/waffle-world/tree/main/apps/templates) 에 Application 을 정의, `/apps/{namespace}/{name}/*` 에 Application 에 포함될 manifest 파일들을 정의하고 있습니다. (manifest 예시: [`snutt-prod/snutt-timetable`](https://github.com/wafflestudio/waffle-world/blob/main/apps/snutt-prod/snutt-timetable/snutt-timetable.yaml))
  - [autodeploy-app](https://github.com/wafflestudio/waffle-world/blob/main/apps/templates/autodeploy-app.yaml) 이 관리하는 [`/apps/templates/*`](https://github.com/wafflestudio/waffle-world/tree/main/apps/templates) 의 Application 들은 `main` branch 의 변경이 Argo CD 에 의해 cluster 에 자동 적용됩니다.

  ### [`/charts/*`](https://github.com/wafflestudio/waffle-world/tree/main/charts)
  - Helm 으로 관리하는 Chart 들이 정의되어 있습니다. Argo CD, Istio, Prometheus, Grafana 등 일반 서비스보다는 핵심적인 clsuter infra 와 관련되어 있습니다.
  - 오픈소스의 Chart 를 바로 사용하지 않고, `dependencies`에 이들을 포함한 새로운 Chart 를 정의하고, `values.yaml`로 필요한 부분을 override 하고 있습니다. (`values.yaml` 예시: [argocd](https://github.com/wafflestudio/waffle-world/blob/main/charts/argocd/values.yaml))
  - `helm install`, `helm upgrade` 등의 커맨드로 [admin](https://github.com/orgs/wafflestudio/teams/waffle-k8s-admin) 이 관리합니다.

  ### [`/misc/*`](https://github.com/wafflestudio/waffle-world/tree/main/misc)
  - [admin](https://github.com/orgs/wafflestudio/teams/waffle-k8s-admin) 이 `kubectl`로 수동으로 관리하는 manifest 들이 위치합니다. load-balancer-controller, cluster-autoscaler, cert-manager 등 핵심적인 clsuter infra 와 관련되어 있습니다.

## Related Links
  ### [argocd.wafflestudio.com](https://argocd.wafflestudio.com)
  - Argo CD 가 관리하는 [`/apps/*`](https://github.com/wafflestudio/waffle-world/tree/main/apps) 의 Application 들을 모니터링, 관리할 수 있습니다.
  - GitHub wafflestudio org 구성원들은 누구나 GitHub SSO 로 로그인 가능합니다.

  ### [grafana.wafflestudio.com](https://grafana.wafflestudio.com)
  - Prometheus 에 있는 데이터를 기반으로 Grafana 에서 cluster 및 서비스에 대한 여러 모니터링이 가능합니다.
  - GitHub wafflestudio org 구성원들은 누구나 GitHub SSO 로 로그인 가능합니다.

## Related Repositories
  ### [ecr-heimdall](https://github.com/wafflestudio/ecr-heimdall)
  **detects ecr push and reflects it to waffle-world**

  **how it works**
  1. GitHub Actions (in each repo) build and push docker image to ECR
  2. ECR push event triggers AWS Lambda function
  3. This Lambda function updates the image tag of the corresponding manifest file in [waffle-world](https://github.com/wafflestudio/waffle-world)
  4. ArgoCD detects the change in the manifest file and deploys the new image

  ### [k8s-monitoring](https://github.com/wafflestudio/k8s-monitoring)
  - 주기적인 모니터링을 통해, Pod, Job 의 동작 실패 시 지정된 wafflestudio Slack 채널로 메시지를 전송합니다.

  ### [waffle-kong-gateway](https://github.com/wafflestudio/waffle-kong-gateway)
  - 일부 서비스는 유저 인증 관련 부분을 [waffle-account-server](https://github.com/wafflestudio/waffle-account-server) 에 위임하여 와플스튜디오 SSO 를 사용합니다.
  - 해당 서비스 서버를 kong-gateway 뒤에 위치시켜 waffle-account-server 에서 발급한 access token 을 검증하고 이를 내부에서 사용 가능한 `waffle-user-id` 헤더로 바꿔주는 plugin 을 활용할 수 있습니다.

## Related Blog Posts
  ### [EKS 로 밑바닥부터 K8S cluster 구축하기 (1)](https://medium.com/wafflestudio/setup-k8s-cluster-with-aws-eks-in-snu-cse-dev-wafflestudio-1-91d620e66276)
