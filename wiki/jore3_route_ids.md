# Jore3 line and route IDs

Jore3 has some information integrated in line and route IDs. The format of code, such as line 9787 or its route 9787A4 is the following.

## First number "9"

In ferries, trams and buses this means the municipality the route primarily servers. Nowadays there are no know use cases that use this, deprecated by Public transport target. In metro and tram, this denotes the mode.

* 0 Not used. Some historical routes have this prefix
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

* For buses, trams (the only example in spring 2021: tram 6T) and metros. If empty, this is the main route of a line. If there is a letter, it is the variant visible for a customer, in this example it is shown in passenger information as "787A"

* For ferries, fifth is used for the route to Suomenlinna that uses the Katajannokka pier, a rarely driven variant. If empty, it is the regular ferry.

* For trains, this is the line name. Trains always have one letter for line name, such as "A" or "Y". The only visible part in passenger information for trains.

## Sixth "4"

This denomination is used similarly for all modes.

* If empty, it is the main route of a line.

* If this is a integer, it is hidden variant. This means that it has some difference in a route compared to basic version of a route, but it is so small that it does not deserve a visible difference in route name. In this example, route 9787A4 runs via Laukkoski to serve some school children, but they are well aware of this so we would not like to confuse other passenger by making this a separate variant.

* If this is a letter, it is a visible variant to customer, such as "9788KV" is visible in passenger information as 788KV.

* A special example is buses that replace rail traffic: Then typically only this letter is used and shown in passenger information: "1004 X" is tram replacing bus 4X. Because fifth is empty, this is the main route of a line, and because sixth is a letter, it is visible to passengers. The empty fifth is omitted in passenger information.

# Examples

| Identifier   | Displayed   | Primary? | Testing? | Other |
|--------------|-------------|----------|----------|-------|
| `0052B`      | `52B`       |          |          |       |
| `1000 X`     | `<empty>`   |          | x        | Should this be `X` & primary? |
| `1000A`      | `A`         |          | x        |       |
| `1000S4`     | `S`         |          | x        |       |
| `1001 3`     | `1`         |          |          |       |
| `1001A4`     | `1A`        |          |          |       |
| `1003 X`     | `3`         |          |          | Should this be `3X` & primary? |
| `1019`       | `19`        | x        |          |       |
| `1039`       | `39`        | x        |          |       |
| `2147KT`     | `147KT`     |          |          |       |
| `2166 1`     | `166`       |          |          |       |
| `3001`       | `<empty>`   | x        |          | A legacy line with no line letter |
| `3001D`      | `D`         | x        |          |       |
| `3001I1`     | `I`         |          |          |       |
| `3002A`      | `A`         | x        |          |       |
| `3002A3`     | `A`         |          |          |       |
| `31M2`       | `M2`        | x        |          |       |
| `31M2 4`     | `M2`        |          |          |       |
| `31M2B4`     | `M2B`       |          |          |       |
| `4001KM`     | `1KM`       |          |          |       |
| `4431 3`     | `431`       |          |          |       |
| `5530 3`     | `530`       |          |          |       |
| `6021SV`     | `21SV`      |          |          |       |
| `7177MB`     | `177MB`     |          |          |       |
| `9008`       | `8`         | x        |          |       |
| `9008TT`     | `8TT`       |          |          |       |
| `9787A4`     | `787A`      |          |          |       |
| `9788KV`     | `788KV`     |          |          |       |
