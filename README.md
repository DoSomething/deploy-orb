# Lambda Orb
A simple helper for deploying Lambda applications.

To use this orb, try:

```yaml
version: 2.1

orbs:
  deploy: dosomething/lambda@0.0.1

jobs:
  build:
    docker:
      - image: circleci/node:8.10
    working_directory: ~/app
    steps:
      # ...Install dependencies, run tests, etc.
      # Finally, use the 'deploy/store-lambda' task to package
      # the working directory to a zip & persist it for deploys.
      - deploy/store
          working_directory: ~/app

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
              only: circleci-workflow
      - lambda/deploy:
          name: deploy-dev
          app: dosomething-graphql-dev # <--- replace with your app name!
          working_directory: ~/app
          requires:
            - build
          filters:
            branches:
              only: master
      - lambda/deploy:
          name: deploy-qa
          app: dosomething-graphql-qa # <--- replace with your app name!
          working_directory: ~/app
          requires:
            - build
          filters:
            branches:
              only: master
      - lambda/deploy:
          name: deploy-production
          app: dosomething-graphql # <--- replace with your app name!
          working_directory: ~/app
          requires:
            - hold
          filters:
            branches:
              only: master
```
