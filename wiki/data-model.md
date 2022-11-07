This file aims to document the Jore4 data model. If there are open questions regarding future development of the data model, you can add them in their own section at the beginning of this document.


Open questions - timetables
===========================

- should "special days" (e.g. holidays or other days with exceptional traffic) be modelled as plain "higher priorities", as the priorities in routes and lines?
- if not, is there any other use for priorities?
- possible timestamp format to allow for >24h times: timestamp + day offset (integer)
- need to question whether timestamp should include tz information
  - => on one hand, the timestamp is always to be seen as the time at the stop, so tz info is intrinsic
  - => but: does the system potentially need to react to the timetable times?


General principles
==================

It has been anticipated that different organizations in different countries might be interested in using Jore4. For Jore4 to be a viable choice in many different environments, its data model has to be designed to be as generic as possible, while still supporting the Jore4 objectives as well as needed.

The Jore4 system has been divided into separate modules, which should be coupled as loosely as possible. This has the advantage of making them potentially exchangable in the future, without having to renew the whole system at once. At the time of writing only the "Routes and Lines" module has reached the implementation stage.

For example the stop module's data is to be kept separate from the "routes and lines" module, even though both modules model aspects of the same physical scheduled stop points. Where needed, the data of both modules could then be joined via their ids. Joining the data could be implemented on the graphql API level.

Postgresql & Hasura
-------------------

Postgresql has been chosen to be the database, with Hasura being the GraphQL engine. Hasura generates the GraphQL schema from the database schemass according to its configuration.

