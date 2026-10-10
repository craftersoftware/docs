:is-up-to-date: True
:last-updated: 4.7.0

.. meta::
   :description: Secure CrafterCMS delivery projects with a properties file, SAML2, headers-based authentication, authorization for sections and content items, and delivery tier security.
   :keywords: security, authentication, users.properties, SAML2, authorization, Crafter Engine, CrafterCMS, headers-based authentication, delivery security

.. _project-security:

===================
Security (Delivery)
===================
Securing CrafterCMS projects/site is a very important aspect of the platform. CrafterCMS provides a number of security features that can be used to secure a project/site. This section will cover the following topics:

- Authentication Mechanics
- Authorization Mechanics
- Securing an Entire Delivery Tier
- Securing Sections or Individual Content Items

.. - Securing Some Static Assets

------------------------
Authentication Mechanics
------------------------
CrafterCMS supports a number of authentication mechanisms. The following are supported out of the box:

- Engine Properties file (``users.properties``)
- SAML2 |enterpriseOnly|
- Headers-based Authentication |enterpriseOnly|

-----------------------
Authorization Mechanics
-----------------------
Authorization is handled by the project/site's configuration and implementation. CrafterCMS provides a number of features that can be used to implement authorization.

^^^^^^^^^^^^^^^^^^^^^^^^
Role-based Authorization
^^^^^^^^^^^^^^^^^^^^^^^^
Role-based authorization is the most common authorization mechanism used in CrafterCMS. Roles can be attached to content items, or entire sections, and users will only be able to see these items or section if they're in a group that has that role.

.. note::
    Users that arrive a at URL for which they do not have access will receive an HTTP 404 error, which is handled by Crafter Engine. If a custom 404 page is configured, the user will be redirected to that page. If no custom 404 page is configured, the user will see the default 404 page.

^^^^^^^^^^^^^^^^^^^^^^^^
Update the Content Model
^^^^^^^^^^^^^^^^^^^^^^^^
To attach roles to items, add the ``authorizedRoles`` model to the item's content type definition. Learn more about content modeling in :ref:`content-modeling`.

Begin by editing the content type definition of the content item, and from the Controls list on the right, select ``Repeating Group`` and add it to the ``Metadata`` Form Section.
In the ``Repeating Group`` properties, set the Title field to “Authorized Roles” and the Name/Variable Name field to “authorizedRoles.”

.. image:: /_static/images/site-admin/authorized_roles_properties.webp
   :alt: Engine Site Security Guide - Authorized Roles Properties

|

.. warning::
    The UI autofills the **Name/Variable Name** field and adds postfixes as you're typing in the **Title** field.  Remember to remove the postfix ``_o``, as ``authorizedRoles`` is a reserved variable name used by CrafterCMS.  For a list of variable names used by CrafterCMS, see :ref:`form-control-variable-names` for more information

    The ``ROLE_`` prefix is optional for values in ``authorizedRoles``

Add an Input control inside the Repeating Group, with the **Title** field set to "Role" and the **Name/Variable
Name** field set to "role". Make this Input required by checking the checkbox under **Constraints** in the
**Required** field in the **Properties Explorer**.

.. image:: /_static/images/site-admin/role_properties.webp
   :alt: Engine Site Security Guide - Role Properties

|

.. warning::
    The UI autofills the **Name/Variable Name** field and adds postfixes as you're typing in the **Title** field.  Remember to remove the postfix ``_s``, as the ``role`` variable name is used by CrafterCMS for enforcing access to a page.  For a list of variable names used by CrafterCMS, see :ref:`form-control-variable-names` for more information

""""""""""""""""""""""""""
Update the Content Item(s)
""""""""""""""""""""""""""
Adding the ``authorizedRoles`` model to the content type definition will not automatically update the content items. Those content instances must be updated manually, or by script to add the ``authorizedRoles`` model to the content item and specify the actual roles that are allowed to access the content item. For new content items, the ``authorizedRoles`` model will be available for content authors to populate.

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Role-based Authorization for Sections
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Role-based authorization can also be applied to entire sections. This is done by adding the ``authorizedRoles`` model to the ``crafter-level-descriptor.level.xml`` file in the section's folder. This will limit access to the entire section to only users that are in the specified roles. Learn more about content inheritance in :ref:`content-inheritance`.
Bear in mind that the ``crafter-level-descriptor.level.xml`` file is not created by default. It must be created manually. Also, individual content items can override the ``authorizedRoles`` model specified in the ``crafter-level-descriptor.level.xml`` file.

