---
title: "A YANG Data Model for Network Incident Management"
abbrev: "Network Incident Management"
category: std

docname: draft-feng-nmop-network-incident-yang-latest
submissiontype: IETF
number:
date:
consensus: true
v: 3
area: "Operations and Management"
workgroup: "NMOP Working Group"
keyword:
 - network incident management
 - yang data model
 - network model
venue:
  group: "NMOP Working Group"
  type: "Working Group"
  mail: "nmop@ietf.org"
  arch: "https://mailarchive.ietf.org/arch/browse/opsawg/"
  github: "billwuqin/network-incident"
  latest: "https://billwuqin.github.io/network-incident/draft-feng-nmop-network-incident-yang.html"

author:
-
     fullname: Chong Feng
     role: editor
     email: fengchonglly@gmail.com
-
     fullname: Tong Hu
     organization: China Mobile (Hangzhou) Information Technology Co., Ltd
     street: Building A01, 1600 Yuhangtang Road, Wuchang Street, Yuhang District
     city: Hangzhou
     code: 311121
     country: China
     email: hutong@cmhi.chinamobile.com
-
    fullname: Luis Miguel Contreras Murillo
    organization: Telefonica I+D
    city: Madrid
    country: Spain
    email: luismiguel.contrerasmurillo@telefonica.com
-
    fullname: Thomas Graf
    organization: Swisscom
    street: Binzring 17CH-8045
    city: Zurich
    country: Switzerland
    email: thomas.graf@swisscom.com
-
    fullname: Qin Wu
    organization: Huawei
    street: 101 Software Avenue, Yuhua District
    city: Nanjing
    code: 210012
    country: China
    email: bill.wu@huawei.com
-
    fullname: Chaode Yu
    organization: Huawei
    email: yuchaode@huawei.com
-
    fullname: Nigel Davis
    organization: Ciena
    email: ndavis@ciena.com

contributor:
-
    name: MingShuang Jin
    org: Huawei Technologies
    email: jinmingshuang@huawei.com
-
    name: Chunchi Liu
    org: Huawei Technologies
    email: liuchunchi@huawei.com
-
    name: Aihua Guo
    org: Futurewei Technologies
    email: aihuaguo.ietf@gmail.com
-
    name: Zhidong Yin
    org: Huawei
    email: yinzhidong@huawei.com
-
    name: Guoxiang Liu
    org: Huawei
    email: liuguoxiang@huawei.com
-
    name: Kaichun Wu
    org: Huawei
    email: wukaichun@huawei.com
-
    name: Yanlei Zheng
    org: China Unicom
    email: zhengyanlei@chinaunicom.cn
-
    name: Yunbin Xu
    org: CAICT
    email: xuyunbin@caict.ac.cn
-
    name: Xing Zhao
    org: CAICT
    email: zhaoxing@caict.ac.cn

normative:

informative:

 BERT:
   title:  BERT (language model)
   target: https://en.wikipedia.org/wiki/BERT_(language_model)

 TMF724A:
   title: Incident Management API Profile v1.0.0
   target: https://www.tmforum.org/resources/standard/tmf724a-incident-management-api-profile-v1-0-0/
   date: 2023

 W3C-Trace-Context:
   title: W3C Recommendation on Trace Context
   target: https://www.w3.org/TR/2021/REC-trace-context-1-20211123/
   date: 2021

--- abstract

A network incident refers to an unexpected interruption of a network
service, degradation of a network service quality, or sub-health of a
network service.  Different data sources including alarms, metrics
and other anomaly information can be aggregated into few amount of
network incidents by data correlation analysis and the service impact
analysis.

This document defines YANG Modules for the network incident lifecycle
management.  The YANG modules are meant to provide a standard way to
report, diagnose, and resolve network incidents for the sake of
network service health and root cause analysis.

--- middle

# Introduction

{{?RFC8969}} defines a framework for Automating Service and Network
Management with YANG to full life cycle network management.  A set of
YANG data models have already been developed in IETF for Network
performance monitoring and fault monitoring,e.g.,A YANG {{?RFC7950}}
data model for alarm management {{?RFC8632}} defines a standard
interface for alarm management.  A data model for Network and VPN
Service Performance Monitoring {{?RFC9375}} defines a standard interface
for network performance management.  In addition, distributed tracing
mechanism defined in {{W3C-Trace-Context}} can also be used to analyze
and debug operations, such as configuration transactions, across
multiple distributed systems.

