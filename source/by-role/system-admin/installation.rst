:is-up-to-date: True
:last-updated: 4.2.0

.. meta::
   :description: Install CrafterCMS via binary bundles, Docker, or Kubernetes for authoring and delivery environments with system requirements and platform support information.
   :keywords: installation, CrafterCMS, binary bundles, Docker, Kubernetes, authoring, delivery, Java 21, system requirements

.. _system-admin-installation:

============
Installation
============
This section describes the various ways of installing CrafterCMS.

CrafterCMS consists of the authoring and delivery systems. The authoring enables writing, managing, and publishing
of all content, and delivery is what users will be accessing from their devices/browsers to view all the content that
have been published. The installation steps for both the authoring and delivery is similar unless otherwise noted in
the instructions below.

.. important::
    **You must change pre-configured defaults** to secure your installation.
    See :ref:`system-admin-security` for more information.

First we'll take a look at the requirements and supported platforms for installing and setting up CrafterCMS.

.. _requirements_supported_platforms:

------------
Requirements
------------
- Java 21
- 8+ Gig of memory to JVM (additional memory may be required depending on the size and number of your web experiences)
- Git 2.20.1 and later
- Docker (if running in Docker or macOS)

Please note that CrafterCMS does not require any external databases for the core system to run and deliver fully dynamic
experiences. MongoDB is used by Crafter Profile and Crafter Social which are optional components that provide external
(non-author) user management and social features.

For more information on sizing your machines, see :ref:`authoring server requirements <authoring-server-requirements>`
and :ref:`delivery server requirements <delivery-server-requirements>`

^^^^^^^^^^^^^^^^^^^
Supported Platforms
^^^^^^^^^^^^^^^^^^^
"""""""""""""""""""""""""""""""""""""""""""""
Operating System/Environment (All components)
"""""""""""""""""""""""""""""""""""""""""""""
The following Operating Systems and environments are supported (64Bit only for all OSs)

    - Docker
    - Linux (Ubuntu, RHEL)
    - macOS (10.12 and later)
    - Windows via WSL 2

"""""""""""""""""""""
JVMs (All components)
"""""""""""""""""""""
The following JVMs are supported:

    - OpenJDK 21
    - Oracle JDK (HotSpot) 21

~~~~~~~~~~~~~~~~~~~
Verify Java Version
~~~~~~~~~~~~~~~~~~~
Ensure that you are running Java 21. To check,
run the following command at the command prompt and make sure that the version displayed is Java 21:

.. code-block:: sh

    java -version

|

The command above should output something like this:

.. code-block:: sh

    openjdk version "21.0.5"

|

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Verify JAVA_HOME environment variable is set correctly
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Make sure that you have a JAVA_HOME environment variable that points to the root of the JDK install directory.
To check the value set for JAVA_HOME, enter the following command at the command prompt:

.. code-block:: sh

    env | grep JAVA_HOME

|

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
How to set the JAVA_HOME environment variable
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
.. tabs::
    .. tab:: Korn and bash shells:

        .. code-block:: bash

            export JAVA_HOME=jdk-install-dir
            export PATH=$JAVA_HOME/bin:$PATH

    .. tab:: Bourne shell:

        .. code-block:: sh

            JAVA_HOME=jdk-install-dir
            export JAVA_HOME
            PATH=$JAVA_HOME/bin:$PATH
            export PATH

    .. tab:: C shell:

        .. code-block:: csh

            setenv JAVA_HOME jdk-install-dir
            export JAVA_HOME
            PATH=$JAVA_HOME/bin:$PATH
            export PATH
            setenv PATH $JAVA_HOME/bin:$PATH
            export PATH=$JAVA_HOME/bin:$PATH

|

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Browsers (Crafter Studio & Crafter Social/Profile Admin Consoles)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
The following browsers are supported:

    - Chrome and Chromium-based Browsers
    - Firefox
    - MS Edge

|

|hr|

^^^^^^^^^^^^^
Prerequisites
^^^^^^^^^^^^^
.. tabs::
    .. tab:: macOS
        For macOS users, the following applies:

        #. The latest ``openssl`` formula needs to be installed via homebrew:

           .. code-block:: sh

               brew install openssl

        #. Docker is used to run OpenSearch and needs to be installed.
           Follow the instructions `here <https://docs.docker.com/install/>`__ to install Docker. |br| (macOS is not supported by
           OpenSearch so Docker is used to run OpenSearch. See https://opensearch.org/docs/latest/install-and-configure/os-comp/
           for more information.)

           When starting up CrafterCMS on macOS, the following message is displayed:

           .. code-block:: bash

               Preflight check.
               Operating system is Mac, must use Docker to run OpenSearch.
               Running in this mode is for development purposes only.

                ██████╗ ██████╗   █████╗  ███████╗ ████████╗ ███████╗ ██████╗   ██████╗ ███╗   ███╗ ███████╗
               ██╔════╝ ██╔══██╗ ██╔══██╗ ██╔════╝ ╚══██╔══╝ ██╔════╝ ██╔══██╗ ██╔════╝ ████╗ ████║ ██╔════╝
               ██║      ██████╔╝ ███████║ █████╗      ██║    █████╗   ██████╔╝ ██║      ██╔████╔██║ ███████╗
               ██║      ██╔══██╗ ██╔══██║ ██╔══╝      ██║    ██╔══╝   ██╔══██╗ ██║      ██║╚██╔╝██║ ╚════██║
               ╚██████╗ ██║  ██║ ██║  ██║ ██║         ██║    ███████╗ ██║  ██║ ╚██████╗ ██║ ╚═╝ ██║ ███████║
                ╚═════╝ ╚═╝  ╚═╝ ╚═╝  ╚═╝ ╚═╝         ╚═╝    ╚══════╝ ╚═╝  ╚═╝  ╚═════╝ ╚═╝     ╚═╝ ╚══════╝

           |

           Depending on the environment started, an ``authoring-search`` or ``delivery-search`` container using the OpenSearch
           Docker image will be running. In the screenshot below, an authoring and delivery CrafterCMS environment is running:

           .. image:: /_static/images/system-admin/opensearch-docker-image-running-on-macos.webp
               :width: 100 %
               :align: center
               :alt: OpenSearch Docker container started on CrafterCMS startup

           .. warning::
               By default, search indexes do not persist across restarts. If you want the OpenSearch indexes to persist
               across restarts, you must use bind mounts with Docker volumes.

               By default, the OpenSearch container created when starting CrafterCMS creates the bind mounts for you.
               Your host ``CRAFTER_HOME/data/indexes`` directory is bind mounted to ``/usr/share/opensearch/data/`` of the
               OpenSearch container. You'll just need to make sure that the directory ``CRAFTER_HOME/data/indexes`` on the host
               is a shared drive in Docker's settings by clicking on Docker Desktop ``Settings`` -> ``Resources`` -> ``File Sharing``

               .. image:: /_static/images/developer/docker/docker-desktop-file-sharing.webp
                   :alt: Docker Desktop - File Sharing
                   :width: 85 %
                   :align: center

               |

               If the directory ``CRAFTER_HOME/data/indexes`` is not under an already shared parent level in Docker Desktop, simply
               input your path in ``Virtual file shares`` by clicking on ``Browse`` then navigate to the desired folder, then
               click on the ``+`` button, and finally the ``Apply & restart`` button to add it.


    .. tab:: Linux
        #. The library ``libaio`` is required by the Authoring install. Please note that some Linux distributions does not install the library ``libaio`` by default and so, may need to be installed. You may get the following error when starting up Studio:

           **error while loading shared libraries: libaio.so.1: cannot open shared object file: No such file or directory**

           To install ``libaio`` for Debian-based Linux distros: ``sudo apt install libaio1t64`` then ``sudo ln -sf /lib/x86_64-linux-gnu/libaio.so.1t64 /usr/lib/libaio.so.1``

           To install ``libaio`` for RedHat-based Linux distros: ``yum install libaio``

        #. For Linux users, some of the scripts uses ``lsof``. Please note that some Linux distributions does not come with ``lsof`` pre-installed and so, may need to be installed.

           To install ``lsof`` for Debian-based Linux distros: ``apt-get install lsof``

           To install ``lsof`` for RedHat-based Linux distros: ``yum install lsof``

        #. The library ``libncurses5`` is required by the Authoring install due to the embedded MariaDB. You may get the following error when running an Authoring install or the restore script without the ``libncurses5`` library installed:

           .. code-block:: text

               error while loading shared libraries: libncurses.so.5: cannot open shared object file: No such file or directory

           To install the library ``libncurses5``, use the following commands:

           **On Debian-based Linux distros:**

           - For Ubuntu 24.04 and later versions, ``libncurses5`` was removed starting in version 24.04. To install ``libncurses5``,
             we need to add the ``focal-security`` distribution main and universe repository to the sources list before installing.
             This will bring the 22.04 APT repository to 24.04 and will then allow us to install ``libncurses5`` within the system.

             Before following the commands below, check if ``ubuntu-focal-sources.list`` exists under ``/etc/apt/sources.list.d``.
             If the file does not exist, create the file first, then run the commands below to install ``libncurses5``

             .. code-block:: bash

                 sudo sh -c 'echo "deb http://security.ubuntu.com/ubuntu focal-security main universe" >> /etc/apt/sources.list.d/ubuntu-focal-sources.list'

                 sudo apt update

                 sudo apt install libncurses5

           - All other versions of Debian-based linux distro: ``sudo apt install libncurses5``

           **On RHEL, CentOS:**

           - For versions 8 and later, the EPEL repository needs to be added in order to install the ``ncurses-compat-libs``.
             To add the EPEL repository, do the following:

             - Enable the CodeReady Linux Builder repository |br|
               CentOS: ``sudo dnf config-manager --set-enabled crb`` |br|
               RHEL: ``sudo subscription-manager repos --enable codeready-builder-for-rhel-9-$(arch)-rpms``
             - Next, install the EPEL RPM: ``sudo dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm``
             - Finally, we can now install ``ncurses-compat-libs``: ``sudo dnf install ncurses-compat-libs``

           - All other versions of RHEL, CentOS: ``sudo yum install ncurses-compat-libs``

           **On Fedora 22 and newer version:** ``sudo dnf install ncurses-compat-libs``

    .. tab:: Windows
        For Windows users, WSL 2 needs to be installed. All CrafterCMS scripts/commands to be executed and items that needs to be downloaded and installed needs to be in WSL 2.

        Follow the instructions `here <https://docs.microsoft.com/en-us/windows/wsl/install>`__ to install WSL 2.

