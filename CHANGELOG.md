Release notes
=============

This document describes all the changes made to the *Outgoing Mobilities API*
document, starting from its first beta draft version.


0.13.0
------

* New mobility status: `nomination-verified`. This new status partially
  replaces the previously used status `live`. The `live` status is still in
  use, but its meaning changes. In particular, when the nomination is being
  accepted, the status of the mobility should now change to
  `nomination-verified`, not to `live` (as it has been previously). See
  [this thread](https://github.com/erasmus-without-paper/ewp-specs-api-omobilities/issues/28)
  for discussion.


0.12.1
------

* The `modified_since` parameter of the `index` endpoint is now in the
  `xs:dateTime` format (not ISO 8601 format). See
  [this thread](https://github.com/erasmus-without-paper/general-issues/issues/27).


0.12.0
------

* `manifest-entry.xsd`: `<update-url>` and `<supported-update-types>` elements
  are now OPTIONAL (previously, they were required). This change was necessary
  because we allow implementers to not support *any* update types (and thus we
  shouldn't force them to have an update endpoint).

* Changes in `get` endpoint response:

  - `<planned-arrival-date>` and `<planned-departure-date>` are now OPTIONAL
    (previously, there were required).
  - New REQUIRED element: `<receiving-academic-year-id>`.

  These two changes are related. See discussion
  [here](https://github.com/erasmus-without-paper/ewp-specs-api-omobilities/issues/23).

* Changes in `index` endpoint parameters: `planned_arrival_after` parameter was
  dropped, and `receiving_academic_year_id` was introduced in its place. This
  change is related to the fact that `<planned-arrival-date>` element is no
  longer required. See discussion
  [here](https://github.com/erasmus-without-paper/ewp-specs-api-omobilities/issues/23).


0.11.0
------

Actual arrival and departure dates were moved to the separate API, as requested
[here](https://github.com/erasmus-without-paper/ewp-specs-api-mobilities/issues/27).
In context of *this* API, the following changes occurred:

 * Removed `<actual-arrival-date>` and `<actual-departure-date>` from the `get`
   endpoint's response.

 * Removed the `update-arrival-departure-dates-v1` update request type.


0.10.0
------

Performed a major refactoring caused by the planned introduction of the
Incoming Mobilities API (more information
[here](https://github.com/erasmus-without-paper/ewp-specs-api-mobilities/issues/27)).
Many XML namespaces and element names were changed:

 * All XML namespaces beginning with
   `https://github.com/erasmus-without-paper/ewp-specs-api-mobilities/`
   now begin with
   `https://github.com/erasmus-without-paper/ewp-specs-api-omobilities/`.

 * Elements renamed in the `get` endpoint response:

   - `mobilities-get-response` is now `omobilities-get-response`,
   - `mobility-id` is now `omobility-id`.

 * Elements renamed in the `index` endpoint response:

   - `mobilities-index-response` is now `omobilities-index-response`,
   - `mobility-id` is now `omobility-id`.

 * Elements renamed in the `update` endpoint's request:

   - `mobilities-update-request` is now `omobilities-update-request`,
   - `mobility-id` is now `omobility-id` (for each update type separately).

 * Elements renamed in the `update` endpoint's response:

   - `mobilities-update-response` is now `omobilities-update-response`.

 * Elements renamed in `manifest-entry.xsd`:

   - `mobilities` is now `omobilities`,
   - `max-mobility-ids` is now `max-omobility-ids`.

 * Parameters renamed in the `get` endpoint request:

   - `mobility_id` is now `omobility_id`,

 * We no longer encourage ignoring the `modified_since` parameter in the
   `index` endpoint. It is recommended to support this parameter.


0.9.0
-----

 * This API now requires implementers to upgrade their implementations to
   [Version 2](https://github.com/erasmus-without-paper/ewp-specs-sec-intro/tree/stable-v2)
   of the *Authentication and Security* document.

   In particular, this means that the clients MUST be aware of the fact, that
   the server is no longer required to support methods of authentication and
   encryption which it *was* required to support in the previous versions of
   this API. Clients SHOULD consult the newly introduced `<http-security>`
   element in the server's manifest entry before making their requests.


0.8.1
-----

* Explicitly declare that this version still requires the use of
  [Version 1](https://github.com/erasmus-without-paper/ewp-specs-sec-intro/tree/stable-v1)
  of the *Authentication and Security* document. You can find more information
  on the planned process of updating security requirements
  [here](https://github.com/erasmus-without-paper/ewp-specs-sec-intro/issues/1).


0.8.0
-----

* Changed XML namespaces (as part of
  [this issue](https://github.com/erasmus-without-paper/ewp-specs-api-iias/issues/22)).
  Since this version, this API is in the `stable-v1` XML namespace.

  This does not mean that Outgoing Mobilities API is stable. It can still
  change in backward-incompatible ways, until version `1.0.0` is released.

* Added a section about handling edit conflicts.


0.7.0
-----

* Backward-incompatible change in the `get` endpoint. The previously mendatory
  `<nominee-eqf-level>` element has been split into two separate elements,
  both of them optional:

  - `<eqf-level-studied-at-nomination>`
  - `<eqf-level-studied-at-departure>`

  See [this issue](https://github.com/erasmus-without-paper/ewp-specs-api-omobilities/issues/20)
  for more details about this change.

* Added optional nominee's photos to the `get` endpoint (see
  [this issue](https://github.com/erasmus-without-paper/ewp-specs-api-omobilities/issues/18)).

* Added a missing update type in `manifest-entry.xsd` (the schema didn't allow
  server implementers to support the `update-statuses-v1` update).

* Better explained the "optional nature" of `<los-id>` and and `<loi-id>`
  elements.


0.6.1
-----

* Minor fixes in XSD's "style".


0.6.0
-----

* Backward-incompatible change in the `update` endpoint. The `<mobility-id>`
  element  in the `update-request.xsd` file has been moved "downwards":
  Previously ever update request referred to exactly one mobility ID, now each
  type of update request may decide on the number of mobility IDs it refers to.
  (If you have already implemented update requests, then you simply need to
  move one element to a different place in the XML you send.)

* New parameter in the `index` endpoint: `modified_since`. It is RECOMMENDED
  for the servers to support this parameter, to avoid unnecessary network
  traffic.

* Certain mobility properties were marked as immutable. That is, we don't want
  these properties to change - if they do, then a new mobility ID should be
  created. Search for "immutable" in `endpoints/get-response.xsd` to read more
  about this.

* Bugfix: Allow for more than one mobility per `get` response
  ([link](https://github.com/erasmus-without-paper/ewp-specs-api-omobilities/issues/21)).

* Highlight the fact, the `xs:language` type is in BCP 47 format. This type
  is used in `nominee-language-skill/language` element.

* Fixed broken links.

* New update type in `update-request.xsd`: `<update-statuses-v1>`. This is for
  approving nominations
  ([link](https://github.com/erasmus-without-paper/ewp-specs-api-omobilities/issues/22)).


0.5.0
-----

* Added a way to attach approval-related properties to LA components snapshots:

  - `approval` element,
  - `should-now-be-approved-by` element,
  - `in-effect-since` attribute.

  Discuss
  [here](https://github.com/erasmus-without-paper/ewp-specs-api-omobilities/issues/16).

* Added `ewp-reason-code` to LA components changes
  ([details](https://github.com/erasmus-without-paper/ewp-specs-api-omobilities/issues/15)).

* Removed incorrect references to EUC PDF LA template Tables C and D from the
  XSD annotations
  ([details](https://github.com/erasmus-without-paper/ewp-specs-api-omobilities/issues/17)).


0.4.0
-----

* The `update` endpoint has been reintroduced.

  - Servers are REQUIRED to implement this endpoint (publish its URL).
  - Servers are also allowed to NOT support any of its update types (in which
    case, the server implementation basicly does nothing).
  - Of course, in the long run, servers are RECOMMENDED to support all types of
    updates.

  New version of this endpoint behaves completely different than the previous
  one did (the one in version 0.2.1). It has been combined from a couple of
  unreleased draft APIs
  (e.g. [this one](https://github.com/erasmus-without-paper/ewp-specs-api-nominations-approval)
  and [this one](https://github.com/erasmus-without-paper/ewp-specs-api-arrivals-departures)).


0.3.0
-----

Learning Agreement components were **completely redesigned**:

* Before this change, a single mobility held *only a single snapshot* of each
  of the two `<component-studied>` and `<component-recognized>` lists. This
  snapshot described the **latest draft** revision of these lists. If the
  client wanted to extract the **latest approved** (aka "after mobility")
  version, or the **first approved** (aka "before mobility") version, then
  the **only way** to do this was by parsing timeline entries.

  Now, each of these two component lists can have up to three snapshots -
  `<before-mobility-snapshot>`, `<latest-approved-snapshot>`, and
  `<latest-draft-snapshot>`.

* The `<timeline>` element was removed. The concept of revisions was replaced
  with somewhat similar concept of "changes between snapshots". Each of the
  snapshot element described above is preceded by a respective
  `<*-changes>` element, which describes the changes made **since a previous
  snapshot**.

  The format of the `<*-changes>` entries is a bit similar to the format
  previously used in `<timeline>`, but the concept is very much different.
  The primary difference is that, now, the sending HEI has **full control**
  over these changes. It is perfectly okay to **rewrite history**, because
  the changes *no longer represent* history. There are no commit dates, there
  are no authors. These new elements only represent differences between
  snapshots. We are no longer forcing implementers to keep fully change
  history in their models.

* As to **how** these snapshots and differences between them are generated,
  it depends on the underlying model used by the server:

  - Some implementers keep a complete timeline of all changes.
  - Others keep a couple of snapshots only.

  Both of these models are acceptable:

  - Snapshots can be dynamically generated from a timeline of changes.
  - And it is also possible to generate a list of changes by comparing
    two consecutive snapshots.

  We require all implementers to express their model in *both these formats*.
  It will make the data a bit more understandable for the clients, *and* it
  will help us detect inconsistencies in server implementations.

**Other changes:**

* The `update` endpoint has been removed. This API is now strictly "read only".

* Mobility IDs are no longer required to be universally unique - it is okay if
  they are unique within the sending institution. They also don't have to be
  in the UUID format anymore (reasoning
  [here](https://github.com/erasmus-without-paper/general-issues/issues/10#issuecomment-280302623)).

* It is now allowed to provide names of the student in multiple languages or
  scripts (same way as in Abstract Contact data type).

* Minor fixes in the texts (spelling, style, etc.).


0.2.1
-----

* Phone Number and Address data types are now in `stable-v1` namespace.

* `minOccurs` and `maxOccurs` are now provided explicitly
  ([why?](https://github.com/erasmus-without-paper/general-issues/issues/22)).

* Introduced limits on `ounit-id` and `iia-id` values
  ([more information](https://github.com/erasmus-without-paper/general-issues/issues/23)).


0.2.0
-----

* The `<component-code>` element has been renamed to `<los-code>`. Also, the
  types and example values of `<los-id>` and `<loi-id>` elements were changed.
  Both of these changes are an aftereffect of the ID/code change in the Courses
  API. See [this thread](https://github.com/erasmus-without-paper/ewp-specs-api-omobilities/issues/9).

* Renamed `nominee-field-isced-code` to `nominee-isced-f-code`
  ([why?](https://github.com/erasmus-without-paper/ewp-specs-api-omobilities/issues/8#issuecomment-270402114)).

* Added a new `planned_arrival_after` request parameter.

* Used surrogate IDs in examples
  ([why?](https://github.com/erasmus-without-paper/ewp-specs-api-omobilities/issues/9#issuecomment-271272493)).


0.1.1
-----

* Exposed `SequenceOfMobilities` element group (to be used in [`.ewpmobility`
  file](https://github.com/erasmus-without-paper/ewp-specs-fileext-ewpmobility).


0.1.0
-----

Initial release.
