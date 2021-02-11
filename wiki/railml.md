# railML

One option for getting schedule data from VR is to import [railML](https://www.railml.org/) from the VR capacity planning system IVU.

As the rail world has its own information and ticketing standards, certified hardware and custom software, we should not get mired in full compatibility.
What do we need?

## Notes

Notes about the IVU example XML file from VR:
- filters (export information as CDATA, not part of xsd):
  - `routeFilter` has foreign IDs but some are recognizable
  - `tripClassFilter` has foreign IDs
  - `vehicleTypeFilter` can be understood but maybe foreign IDs
- `operationControlPoints` has mapping between station names and station codes
  - more on this in [Official station names](#official-station-names)
- guess: `operatingPeriod` uses `bitMask` to signify the date
  - the example XML unfortunately has only one week with Monday on 2021-02-01
- what are `categories` useful for?
- trainParts:
  - `trainPart` seems to match Transmodel DatedVehicleJourney
  - `formationTT` signals the vehicle type/model
  - `trainNumber` is likely some kind of block or vehiclejourney
  - `direction` is Helsinki -> Lahti or Lahti -> Helsinki
    - this interpretation could but should not be parsed from trainLineDescription. so from where?
  - `operationPeriodRef` refers to day
  - guess: `ocpType`: stopping on or passing the station
  - guess: `departureDay="1"` means departure happens on the next day compared to operationPeriodRef
  - guess: `KÄP1(1)->OLK1(1)`: the numbers in parantheses mean the platform
- `train` seems to match Transmodel VehicleJourney/ServiceJourney

## Official station names

Official train station names and IDs are given by [the Finnish Transport Infrastructure Agency (Väylävirasto)](https://julkaisut.vayla.fi/pdf12/vj_2019-25_luettelo_rautatieliikennepaikoista_web.pdf).

Digitraffic might not be the most suitable API for querying stations but [this GraphQL query](https://rata.digitraffic.fi/api/v2/graphql/graphiql?query=query%20AllStations%20%7B%0A%20%20trainsByDepartureDate(departureDate%3A%20%222021-02-10%22)%20%7B%0A%20%20%20%20timeTableRows%20%7B%0A%20%20%20%20%20%20station%20%7B%0A%20%20%20%20%20%20%20%20passengerTraffic%0A%20%20%20%20%20%20%20%20countryCode%0A%20%20%20%20%20%20%20%20location%0A%20%20%20%20%20%20%20%20name%0A%20%20%20%20%20%20%20%20shortCode%0A%20%20%20%20%20%20%20%20uicCode%0A%20%20%20%20%20%20%20%20type%0A%20%20%20%20%20%20%7D%0A%20%20%20%20%7D%0A%20%20%7D%0A%7D%0A&operationName=AllStations) lists some:
```graphql
query AllStations {
  trainsByDepartureDate(departureDate: "2021-02-10") {
    timeTableRows {
      station {
        passengerTraffic
        countryCode
        location
        name
        shortCode
        uicCode
        type
      }
    }
  }
}
```

The result is too long to paste here but cursory skimming indicates that the Digitraffic station `shortCode` matches `<ocp code="FOO">` in the IVU railML.
`shortCode` is the commonly used ID for train stations used by public transport organizations in Finland.

There can be a process for introducing new stations from the Finnish Transport Infrastructure Agency to Jore4.
As building of new stations or renaming of old stations happens so rarely, another option is to hardcode the translation.

## Questions and answers from the railML-meeting between dev team and HSL on 2021-02-11

### General questions about the development / system process

1. *What information does HSL require from VR? Why?*

As an operator VR provides HSL with information about
- the planned rail traffic schedules,
- the planned train station platform choices and
- the planned rolling stock.

These are used for passenger information purposes and checking the plans pre-emptively for following the contract.

Post-checking for following the contract happens with the observation data that does not come from IVU.

2. *How does the data delivery process work? How much can the railML import be automated? Is user interaction needed?*

HSL can get some rail traffic information, including last-minute platform changes, from [Digitraffic](https://www.digitraffic.fi/).
Digitraffic offers data only for the near future, maybe 10 days, though.

Instead VR can provide information as soon as they have planned it.
That happens usually many months in advance.

The plan is that the data delivery is done using a push protocol.
VR will push their latest changes from IVU to HSL integration platform (HIA).
HIA will push it into Jore4.

3. *For which parts of the delivered data / info is VR the owner? Which parts of the data are owned by other stakeholders?*

We know that the Finnish Transport Infrastructure Agency (Väylävirasto) controls the naming of the stations.

We do not know owners of the other data.

For example, [Metropolitan Area Rolling Stock (Pääkaupunkiseudun Junakalusto)](http://junakalusto.fi/) might be the owner of the rolling stock information.

4. *What other formats, e.g. JSON, are possible?*

JSON was mentioned in the contract as the data encoding.
At the time there was no knowledge of the railML format or the Jore4 project.

No further specification for the JSON data is known.
It is unlikely to be used.

### Questions about railML and its import

1. *Is filtering of stations (which are outside of HSL's area of operation) required?*
Or would importing all available station information be useful for some scenarios (e.g. delayed bus connection departures)?

Filtering has caused trouble or inconsistencies in the past.
Journeys of R trains from HSL area onwards to Tampere have been cropped but Z trains have not been cropped.
Probably we prefer not to filter the data.

2. *What do the numeric `routeFilter`-IDs mean? (There are no such regular train lines.)*

`routeFilter` values are not fully known.
`"V_*"` refers to deadruns, i.e. transporting vehicles from one place to the next without serving passengers.

We might be able to filter out the unnecessary line G by changing this.

3. *Can there be operational days ("liikennöintivuorokausi") of more than 24h in railML?*

Very likely VR does not use operational days of more than 24 hours as some other systems of theirs may not be able to handle it.

4. *Should we save / process the distance measures between stops contained within railML? What are they used for?*

Let's keep the network structure separate from the schedule.
This railML export should not overwrite any of the network.
