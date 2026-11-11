:is-up-to-date: True
:last-updated: 4.1.0

.. meta::
   :description: CrafterCMS architecture overview: decoupled CMS, headless, composable, deployment, scalability, security, and availability for enterprise architects.
   :keywords: CrafterCMS architecture, headless CMS, decoupled CMS, composable, scalability, deployment, Crafter Studio, Crafter Engine

.. _architect:

==========
Architects
==========
This section details key concepts for CrafterCMS experience architects

.. _general-architecture:

--------------------
General Architecture
--------------------
CrafterCMS distinguishes itself by its modern architecture, which in turn enables:

* Truly decoupled content management (content authoring decoupled from content delivery), composed of independent microservice-based components
* Dynamic, personalized content delivery of every request at speed
* API-first headless CMS (content as a service via extensible GraphQL & REST)
* Git-based content repository (allows for excellent developer cadence and highly effective DevContentOps processes)
* Shared-nothing, serverless, delivery architecture (planet-scale)
* Front-end agnostic (bring your favorite UI framework or use as a headless CMS)
* First-class support for all three CMS stakeholders: content authors, developers and system administrators (DevContentOps support)
* Build new apps or weave into existing apps and sites

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Modern Decoupled Architecture
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
CrafterCMS is a truly decoupled content management system, and it supports dynamic and personalized content
delivery. To understand this better, CrafterCMS's decoupled architecture is as follows:

.. image:: /_static/images/architecture/decoupled-overview.webp
    :width: 75%
    :alt: CrafterCMS Decoupled Overview
    :align: center

|

You'll note that CrafterCMS doesn't share a database between the authoring and delivery systems. Instead, the authoring system reduces the content to XML and static assets, and the delivery system rehydrates those for personalized, planet-scale, disconnected, and fast delivery.

""""""""""""""""""""""""""""""""""""
CrafterCMS Modules and Core Services
""""""""""""""""""""""""""""""""""""
CrafterCMS comprises a number of services that work together to provide a complete content management solution. These services are:

.. list-table:: CrafterCMS Modules
    :header-rows: 1
    :widths: 25 75

    * - Module
      - Description
    * - :ref:`Studio<crafter-studio>`
      - Studio provides all the content management services to enable authoring, management, and publishing of all content.
    * - :ref:`Engine<crafter-engine>`
      - Engine provides content delivery services to power any type of Web or mobile application. It is multi-tenant and can deliver many projects from a single instance.
    * - :ref:`Deployer<crafter-deployer>`
      - The content deployment system which moves content from Studio to Engine and Search.

.. image:: /_static/images/architecture/modules-overview.webp
    :width: 75%
    :alt: CrafterCMS Decoupled Overview
    :align: center

|

In contrast, many coupled CMSs do claim to be decoupled, but are really not. These systems allow you to have an authoring tier that's separate from the delivery tier; however, these are connected via a database sync. That means that the delivery tiers cannot run without some level of connectivity to the authoring master, and indeed has limits on scale of the delivery tier.

