Outgoing Mobilities API
=======================

* [What is the status of this document?][statuses]
* [See the index of all other EWP Specifications][develhub]


Summary
-------

This document describes the **Outgoing Mobilities API**. This API is
implemented by the sending institution. It allows the receiving HEI to read
and enumerate mobilities stored on the sending HEI's servers.

Currently, this API describes mobilities **of one type only** - *Student
Mobilities for Studies*. More types MAY be added in the future.

If HEI provides any API from the following group:
* Outgoing Mobilities
* Outgoing Mobilities CNR
* Outgoing Mobilities Stats
* Incoming Mobilities
* Incoming Mobilities CNR
* Incoming Mobilities Stats

it must provide all APIs from this group.


### Business requirements and processes


[Business requirements and processes](resources/mandatory_business_requirements_nominations.pdf)
document clarifies the requirements for the technical solutions
developed under EWP and in the local implementation that should adequately support
the business processes related to nominations at Higher Education Institutions.


Reminder on vocabulary
----------------------

Keep in mind that definitions of "sending HEI" and "receiving HEI" come from
the "mobility vocabulary", not the "HTTP vocabulary". In the case of this
particular API, this means that:

* **sending HEI == responding HEI** (HEI which is sending the student == HEI
  which implements the API, *receives* the HTTP request, and responds to it),
* **receiving HEI == requesting HEI** (HEI which is receiving the student ==
  HEI which implements the client, and *sends* the HTTP request).

As long as we use these terms consistently, there shouldn't be much confusion
though.

For brevity, we will use the following shortcuts:
* `S` - sending HEI
* `R` - receiving HEI


Important rules
---------------

* The nomination is uniquely identified by the `omobility-id`.
* S sends the nomination to R – the nomination is in the `pending` state.
* When S is sure that R has received information about the nomination
  (it correctly received the CNR or performed a GET),
  S must immediately inform its users about it (an internal `delivered` status may be noted in the local system).
* R can accept the nomination – it changes the state to `approved`.
* R can reject the nomination – it changes the state to `rejected`.
* R cannot reject a nomination in the `approved` state.
* If the nomination is in the `approved` state, S can notify R about the change of the student's personal data
  (given names, family name, birthdate, nationality, gender, email).
  This notification does not require R to make a new decision (the nomination remains in the `approved` state).
* If the nomination is in the `approved` state, S cannot propose to R changes to this nomination
  in the data other than student’s personal data listed in the point above.
* If the nomination has been rejected, S can submit a proposal for changes to this nomination
  (this requires changing the `proposal-id`). R can accept or reject this proposal.
* S can cancel the nomination at any time – it changes the status to `cancelled`.
  Such a nomination cannot be submitted for reconsideration.


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
 * Implement the [`update` endpoint](endpoints/update.md).
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


Example scenarios of nomination status changes
----------------------------------------------

The example scenarios of nomination status changes will be described using the following symbols:

* `--MOBILITY-STATUS-->` - Outgoing Mobilities API get response
* `<--MOBILITY-UPDATE--` - Outgoing Mobilities API update request


### Student changes her surname after a nomination

* S informs R via CNR about a new nomination.
* `S --PENDING--> R`
* S ask R about a nomination, but R has done nothing yet.
* R informs S that via CNR that nomination has been approved.
* `S <--APPROVE-- R`
* Student nominated by S changes her surname.
* S informs R via CNR about a changed nomination.
* `S --PENDING--> R`
* R does nothing.

### Nomination cancelled by a student or sending HEI

* Nomination was sent but a student or S wants to cancel the mobility.

  Initial state:
  `S --PENDING--> R`

* S informs R via CNR about a nomination cancellation.
* `S --CANCELLED--> R`

### Nomination cancelled after approval

* Nomination has been approved by the receiving HEI, and all initial formalities have been settled.
  Student is about to leave for R. Suddenly, a student or S wants to cancel the mobility.

  Initial state:
  `S --APPROVED--> R`

* S informs R via CNR about mobility cancellation.
* `S --CANCELLED--> R`

### Student returns prematurely - no justification

* Student returns prematurely from R. Student can't justify it and has to return money from grant.

  Initial state:
  `S --APPROVED--> R`

* S informs R via CNR about mobility cancellation.
* `S --CANCELLED--> R`


[develhub]: http://developers.erasmuswithoutpaper.eu/
[statuses]: https://github.com/erasmus-without-paper/ewp-specs-management#statuses
[discovery-api]: https://github.com/erasmus-without-paper/ewp-specs-api-discovery
[sec-v2]: https://github.com/erasmus-without-paper/ewp-specs-sec-intro/tree/stable-v2