|

|hr|

------------
Installation
------------
CrafterCMS provides the following methods for installation depending on your environment. There are various installation methods:

- :ref:`SaaS <saas>`
- :ref:`Docker <running-craftercms-in-docker>`
- :ref:`Kubernetes <deploying-craftercms-in-kubernetes>`
- :ref:`AWS AMI <run-using-aws-ami>`
- :ref:`Binary Bundles <install-craftercms-via-binary-bundles>`
- :ref:`Build from Source <build-from-source>`

We'll go through these one by one.

.. _saas:

^^^^
SaaS
^^^^
You can sign up for `Crafter Cloud <https://craftercms.com/products/crafter-cloud>`_.

|hr|

.. _running-craftercms-in-docker:

^^^^^^
Docker
^^^^^^
You can run CrafterCMS locally using Docker:

""""""""""""""""""""""""""""""""""""
Quick Start for Evaluation in Docker
""""""""""""""""""""""""""""""""""""
For a quick start to evaluate CrafterCMS, follow these simple instructions. If you have `Docker <https://www.docker.com/>`_ installed, you can get started with CrafterCMS in a few minutes. Simply run the following command:

.. code-block:: bash

    docker run -p 8080:8080 craftercms/authoring_local:latest

Then point your browser to ``http://localhost:8080/studio`` and you will be presented with the Crafter Studio login page. The default username is ``admin`` and the default password is ``admin``.

""""""""""""""
Docker Compose
""""""""""""""
To run CrafterCMS in a set of Docker containers using Docker Compose, make sure the following are installed in your local system:

