:is-up-to-date: True
:last-updated: 4.1.0
:orphan:

.. index:: DevContentOps

.. meta::
   :description: Learn CrafterCMS DevContentOps processes for flowing code forward and content back across Dev, QA, and Prod environments using Git branching strategies.
   :keywords: DevContentOps, Git branching, code forward, content back, multi-environment, CrafterCMS, Crafter Studio, deployment workflow

.. _devcontentops:

=============
DevContentOps
=============

This document details clear and scalable DevContentOps processes for CrafterCMS. These processes allow a
team of software developers, content authors, and system administrators to work together on a single
project across multiple environments. Developers build new features and fix bugs by flowing their updates
from lower environments all the way to production. Authors focus on content creation, updates, and
publishing. System administrators and DevOps help orchestrate the flow of code from lower to higher
environments and content from higher to lower environments.

--------
Overview
--------
This section introduces key concepts and draws a broad outline of the detailed DevContentOps processes
that should be followed. CrafterCMS’s content authoring system, Crafter Studio, is built around an
embedded Git-based content repository, which enables these DevContentOps processes and when implemented
properly, provides significant productivity improvements.

^^^^^^^^^^^^
Code Forward
^^^^^^^^^^^^
Developers work on code, typically, on their local workstations. Code is tested there, and is then pushed
up to a development environment for collaboration with other developers.
Once code is judged ready for testing, it typically gets pushed up, again, to a QA environment where more
rigorous testing is performed by QA personnel.

After validation in QA, the code continues to move forward toward the production environment. Along the way,
and depending on the enterprise’s development policies, the code may pass through a number of environments
such as Load Testing, Staging, and/or User Acceptance Testing.

^^^^^^^^^^^^
Content Back
^^^^^^^^^^^^
Developers often greatly appreciate being able to work with actual production content in their development
environments, so that their code can be built and tested in a realistic context. When this is permissible,
flowing content from the production environment down to lower environments can avoid difficult problems
caused by the actual production content being different from developer-created content (based on its nature
or its scale).

Conversely, moving content from a lower environment to a higher one is not desired. Content is owned by
content authors, and content authors work almost exclusively in the production environment. Therefore,
content flow from lower environments to higher ones must be prevented.

.. _devcontentops-flow-diagram:

^^^^^^^^^^^^^^^^^^^^
Visualizing the Flow
^^^^^^^^^^^^^^^^^^^^
The diagram below depicts the flow of code and content through four environments. Code flows from a developer’s
workstation through Dev, QA, and finally Prod. Content flows back from Prod to QA, Dev, and finally to the
developer’s workstation.

The remainder of this document explains the flow illustrated below.

.. image:: /_static/images/developer/devcontentops.webp
    :alt: DevContentOps - Git Flow
    :width: 80 %
    :align: center

.. raw:: html

   <hr/>

--------------
Implementation
--------------
^^^^^^^^^^^^^^^^^
Code Forward Flow
^^^^^^^^^^^^^^^^^

For smaller installations or simpler feature development, code updates can happen entirely in the  production
environment: code edits made in Crafter Studio, validated using Studio’s In-ContextPreview, tested in Staging,
and finally published to the Live delivery environment.

For more complex features, the following guide describes how to flow code across 3 environments (and a local
ad-hoc environment for the developer working on the feature).

""""""""""""""""""""""""""
Understanding Environments
""""""""""""""""""""""""""
For the purpose of this document, an environment is defined as a CrafterCMS deployment serving the same projects
but with different users. For example, the most common is the following set of environments:

#. **Local**: A developer’s local machine
#. **Dev**: Common to all developers
#. **QA**: Quality assurance
#. **Prod**: Production/live

Code normally flows from 1 to 4 in the above example. In the sections below, we will describe a branching process
that facilitates this flow and also allows for content to flow back from 4 to 1.

""""""""""""""""""""""""""""""""""""""""""""""
Understanding the Repository (or Repositories)
""""""""""""""""""""""""""""""""""""""""""""""
There are two main approaches to maintaining a project’s Git repository across environments

#. A single upstream repository with many branches
#. One upstream repository per environment

