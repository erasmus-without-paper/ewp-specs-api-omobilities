Outgoing Mobilities Update endpoint
===================================

* [What is the status of this document?][statuses]
* [See the index of all other EWP Specifications][develhub]


Summary
-------

This endpoint allows the receiving HEI to update the mobility entity (stored on
the sending HEI). It allows to create new timeline entries remotely.


Request method
--------------

 * Requests MUST be made with HTTP POST method. Servers SHOULD reject all other
   request methods.


Request parameters
------------------

Parameters MUST be provided in the regular `application/x-www-form-urlencoded`
format.


### `sending_hei_id` (required)

An identifier of the institution which is the sending partner of the mobility
which the client wants to modify. This parameter MUST be required by the server
even if the server covers only a single institution.

Note, that `mobility_id` identifiers should be unique within the entire EWP
Network, not only within a single sending HEI (see discussion
[here](https://github.com/erasmus-without-paper/general-issues/issues/10)).
That might imply that, in theory, the `sending_hei_id` parameter could be
skipped. However, it still exists, and it is required, for performance and/or
clarity reasons (see discussion [here]
(https://github.com/erasmus-without-paper/ewp-specs-api-echo/issues/3#issuecomment-228278115)).


### `mobility_id` (required)

ID of Outgoing Mobility object the client wants to modify. It must "belong" to
the sending HEI provided in the `sending_hei_id` parameter.


### `sync_verifier` (required)

Integer. The current length of the mobility's timeline. "Current" means "before
this new change is made".

This parameter is required in order to prevent [edit conflicts]
(https://en.wikipedia.org/wiki/Edit_conflict). Before proceeding with updating
the mobility's timeline, the server is REQUIRED to verify if the requester was
in possession of the most recent version of this timeline.

Note, that this method is not perfect, because some properties of the mobility
can be updated without changing the timeline length.


### `append` (required)

XML element to be appended to the mobility's `<timeline>`. The value of this
parameter MUST be a valid XML content representing a single element. Consult
[`get-response.xsd`](get-response.xsd) for detailed specification of all
rules regarding timeline entries.

 * The server MUST validate `commiter-hei-id` element (present *within* the
   given XML). The server MUST verify that the caller is indeed covering this
   HEI (and is therefore allowed to append commits in its name).

 * The server MUST replace `commit-date` value (present *within* the
   given XML) with its own, current server time. This is to make sure that
   subsequent timeline entries have their commit-dates in order.

 * The server MUST validate the appended entry and make sure that they are
   valid in their context. If the specification does not explicitly state if
   the action should be allowed to be performed by the remote caller, then it
   is up to the server implementers to decide.

   This also means that clients cannot be 100% sure if their timeline entry
   will be accepted or not (regardless of how well their implementations are
   coded). Servers SHOULD supply a proper `<user-message>` element in their
   `<error-response>`, for clients to use in cases of such rejected requests.


Permissions
-----------

 * If the caller covers the receiving HEI of the Outgoing Mobility identified
   by `mobility_id` parameter, then he MUST be allowed to call this API.

 * If the caller covers the sending HEI of the mobility, then he MAY be allowed
   to call this API. (It seems reasonable, but we leave this decision to your
   team.)

 * All other callers MUST NOT be allowed access to this API.


Handling of invalid parameters
------------------------------

 * General [error handling rules][error-handling] apply.

 * Invalid or unknown `mobility_id` values MUST result in a HTTP 400 error
   response.

 * If object identified by `mobility_id` exists, but it is not visible to the
   requester (and thus, not modifiable too), then the server also MUST respond
   with HTTP 400 error.

 * Invalid or rejected `append` values (e.g. when the value does not conform to
   the proper XML Schema, or the element is otherwise rejected by the server)
   MUST result in a HTTP 400 error response. If the client did everything
   according to the specification, but the server decided to reject his request
   for its own reasons, then the server SHOULD supply a proper `<user-message>`
   element in their `<error-response>`.

 * Invalid (out-of-date) values of `sync_verifier` MUST result in HTTP 409
   error response. Clients which receive this error should refresh their stale
   Outgoing Mobility object and resolve all conflicts before resubmitting
   their requests.


Response
--------

Servers MUST respond with a valid XML document described by the
[update-response.xsd](update-response.xsd) schema. See the schema annotations
for further information.


[develhub]: http://developers.erasmuswithoutpaper.eu/
[statuses]: https://github.com/erasmus-without-paper/ewp-specs-management#statuses
[registry-spec]: https://github.com/erasmus-without-paper/ewp-specs-api-registry
[discovery-api]: https://github.com/erasmus-without-paper/ewp-specs-api-discovery
[echo]: https://github.com/erasmus-without-paper/ewp-specs-api-echo
[error-handling]: https://github.com/erasmus-without-paper/ewp-specs-architecture#error-handling
[institutions-api]: https://github.com/erasmus-without-paper/ewp-specs-api-institutions