However these YANG data models for network maintenance are based on
specific data source information and manage alarms and performance
metrics data separately by different layers in various different
management systems.  In addition, the frequency and quantity of
alarms and performance metrics data reported to Operating Support
System (OSS) are increased dramatically (in many cases multiple
orders of magnitude) with the growth of service types and complexity
and greatly overwhelm OSS platforms; with existing known dependency
relation between fault, alarm and events at each layer (e.g.,packet
layer or optical layer), the traditional solutions, e.g., data
compression are time-consuming and labor-intensive, usually rely on
maintenance engineers' experience for data analysis, which result in
low processing efficiency, inaccurate root cause identification and
duplicated tickets.  In addition, it is also difficult to assess the
impact of alarms, performance metrics and other anomaly data on
network services without known relation across layer of the network
topology data or the relation with other network topology data.

To address these challenges, a network wide incident-centric solution
is proposed to establish dependency relation with both network
service and network topology at different layers , which not only can
be used at specific layer in specific domain but also can be used to
span across layer for multi-layer network troubleshooting.  A network
incident refers to an unexpected interruption of a network service,
degradation of a network service quality, or sub-health of a network
service {{TMF724A}}.  Different data sources including alarms, metrics
and other anomaly information can be aggregated into few amount of
incidents irrespective layer by correlation analysis and the service
impact analysis.  For example, the protocols related to the interface
fail to work properly due to Service Level Objective (SLO) violation,
large amount of alarms may be reported to upper layer management
system and aggregated into one or a few incidents when some network
services may be affected by this incident (e.g.  L3VPN services
related with the interface will become unavailable
{{?I-D.ietf-ippm-pam}} ).  An incident may also be raised through the
analysis of some network performance metrics, for example, as
described in SAIN {{?RFC9417}} , network services can be decomposed to
several sub-services, specific metrics are monitored for each sub-
service, symptoms will occur if services/sub-services are unhealthy
(after analyzing metrics), these symptoms may raise one incident when
it causes degradation of the network services.

In addition, Artificial Intelligence (AI) and Machine Learning (ML)
play a important role in the processing of large amounts of data with
complex data correlations.  For example, Neural Network Algorithm or
Hierarchy Aggregation Algorithm can be used to replace manual alarm
data correlation.  Through online and offline learning, these
algorithms can be continuously optimized to improve the efficiency of
fault diagnosis.

This document defines the concepts, requirements, and architecture of
incident management.  The document also defines a YANG data model for
incident lifecycle management, which improves troubleshooting
efficiency, ensures network service quality, and improves network
automation {{?RFC8969}}.



# Conventions and Definitions

{::boilerplate bcp14-tagged}

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT",
"SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and
"OPTIONAL" in this document are to be interpreted as described in BCP
14 {{!RFC2119}} {{!RFC8174}} when, and only when, they appear in all
capitals, as shown here.

The following terms are defined in {{?RFC8632}} and are not redefined here:

*  alarm

The following terms are defined in this document:


Incident:  An unexpected interruption of a network service,
   degradation of network service quality, or sub-health of a network
   service {{TMF724A}}.


Problem:  The cause of one or more incidents.  The cause is not
   usually known when a problem record is created, and the problem
   management process is responsible for further investigation
   {{TMF724A}}.

Incident management:  Lifecycle management of incidents including
   incident identification, reporting, acknowledge, diagnosis, and
   resolution.

Incident management system:  An entity which implements incident
   management.  It include incident management server and incident
   management client.

Incident management server:  An entity which provides some functions
   of incident management.  For example, it can detect an incident,
   perform incident diagnosis, resolution and prediction,etc.

Incident management client:  An entity which can manage incidents.
   For example, it can receive incident notifications, query the
   information of incidents, instruct the incident management server
   to diagnose, resolve, etc.


# Sample Use Cases

