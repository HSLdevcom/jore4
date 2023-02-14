This document contains info about upcoming architecture meetings' agendas. It can also be used to document the decisions / actions points of held meetings. Add new meetings to the top of the document.

It is not meant to document the architectural solutions as such, rather to document the meetings in which the architecture was discussed and thus the process through which decisions on the architecture were made. The general outline of the project's architecture is documented in [the architecture document](architecture.md).

This document references the [architectural risks EasyRetro board](https://easyretro.io/board/7bd0a287-133c-49dc-9935-36627d8f9c1c/6f29490c-bfa6-46a6-a400-4f48e0640a1f).

2023-02-16 Architecture meeting
-------------------------------

1. Discuss how to streamline seed data generation (https://github.com/HSLdevcom/jore4/issues/1142)

2022-11-17 Architecture meeting
-------------------------------

1. How to version infrastructure network
  - The topic relates to this backlog ticket: https://github.com/HSLdevcom/jore4/issues/975
  - It was acknowledged that the problem is challenging and has not been solved in the MVP phase
  - Basically, there is no need to store history data of shapes of infrastructure links
    - With regard to compensation ("korvauslaskenta"), we can later add a numeric field into `route` table that defines the length of a route used as a basis for compensation. It is used to override the calculated route length in the same way as in Jore3.
    - In simple cases, the geometry of an infrastructure link can be updated/replaced, but it does not solve the problem with divided/split road links.
  - When a new road junction (intersection point) is created along an existing road link, the road link is divided into two new links, for which new IDs are created. We need to solve the following problems:
    - What to do with an existing route whose part (route link) refers to an old link that existed before the split?
      - Proposal: We maintain different versions of the infrastructure links with non-overlapping validity periods. As long as the route is not modified, it can still point to old versions of the road links. When editing route geometry, the road links are updated to new versions via the map-matching service. The map-matching service would always use the up-to-date versions of infrastructure links.
    - How to get a scheduled stop point updated to point to the new road link?
      - Proposal: We may need to provide UI that shows which stop points refer to old versions of road links (filtering by road link validity periods). Implement a feature to change road link reference from old link to a new link.
      - To be clarified/researched: Is there a need to maintain duplicate versions of scheduled stop points for both new and old road links to allow existing journey patterns to coexist with new routes/journey-patterns based on the updated infrastructure network.
  - There was a discussion about the extent of changes occurring in Digiroad, in order to evaluate a suitable technical solution
    - Digiroad offers a real-time API from which changes can be queried
    - Digiroad staff can be inquired about the number of annual changes. What is the typical number of road splits between Digiroad releases? However, inquiries themselves do not solve the problem of link splitting, which we probably need to solve anyway, as it can also happen with HSL-specific changes in the QGIS fixup project.


2022-11-03 Architecture meeting
-------------------------------

1. How to model referencing days in timetables
  - in Jore3 all days were defined for all timetables imported from Hastus
  - Jore4 does not have this requirement (because of partial imports with higher priority)
  - for this reason, it is not clear how to model references
  - 2 options: either referencing by key "timetable + priority + day type" or by "calendar date"
  - in Jore3 references could be limited by time (e.g. "traffic as on Sundays, but ownly until 16:00")
  - => more input from designers and end users is needed
  - => e.g. question of why until now day types have been used in references as opposded to calendar days
  - => input will be gathered and let's get back to the issue later

2. How to model exceptional days with no vehicle journeys
  - => days with no vehicle journeys (as an exception to "normal" timetables) can be modelled as a
       special case of the referencing days


2022-10-27 Architecture meeting
-------------------------------

1. Keeping SQL functionality separate from data
  - would allow for much easier development of SQL functionality
  - could be done with this card, but possibly would make sense to do this earlier than originally thought: https://github.com/HSLdevcom/jore4/issues/923
  - could work by keeping functionality and data migrations separately
  - functionality migrations would always create everything from scratch
  - basic up flow:  functionality migration N - 1 down -> data migration N up -> functionality migration N up
  - basic down flow:  functionality migration N down -> data migration N down -> functionality migration N - 1 up

2. Discussion about the current architecture and its maintainability in general
  - currently we are using an anti-pattern where the database is doing validation and the UI is doing business logic
  - having a database-heavy architecture is good when we have performance critical operations (e.g. GIS), but that part is already separated to mapmatchingdb, so there's no reason to keep this up with the rest of the database
  - having multiple logical databases will make cross-database validation cumbersome if not impossible
  - currently there's a major risk that noone can really maintain the current functionality implemented in SQL other than a single person, or it takes a major effort to continue with the current style
  - idea: let's implement mutations such that database writes must go through backend microservice(s). Queries however could still work as they do now

2022-10-13 Architecture meeting
-------------------------------

1. State of the timetable data model
  - general overview
  - how do priorities work
  - => priorities are defined on `vehicle_schedule_frame` basis and are evaluated on day and line basis
  - differences to Transmodel

2. Route editing handles, map-matching
  - => loop handling on bi-directional links solved using additional handles


2022-09-22 Architecture meeting
-------------------------------

1. Summary of DoD changes (proposed / decided in slack conversation)
   - => flux-repository update will be automized
   - => update of e2e and dev -branches (and environments) will be automized
   - => corner cases (e.g. new env variables) and breaking e2e tests need to be handled manually
   - => QA will update test env after testing changes in dev env

2. Walk-through of current state of Timetable-module's data model
   - follow-up from last week
   - => discussion about different ways of modelling exceptional days etc
   - => team will get presentation of jore3-timetable module next monday
   - => design might need to be involved again, "special" days and/or priorities need attention


2022-09-15 Architecture meeting
-------------------------------

1. QGIS-fixup-workflow
   - Would HSL traffic planners use git directly?
   - Or should the QGIS-file(s) be exported and sent to the traffic planners (as zip etc)?
   - Or could the changes be imported via a UI feature with a dedicated microservice?
   - => In the MVP-phase, manual exchange of the QGIS-file(s) may be sufficient
   - => HSL's traffic planners need to be involved in the process of deciding this
   - => In later phases, a dedicated microservice with UI feature could be implemented

2. Walk-through of current state of Timetable-module's data model
   - General concepts
   - Impact of Hastus API
   - Impact of using Hastus calendar feature
   - => Using `vehicle journey` vs `service journey`:
     - => routes & lines module only models scheduled stop points
     - => we still may want to be able to model (some cases of) `dead run`s on the time table side
     - => in that sense `vehicle journey` may be the better choice
   - => Some dead runs may need to be modelled, but not all of them
   - => Most dead runs are traffic operators' "own business", i.e. do not affect time tables or compensations
   - => Discussion needs a follow up, either @pallost will organize a separate meeting
   - => Or we will get back to this in slack and / or the next architecture meeting


2022-08-18 Architecture meeting
-------------------------------

1. Identity dependencies between the different modules to build a reasonable roadmap
    - Allows better estimates for taking jore4 into production use
    - To be discussed that how/when to sunset individual modules from jore3 when replaced by jore4
    - While exports like Matkustaja info or GTFS depend on master data that is still only found in jore3, we might not be able to take modules into production use in jore4. E.g. Matkustaja info probably needs all the "routes and lines", "timetables" and "bus stop register" modules to be ready in jore4 before we can do the export.
    - To be discussed whether it's possible to pick some master data from jore3, some from jore4 and create the exports with combining these

2022-08-11 Architecture meeting
-------------------------------

1. Constraint performance problems and possible mitigations
    - constraint checking the routes' integrity (with regards to the journey pattern's stops) is very slow
    - checking a single route's integrity is done in 1-2s
    - this becomes a problem when many (up to 100-200) routes need to be checked when a stop point is modified
    - => first steps to mitigate the problem:
    - => 1. Spinner (already implemented)
    - => 2. Additional text ("this might take a while") shown to user when stops are modified
    - => 3. App gw timeout needs to be increased (currently 30s)
    - => 4. Possibly the checks can be run in parallel batches
    - => later a progress bar can be implemented to indicate progress to the user


2022-08-04 Architecture meeting
-------------------------------

1. Discuss the issues with the typings of the graphql queries on the UI
    - Currently all e.g. `route_route` query results are converted to the same `RouteRoute[]` type
    - This type is too generic, it assumed that the whole graph with all attributes are fetched. This results in incorrect typings
    - Suggestion part 1: let's start using the generated query result types, e.g. `type GetRoutesWithInfrastructureLinksQuery`
    - The problem with this is that it's very deeply nested and can't really use nested types for parameters, like `function myFunction(line: GetRoutesWithInfrastructureLinksQuery['route_route'][0]['route_line]) {}`
    - Suggestion part 2: let's start using the generated fragment types to reference and reuse parts of the typings, e.g. `type LineAllFieldsFragment`
    - Suggestion part 3: as only really few of our queries are actually reusable, let's move their gql definition to the hook/component that's actually using them. The fragments themselves are more reusable, we could keep those in the current `src/graphql` directory
2. Discuss and plan how to clean up the backlog columns: To be deployed (55 tickets), Waiting for QA (5 tickets) and User acceptance testing (12 tickets)
    - Is it possible to have all the tickets that are Done, to Done column before the next sprint planning 16.8.
3. Discuss how to proceed with the new opportunities that the GitHb Projects feature provides
    - Clone and test first?
    - Identify possible risks
    - Consult PO before making updates or changes to the active backlog


2022-06-09 Architecture meeting
-------------------------------

1. Discuss issues related to transferring scheduled stop points from JORE3
    - Infrastructure link association is not correct in a many cases

2022-06-02 Architecture meeting
-------------------------------

**Introduction of QGIS fixup layers**

1. Introduction of `jore4-digiroad-import` repository
    - Short explanation of what is being done with different shell scripts e.g. Digiroad import
    - Walk through work directories containing Digiroad .zip file and shapefiles extracted from the archive file (under `workdir` folder)
    - Show contents of `fixup` folder:
        - QGIS project file
        - GeoPackage file containing actual fix layer definitions (`fixup.gpkg` under `digiroad` subfolder)
2. Walk through different QGIS layers in QGIS fixup project file
    - Background map tiles, currently Digitransit
    - Data from Digiroad shapefiles: infrastructure links and public transport stops
    - JORE4 fix layer group containing layers: `add_link` and `remove_link`
3. Had a glance inside GeoPackage file containing JORE4 fix layers
    - Inspect GeoPackage file as an SQLite database with _DB Browser for SQLite_
    - Walk through mandatory and optional attributes of `add_link` and `remove_link` layers
4. Short explanation of how fix layers in GeoPackage file are incorporated into Digiroad import/export process
    - Explanation of how `link_id` attribute is derived for HSL-specific infrastructure links defined on fix layer
5. QGIS lesson on how new infrastructure links are added to fix layer
    - Walk through essential QGIS tools: _Identify Features_, _Toggle Editing_, _Snapping Tool_, _Add Line Feature_, _Open Attribute_
    - Demonstration of following:
        - Adding a couple of new infrastructure links with attributes entered as well
        - Saving changes
        - Value generation for auto-generated `fid` field (auto-incremented primary key)
        - Editing attributes on fix layer afterwards
    - There was a question on whether speed limit information could be utilised in calculation of route driving times. It was pointed out that speed limits are available through Digiroad but, as of time being, they are not taken into use in JORE4. It was noted that in the future speed limits may be incorporated into JORE4 data model if need be.
6. Discussion of open issues and identified problems
    - The issues were introduced and discussed but there was no time to make decisions. Follow-up discussion will be held in Slack with more details provided and also in further architecture meetings if needed.
    - Open issues
        - Long-term persistence of `link_id` attribute values
        - How HSL-made changes to GeoPackage file should be saved?
            1. Directly to Github repository?
            2. Or is it required to make some validity checks first e.g. with regard to topological integrity by running a script that merges changes?
        - Should an additional layer for public transport stops be added into GeoPackage file?
            - Especially in case a Digiroad road link is marked for removal (on `remove_link` layer) and there exist one or more Digiroad stops along the link
            - Currently, JORE3 Importer depends on public transport stop data provided by Digiroad. Also map-matching service utilises Digiroad stop information to some extent. Hence, removal of Digiroad stops affects scheduled stop points and routes exported to JORE4.
                - It is an open question whether stop processing logic in JORE3 Importer should be changed. Instead of matching JORE3 stops to Digiroad stops (using ELY number as an association key) and taking road link associations and stop directionality from Digiroad, Importer could calculate the same data itself by resolving the closest link to each JORE3 stop using PostGIS functions.
        - How to resolve conflicts between QGIS fixup layer and Digiroad changes in the future?

2022-05-12 Architecture meeting
-------------------------------

**1. Should we keep on using clean Transmodel or rather allow adding our own HSL-specific additions directly (cont'd)**
  - helper views and functions in SQL instead of implementing all business logic in frontend
  - last week we decided to have a follow-up on this issue
  - what was the reactions of the PO and other stakeholders on the presentation made?
  - can we make conclusions for the technical side based on those reactions?

**2. Updating the DoD**
  - Joonas is leaving (18.5.), and this will affect the DoD and the logic that the tickets are moved from column to another in the backlog
  - At the moment there is no further information about the future replacements 
  - How to proceed with the DoD update process?

**3. Robot framework vs alternatives: Current status**
  - there was dicussion about whether robot framework could be replaced by something else
  - the issue was discussed in a separate meeting
  - what is the current status?


2022-05-05 Architecture meeting
-------------------------------

**1. Should we keep on using clean Transmodel or rather allow adding our own HSL-specific additions directly**
  - helper views and functions in SQL instead of implementing all business logic in frontend
  - => "special treatment" of HSL-specific additions has caused some additional work
  - => more general discussion about the expense of using TM ongoing elsewhere
  - => let's get back to this when we've gotten feedback from the PO(s)

**2. What are viable options to mark a route's beginning and end on the route's infra links?**
  - currently start and end stop points are used
  - would it be feasible to use the relative link offset (0..1) instead?
  - how to handle the situation when a stop is moved?
  - what is the "best" way (in different contexts, e.g. kola) to determine a route's length?
  - => it might be good enough to use journey pattern's first and last stops for all applications
  - => no specific case requires keeping start and stop point on route level
  - => more discussion needed about the implications for the users of this in different scenarios
  - => @jschummer will organize a separate meeting in which this can be evaluated together with design


2022-04-28 Architecture meeting
-------------------------------

**1. Should validity_start be nullable in the db? (cont'd)**
  - has a solution been found to avoid duplicate code in qgl queries?
  - => a solution has been found to avoid duplicate code in qgl query filters
  - => there are still other places where the null-possibility of validity_start is taken into account
  - => currently, there is no code changes, which would require additional code because of validity_start's nullability
  - => let's change this, if the need arises (code changes ahead, which require additional work because of this)

**2. Do we need merge commits in the deploy repo?**
  - => not easy to see what happened at merge commits
  - => would be possibly easier to interpret repo history with filtering
  - => only identified use: hotfixes in production
  - => let's let flux repo's branches (dev, prod, etc) follow main branch by rebasing
  - => if there is a need for hotfixes in the future, the exact procedure for those can be revised (force-push vs merge, use of tags, etc)

**3. How to run robot tests and integrate PRs which are dependent on each other and break tests?**
  - run robot tests manually when PRs are waiting for review
  - document and link test run in PR comment
  - integrate all PRs together after review
  - update flux (e2e / dev) after that
  - any way to automate / improve the process?
  - => the window when test runs of unrelated PRs can break is tens of minutes
  - => let's try to keep that window as small as possible by pushing related PRs (almost) at the same time

**4. Should we keep on using clean Transmodel or rather allow adding our own HSL-specific additions directly**
  - helper views and functions in SQL instead of implementing all business logic in frontend
  - => time runs out
  - => let's get back to this next week
  - => please also comment on the slack thread related to costs of using TM


2022-04-21 Architecture meeting
-------------------------------

**1. Ticket cleanup (cont'd):**
  - #424: [Figure out how we want to share files from Azure storage](https://github.com/HSLdevcom/jore4/issues/424)
  - => ticket checked and corrected to meet new requirements

**2. Should validity_start be nullable in the db?**
  - pro: jore3-import contains entities, whose start time is not defined
  - pro: would enable other Jore -using orgs to use e.g. stops without any validity times
  - contra: code needs to be added to frontend-graphql (filters need to take NULL into account)
  - nullability could be disabled (SET NOT NULL) via HSL-specific migration
  - => PR containing duplicated code relating to query filters (validity time) is in review
  - => duplicates might not be solvable only with gql fragments
  - => qgl builder might solve this
  - => alternatively using filter criteria on JS-side of things
  - => a POC will be made as part of the PR
  - => let's get back to this after 1-2 weeks, when the PR has proceeded

**3. Automated testing of frontend**
  - refactoring of frontend difficult, because testing does not ensure things don't break
  - at least robot tests should be added
  - possibly unit tests too
  - with robot tests difficult to choose correct API response to check
  - @jpeuralinna will create ticket to improve robot tests to choose correct api response
  - also @jpeuralinna will organize walk through of how to create / improve robot tests


2022-04-13 Architecture meeting
-------------------------------

**1. How to keep the e2e tests up to date?**
  - UI is introducing changes quite often lately that are breaking the existing e2e tests (~once a week)
  - As agreed previously, there PRs are merged to the main branch of the UI repo but not to the e2e branch of the flux repo
  - All the next PRs in the UI repo are having broken e2e tests until they are fixed
  - Getting the e2e tests fixed nowadays has been taking several days, meaning that for this time we cannot be 100% sure that the new UI PRs we added don't cause regression
  - How to handle the breaking e2e tests? How to improve this process?
  - Should it be mandatory that the developer who creates a PR with breaking changes in the UI repo should also be the one fixing the e2e tests?
  - => e2e tests should be fixed whenever a PR with failing tests is discovered
  - => developer is responsible for fixing it
  - => can of course get help from others, does not necessarily have to fix it herself / alone
  - => fixed tests can be run in jore4-robot -repo with custom branch for verification
  - => @jpeuralinna will organize a workshop on e2e tests for the team

**2. Ticket cleanup:**
  - What to do with the following tickets:
  - #333: [Use multiple Hasura roles on the frontend](https://github.com/HSLdevcom/jore4/issues/333)
  - => could not identify parts not done, asked @pallost for confirmation
  - #344: [Ensure SQL schema up and down migrations complement each other](https://github.com/HSLdevcom/jore4/issues/344)
  - => ticket deemed useful and updated
  - #424: [Figure out how we want to share files from Azure storage](https://github.com/HSLdevcom/jore4/issues/424)
  - => no strong opinions, moved to next week's arch meeting


2022-04-06 Architecture meeting
-------------------------------

**1. How to integrate data model changes specific to certain regions / HSL**
  - own migration folder in hasura repo, seed-data-style
  - docker images build in 3 stages: general -> hsl-specific changes -> seed-data
  - if possible, HSL-specific changes should not be visible in the graphql schema
  - e.g. enum may just be modelled as text referencing an HSL specific table
  - text field is visible in graphql schema
  - validation happens in the background through foreign key constraint
  - => this does not entirely keep up the previous standard "no-one needs to fork when she wants to take it into use"
  - => but it keeps hsl-specific datamodel changes separate and thus allows restoring the state of a general use repository
  - => minus: this does not allow updating the graphql schema with hsl-specific changes
  - => e.g. the "transport target" data cannot be normalized out of the "line" table, since it would not be visible in the graphql schema at all
  - => similarly, no hasura metadata changes could be included
  - => it was agreed that @eleksi will investigate if hasura metadata could be easily modified via diff+patch
  - => this would remove the 2 aforementioned negative points
  - => let's get back to this in slack after the diff+patch investigation


2022-03-30 Architecture meeting
-------------------------------

**1. DoD checkup - contd.**
  - some unclear issues during recent checkup
  - last week's status:
  - @jpeuralinna made a suggestion on how to change UAT related criteria in "in progress" column
  - => fixed / clarified those unclear points

**2. Data model correction**
  - journey patterns do not atm reference abstract stop points ("H1234"), but concrete validity-time-bound manifestations
  - this does not allow to exclude a stop from journey patterns by making its validity time and and start again later
  - problem does not occur between lines and routes
  - => possible solutions:
  - => 1. scheduled_stop_point may reference a separate scheduled_stop_point_label -table, which is also referenced from scheduled_stop_point_in_journey_pattern
  - => 2. scheduled_stop_point_in_journey_pattern may reference scheduled_stop_point labels on a logical level (i.e. without foreign key reference)
  - => number 1. means "normalizing all stop point data, which is invariant with regards to validity and priority, out of the scheduled_stop_point table"
  - => since stop points may be moved to a different location, this invariant data only includes the stop point label
  - => number 2. less optimal, because references can break
  - => @jschummer will try to explore solution 1. (or implement if no obstacles are found)


2022-03-23 Architecture meeting
-------------------------------

**1. Should the ticket numbers be always marked in the commit message? - follow up**
  - What's our opinion now?
  - => only number in commit title is faster to write than whole issue id (hsldevcom/jore4#xxx)
  - => full issue id is more useful, because it allows bidirectional linking of issue<->commit
  - => let's try to remember to mention the issue id (hsldevcom/jore4#xxx) in the commit message body
  - => if a commit is not directly related to any issue, this is not enforced

**2. Deployment-process discussion and walk-through**
  - deployment process
    - which envs in which order
  - deployment of related changes in different components
    - is flux main branch allowed to break?
  - => @pallost gave a walk-through
  - => if a change is noticed in a repo, an "auto-update" PR is created automatically, which will integrate the change into the flux main branch
  - => after a change is present in the flux main branch, "auto-deploy" PRs will be created automatically to deploy the change to the e2e and dev branches
  - => whenever an "auto-update" or "auto-deploy" -PR is seen with succeeding tests, it can be safely accepted and integrated
  - => "auto-deploy" should be integrated with a merge commit, not by re-basing

**3. NetEX nordic profile**
  - should base future data model work on NetEX nordic profile instead of vanilla Transmodel
  - => important with stops and timetables
  - => nordic profile documentation also easier to read than TM standard

**4. DoD checkup**
  - some unclear issues during last week's checkup
  - => @jpeuralinna will make a suggestion on how to change UAT related criteria in "in progress" column
  - => ran out of time, let's discuss this next week again


2022-03-16 Architecture meeting
-------------------------------

**1. Should the ticket numbers be always marked in the commit message? - follow up**
  - What's our opinion now?
  - => possible to mark issue in commit message for github to link them, even if they are in different repos
  - => https://stackoverflow.com/questions/1687262/link-to-the-issue-number-on-github-within-a-commit-message/6742691
  - => Let's go through this next week again, maybe someone has tried until then

**2. Walk-through of most important aspects of data model**
  - how do route and journey pattern relate to each other
  - what do vehicle mode and vehicle submode mean


2022-03-09 Architecture meeting
-------------------------------

**1. All-rounder conversation: What are team members' tech interests and current know-how?**
  - => discussed team members' interests
  - => needs follow up, @ellinooraikaheimo will organize follow-up discussion

**2. Idea: Should the ticket numbers be always marked in the commit message?**
  - Allows to quickly determine if ticket's content has gone into release
  - could be marked into commit message title for quick search
  - or into commit message with github magic words "resolves:" (?) for automatic ticket processing
  - => general opinion: might not be overly useful, but might have some valid use cases
  - => putting the ticket number into the commit title allows quick skimming of commits for ticket number
  - => ...but causes the commit title length to be even more restricted
  - => not all commits necessarily refer to a (single) ticket, no point in forcing mentioning a ticket number
  - => let's suggest this to the rest of the team on slack
  - => some repos can already try this if all committers are willing to do so

2022-03-02 Architecture meeting
-------------------------------

**1. Localization solutions**
  - Options listed in [datamodel sketching document](https://github.com/HSLdevcom/jore4/blob/main/wiki/data-model-sketching.pdf):
    - Option #1 (same as Jore3): Separate columns for each localized string
    - Option #2: Use JSON(B) to store localizations
    - Option #3: Separate tables for localized strings
    - Option #4a: Separate codeset and localization tables for attribute names
    - Option #4b: Separate codeset and localization tables for attribute names (with UUID)

  - => general requirements:
    - system should be usable without forks in many (global) contexts
    - user should be able to use dictionary which offers suggestions when filling in new translatable text fields
    - dictionary does not need to be editable in such a way that all text that was once suggested for fields, would change all at once
    - => text can be copied when user accepts a suggestion
    - text can be suggested to the user one field at a time
    - i.e. we do not need to suggest translation tuples (for e.g. full name and short name) together
    - should have the option to suggest field-specific translations only
    - i.e. only previously used bus stop names would be suggested for a new bus stop's name
  - => preliminary conclusions regarding different solution options:
    - => Option 1 does not support the idea of making Jore4 a system which can be used in many global contexts without the need to fork (since supported languages would be hard-coded into the db schema)
    - => Option 2 is flexible, but does not allow to make any assumptions about the localization data stored (e.g. client bugs / quirks can lead to unusable localization data)
    - => Option 3 is flexible and allows for a robust DB schema, but does not allow adding translation context information (called 'codeset' in the datamodel sketching document)
    - => Options 4a and 4b seem to support all requirements. Context information can be added as needed in separate columns in the localication table.
  - => No definite choice can be made yet, since we need to get back to the datamodel sketching document once more to double check the suggestions
  - => @jschummer will open a slack thread for further discussion / agreeing on a localization option / planning further meetings on the topic

2022-02-16 Architecture meeting
-------------------------------

**1. Discuss what UI features to be tested with E2E tests and what with UI unit tests.**
  - Create general guideline
  - Create issues to kanban board with missing UI unit tests
  - e2e-tests slow
  - e2e-environment setup resource intensive, mem might run out
  - should there be integration tests?
  - => with same effort of maintaining unit or integration tests, we can also maintain e2e-tests
  - => unit tests might be justified:
    - for important unitility functions
    - for other non-interactive functionality
  - => let's add some guidelines regarding this to DoD

**2. Walk-through of Jore3-importer problems and possible solutions**
  - possible solution might be to have two separate spring batch jobs / configurations
  - how to trigger second job when first one has finished?
  - might be separate REST API to start jobs
  - original problem: statistics about failed import rows uses optimistic locking and this gets mixed up with chained transaction managers
  - second possible solution: implement own dummy statistics writer, which does not do anything (or does not use optimistic locking)
  - => if in-memory stats writer solution works, could be added to existing PR to make the jore3 importer work again
  - => if in-memory stats writer does not work, an SQL function could be added to an internal schema (only for jore3-importer's use)

**3. How to identify dependencies of changes in different microservices**
  - should we move the datamodel (hasura) version backwards in the e2e bundle?
  - could make jore3 importer features available more early
  - possible risk that jore3 importer features will change and the early features cannot be used as such later
  - => jore3 importer can pin hasura version in own environment if needed
  - => lately added automatic e2e-deployment-PR will help to keep version in sync


2022-02-09 Architecture meeting
-------------------------------

**1. Let's discuss how to maintain microservice orchestration**
  - How do we ensure that all the microservices are compatible with each other? E.g. by adding a new NOT NULL constraint into Jore4 database we possible break compatibility with Jore4-Jore3-Importer.
  - should we have separate testing for jore3-importer-compatibility of changes in e.g. hasura repo or map-matching-api
  - jore3-importer special case:
    - only one which has db-level-deps with other component (hasura)
    - might not be in use in 6 months anymore
  - could importer be started (and/or importer-integration-tests be run) in e2e tests?
  - atm jore3-importer run succeeds, even though not a single row was written
  - e2e-test runs from importer repo
  - => @pallost creates ticket to include import integration test runs in e2e tests
  - => when data model changes, importer dev should be contacted to make sure they don't break anything
  - => include date in docker tags

**2. Which frontend logging/monitoring system to use**
  - https://github.com/HSLdevcom/jore4/issues/565
  - azure app insights alternative to sentry, same functionality
  - sentry more commonly used
  - app insights would be good, because everything would be in the same place (kube docker logs _should_ go there in the future)
  - with both, analytics data gathering must be taken into account (GDPR)
  - => let's ask customer's preference
  - => if no preference, let's use azure app insights

2022-01-26 Architecture meeting
-------------------------------

**1. How to model Transmodel (TM) route and service/journey pattern in data model and UI**
  - data model supports multiple TM journey patterns under the same TM route
  - in current UI designs, both are combined to a "UI route"
  - the different aspects of TM route and TM journey pattern are not visible to the user
  - according to one interpretation the TM, the route uniquely describes the path "the vehicle takes"
  - cases which may be modeled as a separate TM journey pattern under the same TM route:
    1. same bus number has 2 different variants starting from the same stop, but ending at different stops (one approaches a subset of stops of the other)
    2. a train line does not stop at a certain station during a certain period (but other train lines do approach the same stop - and platform - during that period)
  - => case i. may be modelled as a different TM route + journey pattern
      - this would also correspond to the TM interpretation of the TM route mentioned above (since bus does not drive the full route in both cases)
      - may bring challenges in analytics, since it may be wanted that the two TM routes' data is attributed to the same entities
  - => case ii. may be modelled as a separate temporarily valid TM route
      - not optimal, since TM route data is duplicated
      - this situation does not arrise very often, so it may not justify extending the UI for modelling multiple TM journey patterns under the same TM route
  - => the data model should maintain the possibility to have multiple TM journey patterns under the same TM route in the future
  - => currently, there is no need to support this case in the UI
  - => can use a unique index to prevent multiple TM journey patterns under the same TM route in the data model for now
  - => unique index can be removed and data model and UI can be extended to support this case later
  - => open questions regarding the data model should be documented in an adequate place in order to be able to get back to them later
      - @jschummer will establish a document for them and inform the team about the place


2022-01-19 Architecture meeting
-------------------------------

**1. How to use direct CSS styling mixed with tailwind classes**
  - should we define own classes?
  - in globals.css or on a more fine-grained level?
  - => define all css definitions separately by extending the tailwind theme in `tailwind.config.js`
  - => some definitions may be applied globally in `globals.css`
  - => let's create new shared components whenever common (layout) elements have been identified

**2. How to share graphql queries and query results between components**
  - => Apollo contains own state which caches query results
  - => should not need to keep own state of query results

**3. How to share a dump of Jore3 data between developers**
  - developers should not need to run jore3-importer by themselves (too often)
  - run needs VPN and SSH tunnels and takes a long time to complete (when run for the first time)
  - => CI could create own hasura images containing seed data
  - => @pallost will check this


2021-12-15 Architecture meeting
-------------------------------

**1. Common coding style & conventions and best practices**
  - may be different for different repositories / languages
  - => would be good to obey same coding style in same repo
  - => somwhat functional coding style in Jore3 importer (vavr, immutable)
  - => @pkainulainen-vincit will add mentioned in README
  - => UI elements would benefit from IDs
  - => should be added for important elements, @jschumme will add mention to README

**2. Map-matching mismatch on beginning and ending links**
  - when beginning and endling links don't have stop points, they may be mismatched
  - => possible solution: in addition to stop point matching, also match cross roads
  - => @jarkkoka is implementing this currently


2021-11-24 Architecture meeting
-------------------------------

**1. Time zone information in DB (cont'd)**
  - main objectives
  - storing time zone info vs
  - storing UTC offset vs
  - using PostgreSQL's `TIMESTAMP WITH TIME ZONE` vs
  - storing time with implicit UTC offset / time zone context
  - => 3 different use cases for timestamps in the project were identified:
    - 1. validity period of lines, routes, and stops (now under work)
    - 2. timetable information
    - 3. change logging
  - => best solution for validity periods:
    - `TIMESTAMP WITH TIME ZONE`, which stores absolute time in UTC
    - this way the system can interpret and react to the information contained in the validity period columns
    - additionally real time zone information, e.g. `Europe/Helsinki`
    - this allows the frontend to convert the absolute (UTC) timestamps in the database to the appropriate local time to display to users
    - at this stage of the project, it is sufficient to store the real time zone info in constants in well-chosen places (e.g. frontend and jore3-importer)
    - at a later stage, entities storing timestamps may be required to store the real time zone information as well
    - the time zone information used to display timestamps to the user must not be taken from the user's browser information
    - (the user should see timestamps interpreted according to the place in which the entity under work resides, not where the user (seems to) reside)


2021-11-17 Architecture meeting
-------------------------------

**1. Discuss how to fix e2e environment setup
  - if microservices introduce a new environment variable / secret, the CI jobs will fail
  - https://github.com/HSLdevcom/jore4/issues/538
  
**2. DoD recap**
  - critical points as tasks in board tickets' task list
  - deploying - when and how

**3. Demo of jore4-mssql-testdb and jore4-postgres repos**
  - @pallost will present how things work

**4. Time zone information in DB**
  - main objectives
  - storing time zone info vs
  - storing UTC offset vs
  - using PostgreSQL's `TIMESTAMP WITH TIME ZONE` vs
  - storing time with implicit UTC offset / time zone context


2021-11-10 Architecture meeting
-------------------------------

**1. Review practice**
  - maybe not necessary to ask for new review if only findings were typos or similar?
  - for every review, open a slack thread
  - ping reviewer / author when review proceeds
  - => @jschummer will update review document to reflect these updates

**2. Microservice walk-thorugh**
  - @pallost will be away for some weeks
  - let's check if there is the need to improve our understanding of some of his or others' work
  - => jore4-mssql-testdb and jore4-postgres were identified as repos where more understanding is needed
  - => @pallost will demo them next week

**3. How to model high priority versions "overriding" lower priority versions**
  - e.g. lines, routes, stops
  - should there be a separate "entity ID" on top of priority-bound row ID?
  - => candidates for partial keys (in addition to priority and validity time window):
  - => scheduled_stop_point: label
  - => route: label + direction
  - => line: label


2021-11-03 Architecture meeting
-------------------------------

**1. Presentation on how to use docker-compose bundle Hasura for local repo migrations**
  - => docker-compose-bundle was demoed
  - => how to add use of bundle to new repos
  - => new Hasura migrations can be applied by adding volume mount to Hasura docker image
  - => alternatively, can use Hasura cli (`hasura migrate`) to apply and check migrations

**2. Recap: Has the current way of ticket walk-through and check-lists in tickets been working well?**
  - => mainly positive feedback, ticket walk-through makes a lot of sense
  - => small(ish) negative aspect of using tasks: It's not possible to track who is working on which task in case there is multiple assignees
  - => will continue to re-evaluate this way of working


2021-10-27 Architecture meeting
-------------------------------

**1. Discuss whether we separate something to own databases (ticket #511)**
  - => Use of microservice specific logical databases should be employed where useful
  - => jore3-importer and auth-backend will get their own logical databases (not postgresql instances)
  - => see ticket #516
  - => closed ticket #511, since it is not needed anymore

**2. Presentation on how to use docker-compose bundle Hasura for local repo migrations**
  - => Would be good to have @pallost explain this, since he created the docker-compose bundle generation
  - => Moved to next week


2021-10-20 Architecture meeting
-------------------------------

**1. Check how far we are with route support: https://github.com/HSLdevcom/jore4/issues/331**

**2. Presentation on how to use the docker-compose bundle from the flux repo**


2021-10-13 Architecture meeting
-------------------------------

**1. Routing to microservices**
  - should the ingress allow routing directly to individual microservices or... (easier implementation and less maintenance in case REST API changes)
  - should we expose the microservices' REST API through Hasura (better auditing and authorization control)
  - => create ticket to study / create PoC about using map-matching backend through Hasura (via remote schema)

**2. Repository / component walk-through to identify testing needs**
  - => map-matching backend could be tested through robot tests
  - => after data model deployment, can send real request via UI to Hasura (including auth backend)
  - => jore3 importer can be tested later in e2e tests (when it's ready)

**3. How can services be decoupled on database level?**
  - own schemas, which are (not?) created by Hasura and managed by microservice
  - completely own db
  - => create ticket to separate different microservices' internal data into own DB


2021-10-06 Architecture meeting
-------------------------------

**1. Hasura**
  - data encapsulation using views and INSTEAD OF triggers
  - soft delete implementation using views and BEFORE DELETE triggers
  - => some limitations, such as ON CONFLICT not working
  - => might later decide to just expose all internal_-schemas and not use INSTEAD OF triggers

**2. Local development database**
  - some db conf is needed locally but not in the clound (because deploy scripts create e.g. db users)
  - own image could be used in local development, which has those configs "built in"
  - => @pallost will create ticket to implement this


2021-09-15 Architecture meeting
-------------------------------

**1. Level of documentation in different components**
  - jore3-importer needs better docs?
  - => not possible to write down design intentions after the original author has left
  - => @pkainulainen-vincit will write documentation as good as possible
  - other important components
  - => DoD contains already documentation requirement, let's keep that in mind in the future (also reviewers should check!)
  - different environments of operation (kube, kind, docker) to be documented
  - => should be documented in a centralized spot (architecture docs)
  - => @jschummer will take responsibility (together with @pallost)
  - summary of discussion outcome for the whole team in slack

**2. Is the DoD still up to date?**
  - testing could be taken into account better
  - testing step should / could be pictured by its own ticket on the board
  - => @jpeuralinna will ask PO about this when he gets back from holiday


2021-09-08 Architecture meeting
-------------------------------

**1. Project testing process**
  - Kind-cluster needed anymore?
  - => docker-compose can be used in github actions
  - in which environment should (different sets of) e2e-tests be run?
  - => can be run in e.g. `dev`-kube env
  - => need to solve how to to either access db or trigger tests on container which is running inside kube
  - => will create ticket to study which one is more feasible

**2. Deployment process**
  - what (version?) information is contained in the flux main branch
  - => flux-repo should always refer to other components' main-branch docker-builds

**3. Dependabot**
  - should it be used and how?
  - => atm only in hasura
  - => will generate lots of PRs
  - => if configured to run only once a month, once a month we'll get lots of PRs
  - => should try with one repository, will create ticket about this

**4. (Kubernetes) autoscaling**
  - => some cases have been seen, in which resources were not sufficient in Kube
  - => for now we can increase number of vms, later can take autoscaling into use


2021-09-01 Architecture meeting
-------------------------------

**1. Development model using (generated) docker-compose environment**
  - docker-compose file will be generated from flux config
  - can be edited to local development needs
  - either build docker image locally or refer to local host instance of microservice

**2. digiroad-import-experiment presentation**
  - R and K digiroad maps supported
  - docker image to provide database for import
  - repository contains script to export data for use by map-matching-server
  - data used by map-matching-server and application (hasura) can get out of sync
    - => @jschummer will create ticket to explore possibility to use postgresql foreign data wrapper to have only a single source of 
       truth
  - scripts will be extended to include import into hasura db

**3. Project testing process**
  - => Postponed


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
