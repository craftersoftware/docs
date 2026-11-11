:is-up-to-date: True
:last-updated: 4.4.4

.. meta::
    :description lang=en:
        Documentation listing the frequently asked questions about setting up, running and using CrafterCMS.

===
FAQ
===
--------------------------------
What do I need to run CrafterCMS
--------------------------------
Follow these instructions to run CrafterCMS and to download and install Crafter on your machine or server: :ref:`Quick Start Guide <getting-started>`

------------------------------------------------------------------------------------------------------------------
Does CrafterCMS require Git?  My company uses its own source code control system (GitHub, GitLab, Bitbucket, etc.)
------------------------------------------------------------------------------------------------------------------
No. CrafterCMS does not require your development team to use Git. CrafterCMS employs a Git-based repository and publishing system, which comes embedded in every CrafterCMS. Using Crafter's Git-based repository for your content application's source code is optional. You may continue to use the Source Code Management System of your choice for development.

----------------------------------------------------------------------------------------------------
Can we use my company's source code control system with CrafterCMS (GitHub, GitLab, Bitbucket, etc.)
----------------------------------------------------------------------------------------------------
Yes. CrafterCMS works with any Git service provider, and there are a number of benefits to doing that. :ref:`DevContentOps` is easier with these services, and that facilitates faster and easier development team collaboration.

-------------------------------
Do my authors need to know Git?
-------------------------------
No. Authors do not need to know or work directly with Git at all in order to use CrafterCMS.

Authoring activities such as creating or editing content, uploading images, participating in workflow and publishing are performed via Crafter Studio, a web-based authoring tool. Crafter Studio sits on top of a headless Git-based repository and publishing system. While content authors are performing their work via Crafter Studio, Crafter is managing all of the Git mechanics for them on their behalf behind the scenes.

----------------------------------------------------
Do I need to run MongoDB in order to run CrafterCMS?
----------------------------------------------------
No. MongoDB is not part of the core CMS. Core CrafterCMS (Studio, Engine, Deployer, and Search) does not require MongoDB.

MongoDB is used only by two optional enterprise modules:

* Crafter Profile |enterpriseOnly|, which provides identity management, a distributed user attribute store, and advanced targeting capabilities.
* Crafter Social |enterpriseOnly|, which provides support for User Generated Content (UGC).

You only need MongoDB if you run those modules.

----------------------------------------------
Do I need to connect CrafterCMS to a database?
----------------------------------------------
No. Crafter Studio stores content in Git, not a database. It uses a small internal database for managing users and workflow. This database does not require a DBA to manage it and it is not pluggable. Crafter Engine is the delivery server and does not require a database at all. You can integrate with any database to support your specific delivery application use cases. Any common database is supported in this capacity. Crafter Deployer is the deployment agent and does not require a database at all, but can be used to push content to external systems like a database upon publish. See :ref:`crafter-deployer-processors-guide` for more information.

Crafter Social |enterpriseOnly| and Crafter Profile |enterpriseOnly| use MongoDB. These are optional enterprise modules, not part of the core CMS.

-----------------------------------
Can CrafterCMS be used as a portal?
-----------------------------------
Yes. CrafterCMS is running portals for some of the most well known companies in the world. Use cases include intranets, global extranets, B2B portals, B2E portals and B2C portals in hospitality, finance, logistics, education and telecom and many others.

Many people hear the word portal and immediately search for a "portal" technology that meets the JSR-268 standard. CrafterCMS does not support JSR-268 natively. We do integrate with Liferay Portal and have been deployed in cases where stronger WCM capabilities were required than what Liferay provides out of the box.

You only need JSR-268 if you want to build and deploy your application as "portlets."  Doing so certainly has some advantages like the ability to leverage different application stacks for different applications in the portal. However, from an IT management perspective, unless there is good justification for different technology, doing so creates issues with technology management and total cost of ownership.


--------------------------------------
Can CrafterCMS be used for E-Commerce?
--------------------------------------
Yes. CrafterCMS helps build great e-commerce sites which gives marketing total control of the user experience while integrating with the back-end e-commerce functionality of your choosing.

-------------------------------------------------
Can CrafterCMS be used to build a marketing site?
-------------------------------------------------
Yes. Marketing sites are one of the top use cases for CrafterCMS. CrafterCMS gives marketers powerful tools to create and deploy dynamic, personalized experiences for users. Crafter also integrates with sales and marketing tools such as Salesforce, Marketo, Hubspot, Omniture, Google Analytics, iContact and many others.

----------------------------------------------------
How long does it take to build a CrafterCMS website?
----------------------------------------------------
Project duration depends on complexity of the website or other digital experience you are building. A project might be as short as a day or it could take 9 months to complete. Typical real-world sites with a sophisticated design, personalization and back end integration will take between 3 and 6 months to complete.

Crafter's tools enable an agile development methodology. Many organizations create and deploy a phase one project quickly and then leveraging Crafter's capabilities quickly iterate to deliver continuous value in the form of new features to the site on a regular cadence.

----------------------------------
How do I contribute to CrafterCMS?
----------------------------------
You can find out more about contributing bug reports, code, documentations and tests here: :ref:`contribute`

------------------------------------
What's CrafterCMS's licensing terms?
------------------------------------
* CrafterCMS, the open source project is licensed under the GPLv3 Open Source license.
* Customers who purchase CrafterCMS's support subscription receive a commercial license that alleviates them from the terms of the open source license.
