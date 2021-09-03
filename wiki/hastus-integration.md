# Hastus integration

## Q&A

Q: What kind of calendar Hastus uses? How it is modeled?
A: Hastus has quite advanced calendar feature (documentation available from PO). It is modeled in Giro proprietary data model. But it is at least possible to export in Netex. Support includes possibility to export in Netex Nordic profile (so-called Norwegian dialect)
Giro : Yes, I would like to emphasize on the fact, this is not a generic behavior. It requires site-spec modification and the costs involved are significant.

Q: As a developer I want to know is it possible to integrate to Hastus so that we have the features used there at my disposal.
A: Hastus is able to import from CSV/XML files and export those as well in different standards.
Giro : Our export tools are very flexible and can export CSV, XML and Json. Our import tools are much more limited.

Q: What kind of APIs does Hastus offer?
A: It is possible to create a batch job for CSV/XML exports/imports, no direct API provided
Giro : I should verify if we can automate imports.

Q: What integrations could be done automatically?
A: It is possible to create a batch job

Q: If not possible, is there a way to combine multiple vehicle schedules to one export file Hastus -> Jore?
A: 
Giro : Depending on the format, you can export any number of schedule to a single file. Netherland’s NeTEx format exports +300 schedules to a single 500mb+ file. Norway format separates data by lines.

Q: What kind of state does Hastus store? Can automatic import / export create conflicts with Hastus' own state?
A: 
Giro: It would require a formal analysis to answer this and this is not covered by regular support. You should be VERY careful in the choice of data to import in HASTUS. I do not recommend to plan to import/export other things than route definitions, stops / places data and other static data. Any “complicated” data such as Schedule / Calendar can possibly be exported, but most of the time, it cannot be updated (by import) after that i.e it’s a “one shot deal import”. In the case of calendar, we have tools to simplify the calendar transition from a booking to another one (ex. Copy base service to the new calendar).

Q: If both Jore3 and Jore4 push data into Hastus, will we get conflicts? Do we need a separate Hastus instance for Jore4?
A: Newer import overrides former import of the same schedule/route/calendar etc.
Giro : Import of « complicated » object is most of the time not supported. You cannot re-import a schedule and probably not a calendar. Static objects such as Routes can be re-imported however.

Q: What data formats can Hastus import and export?
A: At least CSV and XML for most features.

Q: Are all route points part of data format? Or only stops?
Giro : I’m not sure I understand this point. This is a “complicated” answer. Places are used to define times and to regroup stops. Both seems to be used un HSL’s route variants. 
A: As a clarification of Giro's answer: Only stops are part of the data format, not crossroads

Q: What lenght information is in the data format?  (1) "jr_linkki" (measured or calculated from geometry of route path link), (2) "jr_pysakkivali" (from stop to another), (3) "jr_reitinsuunta" (total length of route into one direction)
A: Everything

Q: Is there a documentation of Hastus-Jore3 CSV format?
A: Yes, there is a document "Hastus aikataulujen siirto Joreen ja Kolaan, rajapinnan kuvaus" available but it could not be linked here due to copyright reasons. Please ask PO.

## Available documentation

HSL has documentation for Hastus, please ask PO. Licencing has not been figured out so it is not linked here.