The *single upstream repository* approach works well for most use cases and is the simpler approach. The *one
upstream repository* per environment approach, while more complex, might be a better fit when there are
substantial differences between environments in terms of repository size. For example, if the QA environment
has a very large set of test content, it may not be desirable to store that content in the same Git repository
as the production content, even if it is in its own branch. Separating this content into distinct repositories
addresses this problem.

For the single repository approach, the branches will look like the following

.. list-table::
    :widths: 14 43 43
    :header-rows: 1

    * - Branch
      - Usage
      - Typical Location
    * - studio-prod
      - Crafter Studio in Production
      - Production Authoring Server
    * - prod
      - Code management & promotion
      - Git Server\ :sup:`*`
    * - studio-qa
      - Crafter Studio in QA
      - QA Authoring Server
    * - qa
      - Code management & promotion
      - Git Server
    * - studio-dev
      - Crafter Studio in Dev environment
      - Shared Development Server
    * - dev
      - Code management & promotion
      - Git Server
    * - dev-a-fork
      - Day-to-day development
      - Developer’s Laptop

:sup:`*` *Git Server is an external Git service provider like GitHub, GitLab, BitBucket or similar, and is separate from the embedded Git repository in Crafter Studio*

Given the environments listed above, for the one upstream repository per environment approach, we identify the following upstream repositories:

.. list-table::
    :widths: 10 15 65
    :header-rows: 1

    * - Repository
      - Branches
      - Fork Of |br|
        (repo/branch)
    * - Prod
      - studio-prod |br|
        prod
      - \- |br|
        \-
    * - QA
      - studio-qa |br|
        qa
      - \- |br|
        Prod/prod
    * - Dev
      - studio-dev |br|
        dev
      - \- |br|
        QA/qa
    * - Developer A Fork
      - studio-dev |br|
        dev-a-fork
      - \- |br|
        Dev/dev

As you can see, the branches remain the same as the single upstream repository approach.

The repositories are set up as forks of one another. Depending on your external Git service provider, the manner in which these forks are created may vary. As shown in the flow diagram above, there is no need for the *studio-** branches in one repository to track those in another.

""""
Flow
""""
Referring back to the figure above, code flows following the steps

- The developer forks the *Dev* repository's *dev* branch creating *dev-a-fork*

  - This is a one-time action

- The developer pulls their branch down to their workstation
- If the feature requires Studio work, the developer creates an orphan project in Studio based on either *studio-dev* or *dev-a-fork*

  - If *dev-a-fork* is used, then the developer will get the latest content that was "content-backed" from production,
  - If *studio-dev* is used, the developer will get the latest content from the shared developer environment
  - The choice of which branch to use as the source will depend on the developer's needs for the feature being developed

- The developer branches their local repository creating a feature branch (*feature-x*)
- The developer uses their IDE to work on the feature
- The developer carries patches from Studio’s local repository to *feature-x* as needed
- Once done, the developer pushes their changes up to their branch and sends a pull-request to the *dev* branch for approval
- To deploy and test the code on the shared development environment, code flows from *dev* to *studio-dev*, and is then pulled into Crafter Studio.
- Code is similarly pushed from *dev* to *qa* and again to production (see diagram above)

""""""""""""""""""""""""""""""""""""
Using the Git Project Tool in Studio
""""""""""""""""""""""""""""""""""""
The steps above describe code and content moving between Git branches and external repositories. On each Crafter Studio authoring environment, the :ref:`Git project tool <project-tools-git>` is how developers and DevOps personnel sync the project's sandbox repository with those shared branches.

Open it from the **Sidebar** by clicking on |projectTools|, then **Git**. From there you can add a remote repository, pull changes into Studio, and push sandbox commits to a remote when appropriate. For UI walk-throughs of each operation, see :ref:`project-tools-git`.

The Git project tool is typically used **after** branch promotion work has been done in your Git server (merge, pull request, or fast-forward on the target branch). Studio pulls the result into the authoring environment so authors and developers can preview and test against the updated sandbox.

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Example: Deploy Code to Delivery Dev
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
After a feature branch is merged into *dev* (code forward) and the *studio-dev* branch is updated on the Git server:

