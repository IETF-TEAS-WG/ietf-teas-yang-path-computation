---
coding: utf-8

title: A YANG Data Model for requesting path computation

abbrev: Yang for Path Computation
docname: draft-ietf-teas-yang-path-computation-20
submissiontype: IETF
workgroup: TEAS Working Group
category: std
ipr: trust200902

stand_alone: yes
pi: [toc, sortrefs, symrefs, comments]

author:
  -
    name: Italo Busi
    role: editor
    org: Huawei Technologies
    email: italo.busi@huawei.com
  -
    name: Sergio Belotti
    role: editor
    org: Nokia
    email: sergio.belotti@nokia.com
  -
    name: Oscar Gonzalez de Dios
    ins: O. Gonzalez de Dios
    org: Telefonica
    email: oscar.gonzalezdedios@telefonica.com
  -
    name: Anurag Sharma
    org: Google
    email: ansha@google.com
  -
    name: Yan Shi
    org: China Unicom
    email: shiyan49@chinaunicom.cn
  -
    name: Daniele Ceccarelli
    org: Cisco
    email: daniele.ietf@gmail.com

contributor:
  -
    name: Victor Lopez
    org: Nokia
    email: victor.lopez@nokia.com
  -
    name: Ricard Vilalta
    org: CTTC
    email: ricard.vilalta@cttc.es
  -
    name: Karthik Sethuraman
    org: NEC
    email: karthik.sethuraman@necam.com
  -
    name: Michael Scharf
    org: Nokia
    email: michael.scharf@gmail.com
  -
    name: Dieter Beller
    org: Nokia
    email: dieter.beller@nokia.com
  -
    name: Gianmarco Bruno
    org: Ericsson
    email: gianmarco.bruno@ericsson.com
  -
    name: Francesco Lazzeri
    org: Ericsson
    email: francesco.lazzeri@ericsson.com
  -
    name: Young Lee
    org: Samsung Electronics
    email: younglee.tx@gmail.com
  -
    name: Carlo Perocchio
    org: Ericsson
    email: carlo.perocchio@ericsson.com
  -
    name: Olivier Dugeon
    org: Orange Labs
    email: olivier.dugeon@orange.com
  -
    name: Julien Meuric
    org: Orange Labs
    email: julien.meuric@orange.com

--- abstract

   There are scenarios, typically in a hierarchical Software-Defined
   Networking (SDN) context, where the topology information provided by
   a Traffic Engineering (TE) network provider may be insufficient for
   its client to perform multi-domain path computation. In these cases the
   client would need to request the TE network provider to compute some
   intra-domain paths to be used by the client to choose the optimal multi-domain paths.

