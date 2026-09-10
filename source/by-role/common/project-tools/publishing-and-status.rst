:is-up-to-date: True
:last-updated: 5.0.0

.. index:: Publishing Status, Bulk Publish, Publish by Commit Id

.. meta::
   :description: Manage CrafterCMS publishing: view status, bulk publish by path, publish by commit/tag ID, publish everything, and monitor the publishing queue.
   :keywords: publishing, publishing status, bulk publish, publish by commit, publishing queue, Crafter Studio, project tools, Crafter Deployer

.. _publishing-and-status:

=================
Publishing Status
=================
The **Publishing** tool under ``Project Tools`` allows the user to view the publishing status,
publish multiple items from a specified path, publish commits from sandbox repository by commit id or tag and
view the publishing queue.

.. _publishing-status:

------
Status
------
The **Status** section allows the user to view the publishing status, such as the following:

- The publisher was stopped by an administrator.
- The publisher was stopped due to an error.

It also allows the user to **Start** or **Stop** Studio publishing

.. image:: /_static/images/site-admin/project-tools-publish-status.webp
    :alt: Project Tools Publishing - Status
	:align: center

|

|hr|

-----------------
Publish on Demand
-----------------
The **Publish on Demand** section allows the user to publish changes made in Studio via the UI and
to publish commits from sandbox repository by commit id.

.. image:: /_static/images/site-admin/project-tools-publish-on-demand.webp
    :alt: Project Tools Publishing - Publish on Demand
	:align: center

|

The user will first be asked to publish the entire project, if the project has not yet been published:

.. image:: /_static/images/site-admin/project-tools-publish-on-demand-initial.webp
    :alt: Project Tools Publishing - Initial Publish
	:align: center

|

.. image:: /_static/images/site-admin/project-tools-publish-on-demand-initial-detail.webp
    :alt: Project Tools Publishing - Initial Publish details
	:align: center

|

^^^^^^^^^^^^^^^
Publish by path
^^^^^^^^^^^^^^^
The **Publish changes made in Studio via the UI** selection allows the user to publish multiple items under a specified path. This should be used to publish changes made in Studio via the UI.

For example, using the Website Editorial blueprint, to publish all the articles in the year 2021, in the **Path to Publish** field, enter ``/site/website/articles/2021``. Fill in the ``Package Title`` and ``Submission Comment`` and choose the ``Publishing Target``, then click on the ``Publish`` button.

.. image:: /_static/images/site-admin/project-tools-publish-path.webp
    :alt: Project Tools Publishing - Publish changes made in Studio via the UI
	:align: center

|

^^^^^^^^^^^^^^^^^^^^^^^^^^^
Publish by Commit/tag ID(s)
^^^^^^^^^^^^^^^^^^^^^^^^^^^
The **Publish changes made via direct git actions against the repository or pulled from a remote repository** allows the user to publish by tags or commits ids

.. image:: /_static/images/site-admin/project-tools-publish-commit-id.webp
    :alt: Project Tools Publishing - Publish by Commit ID(s)
	:align: center

|

   .. include:: /includes/git-changes-note.rst

|

To publish by commit id, let's use a project created using the Website Editorial blueprint and follow the steps below:

- Edit the Home page (/site/website/index.xm) from the command line or anywhere other than Studio
- From the command line, commit your changes

  .. code-block:: bash

     $ cd crafter-authoring/data/repos/sites/my-editorial/sandbox/site/website
     $ git add .
     $ git commit

- Get the commit id after doing the above step

  .. code-block:: bash

     $ git log
     commit f47c9a5bae4184e7b5ff2cb03b90b8ff86adec37 (HEAD -> master)
     Author: myuser <myuser@example.com>
     Date:   Fri Mar 15 10:09:57 2021 -0400

         edited home page outside of studio

     commit 1121d1b90d7b3131025932b6a0f0269d918caa11
     Author: admin admin <evaladmin@example.com>
     Date:   Fri Mar 15 09:31:57 2021 -0400

  The commit id we want to publish by commit id is ``f47c9a5bae4184e7b5ff2cb03b90b8ff86adec37``

