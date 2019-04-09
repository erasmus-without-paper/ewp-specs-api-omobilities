Outgoing Mobilities API
=======================

* [What is the status of this document?][statuses]
* [See the index of all other EWP Specifications][develhub]


Summary
-------

This document describes the **Outgoing Mobilities API**. This API is
implemented by the sending institution. It allows the receiving HEI to read,
write and enumerate mobilities stored on the sending HEI's servers.

Currently, this API describes mobilities **of one type only** - *Student
Mobilities for Studies*. More types MAY be added in the future.


Reminder on vocabulary
----------------------

Keep in mind that definitions of "sending HEI" and "receiving HEI" come from
the "mobility vocabulary", not the "HTTP vocabulary". In case of this
particular API this means that:

* **sending HEI == responding HEI** (HEI which is sending the student == HEI
  which implements the API, *receives* the HTTP request, and responds to it),
* **receiving HEI == requesting HEI** (HEI which is receiving the student ==
  HEI which implements the client, and *sends* the HTTP request).

As long as we use these terms consistently, there shouldn't be much confusion
though.


Security
--------

This version of this API uses [standard EWP Authentication and Security, Version 2][sec-v2].
Server implementers choose which security methods they
support by declaring them in their Manifest API entry.

This API handles data which is considered private. Server implementers are
allowed to forbid less-secure methods of authentication and encryption for this
API (by dropping support for them). Currently, we leave it for the server
implementers to decide which methods are "secure enough". These recommendations
MAY change in the future.


Endpoints to be implemented
---------------------------

Server implementers MUST:

 * Implement the [`get` endpoint](endpoints/get.md).
 * Implement the [`index` endpoint](endpoints/index.md).
 * Put the URLs of these endpoints in their [manifest file][discovery-api], as
   described in [manifest-entry.xsd](manifest-entry.xsd).

The details on each of these endpoints are described on separate pages of this
API specification (use the links above).


Data model entities involved in the response
--------------------------------------------

 * Mobility
 * Person
 * Coordinator
 * IIA Partner
 * Academic Term


Workflows of changes in nomination and departure statuses
---------------------------------------------------------

Mobility and its nomination have two different sets of statuses sent via Outgoing/Incoming Mobilities API get response. Example scenarios of status changes are presented below.

* `--MOBILITYSTATUS-->` - Outgoing Mobilities API get response
* `<--NOMINATIONSTATUS--` - Incoming Mobilities API get response
* `S` - sending HEI
* `R` - receiving HEI


**1.**

* S informs R via CNR about new nomination.
* `S --NOMINATION--> R`
* S ask R about nomination, but R has done nothing yet.
* `S <--PENDING-- R`
* R informs S that via CNR that nomination was changed.
* `S <--REJECTED-- R`
* S identifies problem and corrects nomination data
* S informs R via CNR about changed nomination.
* `S --NOMINATION--> R`
* R informs S via CNR that nomination was changed.
* `S <--VERIFIED-- R`

**2.**

* Nomination was sent but student or S wants to cancel the mobility.

  Initial state:
  
  `S --NOMINATION--> R`
  
  `S <--ANY-- R`

* S informs R via CNR about changed nomination.
* `S --CANCELLED--> R`

**3.**

* Nomination status is `VERIFIED` and student is about to leave for R.

  Initial state:

  `S --NOMINATION--> R`
  
  `S <--VERIFIED-- R`

* S informs R via CNR about changed nomination/mobility.
* `S --LIVE--> R`
* From this moment we don't care about nomination status.
* Time passes.
* Student passes all the exams and returns to S.
* S informs R via CNR about changed mobility.
* `S --RECOGNIZED--> R` (OR, sometimes, `S --LIVE--> R` - some HEIs don't store explicit information about mobility recognition)

**4.**

* Nomination has been accepted by the receiving HEI, and all initial formalities have been settled. Student is about to leave for R. Suddenly, student or S wants to cancel the mobility.

  Initial state:
  
  `S --LIVE--> R`
  
  `S <--VERIFIED-- R`

* S informs R via CNR about changed mobility.
* `S --CANCELLED--> R`

**5.**

* Student returns prematurely from R. Student can't justify it and has to return money from grant.

  Initial state:
  
  `S --LIVE--> R`
  
  `S <--VERIFIED-- R`

* S informs R via CNR about changed mobility.
* `S --CANCELLED--> R`

**6.**

* Student returns prematurely from R. Student can justify it (force majeure) and doesn't have to return money from grant.

  Initial state:
  
  `S --LIVE--> R`
  
  `S <--VERIFIED-- R`

* S informs R via CNR about changed mobility.
* `S --RECOGNIZED--> R` (OR, sometimes, `S --LIVE--> R` - some HEIs don't store explicit information about mobility recognition)


[develhub]: http://developers.erasmuswithoutpaper.eu/
[statuses]: https://github.com/erasmus-without-paper/ewp-specs-management#statuses
[discovery-api]: https://github.com/erasmus-without-paper/ewp-specs-api-discovery
[sec-v2]: https://github.com/erasmus-without-paper/ewp-specs-sec-intro/tree/stable-v2
