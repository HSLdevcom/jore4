---
name: Feature request
about: Suggest an idea for this project
title: ''
labels: ''
assignees: ''

---

**Is your feature request related to a problem? Please describe.**
A clear and concise description of what the problem is. Ex. I'm always frustrated when [...]

**Describe the solution you'd like**
A clear and concise description of what you want to happen.

**Describe alternatives you've considered**
A clear and concise description of any alternative solutions or features you've considered.

**Additional context**
Add any other context or screenshots about the feature request here.

**Definition of Done checklist**
In Progress:
- [] code pushed to a feature branch
- [] code builds without errors / warnings
- [] code is reliable, important parts are commented
- [] code is formatted
- [] linter does not find any issues
- [] important details are tested with unit and integration tests
- [] accessibility tests are run, accessibility is taken into consideration (pa11y? lighthouse?)
- [] service is dockerised and pushed to Docker Hub
- [] code is documented
  - [] docker interface (volumes, ports, env. vars, secrets)
  - [] how to set up the development environment
  - [] how the feature works, how to use it, etc
- [] CI can build the code, linters/formatters pass, unit, integration and e2e tests are successful
  - [] if e2e tests are allowed to fail, the reason for the failing test is commented to the pull request
- [] add some general manual testing instructions to the jore4 wiki what the feature does and how 
-> Move to Review

Review:
- [] reviewer reviews the code and checks that all the "In progress" points are fulfilled
- [] code is deployed to the dev environment (versions are updated in the jore4-flux repository, dev branch is merged with the update commit)
-> Move to Deployed

Deployed:
- [] if the feature requires to get and automated e2e test, a new issue is created with the specs
- [] if e2e tests pass in the dev environment
- [] update the e2e bundle (versions are updated in the jore4-flux repo + e2e branch is merged with the commit)
- [] deploy to test environment (versions are updated in the jore4-flux repo + test branch is merged with the commit)
- [] Product Owner has (manually) tested and accepted the feature.
  - [] if a bug is found, a new issue is created with the specs on how to reproduce it.
-> Move to Done
