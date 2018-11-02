# CircleCI & Anchore

## Why Continous Integration?

Before examining why container images should be scanned during a CI build, it is important to fundamentally understand what continuous integration is, and why we need it? 

Martin Fowler defines continuous integration as follows: "Continous Integration is a software development practice where members of a team integrate their work frequently; usually, each person integrates at least daily... Each integration is verified by an automated build.. to detect integration erros as quickly as possible."

Now that we have a definition, we can start to uncover why this is necessary. In short, we need a structured and reliable process for building, testing, and deploying software. Most often, software applications are incredibly complex and contain many moving parts. Each of these pieces contain separate features to test, separate bugs to fix, and potentially, different environments to deploy to. We can begin to see where this is going. The more structure and reliablility software development teams have to their build and testing process, the less time they will spend fixing issues, worrying about deployments, rolling back, etc.. At the risk of getting too in the weeds, by automating these moving pieces, developers are saving time by implementing at solid continuous integration pipeline.

## Why scan with Anchore?

Continous Integration plays a critical role when we move to building container images as well. Container images, by nature, are immutable. Meaning, once we build an image, it is unchanged. If we want to make any changes to the application that runs inside the container, we will build another image with the code changes, and then deploy the new, updated container. This decreases development and testing time, and fits in nicely with CI. 

One of the core pieces of CI is running tests automatically. When we build container images in a CI pipeline, we should employ a tool that is able to conduct the appropriate level of analysis on the build artifact. Anchore is a service that conducts static analysis on container images, and applies user-defined acceptable polices to allow automated container image validation and certification. This means that not only can Anchore users gain a deep insight into the OS and non-OS packages contain within an image, but they have the ability to create governence around the artifact and it's contents. 

## Integrating Anchore scanning in a CircleCI build

In the following examples, we will walkthrough how to integrate Anchore scanning into a CircleCI build. 

**Note** these examples leverage the anchore/anchore-engine@1.0.0 CircleCi orb. Additionally, they require a .circleci directory and `config.yml` file.

Adding Anchore scanning of a public image scan job to a CircleCi workflow:
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

Adding Anchore scanning of a private image scan job to a CircleCi workflow:
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
Adding Anchore image scanning to your container build pipeline job.
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
```