#. On the ``Authoring Dev`` (the authoring environment), ensure the project is linked to the upstream repository (see :ref:`create-project-based-on-a-blueprint-then-add-a-remote-bare-git-repository` if the remote is not yet configured). The upstream repository should be the *studio-dev* branch on *Dev* on the ``Git Server``.
#. In the Git project tool, pull from the remote branch that tracks *studio-dev* (or the branch your team uses for the Dev Studio sandbox), by clicking on the down arrow next to the remote repository branch that tracks the *studio-dev* branch on the ``Git server``.
#. Review the changes in Studio, then publish as needed so Delivery Dev is updated.

This corresponds to the step in the flow :ref:`above <devcontentops-flow-diagram>` where code moves from *dev* to *studio-dev* on the Git server and is then pulled into Crafter Studio in ``Authoring Dev``.

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Example: Promote Code to QA or Prod
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
The same pattern applies when promoting code forward to QA and Prod:

#. Merge or promote code on the ``Git Server`` (for example, from *dev* to *qa*, or from *qa* to *prod*) via a PR.
#. On the **QA** or **Prod** authoring environment, use the Git project tool to pull the updated *studio-qa* or *studio-prod* branch on the ``Git Server`` into Studio by clicking on the down arrow next to the desired remote repository branch.
#. Validate in Studio (and Staging, if enabled), then publish to the target delivery environment.

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Example: Sync a Developer Workstation
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
When a developer creates an orphan project from *dev-a-fork* or needs to align a local Studio sandbox with their fork:

#. Add the developer's fork as a remote in the Git project tool on the ``Developer Workstation``.
#. Pull from the *dev-a-fork* branch (or the feature branch being actively developed).
#. Continue Studio-side work, carrying patches to the local *feature-x* branch in the IDE as described above.

To publish IDE work back to the shared flow, push from the workstation to the fork and open a pull request into *dev*—not directly into *studio-dev* on the ``Git Server``.

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Example: Pull Content Back into Studio
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
After a *Content Back* operation syncs production content from *studio-prod* into *prod* then *prod* to *dev* (or *qa*) on the ``Git Server``, the updated content must still be pulled into the lower authoring environments:

#. Confirm the content merge landed on the target branch in your ``Git Server``.
#. On the **Dev** or **QA** authoring environment, pull that branch into Studio using the Git project tool, by clicking on the down arrow next to the desired remote repository branch.
#. If blob storage is in use, sync blob content separately as described in :ref:`Blob Storage and Content Back <devcontentops-blob-storage-content-back>` below.

.. note::
   *Content Back* merges happen on the *dev*, *qa*, or *prod* branches—not by pulling from *studio-qa* into *qa* or from *studio-dev* into *dev*. The Git project tool pulls **into** Studio; it does not replace the Git server-side promotion steps described earlier.

