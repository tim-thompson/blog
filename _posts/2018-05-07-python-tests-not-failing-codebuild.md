---
layout: post
title: Failing Python Tests Don't Stop Build in Aws Codebuild
---

Here's a short post to provide a quick solution to a problem I came across while using AWS CodeBuild with Python tests. Though I say problem, in reality it is intended functionality for AWS CodeBuild. But this workaround keeps things slightly neater for the way that I'm doing things (probably not the right way!) but it may help you.

For my current personal project my services are written with Python 3.6 and deployed to AWS Lambda using [Zappa](https://github.com/Miserlou/Zappa). To automate unit tests and API deployment I'm using a buildspec.yml file so I can use AWS CodeBuild. My build spec file originally looked something like this:

```yaml
version: 0.2

env:
  variables:
    FLASK_APP: ""
    FLASK_DB: ""
  parameter-store:
    AWS_ACCESS_KEY_ID: ""
    AWS_SECRET_ACCESS_KEY: ""

phases:
  install:
    commands:
      - echo Setting up virtualenv
      - python -m venv venv
      - source venv/bin/activate
      - echo Installing requirements from file
      - pip install -r requirements.txt
  build:
    commands:
      - echo Build started on `date`
      - echo Building and running tests
      - python tests.py
      - flask db upgrade
  post_build:
    commands:
      - echo Build completed on `date`
      - echo Starting deployment
      - [Zappa](https://github.com/Miserlou/Zappa) update dev
      - echo Deployment completed
```

I rather foolishly made the assumption that should the build step fail the post build step wouldn't run. In reality the POST_BUILD phase and the UPLOAD_ARTIFACTS phase will always attempt to run so that debug information can be pulled from a failed build.

In hindsight this makes perfect sense. But I'm stubborn and setting up a full deployment project in some way just to run *zappa update dev* seemed pointless. So putting it in the post build phase provides some logical separation as in theory a deployment should always come 'post build'.

Obviously though this means a deployment will be attempted regardless of whether tests failed or not. So I replaced the post_build section of my build spec with this.

```yaml
post_build:
    commands:
      - if [ $CODEBUILD_BUILD_SUCCEEDING = 1 ]; then echo Build completed on `date`; echo Starting deployment; [Zappa](https://github.com/Miserlou/Zappa) update dev; else echo Build failed ignoring deployment; fi
      - echo Deployment completed
```

It runs a command with an if statement that checks the value of an environment variable native to CodeBuild. *$CODEBUILD_BUILD_SUCCEEDING* is set to 0 or 1 automatically depending on whether the build so far has succeeded. There are [other default environment variables](https://docs.aws.amazon.com/codebuild/latest/userguide/build-env-ref-env-vars.html) available too which are worth knowing. All I'm doing here is checking the value is 1, for succeeding, and running a deployment.

Here is a prettier version of the command.

```bash
if [ $CODEBUILD_BUILD_SUCCEEDING = 1 ]
then 
    echo Build completed on `date` 
    echo Starting deployment 
    update dev 
else 
    echo Build failed ignoring deployment 
fi
```

It's about as simple as it gets, but for a quick hack that does what I need it to I'm happy.