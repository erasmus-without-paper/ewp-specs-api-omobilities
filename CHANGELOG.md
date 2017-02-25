Release notes
=============

This document describes all the changes made to the *Outgoing Mobilities API*
document, starting from its first beta draft version.


master (unreleased)
-------------------

* Removed the whole Learning Agreement and `<timeline>` sections. They will be
  rewritten.

* The `update` endpoint has been removed. This API is now strictly "read only".

* Mobility IDs are no longer required to be universally unique - it is okay if
  they are unique within the sending institution. They also don't have to be
  in the UUID format anymore (reasoning [here]
  (https://github.com/erasmus-without-paper/general-issues/issues/10#issuecomment-280302623)).

* It is now allowed to provide names of the student in multiple languages or
  scripts (same way as in Abstract Contact data type).

* Minor fixes in the texts (spelling, style, etc.).


0.2.1
-----

* Phone Number and Address data types are now in `stable-v1` namespace.

* `minOccurs` and `maxOccurs` are now provided explicitly ([why?]
  (https://github.com/erasmus-without-paper/general-issues/issues/22)).

* Introduced limits on `ounit-id` and `iia-id` values ([more information]
  (https://github.com/erasmus-without-paper/general-issues/issues/23)).


0.2.0
-----

* The `<component-code>` element has been renamed to `<los-code>`. Also, the
  types and example values of `<los-id>` and `<loi-id>` elements were changed.
  Both of these changes are an aftereffect of the ID/code change in the Courses
  API. See [this thread]
  (https://github.com/erasmus-without-paper/ewp-specs-api-mobilities/issues/9).

* Renamed `nominee-field-isced-code` to `nominee-isced-f-code` ([why?]
  (https://github.com/erasmus-without-paper/ewp-specs-api-mobilities/issues/8#issuecomment-270402114)).

* Added a new `planned_arrival_after` request parameter.

* Used surrogate IDs in examples ([why?]
  (https://github.com/erasmus-without-paper/ewp-specs-api-mobilities/issues/9#issuecomment-271272493)).


0.1.1
-----

* Exposed `SequenceOfMobilities` element group (to be used in [`.ewpmobility`
  file](https://github.com/erasmus-without-paper/ewp-specs-fileext-ewpmobility).


0.1.0
-----

Initial release.