~~~~~~~~~~~~~~~~~~~
Important Practices
~~~~~~~~~~~~~~~~~~~
- **Pull into Studio; promote in Git.** Use your external Git service for merges, pull requests, and branch protection. Use the Git project tool to bring the result into Studio's sandbox.
- **Respect path conventions.** Avoid pushing author-owned paths (such as ``/site/*`` and ``/static-assets/content/*``) from lower environments upstream. See the path conventions in the next section.
- **Resolve conflicts locally when possible.** If a pull into Studio reports a conflict, prefer resolving it on a developer workstation or in your Git service, then pull again. The Git project tool provides basic conflict options for emergencies; see the note in :ref:`project-tools-git`.
- **Publish after pulling.** A successful pull updates the sandbox repository. Use **Project Tools → Publishing** to deploy pulled changes to preview or delivery targets when required (see :ref:`project-tools-git`).

|

"""""""""""""""""""""""""""""""""""""""""""""""""""""""
What Should and Shouldn’t Be Moved Between Environments
"""""""""""""""""""""""""""""""""""""""""""""""""""""""
There are some paths that are expected to be identical across all environments, and there are some paths that will vary across environments.

Generally, files that are identical across all environments are the domain of administrators and developers, while paths that change from one environment to the next are the domain of the author and publisher.

CrafterCMS recommends the following conventions:

.. list-table::
    :widths: 25 25 50
    :header-rows: 1

    * - Path
      - Mastered In
      - Notes
    * - /config/*
      - Dev and promoted up
      - This is a special case because every environment can have its own configuration, see the guide: |br|
        :ref:`multi-environment-support`
    * - /scripts/*
      - Dev and promoted up
      - Default location for Groovy code
    * - /site/*
      - Production and promoted down
      - Default location for content items and pages
    * - /static-assets/content/*
      - Prod and promoted down
      - Suggested location for binary content objects used by authors
    * - /static-assets/*
      - Dev and promoted up
      - Other binary items typically used by developers, not available to authors
    * - /static-assets/root/*
      - Dev and promoted up
      - Holds concerns like robots.txt, site identifiers, security.txt, favicon.ico, …
    * - /static-assets/app/*
      - Dev and promoted up
      - Suggested location for compiled/transpiled application code
    * - /templates/*
      - Dev and promoted up
      - Default location for FreeMarker code
    * - /sources/*
      - Dev and promoted up
      - Suggested location for sources of compiled/transpiled application code

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Managing and Avoiding Merge Conflicts
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The flexibility of the model described above enables authors and developers to have control over the components that are important to each of them. If teams follow the practices and conventions described above, authors and developers will have a positive experience moving content and code between environments as part of their daily activities. Correspondingly, the Git branching model will function well, and merge conflicts will be infrequent.

However, in exceptional circumstances, authors or developers may temporarily break conventions and deviate from these guidelines. This can result in merge conflicts when resuming normal operations, and these merge conflicts need to be resolved.

Moving content and code between branches or between Git repositories is a task normally performed by developers or DevOps personnel, and these are the individuals that will be responsible for resolving conflicts.

CrafterCMS is not a conflict management utility (although very coarse capabilities do exist), and developers and DevOps personnel have tools that they prefer in addressing them. These tools are indeed suitable for managing these conflicts.

""""""""""""""""""""""""""""""""""""
Resolving the Merge Conflict Locally
""""""""""""""""""""""""""""""""""""

A fairly common conflict scenario arises when promoting code from one environment to the next (e.g. Dev to QA). An administrator may have modified a configuration file directly on the server or changed a data source, or similar. In these cases, the changed config file will likely be in a merge conflict, blocking a regular promotion of the code.

A very simple solution would be to pull the dev and QA branches down to a local developer workstation's Git repository, run the merge locally, manually fix the conflict (using your tool of choice), and then push the merged QA branch back up to the shared Git repository.

If your Git repository permissions include branch protection, the merge conflict may need to be done via a dedicated branch, but the net effect of this operation will be the same.

Merge conflicts can be a nuisance, but resolving them using the tools that developers and DevOps personnel prefer, thus treating them like run-of-the-mill conflicts requiring resolution, allows the experts who perform these tasks regularly to manage them with ease

^^^^^^^^^^^^^^^^^^^^^
The Content Back Flow
^^^^^^^^^^^^^^^^^^^^^

Content originates in the Prod authoring environment, where it is published to the Prod delivery environments. But this content will also flow from the Prod environment back to lower environments, like to QA, and then from QA to Dev, and finally from Dev to individual developer workstations. This *Content Back* flow is a critical feature of DevContentOps.

Referring back to the flow diagram :ref:`here <devcontentops-flow-diagram>`, the *Content Back* pathway clearly shows this model.

""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""
Preventing Content Flow from Lower Environments to High Environments
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

It is important to note that *Content Back* never occurs from *studio-qa* to *qa*, or from *studio-dev* to *dev*. Content must only originate from the *studio-prod* branch to get into the *dev*/*qa*/*prod* branches, to avoid having content from lower environments be picked up in the *Code Forward* model.

Git actions can be applied to the repository to reject pull requests or merges that contain content moving back from these branches. Practically, the most important place to apply this verification is on merges into the dev branch, as this is where most content from individual developers accidentally makes its way into the system. (Depending on how your rule is set up, it may be necessary to override this protection when performing an intentional Content Back operation.)

This production content that has been "*Content Backed*" from prod into lower environments will ultimately make its way into the *studio-dev* and *studio-qa* environments with subsequent deployments which can be done as part of the Content Back operation or left for the next code deployment.

"""""""""""""""""""""""""""""""""""""""""
Content Merge Conflicts with Content Back
"""""""""""""""""""""""""""""""""""""""""

If developers or QA personnel have created content items with the same path as production content items, or if they have modified production content items on the *development* or *qa* environment, a merge conflict will occur during a *Content Back* when moving content from dev to *studio-dev* or *qa* to *studio-qa*

These conflicts can be resolved exactly the same way as code conflicts. The simplest resolution would be to accept the content coming from *dev* or from *qa*. Still, the repository will be able to handle selecting the item that's present in the *studio-* branches as well. Just keep in mind that production content won't make it to the CrafterCMS environment because of the way the conflict was resolved.

.. _devcontentops-blob-storage-content-back:

"""""""""""""""""""""""""""""
Blob Storage and Content Back
"""""""""""""""""""""""""""""

Not all content resides exclusively in the Git repository. CrafterCMS supports using a blob store for large
binary objects (typically stored in the */static-assets/content* folder as noted above). When performing a
*Content Back*, it's important to sync the content in the blob store from the higher environment to the
lower environment.