## Incident-Based Trouble Tickets dispatching

      Traditionally, the dispatching of trouble tickets is mostly based on
      alarms data analysis and need to involve operators' maintenance
      engineers.  These operators' maintenance engineers are able to
      monitor and detect that alarms at both end devices of specific
      network tunnel or at both optical layer and IP layer which are
      associated with the same network fault.  Therefore, they can
      correlate these alarms to the same trouble ticket, which is in the
      low automation.  If there are more alarms, then the human costs for
      network maintenance are increased accordingly.

      Some operators preconfigure whitelist and adopt some coarse
      granularity data correlation rules for the alarm management.  It
      seems to improve fault management automation.  However, some trouble
      tickets could be missed if the filtering conditions are too strict.
      If the filtering conditions are not strict, it might end up with
      multiple trouble tickets being dispatched to the same network fault.

      It is hard to achieve a perfect balance between the network
      management automation and duplicated trouble tickets under the
      traditional working situations.  However, with the help of the
      incident management, massive alarms can be aggregated into a few
      network incidents based on service impact analysis, the number of
      trouble tickets will be greatly reduced.  At the same time, the
      efficiency of network troubleshooting can be largely improved. which
      address the pain point of traditional trouble ticket dispatching.

   ##  Incident Derivation from L3VPN services Unavailability

      The service attachment points defined in {{?RFC9408}} represent the
      network reference points where network services can be delivered to
      customers.

      SLOs can be used to characterize the ability of a particular set of
      nodes to communicate according to certain measurable expectations
      {{?I-D.ietf-ippm-pam}}.  For example, an SLA might state that any given
      SLO applies to at least a certain percentage of packets, allowing for
      a certain level of packet loss and exceeding packet delay threshold
      to take place.  An SLA might establish a multi-tiered SLO of end to
      end latency as follows:

      *  not to exceed 30 ms for any packet;

      *  not to exceed 25 ms for 99.999% of packets;

      *  not to exceed 20 ms for 99% of packets.

      This SLA information can be bound with two or multiple service
      attachment point defined in {{?RFC9408}}, so that the service
      orchestration layer can use these interfaces to commit the delivery
      of a service on specific point to point service topology or point to
      multi-point topology.  Upon specific levels of a threshold of an SLO
      is violated, a specific network incident, associated with,let's say
      L3VPN service will be derived.

   ##  Multi-layer Fault Demarcation

      When a fault occurs in a network that contains both packet-layer
      devices and optical-layer devices, it may cause correlative faults in
      both layers, i.e., packet layer and optical layer.  Specifically,
      fault propagation could be classified into three typical types.
      First, fault occurs at a packet-layer device will further cause fault
      (e.g.,WDM (wavelength division multiplexing) client fault) at an
      optical-layer device.  Second, fault occurs at an optical-layer
      device will further cause fault (e.g., L3 link down) at a packet-
      layer device.  Third, fault occurs at the inter-layer link between a

      packet-layer device and an optical-layer device will further cause
      faults at both devices.  Traditionally, multiple operation teams are
      needed to first analyze huge amount of alarms (triggered by the above
      mentioned faults) from single network layer independently, then
      cooperate to locate the root cause through manually analyzing multi-
      layer topology data and service data, thus fault demarcation becomes
      more complex and time-consuming in multi-layer scenario than in
      single-layer scenario.

      With the help of incident management, the management systems first
      automatically analyze root cause of the alarms at each single network
      layer and report corresponding incidents to the upper layer, then
      multi-layer management system comprehensively analyzes the topology
      relationship and service relationship between the root causes of both
      layers.  The inner relationship among the alarms will be identified
      and finally the root cause will be located among multiple layers.  By
      cooperating with the integrated Optical time-domain reflectometer
      (OTDR) within the network device, we can determine the target optical
      exchange station before site visits.  Therefore, the overall fault
      demarcation process is simplified and automated, the analyze result
      could be reported and visualized in time.  In this case, operation
      teams only have to confirm the analyze result and dispatch site
      engineers to perform relative maintenance actions (e.g., splice
      fiber) based on the root cause.

   ##  Security events Automated Noise reduction based on Situation awareness

      In the continuous data driven monitoring, tools used by the Security
      Operation Center (SoC) scan the network 24/7 to flag any
      abnormalities or suspicious activities.  As the SoC adds more tools
      for security events detection, the volume of security events or
      alerts grows continually. the overwhelming number of threat alerts
      can cause threat fatigue.  In addition, many of these alerts do not
      provide sufficient intelligence, context to investigate, or are false
      positives.  False positives not only drain time and resources, but
      can also distract teams from real incidents.

      With the help of the incident management, BERT(Bidirectional Encoder
      Representations from Transformers) {{BERT}} classifier can be adopted to
      analyses the suspicious activity and understands the significance of
      the gathered data (through both facts and inferences) and help
      operation and maintenance engineers focus on handling important
      security events and avoid wasting resources on false alerts, e.g.,
      automatically determine whether 10,000 network security events are
      real incidents and give reasonable explanations.  Progressively,
      Llama interpreter can be used to explain the reason why such alerts
      are picked out and marked significant, what could be the potential
      security implications that exist yet remain undiscovered.