* Docker (https://docs.docker.com/install/)
* Docker Compose (https://docs.docker.com/compose/install/)
* Git 2.20.1 or later

**For Windows and macOS, we recommend you give Docker Desktop at least 12GB of RAM (6GB of RAM per environment) and 4 CPUs. To do this, go to Docker Desktop's Preferences > Resources > Advanced, and then change the resource limits.**

.. image:: /_static/images/quick-start/docker-advanced-settings.webp
    :alt: Docker Desktop Advanced Settings
    :width: 80%
    :align: center

|

#. Clone the CrafterCMS Docker Compose repo from GitHub

   .. code-block:: bash

      ➜ git clone https://github.com/craftercms/docker-compose.git

   |

#. If you are a developer and want to use your local IDE and other tools, follow :ref:`this <local-dev-with-docker>` documentation to configure your docker container to support access to your projects via local IDE.

#. Go into the authoring folder, then start the container by running ``docker-compose up``. This starts the authoring
   environment, which enables authoring, management, and publishing of all content.

   .. code-block:: bash
      :caption: *Console output when starting the authoring container*
      :emphasize-lines: 2

      ➜  docker-compose git:(master) cd authoring
      ➜  authoring git:(master) docker-compose up
      [+] Running 9/9
      ✔ Network authoring_default               Created                                                        0.1s
      ✔ Volume "crafter_authoring_temp"         Created                                                        0.0s
      ✔ Volume "crafter_authoring_data_search"  Created                                                        0.0s
      ✔ Volume "crafter_authoring_logs_search"  Created                                                        0.0s
      ✔ Volume "crafter_authoring_data"         Created                                                        0.0s
      ✔ Volume "crafter_authoring_logs"         Created                                                        0.0s
      ✔ Container authoring-search-1            Created                                                        0.1s
      ✔ Container authoring-deployer-1          Created                                                        0.1s
      ✔ Container authoring-tomcat-1            Created                                                        0.1s
      Attaching to deployer-1, search-1, tomcat-1
      ...
      tomcat-1    | 11-Aug-2024 11:28:25.535 INFO [main] org.apache.coyote.AbstractProtocol.start Starting ProtocolHandler ["http-nio-8080"]
      tomcat-1    | 11-Aug-2024 11:28:25.579 INFO [main] org.apache.catalina.startup.Catalina.start Server startup in [68028] milliseconds

   You may now login to Crafter Studio at ``http://localhost:8080/studio``. The default username is ``admin`` and the default password is ``admin``.

If you want to view your published project, follow the instructions below to start CrafterCMS delivery and setup your
project for delivery. CrafterCMS delivery is what users will be accessing from their browsers to view all the content
that have been published. Remember to publish your project before setting up your project for delivery.

#. Go into the delivery folder, then start the container by running ``docker-compose up``. This starts the delivery
   environment, which is what users will be accessing from their browsers to view all the content that have been published.

   .. code-block:: bash
       :caption: *Console output when starting the delivery container*
       :emphasize-lines: 2

       ➜  docker-compose git:(master) cd delivery
       ➜  delivery git:(master) docker-compose up
       [+] Running 9/9
       ✔ Network delivery_default               Created                                                        0.1s
       ✔ Volume "crafter_delivery_temp"         Created                                                        0.0s
       ✔ Volume "crafter_delivery_data_search"  Created                                                        0.0s
       ✔ Volume "crafter_delivery_logs_search"  Created                                                        0.0s
       ✔ Volume "crafter_delivery_data"         Created                                                        0.0s
       ✔ Volume "crafter_delivery_logs"         Created                                                        0.0s
       ✔ Container delivery-search-1            Created                                                        0.2s
       ✔ Container delivery-deployer-1          Created                                                        0.1s
       ✔ Container delivery-tomcat-1            Created                                                        0.3s
       Attaching to deployer-1, search-1, tomcat-1
       ...
       tomcat-1    | 18-Apr-2025 21:39:29.709 INFO [main] org.apache.coyote.AbstractProtocol.start Starting ProtocolHandler ["http-nio-8080"]
       tomcat-1    | 18-Apr-2025 21:39:29.756 INFO [main] org.apache.catalina.startup.Catalina.start Server startup in [25618] milliseconds

#. To view your published project, we'll first setup your project for delivery by running the init-site script. Go into
   the scripts folder and run the ``init-site.sh`` script. Remember to replace <SITENAME> below with the actual project name.

   .. code-block:: bash
       :caption: *Setup Project for Delivery*

       ➜  docker-compose git:(master) cd scripts
       ➜  scripts git:(master) ./init-site.sh <SITENAME>
       /path/to/docker-compose/delivery /path/to/docker-compose/scripts
       Creating Deployer Target...
       Target created successfully
       /path/to/docker-compose/scripts

   Once you've setup your project for delivery, to access your published project, open a browser and type in the URL of
   your project, ``localhost:9080``.

   For more information on setting up your project for delivery, see :ref:`setup-project-for-delivery`.

The console output when you start the container (as shown above) contains useful information that you can use to debug or monitor the status of CrafterCMS. To view more of the logs, the Docker Desktop Dashboard provides a runtime view of all your containers and applications, including logs for monitoring/debugging CrafterCMS. To access the Docker Desktop Dashboard, from the **Docker menu**, select **Dashboard**.

.. image:: /_static/images/quick-start/docker-desktop-open-dashboard.webp
   :alt: Open Docker Desktop Dashboard
   :width: 25%
   :align: center

This will open a dialog displaying the running application with a list of containers running inside the application when you expand the application

.. image:: /_static/images/quick-start/docker-desktop-dashboard-list.webp
   :alt: Docker Desktop Dashboard Container List
   :width: 80%
   :align: center

When the ``authoring`` application displayed above is selected, the application view will open and list all the containers running on the application and display a detailed logs view

.. image:: /_static/images/quick-start/docker-desktop-dashboard.webp
   :alt: Docker Desktop Dashboard
   :width: 80%
   :align: center

To configure working on projects in Docker via a local IDE follow the instructions :ref:`here <local-dev-with-docker>`.

For more information on Docker Desktop Dashboard, see https://docs.docker.com/desktop/dashboard/

For more information on CrafterCMS Docker Compose, please see: https://github.com/craftercms/docker-compose.

""""""
Images
""""""
CrafterCMS provides Docker images on `dockerhub <https://hub.docker.com/u/craftercms>`__ for the latest release versions
and snapshots of versions in development. The CrafterCMS Docker images may be pulled by using the tag for a release
version, e.g. ``craftercms/authoring_tomcat:4.1.5``, the latest release, e.g. ``craftercms/authoring_tomcat:latest``
or the tag for a snapshot of a version in development, e.g. ``craftercms/authoring_tomcat:4.1.6-SNAPSHOT``

As mentioned, CrafterCMS is open source and you can always build Docker images from source code `here <https://github.com/craftersoftware/craftercms>`__

|hr|

.. _deploying-craftercms-in-kubernetes:

^^^^^^^^^^
Kubernetes
^^^^^^^^^^
CrafterCMS provides Kubernetes example deployments, which can be found here: https://github.com/craftercms/kubernetes-deployments.

This article will describe examples of deploying CrafterCMS in Kubernetes.

.. _setup-simple-authoring-with-kubernetes-deployment:

"""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""
Simple Authoring With a Single Instance Kubernetes Deployment
"""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""
CrafterCMS has an example Kubernetes deployment for an Authoring with a single instance, which you can get from https://github.com/craftercms/kubernetes-deployments/tree/support/4.x/authoring/simple. This guide covers how to install this example in a Kubernetes cluster.

.. important::
   This guide assumes you have a working understanding of Kubernetes

~~~~~~~~~~~~
Requirements
~~~~~~~~~~~~
You need to have a Kubernetes cluster, and the ``kubectl`` command-line tool must be configured to communicate with your cluster. If you do not already have a cluster, you can create one by using Minikube: https://github.com/kubernetes/minikube.

The nodes in your cluster should at least have 4 CPUs and 16 GB of space, to avoid performance issues and out of memory errors. In Minikube, to start a node with this characteristics, you can run a command similar to the following:
``minikube start --cpus 4 --memory 16384``.

~~~~~~~~~~~~~~~~~~~~~~~~
Setup Kubernetes Secrets
~~~~~~~~~~~~~~~~~~~~~~~~
The deployment files cloned from https://github.com/craftercms/kubernetes-deployments/ have a folder set aside for placing confidential information, ``kubernetes-deployments/authoring/simple/resources/secrets``

This guide details setting up an Authoring pod, but once you setup the Delivery pod, it will need SSH or HTTPS access to the Authoring pod to pull site content. For this, you need to generate an SSH public/private key pair or an HTTPS SSL certificate
and key for authentication and provide them as Kubernetes Secrets to the Pods.

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Choose if SSH/HTTPS as Git protocol
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Deployer containers in Delivery environments can use either SSH or HTTPS to pull changes from the published repositories in Authoring. Kubernetes configuration under ``kubernetes-deployments/authoring/simple`` that's only pertinent to a certain
protocol will start with the comment ``Uncomment if using Git (HTTPS|SSH)``. Make sure only the configuration lines corresponding to the protocol you decide to use are uncommented, and comment the configuration related to the other protocol.

'''''''''''''''''''''''''''''''''''''''''''
Create the SSH Keys Secrets (SSH mode only)
'''''''''''''''''''''''''''''''''''''''''''
#. Go to ``kubernetes-deployments/authoring/simple/resources/secrets/git-ssh-server`` (create the folders if they don't exist). This is where you will create the ssh keys.
#. Run ``ssh-keygen`` to generate the key pair (e.g. ``ssh-keygen -t ecdsa -b 521 -C "your_email@example.com"``).
   When asked for the filename of the key, just enter a filename e.g. ``id_rsa``, ``id_dsa``, ``id_ecdsa`` or ``id_ed25519`` depending
   on the type of key selected (so that the keys are saved in the current folder). Do not provide a passphrase.
#. Create a copy of the public key and name it ``authorized_keys``

      .. code-block:: sh

         ➜ cp id_rsa.pub authorized_keys

''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''
Create the HTTPS Certificate and Key Secrets (HTTPS mode only)
''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''
#. Go to ``kubernetes-deployments/authoring/simple/resources/secrets/git-https-server`` (create the folders if they don't exist). This is where you will create the HTTPS certificate and key secrets.
#. Run ``openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout server.key -out server.crt`` and follow the prompts. It's up to you what to enter in each field, **EXCEPT** for the Common Name (CN). This should match
   the Authoring service name. If following the example, the CN value should be ``authoring-svc-headless``.

~~~~~~~~~~~~~~~~~~~~
Start the Deployment
~~~~~~~~~~~~~~~~~~~~
Create the ``craftercms`` namespace if it doesn't exist yet.

   .. code-block:: bash

      ➜  kubectl create namespace craftercms
      namespace/craftercms created

If using ``minikube``, pre-pull the Authoring and OpenSearch images to avoid ``context deadline exceeded`` errors (change ``CRAFTERCMS_VERSION`` for the actual CrafterCMS version, e.g. 4.5.0).

   .. code-block:: bash

      ➜  minikube image pull craftercms/authoring_tomcat:CRAFTERCMS_VERSION
      ➜  minikube image pull opensearchproject/opensearch:3.3.2

Go to ``kubernetes-deployments/authoring/simple`` then run ``kubectl apply -k .``

   .. code-block:: bash

      ➜  kubectl apply -k .
      secret/ssh-keys-d2khm6g98k created
      service/authoring-svc created
      service/authoring-svc-headless created
      statefulset.apps/authoring created

Check the status of the Authoring StatefulSet by running ``kubectl get -n craftercms statefulsets``, and the status of the Pods by running ``kubectl get -n craftercms pods``.

   .. code-block:: bash

      ➜  kubectl get -n craftercms statefulsets
      NAME        READY   AGE
      authoring   1/1     2m7s

   .. code-block:: bash

      ➜  kubectl get -n craftercms pods
      NAME          READY   STATUS    RESTARTS   AGE
      authoring-0   4/4     Running   0          2m26s

Once it comes up, you will see the new pod in ``RUNNING`` status, with 4 containers ``READY``.

You can tail the logs of the ``tomcat`` and ``deployer`` containers, with the ``kubectl`` command:

   .. code-block:: bash

      kubectl logs -n craftercms -f -c CONTAINER_NAME POD_NAME

For example: ``kubectl logs -n craftercms -f -c tomcat authoring-0``

~~~~~~~~~~~~~~~~
Create a Project
~~~~~~~~~~~~~~~~
To be able to access the applications in Kubernetes, you need to use port forwarding. To access Studio, forward a local port to port 8080 of the pod, with the ``kubectl port-forward`` command:

   .. code-block:: bash

      kubectl port-forward -n craftercms pods/POD_NAME LOCAL_PORT:POD_PORT

Here's an example of forwarding local port 8080 to the 8080 port of the ``authoring-0`` pod:

   .. code-block:: bash

      ➜  kubectl port-forward -n craftercms pods/authoring-0 8080:8080
      Forwarding from 127.0.0.1:8080 -> 8080
      Forwarding from [::1]:8080 -> 8080

After that, you can now access Studio using ``localhost:8080/studio`` in your browser and create a project.
Remember to :ref:`publish <publishing-and-status>` your project before continuing on the next steps.

To setup the project in delivery, follow the instructions listed here: :ref:`simple-delivery-kubernetes-deployment`

.. _simple-delivery-kubernetes-deployment:

"""""""""""""""""""""""""""""""""""""
Simple Delivery Kubernetes Deployment
"""""""""""""""""""""""""""""""""""""
CrafterCMS has an example Kubernetes deployment for a Delivery with a single instance, which you can get from https://github.com/craftercms/kubernetes-deployments/tree/support/4.x/delivery/simple. This guide covers how to install this example in a Kubernetes cluster.

.. important::
   This guide assumes you have a working understanding of Kubernetes

~~~~~~~~~~~~
Requirements
~~~~~~~~~~~~
You need to have a Kubernetes cluster, and the ``kubectl`` command-line tool must be configured to communicate with your cluster. If you do not already have a cluster, you can create one by using Minikube: https://github.com/kubernetes/minikube.

The nodes in your cluster should at least have 4 CPUs and 16 GB of space, to avoid performance issues and out of memory errors. In Minikube, to start a node with this characteristics, you can run a command similar to the following:
``minikube start --cpus 4 --memory 16384``.

In addition to that, you need an Authoring pod with a project published to ``live`` to pull site content from.

.. important::
   If you need to setup an Authoring environment, refer to :ref:`setup-simple-authoring-with-kubernetes-deployment`. **DO NOT** use :ref:`setup-studio-clustering-with-kubernetes-deployment`, which is not compatible with this guide.

~~~~~~~~~~~~~~~~~~~~~~~~
Setup Kubernetes Secrets
~~~~~~~~~~~~~~~~~~~~~~~~
The deployment files cloned from https://github.com/craftercms/kubernetes-deployments/ have a folder set aside for placing confidential information, ``kubernetes-deployments/delivery/simple/resources/secrets``.

If you previously setup Authoring with Git SSH access:

* Copy the ``id_rsa`` and ``id_rsa.pub`` files from ``kubernetes-deployments/authoring/simple/resources/secrets/git-ssh-server`` to ``kubernetes-deployments/delivery/simple/resources/secrets/git-ssh-server``

If you previously setup Authoring with Git HTTPS access:

* Copy the ``server.crt`` file from ``kubernetes-deployments/authoring/simple/resources/secrets/git-https-server`` to ``kubernetes-deployments/delivery/simple/resources/secrets/git-https-server``

Please also make sure that you uncomment the Kubernetes configuration lines under ``kubernetes-deployments/delivery/simple`` that start with ``Uncomment if using the Git (HTTPS|SSH) server`` (comment the configuration related to the other protocol).

~~~~~~~~~~~~~~~~~~~~
Start the Deployment
~~~~~~~~~~~~~~~~~~~~
Create the ``craftercms`` namespace if it doesn't exist yet.

   .. code-block:: bash

      ➜  kubectl create namespace craftercms
      namespace/craftercms created

If using ``minikube``, pre-pull the Delivery and OpenSearch images to avoid ``context deadline exceeded`` errors (change ``CRAFTERCMS_VERSION`` for the actual Crafter version, e.g. 4.5.0).

   .. code-block:: bash

      ➜  minikube image pull craftercms/delivery_tomcat:CRAFTERCMS_VERSION
      ➜  minikube image pull opensearchproject/opensearch:3.3.2

Go to ``kubernetes-deployments/delivery/simple`` then run ``kubectl apply -k .``

   .. code-block:: bash

      ➜  kubectl apply -k .
      kubectl apply -k .
      secret/ssh-keys-h5244t449m created
      service/delivery-svc-headless created
      statefulset.apps/delivery created

Check the status of the Delivery StatefulSet by running ``kubectl get -n craftercms deployments``, and the status of the Pods by running ``kubectl get -n craftercms pods``.

   .. code-block:: bash

      ➜  kubectl get -n craftercms statefulsets
      NAME        READY   AGE
      authoring   1/1     20m
      delivery    1/1     2m10s

   .. code-block:: bash

      ➜  kubectl get -n craftercms pods
      NAME          READY   STATUS    RESTARTS   AGE
      authoring-0   4/4     Running   0          21m
      delivery-0    3/3     Running   0          3m25s

Once it comes up, you will see the new pod in ``RUNNING`` status, with 3 containers ``READY``.

You can tail the logs of the ``tomcat`` and ``deployer`` containers, with the ``kubectl`` command:

   .. code-block:: bash

      kubectl logs -n craftercms -f -c CONTAINER_NAME POD_NAME

For example: ``kubectl logs -n craftercms -f -c tomcat authoring-0``

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Bootstrap the Site in Delivery
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
You will need to run the ``init-site.sh`` in order to setup the project in Delivery. Either of the following commands will create the Deployer site target and create the index in OpenSearch.

If Authoring is running with the Git SSH server container:

* Run ``kubectl exec -n craftercms -it delivery-0 --container deployer -- gosu crafter ./bin/init-site.sh -k /opt/crafter/data/ssh/id_rsa SITE_NAME ssh://authoring-svc-headless/opt/crafter/data/repos/sites/SITE_NAME/published``

   .. code-block:: bash

      ➜ kubectl exec -n craftercms -it delivery-0 --container deployer -- gosu crafter ./bin/init-site.sh -k /opt/crafter/data/ssh/id_rsa mysite ssh://authoring-svc-headless/opt/crafter/data/repos/sites/mysite/published
      Creating Deployer Target...
      SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
      SLF4J: Defaulting to no-operation (NOP) logger implementation
      SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
      Target created successfully

  .. include:: /includes/ssh-private-key.rst

If Authoring is running with the Git HTTPS server container:

* Run ``kubectl exec -n craftercms -it delivery-0 --container deployer -- gosu crafter ./bin/init-site.sh -u crafter -p crafter SITE_NAME https://authoring-svc-headless/repos/sites/SITE_NAME/published``

  .. code-block:: bash

      ➜ kubectl exec -n craftercms -it delivery-0 --container deployer -- gosu crafter ./bin/init-site.sh -u crafter -p crafter mysite https://authoring-svc-headless/repos/sites/mysite/published
      Creating Deployer Target...
      SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
      SLF4J: Defaulting to no-operation (NOP) logger implementation
      SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
      Target created successfully

  .. important::
      The example configuration files include the Git HTTPS credentials in plain text, for simplicity. If setting up Delivery in production, make sure to properly create the credentials as Secrets.

After a minute or two, the Deployer should have pulled the project content from Authoring (you can check it by getting the Delivery Deployer log: ``kubectl logs -n craftercms -c deployer delivery-0``).

You can now access the project in Delivery, by forwarding a local port to port 8080 of the pod, with the ``kubectl port-forward`` command:

   .. code-block:: bash

      kubectl port-forward -n craftercms pods/POD_NAME LOCAL_PORT:POD_PORT

Here's an example of forwarding local port 9080 to the 8080 port of the ``delivery-0`` pod:

   .. code-block:: bash

      ➜  kubectl port-forward -n craftercms pods/delivery-0 9080:8080
      Forwarding from 127.0.0.1:9080 -> 8080
      Forwarding from [::1]:9080 -> 8080

We can now view the project in Delivery from the pod by entering ``localhost:9080?crafterSite=mysite`` in your browser.

.. image:: /_static/images/system-admin/simple-delivery-site-in-browser.webp
   :alt: Simple Delivery Kubernetes deployments - Access site in delivery
   :width: 100%
   :align: center

Also, now when making a change in Authoring and publishing it, the change will be reflected in Delivery after a minute.

.. _setup-studio-clustering-with-kubernetes-deployment:

"""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""
Setup Studio Clustering with Kubernetes Deployment |enterpriseOnly|
"""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""
CrafterCMS has an example Kubernetes deployment for a Studio cluster with 2 nodes, which you can get from https://github.com/craftercms/kubernetes-deployments/tree/support/4.x/authoring/cluster. This guide covers how to install this example in a Kubernetes cluster.

.. important::
   This guide assumes you have a working understanding of Kubernetes

~~~~~~~~~~~~
Requirements
~~~~~~~~~~~~
You will need an AWS EKS cluster, with the AWS Load Balancer Controller installed (https://docs.aws.amazon.com/eks/latest/userguide/aws-load-balancer-controller.html), in order to use the cluster example as-is.

If you can't use an EKS cluster, your Kubernetes infrastructure needs to provide Load Balancers or some other kind of Ingress that is able to handle an active-passive deployment, where the active pod that should receive all traffic returns
HTTP 200 on its healthcheck, while the passive pods that are on standby return HTTP 202. The Load Balancer should be able to also seamlessly switch between pods when an active becomes passive (200 -> 202) and a passive becomes active (202 -> 200).

Each Authoring cluster node is a StatefulSet Pod in Kubernetes, and requires at least 4 CPUs and 16 GB of space, to avoid performance issues and out of memory errors. So we recommend having Kubernetes nodes of a similar size to the Pod requirements,
in different availability zones, so one Pod runs per availability zone.

If you're using bigger nodes that are capable of running multiple Pods, make sure that the Authoring Pods are spread evenly through availability zones by specifying Pod Affinity/Anti-Affinity (you will need to modify the example configuration):
https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#affinity-and-anti-affinity

Remember to have the following ports open:

  - ``8080`` for http
  - ``3306`` for the DB
  - ``5701`` for hazelcast

~~~~~~~~~~~~~~~~~~~~~~~~
Setup Kubernetes Secrets
~~~~~~~~~~~~~~~~~~~~~~~~
The repository https://github.com/craftercms/kubernetes-deployments/ has a folder set aside for placing required Kubernetes Secrets: ``kubernetes-deployments/authoring/cluster/resources/secrets``

This is where we will place the enterprise license to be used by the images in the deployment. Remember to name your license file ``crafter.lic``

Also, you will need an SSL certificate and private key valid for the Authoring Pods cluster addresses. Each Pod's address is specified in the ``CLUSTER_NODE_ADDRESS`` environment variable in ``authoring.yaml``. In the example, this
value is ``$(POD_NAME).authoring-svc-headless.craftercms``, which is a standard FQDN for a Kubernetes StatefulSet Pod: ``pod-hostname.headless-service-name.namespace``. For more information on Kubernetes DNS, see
https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/.

.. warning::
   Our DB cluster limits the DB node addresses to be no longer than 60 characters, so even though ``$(POD_NAME).authoring-svc-headless.craftercms.svc.cluster.local`` is another cluster node address alternative,
   it can't be used.

In order to generate a valid self-signed SSL certificate, you can do the following:

#. Go into the ``kubernetes-deployments/authoring/cluster/resources/secrets/git-https-server`` folder.
#. Run ``openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout server.key -out server.crt`` and follow the prompts. It's up to you what to enter in each field, *EXCEPT* for the Common Name (CN). This should match
   the Pods' base domain name. If following the example, the CN value should be ``*.authoring-svc-headless.craftercms``.

~~~~~~~~~~~~~~~~~
Start the Cluster
~~~~~~~~~~~~~~~~~
Go to ``kubernetes-deployments/authoring/cluster``, then run ``kubectl apply -k .``

This should deploy all necessary resources in Kubernetes. You can monitor the status of the cluster nodes by running ``kubectl get -n craftercms pods``.

Each Pod has 4 containers, and initially only some of those containers will appear as ready:

   .. code-block:: bash

      ➜  ~ kubectl get -n craftercms pods
      NAME          READY   STATUS    RESTARTS   AGE
      authoring-0   1/4     Running   0          21s
      authoring-1   1/4     Running   0          21s

When all containers show as ready (both Pods show ```READY 4/4``), then the cluster is fully initialized:

   .. code-block:: bash

      ➜  ~ kubectl get -n craftercms pods
      NAME          READY   STATUS    RESTARTS   AGE
      authoring-0   4/4     Running   0          8m43s
      authoring-1   4/4     Running   0          8m43s

Another way to monitor the Pods is by tailing the Tomcat container log with the ``kubectl logs -n craftercms -f tomcat authoring-0`` command and look for the ``Server startup in [XXXXX] milliseconds`` message:

   .. code-block:: bash

      [INFO] 2024-10-17T19:59:31,135 [main] [cluster.StudioPrimaryReplicaUtils] | This server is a replica node in a cluster, it will not perform any write                                                                                                                                                                                                                            │
      17-Oct-2024 19:59:31.152 INFO [main] org.apache.catalina.startup.HostConfig.deployWAR Deployment of web application archive [/usr/local/tomcat/webapps/studio.war] has finished in [139,582] ms                                                                                                                                                                                  │
      17-Oct-2024 19:59:31.157 INFO [main] org.apache.coyote.AbstractProtocol.start Starting ProtocolHandler ["http-nio-8080"]                                                                                                                                                                                                                                                         │
      17-Oct-2024 19:59:31.170 INFO [main] org.apache.catalina.startup.Catalina.start Server startup in [168732] milliseconds

~~~~~~~~~~~~~
Access Studio
~~~~~~~~~~~~~
You can easily access Studio through the Authoring load balancer. To get the load balancer address, run ``kubectl get -n craftercms ingress``. The load balancer address is the one in the ``ADDRESS`` column of the ``authoring-ingress``.

.. code-block:: bash

   ➜  ~ kubectl get -n craftercms ingress
   NAME                          CLASS   HOSTS   ADDRESS                                                                            PORTS   AGE
   authoring-git-https-ingress   alb     *       internal-k8s-crafterc-authorin-8830e79fae-1816184747.us-east-1.elb.amazonaws.com   80      24m
   authoring-ingress             alb     *       k8s-crafterc-authorin-2f4ed3b88b-532889167.us-east-1.elb.amazonaws.com             80      24m

~~~~~~~~~~~~~~~~~~~~~~~~~
Setup Delivery (optional)
~~~~~~~~~~~~~~~~~~~~~~~~~
You can use the Delivery Simple example under https://github.com/craftercms/kubernetes-deployments/tree/support/4.x/delivery/simple with this Authoring cluster example:

#. The Authoring Cluster example creates an internal load balancer that can be used by the Delivery Deployer to pull the published content from Authoring. The load balancer will need to have a valid domain name and SSL certificate.
   Follow the next steps to setup a DNS record and a certificate for the load balancer in AWS:

   #. Create a Route 53 CNAME record for the domain name. The record needs to be in a Private Hosted Zone (https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/hosted-zones-private.html), since the load balancer is internal, and
      the zone needs to be associated to the VPC of the EKS cluster where you deployed the Authoring Cluster example. If the hosted zone is in a different account than where the VPC resides, see this guide:
      https://aws.amazon.com/premiumsupport/knowledge-center/route53-private-hosted-zone/.

      .. image:: /_static/images/system-admin/clustering-internal-lb-route53-record-wizard.webp
         :alt: Studio Clustering using Kubernetes deployments - Route 53 record for internal load balancer
         :width: 100%
         :align: center

   #. Create a certificate in the AWS Certificate Manager. Enter the domain name used in the previous step and make sure you select DNS validation as the validation method.

      .. image:: /_static/images/system-admin/clustering-internal-lb-certificate-wizard.webp
         :alt: Studio Clustering using Kubernetes deployments - Certificate for internal load balancer
         :width: 100%
         :align: center

   #. Click on the Certificate ID to open the details of the certificate. On the Domains section, you will see a CNAME name and CNAME value. You will need to copy those and create a Route 53 record in the *Public* Hosted Zone (not the Private Hosted Zone
      mentioned previously) of the domain so that the certificate is validated.

      .. image:: /_static/images/system-admin/clustering-internal-lb-certificate-validation-records.webp
         :alt: Studio Clustering using Kubernetes deployments - Certificate for internal load balancer
         :width: 100%
         :align: center

#. In the ``kubernetes-deployments/authoring/cluster/authoring-deployment.yaml`` file, uncomment the commented lines under the ``authoring-git-https-ingress`` configuration, and fill the value of ``alb.ingress.kubernetes.io/certificate-arn`` with the
   ARN of the certificate just created. After that, run ``kubectl apply -k .`` in the folder to apply the changes.

.. code-block:: yaml

   # alb.ingress.kubernetes.io/listen-ports: '[{"HTTP": 80}, {"HTTPS":443}]'
   # alb.ingress.kubernetes.io/ssl-redirect: '443'
   # alb.ingress.kubernetes.io/certificate-arn: ''

#. Create a project in Authoring and make sure it's fully published
#. Run ``kubectl apply -k .`` in ``kubernetes-deployments/delivery/simple``. Monitor the Pods coming up with ``kubectl get -n craftercms pods``. There should only be one Delivery Pod.
#. After the Delivery Pod has started, run ``kubectl exec -n craftercms -it delivery-0 -c deployer -- gosu crafter bash`` to open a Bash shell to the Deployer container.
#. Run ``./bin/init-site.sh -u crafter -p crafter editorial https://<domain-name>/repos/sites/<site-name>/published`` to create a Deployer target that will pull the published content for the recently created project. Before executing the command, make sure
   to replace ``<domain-name>`` with the internal LB domain name and ``<site-name>`` with the name of the project.

   .. include:: /includes/ssh-private-key.rst

#. Get the Delivery LB address with ``kubectl get -n craftercms ingress`` and access the project by entering ``http://<delivery-lb-address>?crafterSite=<site-name>`` (replacing the ``<>`` placeholders of course).

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Updating and Shutting Down the Cluster
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
The Authoring Cluster's ``StatefulSet`` is configured with ``.spec.updateStrategy`` ``OnDelete``. This means that whenever the Kubernetes configuration for the ``StatefulSet`` is updated, you will need to manually delete the Pods to create new Pods in order
for the modifications to be reflected. We prefer this ``updateStrategy`` instead of ``RollingUpdate`` so administrators can restart the cluster replicas first (by killing their Pods), wait for them to come up, and finally restart the primary, whenever a small
update to the configuration needs to be applied (like changing a small flag in one of the Crafter configuration files under ``/opt/crafter/bin/apache-tomcat/shared/classes``).

For bigger updates, like a version upgrade or any other update that could cause modifications to the project content or the database, progressively scaling down the StatefulSet is recommended, by running
``kubectl scale statefulsets authoring --replicas=<current-replicas-minus-1>``, waiting until each Pod has been fully terminated before scaling down again, until all Pods are down. Then you can scale the StatefulSet up to the original number of Pods (so that they can all synchronized on startup).

If you just want to shutdown the entire cluster, scale down the StatefulSet as described above.

|hr|

.. _run-using-aws-ami:

^^^^^^^
AWS AMI
^^^^^^^
Follow the :ref:`run-using-aws-ami` to setup CrafterCMS authoring and delivery using Crafter's AWS AMIs.

CrafterCMS provides ready-to-run AMIs for Authoring and Delivery available from the AWS marketplace
`here <https://aws.amazon.com/marketplace/seller-profile?id=6d75ffca-9630-44bd-90b4-ac0e99058995>`_.

This guide will walk you through the process of launching these instances from the AWS Marketplace.

.. _setup-authoring-using-aws-ami:

""""""""""""""""""""""""""""""""""""""""""""""""""
Setup CrafterCMS Authoring Using Crafter's AWS AMI
""""""""""""""""""""""""""""""""""""""""""""""""""
This section details how to setup CrafterCMS authoring using Crafter's AWS AMI.

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Step 1: Launch an AWS EC2 instance using a CrafterCMS Authoring AMI
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Crafter provides a prebuilt AMI(s) for Crafter Studio, the authoring component of CrafterCMS. In this step we'll walk you through the initial launch of your instance. To get started, go to your EC2 dashboard and click ``Launch Instance`` AWS will prompt you to choose your AMI type/image.

.. image:: /_static/images/ami/craftercms-aws-ami-authoring-launch-instance.webp
    :width: 100 %
    :align: center
    :alt: CrafterCMS AWS AMI Authoring Launch Instance

You can find CrafterCMS Authoring by entering the keywords ``CrafterCMS Authoring`` in the search box as shown below. You will find a number of options in the search results.

* **CrafterCMS Community Authoring** is our open source version
* **CrafterCMS Enterprise Authoring** is our supported enterprise version

Click ``Select`` on the AMI type you want to use and then follow the launch instance/configuration wizard to determine the sizing and networking details for your instance. Please note, we recommend compute optimized / "C-class" machines for best results with typical production use.

.. image:: /_static/images/ami/craftercms-aws-ami-authoring-choose-ami.webp
    :width: 100 %
    :align: center
    :alt: CrafterCMS AWS AMI Authoring Choose AMI

The launch instance wizard gives you the ability to tweak your instance's configuration details. In the next step we will connect to the machine with SSH via a terminal/console in order to acquire the administrators initial login password for the Crafter Studio web application. You will need to add SSH to your inbound security settings for the instance to complete the next step and acquire the password. To do so, Scroll down to the ``Network settings`` section.


To add the rule for SSH, click the ``Inbound traffic`` tab and then ``Add Rule`` to configure SSH. You can restrict the inbound traffic for SSH in the dropdown box. To allow SSH from anywhere you can enter a CIDR of 0.0.0.0/0. Use the ports and IP masks/policies that align with your needs.

.. image:: /_static/images/ami/craftercms-aws-ami-authoring-configure-security-group.webp
    :width: 100 %
    :align: center
    :alt: CrafterCMS AWS AMI Authoring Configure Security Group

Once you have added SSH access and made any other tweaks you feel are necessary to your instance's configuration, Look through the ``Summary`` panel on the right and then click the ``Launch Instance`` button.

.. image:: /_static/images/ami/craftercms-aws-ami-authoring-review1.webp
    :width: 100 %
    :align: center
    :alt: CrafterCMS AWS AMI Authoring Review

After clicking ``Launch``, AWS will request that you **select a public key for administrative console access to the machine**. You will need access to this key in the next step in order to retrieve the administrator's password for Crafter Studio. Create or choose an existing key and click ``Launch Instances``

.. image:: /_static/images/ami/craftercms-aws-ami-authoring-launch-2.webp
    :width: 100 %
    :align: center
    :alt: CrafterCMS AWS AMI Authoring Launch

Once you click ``Launch instances`` AWS will start and initialize the instance. This may take a minute or two.

.. image:: /_static/images/ami/craftercms-aws-ami-authoring-initializing1.webp
    :width: 100 %
    :align: center
    :alt: CrafterCMS AWS AMI Authoring Initializing

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Step 2: Access the Crafter Studio login screen
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
In this step we want to prove that the application is up and running now that our instance has started and initialized fully. To do this, we're simply going to check that the application login screen shows up when we access it via a web browser.

To access the login screen open a web browser and navigate to ``http://DNS_NAME_OR_IP_ADDRESS/studio``.

.. image:: /_static/images/ami/craftercms-aws-ami-authoring-login.webp
    :width: 100 %
    :align: center
    :alt: CrafterCMS AWS AMI Authoring Login

You can find the IP address and/or DNS name in several locations on your AWS administration console shown here:

.. image:: /_static/images/ami/craftercms-aws-ami-authoring-public-ip-and-dns.webp
    :width: 100 %
    :align: center
    :alt: CrafterCMS AWS AMI Authoring Public IP and DNS

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Step 3: Acquire the Crafter Studio admin user's password
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
CrafterCMS randomly generates the admin user's password for Crafter Studio for each instance you launch. The password can be reset to anything you like after your initial login using the initial password. In this step we're going to log into the machine and acquire the password.

To acquire the initial password you must log in to the instance via SSH. Open a terminal and use the following command to sign in to the instance:

.. code-block:: sh
    :linenos:

    ssh -i ./PATH/TO/PEM/keys.pem ubuntu@IP-ADDRESS

.. image:: /_static/images/ami/craftercms-aws-ami-authoring-ssh.webp
    :width: 100 %
    :align: center
    :alt: CrafterCMS AWS AMI Authoring SSH

**If SSH times out :**

You will need to double check your security policies for the instance to make sure you are using the correct SSH port and that the firewall will accept your traffic from your network. To check these configurations, scroll right and click the ``Security Settings`` column for your instance. Follow the steps outlined in step 1 to check or configure your security policies. Once the rule has been added/updated or you have specified the non-standard port (22) in your SSH command (add -p PORT_NUMBER to your command) you will be able to log in.

**To get the initial administrative password:**

Once logged in you will execute a simple script to acquire the password. To do so, from the console prompt of your instance you can execute the following:

.. code-block:: sh
    :linenos:

    sudo get-studio-password.sh

The password will print out in the console. Copy this password into your clipboard and proceed to the next step.

.. image:: /_static/images/ami/craftercms-aws-ami-authoring-get-admin-password1.webp
    :width: 65 %
    :align: center
    :alt: CrafterCMS AWS AMI Authoring Get Admin Password

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Step 4: Sign into Crafter Studio
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Now that we have the randomly generated admin password for Crafter Studio we can sign in to the application.

In your web browser, navigate to ``http://DNS_NAME/studio`` and then enter admin as the Email/Username, paste the password you acquired from Step 3 then click "Sign In."

.. image:: /_static/images/ami/craftercms-aws-ami-authoring-login.webp
    :width: 100 %
    :align: center
    :alt: CrafterCMS AWS AMI Authoring Login

~~~~~~~~~~~~~~~~~~~~~~
Step 5: Setup complete
~~~~~~~~~~~~~~~~~~~~~~
Welcome to Crafter Studio! Your initial installation and setup is complete.

From here you can create sites, administer users/group and manage your admin user's account settings.

Follow this guide to create your first website or headless CMS project: :ref:`your-first-editorial-project`

Follow this guide to setup a Delivery instance of CrafterCMS on AWS: :ref:`setup-delivery-using-aws-ami`

.. _setup-delivery-using-aws-ami:

"""""""""""""""""""""""""""""""""""""""""""""""""
Setup CrafterCMS Delivery Using Crafter's AWS AMI
"""""""""""""""""""""""""""""""""""""""""""""""""
This section details how to setup CrafterCMS delivery using Crafter's AWS AMI.

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Step 1: Launch an AWS EC2 instance using a CrafterCMS Delivery AMI
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Crafter provides a prebuilt AMI(s) for Crafter Engine, the delivery component of CrafterCMS. In this step we'll walk you through the initial launch of your instance. To get started, go to your EC2 dashboard and click ``Launch Instance`` AWS will prompt you to choose your AMI type/image.

.. image:: /_static/images/ami/craftercms-aws-ami-delivery-launch-instance.webp
    :width: 100 %
    :align: center
    :alt: CrafterCMS AWS AMI Delivery Launch Instance

You can find CrafterCMS Delivery by entering the keywords ``CrafterCMS Delivery`` in the search box as shown below. You will find the following in the search results.

* CrafterCMS Enterprise Delivery - our supported enterprise version

Click ``Select`` on the AMI and then follow the launch instance/configuration wizard to determine the sizing and networking details for your instance. Please note, we recommend "C" class machines for best results with typical production use.

.. image:: /_static/images/ami/craftercms-aws-ami-delivery-select.webp
    :width: 100 %
    :align: center
    :alt: CrafterCMS AWS AMI Delivery Select

The launch instance wizard gives you the ability to tweak your instance's configuration details. In the next step we will connect to the machine with SSH via a terminal/console inorder to acquire the administrators initial login password for the Crafter Studio web application. You will need to add SSH to your inbound security settings for the instance to complete the next step and acquire the password. To do so, click the ``Configure Security Group`` tab.

To add the rule for SSH, click the ``Inbound traffic`` tab and then ``Add Rule`` to configure SSH. You can restrict the inbound traffic for SSH in the source column. To allow SSH from anywhere you can enter a CIDR of 0.0.0.0/0. Use the ports and IP masks/policies that align with your needs.

.. image:: /_static/images/ami/craftercms-aws-ami-delivery-security-groups.webp
    :width: 100 %
    :align: center
    :alt: CrafterCMS AWS AMI Delivery Security Groups

Once you have added SSH access and made any other tweaks you feel are necessary to your instance's configuration, click the ``Review`` tab and then click the ``Launch`` button.

.. image:: /_static/images/ami/craftercms-aws-ami-delivery-review.webp
    :width: 100 %
    :align: center
    :alt: CrafterCMS AWS AMI Delivery Review

After clicking ``Launch``, AWS will request that you select a public key for administrative console access to the machine. You will need access to this key in the next step in order to retrieve the administrator's password for Crafter Studio. Create or choose an existing key and click ``Launch Instances``

.. image:: /_static/images/ami/craftercms-aws-ami-selectkey.webp
    :width: 100 %
    :align: center
    :alt: CrafterCMS AWS AMI Delivery Select Key

Once you click ``Launch instances`` AWS will start and initialize the instance. This may take a minute or two.

.. image:: /_static/images/ami/craftercms-aws-ami-starting.webp
    :width: 100 %
    :align: center
    :alt: CrafterCMS AWS AMI Delivery AMI Starting

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Step 2: Access the Crafter Engine via the Web
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
In this step we want to prove that the application is up and running now that our instance has started and initialized fully. To do this, we're simply going to check that the application login screen shows up when we access it via a web browser.

To access the login screen open a web browser and navigate to ``http://DNS_NAME_OR_IP_ADDRESS``.

.. image:: /_static/images/ami/craftercms-aws-ami-delivery-engine-running.webp
    :width: 100 %
    :align: center
    :alt: CrafterCMS AWS AMI Delivery Engine Running

You can find the IP address and/or DNS name in several locations on your AWS administration console shown here:

.. image:: /_static/images/ami/craftercms-aws-ami-delivery-ipdns.webp
    :width: 100 %
    :align: center
    :alt: CrafterCMS AWS AMI Delivery IP DNS

.. _configure-crafter-engine-to-deliver-published-projects:

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Step 3: Configure Crafter Engine to deliver published projects
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Crafter Engine is now up and running. However, in order to deliver content, its deployer must be configured to monitor a published repository for one or more projects. A delivery engine is multi-tenant and can deliver many projects. Each project must be configured or "initialized" such that the deployer monitors a Git repository for published updates. CrafterCMS's delivery tier has a decoupled, shared-nothing architecture that makes it cloud native and elastically scalable. You can read more about this architecture here: :ref:`general-architecture`

In this section we'll show you how to configure a Crafter Delivery instance to monitor a published Git repository and delivery content for a given project. The simplest topology for this is to directly monitor the published repository of a project on the authoring instance. This is what we will configure. Let's get started.

''''''''''''''''''''''''''''''''''''''
Step 3.1: Configure authoring instance
''''''''''''''''''''''''''''''''''''''
In order to complete step 3, you must have a CrafterCMS authoring instance running. If you do not, please follow these instructions to set an instance:

:ref:`setup-authoring-using-aws-ami`


After your authoring instance is set up and configured, follow this guide to create a simple project.
:ref:`your-first-editorial-project`

Crafter will automatically "publish" the initial state of the project for you for you which will give you the prerequisites for the rest of step 3.

''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''
Step 3.2: Establish SSH-based communication between the delivery instance and the authoring instance
''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''

The delivery instance's deployer can use any git protocol to communicate with the published repository. SSH is a secure method that is available with no additional set up. We'll use this approach. SSH requires authentication and the cleanest way to authenticate is via public / private keys. You may use existing key pairs but they must be RSA type keys. For completeness we'll generate and install keys from scratch.

`````````````````````````````````````````
3.2.1: Create a public / private key pair
`````````````````````````````````````````
On your local machine, generate a public / private key pair. On a terminal/console execute the following command:

.. code-block:: sh
   :linenos:

   ssh-keygen -m PEM -b 4096 -t rsa

You will be prompted for the file name and a passphrase. Since Crafter will use this key as part of an automated process, do not enter a passphase (just hit enter when prompted.)

Once the keygen process completes you should find a file at the location you specified. This is your private key. You will find another file at the same location with a .pub extension. This is your public key.

.. image:: /_static/images/ami/craftercms-ssh-rsa-keygen.webp
    :width: 100 %
    :align: center
    :alt: CrafterCMS AWS AMI Delivery SSH RSA keygen

`````````````````````````````````````````````````````
3.2.2: Install the public key on the authoring server
`````````````````````````````````````````````````````
The next step is to upload and install our public key onto the authoring instance.

**Upload the public key to the authoring server**

You can upload the public key from your local machine to the authoring server by executing a file transfer via the scp command in a terminal window.

The command takes the following form: scp -i [PATH_TO_UBUNTU_USERS_PIVATE_KEY] [PATH_TO_GENERATED_PUBLIC_KEY] ubuntu@[DNS_NAME_OF_DELIVERY_INSTNCE]:~/crafter.pub

Example:

.. code-block:: sh
    :linenos:

    scp -i ~/user-test-aws.pem ~/Desktop/crafter.pub ubuntu@ec2-3-93-34-40.compute-1.amazonaws.com:~/crafter.pub

**Install the public key on the authoring server**

The next step is to log in to the authoring server and install the public key into the crafter user's account.

To do this, SSH on to the authoring server as the ubuntu user and execute the following commands:

.. code-block:: sh
    :linenos:

    sudo mkdir -p /home/crafter/.ssh
    sudo touch /home/crafter/.ssh/authorized_keys
    sudo chown -R crafter:crafter /home/crafter/.ssh
    sudo su -
    cd /home/ubuntu/
    cat crafter.pub >> /home/crafter/.ssh/authorized_keys
    sudo chown -R crafter:crafter /home/crafter/.ssh
    sudo ls -al /home/crafter/.ssh
    exit

The **ls** command will help you verify that the key has been added to the authorized_keys file and that crafter is the owner of the file. The output should look similar to the following:

.. code-block:: sh
    :linenos:

    total 12
    drwxr-xr-x 2 crafter crafter 4096 Apr 17 21:06 .
    drwxr-xr-x 3 crafter crafter 4096 Apr 17 21:06 ..
    -rw-r--r-- 1 crafter crafter 757 Apr 17 21:09 authorized_keys

````````````````````````````````````````````````````````
3.2.3: Install the private key on the delivery server(s)
````````````````````````````````````````````````````````
The next step is to upload and install our private key onto the delivery instance.

**Upload the private key to the delivery server**

You can upload the private key from your local machine to the delivery server by executing a file transfer via the scp command in a terminal window.

The command takes the following form: **scp -i [PATH_TO_UBUNTU_USERS_PIVATE_KEY] [PATH_TO_GENERATED_PRIVATE_KEY] ubuntu@[DNS_NAME_OF_DELIVERY_INSTNCE]:~/crafter**

Example:

.. code-block:: sh
    :linenos:

     scp -i ~/user-test-aws.pem ~/Desktop/crafter ubuntu@ec2-34-293-227-96.compute-1.amazonaws.com:~/crafter

**Install the private key on the delivery server**

The next step is to log in to the delivery server and install the private key into the crafter user's account.

To do this, SSH on to the delivery server as the ubuntu user and execute the following commands:

.. code-block:: sh
    :linenos:

    sudo cp crafter /home/crafter/.ssh/id_rsa

.. code-block:: sh
    :linenos:

    sudo chown crafter:crafter /home/crafter/.ssh/id_rsa

``````````````````````````````````````````````````````````````````````````
3.2.4 Log in to the authoring server from the delivery server(s) using SSH
``````````````````````````````````````````````````````````````````````````
Now that both our public and private keys are installed on their respective servers we're going to test that they work properly and establish a fingerprint for the authoring server on the delivery server. To do this:

SSH on to the delivery server as the ubuntu user and execute the following commands:

.. code-block:: bash
    :linenos:

    sudo su crafter
    ssh -o HostKeyAlgorithms=ssh-rsa crafter@ec2-3-93-34-40.compute-1.amazonaws.com

It's important that you include the  **-o HostKeyAlgorithms=ssh-rsa** parameter in the initial SSH connection to the authoring server. Crafter expects the fingerprint to be stored in an RSA format. Once you execute the SSH command to log in to the authoring machine from the delivery machine as the crafter user. You will be prompted to verify the auhtenticity of the authoring server. Type yes to confirm. After this you will be logged in to the authoring server. No further action is required. Type exit in to the command line of the authoring server to terminate the SSH session.

.. code-block:: text
    :linenos:

    The authenticity of host 'ec2-3-93-34-40.compute-1.amazonaws.com (172.31.79.17)' can't be established.
    RSA key fingerprint is SHA256:OWVEsHafTLRPlmAO/J/RtvXoCrsx2oMKm4qMooIm+WE.
    Are you sure you want to continue connecting (yes/no)? yes

    Warning: Permanently added 'ec2-3-93-34-40.compute-1.amazonaws.com,172.31.79.17' (RSA) to the list of known hosts.
    Welcome to Ubuntu 18.04.1 LTS (GNU/Linux 4.15.0-1031-aws x86_64)

    ....

    exit

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Step 4: Initialize the project on the delivery server
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Now that secure communication is established between the authoring and delivery instances, we can initialize any number of projects to be delivered by the delivery instance. It's very simple to initialize a project on the delivery instance. To do so:

SSH to the delivery server and execute the following command in the **/opt/crafter/bin** directory as the **crafter** user:

.. code-block:: sh
    :linenos:

    init-site.sh

When the init-site.sh command is executed without any parameters, parameter documentation will be presented.

.. include:: /includes/ssh-private-key.rst

We'll use the following parameters in our example based on our public/private key setup that we did in the previous steps:

.. code-block:: sh
    :linenos:

    init-site.sh -b live [SITE_ID] ssh://crafter@[DNS_NAME_OF_AUTHORING_SERVER]:/opt/crafter/data/repos/sites/[SITE_ID]/published

The SITE_ID parameter can be acquired from the authoring server. Log in to Crafter Studio and "Preview" the project you wish to initialize and you will find the SITE_ID in the URL.

.. image:: /_static/images/ami/craftercms-siteid.webp
    :width: 100 %
    :align: center
    :alt: CrafterCMS AWS AMI Delivery Site Id

Successful execution of this command will produce output similar to the following:

.. code-block:: bash
    :linenos:

    Creating Deployer Target...
    Target created successfully

Example:

.. code-block:: bash
    :linenos:

    init-site.sh -b live editorial ssh://crafter@ec2-3-93-34-40.compute-1.amazonaws.com:/opt/crafter/data/repos/sites/editorial/published

.. image:: /_static/images/ami/craftercms-aws-ami-initsite.webp
    :width: 100 %
    :align: center
    :alt: CrafterCMS AWS AMI Delivery initsite

You can further verify that the deployment is working by watching the logs. To watch the logs use the following command:

.. code-block:: sh
    :linenos:

    tail -n 1000 -f /opt/crafter/logs/deployer/crafter-deployer.out

Look for output that is similar to the following:

.. code-block:: bash
    :linenos:

    2024-04-17 21:39:00.001 INFO 4389 --- [pool-5-thread-1] o.c.d.impl.processors.GitPullProcessor : Cloning Git remote repository ssh://crafter@ec2-3-93-34-40.compute-1.amazonaws.com:/opt/crafter/data/repos/sites/editorial/published into /opt/crafter/data/repos/sites/editorial
    2024-04-17 21:39:00.806 INFO 4389 --- [pool-5-thread-1] o.c.d.impl.processors.GitPullProcessor : Successfully cloned Git remote repository ssh://crafter@ec2-3-93-34-40.compute-1.amazonaws.com:/opt/crafter/data/repos/sites/editorial/published into /opt/crafter/data/repos/sites/editorial

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Step 5: View the project on the delivery server from a web browser
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Now that project has deployed it can be viewed via web browser. As previously mentioned, Crafter's delivery tier is multi-tenant. A SITE_ID is used on the URL to indicate which tenant is to be displayed. To preview the project you initialized, open a browser and navigate to the following URL:

**http://[DELIVERY_DNS_NAME]?crafterSite=[SITE_ID]**

Example:

**http://ec2-34-239-227-96.compute-1.amazonaws.com?crafterSite=editorial**

.. image:: /_static/images/ami/craftercms-aws-ami-delivery-viewsite.webp
    :width: 100 %
    :align: center
    :alt: CrafterCMS AWS AMI Delivery View Site

.. note::
   The **SITE_ID** parameter is typically added automatically to all requests for a given project by a webserver "virtual host" configured for each project/tenant that sits in front of Crafter Engine. You can learn more about this :ref:`here <configure-reverse-proxy-for-delivery>`

This configuration and other advanced topology topics such as load balancing are outside the scope of a basic installation.

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Step 6: Make an update in authoring and see it published on the delivery server
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
To further test publishing, log in to Crafter Studio for the given project, make an edit and then approve the edit for publish. In a few moments you will see your updates in the web browser on the delivery server. You can find step by step instructions on editing and publishing here: :ref:`your-first-editorial-project`

.. image:: /_static/images/ami/craftercms-approve-publish.webp
    :width: 100 %
    :align: center
    :alt: CrafterCMS AWS AMI Delivery Approve & Publish

~~~~~~~~~~~~~~~~~~~~~~~
Step 7: Setup complete!
~~~~~~~~~~~~~~~~~~~~~~~
Your setup and configuration of your CrafterCMS Delivery on AWS is now complete!

|hr|

.. _install-craftercms-via-binary-bundles:

^^^^^^^^^^^^^^
Binary Bundles
^^^^^^^^^^^^^^
CrafterCMS provides pre-built binary bundles for Linux, MacOS x86 and ARM.
Follow the instructions below to install CrafterCMS using pe-built binary bundles.

"""""""""""""""""""
Download the Bundle
"""""""""""""""""""
Download CrafterCMS binary bundle from https://craftercms.com/download

Select your operating system then click on the ``Download`` link next to ``Authoring`` or ``Delivery`` depending on your
needs.  For the authoring, the file downloaded will be ``crafter-cms-authoring-*.tar.gz``  and ``crafter-cms-delivery*.tar.gz``
for delivery. The ``.tar.gz`` file will install a fully functional authoring/delivery instance. Out of the box, the
authoring instance uses a local directory as the repository and an embedded database, which allows a quick and easy set
up for local development.

"""""""""""""""""""""""""""""""
Extract the CrafterCMS binaries
"""""""""""""""""""""""""""""""
Extract the contents in the desired directory.

.. code-block:: sh

  tar -zxvf crafter-cms-authoring*.tar.gz -C /tmp/target-unzip-directory/

|

   The extracted files should look like this:

   .. code-block:: none

      {target-unzip-directory}
      |--crafter/
         |--LICENSE
         |--README.txt
         |--bin/

   |

You can then do the same for the delivery bundle.

""""""""""""""""
Start CrafterCMS
""""""""""""""""
To start CrafterCMS, execute the startup script:

.. code-block:: sh

  {CRAFTER_HOME}/bin/startup.sh

.. note::

    *It takes a few seconds for CrafterCMS to startup and takes longer to startup the very first time you startup CrafterCMS.*


"""""""""""""""
Stop CrafterCMS
"""""""""""""""
To stop CrafterCMS, execute the shutdown script:

.. code-block:: sh

  {CRAFTER_HOME}/bin/shutdown.sh

.. note::

    *It takes a few seconds for CrafterCMS to stop.*


"""""""""""""""""""""""
Login to Crafter Studio
"""""""""""""""""""""""
To access Crafter Studio, In your browser, go to

.. code-block:: none

  http://SERVER:PORT/studio

.. note::
    For local deployments, the URL is ``http://localhost:8080/studio``

* Login with the following:

  * **username:** admin
  * **password:** admin


After logging in, you should be redirected to the ``Projects`` screen, and you're now ready to create your first experience!

"""""""""""""""""""""""""""""""""""""""""
Viewing Your Published Project (optional)
"""""""""""""""""""""""""""""""""""""""""
To view your published project, we'll need to start the CrafterCMS delivery instance and setup your project for delivery.
CrafterCMS delivery is what users will be accessing from their browsers to view all the content that have been published.
Remember to publish your project before setting up your project for delivery.

To start your delivery instance, follow the instructions above for downloading the delivery bundle and starting the
CrafterCMS delivery instance. Once delivery is running, we can now setup our published project in authoring for delivery.

In your CrafterCMS delivery, go to the ``bin`` folder and run the ``init-site.sh`` script. Remember to replace <SITENAME>
below with the actual published project name.

.. code-block:: sh

    cd CRAFTER_HOME/bin
    ./init-site <SITE_NAME> /absolute/path/to/authoring/published/repo

Once you've setup your project for delivery, to access your published project, open a browser and type in the URL of
your project, for local deployments, the URL is ``localhost:9080``.

For more information on setting up your project for delivery, see :ref:`setup-project-for-delivery`.

|hr|

.. _build-from-source:

^^^^^^^^^^^^^^^^^
Build from Source
^^^^^^^^^^^^^^^^^

CrafterCMS is open source and you can always build and run it from source code `here <https://github.com/craftersoftware/craftercms>`__
