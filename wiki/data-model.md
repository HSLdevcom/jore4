This file aims to document the Jore4 data model and open questions regarding future development of the data model.

Open questions
--------------

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
------------------

It has been anticipated that different organizations in different countries might be interested in using Jore4. For Jore4 to be a viable choice in many different environments, its data model has to be designed to be as generic as possible, while still supporting the Jore4 objectives as well as needed.

Geography
---------

In order to support geographic locations of large scale, the system should not be limited to using only a single SRID throughout the whole system. Therefore, the PostGIS `geography` type was chosen to be used within the data model wherever feasible.

When the need to use a function supporting only the `geometry` type arises, the `internal_utils.determine_SRID` functions should be used to determine the SRID for the conversion to the `geometry` type. The `internal_utils.determine_SRID` functions have been created to provide an abstraction layer to the PostGIS internal `_ST_BestSRID` functions, which are not documented and may be deprecated in the future. When this happens, the `internal_utils.determine_SRID` functions serve as single point of failure and can be changed to adopt a different way of determinining the most suitable SRIDs for the given `geography` parameters.

Timestamp with time zone
------------------------

The system should be able to perform actions at times relating to the timestamps contained in the data base. (For example, a notification email might be sent prior to an entity's validity time expiration.) This means the system needs to be able to interpret the timestamps as absolute points in time, making the `timestamp with time zone` type the natural choice.

However, it needs to be taken into account that the time zone applied when times are displayed to the user, is most likely not UTC and might not be the same as the user's web browser's time zone. (In many cases, the displayed entity's location should be used to determine the time zone to be applied. In a simplified version, the same constant time zone might be used to display user information.)



- internal schemas for non-exposed db objects
- transmodel-based
- exceptions: label (corresponds to differently named properties with translatable content in transmodel)
- additions: validity time

Routes and lines
----------------

TBD, see [ticket](https://github.com/HSLdevcom/jore4/issues/584).
