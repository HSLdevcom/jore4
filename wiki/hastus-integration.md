# Hastus integration

## Q&A

Q: What kind of calendar Hastus uses? How it is modeled?
A: Giro proprietary data model. But it is at least possible to export in Netex. Support includes possibility to export in Netex Nordic profile (so-called Norwegian dialect)

Q: As a developer I want to know is it possible to integrate to Hastus so that we have the features used there at my disposal.
A: Hastus is able to import from CSV/XML files and export those as well in different standards.

Q: What kind of APIs does Hastus offer?
A: It is possible to create a batch job for CSV/XML exports/imports, no direct API provided

Q: What integrations could be done automatically?
A: It is possible to create a batch job

Q: If not possible, is there a way to combine multiple vehicle schedules to one export file Hastus -> Jore?
A: 

Q: What kind of state does Hastus store? Can automatic import / export create conflicts with Hastus' own state?
A: 

Q: If both Jore3 and Jore4 push data into Hastus, will we get conflicts? Do we need a separate Hastus instance for Jore4?
A: Newer import overrides former import of the same schedule/route/calendar etc.

Q: What data formats can Hastus import and export?
A: At least CSV and XML for most features.

Q: Are all route points part of data format? Or only stops?
A: 

Q: What lenght information is in the data format?  (1) "jr_linkki" (measured or calculated from geometry of route path link), (2) "jr_pysakkivali" (from stop to another), (3) "jr_reitinsuunta" (total length of route into one direction)
A: 

Q: Is there a documentation of Hastus-Jore3 CSV format?
A: Yes, there is a document "Hastus aikataulujen siirto Joreen ja Kolaan, rajapinnan kuvaus" available but it could not be linked here due to copyright reasons. Please ask PO.

## Available documentation

HSL has documentation for Hastus, please ask PO. Licencing has not been figured out so it is not linked here.
