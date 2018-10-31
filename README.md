# CircleCI & Anchore

## Why Continous Integration?

Before examining why container images should be scanned during a CI build, it is important to fundamentally understand what continuous integration is, and why we need it? 

Martin Fowler defines continuous integration as follows: "Continous Integration is a software development practice where members of a team integrate their work frequently; usually, each person integrates at least daily... Each integration is verified by an automated build.. to detect integration erros as quickly as possible."

Now that we have a definition, we can start to uncover why this is necessary. In short, we need a structured and reliable process for building, testing, and deploying software. Most often, software applications are incredibly complex and contain many moving parts. Each of these pieces contain separate features to test, separate bugs to fix, and potentially, different environments to deploy to. We can begin to see where this is going. The more structure and reliablility software development teams have to their build and testing process, the less time they will spend fixing issues, worrying about deployments, rolling back, etc.. At the risk of getting too in the weeds, by automating these moving pieces, developers are saving time by implementing at solid continuous integration pipeline.

## Why scan with Anchore?

Continous Integration plays a critical role when we move to building container images as well. Container images, by nature, are immutable. Meaning, once we build an image, it is unchanged. If we want to make any changes to the application that runs inside the container, we will build another image with the code changes, and then deploy the new, updated container. This decreases development and testing time, and fits in nicely with CI. 

One of the core pieces of CI is running tests automatically. When we build container images in a CI pipeline, we should employ a tool that is able to conduct the appropriate level of analysis on the build artifact. Anchore is a service that conducts static analysis on container images, and applies user-defined acceptable polices to allow automated container image validation and certification.


