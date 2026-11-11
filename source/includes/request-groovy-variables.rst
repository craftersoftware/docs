+------------------------+--------------------------------------+---------------------------------+
| Name                   | Description                          | API                             |
+========================+======================================+=================================+
| request                || The current request                 || `HttpServletRequest`_          |
+------------------------+--------------------------------------+---------------------------------+
| response               || The current response                || `HttpServletResponse`_         |
+------------------------+--------------------------------------+---------------------------------+
| params                 || The parameter values for the current|| `Map`_                         |
|                        || request                             ||                                |
+------------------------+--------------------------------------+---------------------------------+
| headers                || The header values for the current   || `Map`_                         |
|                        || request                             ||                                |
+------------------------+--------------------------------------+---------------------------------+
| cookies                || The cookie values for the current   || `Map`_                         |
|                        || request                             ||                                |
+------------------------+--------------------------------------+---------------------------------+
| session                || The current session, if it has been || `HttpSession`_                 |
|                        || created                             ||                                |
+------------------------+--------------------------------------+---------------------------------+
| locale                 || The current locale for the current  || `Locale`_                      |
|                        || user                                ||                                |
+------------------------+--------------------------------------+---------------------------------+
| authToken              || The current authentication (if the  || `Authentication`_              |
|                        || user has logged in), created by     ||                                |
|                        || Spring Security                     ||                                |
+------------------------+--------------------------------------+---------------------------------+
| pathVars               || The |pathVars| values for the       || `Map`_                         |
|                        || current request                     ||                                |
+------------------------+--------------------------------------+---------------------------------+

The following variables are provided for backward compatibility when using Crafter Profile |enterpriseOnly|, should be replaced
with ``authToken`` if possible:

+------------------------+--------------------------------------+---------------------------------+
| Name                   | Description                          | API                             |
+========================+======================================+=================================+
| authentication         || The current authentication (if the  || |Authentication|               |
|                        || user has logged in), created by the ||                                |
|                        || Crafter Security Provider           ||                                |
+------------------------+--------------------------------------+---------------------------------+
| profile                || The current profile (if the user    || |Profile|                      |
|                        || has logged in), created by the      ||                                |
|                        || Crafter Security Provider           ||                                |
+------------------------+--------------------------------------+---------------------------------+

.. note::
    The variables ``profile`` **and** ``authentication`` listed  above will be null in most cases and should not be used anymore

The following variables are restricted by default, to use them see :ref:`configure-custom-services`

+------------------------+--------------------------------------+---------------------------------+
| Name                   | Description                          | API                             |
+========================+======================================+=================================+
| application            || The servlet context                 || `ServletContext`_              |
+------------------------+--------------------------------------+---------------------------------+

.. _ServletContext: http://docs.oracle.com/javaee/6/api/javax/servlet/ServletContext.html
.. _HttpServletRequest: http://docs.oracle.com/javaee/6/api/javax/servlet/http/HttpServletRequest.html
.. _HttpServletResponse: http://docs.oracle.com/javaee/6/api/javax/servlet/http/HttpServletResponse.html
.. _Map: https://docs.oracle.com/javase/7/docs/api/java/util/Map.html
.. _HttpSession: http://docs.oracle.com/javaee/6/api/javax/servlet/http/HttpSession.html
.. _Locale: https://docs.oracle.com/javase/7/docs/api/java/util/Locale.html
.. |Authentication| replace:: :javadoc_base_url:`Authentication <profile/org/craftercms/security/authentication/Authentication.html>`
.. |Profile| replace:: :javadoc_base_url:`Profile <profile/org/craftercms/profile/api/Profile.html>`
.. _Authentication: https://docs.spring.io/spring-security/site/docs/4.0.x/apidocs/org/springframework/security/core/Authentication.html
.. |pathVars| replace:: :ref:`path variable <groovy-path-variables>`