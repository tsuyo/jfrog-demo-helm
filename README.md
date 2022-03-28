# JFrog Platform Demo: Helm

Sources for [Deploying to Kubernetes in Pipelines](https://www.jfrog.com/confluence/display/JFROG/Deploying+to+Kubernetes+in+Pipelines)

## Preparation
K8s
- K8s docker-registry secrets (see [Pull an Image from a Private Registry](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/))
  - name: regcred, namespace: app
  ```
  $ kubectl create ns jfrog-demo
  $ kubectl create -n jfrog-demo secret docker-registry regcred --docker-server=<your-registry-server> --docker-username=<your-name> --docker-password=<your-pword> --docker-email=<your-email>
  ```
- DNS entry for App Ingress URL  

Artifactory
```
$ jf rt rc --vars "project=demo" artifactory/helm-local.json
$ jf rt rc --vars "project=demo" artifactory/helm.json
```

Pipelines
- Select "demo" project -> Project Settings -> Integrations -> Add an Integration
  - GitHub
    - Name: github
    - Integration Type: GitHub
    - Token: <your github token>
  - Artifactory
    - Name: artifactory
    - Integration Type: Artifactory
    - Artifactory URL: <default on UI>
    - User: admin
    - API Key: <press "Get API Key">
  - Kubernetes
    - Name: kubernetes
    - Integration Type: Kubernetes
    - Kube Config: <paste jfrog-boot helm output>

- Select "demo" project -> Project Settings -> Pipeline Sources -> Add Pipeline Source -> From YAML
  - Branch Type: Single Branch
  - Protocol Type: HTTPS
  - Name: jfrog_demo_helm
  - SCM Provider Integration: github
  - Repository Full Name: tsuyo/jfrog-demo-helm
  - Branch: main
  - Pipeline Config File Filter: pipelines/pipelines-.+.yml

## Local Test
```
$ helm upgrade jfrog-demo -i -n jfrog-demo --set image.name=platform.dev.gcp.tsuyo.org/demo-docker/jfrog-demo-core --set image.tag=1 --dry-run --debug ./jfrog-demo-chart
```