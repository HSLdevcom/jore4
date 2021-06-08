# Spatial data and processes

Jore4 is an opportunity to fix some issues regarding spatial data ownership, data flows and update processes.
We should try to recognize those.

## What is spatial data?

Spatial data describes locations, e.g. coordinates or boundaries, or relates to those locations, e.g. the population count in an area or the median measured bus speed on a road segment at a given time range.

## Relevant data sets

### Infrastructure network

Infrastructure network consists of the roads, rails, tram tracks, metro tracks and so on.
Think of the asphalt without the cars.

#### Important aspects

1. Geographic structure
   - For displaying beautiful route lines on the map
   - For snapping to roads when editing routes in the UI
   - For automatic routing from A to B when sketching routes in the UI
   - For measuring the length of routes
1. Topographic structure
   - Electric busses require the elevation information
1. High accuracy
   - Observations, e.g. vehicle GPS positions, need to be matched to the roads. The map-matched observations are used for some measures of public transport quality. If the infrastructure network model is inaccurate, the measures will suffer.
     - Examples of such measures: speed distributions, waiting time distributions
1. Appropriate fidelity
   - E.g. bus turnouts
     - Modeling bus turnouts might make service quality analysis more difficult. If the bus turnouts are in Jore4 but the service quality analysis is done on a junction and link model without bus turnouts, vehicles might momentarily disappear from the quality index calculations.
     - In contradiction, reimbursement for operating the traffic is partly based on the length of the drive. That length is measured from a bus turnout to the next bus turnout.
     - Also bus turnouts and the number of lanes affect when busses can overtake each other. This might not affect planning but it might affect arrival and departure estimates.
     - The difference in driving distance between stopping on a bus turnout and passing through might be around 3 meters.
   - So maybe we need to model the bus turnouts but consider the effect on the analysis.
1. Bus lanes
   - i.e. who is allowed to drive on the lane
   - Full-time, part-time
   - Exclusive or selectively for other traffic as well, e.g. a lane also for private cars turning right in the next junction or a tunnel only for public transit
   - Tram lanes ("raitioliikennekaista") and bus lanes ("linja-autokaista") separately
   - Municipalities did not have the information on bus lanes
   - There exists an unnamed project that collected this information

#### Not seen as important at the moment

1. Speed limits

#### Unknown importance

1. Bus turnouts
1. Turning restrictions
   - Can one turn left here?
1. Road restrictions
   - Can a large bus practically fit here?
1. Road lanes
   - In some junctions one route continues straight while another turns left. The vehicles might end up on different lanes before the junction. If there's congestion on one lane but not on the other, it might affect stop arrival and departure time estimates.
   - In comparison, we model each rail track separately instead of e.g. having one link from Helsinki to Pasila.
1. Bus bulbs
   - i.e. separate islands in the middle of the road for passengers to embark or disembark

#### Planned future changes to the network

Some infrastructure network plans are probably not available from the external data sources and need to be modelled in advance within HSL.
The plans need to be modelled at least for the purposes of procurement of future traffic.

For example in the summer of 2021, terminals have exceptional situations and the metro may have an operating break.

Combining these data sources and keeping them updated, overriding the external data with still relevant changes by HSL, forms a challenge.

### Routes

A large part of the route structure is determined by the capabilities of the infrastructure network model.

#### Schematic maps

Schematic maps of the routes include metro line maps, train line maps, tram line maps and trunk bus line maps.

These are often visuals created manually.

There are automated tools, though.

### Stops and terminals

Different kinds of representations of stops:

- Point
  - Simplifying the location of the physical stop
    - Pole with a flag
    - Bus stop shelter
  - Projection point from the side of the road on to the middle line of the road
  - Jore3 has four different kinds of points to describe one stop
    1. Theoretical central point
       - Used for calculating lengths and thus reimbursements
    1. Visualization point ("esityspiste")
       - Does not obscure other elements on a visual map
         - Likely optimized for a certain map
         - Maybe not useful in the future, could be automatically placed
    1. LIJ-piste
       - Used for stop radius in LIJ
    1. Location of the stop flag
- Stop radius
  - An imagined 20-30-metre, individually sized radius around one of the point representations of the stop
  - Used for triggering events of arrival, departure and passing through
  - Simple but not accurate enough
- Polygon
  - A bounded area describing where it is allowed for vehicles to stop ("toiminnallinen alue")
  - Could be used to describe a terminal
- Multipolygon
  - Several bounded areas
  - Maybe useful for terminals
- Line
  - Describing where along an infrastructure link, e.g. a road segment described as a line, a vehicle is allowed to stop
  - Might encompass 20-30 metres

Stop areas might simply be sets of stops without a spatial structure.

Stop metadata also includes zip codes but the purpose is unclear.

### City bike stations

