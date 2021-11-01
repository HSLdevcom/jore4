# Various aspects regarding routes

This document describes various aspects regarding routes in Jore. This document uses word "route" as combination of Transmodel's "Route" and "JourneyPattern", consisting of network links (such as streets) a vehicle runs on as well as stops/stations on which it stops.

## Route categories

Routes get (most of?) their metadata from their parent line. Lines' categories are described in a separate [document in wiki](linetype.md).

## Route diversions

Route diversions are modifications to existing route due to a closed road, construction work etc. In practice, if a stop is moved it also affects the route (JourneyPattern is affected) even if vehicle runs the same physical street.

Diversions are typically selected to have "temporary"/"väliaikainen" priority in Jore4.

## Exceptional routes

Sometimes there is a need to run routes that are only run for a limited time for some special occasion. There are four main use cases:
1. A normally operating route that has added service when needed. For example additional buses run on route 436 when I/P trains does not run due to an acute issue. In GTFS-RT, this has a Trip Update of a category "Added".
2. A pre-planned route that only operates on special circumstances. For example 130X which runs only when Metro is out-of-order due to an acute issue. In GTFS-RT, similar to category 1.
3. A pre-planned route with pre-planned timetables that run for a short period of time, such as 24X which runs every Christmas.
4. Routes that are only used for testing, never operating.

### How these exceptions should behave technically
|Exception type | 1 | 2 | 3 | 4 |
|---|---|---|---|---|
|Priority|Normal|Temporary|Temporary|?|
|Visible on static passenger information|Only normal service|No|No|No|
|Visible on dynamic passenger information|Yes|Yes|Yes|No|
