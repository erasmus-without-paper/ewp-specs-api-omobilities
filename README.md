Outgoing Mobilities API
=======================

* [What is the status of this document?][statuses]
* [See the index of all other EWP Specifications][develhub]


Summary
-------

This document describes the **Outgoing Mobilities API**. This API is
implemented by the sending institution. It allows the receiving institution to
retrieve given Outgoing Mobility objects. **Each** such object describes
**a complete history of a single student mobility**.


Reminder on vocabulary
----------------------

Keep in mind that definitions of "sending HEI" and "receiving HEI" come from
the student mobility vocabulary. In case of this particular API this means
that:

* **sending HEI == responding HEI** (HEI which is sending the student == HEI
  which implements the API, and responds to the HTTP request),
* **receiving HEI == requesting HEI** (HEI which is receiving the student ==
  HEI which implements the client, and sends the HTTP request).

As long as we use these terms consistently, there shouldn't be much confusion
though.


Request method
--------------

 * Requests MUST be made with either HTTP GET or HTTP POST method. Servers MUST
   support both these methods. Servers SHOULD reject all other request methods.

 * Clients are advised to use POST when passing large number of parameters
   (servers MAY set a limit on their GET query string length).


Request parameters
------------------

Parameters MUST be provided in the regular `application/x-www-form-urlencoded`
format.


### `sending_hei_id` (required)

An identifier of the institution which is the master of the Outgoing Mobility
objects we are attempting to retrieve. This parameter MUST be required by the
server even if it covers only a single institution.

Note, that `mobility_id` identifiers should be unique within the entire EWP
Network, not only within a single sending HEI (see discussion
[here](https://github.com/erasmus-without-paper/general-issues/issues/10)).
That might imply that, in theory, the `sending_hei_id` parameter could be
skipped. However, it still exists, and it is required, for performance and/or
clarity reasons (see discussion [here]
(https://github.com/erasmus-without-paper/ewp-specs-api-echo/issues/3#issuecomment-228278115)).


### `mobility_id` (repeatable, required)

A list of Outgoing Mobility identifiers (no more than `<max-mobility-ids>`
items) - IDs of Outgoing Mobility objects the client wants to retrieve. All of
these identifiers should "belong" to the sending HEI provided in the
`sending_hei_id` parameter (otherwise, they will be ignored).

This parameter is *repeatable*, so the request MAY contain multiple occurrences
of it. The server is REQUIRED to process all of them.

Server implementers provide their own chosen value of `<max-mobility-ids>` via
their manifest entry (see [manifest-entry.xsd](manifest-entry.xsd)). Clients
SHOULD parse this value (or assume it's equal to `1`).


Permissions
-----------

All requests from the EWP Network MUST be allowed access to this API. Consult
the [Echo API][echo] specs for details on handling unprivileged requests.

However, only selected Outgoing Mobility objects should be accessible to the
caller:

 * If the caller covers the receiving HEI of the Outgoing Mobility object, then
   he MUST be allowed access.

 * If the caller covers the sending HEI of the Outgoing Mobility object, then
   he MAY be allowed access. (It seems reasonable, but we leave this decision
   to your team.)

 * All other callers SHOULD NOT be allowed access.

 * Note, that you will need to verify these access rights for each ID on the
   `mobility_id` list. It is possible that the caller has access to only some
   of the IDs he provided. (If this seems problematic, then you can always set
   your `<max-mobility-ids>` to `1`.)


Handling of invalid parameters
------------------------------

 * General [error handling rules][error-handling] apply.

 * Invalid (unknown) `mobility_id` values MUST be **ignored**. Servers MUST
   return a valid (HTTP 200) XML response in such cases, but the response will
   simply not contain the information on the unknown `mobility_id` values. If
   all values are unknown, servers MUST respond with an empty `<response>`
   element. This requirement is true even when `<max-mobility-ids>` is `1`.

 * If the caller doesn't have permission to read some of the `mobility_ids`,
   then such `mobility_ids` MUST also be **ignored**, exactly as above. Servers
   MUST return a valid (HTTP 200) XML response in such cases which MUST include
   proper Mobility objects for the rest of `mobility_ids`.

 * Currently, clients have no way of telling the difference between "this
   mobility does not exist" and "it does exist, but I don't have access
   to read it". In both cases, the proper `<mobility>` element will simply be
   missing from the response. If such distinction is requested, we may add it
   in future versions of this API.

 * If the length of `mobility_id` list is greater than `<max-mobility-ids>`,
   servers MUST respond with HTTP 400.


Response
--------

Servers MUST respond with a valid XML document described by the [response.xsd]
(response.xsd) schema. See the schema annotations for further information.


Data model entities involved in the response
--------------------------------------------

 * Mobility
 * Person
 * Coordinator
 * IIA Partner
 * Learning Agreement
 * Learning Agreement Component
 * Learning Opportunity Specification
 * Learning Opportunity Instance
 * Academic Term


[develhub]: http://developers.erasmuswithoutpaper.eu/
[statuses]: https://github.com/erasmus-without-paper/ewp-specs-management#statuses
[registry-spec]: https://github.com/erasmus-without-paper/ewp-specs-api-registry
[discovery-api]: https://github.com/erasmus-without-paper/ewp-specs-api-discovery
[echo]: https://github.com/erasmus-without-paper/ewp-specs-api-echo
[error-handling]: https://github.com/erasmus-without-paper/ewp-specs-architecture#error-handling
[institutions-api]: https://github.com/erasmus-without-paper/ewp-specs-api-institutions
