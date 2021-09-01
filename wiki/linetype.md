# Line types and coloring

In HSL traffic, all modes have their own color scheme. Buses and trams have two different color schemes depending on line's role in public transport system.

In addition, lines have types defined to aid reporting and financial planning. In Jore3, there are two overlapping type categories: Public transport target and public transport species. In this we use "public transport target" codes and names when referencing Jore3.

This document describes the different possibilities of line types and colors for each mode of transport.

## By mode

### Bus

For trunk lines:
* Example: 560 Rastila-Myyrmäki
* Type: Depending on area served, please see below
* GTFS route type: 401
* Color: #FF6319
* Text color: #FFFFFF

For local buses "lähibussit":
* Example: 413 Myyrmäki-Martinlaakso
* Type: Depending on area served, please see below
* GTFS route type: 707 for most lines, 715 for routes that operate on call (example: 918 for southern Kirkkonummi)
* Color: #007AC9
* Text color: #FFFFFF

For U lines:
* Example: U848 Kamppi-Porvoo
* Type: U lines + municipality served (currently includes capital region (Jore3 id: 9510), Kirkkonummi (Jore3 id: 9511), Sipoo (Jore3 id: 9512) and Tuusula (Jore3 id: 9513))
* GTFS route type: 701
* Color: #007AC9
* Text color: #FFFFFF

For all except ones listed above:
* Example: 112 Tapiola-Matinkylä
* Type: Depending on area served, please see below
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
|9311|Kerava regional traffic|
|9312|Kirkkonummi regional traffic|
|9314|Tuusula regional traffic|
|9315|Siuntio regional traffic|
|9320|Espoo regional traffic|
|9321|Vantaa regional traffic|
|9322|Transverse regional|

### Train

For HSL tendered lines (Y, X, U, L, E, A, P, I and K):
* Type: Regional traffic (Jore3 target id: 9310)
* GTFS route type: 109
* Color: #8C4799
* Text color: #FFFFFF

For other lines (R, Z, D and T trains):
* Type: Regional U traffic (Jore3 target id: 9510)
* GTFS route type: 106
* Color: #8C4799
* Text color: #FFFFFF

### Ferry

For all lines:
* Type: Uses similar logic as buses. Currently only one ferry runs between Kauppatori and Suomenlinna, that is Helsinki internal traffic.
* GTFS route type: 1200
* Color: #00B9E4
* Text color: #FFFFFF

### Metro

For all lines:
* Type: Regional traffic (Jore3 target id: 9310)
* GTFS route type: 401
* Color: #FF6319
* Text color: #FFFFFF

### Tram

TODO

## References

* [HSL Design System regarding colors](https://tyyliopas.hsl.fi/d/h8JR9dHeqfgd/braendi#/visuaalinen-ilme/vaerit)
* [GTFS specification regarding line (GTFS: route) types](https://developers.google.com/transit/gtfs/reference/extended-route-types)
