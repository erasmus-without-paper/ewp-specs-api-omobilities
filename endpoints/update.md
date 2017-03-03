Outgoing Mobilities Update endpoint
===================================

* [What is the status of this document?][statuses]
* [See the index of all other EWP Specifications][develhub]


Summary
-------

This endpoint allows the receiving HEI to suggest updates to specific Outgoing
Mobility fields, stored on the *sending* HEI's servers.


Updates or suggestions?
-----------------------

An "update request" can be looked at in two different ways. At first, we expect
for EWP partners to perceive it as a set suggestions:

 - **The client** will sometimes send them, sometimes not. Some IRO staff will
   still prefer to use "the old way", and send their data by emails.

 - **The server** will receive them, but it will treat them as suggestions
   only. It will respond something like *"Thank you! We will review your
   suggestion"*.

However, if adoption of EWP goes well, then the users and developers will begin
to get used to the new data flow, and the meaning of "update request" will
shift:

 - **The client** will send them often. Sending these "suggestions" will
   gradually become a **requirement** for the receiving institution. IRO staff
   members will prefer to use this API than sending an email.

 - **The server** might begin to put more trust in both EWP and the partner. It
   might consider saving updates *without* having a human review them. As this
   continues, the server will begin responding with *"Your update has been
   received and approved automatically"*.


Request method and parameters
-----------------------------

 * Requests MUST be made with HTTP POST method. Servers SHOULD reject all other
   request methods.

 * The body of the request MUST contain a raw XML element described in the
   [request.xsd](request.xsd) schema. The request SHOULD also contain a
   matching `Content-Type` header (such as `text/xml`).

   (This endpoint is **NOT** using the regular `application/x-www-form-urlencoded`
   request format which most of other EWP endpoints do.)

 * The are various types of update requests described in the [request.xsd]
   (request.xsd) schema. The server is NOT REQUIRED to support all of them.
   Clients MUST check if the server supports certain type of update request
   before sending it.

 * The server is REQUIRED to publish the list of supported update requests in
   its [manifest entry](../manifest-entry.xsd).


Permissions
-----------

All requests from the EWP Network MUST be allowed access to this API. Consult
the [Echo API][echo] specs for details on handling unprivileged requests.


Handling of invalid parameters
------------------------------

 * General [error handling rules][error-handling] apply.

 * If some of the required parameters (the ones with `minOccurs="1"`) are
   missing, or are passed in an invalid format, then the server MUST respond
   with HTTP 400 error response.

 * If the request contains unknown parameters (extra elements not allowed in
   the schema), then the server MUST ignore such parameters, and continue with
   processing the request.

 * If `<mobility-id>` doesn't exist, or the requester does not cover the
   receiving HEI of this mobility, then server MUST respond with HTTP 400 error
   response.

 * If `<sending-hei-id>` doesn't match `<mobility-id>`'s sending HEI, then the
   server MUST respond with HTTP 400 error response.

 * More requirements - in context of specific types of updates - are described
   in the [request.xsd](request.xsd) file.


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
