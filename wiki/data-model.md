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

General principles
==================

It has been anticipated that different organizations in different countries might be interested in using Jore4. For Jore4 to be a viable choice in many different environments, its data model has to be designed to be as generic as possible, while still supporting the Jore4 objectives as well as needed.

Data types
==========

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

The data model is somewhat closely based on parts of the [Transmodel (TM) specification](https://www.transmodel-cen.eu/model/index.htm). In general, TM Models have been modelled as individual database schemas, TM Classes and TM Associations have been modelled as tables and views, with columns corresponding to TM Class Attributes. However, it should be noted, that only those parts of the Transmodel have been modelled, which are of relevance to the Jore4 system.

Also, there have been cases, in which these basic TM modelling principles were seen not to support the system's operational capabilities. Thus, additions and adoptions had to be made, some of the most important being:

- The line, route, and scheduled stop point entity are considered "core entities" and have a validity time and priority assigned to them. (See section [Validity times and priorities](#validity-times-and-priorities) below.)
- A route can have only a single journey pattern. This is enforced by a constraint in the current implementation and may be changed in the future.

Validity times and priorities
-----------------------------

The line, route, and scheduled stop point entity are considered _core entities_ and have a validity time and priority associated with them. The priority determines which entitity is the one "in effect" at a given point in time, if at that point in time there is more than one valid entity.

These core entities have a 'label' property, which serves as the unique identifier of the entity within the scope of the entity's validity time and priority. The label is a user-readable, non-translatable character sequence. E.g. in case of a bus line, the `label` column of the line table contains the line's real world bus line number.

As opposed to the `label` column, other descriptive columns of entity tables may be translateble and are not necessarily unique in any context. E.g. the line table's 'short_name_i18n' column is not required to be unique in any context on database level, even though from a user perspective it may contain information unique to the line.
