OBSOLETE - Mobility Signing API
===============================

* [What is the status of this document?][statuses]
* [See the index of all other EWP Specifications][develhub]


Summary
-------

**OBSOLETE: This proposal has been merged with [Outgoing Mobilities
API][mobilities-api].**

This document describes the **Mobility Signing API**. This API can be
implemented by a sending HEI if it wants to **allow** receiving HEIs to approve
and/or sign the latest Learning Agreement version.

This API serves a couple of purposes:

 * It allows the receiving HEI to **approve** the `<latest-draft-snapshot>`
   version of the mobility's components. (So that it becomes the
   `<latest-approved-snapshot>`.)

 * It allows both HEIs to store an electronically signed proof that both HEIs
   have approved the mobility and/or the given state of the Learning Agreement.

   **Important:** Currently, **this latter claim is not true**, because neither
   request nor response contains non-repudiable electronic signatures. We
   would like to add them before this specification is released, but we're not
   yet sure:

   - how exactly we want them to work (it depends on the resolution of [this issue]
     (https://github.com/erasmus-without-paper/ewp-specs-architecture/issues/17)),
   - whether they are actually needed (it depends on the resolution of [this
     issue]
     (https://github.com/erasmus-without-paper/ewp-specs-api-mobilities/issues/5#issuecomment-268913671)).

   <!--
   WRTODO: Remember to clean this up before it is released.
   -->


Request method and parameters
-----------------------------

 * Requests MUST be made with HTTP POST method. Servers SHOULD reject all other
   request methods.

 * The body of the request MUST contain a raw XML element described in the
   [request.xsd](request.xsd) schema. The request SHOULD also contain a
   matching `Content-Type` header (such as `text/xml`).

   (This API is **NOT** using the regular `application/x-www-form-urlencoded`
   request format which most of other EWP APIs do.)


Permissions
-----------

All requests from the EWP Network MUST be allowed access to this API. Consult
the [Echo API][echo] specs for details on handling unprivileged requests.


Handling of invalid parameters
------------------------------

 * General [error handling rules][error-handling] apply.

 * If the mobility object included the request XML doesn't match the current
   state of the same mobility as kept in the server's database, then the server
   MUST respond with [HTTP 409][http409] error response.

   - The response SHOULD include the `<user-message>` element with a brief
     explanation (e.g. _Your copy of the mobility is not up-to-date and cannot
     be signed. Please refresh your copy from our servers first._).

   - Mobility element supplied by the client MAY use different XML namespace
     prefixes than the one the server uses in its Outgoing Mobilities API.
     These prefixes MUST NOT be taken into account when the documents are being
     compared.

   - If the output of the server's Outgoing Mobilities API contains some extra
     dynamic content which varies heavily from request to request (e.g.
     a current timestamp), but its changes don't constitute a change in the in
     a legal manner, then such content MUST NOT be taken into account when the
     documents are being compared.

 * If the requester does not cover the receiving HEI of the mobility described
   in his XML, then the server MUST respond with HTTP 400 error response.

 * If `<receiving-hei-request-date>` differs from `<sending-hei-response-date>`
   by more than 2 hours, then the server SHOULD respond with a HTTP 400 error
   response. Both clients and servers MUST keep their clocks updated. The
   accepted margin MAY change in future versions of this API.

<!--
WRTODO: Add signature requirements. (What if request is improperly signed, etc.)
-->


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
[http409]: https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.4.10
[mobilities-api]: https://github.com/erasmus-without-paper/ewp-specs-api-mobilities