This document provides a mechanism to request path computation by augmenting the Remote Procedure Calls (RPCs) defined in RFC YYYY.

   \[RFC EDITOR NOTE: Please replace RFC YYYY with the RFC number of
   draft-ietf-teas-yang-te once it has been published.

   Moreover, this document describes some use cases where the path
   computation request, via YANG-based protocols (e.g., NETCONF or
   RESTCONF), can be needed.

--- middle

{: #intro}

# Introduction

{: #pc-model}

   There are scenarios, typically in a hierarchical Software-Defined
   Networking (SDN) context, where the topology information provided by
   a Traffic Engineering (TE) network provider may be insufficient for
   its client to perform multi-domain path computation. In these cases the
   client would need to request the TE network provider to compute some
   intra-domain paths that could be used together with its topology information
   to compute the multi-domain path.

   These types of scenarios can be applied to different interfaces in
   different reference architectures:

-  Application-Based Network Operations (ABNO) control interface
{{?RFC7491}}, in which an Application Service Coordinator can request the
ABNO controller to take in charge path calculation (see Figure 1
in {{?RFC7491}}).

-  Abstraction and Control of TE Networks (ACTN) {{?RFC8453}}, where a
controller hierarchy is defined.
In the ACTN context, path computation is needed on the interface
between Customer Network Controller (CNC)  and Multi-Domain
Service Coordinator (MDSC), called CNC-MDSC Interface (CMI),
and on the interface between MSDC and Provisioning Network
Controller (PNC), called MDSC-PNC Interface  (MPI). 
{{?RFC8454}} describes an information model for the Path
Computation request.

   Multiple protocol solutions can be used for communication between
   different controller hierarchical levels. This document assumes that
   the controllers are communicating using YANG-based protocols (e.g.,
   NETCONF {{!RFC6241}} or RESTCONF {{!RFC8040}}).

   Path Computation Elements (PCEs), controllers and orchestrators
   perform their operations based on Traffic Engineering Databases
   (TED). Such TEDs can be described, in a technology agnostic way, with
   the YANG data model for TE Topologies {{!RFC8795}}. Furthermore, the
   technology specific details of the TED are modelled in the technology
   specific topology models, e.g., the {{?I-D.ietf-ccamp-otn-topo-yang}} for Optical Transport
   Network (OTN) Optical Data Unit (ODU) technologies, which augment the
   common TE topology model in {{!RFC8795}}.

   The availability of such topology models allows the provisioning of
   the TED using YANG-based protocols (e.g., NETCONF or RESTCONF).
   Furthermore, it enables a PCE/controller performing the necessary
   abstractions or modifications and offering this customized topology
   to another PCE/controller or high level orchestrator.

   The tunnels that can be provided over the networks described with the
   topology models can be also set-up, deleted and modified via YANG-
   based protocols (e.g., NETCONF or RESTCONF) using the TE tunnel YANG
   data model {{!I-D.ietf-teas-yang-te}}.

   This document defines a YANG data model {{!RFC7950}} that augments the RPC defined in {{!I-D.ietf-teas-yang-te}}. The use of this RPC is complimentary to the configuration of a TE tunnel path in "compute-only" mode, as described in {{!I-D.ietf-teas-yang-te}}.

   The YANG data model definition does not make any assumption about
   whether that the client or the server implement a "PCE"
   functionality, as defined in {{?RFC4655}}, and the Path Computation
   Element Communication Protocol (PCEP) protocol, as defined in
   {{!RFC5440}}.

   Moreover, this document describes some use cases where a path
   computation request, via YANG-based protocols (e.g., NETCONF or
   RESTCONF), can be needed.

   The YANG data model defined in this document conforms to the Network
   Management Datastore Architecture {{?RFC8342}}.

## Terminology

   TED:
   
   > The traffic engineering database is a collection of all TE
   information about all TE nodes and TE links in a given network.

   PCE:
   
   > A Path Computation Element (PCE) is an entity that is capable of
   computing a network path or route based on a network graph, and of
   applying computational constraints during the computation.  The PCE
   entity is an application that can be located within a network node or
   component, on an out-of-network server, etc.  For example, a PCE
   would be able to compute the path of a TE Label Switched Path (LSP)
   by operating on the TED and considering bandwidth and other
   constraints applicable to the TE LSP service request. {{?RFC4655}}.

   Domain:
   
   > TE information is the data relating to nodes and TE links
   that is used in the process of selecting a TE path.  TE information
   is usually only available within a network.  We call such a zone of
   visibility of TE information a domain.  An example of a domain may be
   an IGP area or an Autonomous System. {{!RFC7926}}

   The terminology for describing YANG data models is found in
   {{!RFC7950}}.

## Tree Diagram

   Tree diagrams used in this document follow the notation defined in
   {{!RFC8340}}.

## Prefixes in Data Node Names

   In this document, names of data nodes and other data model objects
   are prefixed using the standard prefix associated with the
   corresponding YANG imported modules, as shown in {{tab-prefix}}.

| Prefix        | YANG module              | Reference    |
|---------------|--------------------------|--------------|
| te-types      | ietf-te-types            | \[RFCZZZZ]   |
| te            | ietf-te                  | \[RFCYYYY]   |
| te-pc         | ietf-te-path-computation | RFCXXXX      |
{: #tab-prefix title="Prefixes and corresponding YANG modules"}

RFC Editor Note:
Please replace XXXX with the RFC number assigned to this document.
Please replace YYYY with the RFC number of {{!I-D.ietf-teas-yang-te}} once it has been published.
Please replace ZZZZ with the RFC number of {{!I-D.ietf-teas-rfc8776-update}} once it has been published.
Please remove this note.

{: #use-cases}

# Use Cases

   This section presents some use cases, where a client needs to request
   underlying SDN controllers for path computation.

   The use of the YANG data model defined in this document is not
   restricted to these use cases but can be used in any other use case
   when deemed useful.

   The presented uses cases have been grouped, depending on the
   different underlying topologies: Packet/Optical Integration ({{poi-uc}});
   multi-domain Traffic Engineered (TE) Networks ({{md-uc}}); and Data Center
   Interconnections ({{dci-uc}}). Use cases in {{brpc-uc}} and {{hpce-uc}}
   respectively present how to
   apply this YANG data model for standard multi-domain PCE i.e.
   Backward Recursive Path Computation {{!RFC5441}} and Hierarchical PCE
   {{?RFC6805}}.

{: #poi-uc}

## Packet/Optical Integration

   In this use case, an optical domain is used to provide connectivity
   to some nodes of a packet domain (see {{fig-poi-uc}}).

~~~~ ascii-art
                                +----------------+
                                |                |
                                | Packet/Optical |
                                |  Coordinator   |
                                |                |
                                +---+------+-----+
                                    |      |
                       +------------+      |
                       |                   +-----------+
                +------V-----+                         |
                |            |                  +------V-----+
                | Packet     |                  |            |
                | Domain     |                  | Optical    |
                | Controller |                  | Domain     |
                |            |                  | Controller |
                +------+-----+                  +-------+----+
                       |                                |
              .........V.........................       |
              :          packet domain          :       |
          +----+                               +----+   |
          | R1 |= = = = = = = = = = = = = = = =| R2 |   |
          +-+--+                               +--+-+   |
            | :                                 : |     |
            | :................................ : |     |
            |                                     |     |
            |               +-----+               |     |
            |    ...........| Opt |...........    |     |
            |    :          |  C  |          :    |     |
            |    :         /+--+--+\         :    |     |
            |    :        /    |    \        :    |     |
            |    :       /     |     \       :    |     |
            |   +-----+ /   +--+--+   \ +-----+   |     |
            |   | Opt |/    | Opt |    \| Opt |   |     |
            +---|  A  |     |  D  |     |  B  |---+     |
                +-----+\    +--+--+    /+-----+         |
                 :      \      |      /      :          |
                 :       \     |     /       :          |
                 :        \ +--+--+  / optical<---------+
                 :         \| Opt |/  domain :
                 :..........|  E  |..........:
                            +-----+
~~~~
{: #fig-poi-uc title="Packet/Optical Integration use case"
artwork-name="poi-use-case.txt"}

   {{fig-poi-uc}} as well as {{fig-poi-abstraction}} describe two different
   examples of packet/optical topologies and only show a partial view of the
   packet network connectivity, before additional packet connectivity is
   provided by the optical network.

   It is assumed that the Optical Domain Controller provides to the
   Packet/Optical Coordinator an abstracted view of the optical network.
   A possible abstraction could be to represent the whole optical
   network as one "virtual node" with "virtual ports" connected to the
   access links, as shown in {{fig-poi-abstraction}}.

   It is also assumed that Packet Domain Controller can provide the
   Packet/Optical Coordinator the information it needs to set up
   connectivity between packet nodes through the optical network (e.g.,
   the access links).

   The path computation request helps the Packet/Optical Coordinator to
   know the real connections that can be provided by the optical
   network.

~~~~ ascii-art
                       ,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,.
                      ,  Packet/Optical Coordinator view          ,
                     ,                              +----+       , .
                    ,                               |    |      ,
                   ,                                | R2 |     ,   .
                  ,  +----+  +------------ +       /+----+    ,
                 ,   |    |  |             |/-----/ / /      ,     .
                ,    | R1 |--O VP1     VP4 O       / /      ,
               ,     |    |\ |             | /----/ /      ,       .
              ,      +----+ \|             |/      /      ,
             ,        /      O VP2     VP5 O      /      ,         .
            ,        /       |             |  +----+    ,
           ,        /        |             |  |    |   ,           .
          ,        /         O VP3     VP6 O--| R4 |  ,
         ,     +----+ /-----/|_____________|  +----+ ,             .
        ,      |    |/       +------------ +        ,
       ,       | R3 |                              ,               .
      ,        +----+                             ,,,,,,,,,,,,,,,,,
     ,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,                ,.
     . Packet Domain Controller view                +----+       ,
       only packet nodes and packet links           |    |      ,  .
     . with access links to the optical network     | R2 |     ,
                  ,  +----+                        /+----+    ,    .
     .           ,   |    |                 /-----/ / /      ,
                ,    | R1 |---                     / /      ,      .
     .         ,     +----+\                 /----/ /      ,
              ,        /    \               /      /      ,        .
     .       ,        /                           /      ,
            ,        /                        +----+    ,          .
     .     ,        /                         |    |   ,
          ,        /                       ---| R4 |  ,            .
     .   ,     +----+ /-----/                 +----+ ,
        ,      |    |/                              ,              .
     . ,       | R3 |                              ,
      ,        +----+                             ,,,,,,,,,,,,,,,,,.
     .,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,                ,
       Optical Domain Controller view                            , .
     . only optical nodes,        +--+                          ,
       optical links and         /|OF|                         ,   .
     . access links from the  +--++--+             /          ,
       packet network         |OA|    \     /-----/ /        ,     .
     .          ,          ---+--+--\  +--+/       /        ,
               ,           \   |  \  \-|OE|-------/        ,       .
     .        ,             \  |   \ /-+--+               ,
             ,               \+--+  X    |               ,         .
     .      ,                 |OB|-/ \   |              ,
           ,                  +--+-\  \+--+            ,           .
     .    ,                  /   \  \--|OD|---        ,
         ,            /-----/     +--+ +--+          ,             .
     .  ,            /            |OC|/             ,
       ,                          +--+             ,               .
     .,                                           ,,,,,,,,,,,,,,,,,,
      ,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,                ,
     . Actual Physical View                         +----+       ,
                    ,             +--+              |    |      ,
     .             ,             /|OF|              | R2 |     ,
                  ,  +----+   +--++--+             /+----+    ,
     .           ,   |    |   |OA|    \     /-----/ / /      ,
                ,    | R1 |---+--+--\  +--+/       / /      ,
     .         ,     +----+\   |  \  \-|OE|-------/ /      ,
              ,        /    \  |   \ /-+--+        /      ,
     .       ,        /      \+--+  X    |        /      ,
            ,        /        |OB|-/ \   |    +----+    ,
     .     ,        /         +--+-\  \+--+   |    |   ,
          ,        /         /   \  \--|OD|---| R4 |  ,
     .   ,     +----+ /-----/     +--+ +--+   +----+ ,
        ,      |    |/            |OC|/             ,
     . ,       | R3 |             +--+             ,
      ,        +----+                             ,
     .,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,
~~~~
{: #fig-poi-abstraction title="Packet and Optical Topology Abstractions"
artwork-name="poi-topology-abstraction.txt"}

   In this use case, the Packet/Optical Coordinator needs to set up an
   optimal underlying path for an IP link between R1 and R2.

   As depicted in {{fig-poi-abstraction}}, the Packet/Optical Coordinator has only an
   "abstracted view" of the physical network, and it does not know the
   feasibility or the cost of the possible optical paths (e.g., VP1-VP4
   and VP2-VP5), which depend on the current status of the physical
   resources within the optical network and on vendor-specific optical
   attributes.

   The Packet/Optical Coordinator can request the underlying Optical
   Domain Controller to compute a set of potential optimal paths, taking
   into account optical constraints. Then, based on its own constraints,
   policy and knowledge (e.g. cost of the access links), it can choose
   which one of these potential paths to use to set up the optimal end-
   to-end path crossing optical network.

~~~~ ascii-art
                    ............................
                    :                          :
                    O VP1                  VP4 O
           cost=10 /:\                        /:\ cost=10
                  / : \----------------------/ : \
          +----+ /  :         cost=50          :  \ +----+
          |    |/   :                          :   \|    |
          | R1 |    :                          :    | R2 |
          |    |\   :                          :   /|    |
          +----+ \  :  /--------------------\  :  / +----+
                  \ : /       cost=55        \ : /
            cost=5 \:/                        \:/ cost=5
                    O VP2                  VP5 O
                    :                          :
                    :..........................:
~~~~
{: #fig-poi-example title="Packet/Optical Integration path computation example" artwork-name="poi-example.txt"}

   For example, in {{fig-poi-example}}, the Packet/Optical Coordinator can request
   the Optical Domain Controller to compute the paths between VP1-VP4
   and VP2-VP5 and then decide to set up the optimal end-to-end path
   using the VP2-VP5 optical path even if this is not the optimal path
   from the optical domain perspective.

   Considering the dynamicity of the connectivity constraints of an
   optical domain, it is possible that a path computed by the Optical
   Domain Controller when requested by the Packet/Optical Coordinator is
   no longer valid/available when the Packet/Optical Coordinator
   requests it to be set up. This is further discussed in {{rpc-motivation}}.

{: #md-uc}

## Multi-domain TE networks

   In this use case there are two TE domains which are interconnected
   together by multiple inter-domains links.

   A possible example could be a multi-domain optical network.

~~~~ ascii-art
                            +--------------+
                            | Multi-Domain |
                            | Controller   |
                            +---+------+---+
                                |      |
                   +------------+      |
                   |                   +-----------+
            +------V-----+                         |
            |            |                         |
            | TE Domain  |                  +------V-----+
            | Controller |                  |            |
            |      1     |                  | TE Domain  |
            +------+-----+                  | Controller |
                   |                        |      2     |
                   |                        +------+-----+
          .........V..........                     |
          :                  :                     |
         +-----+             :                     |
         |     |             :            .........V..........
         |  X  |             :            :                  :
         |     |          +-----+      +-----+                :
         +-----+          |     |      |     |               :
          :               |  C  |------|  E  |               :
      +-----+    +-----+ /|     |      |     |\ +-----+    +-----+
      |     |    |     |/ +-----+      +-----+ \|     |    |     |
      |  A  |----|  B  |     :            :     |  G  |----|  H  |
      |     |    |     |\    :            :    /|     |    |     |
      +-----+    +-----+ \+-----+      +-----+/ +-----+    +-----+
          :               |     |      |     |               :
          :               |  D  |------|  F  |               :
          :               |     |      |     |          +-----+
          :               +-----+      +-----+          |     |
          :                  :            :             |  Y  |
          :                  :            :             |     |
          :   TE domain 1    :            : TE domain 2 +-----+
          :..................:            :.................:
~~~~
{: #md-ml-connection title="Multi-domain multi-link interconnection"
artwork-name="multi-domain-use-case.txt"}

   In order to set up an end-to-end multi-domain TE path (e.g., between
   nodes A and H), the Multi-Domain Controller needs to know the
   feasibility or the cost of the possible TE paths within the two TE
   domains, which depend on the current status of the physical resources
   within each TE domain. This is more challenging in case of optical
   networks because the optimal paths depend also on vendor-specific
   optical attributes (which may be different in the two domains if they
   are provided by different vendors).

   In order to set up a multi-domain TE path (e.g., between nodes A and
   H), the Multi-Domain Controller can request the TE Domain Controllers
   to compute a set of intra-domain optimal paths and take decisions
   based on the information received. For example:

-  The Multi-Domain Controller asks TE Domain Controllers to provide
set of paths between A-C, A-D, E-H and F-H

-  TE Domain Controllers return a set of feasible paths with the
associated costs: the path A-C is not part of this set (in optical
networks, it is typical to have some paths not being feasible due
to optical constraints that are known only by the Optical Domain
Controller)

-  The Multi-Domain Controller will select the path A-D-F-H since it
is the only feasible multi-domain path and then request the TE
Domain Controllers to set up the A-D and F-H intra-domain paths

-  If there are multiple feasible paths, the Multi-Domain Controller
can select the optimal path knowing the cost of the intra-domain
paths (provided by the TE domain controllers) and the cost of the
inter-domain links (known by the Multi-Domain Controller)

   This approach may have some scalability issues when the number of TE
   domains is quite big (e.g. 20).

   In this case, it would be worthwhile using the abstract TE topology
   information provided by the TE Domain Controllers to limit the number
   of potential optimal end-to-end paths and then request path
   computation from fewer TE Domain Controllers in order to decide what
   the optimal path within this limited set is.

   For more details, see {{topo-pc-complement}}.

{: #dci-uc}

## Data Center Interconnections

   In these use case, there is a TE domain which is used to provide
   connectivity between Data Centers which are connected with the TE
   domain using access links.

~~~~ ascii-art
                        +--------------+
                        | Cloud Network|
                        | Orchestrator |
                        +--------------+
                          |  |  |  |
            +-------------+  |  |  +------------------------+
            |                |  +------------------+        |
            |       +--------V---+                 |        |
            |       |            |                 |        |
            |       | TE Network |                 |        |
     +------V-----+ | Controller |          +------V-----+  |
     | DC         | +------------+          | DC         |  |
     | Controller |     |                   | Controller |  |
     +------------+     |   +-----+         +------------+  |
          |         ....V...|     |........         |       |
          |         :       |  P  |       :         |       |
     .....V.....    :      /+-----+\      :    .....V.....  |
     :         :  +-----+ /    |    \ +-----+  :         :  |
     :  DC1 || :  |     |/     |     \|     |  :  DC2 || :  |
     :    ||||----| PE1 |      |      | PE2 |----   |||| :  |
     : _|||||| :  |     |\     |     /|     |  : _|||||| :  |
     :         :  +-----+ \ +-----+ / +-----+  :         :  |
     :.........:    :      \|     |/      :    :.........:  |
                    :.......| PE3 |.......:                 |
                            |     |                         |
                            +-----+               +---------V--+
                          .....|.....             | DC         |
                          :         :             | Controller |
                          :  DC3 || :             +------------+
                          :    |||| :                  |
                          : _|||||| <------------------+
                          :         :
                          :.........:
~~~~
{: #fig-dci-uc title="Data Center Interconnection use case"
artwork-name="dci-use-case.txt"}

   In this use case, there is the need to transfer data from Data Center
   1 (DC1) to either DC2 or DC3 (e.g. workload migration).

   The optimal decision depends both on the cost of the TE path (DC1-DC2
   or DC1-DC3) and of the Data Center resources within DC2 or DC3.

   The Cloud Network Orchestrator needs to make a decision for optimal
   connection based on TE network constraints and Data Center resources.

   It may not be able to make this decision because it has only an
   abstract view of the TE network (as in {{poi-uc}}).

   The Cloud Network Orchestrator can request to the TE Network
   Controller to compute the cost of the possible TE paths (e.g., DC1-
   DC2 and DC1-DC3) and to the DC Controller to provide the information
   it needs about the required Data Center resources within DC2 and DC3
   and then it can take the decision about the optimal solution based on
   this information and its policy.

{: #brpc-uc}

## Backward Recursive Path Computation scenario

   {{!RFC5441}} has defined the Virtual Source Path Tree (VSPT) flag within the RP (Request Parameters) object in order to compute inter-domain paths following a
   "Backward Recursive Path Computation" (BRPC) method. The main
   principle is to forward the PCReq message up to the destination
   domain. Then, each PCE involved in the computation will compute its
   part of the path and send it back to the requester through PCE
   Response message. The resulting computation is spread from
   destination PCE to source PCE. Each PCE is in charge of merging the
   path it received with the one it calculated. At the end, the source
   PCE merges its local part of the path with the received one to
   achieve the end-to-end path.

   {{fig-brpc-example}} below show a typical BRPC scenario where 3 PCEs cooperate to
   compute inter-domain paths.

~~~~ ascii-art
                   +----------------+          +----------------+
                   |  Domain (B)    |          |  Domain (C)    |
                   |                |          |                |
                   |        /-------|---PCEP---|--------\       |
                   |       /        |          |         \      |
                   |   (PCE)        |          |   -    (PCE)   |
                   |    /           <---------->  |D|           |
                   |   /            |  Inter   |   -            |
                   +---|----^-------+  Domain  +----------------+
                       |    |          Link
                     PCEP   |
                       |    | Inter-domain Link
                       |    |
                   +---|----v-------+
                   |   |            |
                   |   | Domain (A) |
                   |   \            |
                   |  (PCE)    -    |
                   |          |S|   |
                   |           -    |
                   +----------------+
~~~~
{: #fig-brpc-example title="BRPC Scenario" artwork-name="brpc-scenario.txt"}

   In this use case, a client can use the YANG data model defined in
   this document to request path computation from the PCE that controls
   the source of the tunnel. For example, a client can request to the
   PCE of domain A to compute a path from a source S, within domain A,
   to a destination D, within domain C. Then PCE of domain A will use
   PCEP protocol, as per {{!RFC5441}}, to compute the path from S to D and
   in turn gives the final answer to the requester.

{: #hpce-uc}

## Hierarchical PCE scenario

   {{?RFC6805}} has defined an architecture and extensions to the PCE
   standard to compute inter-domain path following a hierarchical
   method. Two new roles have been defined: parent PCE and child PCE.
   The parent PCE is in charge to coordinate the end-to-end path
   computation. For that purpose it sends to each child PCE involved in
   the multi-domain path computation a PCE Request message to obtain the
   local part of the path. Once received all answer through PCE Response
   message, the parent PCE will merge the different local parts of the
   path to achieve the end-to-end path.

   {{fig-hpce-example}} below shows a typical hierarchical scenario where a parent
   PCE request end-to-end path to the different child PCE. Note that a
   PCE could take independently the role of child or parent PCE
   depending of which PCE will request the path.

~~~~ ascii-art
    -----------------------------------------------------------------
    |   Domain 5                                                      |
    |                              -----                              |
    |                             |PCE 5|                             |
    |                              -----                              |
    |                                                                 |
    |    ----------------     ----------------     ----------------   |
    |   | Domain 1       |   | Domain 2       |   | Domain 3       |  |
    |   |                |   |                |   |                |  |
    |   |        -----   |   |        -----   |   |        -----   |  |
    |   |       |PCE 1|  |   |       |PCE 2|  |   |       |PCE 3|  |  |
    |   |        -----   |   |        -----   |   |        -----   |  |
    |   |                |   |                |   |                |  |
    |   |            ----|   |----        ----|   |----            |  |
    |   |           |BN11+---+BN21|      |BN23+---+BN31|           |  |
    |   |   -        ----|   |----        ----|   |----        -   |  |
    |   |  |S|           |   |                |   |           |D|  |  |
    |   |   -        ----|   |----        ----|   |----        -   |  |
    |   |           |BN12+---+BN22|      |BN24+---+BN32|           |  |
    |   |            ----|   |----        ----|   |----            |  |
    |   |                |   |                |   |                |  |
    |   |         ----   |   |                |   |   ----         |  |
    |   |        |BN13|  |   |                |   |  |BN33|        |  |
    |    -----------+----     ----------------     ----+-----------   |
    |                \                                /               |
    |                 \       ----------------       /                |
    |                  \     |                |     /                 |
    |                   \    |----        ----|    /                  |
    |                    ----+BN41|      |BN42+----                   |
    |                        |----        ----|                       |
    |                        |                |                       |
    |                        |        -----   |                       |
    |                        |       |PCE 4|  |                       |
    |                        |        -----   |                       |
    |                        |                |                       |
    |                        | Domain 4       |                       |
    |                         ----------------                        |
    |                                                                 |
     -----------------------------------------------------------------
~~~~
{: #fig-hpce-example title="Hierarchical domain topology from RFC6805"
artwork-name="hierarchical-domain-topology.txt"}

   In this use case, a client can use the YANG data model defined in
   this document to request to the parent PCE a path from a source S to
   a destination D. The parent PCE will in turn contact the child PCEs
   through PCEP protocol to compute the end-to-end path and then return
   the computed path to the client, using the YANG data model defined in
   this document. For example the YANG data model can be used to request
   to PCE5 acting as parent PCE to compute a path from source S, within
   domain 1, to destination D, within domain 3. PCE5 will contact child
   PCEs of domain 1, 2 and 3 to obtain local part of the end-to-end path
   through the PCEP protocol. Once received the PCRep message, it
   merges the answers to compute the end-to-end path and send it back to
   the client.

# Motivations

   This section provides the motivation for the YANG data model defined
   in this document.

   {{yang-motivation}} describes the motivation for a YANG data model to request
   path computation.

   {{topo-interaction}} describes the motivation for a YANG data model which
   complements the TE topology YANG data model defined in {{!RFC8795}}.

   {{rpc-motivation}} describes the motivation for a YANG RPC which complements
   the TE tunnel YANG data model defined in {{!I-D.ietf-teas-yang-te}}.

{: #yang-motivation}

## Motivation for a YANG Model

### Benefits of common data models

   The YANG data model for requesting path computation is closely
   aligned with the YANG data models that provide (abstract) TE topology
   information, i.e., {{!RFC8795}} as well as that are used to configure
   and manage TE tunnels, i.e., {{!I-D.ietf-teas-yang-te}}.

   There are many benefits in aligning the data model used for path
   computation requests with the YANG data models used for TE topology
   information and for TE tunnels configuration and management:

-  There is no need for an error-prone mapping or correlation of
information.

-  It is possible to use the same endpoint identifiers in path
computation requests and in the topology modeling.

- The attributes used for path computation constraints are the same
as those used when setting up a TE tunnel.

### Benefits of a single interface

   The system integration effort is typically lower if a single,
   consistent interface is used by controllers, i.e., one data modeling
   language (i.e., YANG) and a common protocol (e.g., NETCONF or
   RESTCONF).

   Practical benefits of using a single, consistent interface include:

1. Simple authentication and authorization: The interface between
different components has to be secured. If different protocols
have different security mechanisms, ensuring a common access
control model may result in overhead. For instance, there may be a
need to deal with different security mechanisms, e.g., different
credentials or keys. This can result in increased integration
effort.

2. Consistency: Keeping data consistent over multiple different
interfaces or protocols is not trivial. For instance, the sequence
of actions can matter in certain use cases, or transaction
semantics could be desired. While ensuring consistency within one
protocol can already be challenging, it is typically cumbersome to
achieve that across different protocols.

3. Testing: System integration requires comprehensive testing,
including corner cases. The more different technologies are
involved, the more difficult it is to run comprehensive test cases
and ensure proper integration.

4. Middle-box friendliness: Provider and consumer of path computation
requests may be located in different networks, and middle-boxes
such as firewalls, NATs, or load balancers may be deployed. In
such environments it is simpler to deploy a single protocol. Also,
it may be easier to debug connectivity problems.

5. Tooling reuse: Implementers may want to implement path computation
requests with tools and libraries that already exist in
controllers and/or orchestrators, e.g., leveraging the rapidly
growing eco-system for YANG tooling.

### Extensibility

   Path computation is only a subset of the typical functionality of a
   controller. In many use cases, issuing path computation requests
   comes along with the need to access other functionality on the same
   system. In addition to obtaining TE topology, for instance also
   configuration of services (set-up/modification/deletion) may be
   required, as well as:

1. Receiving notifications for topology changes as well as
integration with fault management

2. Performance management such as retrieving monitoring and telemetry
data

3. Service assurance, e.g., by triggering OAM functionality

4. Other fulfilment and provisioning actions beyond tunnels and
services, such as changing QoS configurations

   YANG is a very extensible and flexible data modeling language that
   can be used for all these use cases.

{: #topo-interaction}

## Interactions with TE topology

   The use cases described in {{use-cases}} have been described assuming
   that the topology view exported by each underlying controller to its
   client is aggregated using the "virtual node model", defined in
   {{!RFC7926}}.

   TE topology information, e.g., as provided by {{!RFC8795}}, could in
   theory be used by an underlying controller to provide TE information
   to its client thus allowing a PCE available within its client to
   perform multi-domain path computation on its own, without requesting
   path computations to the underlying controllers.

   In case the client does not implement a PCE function, as discussed in
   {{intro}}, it could not perform path computation based on TE topology
   information and would instead need to request path computation from
   the underlying controllers to get the information it needs to find
   the optimal end-to-end path.

   In case the client implements a PCE function, as discussed in 
   {{intro}}, the TE topology information needs to be complete and accurate,
   which would bring to scalability issues.

   Using TE topology to provide a "virtual link model" aggregation, as
   described in {{!RFC7926}}, may be insufficient, unless the aggregation
   provides complete and accurate information, which would still cause
   scalability issues, as described in {{topo-aggregation}} below.

   Using TE topology abstraction, as described in {{!RFC7926}}, may lead to
   compute an unfeasible path, as described in {{!RFC7926}} in 
   {{topo-abstraction}} below.

   Therefore when computing an optimal multi-domain path, there is a
   scalability trade-off between providing complete and accurate TE
   information and the number of path computation requests to the
   underlying controllers.

   The TE topology information used, in a complimentary way, to reduce
   the number for path computation requests to the underlying
   controllers, are described in {{topo-pc-complement}} below.

{: #topo-aggregation}

### TE topology aggregation

   Using the TE topology model, as defined in {{!RFC8795}}, the underlying
   controller can export the whole TE domain as a single TE node with a
   "detailed connectivity matrix" (which provides specific TE
   attributes, such as delay, Shared Risk Link Groups (SRLGs) and other
   TE metrics, between each ingress and egress links).

   The information provided by the "detailed connectivity matrix" would
   be equivalent to the information that should be provided by "virtual
   link model" as defined in {{!RFC7926}}.

   For example, in the Packet/Optical Integration use case, described in
   {{poi-uc}}, the Optical Domain Controller can make the information
   shown in {{fig-poi-example}} available to the Packet/Optical Coordinator as part
   of the TE topology information and the Packet/Optical Coordinator
   could use this information to calculate on its own the optimal path
   between R1 and R2, without requesting any additional information to
   the Optical Domain Controller.

   However, when designing the amount of information to provide within
   the "detailed connectivity matrix", there is a tradeoff to be
   considered between accuracy (i.e., providing "all" the information
   that might be needed by the PCE available within the client) and
   scalability.

   {{poi-multi-path}} below shows another example, similar to {{fig-poi-example}}, where
   there are two possible Optical paths between VP1 and VP4 with
   different properties (e.g., available bandwidth and cost).

~~~~ ascii-art
                    ............................
                    :  /--------------------\  :
                    : /       cost=65        \ :
                    :/    available-bw=10G    \:
                    O VP1                  VP4 O
           cost=10 /:\                        /:\ cost=10
                  / : \----------------------/ : \
          +----+ /  :         cost=50          :  \ +----+
          |    |/   :     available-bw=2G      :   \|    |
          | R1 |    :                          :    | R2 |
          |    |\   :                          :   /|    |
          +----+ \  :  /--------------------\  :  / +----+
                  \ : /       cost=55        \ : /
            cost=5 \:/    available-bw=3G     \:/ cost=5
                    O VP2                  VP5 O
                    :                          :
                    :..........................:
~~~~
{: #poi-multi-path title="Packet/Optical Integration path computation Example with multiple choices" artwork-name="poi-example-multiple.txt"}

   If the information in the "detailed connectivity matrix" is not
   complete/accurate, we can have the following drawbacks:

- If only the VP1-VP4 path with available bandwidth of 2 Gb/s and
cost 50 is reported, the client's PCE will fail to compute a 5
Gb/s path between routers R1 and R2, although this would be
feasible;

- If only the VP1-VP4 path with available bandwidth of 10 Gb/s and
cost 65 is reported, the client's PCE will compute, as optimal,
the 1 Gb/s path between R1 and R2 going through the VP2-VP5 path
within the optical domain while the optimal path would actually be
the one going thought the VP1-VP4 sub-path (with cost 50) within
the optical domain.

   Reporting all the information, as in {{poi-multi-path}}, using the "detailed
   connectivity matrix", is quite challenging from a scalability
   perspective. The amount of this information is not just based on
   number of end points (which would scale as N-square), but also on
   many other parameters, including client rate, user
   constraints/policies for the service, e.g. max latency < N ms, max
   cost, etc., exclusion policies to route around busy links, min
   Optical Signal to Noise Ratio (OSNR) margin, max pre-Forward Error
   Correction (FEC) Bit Error Rate (BER) etc. All these constraints
   could be different based on connectivity requirements.

   It is also worth noting that the "connectivity matrix" has been
   originally defined in Wavelength Switched Optical Networks (WSON),
   {{?RFC7446}}, to report the connectivity constrains of a physical node
   within the Wavelength Division Multiplexing (WDM) network: the
   information it contains is pretty "static" and therefore, once taken
   and stored in the TE data base, it can be always being considered
   valid and up-to-date in path computation request.

   The "connectivity matrix" is sometimes confused with "optical reach
   table" that contain multiple (e.g. k-shortest) regen-free reachable
   paths for every A-Z node combination in the network. Optical reach
   tables can be calculated offline, utilizing vendor optical design and
   planning tools, and periodically uploaded to the Controller: these
   optical path reach tables are fairly static. However, to get the
   connectivity matrix, between any two sites, either a regen free path
   can be used, if one is available, or multiple regen free paths are
   concatenated to get from the source to the destination, which can be
   a very large combination. Additionally, when the optical path within
   optical domain needs to be computed, it can result in different paths
   based on input objective, constraints, and network conditions. In
   summary, even though "optical reach table" is fairly static, which
   regen free paths to build the connectivity matrix between any source
   and destination is very dynamic, and is done using very sophisticated
   routing algorithms.

   Using the "basic connectivity matrix" with an abstract node to
   abstract the information regarding the connectivity constraints of an
   Optical domain, would make this information more "dynamic" since the
   connectivity constraints of an optical domain can change over time
   because some optical paths that are feasible at a given time may
   become unfeasible at a later time when e.g., another optical path is
   established.

   The information in the "detailed connectivity matrix" is even more
   dynamic since the establishment of another optical path may change
   some of the parameters (e.g., delay or available bandwidth) in the
   "detailed connectivity matrix" while not changing the feasibility of
   the path.

   There is therefore the need to keep the information in the "detailed
   connectivity matrix" updated which means that there another tradeoff
   between the accuracy (i.e., providing "all" the information that
   might be needed by the client's PCE) and having up-to-date
   information. The more the information is provided and the longer it
   takes to keep it up-to-date which increases the likelihood that the
   client's PCE computes paths using not updated information.

   It seems therefore quite challenging to have a "detailed connectivity
   matrix" that provides accurate, scalable and updated information to
   allow the client's PCE to take optimal decisions by its own.

   Considering the example in {{poi-multi-path}} with the approach defined in this
   document, the client, when it needs to set up an end-to-end path, it
   can request the Optical Domain Controller to compute a set of optimal
   paths (e.g., for VP1-VP4 and VP2-VP5) and take decisions based on the
   information received:

- When setting up a 5 Gb/s path between routers R1 and R2, the
Optical Domain Controller may report only the VP1-VP4 path as the
only feasible path: the Packet/Optical Coordinator can
successfully set up the end-to-end path passing though this
optical path;

- When setting up a 1 Gb/s path between routers R1 and R2, the
Optical Domain Controller (knowing that the path requires only 1
Gb/s) can report both the VP1-VP4 path, with cost 50, and the VP2-
VP5 path, with cost 65. The Packet/Optical Coordinator can then
compute the optimal path which is passing thought the VP1-VP4 sub-
path (with cost 50) within the optical domain.

{: #topo-abstraction}

### TE topology abstraction

   Using the TE topology model, as defined in {{!RFC8795}}, the underlying
   controller can export to its client an abstract TE topology, composed
   by a set of TE nodes and TE links, representing the abstract view of
   the topology under its control.

   For example, in the multi-domain TE network use case, described in
   {{md-uc}}, the TE Domain Controller 1 can export a TE topology
   encompassing the TE nodes A, B, C and D and the TE links
   interconnecting them. In a similar way, the TE Domain Controller 2
   can export a TE topology encompassing the TE nodes E, F, G and H and
   the TE links interconnecting them.

   In this example, for simplicity reasons, each abstract TE node maps
   with each physical node, but this is not necessary.

   In order to set up a multi-domain TE path (e.g., between nodes A and
   H), the Multi-Domain Controller can compute by its own an optimal
   end-to-end path based on the abstract TE topology information
   provided by the domain controllers. For example:

- Multi-Domain Controller can compute, based on its own TED data,
the optimal multi-domain path being A-B-C-E-G-H, and then request
the TE Domain Controllers to set up the A-B-C and E-G-H intra-
domain paths

- But, during path set-up, the TE Domain Controller may find out
that A-B-C intra-domain path is not feasible (as discussed in
{{md-uc}}, in optical networks it is typical to have some paths
not being feasible due to optical constraints that are known only
by the Optical Domain Controller), while only the path A-B-D is
feasible

- So what the Multi-Domain Controller has computed is not good and
it needs to re-start the path computation from scratch

  As discussed in {{topo-aggregation}}, providing more extensive abstract
  information from the TE Domain Controllers to the Multi-Domain
  Controller may lead to scalability problems.

  In a sense this is similar to the problem of routing and wavelength
  assignment within an optical domain. It is possible to do first
  routing (step 1) and then wavelength assignment (step 2), but the
  chances of ending up with a good path is low. Alternatively, it is
  possible to do combined routing and wavelength assignment, which is
  known to be a more optimal and effective way for optical path set-up.
  Similarly, it is possible to first compute an abstract end-to-end
  path within the Multi-Domain Controller (step 1) and then compute an
  intra-domain path within each optical domain (step 2), but there are
  more chances not to find a path or to get a suboptimal path than by
  performing multiple per-domain path computations and then stitching
  them together.

{: #topo-pc-complement}
### Complementary use of the TE topology

   As discussed in {{md-uc}}, there are some scalability issues with
   path computation requests in a multi-domain TE network with many TE
   domains, in terms of the number of requests to send to the TE Domain
   Controllers. It would therefore be worthwhile using the abstract TE
   topology information provided by the TE Domain Controllers to limit
   the number of requests.

   An example can be described considering the multi-domain abstract TE
   topology shown in {{fig-topo-many-domains}}. In this example, an end-to-end TE path
   between domains A and F needs to be set up. The transit TE domain
   should be selected between domains B, C, D and E.

~~~~ ascii-art
                          .........B.........
                          : _ _ _ _ _ _ _ _ :
                          :/               \:
                      +---O  NOT FEASIBLE   O---+
                cost=5|   :                 :   |
    ......A......     |   :.................:   |     ......F......
    :           :     |                         |     :           :
    :           O-----+   .........C.........   +-----O           :
    :           :         : /-------------\ :         :           :
    :           :         :/               \:         :           :
    :  cost<=20 O---------O   cost <= 30    O---------O cost<=20  :
    :          /: cost=5  :                 : cost=5  :\          :
    :  /------/ :         :.................:         : \------\  :
    : /         :                                     :         \ :
    :/ cost<=25 :         .........D.........         : cost<=25 \:
    O-----------O-------+ : /-------------\ : +-------O-----------O
    :\          : cost=5| :/               \: |cost=5 :          /:
    : \         :       +-O   cost <= 30    O-+       :         / :
    :  \------\ :         :                 :         : /------/  :
    : cost>=30 \:         :.................:         :/ cost>=30 :
    :           O-----+                         +-----O           :
    :...........:     |   .........E.........   |     :...........:
                      |   : /-------------\ :   |
                cost=5|   :/               \:   |cost=5
                      +---O   cost >= 30    O---+
                          :                 :
                          :.................:
~~~~
{: #fig-topo-many-domains title="Multi-domain with many domains (Topology information)" artwork-name="many-domains-topology.txt"}

   The actual cost of each intra-domain path is not known a priori from
   the abstract topology information. The Multi-Domain Controller only
   knows, from the TE topology provided by the underlying domain
   controllers, the feasibility of some intra-domain paths and some
   upper-bound and/or lower-bound cost information. With this
   information, together with the cost of inter-domain links, the Multi-
   Domain Controller can understand by its own that:

- Domain B cannot be selected as the path connecting domains A and F
is not feasible;

- Domain E cannot be selected as a transit domain since it is known
from the abstract topology information provided by domain
controllers that the cost of the multi-domain path A-E-F (which is
100, in the best case) will be always be higher than the cost of
the multi-domain paths A-D-F (which is 90, in the worst case) and
A-C-F (which is 80, in the worst case).

   Therefore, the Multi-Domain Controller can understand by its own that
   the optimal multi-domain path could be either A-D-F or A-C-F but it
   cannot know which one of the two possible option actually provides
   the optimal end-to-end path.

   The Multi-Domain Controller can therefore request path computation
   only to the TE Domain Controllers A, D, C and F (and not to all the
   possible TE Domain Controllers).

~~~~ ascii-art
                          .........B.........
                          :                 :
                      +---O                 O---+
    ......A......     |   :.................:   |     ......F......
    :           :     |                         |     :           :
    :           O-----+   .........C.........   +-----O           :
    :           :         : /-------------\ :         :           :
    :           :         :/               \:         :           :
    :  cost=15  O---------O    cost = 25    O---------O  cost=10  :
    :          /: cost=5  :                 : cost=5  :\          :
    :  /------/ :         :.................:         : \------\  :
    : /         :                                     :         \ :
    :/ cost=10  :         .........D.........         : cost=15  \:
    O-----------O-------+ : /-------------\ : +-------O-----------O
    :           : cost=5| :/               \: |cost=5 :           :
    :           :       +-O    cost = 15    O-+       :           :
    :           :         :                 :         :           :
    :           :         :.................:         :           :
    :           O-----+                         +-----O           :
    :...........:     |   .........E.........   |     :...........:
                      |   :                 :   |
                      +---O                 O---+
                          :.................:
~~~~
{: #fig-pc-many-domains title="Multi-domain with many domains (Path Computation information)" artwork-name="many-domain-path-computation.txt"}

   Based on these requests, the Multi-Domain Controller can know the
   actual cost of each intra-domain paths which belongs to potential
   optimal end-to-end paths, as shown in {{fig-pc-many-domains}}, and then compute the
   optimal end-to-end path (e.g., A-D-F, having total cost of 50,
   instead of A-C-F having a total cost of 70).

{: #rpc-motivation}
## Path Computation RPC

   The TE tunnel YANG data model, defined in {{!I-D.ietf-teas-yang-te}}, can support
   the need to request path computation, as described in section 5.1.2
   of {{!I-D.ietf-teas-yang-te}}.

   This solution is stateful since the state of each created "compute-
   only" TE tunnel path needs to be maintained, in the YANG datastores
   (at least in the running datastore and operational datastore), and
   updated, when underlying network conditions change.

   The RPC mechanism allows requesting path computation using a simple
   atomic operation, without creating any state in the YANG datastores,
   and it is the natural option/choice, especially with stateless PCE.

   It is very useful to provide both the options of using an RPC as well
   as of setting up TE tunnel paths in "compute-only" mode. It is
   suggested to use the RPC as much as possible and to rely on
   "compute-only" TE tunnel paths, when really needed.

   Using the RPC solution would imply that the underlying controller
   (e.g., a PNC) computes a path twice during the process to set up an
   LSP: at time T1, when its client (e.g., an MDSC) sends a path
   computation RPC request to it, and later, at time T2, when the same
   client (MDSC) creates a TE tunnel requesting the set-up of the LSP.
   The underlying assumption is that, if network conditions have not
   changed, the same path that has been computed at time T1 is also
   computed at time T2 by the underlying controller (e.g. PNC) and
   therefore the path that is set up at time T2 is exactly the same path
   that has been computed at time T1.

   However, since the operation is stateless, there is no guarantee that
   the returned path would still be available when path set-up is
   requested: this does not cause major issues when the time between
   path computation and path set-up is short (especially if compared
   with the time that would be needed to update the information of a
   very detailed connectivity matrix).

   In most of the cases, there is even no need to guarantee that the
   path that has been set up is the exactly same as the path that has
   been returned by path computation, especially if it has the same or
   even better metrics. Depending on the abstraction level applied by
   the server, the client may also not know the actual computed path.

   The most important requirement is that the required global objectives
   (e.g., multi-domain path metrics and constraints) are met. For this
   reason a path verification phase is always necessary to verify that
   the actual path that has been set up meets the global objectives (for
   example in a multi-domain network, the resulting end-to-end path
   meets the required end-to-end metrics and constraints).

   In most of the cases, even if the path being set up is not exactly
   the same as the path returned by path computation, its metrics and
   constraints are "good enough" and the path verification passes
   successfully. In the few corner cases where the path verification
   fails, it is possible repeat the whole process (path computation,
   path set-up and path verification).

   In case it is required to set up at T2 exactly the same path computed
   at T1, the RPC solution should not be used and, instead, a "compute-
   only" TE tunnel path should be set up, allowing also notifications in
   case the computed path has been changed.

   In this case, at time T1, the client (MDSC) creates a TE tunnel in a
   compute-only mode in the running datastore and later, at time T2,
   changes the configuration of that TE tunnel (not to be any more in a
   compute-only mode) to trigger the set-up of the LSP over the path
   which have been computed at time T1 and reported in the operational
   datastore.

   It is worth noting that also using the "compute-only" TE tunnel path,
   although increasing the likelihood that the computed path is
   available at path set-up, does not guarantee that because
   notifications may not be reliable or delivered on time. Path
   verification is needed also in this case.

   The solution based on "compute-only" TE tunnel path has also the
   following drawbacks:

-  Several messages required for any path computation

-  Requires persistent storage in the underlying controller

-  Need for garbage collection for stranded paths

-  Process burden to detect changes on the computed paths in order to
provide notifications update


{: #temp-state}

### Temporary reporting of the computed path state

   This section describes an optional extension to the stateless
   behavior of the path computation RPC, where the underlying
   controller, after having received a path computation RPC request,
   maintains some "transient state" associated with the computed path,
   allowing the client to request the set-up of exactly that path, if
   still available.

   This is similar to the "compute-only" TE tunnel path solution but, to
   avoid the drawbacks of the stateful approach, is leveraging the path
   computation RPC and the separation between configuration and
   operational datastore, as defined in the NMDA architecture {{?RFC8342}}.

   The underlying controller, after having computed a path, as requested
   by a path computation RPC, also creates a TE tunnel instance within
   the operational datastore, to store that computed path. This would be
   similar to a "compute-only" TE tunnel path, with the only difference
   that there is no associated TE tunnel instance within the running
   datastore.

   Since the underlying controller stores in the operational datastore
   the computed path based on an abstract topology it exposes, it also
   remembers, internally, which is the actual native path (physical
   path), within its native topology (physical topology), associated
   with that compute-only TE tunnel instance.

   Afterwards, the client (e.g., MDSC) can request the set-up of that
   specific path by creating a TE tunnel instance (not in compute-only
   mode) in the running datastore using the same tunnel-name of
   the existing TE tunnel in the operational datastore: this will
   trigger the underlying controller to set up that path, if still
   available.

   There are still cases where the path being set up is not exactly the
   same as the path that has been computed:

-  When the tunnel is configured with path constraints which are not
compatible with the computed path;

- When the tunnel set-up is requested after the resources of the
computed path are no longer available;

- When the tunnel set-up is requested after the computed path is no
longer known (e.g. due to a server reboot) by the underlying
controller.

   In all these cases, the underlying controller should compute and set
   up a new path.

   Therefore the "path verification" phase, as described in {{rpc-motivation}}
   above, is always needed to check that the path that has been set up
   is still "good enough".

   Since this new approach is not completely stateless, garbage
   collection is implemented using a timeout that, when it expires,
   triggers the removal of the computed path from the operational
   datastore. This operation is fully controlled by the underlying
   controller without the need for any action to be taken by the client
   that is not able to act on the operational datastore. The default
   value of this timeout is 10 minutes but a different value may be
   configured by the client.

   In addition, it is possible for the client to tag each path
   computation request with a transaction-id allowing for a faster
   removal of all the paths associated with a transaction-id, without
   waiting for their timers to expire.

   The underlying controller can remove from the operational datastore
   all the paths computed with a given transaction-id which have not
   been set up either when it receives a Path Delete RPC request for
   that transaction-id or, automatically, right after the set-up up of a
   path that has been previously computed with that transaction-id.

   This possibility is useful when multiple paths are computed but, at
   most, only one is set up (e.g., in multi-domain path computation
   scenario scenarios). After the selected path has been set up (e.g, in
   one domain during multi-domain path set-up), all the other
   alternative computed paths can be automatically deleted by the
   underlying controller (since no longer needed). The client can also
   request, using the Path Delete RPC request, the underlying controller
   to remove all the computed paths, if none of them is going to be set
   up (e.g., in a transit domain not being selected by multi-domain path
   computation and so not being automatically deleted).

   This approach is complimentary and not alternative to the timer which
   is always needed to avoid stranded computed paths being stored in the
   operational datastore when no path is set up and no explicit Path
   Delete RPC request is received.

# Path computation and optimization for multiple paths

   There are use cases, where it is advantageous to request path
   computation for a set of paths, through a network or through a
   network domain, using a single request {{!RFC5440}}.

   In this case, sending a single request for multiple path
   computations, instead of sending multiple requests for each path
   computation, would reduce the protocol overhead and it would consume
   less resources (e.g., threads in the client and server).

   In the context of a typical multi-domain TE network, there could
   multiple choices for the ingress/egress points of a domain and the
   Multi-Domain Controller needs to request path computation between all
   the ingress/egress pairs to select the best pair. For example, in the
   example of {{md-uc}}, the Multi-Domain Controller needs to request
   the TE Network Controller 1 to compute the A-C and the A-D paths and
   to the TE Network Controller 2 to compute the E-H and the F-H paths.

   It is also possible that the Multi-Domain Controller receives a
   request to set up a group of multiple end to end connections. The
   Multi-Domain Controller needs to request each TE domain Controller to
   compute multiple paths, one (or more) for each end to end connection.

   There are also scenarios where it can be needed to request path
   computation for a set of paths in a synchronized fashion.

   One example could be computing multiple diverse paths. Computing a
   set of diverse paths in an unsynchronized fashion, leads to the
   possibility of not being able to satisfy the diversity requirement.
   In this case, it is preferable to compute a sub-optimal primary path
   for which a diversely routed secondary path exists.

   There are also scenarios where it is needed to request optimizing a
   set of paths using objective functions that apply to the whole set of
   paths, see {{!RFC5541}}, e.g. to minimize the sum of the costs of all
   the computed paths in the set.

# YANG data model for requesting Path Computation

   This document define a YANG RPC to request path computation as an
   "augmentation" of tunnel-rpc, defined in {{!I-D.ietf-teas-yang-te}}. This model
   provides the RPC input attributes that are needed to request path
   computation and the RPC output attributes that are needed to report
   the computed paths.

~~~~ ascii-art
{::include snapshots/overview.txt}
~~~~
{: artwork-name="overview.txt"}

   This model extensively re-uses the grouping defined in {{!I-D.ietf-teas-yang-te}}
   and {{!RFC8776}} to ensure maximal syntax and semantics commonality.

   This YANG data model allows one RPC to include multiple path
   requests, each path request being identified by a request-id.
   Therefore, one RPC can return multiple responses, one for each path
   request, being identified by a response-id equal to the corresponding
   request-id. Each response reports one or more computed paths, as
   requested by the k-requested-paths attribute. By default, each
   response reports one computed path.

## Synchronization of multiple path computation requests

   The YANG data model permits the synchronization of a set of multiple
   path requests (identified by specific request-id) all related to a
   "svec" container emulating the syntax of the Synchronization VECtor
   (SVEC) PCEP object, defined in {{!RFC5440}}.

~~~~ ascii-art
{::include snapshots/synchronization.txt}
~~~~
{: artwork-name="synchronization.txt"}

   The model, in addition to the metric types, defined in {{!RFC8776}},
   which can be applied to each individual path request, supports also
   additional metric types, which apply to a set of synchronized
   requests, as referenced in {{!RFC5541}}. These additional metric types
   are defined by the following YANG identities:

- svec-metric-type: base YANG identity from which cumulative metric
types identities are derived.

- svec-metric-cumul-te: cumulative TE cost metric type, as defined
in {{!RFC5541}}.

- svec-metric-cumul-igp: cumulative IGP cost metric type, as defined
in {{!RFC5541}}.

- svec-metric-cumul-hop: cumulative Hop metric type, representing
the cumulative version of the Hop metric type defined in
{{!RFC8776}}.

- svec-metric-aggregate-bandwidth-consumption: aggregate bandwidth
consumption metric type, as defined in {{!RFC5541}}.

- svec-metric-load-of-the-most-loaded-link: load of the most loaded
link metric type, as defined in {{!RFC5541}}.


## Returned metric values

   This YANG data model provides a way to return the values of the
   metrics computed by the path computation in the output of RPC,
   together with other important information (e.g. SRLG, affinities,
   explicit route), emulating the syntax of the "C" flag of the "METRIC"
   PCEP object {{!RFC5440}}:

~~~~ ascii-art
{::include snapshots/returned-metrics.txt}
~~~~
{: artwork-name="returned-metrics.txt"}

   It also allows the client to request which information (metrics, SRLG
   and/or affinities) should be returned:

~~~~ ascii-art
{::include snapshots/requested-metrics.txt}
~~~~
{: artwork-name="requested-metrics.txt"}

   This feature is essential for path computation in a multi-domain TE
   network as described in {{md-uc}}. In this case, the metrics
   returned by a path computation requested to a given underlying
   controller must be used by the client to compute the best end-to-end
   path. If they are missing, the client cannot compare different paths
   calculated by the underlying controllers and choose the best one for
   the optimal end-to-end (e2e) path.

## Multiple Paths Requests for the same TE tunnel

   The YANG data model allows including multiple requests for different
   paths intended to be used within the same tunnel or within different
   tunnels.

   When multiple requested paths are intended to be used within the same
   tunnel (e.g., requesting path computation for the primary and
   secondary paths of a protected tunnel), the set of attributes that
   are intended to be configured on per-tunnel basis rather than on per-
   path basis are common to all these path requests. These attributes
   includes both attributes which can be configured only a per-tunnel
   basis (e.g., tunnel-name, source/destination TTP, encoding and
   switching-type) as well attributes which can be configured both on a
   per-tunnel and on a per-path basis (e.g., the te-bandwidth or the
   associations).

   Therefore, a tunnel-attributes list is defined, within the path
   computation request RPC:

~~~~ ascii-art
{::include snapshots/tunnel-attributes-list.txt}
~~~~
{: artwork-name="tunnel-attributes-list.txt"}

   The path requests that are intended to be used within the same tunnel
   should reference the same entry in the tunnel-attributes list. This
   allows:

- avoiding repeating the same set of per-tunnel parameters on
multiple requested paths;

- the server to understand what attributes are intended to be
configured on a per-tunnel basis (e.g., the te-bandwidth
configured in the tunnel-attributes) and what attributes are
intended to be configured on a per-path basis(e.g., the te-
bandwidth configured in the path-request). This could be useful
especially when the server also creates a TE tunnel instance
within the operational datastore to report the computed paths, as
described in {{temp-state}}: in this case, the tunnel-name is also
used as the suggested name for that TE tunnel instance.

   The YANG data model allows also including requests for paths intended
   to modify existing tunnels (e.g., adding a protection path for an
   existing un-protected tunnel). In this case, the per-tunnel
   attributes are already provided in an existing TE tunnel instance and
   do not need to be re-configured in the path computation request RPC.
   Therefore, these requests should reference an existing TE tunnel
   instance.

   It is also possible to request computing paths without indicating in
   which tunnel they are intended to be used (e.g., in case of an
   unprotected tunnel). In this case, the per-tunnel attributes could be
   provided together with the per-path attributes in the path request,
   without using the tunnel-attributes list.

   The choices below are defined to distinguish the cases above:

- whether the per-tunnel attributes are configured by reference
(providing a leafref), to:

   - either a TE tunnel instance, if it exists;

   - or to an entry of the tunnel-attributes list, if the TE tunnel
instance does not exist;

- or by value, providing the set of tunnel attributes within the
path request:

~~~~ ascii-art
{::include snapshots/tunnel-attributes.txt}
~~~~
{: artwork-name="tunnel-attributes.txt"}

### Tunnel attributes specified by value

   The (value) case provides the set of attributes that are configured
   only on per-tunnel basis (e.g., tunnel-name, source/destination TTP,
   encoding and switching-type).

   In this case, it is assumed that the requested path will be the only
   path within a tunnel.

   If the only path within a tunnel is the transit segment of a multi-domain end-to-end path, it can be of any type (primary, secondary, reverse-primary
   or reverse-secondary). The (path-role) choice is used to specify its role in the path request:

~~~~ ascii-art
{::include snapshots/tunnel-by-value.txt}
~~~~
{: artwork-name="tunnel-by-value.txt"}

   In all the other cases, the only path within a tunnel is a primary path.

   The secondary-path, the primary-reverse-path and the secondary-
   reverse-path are presence container used to indicate the role of the
   path: by default, the path is assumed to be a primary path.

   They optionally can contain the name of the primary-path under which
   the requested path could be associated within the YANG tree structure
   defined in {{!I-D.ietf-teas-yang-te}}, which could be useful when the server also
   creates a TE tunnel instance within the operational datastore to
   report the computed paths, as described in {{temp-state}}: in this
   case, the tunnel-name and the path names are also used as the
   suggested name for that TE tunnel and path instances.

### Tunnel attributes specified by reference

   The (reference) case provides the information needed to associate
   multiple path requests that are intended to be used within the same
   tunnel.

   In order to indicate the role of the path being requested within the
   intended tunnel (e.g., primary or secondary path), the (path-role)
   choice is defined:

~~~~ ascii-art
{::include snapshots/tunnel-by-reference.txt}
~~~~
{: artwork-name="tunnel-by-reference.txt"}

   The primary-path is a presence container used to indicate that the
   requested path is intended to be used as a primary path. It can also
   contain some attributes which are configured only on primary paths
   (e.g., the k-requested-paths).

   The secondary-path container indicates that the requested path is
   intended to be used as a secondary path and it contains at least
   references to one or more primary paths which can use it as a
   candidate secondary path:

~~~~ ascii-art
{::include snapshots/secondary-path.txt}
~~~~
{: artwork-name="secondary-path.txt"}

   A requested secondary path can reference any requested primary paths,
   and, in case they are intended to be used within an existing TE
   tunnel, it could also reference any existing primary-paths.

   The secondary-path container can also contain some attributes which
   are configured only on secondary paths (e.g., the protection-type).

   The primary-reverse-path container indicates that the requested path
   is intended to be used as a primary reverse path and it contains only
   the reference to the primary path which is intended to use it as a
   reverse path:

~~~~ ascii-art
{::include snapshots/primary-reverse-path.txt}
~~~~
{: artwork-name="primary-reverse-path.txt"}

   A requested primary reverse path can reference either a requested
   primary path, or, in case it is intended to be used within an
   existing TE tunnel, an existing primary-path.

   The secondary-reverse-path container indicates that the requested
   path is intended to be used as a secondary reverse path and it
   contains at least references to one or more primary paths, whose
   primary reverse path can use it as a candidate secondary reverse
   path:

~~~~ ascii-art
{::include snapshots/secondary-reverse-path.txt}
~~~~
{: artwork-name="secondary-reverse-path.txt"}

   A requested secondary reverse path can reference any requested
   primary paths, and, in case they are intended to be used within an
   existing TE tunnel, it could reference also existing primary-paths.

   The secondary-reverse-path container can also contain some attributes
   which are configured only on secondary reverse paths (e.g., the
   protection-type).

   In case the requested path is a transit segment of a multi-domain
   end-to-end path, the primary-path may not be needed to be
   setup/computed. However, the request for path computation of a
   secondary-path or a primary-reverse or of a secondary-reverse-path
   requires that the primary-path exists or it is requested within the
   same RPC request. In the latter case, the path request for the
   primary-path should have an empty 'route-object-include-exclude' list,
   as described in section 5.1.1 of {{!I-D.ietf-teas-yang-te}}, to indicate to the server that
   path computation is not requested and no path properties will
   therefore be returned in the RPC response.

## Multi-Layer Path Computation

   The models supports requesting multi-layer path computation following
   the same approach based on dependency tunnels, as defined in {{!I-D.ietf-teas-yang-te}}.

   The tunnel-attributes of a given client-layer path request can
   reference server-layer TE tunnels which can already exist in the YANG
   datastore or be specified in the tunnel-attributes list, within the
   same RPC request:

~~~~ ascii-art
{::include snapshots/dependency-tunnels.txt}
~~~~
{: artwork-name="dependency-tunnels.txt"}

   In a similar way as in {{!I-D.ietf-teas-yang-te}}, the server-layer tunnel
   attributes should provide the information of what would be the
   dynamic link in the client layer topology supported by that tunnel,
   if instantiated:

~~~~ ascii-art
{::include snapshots/hierarchical-link.txt}
~~~~
{: artwork-name="hierarchical-link.txt"}

   It is worth noting that since path computation RPC is stateless, the
   dynamic hierarchical links configured for the server-layer tunnel
   attributes cannot be used for path computation of any client-layer
   path unless explicitly referenced in the dependency-tunnel-attributes
   list within the same RPC request.

# YANG data model for TE path computation

{: #pc-tree}

## Tree diagram

   {{fig-pc-tree}} below shows the tree diagram of the YANG data model defined
   in module ietf-te-path-computation.yang, defined in {{pc-yang}}.

~~~~ ascii-art
{::include ./ietf-te-path-computation.tree}
~~~~
{: #fig-pc-tree title="TE path computation tree diagram"
artwork-name="ietf-te-path-computation.tree"}

{: #pc-yang}

## YANG module

~~~~ yang
{::include ./ietf-te-path-computation.yang}
~~~~
{: #fig-pc-yang title="TE path computation YANG module"
sourcecode-markers="true" sourcecode-name="ietf-te-path-computation@2022-10-21.yang"}

# Security Considerations

   This document describes use cases of requesting Path Computation
   using YANG data models, which could be used at the ABNO Control
   Interface {{?RFC7491}} and/or between controllers in ACTN {{?RFC8453}}. As
   such, it does not introduce any new security considerations compared
   to the ones related to YANG specification, ABNO specification and
   ACTN Framework defined in {{!RFC7950}}, {{?RFC7491}} and {{?RFC8453}}.

   The YANG module defined in this document is designed to be accessed via
   the NETCONF protocol {{!RFC6241}} or RESTCONF protocol {{!RFC8040}}. The
   lowest NETCONF layer is the secure transport layer, and the
   mandatory-to-implement secure transport is Secure Shell (SSH)
   {{!RFC6242}}. The lowest RESTCONF layer is HTTPS, and the mandatory-to-
   implement secure transport is TLS {{!RFC8446}}.

   The Network Configuration Access Control Model (NACM) 
   {{!RFC8341}} provides the means to
   restrict access for particular NETCONF or RESTCONF users to a
   preconfigured subset of all available NETCONF or RESTCONF protocol
   operations and content.

   The YANG module defined in this document augments the "tunnels-path-compute" and the "tunnel-actions" RPCs defined in {{!I-D.ietf-teas-yang-te}}. The security considerations provided in {{!I-D.ietf-teas-yang-te}} are also applicable to the YANG module
   defined in this document.

   Some of the RPC operations defined in this YANG module may be considered
   sensitive or vulnerable in some network environments.  It is thus
   important to control access to these operations. These are the
   operations and their sensitivity/vulnerability:

   "te-pc:response/computed-paths-properties": provides the same information provided by the "te:computed-paths-properties" defined in {{!I-D.ietf-teas-yang-te}}. The security considerations provided in {{!I-D.ietf-teas-yang-te}} for the TE tunnel state apply also to this subtree.

   "te-pc:response/te-pc:tunnel-ref", "te-pc:response/te-pc:primary-path-ref", "te-pc:response/te-pc:primary-reverse-path-ref", "te-pc:response/te-pc:secondary-path-ref" and "te-pc:response/te-pc:secondary-reverse-path-ref" provides a reference where the same information provided in "te-pc:response/computed-paths-properties" is temporarly stored with the operational datastore (see {{temp-state}}). Therefore access to this information does not provide any additional security issue that the information provided with "te-pc:response/computed-paths-properties".

   "/te:tunnels-actions": the YANG model defined in this document augments this action with a new action type that allows deleting the transient states of computed paths (see {{temp-state}}). A malicious use of this action would have no impact on the paths carrying live traffic but it would preclude the client from using the "transient states" to request the set-up of exactly that path, if still available.

   The security considerations spelled out in the
   YANG specification {{!RFC7950}} apply for this document as well.

# IANA Considerations

   This document registers the following URIs in the "ns" subregistry
   within the "IETF XML registry" {{!RFC3688}}.

~~~~
      URI: urn:ietf:params:xml:ns:yang:ietf-te-path-computation
      Registrant Contact:  The IESG.
      XML: N/A, the requested URI is an XML namespace.
~~~~

   This document registers a YANG module in the "YANG Module Names"
   registry {{!RFC7950}}.

~~~~
      name:      ietf-te-path-computation
      namespace: urn:ietf:params:xml:ns:yang:ietf-te-path-computation
      prefix:    te-pc
      reference: this document
~~~~

--- back

# Examples

This section contains examples of use of the model with RESTCONF
{{!RFC8040}} and JSON encoding.

These examples show how path computation can be requested for the tunnels configuration provided in Appendix A of {{!I-D.ietf-teas-yang-te}}.

{: #basic-example}

## Basic Path Computation

This example uses the path computation RPC defined in this document to request the computation of the path for the tunnel defined in section 12.1 of of {{!I-D.ietf-teas-yang-te}}.

In this case, the TE Tunnel has only one primary path with no specific constraints.

~~~~ ascii-art
POST /restconf/operations/ietf-te:tunnels-path-compute HTTP/1.1
Host: example.com
Content-Type: application/yang-data+json

~~~~
~~~~ json
{::include ./json-examples/basic.json}
~~~~
{: sourcecode-markers="false" sourcecode-name="basic.json"}

{: #transient-state-example}

## Path Computation with transient state

This example uses the path computation RPC defined in this document to request the computation of the path for the tunnel defined in section 12.1 of of {{!I-D.ietf-teas-yang-te}} requesting some transient state to be reported within the operational datastore, as described {{temp-state}}.

In this case, the TE Tunnel has only one primary path with no specific constraints.

~~~~ ascii-art
POST /restconf/operations/ietf-te:tunnels-path-compute HTTP/1.1
Host: example.com
Content-Type: application/yang-data+json

~~~~
~~~~ json
{::include ./json-examples/transient-state.json}
~~~~
{: sourcecode-markers="false" sourcecode-name="transient-state.json"}

{: #global-path-constraint-example}

## Path Computation with Global Path Constraint

This example uses the path computation RPC defined in this document to request the computation of the path for the tunnel defined in section 12.3 of of {{!I-D.ietf-teas-yang-te}}. The 'named path constraint' is created in section 12.2 of {{!I-D.ietf-teas-yang-te}} applies to this path computation request.

~~~~ ascii-art
POST /restconf/operations/ietf-te:tunnels-path-compute HTTP/1.1
Host: example.com
Content-Type: application/yang-data+json

~~~~
~~~~ json
{::include ./json-examples/global-path-constraint.json}
~~~~
{: sourcecode-markers="false" sourcecode-name="global-path-constraint.json"}

{: #tunnel-path-constraint-example}

## Path Computation with Per-tunnel Path Constraint

This example uses the path computation RPC defined in this document to request the computation of the path for the tunnel defined in section 12.4 of of {{!I-D.ietf-teas-yang-te}}, using a per tunnel path constraint.

~~~~ ascii-art
POST /restconf/operations/ietf-te:tunnels-path-compute HTTP/1.1
Host: example.com
Content-Type: application/yang-data+json

~~~~
~~~~ json
{::include ./json-examples/tunnel-path-constraint.json}
~~~~
{: sourcecode-markers="false" sourcecode-name="tunnel-path-constraint.json"}

## Path Computation result

This example reports the output of the path computation RPC request described in {{tunnel-path-constraint-example}}.

~~~~ ascii-art
HTTP/1.1 200 OK
Host: example.com
Content-Type: application/yang-data+json

~~~~
~~~~ json
{::include ./json-examples/computed-path.json}
~~~~
{: sourcecode-markers="false" sourcecode-name="computed-path.json"}

## Path Computation with Primary and Secondary Paths

This section contains examples of use of the model to compute primary and secondary paths described in section 12.6 of {{!I-D.ietf-teas-yang-te}}.

These examples consider the cases where:
- primary and reverse paths are unidirectional and not co-routed (example-1);
- primary and reverse paths are bidirectional (example-3);
- primary and reverse paths are unidirectional and co-routed (example-4).

~~~~ ascii-art
POST /restconf/operations/ietf-te:tunnels-path-compute HTTP/1.1
Host: example.com
Content-Type: application/yang-data+json

~~~~
~~~~ json
{::include ./json-examples/primary-secondary-paths.json}
~~~~
{: sourcecode-markers="false" sourcecode-name="primary-secondary-paths.json"}


{: numbered="false"}

# Acknowledgments

   The authors would like to thank Igor Bryskin and Xian Zhang for
   participating in the initial discussions that have triggered this
   work and providing valuable insights.

   The authors would like to thank the authors of the TE tunnel YANG
   data model {{!I-D.ietf-teas-yang-te}}, in particular Igor Bryskin, Vishnu Pavan
   Beeram, Tarek Saad and Xufeng Liu, for their inputs to the
   discussions and support in having consistency between the Path
   Computation and TE tunnel YANG data models.

   The authors would like to thank Adrian Farrel, Dhruv Dhody, Igor
   Bryskin, Julien Meuric and Lou Berger for their valuable input to the
   discussions that has clarified that the path being set up is not
   necessarily the same as the path that has been previously computed
   and, in particular to Dhruv Dhody, for his suggestion to describe the
   need for a path verification phase to check that the actual path
   being set up meets the required end-to-end metrics and constraints.

   The authors would like to thank Aihua Guo, Lou Berger, Shaolong Gan,
   Martin Bjorklund and Tom Petch for their useful comments on how to
   define XPath statements in YANG RPCs.

   The authors would like to thank Haomian Zheng, Yanlei Zheng, Tom
   Petch, Aihua Guo and Martin Bjorklund for their review and valuable
   comments to this document.

   Previous versions of document were prepared using 2-Word-v2.0.template.dot.

This document was prepared using kramdown.