---------------------------------
Securing the Entire Delivery Tier
---------------------------------
The easiest way to secure a project is by wrapping the entire delivery tier with an authentication layer that blocks access to _all_ content unless the user is authenticated.

Protecting the entire delivery tier means blocking all access unless the user is authenticated. This can be done in a number of ways:

- Configuring Crafter Engine to authenticate with ``users.properties``, SAML2, or headers-based authentication
- Configuring the CDN (Content Delivery Network), e.g. AWS CloudFront with AWS Cognito
- Configuring a reverse-proxy, e.g. Apache HTTPd with `mod_auth_openidc`

In this article, we will cover how to configure Crafter Engine authentication. Utilizing a CDN or reverse-proxy is outside the scope of this article, but we will provide a general overview of how to configure them.

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Configure Delivery-Wide Authentication
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Delivery-wide authentication can be accomplished with Crafter Engine, a CDN, a reverse-proxy, or a combination of these mechanisms.

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Crafter Engine Instance-Wide Authentication
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Crafter Engine supports properties-based authentication, the SAML2 protocol, and HTTP-headers-based authentication.

""""""""""""""""""""""""""""""""""""""
Engine Properties-Based Authentication
""""""""""""""""""""""""""""""""""""""
Engine can authenticate users from a Spring Security properties file at ``CRAFTER_HOME/bin/apache-tomcat/shared/classes/crafter/engine/extension/users.properties``.

Follow :ref:`engine-users-properties` to add users. Roles in that file must match the roles used by ``authorizedRoles`` or the site's URL security rules. A user in that file is an Engine user only, separate from Studio users, and has no access to Studio.

""""""""""""""""""""""""""""""""""""""""""""
Engine SAML2 Authentication |enterpriseOnly|
""""""""""""""""""""""""""""""""""""""""""""
Crafter Engine's SAML2 implementation is configured at the instance level. This means that all projects served by this instance node will use the same SAML2 configuration, and will be authenticated against the same SAML2 IdP (Identity Provider).

Follow the article :ref:`engine-saml2-configuration` to configure SAML2 authentication for Crafter Engine.

.. TODO The following section can be put back in if we go back to supporting different SAML2 per project
    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    Multi-Environment Authentication Support
    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    It's often the case that lower environments will require their own authentication configuration. Crafter Engine supports that by allowing you to configure multiple SAML2 configurations, and then specify which configuration to use for each environment. See the article :ref:`saml2-multi-environment-support` for more information.

"""""""""""""""""""""""""""""""""""""""""""""""""""""""""
Engine HTTP Headers-Based Authentication |enterpriseOnly|
"""""""""""""""""""""""""""""""""""""""""""""""""""""""""
When authenticating outside of Crafter Engine, the authenticating system can pass the authenticated user information to Crafter Engine via HTTP headers. Crafter Engine can then use that information to authenticate the user to the project/site. Learn more about this in the article :ref:`engine-headers-authentication`.

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
CDN or Reverse-Proxy and Securing Static Assets
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
It's sometimes desirable to use a CDN or reverse-proxy to secure the delivery tier. The primary use-case here is the delivery of static assets, such as videos, images, CSS, and JavaScript files via the CDN or reverse-proxy instead of the application tier. This can speed up delivery of these assets, and reduce the load on the application tier.

As an example, AWS CloudFront supports this approach as described in https://aws.amazon.com/blogs/networking-and-content-delivery/securing-cloudfront-distributions-using-openid-connect-and-aws-secrets-manager.

A similar setup can be accomplished with Apache HTTPd and ``mod_auth_openidc``, whereby Apache HTTPd serves the static assets, and Engine serves the dynamic experience and API calls.

It's important to remember that for Crafter Engine to know about this authentication that was terminated upstream, it's important to pass in the correct headers for Crafter Engine to use. Learn more about this in the article :ref:`engine-headers-authentication`.

.. TODO Do we need to discuss Signed URLs here?