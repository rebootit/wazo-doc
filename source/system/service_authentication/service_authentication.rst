.. _service-authentication:

**********************
Service Authentication
**********************

XiVO services expose more and more resources through REST API, but they also ensure that the access
is restricted to the authorized programs. For this, we use an :ref:`authentication daemon
<xivo-auth>` who delivers authorizations via tokens.


Call flow
=========

Here is the call flow to access a REST resource of a XiVO service:

1. Create a username/password (also called service_id/service_key) with the right :ref:`ACLs
   <rest-api-acl>`, via :ref:`web_services_access`.
2. :ref:`Create a token <xivo-auth>` with these credentials and the backend :ref:`xivo-service
   <auth-backends-service>`.
3. :ref:`Use this token <rest-api-authentication>` to access the REST resource defined by the
   :ref:`ACL <rest-api-acl>`.

.. figure:: images/service_authentication_workflow.png

   Call flow of service authentication


Service
    Service who needs to access a REST resource.

xivo-{daemon}
    Server that exposes a REST resource. This resource must have an attached ACL.

xivo-auth
    Server that authenticates the `Service` and validates the required ACL with the token.

XiVO services directly use this system to communicate with each other, as you can see in their Web
Services Access.