Generally, all database schemas of the Jore4 database should be exposed to the GraphQL schema, except the ones prefixed with `internal_`. These can contain e.g. "raw" tables, whose data may be exposed to the GraphQL schema via a view (also see [below](#exposing-data-via-views)) or may not be directly exposed at all.

Array and object relationships have been added in the Hasura configuration in many places wherever seen necessary.

Exposing calculated data in the GraphQL schema
----------------------------------------------

In some cases, the data contained in the tables should be transformed or enriched when accessed via the GraphQL schema. For this, database views had been used originally. The views were made modifyable by providing `INSTEAD OF` tiggers and were then exposed in the GraphQL schema. One example was the `internal_service_pattern.scheduled_stop_point` table, which was not exposed as such. Instead, the view `service_pattern.scheduled_stop_point` had been created, which provided the data from the `internal_service_pattern.scheduled_stop_point` table together with calculcated geometry data.

However, later it was discovered that the maintenance overhead of this techniqe was rather high. This was due to every change in the underlying original table requiring changes to be incorporated into the view and the affected `INSTEAD OF`-triggers.

Instead of exposing computed data via views, all tables are now exposed directly and are enriched via Hasura's calculated fields. For example, the `service_pattern.scheduled_stop_point` view was removed and the `internal_service_pattern.scheduled_stop_point` table was moved to the `service_pattern` schema instead and exposed in the GraphQL API. The `closest_point_on_infrastructure_link` field, which was previously computed as a view column, is now included in the generated GraphQL queries as a computed field.

Constraints
-----------

The integrity of the data needs to be validated at database level, since no separate backend code can provide this functionality. Database constraints are used for this purpose.

Not all validation can be done via readily provided constraints (such as not-NULL-constraints or exclusion constraints). Therefore trigger functions have to be employed in those cases, in which it is not possible to perform validation via builtin constraints. The trigger functions are installed as deferred constraint triggers on row level and the transaction isolation level is set to 'serializable'. The most complex checks are performed by the route verification constraints, for more information see [Route verification](#route-verification) below.

In case the transaction isolation level is seen to cause too big a performance hit, it can later be lowered. In that case, the integrity of the data is no longer guaranteed due to possible race conditions in concurrent access situations. But the likelihood of such corruption occurring can be thought to be rather low.

Data type considerations
========================

Geography
---------

In order to support geographic locations of large scale, the system should not be limited to using only a single SRID throughout the whole system. Therefore, the PostGIS `geography` type was chosen to be used within the data model wherever feasible.

When the need to use a function supporting only the `geometry` type arises, the `internal_utils.determine_SRID` functions should be used to determine the SRID for the conversion to the `geometry` type. The `internal_utils.determine_SRID` functions have been created to provide an abstraction layer to the PostGIS internal `_ST_BestSRID` functions, which are not documented and may be deprecated in the future. When this happens, the `internal_utils.determine_SRID` functions serve as single point of failure and can be changed to adopt a different way of determinining the most suitable SRIDs for the given `geography` parameters.

Validity times
--------------

Validity times were originally modelled using the `timestamp with time zone` data type. It was thought that the system would need to be able to react to validity time changes autonomously, e.g. by sending out notifications to users when a validity time span reaches its end. This would make it necessary to define an exact point in time (hence `timestamp`) with an absolute time relation (hence `with time zone`).

However, exact points in time did not turn out to be a good solution. The user interface was designed to let the user handle validity dates only, which led to problems when the UI generated timestamps from a user input date. A `date` type seemed to be the more natural choice, since it does not offer addition of "best guess" exact times to the actual user input values. The `date`type also removed the need for an absolute time relation, since the system cannot in any case autonomously determine an absolute point in time at which to react.

Validity times, which are now modelled as local timezone `date`s, should be interpreted to be local dates of the entities they are affecting.

Localizable strings
-------------------

Localizable strings are modelled via JSON fields in the respective database entities. This solution was seen to be sufficiently stable and provides easy access to the localized data for the UI and other components. Also, JSON fields do not impose as much overhead as separate translation tables.

Routes and lines
================

Transmodel
----------

The data model is somewhat closely based on parts of the [Transmodel (TM) specification](https://www.transmodel-cen.eu/model/index.htm). In general, TM Models have been modelled as individual database schemas, TM Classes and TM Associations have been modelled as tables and views, with columns corresponding to TM Class Attributes. However, it should be noted that only those parts of the Transmodel have been modelled, which are of relevance to the Jore4 system.

Also, there have been cases, in which these basic TM modelling principles were seen not to support the system's operational capabilities. Thus, additions and adoptions had to be made, some of the most important being:

- The line, route, and scheduled stop point entity are considered "core entities" and their instances have a label, validity time and priority assigned to them. (See section [Validity times and priorities](#validity-times-and-priorities) below.)
- A route can have only a single journey pattern. This is enforced by a constraint in the current implementation and may be changed in the future.
- An actual journey pattern valid at a certain point in time has to be determined by evaluating the linked scheduled stop points' highest priority instances at that point in time (see [Validity times and priorities](#validity-times-and-priorities) below.)

Validity times and priorities
-----------------------------

The line, route, and scheduled stop point entities are considered _core entities_ and have a validity time and priority associated with their instances. The priority determines which instance is the one "in effect" at a given point in time, if at that point in time there is more than one valid instance.

These core entities have a 'label' property, which serves as the unique identifier of the entity. It has to be unique within the scope of the entity instances' validity times and priorities. The label is a user-readable, non-translatable character sequence. E.g. in case of a bus line, the `label` column of the line table contains the line's real world bus line number. (Transmodel features a `label` attribute for scheduled stop point entities, which served as the inspiration for the Jore4 label property. Note, however, that TM does not specify a label property for routes or lines and that the TM scheduled stop point label is meant to be translatable.)

As a result of the identification of entities via their label, journey patterns do not reference scheduled stop point instances directly, but rather refer to the scheduled stop point entity via the `scheduled_stop_point_invariant` table. This indirection allows the journey pattern to automatically reference the highest priority stop point instance at any given point in time. Other hand this means that it is necessary to consider all scheduled stop point instances with stop point labels referenced by a journey pattern (and valid at a given point in time) in order to determine the actual TM journey pattern at that point in time. For more details, also see [Route verification](#route-verification) below.

Validity times allow modelling the situation of an entity being "out of order" for a certain time by making an instance's validity end when the entity is put out of order and creating another instance, whose validity starts when the "out of order" situation is resolved. If e.g. a scheduled stop point with label A cannot be used for the duration of construction work, it's scheduled stop point instance's validity end time should be set to the starting point in time of the construction work. Then another stop point instance with label A should be created, whose validity start time is set to the point in time when the construction work ends.

As opposed to the `label` column, other descriptive columns of entity tables may be translateble and are not necessarily unique in any context. E.g. the line table's 'short_name_i18n' column is not required to be unique in any context on database level, even though from a user perspective it may contain information unique to the line.

Route verification
------------------

Since the route and journey pattern are stored separately from each other in the Jore4 data model (as specified in Transmodel), it is advisable to check that a journey pattern referencing a certain route is compatible with that route. In other words, it should be ensured that all scheduled stop points of the journey pattern can be reached when traversing the route. This implies that the following conditions are met:
  - all links, on which the stop points reside, are included in the route
  - those links are traversed in the same order in which they appear in the journey pattern
  - each of those links is traversed in a direction, which allows approaching the stop point residing on it

Since the same route instance references all scheduled stop point instances with the same label, the check should also ensure that all of the above hold at all points in time when the route is valid, i.e. the above must hold for all scheduled stop point instances referenced.

This aspect in turn has to pay attention to the fact that as stated above, only the instance with the highest priority at a certain point in time is considered valid. In other words, if a scheduled stop point entity has an instance with high priority "overriding" a lower priority instance, then for the time span of overriding, only the higher priority instance is considered valid and only that one should be included in the route verification. But for the remaining validity time of the lower priority stop instance (when it is not "overridden"), the lower priority instance is considered valid and for that part should be included in the route verification for that time span.

The same concept is applied to routes themselves. If a higher priority route instance overrides a lower priority route instance for part of its validity time, the lower priority route is not considered valid - and thus should not be verified - for the time it is being "overridden".

This concept allows modelling temporary (= high priority) stops residing on infrastructure links, which are only included in a temporary (= high priority) instance of the route, but not in the route's basic version. As an example, consider the following basic route version:

![basic-route](https://user-images.githubusercontent.com/77336519/200253786-88313187-e898-4187-b335-f8a8303135c7.png)


If that route has to deviate from its usual course for the duration of a construction site, a high priority instance of that route is created following the new course and which is valid for the duration of the construction site. Additionally, the user can create a high priority instance of a stop point residing on the area of the construction site, which is also valid for the duration of the construction site. With this setup, the temporary route may reference the stop point entity, because for the duration of the construction site, the high priority stop point is on the path of the high priority route and for the remaining time, the (then valid) lower priority stop point is on the path of the (then valid) lower priority route:

![temp-route](https://user-images.githubusercontent.com/77336519/200253788-67719bdd-5d6b-4e59-919c-c6a524ba0622.png)


Without the concept of priority-based validity, it would be unclear which of the two stop point instances are referenced by each instance of the route (high and low priority) and it could therefore be concluded that some instances of the stop point can not be reached when traversing either instance of the route.

An exception to the concept of priority-based validity are draft-priority instances, which are not considered valid, even though their numerical priority is higher than e.g. a basic version instance's priority. Therefore draft-priority instances are not included in the route verification at all.
