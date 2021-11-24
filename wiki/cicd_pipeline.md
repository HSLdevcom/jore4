# CI/CD pipeline

Description of our development and deployment workflow.

## Development environment (local)

We have docker-compose bundle that can be downloaded from `jore4-flux` repository to setup local environment.
Versions for images can be changed with docker-compose override file. Instructions for this can be found in the repositorys [readme.md](https://github.com/HSLdevcom/jore4-flux#docker-compose)

## Pull request

After pull request is created:

1. Ticket is moved from `In progress dev` to `Review`
1. Workflows for running tests and building docker image are triggered.

   1. Unit and integration tests are run.
   1. New image is pushed to docker-hub.
   1. E2E docker-compose bundle is started with the new image.

   - Healthcheks for images are run.
   - Robot Framework smoke tests are run.
   - If tests fail because changes made to the environment, then those changes should be commented to the pull request and applied when adding the new image in `jore4-flux`repository.

1. Code review, instructions [here](code_review.md).

## Deployment to development environment

After code passes review:

1. Update images in `jore4-flux`repository and add new environment variables if needed.
1. Move changes to `dev`branch.
1. Ticket is moved to `Test and deployment`

More information on updating infrastructure [here](infrastructure-deployment.md).

## Deployment to e2e bundle and test environment

After workflows are green for the new image in `jore4-flux`repository:

1. Update the e2e bundle, by moving changes to `e2e` branch

After Robot Framework tests are run in azure `dev` environment:

1. Update azure `test`environment by moving changes to `test`branch.

Then ticket is moved to `Done`.

## Deployment to production environment

After a feature/features are accepted by customer, production environment is updated with the new images.

## Open issues

[To enable e2e tests in azure environments.](https://github.com/HSLdevcom/jore4/issues/462)
[To run tests in azure environments.](https://github.com/HSLdevcom/jore4/issues/464)
