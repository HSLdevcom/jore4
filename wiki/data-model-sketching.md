# Data model sketching

## Introduction
Intention:
- Enable low-friction collaboration before committing to any SQL schema


Desirable outcomes:
- Clarify infrastructure network data model enough to probably enable importing routes from Jore3
- SQL schema -like structure


Suggestions:
- Work asynchronously by default
- Suggest a synchronous session in Slack if you deem it useful
- Write down your ideas freely, create suggestions for others, comment
- Once the initial schema is committed to in a documented manner, we delete this document


Roles:
- haphut copies this document to the wiki as such before every review session
## Content
How are schemas named and bounded?


Start from Transmodel if it makes sense.


Having a separate schema for the infrastructure network makes sense as HSL does not own or make decisions regarding the infrastructure network. In that sense there's hope that at some point the responsible organizations will offer usable APIs so that HSL would not need its own service for the infrastructure network.


________________
### Jore 3
Challenges in the legacy data model


Bus stops are part of the infrastructure layer.
* Moving a bus stop always requires altering the related links (e.g. splitting an existing link), and thus affects routes




Routes consist of consecutive route links between two nodes
* Each link describes the attributes of the starting node
* As there are 1 fewer links than nodes, an extra link must be added to describe the final node


Route links and infrastructure links are the same
* Each link can contain multiple points, which describe the shape of the link. These shapes are drawn by hand using a reference map
* One route may go directly from A->C with a single link while another route might take two links to traverse A->B->C
   * This results in the segment A->C having 2 separate shapes






________________
### Jarkko’s proposal


* Hahmottelin yksittäiset pysäkit siten, että ne viittaavat johonkin tielinkkiin (ovat sen varrella) mutta eivät lukeudu tielinkkien välisiin solmuihin. Näin ollen pysäkin paikkaa siirrettäessä ei tielinkkeihin tarvitse koskea. Kuten Jore3:ssa, pysäkillä on tien keskiviivalle projisoitu koordinaatti.
* Hahmottelin reitit järjestettynä pistelistana reittilinkkien sijaan käyttäen RoutePoint- ja PointOnRoute-käsitteitä, jotka ovat Transmodelista tuttuja. RoutePoint- ja PointOnRoute-käsitteet mahdollistavat sykliset reitit, missä reitti voi kulkea saman solmun kautta useammin kuin kerran.
* Hahmotelmassa RoutePoint viittaa joko pysäkkiin tai katuinfran risteyssolmuun (tielinkin päätepiste).
* Tietomalli vastaa mm. seuraaviin kysymyksiin:
   * Mitä pysäkkejä on tielinkin varrella?
   * Mitä reittejä kulkee tietyn tielinkin tai risteyssolmun tai pysäkin kautta?
   * Jos pysäkin paikkaa muutetaan, mitä reittejä pitää päivittää uusilla versioilla?
* Hahmotelmasta puuttuu paljon olennaisiakin kenttiä, joiden lisääminen saattaa aiheuttaa päänvaivaa, koska malli ei vastaa Jore3:sta (tunnistin jo jotain Avoimia kysymyksiä -kohdassa)
* Jos valitaan geometrioiden koordinaatistojärjestelmäksi sellainen, jonka yksikkö on metrinen (eikä aste kuten EPSG:4326 tapauksessa), se voi tehdä kehittäjän työstä helpompaa joiltakin osin mm. PostGIS-funktioiden kanssa. On esimerkiksi ymmärrettävämpää, jos koodissta kysytään “hae pysäkit 5 metrin säteellä risteyksen X koordinaateista” kuin “hae pysäkit 0,0017 asteen säteellä risteyksen X koordinaateista”. Toki koordinaatistomuunnokset ovat mahdollisia ennen PostGIS-funktioiden kutsumista, mutta koordinaatiston valinnassa lienee hyvä painottaa myös kehittäjäystävällisyyttä.


### Open questions
* Do we need a concept for RoutePathLink or can we just model a sequence of PointsOnRoute?
   * How to model “Via-nimet”
   * A route link might have a different transit type than the line. How should this be handled?
