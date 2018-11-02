# CircleCI & Anchore

## Introduction

Docker gives developers the ability to streamline packaging, storage, and deployment of applications at great scale. With increased use of container technologies across software development teams, securing these images become challenging. Due to the increased flexibility and agility, security checks for these images need to be woven into an automated pipeline and become part of the development lifecycle.

## Securing container images with Anchore

Continous Integration plays a critical role when we move to building container images as well. Container images, by nature, are immutable. Meaning, once we build an image, it is unchanged. If we want to make any changes to the application that runs inside the container, we will build another image with the code changes, and then deploy the new, updated container. This decreases development and testing time, and fits in nicely with CI. 

One of the core pieces of CI is running tests automatically. When we build container images in a CI pipeline, we should employ a tool that is able to conduct the appropriate level of analysis on the build artifact. Anchore is a service that conducts static analysis on container images, and applies user-defined acceptable polices to allow automated container image validation and certification. This means that not only can Anchore users gain a deep insight into the OS and non-OS packages contain within an image, but they have the ability to create governence around the artifact and it's contents. 

## Integrating Anchore scanning in a CircleCI build

In the following examples, we will walkthrough how to integrate Anchore scanning into a CircleCI build. 

**Note** these examples leverage the anchore/anchore-engine@1.0.0 CircleCi orb. Additionally, they require a .circleci directory and `config.yml` file.

### Adding Anchore scanning of a public image scan job to a CircleCi workflow

Here the public image is just the anchore-engine:latest image. 

```
version: 2.1
orbs:
  anchore-engine: anchore/anchore-engine@1.0.0
workflows:
  scan_image:
    jobs:
      - anchore/image_scan:
          image_name: anchore/anchore-engine:latest
          timeout: '300'
```

### Adding Anchore scanning of a private image scan job to a CircleCi workflow

Here the we are connecting to a private registry and pulling the defined image. 

```
version: 2.1
orbs:
  anchore-engine: anchore/anchore-engine@1.0.0
workflows:
  scan_image:
    jobs:
      - anchore/image_scan:
          image_name: anchore/anchore-engine:latest
          timeout: '300'
          private_registry: True
          registry_name: docker.io
          registry_user: "${DOCKER_USER}"
          registry_pass: "${DOCKER_PASS}"
```
### Adding Anchore image scanning to your container build pipeline job

Here we are building a docker image from the connected repository, and scanning the image after. As part of the scan, we've added `analysis_fail: True` to the configuration. When this is set to true, the analyzed image will also be evaluated against the default policy bundle that ships with anchore-engine. Should the result of the policy evaluation fail, the pipline will fail at this step. 

```
version: 2.1
orbs:
  anchore-engine: anchore/anchore-engine@1.0.0
jobs:
  local_image_scan:
    executor: anchore/anchore_engine
    steps:
      - checkout
      - run:
          name: build container
          command: docker build -t ${CIRCLE_PROJECT_REPONAME}:ci .
      - anchore/analyze_local_image:
          image_name: ${CIRCLE_PROJECT_REPONAME}:ci
          timeout: '500'
          analysis_fail: True
```
