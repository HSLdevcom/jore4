# Jore3 line and route IDs

Jore3 has some information integrated in line and route IDs. The format of code, such as line 9787 or its route 9787A4 is the following. 

## First number "9"

In ferries, trams and buses this means the municipality the route primarily servers. Nowadays there are no know use cases that use this, deprecated by Public transport target. In metro and tram, this denotes the mode.

* 1 Helsinki
* 2 Espoo
* 3 Train or metro
* 4 Vantaa
* 5 Regional routes between Vantaa and Espoo
* 6 Kirkkonummi and Siuntio
* 7 U bus routes
* 8 for testing purposes, not used
* 9 Tuusula, Kerava and Sipoo

## Second to fourth number "787"

* For trams and buses, this is the human readable line number. Leading zeroes are not shown to end-users, eg. "1039" is the bus 39 that operates (mostly) in Helsinki.

* For the Suomenlinna ferry, the only ferry in HSL traffic, it has a line number of 1019, but it is not supposed to be shown to customers (but it is unfortunately shown in GTFS at the moment)

* For train, second and third are zeroes and fourth is 1 or 2. Two is for trains operating west from Pasila and one for ones operating north/east.

* For metro, second is one and third and fourth are the line number, such as "31M2" means that line number is M2.

## Fifth "A"

* For buses, trams (not currently used) and metros. If empty, this is the main route of a line. If there is a letter, it is the variant visible for a customer, in this example it is shown in passenger information as "787A"

* For ferries, fifth is used for the route to Suomenlinna that uses the Katajannokka pier, a rarely driven variant. If empty, it is the regular ferry.

* For trains, this is the line name. Trains always have one letter for line name, such as "A" or "Y". The only visible part in passenger information for trains.

## Sixth "4"

This denomination is used similarly for all modes. 

* If empty, it is the main route of a line. 

* If this is a integer, it is hidden variant. This means that it has some difference in a route compared to basic version of a route, but it is so small that it does not deserve a visible difference in route name. In this example, route 9787A4 runs via Laukkoski to serve some school children, but they are well aware of this so we would not like to confuse other passenger by making this a separate variant.

* If this is a letter, it is a visible variant to customer, such as "9788KV" is visible in passenger information as 788KV.
