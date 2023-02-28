# New GitHub repositories

When creating a new GitHub repository that is not a fork of another project, follow these steps:

1. Prefix the name of the repository with `jore4-`.
1. Add the European Union Public License 1.2 (EUPL v1.2) as the file `LICENSE` in project root as your first commit, like [here](https://github.com/HSLdevcom/jore4-graphql-playground/commit/e44e5335b3a93c02de2468e8b17e2295bd36cd53).
1. Add GitHub group `jore4` in role Admin like in [jore4-ui access settings](https://github.com/HSLdevcom/jore4-ui/settings/access).
1. Configure the GitHub repository settings regarding available merges and protected branches as described in [Code Review](./code_review.md#git-repository-settings).
   FIXME: add example image of complete settings here and move the corresponding section in code review wiki page here
1. [Link the repository](https://github.com/orgs/HSLdevcom/projects/2/settings/linked_repositories) to the project backlog.
1. In <https://vincit.slack.com> on channel `#jore-hsl-bots`, run:
   1. `/github subscribe HSLdevcom/jore4-new-repository-name`
   1. `/github unsubscribe HSLdevcom/jore4-new-repository-name commits`.
1. If the repository is intended to build Docker images, ask the HSL DevOps team to create a Docker Hub repository with the same name as the GitHub repository.
   Provide the link to the GitHub repository to the HSL DevOps team as documentation.
   When the HSL DevOps team says the task is done, check that the Docker Hub credentials are available in the GitHub repository like they are in [jore4-ui secrets settings](https://github.com/HSLdevcom/jore4-ui/settings/secrets/actions).

When forking an existing project outside of Jore4:

1. As with any dependency, check from [the list](https://joinup.ec.europa.eu/collection/eupl/matrix-eupl-compatible-open-source-licences#section-2) that the license of the project to be forked is upstream-compatible with EUPL v1.2.
1. Fork under `HSLdevcom`.
1. In most cases prefer the original repository name instead of `jore4-something`.
1. Do not change the license of the original project.
   This way our contributions may eventually swim upstream.
   That would reduce our maintenance burden and give back to the ecosystem.
