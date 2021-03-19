# Existing Jore 3 Integrations

![Overview](images/jore3_integrations_overview.png "Architecture diagram")

## Document Import & Exports

Jore 3 offers several endpoints for importing & exporting data (e.g. CSV, Excel..) to external systems or use cases.

### HASTUS

[Hastus](https://www.giro.ca/en-ca/our-solutions/hastus-software/) is a separate timetable planning software.

#### Route API

Routes are uploaded to Hastus using a plain CSV file, which can be exported from Jore using the desktop UI.

```
Format: CSV
Contact: Ossi Berg
Data:
  - routes
  - stop places and distances between them
```

Sample:

```
...
rvpoint;4ROT;6.387;1;0;0;4920208;1091K2
place;1PRN;Puroniitty
stop;1600207;;Talosaarentie;Husövägen;Uusi Porvoontie;Nya Borgåvägen;;60.249545;25.164438;H5007
stpdist;1600207;1600209;1;696
...
```

#### Schedule API

Schedules are retrieved from Hastus using a plain CSV file, and imported to Jore using the desktop UI.

```
Format: CSV
Contact: Ossi Berg
Data:
  - vehicle schedules / "kaaviot"
    -- departure and arrival times
    -- "special days"
    -- stop place timing information
    -- vehicle assignments
```

Sample:

```
...
6;14731707;;4940297;;;;;0722;197.0;;
6;14731707;;4940204;;;;;0722;350.0;;
6;14731707;;4940206;;;;;0723;204.0;;
6;14731707;;4940208;;;;;0724;435.0;;
...
```

### Excel Exports

#### Route Usage Export

```
Filename: reitin_lmaarat.xls
Format: XLS
Contact: Ossi Berg, Natalia Berezina
Data:
  - ??
```

#### Route Lengths Export

```
Filename: reitin_tiedot.xls
Format: XLS
Contact: Natalia Berezina
Data:
  - ??
```

#### "Suunnitellut suoritteet" Export

```
Filename: "Massalistaus Exceliin suunnitellut suoritteet.xlsx"
Format: XLSX
Contact: Oskari Leho
Data:
  - ??
```

#### "Suoritteet ja korvaukset" Export

```
Filename: "Massalistaus Exceliin suoritteet ja korvaukset.xlsx"
Format: XLSX
Contact: Oskari Leho
Data:
  - ??
```

#### Bus Stop Equipment Export

```
Filename: "Varustelutiedot.xlsx"
Format: XLSX
Contact: Joona Packalén, Kari Lehtonen
Data:
  - ??
```

#### Poster Export for Terminals

```
Filename: "Julistepaikkatiedot.xlsx"
Format: XLSX
Contact: Joona Packalén
Data:
  - ??
```

#### "Infopaikka" Export

```
Filename: "Infopaikkatiedot.xlsx"
Format: XLSX
Contact: Joona Packalén
Data:
  - ??
```

#### Stop time export for a route Export

```
Filename: "valipisteajat.xlsx"
Format: XLSX
Contact: Ossi Berg
Data:
  - ??
```

### Word Exports

#### Poster Installation Instructions Export for Terminals

```
Filename: "vb2JulistePaikat.docx"
Format: DOCX
Contact: Joona Packalén
Data:
  - ??
```

#### Plate Installation Instructions for Stop Places

```
Filename: "vb2Kilvitys.docx"
Format: DOCX
Contact: Joona Packalén
Data:
  - ??
```

## Database Batch Jobs

Several Jore 3 integrations are implemented as scheduled database batch jobs, which first extract the necessary data and then submit it further to the target integrations in various formats (e.g. to a FTP server or a web service API).

### Lippu- ja informaatiojärjestelmä (LIJ)

The LIJ integration is a web service integration based on the Hogia [Pubtrans](https://www.hogia.se/int/public-transport-system/solutions) WSDL schema.

```
Protocol: SOAP
Format: XML
Data:
  - ??
```

### Google GTFS

```
Format: ??
Protocol: ??
Data:
  - ??
```

### Infopoiminta

```
Format: ??
Protocol: ??
Data:
  - ??
```

### Digiroad

```
Format: ??
Protocol: ??
Data:
  - ??
```

### Kalustorekisteri

```
Format: ??
Protocol: ??
Data:
  - ??
```

### Jore History GraphQL Importer

Source code is available on [github](https://github.com/HSLdevcom/jore-history-graphql-import).

```
Format: ??
Protocol: ??
Data:
  - ??
```

## Direct Database integrations

Some Jore 3 integrations directly read data from the Jore database.

### Bultti

Source code is available on [github](https://github.com/HSLdevcom/bultti), see e.g. the [Jore DB API](https://github.com/HSLdevcom/bultti/blob/master/src/jore/JoreDataSource.ts).

## Web Service integrations

Jore contains a web service interface (WSDL), which is used by some integrations to query data.

See `Jore_WS_Metodit.doc` document.

### Lissu

Lissu is an analysis toolkit.

```
Protocol: SOAP
Format: XML
Data:
  - "liikennemuototyypit"
  - routes
  - stop places
  - stop place usage statistics
```

### Taku

[Taku](https://www.crasman.fi/asiakkaat/hsl) is a page layout tool by [Crasman](https://www.crasman.fi/) used for designing physical timetable books.

```
Protocol: SOAP
Format: XML
Data:
  - Routes
  - Hastus places & bus stops
  - Timetables and charts
  - Equipments
```

## Other Integrations

### OMM (& Muuli)

Similar to LIJ, the OMM integration is based on the [Pubtrans](https://www.hogia.se/int/public-transport-system/solutions) web service. However, unlike LIJ, here Jore requests data from OMM.

Previously, a legacy system called _Muuli_ implemented this, but it was later replaced by OMM. Some Jore UI elements still refer to "OMM Muuli" when in fact they mean OMM.

```
Protocol: SOAP
Format: XML
Data:
  - vehicle journeys which were not run, "ajamattomat lähdöt"
```

### Microsoft Dynamics 365

Accounting information about journeys performed by public transport operators are stored in Microsoft Dynamics 365.

```
Format: XML
Protocol: ??
Data:
  - ??
```
