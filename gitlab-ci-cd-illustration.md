title: Gitlab CI/CD Illustration
speaker: Merry Chris
url: https://github.com/packageman/webppt
transition: slide
theme: dark
files: /js/gitlab.js,/css/gitlab.css

[slide]

# Gitlab CI/CD Illustration
<br/>
## Speaker: Merry Chris

[slide]

## What is CI/CD?
----
- CI: **C**ontinuous **I**ntegration {:&.moveIn}
- CD:
    - **C**ontinuous **D**elivery
    - **C**ontinuous **D**eployment 

[slide id="continuous-integration"]

## Continuous Integration
----
- A software development practice {:&.fadeIn}
- **Test** and **Build** software every time a developer pushes code to the application
- It happens several times a day
<br/>
<br/>
- ![ci-1](/img/gitlab-ci-cd/ci-1.png "ci-1")

[note]
Continuous Integration is a software development practice in which you **build** and **test** software every time a developer pushes code to the application, and it happens several times a day.
<br/>
<br/>
Demo: https://git.bzy.ai/bzy/vips-frontend/pipelines/5727
[/note]

[slide id="continuous-delivery"]

## Continuous Delivery
----
- A software engineering approach {:&.fadeIn}
- **Continuous integration**, **Automated testing**, and **Automated deployment**
- The **deployment to production** is defined strategically and **triggered manually**.
<br/>
<br/>
- ![cd-1](/img/gitlab-ci-cd/cd-1.png "cd-1")

[note]
Continuous Delivery is a software engineering approach in which continuous integration, automated testing, and automated deployment capabilities allow software to be developed and deployed rapidly, reliably and repeatedly with minimal human intervention. Still, the deployment to production is defined strategically and triggered manually.
<br/>
<br/>
Demo: https://git.bzy.ai/bzy/admin/pipelines/4890
[/note]

[slide id="continuous-deployment"]

## Continuous Deployment
----
- A software development practice {:&.fadeIn}
- Every code change is **put into production automatically**
- It does everything that Continuous Delivery does, but the process is fully automated, there's **no human intervention at all**.
<br/>
<br/>
- ![cd-2](/img/gitlab-ci-cd/cd-2.png "cd-2")

[note]
Continuous Deployment is a software development practice in which every code change goes through the entire pipeline and is put into production automatically, resulting in many production deployments every day. It does everything that Continuous Delivery does, but the process is fully automated, there's no human intervention at all.
<br/>
<br/>
Demo: https://git.bzy.ai/bzy/admin/pipelines/4943
[/note]

[slide]

## Why GitLab CI/CD?
---
- **Integrated**: GitLab CI/CD is part of GitLab {:&.fadeIn}
- **Simple to Use** (vs Jenkins)
- **Economical and Secure** (GitLab Container Registry, better permission control)
- **Reliable dockerized builds**
  - Every build is isolated from the others
  - Building the same project on different architectures is easy
  - Greater control on the setup of their build environment (different skd version)
- **Better resource allocation** (vs Jenkins)
- **It scales** (runners)
- **Tests on merge requests** (Merge automatically when the build succeeds)

[note]
- https://about.gitlab.com/2016/10/17/gitlab-ci-oohlala/
- https://about.gitlab.com/2016/07/22/building-our-web-app-on-gitlab-ci/
[/note]

[slide]

## How GitLab CI/CD works?
----
1. Add `.gitlab-ci.yml` to the root directory of your repository {:&.fadeIn}
2. Configure a Runner

[note]
- https://docs.gitlab.com/ce/ci/yaml/README.html
- https://git.bzy.ai/bzy/admin/blob/develop/.gitlab-ci.yml
[/note]

[slide]

## What does `.gitlab-ci.yml` look like?

[note]
```
stages:
- build
- test
- deploy

build 1:
  stage: build
  script: echo "build dependencies"

build 2:
  stage: build
  script: echo "build artifacts"

test 1:
  stage: test
  script: echo "test"

deploy 1:
  stage: deploy
  script: echo "deploy"
```
[/note]

