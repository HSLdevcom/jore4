# Infrastructure networks in Jore4

Jore4 uses infrastructure networks from external sources. Jore4 does not include functionalities to modify infrastructure.

## Bus

Digiroad with our custom fix layer for bus terminals and temporary infrastructure.

## Tram

Potential sources: Digiroad/MML, City of Helsinki (Kaupunkimittaus) or HKL

Helsinki/Kaupunkimittaus & HKL will discuss the possibility to maintain a network dataset in the future. Based on this discussion HSL's role will also be clarified. Possible roles are :
- user of the dataset provided by Helsinki/Kaupunkimittaus or HKL
- maintainer of the dataset (whether there will be no maintained dataset available elsewhere)

## Train

Two options, we can pick one that is easier to use:
- [Väylävirasto](https://julkinen.vayla.fi/oskari/?zoomLevel=2&coord=429230.65625_6755196&mapLayers=1+100+default,481+100+avoin:ratko_line&uuid=e139ce6f-ad06-4d64-a49f-b7e57c27e81d&noSavedState=true&showIntro=false), available also as a WFS-api
- [Fintraffic](https://rata.digitraffic.fi/infra-api/)

## Metro

HSL creates the layer with the possible help of HKL and Helsinki/Kaupunkimittaus with some assisting datasets

## Ferry

HSL creates the layer based on ferry GPS-data
