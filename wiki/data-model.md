This file aims to document the Jore4 data model and open questions regarding future development of the data model.

Routes and lines
----------------

TBD, see [ticket](https://github.com/HSLdevcom/jore4/issues/584).

Open questions
--------------

1. How can a temporarily closed / out of order stop point in a service pattern be modelled?
   - e.g. ending stop point's validity time and creating a new one whose validity time starts in the future?
   - what about a stop point not being accessable only by certain journey patterns?

2. How to model a stop point, whose usability / access is limited (e.g. wheel chair access not possible)?
   - stop point register's data should be connected to route and line module's data?