[slide id="pipeline"]

## Pipeline
----
- A group of jobs that get executed in stages(batches) {:&.fadeIn}
- All of the jobs in a stage are executed in parallel
<br/>
<br/>
- ![pipeline-2](/img/gitlab-ci-cd/pipeline-2.png "pipeline-2")

[note]
Demo: 
- https://git.bzy.ai/bzy/admin-server/pipelines
- https://git.bzy.ai/bzy/admin-server/pipelines/4952
[/note]

[slide]

## Type of Pipelines
----
- CI Pipeline {:&.fadeIn}
- Deploy Pipeline
- Project Pipeline

[note]
![types-of-pipelines](/img/gitlab-ci-cd/types-of-pipelines.svg "types-of-pipelines")
[/note]

[slide id="runner"]

## Runner
---
<br/>
- Request jobs from Gitlab {:&.fadeIn}
- Run your jobs and send the results back to GitLab

[note]
GitLab Runner is the open source project that is used to run your jobs and send the results back to GitLab. It is used in conjunction with GitLab CI, the open-source continuous integration service included with GitLab that coordinates the jobs.
<br/>
<br/>
Gitlab runner source code: https://gitlab.com/gitlab-org/gitlab-runner
[/note]

[slide]

## Type of Runners
---
- Shared Runner ([Fair Usage Queue](https://docs.gitlab.com/ce/ci/runners/#how-shared-runners-pick-jobs)) {:&.fadeIn}
- Group Runner ([FIFO](https://en.wikipedia.org/wiki/FIFO_(computing_and_electronics))
- Specific Runner ([FIFO](https://en.wikipedia.org/wiki/FIFO_(computing_and_electronics))

[slide]

## Runner Executors
---
- SSH {:&.fadeIn}
- Shell
- Parallels
- VirtualBox
- Docker
- Docker Machine (auto-scaling)
- [Kubernetes](https://docs.gitlab.com/runner/executors/kubernetes.html)

[slide]

## Workflow of Kubernetes Executor
---
- **Prepare**: create the Pod against the Kubernetes Cluster {:&.fadeIn}
- **Pre-build**: clone, restore cache and download artifacts from previous stages
- **Build**: user build
- **Post-build**: create cache, upload artifacts to GitLab

[slide]

## Using Gitlab Runner in Kubernetes
---

- [Register Runner](https://docs.gitlab.com/runner/register/)(binds the Runner with a GitLab instance) {:&.fadeIn}
- [Configure Runner](https://git.bzy.ai/sre/deployment/blob/master/k8s/sre/gitlab/runner/gitlab-runner.configmap.yml#L9)
- [Deploy Runner on Kubernetes](https://git.bzy.ai/sre/deployment/tree/master/k8s/sre/gitlab/runner) (deployment, namespace, pvc...)
- [View Runners on Gitlab](https://git.bzy.ai/admin/runners)

[slide]

## Cache VS Artifacts
---
- https://docs.gitlab.com/ee/ci/caching/#cache-vs-artifacts

[slide]
## Issues & Solutions
---
- [Docker volumes not mounted when using docker:dind](https://gitlab.com/gitlab-org/gitlab-ce/issues/41227)
- [Distributed runners caching](https://docs.gitlab.com/runner/configuration/autoscale.html#distributed-runners-caching)
- [Git Clone every time](https://git.bzy.ai/sre/ops/issues/9)
- [Current Practice](https://git.bzy.ai/sre/ops/issues/9)

[slide]

## Performance Improvement
---
- [Cache Build Dependencies](https://git.bzy.ai/sre/deployment/blob/master/k8s/sre/gitlab/runner/gitlab-runner.pv.yml)
- [Using Docker Caching](https://docs.gitlab.com/ce/ci/docker/using_docker_build.html#using-docker-caching)
- [Using the GitLab Container Registry](https://docs.gitlab.com/ce/ci/docker/using_docker_build.html#using-the-gitlab-container-registry)

[slide]

## Q&A
---
Thanks