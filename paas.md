title: IaaS、PaaS、SaaS
speaker: byron
plugins:
    - echarts

<slide :class="size-60 aligncenter">

# IaaS、PaaS、SaaS {.text-shadow}

By byron.zhang {.text-intro}

<slide>

![hosting_model](/img/paas/hosting_model.png)

<slide>

## Advantages vs Disadvantages

- complexity
- maintenance
- costs(prepayment, pay-as-you-go)
- security(bank, health care)
- stability(climate, power, bandwidth)
- reliability(fault tolerance)

<slide>

## Heroku

- App
- Pipeline

<slide>

## App

- Dynos(app containers)
- Add-ons(components, services)
- Config vars
- Metrics
- Logs
- Activities
- Access
- Domains(defaults to `[name of app].herokuapp.com`)

<slide>

## Dynos

- Web
- Worker(long-running、batch job)
- One-off

<slide>

## Scalability

![heroku_autoscaling](/img/paas/heroku_autoscaling.png)

<slide>

## Add-ons

https\://elements.heroku.com/addons

<slide>

## Pipeline

- Reivew apps
- CI
- Staging
- Production
- Release phase(database migrations)

<slide>

![heroku_pipeline](/img/paas/heroku_pipeline.png)

<slide>

## Deployment with docker

<slide>

## `heroku.yml`

- setup - Specifies the add-ons and config vars to create during app provisioning
- build - Specifies the Dockerfile to build
- release - Specifies the release phase tasks to execute
- run - Specifies process types and the commands to run for each

<slide>

```yml
setup:
  addons:
    - plan: heroku-postgresql
      as: DATABASE
  config:
    S3_BUCKET: my-example-bucket
build:
  docker:
    web: Dockerfile
    worker: worker/Dockerfile
  config:
    RAILS_ENV: development
    FOO: bar
release:
  command:
    - ./deployment-tasks.sh
  image: worker
run:
  web: bundle exec puma -C config/puma.rb
  worker: python myworker.py
  asset-syncer:
    command:
      - python asset-syncer.py
    image: worker
```

<slide>

## Heroku CLI

<slide>

## Demo

<slide>

## 参考

- [https://medium.com/google-cloud/gcp-the-google-cloud-platform-compute-stack-explained-c4ebdccd299b](https://medium.com/google-cloud/gcp-the-google-cloud-platform-compute-stack-explained-c4ebdccd299b)
- [What is Colocation Hosting?](https://www.atlantic.net/what-is-colocation-hosting)
- [阿里云函数计算](https://help.aliyun.com/document_detail/52895.html?spm=5176.cnfc.0.0.4d1a224eIHxb2t&aly_as=rsSvq1hE)
- [Heroku 文档](https://devcenter.heroku.com/categories/heroku-architecture)
- [Heroku redis](https://devcenter.heroku.com/articles/heroku-redis#connecting-in-go)