- Go back to Studio and click on ``Project Tools`` -> ``Publishing``
- Scroll down to the ``Publish on Demand`` section
- Select the ``Publish changes made via direct git actions against the repository or pulled from a remote repository`` radio button
- Paste the commit id from the earlier step, fill in the ``Package Title`` and ``Submission Comment``, select the ``Publishing Target``, then click the ``Publish`` button

.. _publish-everything:

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Publish all changes on the repo
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. version_tag::
   :label: Since
   :version: 4.0.2

The **Publish everything** selection allows the user to publish all changes on the repo to the publishing
target you choose.

For example, using the Website Editorial blueprint, to publish all the changes made in the sandbox repo,
simply select ``Publish everything`` and then fill in the ``Package Title`` and ``Submission Comment`` and choose the
``Publishing Target``. Remember to check the box confirming that you understand the entire site will be published.
Finally click on the ``Publish`` button.


.. image:: /_static/images/site-admin/project-tools-publish-bulk.webp
   :alt: Project Tools Publishing - Publish changes made in Studio via the UI
   :align: center

|

.. _publishing-queue:

----------------
Publishing Queue
----------------
The **Publishing Queue** tab allows the user to see the items(publishing packages) that are in the queue for publishing.

.. image:: /_static/images/site-admin/project-tools-publish-queue.webp
    :alt: Project Tools Publishing - Publishing Queue
	:align: center

|

**Publishing packages** contain the following information:

* ``Package Title``: title for the publishing package
* ``Publishing Target``: publishing package destination
* ``Submitter``:  user that sent/requested publishing package
* ``Submission Comment``: submission comment for publishing package
* ``Status``: status of the publishing package and depending on the status, may contain the following:

  - ``Reviewer``: user that reviewed and approved/rejected the package
  - ``Approved`` / ``Rejected``: whether the package is approved/rejected
  - ``Comment`` / ``Reason``: approval comment provided/ rejection reason provided

* ``Submitted On``: date package was submitted for publishing
* ``Scheduled For``: schedule for publishing the package
* A list of file(s) contained in the publishing package with the following information:

    * The content title
    * The content path


.. image:: /_static/images/site-admin/project-tools-publishing-package.webp
    :alt: Project Tools Publishing - Publishing Package
	:align: center

|

You can filter the publishing queue displayed, based on the following:

* ``Publishing Target`` (a list of all available targets)
* ``State`` of the publishing packages

  The following are the applicable states for publishing packages.

    * **Ready for Live**: The package is ready to be published (this is conditional on package approvalState)
    * **Processing**: The package is currently being processed
    * **Live Success**: The package has been published to live target
    * **Live Completed with Errors**: The package has been partially published to live target due to errors
    * **Live Failed**: The package failed to be published to live target
    * **Staging Success**: The package has been published to staging
    * **Staging Completed with Errors**: The package has been partially published to staging target due to errors
    * **Staging Failed**: The package failed to be published to staging target
    * **Completed**: The package has been completed in at least one target
    * **Cancelled**: The package has been cancelled


.. image:: /_static/images/site-admin/project-tools-publish-queue-filter.webp
    :alt: Project Tools Publishing - Publishing Queue Filters
    :width: 35 %
    :align: center

|

Filtering shows packages where one or more items in the package match. The default filter is set for items that are in State ``READY_FOR_LIVE`` and Publishing Target ``All``  which will show a list of packages that are ready to go live.

Below, we have the filter set to display items in the ``COMPLETED`` state

.. image:: /_static/images/site-admin/project-tools-publish-queue-filter-completed.webp
    :alt: Project Tools Publishing - Publishing Queue Filter Completed
    :align: center

|

Packages in the ``READY_FOR_LIVE`` state can be selected and the publishing package cancelled. In the image below, we have two publishing packages selected with the ``Cancel Selected`` button clicked and confirming if the user wants to set the state of the selected publishing packages to state ``CANCELLED``

.. image:: /_static/images/site-admin/project-tools-publish-queue-filter-cancel.webp
    :alt: Project Tools Publishing - Publishing Queue Filter Completed
    :align: center

|