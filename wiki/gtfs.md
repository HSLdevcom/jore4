# GTFS 

GTFS, Generic Transit Feed Specification, is a common format for exchanging public transport routes and schedules as well as basic fare policies. It is practically a ZIP file hosted somewhere that contains a bunch of CSV files.

GTFS is created by Google and it is probably the most common format for public transport route and schedule data. It is aimed for route planner use, therefore it is not very good for static timetables or compensation calculation use cases.

## Some aspects of time in GTFS

- GTFS has 24h+ clock, so trip operating after midnight uses times like 25:30:00 in contrast of Transmodel/Netex, where there is OperatingDayOffset
- GTFS's calendar.txt describes day types, such as friday, for a validity period and a separate calendar_dates.txt for describing differences compated to day types. Many GTFS feed providers rely only on calendar_dates.txt, so for them every day is an exceptional day. This prevets using GTFS for some classic static timetable formats

## GTFS Flex

GTFS could also be used for describing traffic that is not bound to timetables and/or a route by proposed extension called GTFS-flex.

An example of such route is HSL's 918 (https://www.hsl.fi/sites/default/files/uploads/hsl_lahibussit_kirkkonummi_esitteet_918_ja_919_web_24062020.pdf).

More info: https://github.com/MobilityData/gtfs-flex

## Use cases

Basic GTFS is for static data only, typically new GTFS packages are created every day. There is an additional format, GTFS-RT, that provides real-time data, but it is at the moment out of Jore4's scope.

In Jore3 world, GTFS is used:
- For Journey Planner (Digitransit/Reittiopas)
- For many 3rd party journey planners (such as Google Maps)
- For various analytics purposes (e.g. Sujuiko (https://github.com/datarttu/sujuikoDB))
- For reading U bus line operator's timetable data to Jore3 (gtfs2jore3 https://github.com/HSLdevcom/gtfs2jore3)

## More info

* Google's specification: https://developers.google.com/transit/gtfs
* Best practices: https://gtfs.org/best-practices/
