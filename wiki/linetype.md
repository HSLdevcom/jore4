# Line types and coloring

In HSL traffic, all modes have their own color scheme. Buses and trams have two different color schemes depending on line's role in public transport system.

In addition, lines have types defined to aid reporting and financial planning. In Jore3, there are two overlapping type categories: Public transport target (joukkoliikennekohde) and public transport species (joukkoliikennelaji). In this we use "public transport target" codes and names when referencing Jore3 as these are used in all financial planning needs and for most reporting needs.

This document describes the different possibilities of line types and colors for each mode of transport.

## By mode

### Bus

For trunk lines:
* How to detect if a line is trunk line: In jore3, the line has a requirement "Runkolinjaväritys"
* Example: 560 Rastila-Myyrmäki
* Target: Depending on area served, please see below
* GTFS route type: 702
* Color: #FF6319
* Text color: #FFFFFF

For local buses "lähibussit":
* How to detect if a line is lähibussi: In jore3, public transport species is "21 Lähibussiliikenne"
* Example: 413 Myyrmäki-Martinlaakso
* Target: Depending on area served, please see below
* GTFS route type: 707 for most lines, 715 for routes that operate on call (example: 918 for southern Kirkkonummi)
* Color: #007AC9
* Text color: #FFFFFF

For U lines:
* How to detect if a line is U-line: In jore3, public transport species is "08 U-liikenne"
* Example: U848 Kamppi-Porvoo
* Target: U lines + municipality served (currently includes capital region (Jore3 id: 9510), Kirkkonummi (Jore3 id: 9511), Sipoo (Jore3 id: 9512) and Tuusula (Jore3 id: 9513))
* GTFS route type: 701
* Color: #007AC9
* Text color: #FFFFFF

For all except ones listed above:
* Example: 112 Tapiola-Matinkylä
* Target: Depending on area served, please see below
* GTFS route type: 703
* Color: #007AC9
* Text color: #FFFFFF

#### Jore3 public transport targets used for buses
|Jore3 id|Type|
|--------|----|
|9201|Helsinki internal traffic|
|9202|Espoo and Kauniainen internal traffic|
|9204|Vantaa internal traffic|
|9205|Kerava internal traffic|
|9206|Kirkkonummi internal traffic|
|9207|Sipoo internal traffic|
|9208|Tuusula internal traffic|
|9209|Siuntio internal traffic|
|9310|Regional traffic|
|9311|Kerava regional traffic|
|9312|Kirkkonummi regional traffic|
|9314|Tuusula regional traffic|
|9315|Siuntio regional traffic|
|9320|Espoo regional traffic|
|9321|Vantaa regional traffic|
|9322|Transverse regional|
|9510|Regional U-traffic|
|9511|Kirkkonummi regional U-traffic|
|9512|Sipoo internal U-traffic|
|9513|Tuusula regional U-traffic|

### Train

For HSL tendered lines (Y, X, U, L, E, A, P, I and K):
* Target: Regional traffic (Jore3 target id: 9310)
* GTFS route type: 109
* Color: #8C4799
* Text color: #FFFFFF

For other lines (R, Z, D and T trains):
* Target: Regional U traffic (Jore3 target id: 9510)
* GTFS route type: 106
* Color: #8C4799
* Text color: #FFFFFF

### Ferry

For all lines:
* Target: Uses similar logic as buses. Currently only one ferry runs between Kauppatori and Suomenlinna, that is Helsinki internal traffic.
* GTFS route type: 1200
* Color: #00B9E4
* Text color: #FFFFFF

### Metro

For all lines:
* Target: Regional traffic (Jore3 target id: 9310)
* GTFS route type: 401
* Color: #FF6319
* Text color: #FFFFFF

### Tram

For light rail (RaideJokeri / Pikaraitiotie 550 under construction):
* How to detect: Currently not saved to Jore3.
* Target: Regional traffic (Jore3 target id: 9310)
* GTFS route type: 903
* Color: #00B2A9
* Text color: #FFFFFF

Other lines (1-10, ones operating in center of Helsinki):
* Target: Uses similar logic as buses. Currently all these trams are Helsinki internal traffic.
* GTFS route type: 901
* Color: #00985F
* Text color: #FFFFFF


## References

* [HSL Design System regarding colors](https://tyyliopas.hsl.fi/d/h8JR9dHeqfgd/braendi#/visuaalinen-ilme/vaerit)
* [GTFS specification regarding line (GTFS: route) types](https://developers.google.com/transit/gtfs/reference/extended-route-types)
