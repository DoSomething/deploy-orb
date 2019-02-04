# Lambda Orb
A simple helper for deploying Lambda applications.

To use this orb, try:

```yaml
version: 2.1

orbs:
  lambda: dosomething/lambda@0.0.3

jobs:
  build:
    docker:
      - image: circleci/node:8.10
    steps:
      # Install dependencies, run tests, etc:
      # - ...

      # Finally, use the 'lambda/store' task to package the
      # working directory to a zip & persist it for deploys.
      - lambda/store

# Then, configure workflows by chaining the app-specific 'build'
# and our standardized 'lambda/deploy' job. Here's an example that
# automatically deploys to development & QA, and holds for sign-off
# before deploying to production.
workflows:
  version: 2
  build-deploy:
    jobs:
      - build
      - hold:
          type: approval
          requires:
            - deploy-dev
            - deploy-qa
          filters:
            branches:
              only: master
      - lambda/deploy:
          name: deploy-dev
          app: dosomething-graphql-dev # <--- replace with your app name!
          requires:
            - build
          filters:
            branches:
              only: master
      - lambda/deploy:
          name: deploy-qa
          app: dosomething-graphql-qa # <--- replace with your app name!
          requires:
            - build
          filters:
            branches:
              only: master
      - lambda/deploy:
          name: deploy-production
          app: dosomething-graphql # <--- replace with your app name!
          requires:
            - hold
          filters:
            branches:
              only: master
```
