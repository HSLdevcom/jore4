# Infrastructure networks in Jore4

Jore4 uses infrastructure networks from external sources. Jore4 does not include functionalities to modify infrastructure.

## Bus

Digiroad with our custom fix layer for bus terminals and temporary infrastructure.

## Tram

Potential sources: Digiroad/MML, City of Helsinki (Kaupunkimittaus) or HKL

Helsinki/Kaupunkimittaus & HKL will discuss the possibility to maintain a network dataset in the future. Based on this discussion HSL's role will also be clarified. Possible roles are :
- user of the dataset provided by Helsinki/Kaupunkimittaus or HKL
- maintainer of the dataset (whether there will be no maintained dataset available elsewhere)

In November 30th 2021 we received a dataset from HKL (available from Drive). Arttu K's remarks:

Vilkaisin aineistoa nopeasti QGIS:issä, ja ihan hyvältä näyttää mittatarkkuuden osalta. Alla tarkempia esimerkkejä. Mutta arvioisin, että mittatarkan ja topologisesti eheän aineiston laatiminen vaikkapa tätä pohjadataa käyttäen (jos HKL:n kanssa sovittaisiin) veisi ehkä 1-2 työpäivää. Osan työstä voi automatisoida, esim. linkkien katkominen oikeissa kohdissa ST_Touches(geom1, geom2)-predikaatin avulla, osa taas olisi manuaalista, kuten katkeavien geometrioiden fiksaaminen. Aineisto on kokonaisuudessaan niin pieni, ettei tästä mielestäni kannata tehdä ongelmaa. Ja ratikkaverkko uudistuu sen verran hallittavissa palasissa, että päivittämiseenkään ei kai tarvitsisi niin järeää automaatiota ym. kuin mitä tieverkko kaikkine osineen vaatii.

Keskeisempi ongelma ratkaistavaksi onkin se, kuka tämän Jore-kelpoisen ratikkalinkkiaineiston ja sen ylläpitoprosessin omistaisi, ja onko esimerkiksi HKL:llä tarve pitää se ja nämä dwg-/dgn-aineistot jotenkin tietomallin tasolla synkassa. Edelleenkään tästä ei mielestäni kannattaisi tehdä liian mutkikasta ja pitkälle automatisoitua, koska ratikka-aineisto on niin pieni verrattuna tieverkkoon – pääasia, että tälle löytyisi joku järkevä omistaja ja simppeli prosessi kaikille osapuolille.

Aineisto ei ole topologisesti eheä (eikä varmaan yritäkään olla, koska oletettavasti tehty AutoCAD:illä tai vastaavalla dwg/dgn-muotoon), eli mallinnetut raidelinkit eivät katkea erkanemiskohdissa, ja myös päällekkäisiä geometrioita on paljon. Esim. tässä kuvassa punainen osuus (kuvan ulkopuolelle asti koko Kirurgin silmukka) on kuvattu yhtenä geometriana. 

Vaihteiden erkanemiskohdat eivät yhdisty oikein topologisesti eheän verkon aikaansaamiseksi, vaan tuolla on < 1 m heittoja, joiden takia topologia saattaisi käytetystä toleranssista riippuen katketa.

## Train

Two options, we can pick one that is easier to use:
- [Väylävirasto](https://julkinen.vayla.fi/oskari/?zoomLevel=2&coord=429230.65625_6755196&mapLayers=1+100+default,481+100+avoin:ratko_line&uuid=e139ce6f-ad06-4d64-a49f-b7e57c27e81d&noSavedState=true&showIntro=false), available also as a WFS-api
- [Fintraffic](https://rata.digitraffic.fi/infra-api/)

## Metro

HSL creates the layer with the possible help of HKL and Helsinki/Kaupunkimittaus with some assisting datasets

## Ferry

HSL creates the layer based on ferry GPS-data
