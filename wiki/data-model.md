This file aims to document the Jore4 data model and open questions regarding future development of the data model.

Open questions
==============

This is the list of open questions regarding the data model, which are to be taken into account and / or solved in the future:

1. How can a temporarily closed / out of order stop point in a service pattern be modelled?
   - e.g. ending stop point's validity time and creating a new one whose validity time starts in the future?
   - what about a stop point not being accessable only by certain journey patterns?

2. How to model a stop point, whose usability / access is limited (e.g. wheel chair access not possible)?
   - stop point register's data should be connected to route and line module's data?

3. How to model versions of the same route with different end stops? (
   - example: route1 = stop A -> stop B, route2 = stop A -> stop C
   - are they different routes with different labels (1a, 1b, 1c, etc), each having a single journey pattern
   - or are they a single route (1) with multiple different journey patterns

4. Multilingual strings
   - Option #1 (same as Jore3): Separate columns for each localized string
   - Option #2: Use JSON(B) to store localizations
   - Option #3: Separate tables for localized strings
   - Option #4a: Separate codeset and localization tables for attribute names
   - Option #4b: Separate codeset and localization tables for attribute names

General principles
==================

It has been anticipated that different organizations in different countries might be interested in using Jore4. For Jore4 to be a viable choice in many different environments, its data model has to be designed to be as generic as possible, while still supporting the Jore4 objectives as well as needed.

The Jore4 system has been divided into separate modules, which should be coupled as loosely as possible. This has the advantage of making them potentially exchangable in the future, without having to renew the whole system at once. At the time of writing only the "Routes and Lines" module has reached the implementation stage.

Postgresql & Hasura
-------------------

Postgresql has been chosen to be the database, with Hasura being the GraphQL engine. Hasura generates the GraphQL schema from the database schemass according to its configuration.

Generally, all database schemas of the Jore4 database should be exposed to the GraphQL schema, except the ones prefixed with `internal_`. These can contain e.g. "raw" tables, whose data may be exposed to the GraphQL schema via a view (also see [below](#exposing-data-via-views)) or may not be directly exposed at all.

Array and object relationships have been added in the Hasura configuration in many places wherever seen necessary.

Exposing data via views
-----------------------

In some cases, the data contained in the tables should be transformed or enriched when accessed via the GraphQL schema. For this, database views can be used. The view can be made modifyable by providing `INSTEAD OF` tiggers. This view is then exposed in the GraphQL schema.

One example for this technique is the `internal_service_pattern.scheduled_stop_point` table, which is not exposed as such. Instead, the view `service_pattern.scheduled_stop_point` has been created, which provides the data from the `internal_service_pattern.scheduled_stop_point` table together with calculcated geometry data.

Constraints
-----------

The integrity of the data needs to be validated at database level, since no separate backend code can provide this functionality. Database constraints are used for this purpose.

Not all validation can be done via readily provided constraints (such as not-NULL-constraints or exclusion constraints). Therefore trigger functions have to be employed in those cases, in which it is not possible to perform validation via builtin constraints. The trigger functions are installed as deferred constraint triggers on row level and the transaction isolation level is set to 'serializable'. The most complex checks are performed by the route verification constraints, for more information see [Route verification](#route-verification) below.

In case the transaction isolation level is seen to cause too big a performance hit, it can later be lowered. In that case, the integrity of the data is no longer guaranteed due to possible race conditions in concurrent access situations. But the likelihood of such corruption occurring can be thought to be very low.

Data type considerations
========================

Geography
---------

In order to support geographic locations of large scale, the system should not be limited to using only a single SRID throughout the whole system. Therefore, the PostGIS `geography` type was chosen to be used within the data model wherever feasible.

When the need to use a function supporting only the `geometry` type arises, the `internal_utils.determine_SRID` functions should be used to determine the SRID for the conversion to the `geometry` type. The `internal_utils.determine_SRID` functions have been created to provide an abstraction layer to the PostGIS internal `_ST_BestSRID` functions, which are not documented and may be deprecated in the future. When this happens, the `internal_utils.determine_SRID` functions serve as single point of failure and can be changed to adopt a different way of determinining the most suitable SRIDs for the given `geography` parameters.

Timestamp with time zone
------------------------

The system should be able to perform actions at times relating to the timestamps contained in the data base. (For example, a notification email might be sent prior to an entity's validity time expiration.) This means the system needs to be able to interpret the timestamps as absolute points in time, making the `timestamp with time zone` type the natural choice.

However, it needs to be taken into account that the time zone applied when times are displayed to the user, is most likely not UTC and might not be the same as the user's web browser's time zone. In fact, in many cases the displayed entity's actual location should be used to determine the time zone to be applied. This way the user can be displayed the time according to the entity's local time. (In a simplified version, the same constant time zone might be used to display user information. This will serve the system as long as all entities processed by the system live in the same time zone and can later be extended to cover cases in which the system spans multiple time zones.)

Routes and lines
================

Transmodel
----------

The data model is somewhat closely based on parts of the [Transmodel (TM) specification](https://www.transmodel-cen.eu/model/index.htm). In general, TM Models have been modelled as individual database schemas, TM Classes and TM Associations have been modelled as tables and views, with columns corresponding to TM Class Attributes. However, it should be noted that only those parts of the Transmodel have been modelled, which are of relevance to the Jore4 system.

Also, there have been cases, in which these basic TM modelling principles were seen not to support the system's operational capabilities. Thus, additions and adoptions had to be made, some of the most important being:

- The line, route, and scheduled stop point entity are considered "core entities" and their instances have a validity time and priority assigned to them. (See section [Validity times and priorities](#validity-times-and-priorities) below.)
- A route can have only a single journey pattern. This is enforced by a constraint in the current implementation and may be changed in the future.
- An actual journey pattern valid at a certain point in time has to be determined by evaluating the linked scheduled stop points' highest priority instances at that point in time (see [Validity times and priorities](#validity-times-and-priorities) below.)

Validity times and priorities
-----------------------------

The line, route, and scheduled stop point entities are considered _core entities_ and have a validity time and priority associated with their instances. The priority determines which instance is the one "in effect" at a given point in time, if at that point in time there is more than one valid instance.

These core entities have a 'label' property, which serves as the unique identifier of the entity. It has to be unique within the scope of the entity instances' validity times and priorities. The label is a user-readable, non-translatable character sequence. E.g. in case of a bus line, the `label` column of the line table contains the line's real world bus line number.

As a result of the identification of entities via their label, journey patterns do not reference scheduled stop point instances directly, but rather refer to the scheduled stop point entity via the `scheduled_stop_point_invariant` table. This indirection allows the journey pattern to automatically reference the highest priority stop point instance at any given point in time. Other hand this means that it is necessary to consider all scheduled stop point instances with stop point labels referenced by a journey pattern (and valid at a given point in time) in order to determine the actual TM journey pattern at that point in time. For more details, also see [Route verification](#route-verification) below.

As opposed to the `label` column, other descriptive columns of entity tables may be translateble and are not necessarily unique in any context. E.g. the line table's 'short_name_i18n' column is not required to be unique in any context on database level, even though from a user perspective it may contain information unique to the line.

Route verification
------------------

