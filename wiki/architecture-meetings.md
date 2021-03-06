This document contains info about upcoming architecture meetings' agendas. It can also be used to document the decisions / actions points of held meetings. Add new meetings to the top of the document.

It is not meant to document the architectural solutions as such, rather to document the meetings in which the architecture was discussed and thus the process through which decisions on the architecture were made. The general outline of the project's architecture is documented in [the architecture document](architecture.md).

This document references the [architectural risks EasyRetro board](https://easyretro.io/board/7bd0a287-133c-49dc-9935-36627d8f9c1c/6f29490c-bfa6-46a6-a400-4f48e0640a1f).


2021-06-23 Architecture meeting
-------------------------------

**1. "Netex Transmodel Nordic Profile Discussion" -meeting news**
  - ABTBO and Jore4 -project have interfaces
  - ABTBO might need bus stop, time table, etc data
  - => projects will sync their common parts later when ABTBO has identified their needs

**2. GraphHopper vs pgRouting**
  - ready docker image available containing pgrouting and shapefile import extensions?
  - mapmatching using pgrouting works quite well
  - => if pgrouting is not available in managed db, map matching and navigation can be done in own microservice container
  - => postgresql foreign data wrapper could be used to expose "main db" data to navigation service db container
  - => hasura could use the navigation service like a "normal" db
  - => 2 use cases for pgRouting:
    1. jore3 route map matching and
    2. route creation and navigation
  - => hasura v2 needed to run navigation service sql functionality easily (or use two hasura v1 instances)

**3. Kind cluster vs docker-compose for development dependencies**
  - during development of only one microservice, many microservices may be needed to run locally
  - kind is slow (locally)
  - docker compose better, but would need duplicate data
  - => can share docker image tags between kind and docker compose in flux repo
  - => services, which are needed during development, but not development target, can be started from docker compose
  - => services, which are to be developed, can be started from their repo using development tools
  - => need to solve how locally started services can be accessed from inside docker containers


2021-06-16 Architecture meeting
-------------------------------

**1. Azure psql single-server vs flexi-server**
  - flexi-server
    - => API not ready
    - => scales well
    - => high availability
  - single server instance
    - => does not scale well
    - => supports pgRouting extension
  - postgresql in kubernetes
    - => most flexible
    - => allows to use latest postgresql versions and features
    - => most work (administration)
    - => needs own backup solutions
  - => will create ticket to study how backups work in Azure hosted postgresql
  - => no hurry to decide which solution to use
  - => let's hear @haphut's reasons for suggesting self-hosted solutions before deciding

**2. Running pgRouting (in PostgreSQL database with PostGIS) in Docker as a dedicated microservice**

2021-06-09 Architecture meeting
-------------------------------

**1. For documentation purposes: Frontend graphql library**
  - apollo was decided, since hasura's relay support is not reliable enough
  - is redux needed / wanted to keep (also) Apollo's state?
  - => might not be needed, Apollo has its own store

**2. Frontend map library**
  - different libraries have been studied
  - problems have been found
  - => Some findings documented in [board issue](https://github.com/HSLdevcom/jore4/issues/26)
  - => OpenLayers not natively React-compatible
  - => MapLibre has "ecosystem risk" (proprietary version has been developed)
  - => Leaflet was easy to demo with markers
  - => criteria should include team's experience
  - => performance might not be a real problem
  - => it was decided to use leaflet, since there is experience with it

**3. Common /api prefix for backend paths in cloud?**
  - only reason to use /api is to avoid conflicts in routes between frontend and backends
  - => let's use /api prefix

**4. How to import digiroad data**
  - let's schedule own meeting for this
  

2021-06-02 Architecture meeting
-------------------------------

- skipped


2021-05-26 Architecture meeting
-------------------------------

**1. [Data model sketching document](data-model-sketching.pdf) walk-through**
  * @jarkkoka showed state of work on data model
  * => open issues:
    - geometry vs geography
    - maybe good to model route as own entity (not only journey-pattern-based)
    - Jore3:ssa lot of redundant data, might be good to come up with ways to join these

**2. State of repo management (West)**
  * @jschummer will demonstrate how / if West can be used
  * useful if dependencies between components differ in development and "production" environments
  * => not needed yet, let's get back to it when needed

**3. Frontend graphql library**
  - relay
    - principled
    - developed and used heavily by Facebook
    - meeting comments:
      - documentation not good
      - can support types
      - good automatic performance optimizations
      - may not work together with Hasura well
      - => Hasura community call 27.5. might reveil more about what will be supported in the future
  - apollo
    - most popular
    - easy to start with
  - https://formidable.com/open-source/urql/docs/comparison/
    - about two developers from one company, Formidable Labs
    - 6k GitHub stars
  - https://gqless.com/
    - about 1.5 developers
    - 3k GitHub stars
  - https://github.com/ablestack/graphql-codegen-hasura
    - not a client but helps with typing when using both apollo and hasura
    - beta status
    - 120 GitHub stars
    - dwindling development
      - on the other hand, I think it's just a generator so maybe not that much to maintain
  - let's continue next week


2021-05-19 Architecture meeting
-------------------------------

**1. [Data model sketching document](data-model-sketching.pdf) walk-through**
  * => skipped since @jarkkoka is not present
  * => let's talk about it next week

**2. Auth-backend - open issues**
  * use of sessions
  * sharing of resources (role and permission definitions)
  * => at least roles, better also definitions
  * => Hasura generates own role definitions (and what permission they include)
  * => these are shared with frontend in graphql schema
  * => problem: How can frontend use compile-time static type defs if Hasura uses dynamically defined roles
  * => possible solution: Super-user can be used to generate all possible role definitions, but not used for anything else
  * => problem: do all queries have to be formed separately for all roles? (if e.g. only super-user is allowed to see certain response parts)
  * => possible solution: graphql's "include" directive could be used to include certain information only for certain users
  * => @haphut will create ticket to try out how this can be achieved with Hasura

**3. Port conventions for microservices**
  * local / cloud env
  * => @jschummer will create wiki file to keep track of ports used with different components

**4. State of repo management (West?)**
  * => @jschummer will try out west in current scenario and report next week how it can be used

**5. Dependency security issues (github actions vs frontend / backend dependency exploitability)**
  * => good to pin github actionsit to prevent easy ways of sabotage
  * => SHA-pinning (as opposed to referring to versions) good way to prevent accidents and attacks
  * => trusted orgs repositories' actions could be referenced by version
  * => others (e.g. Lighthouse actions) could be audited and forked or SHA-pinned
  * => all actions should be checked case-by-case
  * => the ticket related to SHA-pinning (created by @pallost) could be used to cover this work


2021-05-12 Architecture meeting
-------------------------------
- Short discussion about the current implementation state of the auth backend
- Uses sessions, implemented in Kotlin
- Agenda moved to next week due to many absences


2021-05-05 Architecture meeting
-------------------------------
Skipped because of calendar overlap.


2021-04-28 Architecture meeting
-------------------------------

**1. SQL migration strategy**
1. What is the scope of the applications, overlapping schemas?
  * => Hasura runs migrations, creates schemas 
2. Modules' unit tests need at least scoped sql migration scripts?
  * => need to ensure that schema can be used by all modules / backends
  * => modules could create their own migrations, which would be populated to hasura
  * => at a later stage, the modules could possibly run their own independent migrations
  * => common schema might be problematic, since single module might need to create common schema content in tests, but not in prod
  * => [another document on datamodel sketching](data-model-sketching.pdf) is already under work, might want to go through that later
  * => during development of module could override hasura docker container's migrations via volume mounts
3. Who is responsible for creating sql users?
  * => secrets should be put in key vault
  * => only ansible can write in key vault
  * => in the beginning ansible should create users, later "big migrator" 
4. Who has admin role to execute sql migrations?
5. How to implement SQL migrator? As a single docker container or split to applications?
  * => Hasura gets admin role and runs migrations 

**2. State of Hastus-questions ([github-ticket](https://github.com/HSLdevcom/jore4/issues/14))**
  * => Questions could be skimmed at local Hastus demonstration (5.5.2021)
  * => Should be sent on after that

**3. State of coding style / library conventions ([backend-github-ticket](https://github.com/HSLdevcom/jore4/issues/297), [frontent-github-ticket](https://github.com/HSLdevcom/jore4/issues/290))**
  * => First should check if Kotlin should be used
  * => Clojure maybe not an option, not enough large support community available
  * => @haphut will ask about possibility to use Kotlin
  * => let's leave tickets in there, get back to them later

**4. Gerrit / jenkins**
  * => currently it does not pay off to have own gerrit / jenkins
  * => let's get back when more coding is underway


2021-04-21 Architecture meeting
-------------------------------

**1. Status of different proof-of-concept / trial tracks**
  * graphql-playground: data model and UI in good state, repo can be used in future for frontend PoC
    * => subscriptions good to use
  * jore4-model-mapping-experiment
    * => manual Transmodel XML message creation difficult
  * jore4-deploy: under active development, provisioning Azure cloud resources, in the future not used for Kube (which has its own repo, jore4-flux)
  * jore4-sql-schema-experiment: ready, data model not useful for further development
    * => showed how Hasura can be used
  * jore4-jore3-importer: under active development
  * jore4-robot: template for future times when we have something to test
  * jore4-digiroad-import-experiment: loading and importing of digiroad Uusimaa material
    * => loading succeeds, open PR
  * jore4-ui: Jore4 UI, under active development
  * jore4-flux: under active development Kubernetes configurations, deployment
  * jore4-deploy-branch-experiment: how to use different branches with flux
  * temporal-jooq (repo under different account): various experiments related to database structure
  * hootenanny (not yet in repo): experiments related to combining of different infrastructure networks

**2. State of Hastus-questions ([github-ticket](https://github.com/HSLdevcom/jore4/issues/14))**
  * discussed ticket comments

**3. State of coding style / library conventions ([backend-github-ticket](https://github.com/HSLdevcom/jore4/issues/297), [frontent-github-ticket](https://github.com/HSLdevcom/jore4/issues/290))**
  * discussed ticket comments


2021-04-14 Architecture meeting
-------------------------------

**1. Coding conventions (nullability, collection libraries, etc)**
  * Both frontend and backend conventions / libraries can be discussed, see tickets HSLdevcom/jore4#290 and HSLdevcom/jore4#291
  * => Let's first gather general ideas in the form of comments on the tickets themselves, as suggested by @haphut
  * => Can then in a second step gather to discuss our findings and write down actual conventions

**2. How to deploy releases in the future?**
  * Short demo by @pallost, if ready?
  * If not ready, next time
  * => demo by @pallost
  * => yaml-file defines
    * definitions of different env's env vars
    * docker-image versions to be used in kubernetes
  * => deployment to different environments via git tags
  * => rollback via new tag, new deployment
  * => everything to be deployed goes through main branch

**3. How / when to tackle prioritized bi-temporal data model integrity issues**
  * Which entities need to be prioritizable?
  * Which entities need to have have a validity time?
  * How to determine which set of changes (which tables, transaction?) to revert from the temporal history data?
  * How to check consistency / integrity before changing data? (Both priority and validity period affect the actually valid set of time at a certain point of time)
  * => priorities don't need to be integers
  * => enough to be able to tell the order of different entities
  * => Transmodel has versions, they are however probably not related to priorities
  * => can avoid modeling of priorities by saving only the relevant ("visible") parts of data
    * i.e. temporary route would split the "normal" route into two parts
  * => need to check how designers see prioritization needs, which entities need to be prioritizable

**4. Status of different proof-of-concept / trial tracks**
  * => Ran out of time, let's check those next week

**5. Gather / check questions on Hastus-integration (ticket jore4#14)**
  * Questions should be added to ticket and finally be sent / presented to Hastus-owner(s)
  * => Let's add more questions to the ticket and sync with PO when we're ready for sending
  * => @jschummer will ask for a Hastus demo
  

2021-04-07 Architecture meeting
-------------------------------

**1. How to deploy releases in the future?**
  * Short demo by @pallost, if ready?
  * If not ready, next time
  * => next time

**2. Which credentials / IPs to keep secret?**
  * No special requirements from customer's side regarding Jore3 db IP
  * => Nothing to be kept secret in documentation except for DB credentials themselves
  * => @jschummer will perform documentation updates (Jore3 db IP, credential vault name, credential names to be published)

**3. Which risks to tackle next?**
  * Chosen from EasyRetro board: "data model becomes a monolithic mess"
  * 3 most voted:
    1. plan data model so that different modules' data is connected via well defined interfaces (API if microservice arch)
    1. gherkin-style requirement modelling for db / other query-based trial and testing of db
       * EDIT 2021-04-08: @jarkkoka pointed out that jore-map-ui contains a set of queries used with jore3. (Some of) those can maybe be used to check the new data model of jore4.
    1. (many different ones got the same amount of votes, see EasyRetro board for info)
    * let's get back to these when the data model is being changed more rapidly / substantially
  * side comment: large risks / open issues in frontend development:
    * graphql (relay)
    * map library


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
