# A YANG Data Model for requesting Path Computation

## Scope

This repository is to facilitate the work on the [IETF TEAS](https://datatracker.ietf.org/wg/teas/documents/) WG Internet-Draft, "[A YANG Data Model for requesting Path Computation](https://datatracker.ietf.org/doc/html/draft-ietf-teas-yang-path-computation/)".

Diff editor copy vs published version:
- [Side-by-side diff](https://www.ietf.org/rfcdiff?url1=draft-ietf-teas-yang-path-computation&url2=https://raw.githubusercontent.com/rvilalta/ietf-te-path-computation/wg-lc/draft-ietf-teas-yang-path-computation.txt)
- [Before-after diff](https://www.ietf.org/rfcdiff?difftype=--abdiff&url1=draft-ietf-teas-yang-path-computation&url2=https://raw.githubusercontent.com/rvilalta/ietf-te-path-computation/wg-lc/draft-ietf-teas-yang-path-computation.txt)
- [Changebars](https://www.ietf.org/rfcdiff?difftype=--chbars&url1=draft-ietf-teas-yang-path-computation&https://raw.githubusercontent.com/rvilalta/ietf-te-path-computation/wg-lc/draft-ietf-teas-yang-path-computation.txt)
- [Html wdiff](https://www.ietf.org/rfcdiff?difftype=--hwdiff&url1=draft-ietf-teas-yang-path-computation&url2=https://raw.githubusercontent.com/rvilalta/ietf-te-path-computation/wg-lc/draft-ietf-teas-yang-path-computation.txt)

## Abstract

   There are scenarios, typically in a hierarchical Software-Defined
   Networking (SDN) context, where the topology information provided by
   a Traffic Engineering (TE) network provider may be insufficient for
   its client to perform end-to-end path computation. In these cases the
   client would need to request the provider to calculate some (partial)
   feasible paths.

   This document defines a YANG data model for a Remote Procedure Call
   (RPC) to request path computation. This model complements the
   solution, defined in draft-ietf-teas-yang-te, to configure a TE tunnel path in
   "compute-only" mode.

   Moreover this document describes some use cases where a path
   computation request, via YANG-based protocols (e.g., NETCONF or
   RESTCONF), can be needed.

## Contributing

This repository relates to activities in the Internet Engineering Task Force
([IETF](https://www.ietf.org/)). All material in this repository is considered
Contributions to the IETF Standards Process, as defined in the intellectual
property policies of IETF currently designated as
[BCP 78](https://www.rfc-editor.org/info/bcp78),
[BCP 79](https://www.rfc-editor.org/info/bcp79) and the
[IETF Trust Legal Provisions (TLP) Relating to IETF Documents](http://trustee.ietf.org/trust-legal-provisions.html).

Any edit, commit, pull request, issue, comment or other change made to this
repository constitutes Contributions to the IETF Standards Process
(https://www.ietf.org/).

You agree to comply with all applicable IETF policies and procedures, including,
BCP 78, 79, the TLP, and the TLP rules regarding code components (e.g. being
subject to a Simplified BSD License) in Contributions.


### Other Resources

Discussion of this work occurs on the
[TEAS working group mailing list](https://mailarchive.ietf.org/arch/browse/teas/)
([subscribe](https://www.ietf.org/mailman/listinfo/teas)).  In addition to
contributions in GitHub, you are encouraged to participate in discussions there.

**Note**: Some working groups adopt a policy whereby substantive discussion of
technical issues needs to occur on the mailing list.

You might also like to familiarize yourself with other
[working group documents](https://datatracker.ietf.org/wg/teas/documents/).