# Incident Management Architecture
~~~~
                     +----------------------+-------------------+
                     |                                          |
                     |            Incident Management Client    |
                     |                                          |
                     |                                          |
                     +------------+---------+---------+---------+
                        ^         |         |         |
                        |Incident |Incident |Incident |Incident
                        |Report   |Ack      |Diagnose |Resolve
                        |         |         |         |
                        |         V         V         V
                     +--+-------------------+---------+----------+
                     |                                           |
                     |                                           |
                     |            Incident Management Server     |
                     |                                           |
                     |                                           |
                     |                                           |
                     |                                           |
                     +-------------------------------+-----------+
                           ^       ^Abnormal         ^
                           |Alarm  |Operations       |Metrics
                           |Report |Report           |/Telemetry
                           |       |                 V
              +------------+-------+-----------------+----------------+
              |                                                       |
              |                     Network                           |
              |                                                       |
              +------------------------------------+------------------+

~~~~
 {:#arch title="Network Incident Management Architecture" artwork-align="center"}

         {{arch}} illustrates the incident management architecture.  Two key
         components for the incident management are incident management client
         and incident management server.

         Incident management server can be deployed in network analytics
         platform, controllers and provides functionalities such as incident
         identification, report, diagnosis, resolution, querying for incident
         lifecycle management.

         Incident management client can be deployed in the network OSS or
         other business systems of operators and invokes the functionalities
         provided by incident management server to meet the business
         requirements of fault management.

         A typical workflow of incident management is as follows:

         *  Some alarms or abnormal operations, network performance metrics
            are reported from the network.  Incident management server
            receives these alarms/abnormal operations/metrics and try to
            analyze the correlation of them, if the incidents are identified,
            it will be reported to the client.  The impact of network services
            will be also analyzed and will update the incident.

         *  Incident management client receives the incident raised by server,
            and acknowledge it.  Client may invoke the "incident diagnose" rpc
            to diagnose this incident to find the root causes.

         *  If the root causes have been found, the client can resolve this
            incident by invoking the 'incident resolve' rpc operation,
            dispatching a ticket or using other functions (e.g. routing
            calculation,configuration)

## Interworking with Alarm Management

~~~~
            +-----------------------------+
            |         OSS                 |
            |+-------+      +-----------+ |
            ||alarm  |      | incident  | |
            ||handler|      |  client   | |
            |+-------+      +-----------+ |
            +---^---------------^---------+
                |               |
                |alarm          |incident
            +---|---------------|---------+
            |   |  controller   |         |
            |   |               |         |
            |+--+---++      +-----------+ |
            ||alarm  |      |           | |
            ||process+----->|  incident | |
            ||       |alarm |   server  | |
            |+------++      +-----------+ |
            |   ^              ^          |
            +---+--------------|----------+
                |alarm         | metrics/trace/etc.
                |              |
            +---+--------------+----------+
            |         network             |
            |                             |
            +-----------------------------+
~~~~
{:#alarm title="Interworking with alarm management" artwork-align="center"}

         YANG model for the alarm management {{?RFC8632}} defines a standard
         interface to manage the lifecycle of alarms.  Alarms represent the
         undesirable state of network resources, alarm data model also defines
         the root causes and impacted services fields, but there may lack
         sufficient information to determine them in lower layer system
         (mainly in devices level), so alarms do not always tell the status of
         services or the root causes.  As described in {{?RFC8632}}, alarm
         management act as a starting point for high-level fault management.
         While incident management often works at the network level, so it's
         possible to have enough information to perform correlation and
         service impact analysis.  Alarms can work as one of data sources of
         incident management and may be aggregated into few amount of
         incidents by correlation analysis, network service impact and root
         causes may be determined during incident process.

         Incident also contains some related alarms,if needed users can query
         the information of alarms by alarm management interface {{?RFC8632}}.
         In some cases, e.g. cutover scenario, incident server may use alarm
         management interface {{?RFC8632}} to shelve some alarms.

         Alarm management may keep the original process, alarms are reported
         from network to network controller or analytics and then reported to
         upper layer system(e.g.  OSS).  Upper layer system may store these
         alarms and provide the information for fault analysis (e.g. deeper
         analysis based on incident).

         Compared with alarm management, incident management provides not only
         incident reporting but also diagnosis and resolution functions, it's
         possible to support self-healing and may be helpful for single-domain
         closed-loop control.

         Incident management is not a substitute for alarm management.
         Instead, they can work together to implement fault management.

##  Interworking with SAIN
~~~~
                   +----------------+
                   | incident client|
                   +----------------+
                           ^
                           |incident
                   +-------+--------+
                   |incident server |
                   +----------------+
                           ^
                           |symptoms
                   +-------+--------+
                   |     SAIN       |
                   |                |
                   +----------------+
                           ^
                           |metrics
             +-------------+-------------+
             |                           |
             |         network           |
             |                           |
             +---------------------------+

~~~~
{:#sain title="Interworking with SAIN" artwork-align="center"}

         SAIN {{?RFC9417}} defines the architecture of network service assurance.
         A network service can be decomposed into some sub-services, and some
         metrics can be monitored for sub-services.  For example, a tunnel
         service can be decomposed into some peer tunnel interface sub-
         services and IP connectivity sub-service.  If some metrics are
         evaluated to indicate unhealthy for specific sub-service, some
         symptoms will be present.  Incident server may identify the incident
         based on symptoms, and then report it to upper layer system.  So,
         SAIN can be one way to identify incident, services, sub-services and
         metrics can be preconfigured via APIs defined by service assurance
         YANG model {{?RFC9418}} and incident will be reported if symptoms match
         the condition of incident.

##  Relationship with RFC8969

         {{?RFC8969}} defines a framework for network automation using YANG, this
         framework breaks down YANG modules into three layers, service layer,
         network layer and device layer, and contains service deployment,
         service optimization/assurance, and service diagnosis.  Incident
         works at the network layer and aggregates alarms, metrics and other
         information from device layer, it's helpful to provfide service
         assurance.  And the incident diagnosis may be one way of service
         diagnosis.

##  Relationship with Trace Context

         W3C defines a common trace context{{W3C-Trace-Context}} for distributed
         system tracing, {{?I-D.rogaglia-netconf-trace-ctx-extension}} defines a
         netconf extension for {{W3C-Trace-Context}} and
         {{?I-D.quilbeuf-opsawg-configuration-tracing}} defines a mechanism for
         configuration tracing.  If some errors occur when services are
         deploying, it's very easy to identify these errors by distributed
         system tracing, and an incident should be reported.

# Functional Interface Requirements between the Client and the Server

## Incident Identification

~~~~
                    +--------------+
                 +--|  Incident1   |
                 |  +--+-----------+
                 |     |  +-----------+
                 |     +--+  alarm1   |
                 |     |  +-----------+
                 |     |
                 |     |  +-----------+
                 |     +--+  alarm2   |
                 |     |  +-----------+
                 |     |
                 |     |  +-----------+
                 |     +--+  alarm3   |
                 |        +-----------+
                 |  +--------------+
                 +--|  Incident2   |
                 |  +--+-----------+
                 |     |  +-----------+
                 |     +--+  metric1  |
                 |     |  +-----------+
                 |     |  +-----------+
                 |     +--+  metric2  |
                 |        +-----------+
                 |
                 |  +--------------+
                 +--|  Incident3   |
                    +--+-----------+
                       |  +-----------+
                       +--+ alarm1    |
                       |  +-----------+
                       |
                       |  +-----------+
                       +--| metric1   |
                          +-----------+
~~~~
{:#ident title="Incident Identification" artwork-align="center"}

            As described in {{ident}}, multiple alarms, metrics, or hybrid can be
            aggregated into an incident after analysis.

            The incident management server MUST be capable of identifying
            incidents.  Multiple alarms, metrics and other information are
            reported to incident server, and the server must analyze it and find
            out the correlations of them, if the correlation match the incident
            rules, incident will be identified and reported to the client.
            Service impact analysis will be performed if an indent is identified,
            and the content of incident will be updated if impacted network
            services are detected.

            AI/ML may be used to identify the incident.  Expert system and online
            learning can help AI to identify the correlation of alarms, metrics
            and other information by time-base correlation algorithm, topo-based
            correlation algorithm, etc.  For example, if interface is down, then
            many protocol alarms will be reported, AI will think these alarms
            have some correlations.  These correlations will be put into
            knowledge base, and the incident will be identified faster according
            to knowledge base next time.

            As mentioned above, SAIN is another way to implement incident
            identification.  Observation timestamp defined in
            {{?I-D.tgraf-yang-push-observation-time}} and trace context defined in
            {{W3C-Trace-Context}} may be helpful for incident identification.

~~~~
                                 +----------------------+
                                 |                      |
                                 |     Orchestrator     |
                                 |                      |
                                 +----+-----------------+
                                      ^VPN A Unavailable
                                      |
                                  +---+----------------+
                                  |                    |
                                  |     Controller     |
                                  |                    |
                                  |                    |
                                  +-+-+-+-----+--+-----+
                                  ^ ^            ^
                              IGP | |Interface   |IGP Peer
                             Down | |Down        | Abnormal
                                  | |            |
                 VPN A            | |            |
                +-----------------+-+------------+------------------*
                | \  +---+       ++-++         +-+-+        +---+  /|
                |  \ |   |       |   |         |   |        |   | / |
                |   \|PE1+-------| P1+X--------|P2 +--------|PE2|/  |
                |    +---+       +---+         +---+        +---+   |
                +---------------------------------------------------+
~~~~
{:#exam1 title="Example 1 of Incident Identification" artwork-align="center"}

            As described in {{exam1}}, vpn a is deployed from PE1 to PE2, if a
            interface of P1 is going down, many alarms are triggered, such as
            interface down, igp down, and igp peer abnormal from P2.  These
            alarms are aggregated and analyzed by controller, and the incident
            'vpn unavailable' is triggered by the controller.


~~~~

                                +----------------------+
                                |                      |
                                |     Orchestrator     |
                                |                      |
                                +----+-----------------+
                                     ^VPN A Degradation
                                     |
                                 +---+----------------+
                                 |                    |
                                 |     controller     |
                                 |                    |
                                 |                    |
                                 +-+-+-+-----+--+-----+
                                   ^            ^
                                   |Packet      |Path Delay
                                   |Loss        |
                                   |            |
                VPN A              |            |
               +-------------------+------------+-------------------+
               | \  +---+       ++-++         +-+-+        +---+  / |
               |  \ |   |       |   |         |   |        |   | /  |
               |   \|PE1+-------|P1 +---------|P2 +--------|PE2|/   |
               |    +---+       +---+         +---+        +---+    |
               +----------------------------------------------------+
~~~~
{:#exam2 title="Example 2 of Incident Identification" artwork-align="center"}

            As described in {{exam2}}, controller collect the network metrics from
            network elements, it finds the packet loss of P1 and the path delay
            of P2 exceed the thresholds, an incident 'VPN A degradation' may be
            triggered after service impact analysis.

## Incident Diagnosis

            After an incident is reported to the incident management client, the
            client MAY diagnose the incident to determine the root cause.  Some
            diagnosis operations may affect the running network services.  The
            client can choose not to perform that diagnosis operation after
            determining the impact is not trivial.  The incident management
            server can also perform self-diagnosis.  However, the self-diagnosis
            MUST not affect the running network services.  Possible diagnosis
            methods include link reachability detection, link quality detection,
            alarm/log analysis, and short-term fine-grained monitoring of network
            quality metrics, etc.

## Incident Resolution

            After the root cause is diagnosed, the client MAY resolve the
            incident.  The client MAY choose resolve the incident by invoking
            other functions, such as routing calculation function, configuration
            function, dispatching a ticket or asking the server to resolve it.
            Generally, the client would attempt to directly resolve the root
            cause.  If the root cause cannot be resolved, an alternative solution
            SHOULD be required.  For example, if an incident caused by a physical
            component failure, it cannot be automatically resolved, the standby
            link can be used to bypass the faulty component.

            Incident server will monitor the status of incident, if the faults
            are fixed, the server will update the status of incident to
            'cleared', and report the updated incident to the client.

            Incident resolution may affect the running network services.  The
            client can choose not to perform those operations after determining
            the impact is not trivial.

# Incident Data Model Concepts

## Identifying the Incident Instance

               An incident ID is used as an identifier of an incident instance, if
               an incident instance is identified, a new incident ID is created.
               The incident ID MUST be unique in the whole system.

## The Incident Lifecycle

### Incident Instance Lifecycle

               From an incident instance perspective, an incident can have the
               following lifecycle: 'raised', 'updated', 'cleared'.  When an
               incident is generated, the status is 'raised'.  If the status changes
               after the incident is generated, (for example, self-diagnosis,
               diagnosis command issued by the client, or any other condition causes
               the status to change but does not reach the 'cleared' level.) , the
               status changes to 'updated'.  When an incident is successfully
               resolved, the status changes to 'cleared'.

###  Operator Incident Lifecycle

               From an operator perspective, the lifecycle of an incident instance
               includes 'acknowledged', 'diagnosed', and 'resolved'.  When an
               incident instance is generated, the operator SHOULD acknowledge the
               incident.  And then the operator attempts to diagnose the incident
               (for example, find out the root cause and affected components).
               Diagnosis is not mandatory.  If the root cause and affected
               components are known when the incident is generated, diagnosis is not
               required.  After locating the root cause and affected components,
               operator can try to resolve the incident.

# Incident Data Model Design

## Overview

                  There are two YANG modules in the model.  The first module, "ietf-
                  incident-type", provides common definitions such as incident domain,
                  incident category, incident priority.  The second module, "ietf-
                  incident", defines technology independent abstraction of network
                  incident construct for alarm, log, performance metrics, etc.  The
                  information reported in the incident include Root cause,
                  priority,impact, suggestion, etc.  At the top of "ietf-incident"
                  module is the Network Incident.  Network incident is represented as a
                  list and indexed by "incident-id".  Each Network Incident is
                  associated with a service instance, domain and sources.  Under
                  sources, there is one or more sources.  Each source corresponds to
                  node defined in the network topology model and network resource in
                  the network device,e.g., interface.  In addition, "ietf-incident"
                  support one general notification to report incident state changes and
                  three rpcs to manage the network incidents.

~~~~
                  module: ietf-incident
                    +--ro incidents
                       +--ro incident* [incident-id]
                          +--ro incident-id string
                          +--ro csn? uint64
                          +--ro service-instance* string
                          +--ro name? string
                          +--ro type? enumeration
                          +--ro domain? identityref
                          +--ro priority? int:incident-priority
                          +--ro status? enumeration
                          +--ro ack-status? enumeration
                          +--ro category? identityref
                          +--ro detail? string
                          +--ro resolve-advice? string
                          +--ro sources
                          ...
                          +--ro root-causes
                          ...
                          +--ro root-events
                          ...
                          +--ro events
                          ...
                          +--ro raise-time? yang:date-and-time
                          +--ro occur-time? yang:date-and-time
                          +--ro clear-time? yang:date-and-time
                          +--ro ack-time? yang:date-and-time
                          +--ro last-updated? yang:date-and-time
                    rpcs:
                      +---x incident-acknowledge
                      ...
                      +---x incident-diagnose
                      ...
                      +---x incident-resolve

                    notifications:
                      +---n incident-notification
                         +--ro incident-id?
                                 -> /inc:incidents/inc:incident/inc:incident-id
                         ...
                         +--ro time? yang:date-and-time
~~~~

## Incident Notifications

~~~~
                    notifications:
                      +---n incident-notification
                         +--ro incident-id?
                                 -> /inc:incidents/inc:incident/inc:incident-id
                         +--ro csn? uint64
                         +--ro service-instance* string
                         +--ro name? string
                         +--ro type? enumeration
                         +--ro domain? identityref
                         +--ro priority? int:incident-priority
                         +--ro status? enumeration
                         +--ro ack-status? enumeration
                         +--ro category? identityref
                         +--ro detail? string
                         +--ro resolve-advice? string
                         +--ro sources
                         |  +--ro source* [node]
                         |     +--ro node -> /nw:networks/nw:network/nw:node/nw:node-id
                         |     +--ro resource* [name]
                         |        +--ro name al:resource
                         +--ro root-causes
                         |  +--ro root-cause* [node]
                         |     +--ro node -> /nw:networks/nw:network/nw:node/nw:node-id
                         |     +--ro resource* [name]
                         |     |  +--ro name al:resource
                         |     |  +--ro cause-name? string
                         |     |  +--ro detail? string
                         |     +--ro cause-name? string
                         |     +--ro detail? string
                         +--ro root-events
                         |  +--ro root-event* [type event-id]
                         |     +--ro type -> ../../../events/event/type
                         |     +--ro event-id leafref
                         +--ro events
                         |  +--ro event* [type event-id]
                         |     +--ro type enumeration
                         |     +--ro event-id string
                         |     +--ro (event-type-info)?
                         |        +--:(alarm)
                         |        |  +--ro alarm
                         |        |     +--ro resource? leafref
                         |        |     +--ro alarm-type-id? leafref
                         |        |     +--ro alarm-type-qualifier? leafref
                         |        +--:(notification)
                         |        +--:(log)
                         |        +--:(KPI)
                         |        +--:(unknown)
                         +--ro time? yang:date-and-time
~~~~

                  A general notification, incident-notification, is provided here.
                  When an incident instance is identified, the notification will be
                  sent.  After a notification is generated, if the incident management
                  server performs self diagnosis or the client uses the interfaces
                  provided by the incident management server to deliver diagnosis and
                  resolution actions, the notification update behavior is triggered,
                  for example, the root cause objects and affected objects are updated.
                  When an incident is successfully resolved, the status of the incident
                  would be set to 'cleared'.

## Incident Acknowledge

~~~~
                      +---x incident-acknowledge
                      |  +---w input
                      |  |  +---w incident-id*
                      |  |          -> /inc:incidents/inc:incident/inc:incident-id
~~~~
                  After an incident is generated, updated, or cleared, (In some
                  scenarios where automatic diagnosis and resolution are supported, the
                  status of an incident may be updated multiple times or even
                  automatically resolved.)  The operator needs to confirm the incident
                  to ensure that the client knows the incident.

                  The incident-acknowledge rpc can confirm multiple incidents at a time

##  Incident Diagnose

~~~~
                      +---x incident-diagnose
                      |  +---w input
                      |  |  +---w incident-id*
                      |  |          -> /inc:incidents/inc:incident/inc:incident-id
~~~~
                  After an incident is generated, incident diagnose rpc can be used to
                  diagnose the incident and locate the root causes.  Diagnosis can be
                  performed on some detection tasks, such as BFD detection, flow
                  detection, telemetry collection, short-term threshold alarm,
                  configuration error check, or test packet injection.

                  After the diagnosis is performed, a incident update notification will
                  be triggered to report the latest status of the incident.

## Incident Resolution

~~~~
                      +---x incident-resolve
                         +---w input
                         |  +---w incident-id*
                         |          -> /inc:incidents/inc:incident/inc:incident-id
~~~~

                  After the root causes and impacts are determined, incident-resolve
                  rpc can be used to resolve the incident (if the server can resolve
                  it).  How to resolve an incident instance is out of the scope of this
                  document.

                  Incident resolve rpc allows multiple incident instances to be
                  resolved at a time.  If an incident instance is successfully
                  resolved, a notification will be triggered to update the incident
                  status to 'cleared'.  If the incident content is changed during this
                  process, a notification update will be triggered.

# Incident Management YANG Module

~~~~
<CODE BEGINS> file "ietf-incident-types@2023-05-16.yang"
{::include-fold ./yang/ietf-incident-types.yang}
<CODE ENDS>
~~~~

~~~~
<CODE BEGINS> file "ietf-incident@2023-05-16.yang"
{::include-fold ./yang/ietf-incident.yang}
<CODE ENDS>
~~~~


# Security Considerations

The YANG modules specified in this document define a schema for data
that is designed to be accessed via network management protocol such
as NETCONF {{!RFC6241}} or RESTCONF {{!RFC8040}}.  The lowest NETCONF layer
is the secure transport layer, and the mandatory-to-implement secure
transport is Secure Shell (SSH) {{!RFC6242}}.  The lowest RESTCONF layer
is HTTPS, and the mandatory-to-implement secure transport is TLS
{{!RFC8446}}.

The Network Configuration Access Control Model (NACM) {{!RFC8341}}
provides the means to restrict access for particular NETCONF or
RESTCONF users to a preconfigured subset of all available NETCONF or
RESTCONF protocol operations and content.

There are a number of data nodes defined in this YANG module that are
writable/creatable/deletable (i.e., config true, which is the
default).  These data nodes may be considered sensitive or vulnerable
in some network environments.  Write operations (e.g., edit-config)
to these data nodes without proper protection can have a negative
effect on network operations.  These are the subtrees and data nodes
and their sensitivity/vulnerability:

Some of the readable data nodes in this YANG module may be considered
sensitive or vulnerable in some network environments.  It is thus
important to control read access (e.g., via get, get-config, or
notification) to these data nodes.  These are the subtrees and data
nodes and their sensitivity/vulnerability:

Some of the RPC operations in this YANG module may be considered
sensitive or vulnerable in some network environments.  It is thus
important to control access to these operations.  These are the
operations and their sensitivity/vulnerability:



# IANA Considerations

## The "IETF XML" Registry

   This document registers one XML namespace URN in the 'IETF XML
   registry', following the format defined in {{!RFC3688}}.

   URI: urn:ietf:params:xml:ns:yang:ietf-incident
   Registrant Contact: The IESG.
   XML: N/A, the requested URIs are XML namespaces.

##  The "YANG Module Names" Registry

   This document registers one module name in the 'YANG Module Names'
   registry, defined in {{!RFC6020}}.

   name: ietf-incident
   prefix: inc
   namespace: urn:ietf:params:xml:ns:yang:ietf-incident
   RFC: XXXX
   // RFC Ed.: replace XXXX and remove this comment


--- back

# Acknowledgments
{:numbered="false"}

The authors would like to thank Mohamed Boucadair, Robert Wilton,
Benoit Claise, Oscar Gonzalez de Dios, Adrian Farrel, Mahesh
Jethanandani, Balazs Lengyel, Bo Wu, Qiufang Ma, Haomian Zheng,
YuanYao for their valuable comments and great input to this work.