- Stations owned by Helsinki City Transport (HKL), other municipalities (Vantaa has its own system) or City Bike Finland
- City Bike Finland owns the data
  - Excel sheets delivered to City Bike Finland
  - Sometimes had to create redundant, temporary solutions

### Depots

Where vehicles sleep.
Garages in Transmodel.

### Spaces for breaks and the restrooms for drivers

### Park & Ride / Kiss and ride

Parking areas near important public transport hubs for switching to and from public transport ("Liityntäpysäköinti", "LIIPI").

Service [here][park-and-ride].

### Poster frames

The locations and qualities of poster frames at the terminals and the stops.

### Ticket machines

They sell tickets.

### Ticket vendor points of sale

E.g. R-kioski.

Currently stored and modified both in LIJ and ArcGIS.

### Bluetooth beacons

On stops, platforms, terminals, city bike stations.
Used to help people navigate.

### Displays at stops and terminals

Show schedules, platforms and other relevant information.

Not displays in vehicles.

### Fare zones

- Currently zones A, B, C, D
- Linestrings for displaying beautifully on a map
- More accurately each stop has a property of one or more zones, possibly separate for embarking and disembarking
- Currently stored in ArcGIS

### Geocoding

Transformation of names to map coordinates. Reverse geocoding is turning coordinates into names.

- Street addresses
- Areas like zip codes or city districts
- Points of interest (POIs) like market places, shops, churches or statues.

### Aerial images

## Data ownership

### Relevant spatial data owner systems at HSL

- Jore3
- Systems within Lippu- ja informaatiojärjestelmä (LIJ)
  - Service Provider Management (SPM)
  - PubTrans
  - Beacon database
- HSL map project
- ArcGIS Online
- MONO system?
  - Display management for Trapeze displays
- Axentia system?
  - Display management for Axentia displays

### What spatial data should Jore4 own?

1. Future planned infrastructure network

- HSL does not rule over the infrastructure network so in principle this is the responsibility of other authorities
- As long as these plans are not available from elsewhere, it is modelled in-house

1. Stops
1. Routes
1. "All worldly possessions of HSL"
   1. Ticket machine locations
   1. Ticket vendor locations
1. Fare zones
1. Spaces for breaks and the restrooms for drivers
1. "Poster information"
1. Depots

### What spatial data should Jore4 cache?

1. The current infrastructure network
1. City bike stations

Reasons for caching:

1. Poor or no APIs for the data
1. Changes to the data need to be approved

### What spatial data is kept separate from Jore4?

- Parking areas near important public transport hubs for switching to and from public transport ("Liityntäpysäköinti" (LIIPI))

### What spatial data are we not sure of whether it should be owned by Jore4?

If there is a system that already serves its purpose, Jore4 might not need to replace it.

- Displays at stops and terminals
- Bluetooth beacons
- Schematic maps
- Geocoding
- HSL/HSY aerial images

## Infrastructure network data sources

Details about the data sources in [Data sources](data-sources.md).

- OpenStreetMap
  - variable quality, usually high in HSL area
  - variable structure, e.g. in interchanges
  - includes tram network, metro network, sidewalks, squares
  - fast update process (1-2 days)
- Digiroad
  - constant quality
  - constant structure
  - limited content
  - slow update process (months)
  - no tram tracks, train tracks or metro tracks
- Municipal data sets
  - Vantaa has accurate data, includes street lanes
  - Helsinki has the tram track network
- National Land Survey
- To be deprecated: Jore3 road network
  - Migration phase from Jore3 to Jore4 needs to be managed, though.
- Digitraffic
  - Rail network, operational point of view
- Finnish Transport Infrastructure Agency
  - Rail network, owner of infrastructure 
- Aerial images
- Street view images
- 3D models

For ideal quality and results the preference is to [combine Digiroad and OpenStreetMap][peltonen2016].
OpenStreetMap licensing causes issues, though.

We are asked to:

- separate the sources and keep the reference IDs of the original sources
- keep history of changes
- control changes to the network with at least some human supervision
- have an audit log of the changes

### Road network sources

- OpenStreetMap
- Digiroad
- Base map of the City of Helsinki ("kantakartta")
- Jore3 implicitly for migrating routes

### Tram track network sources

- OpenStreetMap
- Base map of the City of Helsinki ("kantakartta")
  - accurate
- Jokeri Light Rail by Helsinki city
- Jore3 implicitly for migrating routes

### Metro track network sources

- Base map of the City of Helsinki ("kantakartta")
  - accurate
  - excludes track segments underground
  - excludes West Metro ("Länsimetro") in Espoo
- Guide Map of the City of Helsinki ("opaskartta")
  - approximate, no separation of tracks
  - <https://kartta.helsinginseutu.fi/>
- National Land Survey
  - approximate, no separation of tracks
  - vector format excludes West Metro ("Länsimetro") in Espoo
  - raster format includes West Metro ("Länsimetro") in Espoo
