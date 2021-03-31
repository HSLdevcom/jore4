This document contains info about upcoming architecture meetings' agendas. It can also be used to document the decisions / actions points of held meetings. Add new meetings to the top of the document.

It is not meant to document the architectural solutions as such, rather to document the meetings in which the architecture was discussed and thus the process through which decisions on the architecture were made. The general outline of the project's architecture is documented in [the architecture document](architecture.md).

This document references the [architectural risks EasyRetro board](https://easyretro.io/board/7bd0a287-133c-49dc-9935-36627d8f9c1c/6f29490c-bfa6-46a6-a400-4f48e0640a1f).


2021-04-07 Architecture meeting
-------------------------------

**1. How to deploy releases in the future?**
  * Short demo by @pallost, if ready?
  * If not ready, next time


2021-03-31 Architecture meeting
-------------------------------

**1. How can transportation routes be modeled with infrastructure maps from e.g. digiroad?**

**2. Which risk (of the ones discussed in the risks board, see above) should we tackle next?**
  * => that the IaC-solutions might not be very reliable, and thus deployment neither

**3. How to deploy releases in the future?**
  * at the moment using fluxcd
  * two basic scenarios:
      * (a) modifying environment-dependent `cluster.yml` (e.g. `cluster-test.yml`) file to reference the updated docker images to be deployed and commiting the change into e.g. the main branch
          * pro: can check form the git history which versions were deployed when
          * con: must commit (i.e. make) many changes to a file to deploy an already tested version set from test to prod
      * (b) pushing the same environment-independent `cluster.yml` changes contained in e.g. the `test` branch to the `prod` branch
          * pro: semantic action ("change which is now in test should be deployed in prod") more clear
          * con: have to check different place for version history (e.g. flux log)
  * => @pallost will demo / show a simple example of what the different solutions could look like in an upcoming meeting

**4. How to deploy cloud infra changes in the future?**
  * not urgent, an [idea-ticket](https://github.com/HSLdevcom/jore4/issues/269) has been created to start working on this
  * => will talk about this when the ticket has been prioritized by the PO and taken into a sprint

**5. How can developers update their IP-address in the Azure bastion host Network Security Group in the future?**
  * manually, either through Azure portal or via a bash script
  * => @pallost agreed to create a script, [an idea-ticket](https://github.com/HSLdevcom/jore4/issues/274) has been created for this

**6. Should developers use rather the `test` env instead of the `dev` env for regular development work?**
  * this way the dev-env could be used for cloud infra experiments / development
  * => no, developers should use the dev-env also in the future
  * => cloud guys can create their own experimental env when / if needed, should not be financially significant
