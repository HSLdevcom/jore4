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
5. Tram's days first trip from depot to middle of some route and from there to route's terminus

### How these exceptions should behave technically
|Exception type | 1 | 2 | 3 | 4 | 5 | 
|---|---|---|---|---|---|
|Priority|Normal|Temporary|Temporary|?|?|
|Visible on static passenger information|Only normal service|No|No|No|No|
|Visible on dynamic passenger information|Yes|Yes|Yes|No|Like case 2, only when a driver has logged in to that trip/vehicle task|


## Notes / specification from a design meet 17.12.21

1. New additional timetables are added for routes in the regular manner, when it comes to external data sources. URL to receive the timetable information is added in Jore as a source, and the timetables are updated through that source automatically.
2. In this case, a pre-planned route will exist indefinitely as “temporary”. No timetables are attached to it until the route’s use is required. When required, a temporary timetable is added to it with a validity period. Only then will it be visible in dynamic passenger information.
3. No need to set re-occurring routes or timetables. The required temporary route and timetable will be created as needed or picked from drafts. 
4. Will be done with draft priority. (Luonnos) Tags can be used to target the draft’s use in testing.
5. In this case, the passengers can use these trips. The routes and the related timetables can be treated as a temporary route variant. The timetables can be with normal priority.

Note
- Static passenger information presents only the valid normal priority routes with valid timetables. (Reittien perusversiot voimassaolevilla aikatauluilla) 
- Dynamic passenger information shows valid routes, both priorities normal and temporary, with valid timetables. (Reittien sekä perus- ja väliaikaiset versiot voimassaolevilla aikatauluilla) 