A very simple way of performing this operation is to simply add all of the content in prod into the blob
store of the stage environment and then to that of dev. If AWS S3 is the datastore, a simple command like

.. code-block:: bash

    aws s3 sync s3://myProdBlobStoreBucket/v1/static-assets/content/ s3://myQaBlobStoreBucket/v1/static-assets/content/ --profile myCompany

To run this operation on the local developer's machine, something like this can be done

.. code-block:: bash

    aws s3 sync s3://myProdBlobStoreBucket/v1/static-assets/content/ /Volumes/Projects/MyCompany/crafter/data/repos/sites/mySite/sandbox/static-assets/content/ --profile myCompany

Feel free to alter this process to suit your needs. Some teams find it handy to keep these commands ready
to run whenever a *Content Back* is done.

"""""""""""""""""""""""""""""
Repository Cleanup (Optional)
"""""""""""""""""""""""""""""

Projects with low or moderate content quantities will generally not require any sort of repository maintenance.
Git is an efficient, robust, and simple storage system that performs well for some of the busiest and largest
code projects on the planet.

In projects that involve truly immense quantities of content, the repository may need to be periodically
cleaned up to maintain optimum performance. Total object size and the number of commits can both impact
repository performance.

Projects that are subject to these conditions should consider the multi-upstream repository model described above.

Git repository tuning techniques can be employed when necessary to keep each environment performing well. However, occasionally (particularly in the dev environment), a clean start is the best choice. As the Dev repository is simply a fork of QA, the Dev repository and environment can both be dropped and re-created to match the performance profile of QA. Individual developers will need to re-create their forks and local environments to maintain alignment with their Git histories.

The same approach can also be done on the QA environment as well, though environment re-creation may need to
cascade down to Dev and individual developers as well to ensure that Git histories are maintained properly.

Finally, this approach also allows the entire stack of Dev, QA, and Prod environments to also be completely
re-created consistently and reliably (and it can even be automated) if a repository overhaul is desired,
although the need to do this is extremely rare.

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Developer Environment Creation
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

CrafterCMS's DevContentOps model provides a systematic process for creating all of the environments in a CMS
ecosystem from production to local development. A robust and consistently-reproducible environment is
particularly important for advanced development. The techniques described in this document allow developers
to completely recreate their environments on demand in a couple of minutes. This is a significant advantage
over having to spend hours getting a local development environment of an enterprise platform up and running
and is a differentiator for CrafterCMS when compared to database-oriented CMSs.

--------------------------
Working with DevContentOps
--------------------------
When the above DevContentOps processes are adopted, the content authoring experience is streamlined and very
robust. Amazingly though, the developer experience is greatly enhanced by allowing developers to easily work
with a true replica of the production system in every environment.

This leads to a much more efficient workflow, without imposing complicated demands on the DevOps teams.

CrafterCMS's inspired Git-based repository architecture allows *Code Forward* and *Content Back* operations to
be done seamlessly. Moreover, because CrafterCMS’s DevContentOps support is Git-based (rather than just
Git-like), developers work with the development tools and platforms of their choice and can integrate
natively with their processes and best practices.
