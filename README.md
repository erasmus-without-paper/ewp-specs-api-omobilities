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


Request method
--------------

 * Requests MUST be made with either HTTP GET or HTTP POST method. Servers MUST
   support both these methods. Servers SHOULD reject all other request methods.

 * Clients are advised to use POST when passing large number of parameters
   (servers MAY set a limit on their GET query string length).


Request parameters
------------------

Parameters MUST be provided either in a query string (for GET requests), or in
the `application/x-www-form-urlencoded` format (for POST requests).


### `mobility_id` (repeatable, required)

A list of Outgoing Mobility identifiers (max 100 items) - IDs of Outgoing
Mobility objects the client wants to retrieve.

This parameter is *repeatable*, so the request MAY contain multiple occurrences
of it. The server is REQUIRED to process all of them.

Note, that `mobility_id` identifiers are unique within the entire EWP Network
(see discussion
[here](https://github.com/erasmus-without-paper/general-issues/issues/10)), so
there is no reason for the `hei_id` parameter to exist.


Permissions
-----------

All requests from the EWP Network MUST be allowed access to this API. Consult
the [Echo API][echo] specs for details on handling unprivileged requests.

However, only selected Outgoing Mobility objects should be accessible to the
caller:

 * If the caller is receiving HEI of the Outgoing Mobility object, then he MUST
   be allowed access to read the mobility data.

 * If the caller is the sending HEI (yourself), then he MAY be allowed
   access to read the mobility data. (It seems reasonable, but it's really an
   internal decision of your team.)

 * All other callers SHOULD NOT be allowed to view the mobility data.

 * Note, that you will need to verify these access rights for each ID on the
   `mobility_id` list. It is possible that the caller has access to only some
   of the IDs he provided.


Handling of invalid parameters
------------------------------

 * General [error handling rules][error-handling] apply.

 * Invalid (unknown) `mobility_id` values MUST be ignored. Servers MUST return
   a valid (HTTP 200) XML response in such cases, but the response will simply
   not contain the information on the unknown `mobility_id` values. (If all
   values are unknown, servers MUST respond with an empty envelope.)

 * If the caller doesn't have permission to view some of the `mobility_ids`,
   then such `mobility_ids` MUST also be ignored, exactly as above. Servers
   MUST return a valid (HTTP 200) XML response in such cases.

 * Currently, clients have no way of telling the difference between "this
   `mobility_id` does not exist" and "it does exist, but you don't have access
   to it". In both cases, the proper `<mobility>` element will simply be
   missing from the response. If such feature is requested, we may add it in
   future versions of this API.

 * If the length of `mobility_id` list is greater than 100, servers MAY respond
   with HTTP 400. Clients SHOULD split such large requests into a couple of
   smaller ones.


Response
--------

Servers MUST respond with a valid XML document described by the [response.xsd]
(response.xsd) schema. See the schema annotations for further information.


[develhub]: http://developers.erasmuswithoutpaper.eu/
[statuses]: https://github.com/erasmus-without-paper/ewp-specs-management#statuses
[registry-spec]: https://github.com/erasmus-without-paper/ewp-specs-api-registry
[discovery-api]: https://github.com/erasmus-without-paper/ewp-specs-api-discovery
[echo]: https://github.com/erasmus-without-paper/ewp-specs-api-echo
[error-handling]: https://github.com/erasmus-without-paper/ewp-specs-architecture#error-handling
[institutions-api]: https://github.com/erasmus-without-paper/ewp-specs-api-institutions
