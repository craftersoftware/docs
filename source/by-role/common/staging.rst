:is-up-to-date: True
:last-updated: 5.0.0

.. meta::
   :description: Configure CrafterCMS staging publishing target to test content before going live, including enabling staging, delivery setup, and syncing with live.
   :keywords: staging, publishing target, CrafterCMS, staging environment, publish to staging, sync staging, Crafter Studio

.. _staging-env:

========================================
Configuring Publishing to Staging Target
========================================
CrafterCMS supports an intermediate publishing target, named ``staging``, where the project can be fully exercised with regards to system and integration points. This staging publishing target allows testing of your project. Once validated in staging, items can be pushed live. This ``staging`` publishing target precedes ``live``.

When staging is set up, the **Publish** dialog gives the user the option to select which publishing target to publish to:

.. image:: /_static/images/system-admin/staging-publish-option.webp
    :width: 75 %
    :align: center
    :alt: System Administrator - Staging Setup for Publish dialogs

|

Users will see **live** or **staging** underneath items indicating which publishing target an item has been/would be published to: ``Pending Approval``, ``Scheduled For Publish`` and ``Recently Published``

.. image:: /_static/images/system-admin/staging-dashboard.webp
    :width: 75 %
    :align: center
    :alt: System Administrator - Dashboard

|

Notice the pages published to staging and live with the same time in the above image. When staging is setup, all items will go through staging before going to live. So, if a user approves an item to be published directly to live, it will be published to staging first, then onto live.


------------------------------
Setting Up Staging per Project
------------------------------
""""""""""""""""
Enabling Staging
""""""""""""""""
Staging is setup per project and by default is not enabled. To enable staging for your project, click on |projectTools| from the **Sidebar**, then click on **Configuration** and select **Project Configuration** from the list.

Under the **<published-repository>** tags, set **<enable-staging-environment>** to ``true``.

   .. code-block:: xml
       :linenos:

       <published-repository>
            <enable-staging-environment>true</enable-staging-environment>
       </published-repository>

|

""""""""""""""""""""""
Setup Staging Delivery
""""""""""""""""""""""
To view your project with respect to the staging preview, we need to set it up for delivery. To set up your project, run the ``init-site`` script and follow the instructions in :ref:`setup-project-for-delivery` using the staging branch in the options. Remember to publish your project to the ``staging`` publishing target before running the ``init-site`` script.

    .. code-block:: bash

        ./init-site.sh -b staging mysite-staging /path/to/published/repo

.. include:: /includes/ssh-private-key.rst

|

-------------------------
Syncing Staging with Live
-------------------------
There are times when you need to sync the staging publishing target with the live publishing target. When adding the ``staging`` publishing target to an established project, the live repository is not cloned to the staging repository until an item has been published after setting up staging. There may be some other scenarios, when you might need to sync the staging publishing target with the live publishing target.

To sync the staging target with the live target, simply call the ``reset-staging`` API. See :base_url:`publishResetStaging <_static/api/studio.html#tag/publishing/operation/publishResetStaging>` for more details on the API