A truly decoupled system will support disconnected delivery (think of a delivery tier that's running in a submarine or on a cruise ship). While running disconnected delivery nodes is an extreme example, it's a good test of the true scalability of the delivery tier of a CMS.

.. image:: /_static/images/architecture/traditional-modern-decoupled.webp
    :width: 100%
    :alt: CrafterCMS Modern Decoupled
    :align: center

|

How can CrafterCMS deliver a dynamic experience? During ingestion, the delivery tier indexes the content into a local search engine and builds an in-memory representation of content items to help drive the dynamic behavior. The search engine and in-memory store are local and therefore share nothing with other nodes; however, you're now able to search and build dynamic responses. These dynamic responses can be driven by defined user personas along with user identity or prior user behavior. If you have a user store, behavior stream, or social media profile access, you can then tailor the content dynamically in real time.

^^^^^^^^^^^^^^^^^^^^^^^
Application Development
^^^^^^^^^^^^^^^^^^^^^^^
.. image:: /_static/images/architecture/application-development.webp
    :width: 100%
    :alt: Application Development on CrafterCMS
    :align: center

|

CrafterCMS is a flexible platform, and features:

* Content access via API (extensible GraphQL / REST)
* Extend GraphQL with your own schema and pull data from any external source to augment what's in the CMS
* Quickly build REST end-points with small snippets of Groovy
* Modern SPAs (Single Page Applications) support, like React, Angular, Vue
* Support for in-context editing via content edit pencils, drag-and-drop component management, etc.
* Server-side business logic developed in

  * Groovy/Java if backed by Crafter Engine
  * Any language if backed by an external service
* HTML5 sites
* Mobile applications and other headless use-cases

|hr|

.. _deployment-architecture:

-----------------------
Deployment Architecture
-----------------------
CrafterCMS supports various deployments (both SaaS solutions and PaaS and self-hosted solutions).

^^^^^^^^^^^^^
Crafter Cloud
^^^^^^^^^^^^^
Crafter Cloud provides a private SaaS offering of CrafterCMS. Crafter Cloud is:

* Fully elastically scalable (based on Kubernetes)
* High-availability with disaster recovery
* Fronted by a global CDN

See https://craftercms.com/products/crafter-cloud/ for more information.

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
On-Prem or IaaS/Cloud Providers
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. Fix to indicate you can run Kuber or traditional

Serverless deployment is supported via Docker, Kubernetes, and S3 or S3-compatible storage tier.
This allows for very fast scaling up/down and a true planet-scale deployment. Here's a typical
serverless deployment:

.. image:: /_static/images/architecture/serverless-deployment-architecture.webp
   :width: 80%
   :alt: CrafterCMS Serverless Deployment
   :align: center

|

If you're not quite ready for serverless deployment, CrafterCMS deploys quite nicely in a traditional
server/scale-group architecture. Here is a typical deployment at a high level:

.. image:: /_static/images/architecture/typical-deployment.webp
   :width: 100%
   :alt: CrafterCMS Typical Real-life Deployment
   :align: center


|hr|

.. _architect-composable:

----------
Composable
----------
CrafterCMS is a composable platform. This means that it is designed to allow you to compose your digital experiences from Packaged Business Capabilities (PBC) through a variety of different marketplace extensions, and extensions you develop yourself. This is in contrast to a monolithic platform that is designed to provide all of the services that you need to build your digital experiences.

Additionally, CrafterCMS provides the substrate for said composition, whereby the composed components can leverage the core platform services, inter-communicate, and share data and services.

Learn more about the composablity of CrafterCMS :ref:`in the developer's composable section <composable>`.

|hr|

.. _architect-headless:

--------
Headless
--------
CrafterCMS is an API-first CMS, and therefore it's natively headless. This is in stark contrast to traditional CMSs
that became headless with the addition of an API that tries to translate page-oriented data structures into something
more general.

Fundamentally, CrafterCMS simply retrieves content that the developer models in the modeling tool and applies actions/rules
like inheritance, security, versioning, etc., and returns the transformed content to the caller as JSON.

This means you can model any type of content, provide your content authors the ability to visually author content items
and then retrieve that content for your SPA, iOS, Android, or other applications.

Modeling, managing, and retrieving content may be enough for many simple use cases, there are other more advanced
considerations that CrafterCMS supports. Considerations include:

* In-context and in-place editing of headless content (Headless+)
* Workflow of authored content from the first edit, through staging, and finally to a live state
* :ref:`DevContentOps` and the flow of code, content, and team cadence

|hr|

.. _ architect-templated:

---------
Templated
---------
While CrafterCMS is an API-first CMS, it also has first-class support for templated (page-based) sites. CrafterCMS embeds
FreeMarker provides a high-performance, clean, flexible, and tolerant of syntax variance, templating engine
to render HTML directly from CrafterCMS.

CrafterCMS allows developers to model the content as general reusable items, and fold those into pages. Pages aggregate
content from components as needed and are associated with a FreeMarker template that can render the final page.
The choice of HTML tools and frameworks doesn't matter to CrafterCMS. Developers can use whatever front-end technology
they want. For development React, Vue, Angular, Flutter, or similar SPA, please see section :ref:`architect-headless`.

The use of templated sites as opposed to Headless sites doesn't detract from CrafterCMS' full support of building
custom APIs. CrafterCMS allows developers to quickly drop a Groovy file that becomes a server-side API
and/or REST endpoint. The project being developed can then invoke this API call from FreeMarker or as a REST API call.

Given the freedom available to developers in creating their HTML, CSS, and JS from scratch, concerns like Responsive
Design is a non-issue for CrafterCMS. Whatever developers can build in HTML CrafterCMS can happily render, and can
augment with in-context and in-place editing for content authors.

Features like workflow, review and approval processes, staging, and final go-live are of course all built-in.

Finally, given that CrafterCMS's content repository is built on Git, full DevContentOps support is native and fully supports templated sites. Learn more in the article :ref:`devcontentops`.

|hr|

.. _architect-general-concepts:

----------------
General Concepts
----------------
^^^^^^^^^^^^
Content APIs
^^^^^^^^^^^^
.. include:: /includes/content-retrieval-apis.rst

^^^^^^^^^^^^^^^^^^^
Content Inheritance
^^^^^^^^^^^^^^^^^^^
Having content authors enter the same meta-data/content for every content item where it doesn't change is both
laborious and wasteful. It's best for common meta-data for a section or the whole application to be entered once
and inherited by all child items. This is content inheritance, and the implementation in CrafterCMS is very
powerful supporting many inheritance mechanisms. More on this in the article :ref:`content-inheritance`.

^^^^^^^^
Security
^^^^^^^^
Securing content access and providing role-based access to different content items or hierarchies is critical to any
enterprise-grade content-rich application. It's critical that the security be implemented at the content API level.
CrafterCMS provides enterprise-grade authentication and authorization mechanics to help achieve this.
More on this :ref:`in the Product Security section <architect-security>`.

""""""""""""""
Authentication
""""""""""""""
CrafterCMS supports authentication integration with:

* SAML2 providers (see :ref:`engine-saml2-configuration` and :ref:`crafter-studio-configure-studio-saml`)
* :ref:`Studio LDAP Authentication <crafter-studio-configure-ldap>` , AD, ADFS
* Header-based providers (most SSO vendors) (see :ref:`Engine Headers Based Authentication <engine-headers-authentication>` and :ref:`Studio Headers Based Authentication <crafter-studio-configure-header-based-auth>`)

"""""""""""""
Authorization
"""""""""""""
CrafterCMS provides role-based access to all content items, per item, or per section/hierarchy/URL space, across
all APIs and it includes search.

^^^^^^^^^^^^^^^^^^
In-Context Editing
^^^^^^^^^^^^^^^^^^
CrafterCMS provides an SDK that lets developers focused on a headless use-case add the tools content authors expect,
like visual in-context and in-place editing of Web content regardless of the development platform and tools.

More information on Experience Builder in the :ref:`experience-builder` article and on the SDK at https://www.npmjs.com/package/@craftercms/experience-builder.

------------
Availability
------------
CrafterCMS supports geo-distributed deployments with multiple data centers per geography making the uptime of
the delivery tier near 100%. Traffic to the content delivery system flows to the nearest healthy data center providing
speed and availability.

Availability can be divided into High-Availability (HA) and Disaster Recovery (DR). Since CrafterCMS is two
separate subsystems, delivery, and authoring, then will describe HA and DR in the context of content delivery
and then in the context of content authoring.

^^^^^^^^
Delivery
^^^^^^^^
Content Delivery in CrafterCMS is based on the principles of Shared Nothing Architecture. This means delivery nodes
don't intercommunicate and are not backed by a single database or file system. As such, adding new nodes increases
reliability since the failure of one node doesn't result in a failure in content delivery.

Ideally, an installation would follow an N+1 delivery allocation, where N is the number of nodes required to handle
the delivery load and the extra node in case of a single node failure.

Distributing nodes across geographies (multi-region) means automatic Disaster Recovery is built-in. A failure of
a data center or several data centers in a region will not bring down content delivery.

^^^^^^^^^
Authoring
^^^^^^^^^
Content Authoring is the tier used by the few authors to generate content (XML, code, and static assets) to be
published to the delivery tier. As such, there are much less stringent availability requirements for the authoring
tier.

The authoring application has built-in primary/replica clustering. Being a stateful application, the clustering
is based on replicas following the primary with a fronting load-balancer that directs traffic to the primary
while the primary node is healthy. Fail-over is automatic with the replica becoming a primary and switching its
health status so the load-balancer directs traffic to it instead of the defunct primary.

Learn about clustering Crafter Studio in the :ref:`Crafter Studio Clustering Guide <studio-clustering>`.

|hr|

.. _architect-scalability:

-----------
Scalability
-----------
.. TODO Horizontal and Geo

Fundamental to scalability is:
- Horizontal scalability
- Minimizing bottlenecks
- Maximizing concurrency

With CrafterCMS, scalability is a fundamental part of the architecture. CrafterCMS has several core principles when
it comes to content delivery:
- Shared-nothing architecture
- Stateless and Cloud Native
- Minimal locking

Let's elaborate on the above.

CrafterCMS delivery nodes share nothing. They don't share a database, a file system, or memory, nor keep in sync with
one another. They're completely independent. Therefore, these nodes can be added arbitrarily and can be in different
data centers and in different geographies regardless of the latency between the nodes since they don't communicate
with one another.

CrafterCMS delivery nodes are also stateless and cloud-native. CrafterCMS delivery nodes run as a stateless set in
Kubernetes can pull content artifacts from remote storage for dynamic delivery.

Finally, CrafterCMS delivery nodes try to avoid locks to maximize thread throughput. This makes the system scale both,
vertically and horizontally very well.

^^^^^^^^^^^^
Planet-scale
^^^^^^^^^^^^
For global deployments where your users are spread across the planet, it's best to deploy CrafterCMS services
close to the users where possible. This guarantees fast, yet dynamic, responses to the users (CDNs are great,
but they won't help you with dynamic or personalized responses).

Because CrafterCMS is made up of two main systems: authoring (content authors work here), delivery (content consumers
consume from this tier), you can deploy different services in different regions depending on where the users are.

The authoring clusters are typically deployed closest to the content authors, whereas delivery clusters are spread
across geographies where you have end users. Delivery nodes can pull content from an arbitrary number of authoring
nodes and are typically deployed in geographies where your end users are.

.. image:: /_static/images/architecture/global-delivery.webp
    :width: 65%
    :alt: CrafterCMS Geo Distributed Deployment
    :align: center

|hr|

.. _architect-security:

----------------
Product Security
----------------
Developing secure software requires having policies and procedures and abiding by them. CrafterCMS maintains a set
of processes for its developers, machines, and facilities. This includes, but not limited to:

- Penetration testing and security audits per release
- Automated security scans using multiple tools
- Security researcher program and clear guidelines for reporting security issues
- CrafterCMS is a registered CNA and works closely with the security community to ensure timely and responsible
  disclosure of security issues.
- Secure development practices
- Secure deployment practices
- Secure operations practices

Learn more about CrafterCMS' security policies in the :ref:`security section <security>`.
