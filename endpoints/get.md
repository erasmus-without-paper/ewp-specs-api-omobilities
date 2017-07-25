Outgoing Mobility Get endpoint
==============================

* [What is the status of this document?][statuses]
* [See the index of all other EWP Specifications][develhub]


Summary
-------

This endpoint allows the client to retrieve information on specific outgoing
mobilities from the sending HEI.


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

SCHAC ID of the mobilities' owner HEI (in EWP, the *sending* HEI is always the
mobility's "owner"). This parameter MUST be required by the server even if the
server covers only a single institution.


### `mobility_id` (repeatable, required)

A list of identifiers (no more than `<max-mobility-ids>` items) of mobilities
which the client wants to retrieve information on. All of these mobilities
should be the outgoing mobilities of the sending HEI provided in the
`sending_hei_id` parameter (otherwise, they will be ignored).

This parameter is *repeatable*, so the request MAY contain multiple occurrences
of it. The server is REQUIRED to process all of them.

Server implementers provide their own chosen value of `<max-mobility-ids>` via
their manifest entry (see [manifest-entry.xsd](manifest-entry.xsd)). Clients
SHOULD parse this value (or assume it's equal to `1`).


Permissions
-----------

Only a subset of all known mobilities should be made available to the caller:

 * If the caller covers the receiving HEI of this mobility, then he MUST be
   allowed access to it.

 * If the caller covers the sending HEI of this mobility, then he SHOULD be
   allowed access to it. (It seems reasonable, but we leave this decision
   to your team.)

 * All other callers probably SHOULD NOT be allowed access, but we leave this
   decision to your team.

 * Note, that server implementers need to verify these access rights for each
   ID on the `mobility_id` list. It is possible that the caller has access to
   only some of the IDs he provided. If this seems problematic, then you (the
   server implementer) can always set your `<max-mobility-ids>` to `1`.


Handling of invalid parameters
------------------------------

 * General [error handling rules][error-handling] apply.

 * Invalid (unknown) `mobility_id` values MUST be **ignored**. Servers MUST
   return a valid (HTTP 200) XML response in such cases, but the response will
   simply not contain the information on the unknown `mobility_id` values. If
   all values are invalid/unknown, servers MUST respond with an empty
   `<response>` element. This requirement is true even when
   `<max-mobility-ids>` is `1`.

 * If the caller doesn't have permission to read some of the `mobility_ids`,
   then such `mobility_ids` MUST also be **ignored**, exactly as above. Servers
   MUST return a valid (HTTP 200) XML response in such cases, and this response
   MUST include all the remaining mobilities (the ones that the requester *has*
   access to). If the requester doesn't have access to none of the requested
   `mobility_ids`, an empty HTTP 200 `<response>` element MUST be returned.

 * Note, that currently clients have no way of telling the difference between
   "this mobility does not exist" and "it does exist, but I don't have access
   to read it". In both cases, the proper `<mobility>` element will simply be
   missing from the response.

 * If the length of `mobility_id` list is greater than `<max-mobility-ids>`,
   servers MUST respond with HTTP 400.


Response
--------

Servers MUST respond with a valid XML document described by the
[get-response.xsd](get-response.xsd) schema. See the schema annotations for
further information.


[develhub]: http://developers.erasmuswithoutpaper.eu/
[statuses]: https://github.com/erasmus-without-paper/ewp-specs-management#statuses
[mobilities-api]: https://github.com/erasmus-without-paper/ewp-specs-api-mobilities
[echo]: https://github.com/erasmus-without-paper/ewp-specs-api-echo
[error-handling]: https://github.com/erasmus-without-paper/ewp-specs-architecture#error-handling
