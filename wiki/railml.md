# railML

One option for getting schedule data from VR is to import [railML](https://www.railml.org/) from the VR capacity planning system IVU.

As the rail world has its own information and ticketing standards, certified hardware and custom software, we should not get mired in full compatibility.
What do we need?

## Notes

Notes about the IVU example XML file from VR:
- `routeFilter` has foreign IDs but some are recognizable
- `tripClassFilter` has foreign IDs
- `vehicleTypeFilter` can be understood but maybe foreign IDs
- `operationControlPoints` has mapping between station names and station codes
  - more on this in [Official station names](#official-station-names)
- guess: `operatingPeriod` uses `bitMask` to signify the date
  - the example XML unfortunately has only one week with Monday on 2021-02-01
- what are categories useful for?
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

What are the official train station names and IDs?
Where is the master for them?

Maybe Fintraffic is responsible for them.

Digitraffic might not be the most suitable API for querying Fintraffic station data but [this GraphQL query](https://rata.digitraffic.fi/api/v2/graphql/graphiql?query=query%20AllStations%20%7B%0A%20%20trainsByDepartureDate(departureDate%3A%20%222021-02-10%22)%20%7B%0A%20%20%20%20timeTableRows%20%7B%0A%20%20%20%20%20%20station%20%7B%0A%20%20%20%20%20%20%20%20passengerTraffic%0A%20%20%20%20%20%20%20%20countryCode%0A%20%20%20%20%20%20%20%20location%0A%20%20%20%20%20%20%20%20name%0A%20%20%20%20%20%20%20%20shortCode%0A%20%20%20%20%20%20%20%20uicCode%0A%20%20%20%20%20%20%20%20type%0A%20%20%20%20%20%20%7D%0A%20%20%20%20%7D%0A%20%20%7D%0A%7D%0A&operationName=AllStations) lists some stations:
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

The result is too long to paste here but cursory skimming indicates that the Digitraffic station `shortCode`s matches `<ocp code="FOO">` in the IVU railML.

There can be a process for introducing new stations from Fintraffic to Jore4.
As building of new stations or renaming of old stations happens so rarely, another option is to hardcode the translation.
