<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->

- [Glossary](#glossary)
  - [References](#references)
  - [Non-transmodel terminology of less technical subjects](#non-transmodel-terminology-of-less-technical-subjects)
    - [Physical world](#physical-world)
      - [Installation instruction for stop signs](#installation-instruction-for-stop-signs)
      - [Poster frame](#poster-frame)
      - [Break room](#break-room)
    - [Employees](#employees)
      - [Public transport planner](#public-transport-planner)
      - [Transport planner](#transport-planner)
      - [Transport researcher](#transport-researcher)
      - [Planning assistant](#planning-assistant)
      - [Data service coordinator](#data-service-coordinator)
    - [Work processes](#work-processes)
      - [Public transport target](#public-transport-target)
      - [Trip operation plan](#trip-operation-plan)
      - [Compensation](#compensation)
      - [Compensation calculation](#compensation-calculation)
      - [Route bundle contract](#route-bundle-contract)
  - [Transport data terminology](#transport-data-terminology)
    - [Common Objects](#common-objects)
      - [Point on Link](#point-on-link)
    - [Fixed Objects](#fixed-objects)
      - [Stop Place](#stop-place)
      - [Quay](#quay)
    - [Networks](#networks)
      - [Infrastructure point](#infrastructure-point)
      - [Infrastructure link](#infrastructure-link)
    - [Routes and Lines](#routes-and-lines)
      - [Line](#line)
      - [Route](#route)
      - [Route Point](#route-point)
      - [Route Link](#route-link)
      - [Point On Route](#point-on-route)
    - [Tactical Planning](#tactical-planning)
      - [Scheduled stop point](#scheduled-stop-point)
      - [Timing Point](#timing-point)
    - [Service Calendar](#service-calendar)
      - [Day Types](#day-types)
      - [Property of Day Type](#property-of-day-type)
    - [Timetables](#timetables)
      - [Vehicle Journey](#vehicle-journey)
      - [Passing Time](#passing-time)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

# Glossary

## References

- [Transmodel V6 hierarchy](http://www.transmodel-cen.eu/model/index.htm)
- [Entur AS wiki](https://enturas.atlassian.net/wiki/spaces/PUBLIC/overview) (Nordic NeTEx profile)

## Non-transmodel terminology of less technical subjects

### Physical world

#### Installation instruction for stop signs

* In Finnish: _Kilvitysohje_
* This is a terrible translation

#### Poster frame

* In Finnish: _Julistepaikka_
* _Infopaikka_ should also be translated to Poster frame. They are the same physical object. In Jore3 world, Infopaikka is something attached to a stop and Julistepaikka is something attached to a terminal.

#### Break room

* In Finnish: _Taukotila_
* Used by bus and tram drivers. Metro and train driver break rooms are at the moment (3/2021) not interesting for HSL
* Typically in HSL jargon "taukotila" might mean also toilet. Toilet is like a smallest possible version of a break room.

### Employees

#### Public transport planner

* In Finnish: _Joukkoliikennesuunnittelija_

#### Transport planner

* In Finnish: _Liikennesuunnittelija_

#### Transport researcher

* In Finnish: _Liikennetutkija_

#### Planning assistant

* In Finnish: _Suunnitteluavustaja_

#### Data service coordinator

* In Finnish: _Tietopalvelukoordinaattori_

### Work processes

#### Public transport target

* In Finnish: _Joukkoliikennekohde_
* A route category used in subvention calculations, such as "Regional traffic towards Vantaa" / "Seutu, Vantaan suunta"

#### Trip operation plan

* In Finnish: _Lähtöketju_
* Operator's plan on how their buses (phyical vehicles) run trips HSL has ordered them to run.
* Practically a CSV file uploaded by operator to LIJ

#### Compensation

* In Finnish: _Liikennöintikorvaus_

#### Compensation calculation

* In Finnish: _Korvauslaskenta_

#### Route bundle contract

* In Finnish: _Sopimuskohde_
* Practically all routes that are driven from a single contract. Contract has a limited validity period, such as 5 years.

## Transport data terminology

The following taxonomy loosely follows the Transmodel hierarchy and attemps to map similar concepts in Transmodel and Jore 3.x.

### Common Objects

#### Point on Link

* In Finnish: _Linkin piste_
* In Jore 3: _Piste_
* In NeTEx: 7.6.5.1.3
* Examples: a point on a road element
* Attributes: `coordinates`, `order`

A single point on a link.

> NeTEx: "A POINT in a LINK SEQUENCE indicating its order in that particular LINK SEQUENCE."

### Fixed Objects

#### Stop Place

* In Finnish: _Pysähdyspaikka (fyysinen)_
* In Jore 3: _Pysäkkialue_ or _Terminaali_
* In NeTEx: 8.5.4
* Examples: a rail way terminal, two bus stops on the opposite sides of the road

A physical place, region or structure where vehicles may stop.

> NeTEx: "A place comprising one or more locations where vehicles may stop and where passengers may board or leave vehicles or prepare their trip. A STOP PLACE will usually have one or more well-known names."

#### Quay

* In Finnish: _Laituri_
* In Jore 3: _Pysäkki_
* In NeTEx: 8.5.4.5.6

  > NeTEx: "A place such as platform, stance, or quayside where passengers have access to PT vehicles, Taxi, cars or other means of transportation. A QUAY may serve one or more VEHICLE STOPPING PLACEs and be associated with one or more STOP POINTS."

### Networks

#### Infrastructure point

* In Finnish: _Verkon piste_
* In Jore 3: _Verkon solmu_
* In NeTEx: 8.4.1.4.1
* Examples: road junction, railway junction
* Attributes: `coordinates`

An intersection point through which public transport traffic flows.

> NeTEx: "A super-type including all POINTs of the physical network (e.g. RAILWAY JUNCTION)."

#### Infrastructure link

* In Finnish: _Verkon linkki_
* In Jore 3: _Verkon linkki_
* In NeTEx: 8.4.1.4.5
* Examples: road element, railway element
* Attributes: `from point`, `to point`, `distance`, `points on link`

In Jore 3:

In NeTEx: A directed link between two `infrastructure points`. May contain `points on link` which specify the exact shape of the link.

> NeTEx: "A super-type including all LINKs of the physical network (e.g. RAILWAY ELEMENT)."


### Routes and Lines
#### Line

* In Finnish: _Linja_
* In Jore 3: _Linja_ + _Linjan nimet_
* In NeTEx: Part 1 - 8.4.5.6

  > NeTEx: "A group of ROUTEs which is generally known to the public by a similar name or number."

#### Route

* In Finnish: _Reitti_
* In Jore 3: _Reitti_ + _Reitin suunta_
* In NeTEx: Part 1 - 8.4.5.1, 8.4.5.5.4
* Attributes: `direction`, `points on route`

A single directed path through the networking following an ordered list of `points on route`.

Note that in Jore 3, a single _reitti_ defines two _reitin suunta_: two routes in the opposite directions. In NeTEx this would be modelled as two separate routes.

> NeTEx: "An ordered list of located POINTs defining one single path through the road (or rail) network. A ROUTE may pass through the same POINT more than once".

#### Route Point

* In Finnish: _Reittipiste_
* In NeTEx: Part 1 - 8.4.5.5.2
* Examples: an `infrastructure point`, e.g. a road junction

A point through which `route(s)` may flow. The same `route` may go through the same point multiple times.

> NeTEx: "A POINT used to define the shape of a ROUTE through the network."

#### Route Link

* In Finnish: _Reitin linkki_
* In Jore 3: _Reitin linkki_
* In NeTEx: Part 1 - 8.4.5.5.3
* Attributes: `from point`, `to point`

Connects two points as a link.

In Jore 3 each `route link` points to a corresponding `infrastructure link`.

In NeTEx each point may either be a `route point` or a `scheduled stop point`.

> NeTEx: "an oriented link between two ROUTE POINTs allowing the definition of a unique path through the network."

#### Point On Route

* In Finnish: _Piste reitillä_
* In Jore 3: N/A
* In NeTEx: Part 1 - 8.4.5.5.5
* Attributes: `route point`, `route link`, `order`

Links a `route point` with a `route`. The `order` attribute defines the order in which individual `point on route` form the actual `route`. An optional `route link` points towards the next `point on route` and is present on all but the last `point on route`.

> NeTEx: "A ROUTE POINT used to define a ROUTE with its order on that ROUTE."

### Tactical Planning

#### Scheduled stop point

* In Finnish: _Pysähdyspaikka (looginen)_
* In Jore 3: N/A
* In NeTEx: Part 1 - 8.6.3.4.2

  > NeTEx: "A POINT where passengers can board or alight from vehicles"

#### Timing Point

* In Finnish: _Hastus-paikka_
* In Jore 3: _Hastus-paikka_
* In NeTEx: 8.4.7.4.1

> NeTEx: "A POINT against which the timing information necessary to build schedules may be recorded."

In Jore 3, hastus points represent important route-specific stop places, which are used to e.g. synchronize timetables between multiple routes, or highlight a point where the vehicle speed changes significantly (e.g. the last stop before transitioning to a highway).

Because the hastus points are route specific, a single stop place may be a hastus point on a particular route but only a regular stop place on other routes. A single route may have any number of hastus points, minimum amount is two: The first and last stops are always Hastus points.

### Service Calendar

#### Day Types
+ In NeTEx: part1 - 7.7.5.5.2

> NeTEx: "A type of day characterised by one or more properties which affect public transport operation. For example: weekday in school holidays."

This is a generic description of day types, like weekdays, weekends, holidays, etc. These attributes described by multiple PROPERTIES OF DAY TYPE

#### Property of Day Type
+ In NeTEx: part1 - 7.7.5.6

> NeTEx: "A property which a day may possess, such as school holiday, weekday, summer, winter etc. This may be used to generate a description of a day type in many different natural languages."

These properties are technically enums, like "day of week", "week of month" or "day of year", etc.

### Timetables

#### Vehicle Journey
- In NeTEx: part2 7.2.1.1

> NeTEx: "The planned movement of a public transport vehicle on a DAY TYPE from the start point to the end point of a JOURNEY PATTERN on a specified ROUTE."

This is a generic wrapper for a given route's timetables for a given day type.

#### Passing Time
- In NeTEx: part2 7.2.13.5.1

> NeTEx: "Time data concerning public transport vehicles passing a particular POINT; e.g. arrival time, departure time, waiting time."

This tells that when does a vehicle arrive and depart from a scheduled stop point for a given vehicle journey.