- Sitowise West Metro progress map
  - excludes anything east of Ruoholahti so most of Helsinki
  - includes separate tracks
  - includes some but not all of the track segments, e.g. many crossovers ("puolenvaihtopaikka") are missing
- Jore3 implicitly for migrating routes

### Train track network sources

Not yet properly looked at.

- National Land Survey has simplified track lines
- Base map of the City of Helsinki
  - includes only tracks within Helsinki
- Jore3 implicitly for migrating routes

Maybe Finnish Transport Infrastructure Agency ("Väylävirasto") can help.

Not in Digiroad.

### Waterway network

Not yet looked at.

Maybe not needed.

## Licensing

OpenStreetMap is published using the Open Database License (ODbL).
ODbL is a share-alike license.

We need to publish at least one version of the GTFS dump without ODbL because:

1. Google Maps is often used by the customers of HSL. Google avoids opening their map data so it refuses to import ODbL-licensed data.
1. Digiroad will not take ODbL-licensed stop locations.
1. The National Access Point might demand it.

As the GTFS dump includes routes and stops, most of the spatial data in Jore4 must be available also without ODbL.

Offering also an ODbL-licensed version of the GTFS dump to match the routes with OpenStreetMap roads has been done before and can be done again.
The ODbL GTFS dump would ease the use of Jore4 data in services that use OpenStreetMap, e.g. Digitransit, the HSL app and many third-party apps.

HSL generally releases data under [CC BY](https://creativecommons.org/licenses/by/4.0/) and possibly [CC0](https://creativecommons.org/share-your-work/public-domain/cc0).
OpenStreetMap Foundation considers CC BY -licensed data sets as incompatible data sources for OpenStreetMap.
OpenStreetMap has [an extra waiver][hsl-osm-waiver] to use the HSL CC BY data.

We might need legal help in interpreting ODbL use cases in the Finnish legislative framework.

### Links for license investigation

- [ODbL license text](https://opendatacommons.org/licenses/odbl/)
- OpenStreetMap Foundation (OSMF), authoritative source:
  - [Root for license information](https://wiki.osmfoundation.org/wiki/Licence)
  - [Use cases considered](https://wiki.osmfoundation.org/wiki/Licence/Community_Guidelines)
- OpenStreetMap wiki, practical, non-authoritative source:
  - [Legal FAQ](https://wiki.openstreetmap.org/wiki/Legal_FAQ#Using)

## Process flows

Digiroad imports stops from Jore3 once a week.
That will likely continue with Jore4.

Digiroad will likely export roads into Jore4.

## Hopes for tools to import spatial data into Jore4

At least an API for importing new spatial data, either case by case or as a mass import.

Maybe also UI.

- zip codes for stops
- "alueurakka", change the contractor on all stops in the area
- a new municipality joins
  - case Tuusula, case Siuntio
  - check stop locations
  - add into Jore4 one by one or as a set
- map export, stops, lines
- wfs, wms, something else?
  - for qgis and elsewhere
  - postgis->wfs pretty easy
- graphql api
  - not only for jore4 ui but also for other uses

## How is spatial data currently handled at HSL?

- Jore3 contains a road network maintained by HSL alone.
- Jore3 exports are further refined by the HSL map project and Digitransit.

## How could spatial data be handled at HSL?

- ODbL use clearly documented: which systems use OSM data and how?

## How to document our efforts?

- Wiki for now.
- [Digitransit documentation](https://digitransit.fi/en/developers/) is one benchmark to aim for over time.
  - [A glossary](glossary.md) would be an important part of the website

## Links

1. [Peltonen, T. (2016). Digiroad-ja OpenStreetMap-aineistojen yhteiskäyttö joukkoliikennepysäkeissä. Opinnäytetyö.][peltonen2016]
1. [HSL map project documentation][hsl-map-documentation]
1. [The waiver from HSL to allow OpenStreetMap to use the CC BY material from HSL][hsl-osm-waiver]
1. [Road traffic law ("Tieliikennelaki")][law-for-road-traffic]
1. [Park & Ride][park-and-ride]

[hsl-osm-waiver]: https://wiki.openstreetmap.org/w/images/7/75/HSL_permission.pdf "The waiver from HSL to allow OpenStreetMap to use the CC BY material from HSL"
[hsl-map-documentation]: https://github.com/HSLdevcom/hsl-map-documentation "HSL map project documentation"
[peltonen2016]: https://www.doria.fi/handle/10024/121944 "Peltonen, T. (2016). Digiroad-ja OpenStreetMap-aineistojen yhteiskäyttö joukkoliikennepysäkeissä. Opinnäytetyö."
[law-for-road-traffic]: https://finlex.fi/fi/laki/ajantasa/2018/20180729 "Road traffic law, Tieliikennelaki"
[park-and-ride]: https://p.hsl.fi/ "Park & Ride (Liityntäpysäköinti, LIIPI)"
