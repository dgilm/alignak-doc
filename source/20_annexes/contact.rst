.. _monitoring_objects/contact:

===================
Contact Definition
===================

A contact definition is used to identify someone who should be contacted in the event of a problem on your network. The different arguments to a contact definition are described below.


Syntax
======

Bold variables are required, while others are optional.
Emphasized variables are Alignak extensions with reference to the Nagios legacy definition.


================================= =====================================
define contact{
**contact_name**                  ***contact_name***
alias                             *alias*
contactgroups                     *contactgroup_names*
**host_notifications_enabled**    **[0/1]**
**service_notifications_enabled** **[0/1]**
**host_notification_period**      ***timeperiod_name***
**service_notification_period**   ***timeperiod_name***
**host_notification_options**     **[d,u,r,f,s,n]**
**service_notification_options**  **[w,u,c,r,f,s,n]**
**host_notification_commands**    ***command_name***
**service_notification_commands** ***command_name***
email                             *email_address*
pager                             *pager_number or pager_email_gateway*
address*x*                        *additional_contact_address*
can_submit_commands               [0/1]
is_admin                          [0/1]
min_business_impact               [0/1/2/3/4/5]
}
================================= =====================================


Example
=======

::

  define contact{
      contact_name                    jdoe
      alias                           John Doe
      host_notifications_enabled      1
      service_notifications_enabled   1
      service_notification_period     24x7
      host_notification_period        24x7
      service_notification_options    w,u,c,r
      host_notification_options       d,u,r
      service_notification_commands   notify-service-by-email
      host_notification_commands      notify-host-by-email
      email                           jdoe@localhost.localdomain
      pager                           555-5555@pagergateway.localhost.localdomain
      address1                        xxxxx.xyyy@icq.com
      address2                        555-555-5555
      can_submit_commands             1
  }


Variables
=========

contact_name
  This directive is used to define a short name used to identify the contact. It is referenced in :ref:`contact group <monitoring_objects/contactgroup>` definitions. Under the right circumstances, the $CONTACTNAME$ :ref:`macro <monitoring_features/macros>` will contain this value.

alias
  This directive is used to define a longer name or description for the contact. Under the rights circumstances, the $CONTACTALIAS$ :ref:`macro <monitoring_features/macros>` will contain this value. If not specified, the *contact_name* will be used as the alias.

contactgroups
  This directive is used to identify the *short name(s)* of the :ref:`contactgroup(s) <monitoring_objects/contactgroup>` that the contact belongs to. Multiple contactgroups should be separated by commas. This directive may be used as an alternative to (or in addition to) using the *members* directive in :ref:`contactgroup <monitoring_objects/contactgroup>` definitions.

host_notifications_enabled
  This directive is used to determine whether or not the contact will receive notifications about host problems and recoveries. Values :

    * 0 = don't send notifications
    * 1 = send notifications

service_notifications_enabled
  This directive is used to determine whether or not the contact will receive notifications about service problems and recoveries. Values:

    * 0 = don't send notifications
    * 1 = send notifications

host_notification_period
  This directive is used to specify the short name of the :ref:`time period <monitoring_objects/timeperiod>` during which the contact can be notified about host problems or recoveries. You can think of this as an “on call" time for host notifications for the contact. Read the documentation on :ref:`time periods <monitoring_features/timeperiods>` for more information on how this works and potential problems that may result from improper use.

service_notification_period
  This directive is used to specify the short name of the :ref:`time period <monitoring_objects/timeperiod>` during which the contact can be notified about service problems or recoveries. You can think of this as an “on call" time for service notifications for the contact. Read the documentation on :ref:`time periods <monitoring_features/timeperiods>` for more information on how this works and potential problems that may result from improper use.

host_notification_commands
  This directive is used to define a list of the *short names* of the :ref:`commands <monitoring_objects/command>` used to notify the contact of a *host* problem or recovery. Multiple notification commands should be separated by commas. All notification commands are executed when the contact needs to be notified. The maximum amount of time that a notification command can run is controlled by the :ref:`notification_timeout <configuration/core#notification_timeout>` option.

host_notification_options
  This directive is used to define the host states for which notifications can be sent out to this contact. Valid options are a combination of one or more of the following:

    * d = notify on DOWN host states
    * u = notify on UNREACHABLE host states
    * r = notify on host recoveries (UP states)
    * f = notify when the host starts and stops :ref:`flapping <monitoring_features/flapping>`,
    * s = send notifications when host or service :ref:`scheduled downtime <monitoring_features/downtime>` starts and ends. If you specify **n** (none) as an option, the contact will not receive any type of host notifications.

service_notification_options
  This directive is used to define the service states for which notifications can be sent out to this contact. Valid options are a combination of one or more of the following:

    * w = notify on WARNING service states
    * u = notify on UNKNOWN service states
    * c = notify on CRITICAL service states
    * r = notify on service recoveries (OK states)
    * f = notify when the service starts and stops :ref:`flapping <monitoring_features/flapping>`.
    * n = (none) : the contact will not receive any type of service notifications.

service_notification_commands
  This directive is used to define a list of the *short names* of the :ref:`commands <monitoring_objects/command>` used to notify the contact of a *service* problem or recovery. Multiple notification commands should be separated by commas. All notification commands are executed when the contact needs to be notified. The maximum amount of time that a notification command can run is controlled by the :ref:`notification_timeout <configuration/core#notification_timeout>` option.

email
  This directive is used to define an email address for the contact. Depending on how you configure your notification commands, it can be used to sendout an alert email to the contact. Under the right circumstances, the $CONTACTEMAIL$ :ref:`macro <monitoring_features/macros>` will contain this value.

pager
  This directive is used to define a pager number for the contact. It can also be an email address to a pager gateway (i.e. *pagejoe@pagenet.com* ). Depending on how you configure your notification commands, it can be used to send out an alert page to the contact. Under the right circumstances, the $CONTACTPAGER$ :ref:`macro <monitoring_features/macros>` will contain this value.

address*x*
  Address directives are used to define additional “addresses" for the contact. These addresses can be anything - cell phone numbers, instant messaging addresses, etc. Depending on how you configure your notification commands, they can be used to send out an alert o the contact. Up to six addresses can be defined using these directives (*address1* through *address6*). The $CONTACTADDRESS*x*$ :ref:`macro <monitoring_features/macros>` will contain this value.

can_submit_commands
  This directive is used to determine whether or not the contact can submit :ref:`external commands <monitoring_features/external_commands>` to Alignak from the WebUI. Values:

    * 0 = don't allow contact to submit commands
    * 1 = allow contact to submit commands.

is_admin
  This directive is used to determine whether or not the contact can see all objects in the User Interface and if he/she can send commands from the UI. Values:

    * 0 = normal user, can see all objects he is in contact
    * 1 = allow contact to see all objects

min_business_impact
  This directive is use to define the minimum business criticity level of a service/host the contact will be notified. Please see :ref:`root_problems_and_impacts <alignak_features/problems-and-impacts>`  for more details.

    * 0 = less important
    * 1 = more important than 0
    * 2 = more important than 1
    * 3 = more important than 2
    * 4 = more important than 3
    * 5 = most important
