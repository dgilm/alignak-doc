.. _monitoring_objects/service:

===================
Service Definition
===================

A service definition is used to identify a “service" that runs on a host. The term “service" is used very loosely. It can mean an actual service that runs on the host (POP, "SMTP", "HTTP", etc.) or some other type of metric associated with the host (response to a ping, number of logged in users, free disk space, etc.). The different arguments to a service definition are outlined below.


Syntax
======

Bold variables are required, while others are optional.
Emphasized variables are Alignak extensions with reference to the Nagios legacy definition.


========================================== ======================================
define service{
**host_name**                              ***host_name***
hostgroup_name                             *hostgroup_name*
**service_description**                    ***service_description***
display_name                               *display_name*
servicegroups                              servicegroup_names
is_volatile                                [0/1]
**check_command**                          ***command_name***
initial_state                              [o,w,u,c]
initial_output                             *output*
**max_check_attempts**                     **#**
**check_interval**                         **#**
**retry_interval**                         **#**
active_checks_enabled                      [0/1]
passive_checks_enabled                     [0/1]
**check_period**                           ***timeperiod_name***
obsess_over_service                        [0/1]
check_freshness                            [0/1]
freshness_threshold                        #
event_handler                              *command_name*
event_handler_enabled                      [0/1]
low_flap_threshold                         #
high_flap_threshold                        #
flap_detection_enabled                     [0/1]
flap_detection_options                     [o,w,c,u]
process_perf_data                          [0/1]
retain_status_information                  [0/1]
retain_nonstatus_information               [0/1]
**notification_interval**                  **#**
first_notification_delay                   #
notification_period                        ***timeperiod_name***
notification_options                       [w,u,c,r,f,s]
notifications_enabled                      [0/1]
**contacts**                               ***contacts***
**contact_groups**                         ***contact_groups***
stalking_options                           [o,w,u,c]
notes                                      *note_string*
notes_url                                  *url*
action_url                                 *url*
poller_tag                                 *poller_tag*
reactionner_tag                            *reactionner_tag*
duplicate_foreach                          *$MACRO$*
service_dependencies                       *host,service_description*
business_impact                            [0/1/2/3/4/5]
maintenance_period                         *timeperiod_name*
host_dependency_enabled                     [0/1]
labels                                     *labels*
business_rule_output_template              *template*
business_rule_smart_notifications          [0/1]
business_rule_downtime_as_ack              [0/1]
business_rule_host_notification_options    [d,u,r,f,s]
business_rule_service_notification_options [w,u,c,r,f,s]
snapshot_enabled                           [0/1]
snapshot_command                           *command_name*
snapshot_period                            *timeperiod_name*
snapshot_criteria                          [w,c,u]
snapshot_interval                          #
priority                                   *priority*
}
========================================== ======================================


Example
=======

::

  define service{
         host_name               linux-server
         service_description     check-disk-sda1
         check_command           check-disk!/dev/sda1
         max_check_attempts      5
         check_interval          5
         retry_interval          3
         check_period            24x7
         notification_interval   30
         notification_period     24x7
         notification_options    w,c,r
         contact_groups          linux-admins
         poller_tag              DMZ
         icon_set                server
         }


Variables
=========

host_name
  This directive is used to specify the *short name(s)* of the :ref:`host(s) <monitoring_objects/host>` that the service "runs" on or is associated with. Multiple hosts should be separated by commas.

hostgroup_name
  This directive is used to specify the *short name(s)* of the :ref:`hostgroup(s) <monitoring_objects/hostgroup>` that the service "runs" on or is associated with. Multiple hostgroups should be separated by commas. The hostgroup_name may be used instead of, or in addition to, the host_name directive.

  This is possible to define "complex" hostgroup expression with the following operators :

    * & : it's use to make an AND betweens groups
    * | : it's use to make an OR betweens groups
    * ! : it's use to make a NOT of a group or expression
    * , : it's use to make a OR, like the | sign.
    * ( and ) : they are use like in all math expressions.

  For example the above definition is valid

  ::

    hostgroup_name=(linux|windows)&!qualification,routers


  This service wil be apply on hosts that are in the routers group or (in linux or windows and not in qualification group).

service_description
  This directive is used to define the description of the service, which may contain spaces, dashes, and colons (semicolons, apostrophes, and quotation marks should be avoided). No two services associated with the same host can have the same description. Services are uniquely identified with their *host_name* and *service_description* directives.

display_name
  This directive is used to define an alternate name that should be displayed in the web interface for this service. If not specified, this defaults to the value you specify for the *service_description* directive.

  The current CGIs do not use this option, although future versions of the web interface will.

servicegroups
  This directive is used to identify the *short name(s)* of the :ref:`servicegroup(s) <monitoring_objects/servicegroup>` that the service belongs to. Multiple servicegroups should be separated by commas. This directive may be used as an alternative to using the *members* directive in :ref:`servicegroup <monitoring_objects/servicegroup>` definitions.

is_volatile
  This directive is used to denote whether the service is "volatile". Services are normally *not* volatile. More information on volatile service and how they differ from normal services can be found :ref:`here <monitoring_features/volatile_services>`. Value: 0 = service is not volatile, 1 = service is volatile.

check_command
  This directive is used to specify the *short name* of the :ref:`command <monitoring_objects/command>` that Alignak will run in order to check the status of the service. The maximum amount of time that the service check command can run is controlled by the :ref:`service_check_timeout <configuration/core#service_check_timeout>` option.
  There is also a command with the reserved name "bp_rule". It is defined internally and has a special meaning. Unlike other commands it mustn't be registered in a command definition. It's purpose is not to execute a plugin but to represent a logical operation on the statuses of other services. It is possible to define logical relationships with the following operators :

    * & : it's use to make an AND betweens statuses
    * | : it's use to make an OR betweens statuses
    * ! : it's use to make a NOT of a status or expression
    * , : it's use to make a OR, like the | sign.
    * ( and ) : they are used like in all math expressions

  For example the following definition of a business process rule is valid

  ::

    bp_rule!(websrv1,apache | websrv2,apache) & dbsrv1,oracle

  If at least one of the apaches on servers websrv1 and websrv2 is OK and if the oracle database on dbsrv1 is OK then the rule and thus the service is OK

initial_state
  By default Alignak will assume that all services are in PENDING state when in starts. You can override the initial state for a service by using this directive. Valid options are:

    * **o** = OK
    * **w** = WARNING
    * **u** = UNKNOWN
    * **c** = CRITICAL.

initial_output
  As of the initial state, the initial check output may also be overridden by this directive.

max_check_attempts
  This directive is used to define the number of times that Alignak will retry the service check command if it returns any state other than an OK state. Setting this value to 1 will cause Alignak to generate an alert without retrying the service check again.

check_interval
  This directive is used to define the number of “time units" to wait before scheduling the next “regular" check of the service. “Regular" checks are those that occur when the service is in an OK state or when the service is in a non-OK state, but has already been rechecked **max_check_attempts** number of times. Unless you've changed the ``interval_length`` global variable from the default value of 60, this number will mean minutes.

retry_interval
  This directive is used to define the number of “time units" to wait before scheduling a re-check of the service. Services are rescheduled at the retry interval when they have changed to a non-OK state. Once the service has been retried **max_check_attempts** times without a change in its status, it will revert to being scheduled at its “normal" rate as defined by the **check_interval** value. Unless you've changed the ``interval_length`` global variable from the default value of 60, this number will mean minutes.

active_checks_enabled
  This directive is used to determine whether or not active checks of this service are enabled. Values:

    * 0 = disable active service checks
    * 1 = enable active service checks.

passive_checks_enabled
  This directive is used to determine whether or not passive checks of this service are enabled. Values:

    * 0 = disable passive service checks
    * 1 = enable passive service checks.

check_period
  This directive is used to specify the short name of the :ref:`time period <monitoring_objects/timeperiod>` during which active checks of this service can be made.

check_freshness
  This directive is used to determine whether or not freshness checks are enabled for this service. Values:

    * 0 = disable freshness checks
    * 1 = enable freshness checks

freshness_threshold
  This directive is used to specify the freshness threshold (in seconds) for this service. If you set this directive to a value of 0, Alignak will determine a freshness threshold to use automatically.

event_handler
  This directive is used to specify the *short name* of the :ref:`command <monitoring_objects/command>` that should be run whenever a change in the state of the service is detected (i.e. whenever it goes down or recovers). Read the documentation on :ref:`event handlers <monitoring_features/event_handlers>` for a more detailed explanation of how to write scripts for handling events. The maximum amount of time that the event handler command can run is controlled by the :ref:`event_handler_timeout <configuration/core#event_handler_timeout>` option.

event_handler_enabled
  This directive is used to determine whether or not the event handler for this service is enabled. Values:

    * 0 = disable service event handler
    * 1 = enable service event handler.

low_flap_threshold
  This directive is used to specify the low state change threshold used in flap detection for this service. More information on flap detection can be found :ref:`here <monitoring_features/flapping>`. If you set this directive to a value of 0, the program-wide value specified by the :ref:`low_service_flap_threshold <configuration/core#low_service_flap_threshold>` directive will be used.

high_flap_threshold
  This directive is used to specify the high state change threshold used in flap detection for this service. More information on flap detection can be found :ref:`here <monitoring_features/flapping>`. If you set this directive to a value of 0, the program-wide value specified by the :ref:`high_service_flap_threshold <configuration/core#high_service_flap_threshold>` directive will be used.

flap_detection_enabled
  This directive is used to determine whether or not flap detection is enabled for this service. More information on flap detection can be found :ref:`here <monitoring_features/flapping>`. Values:

    * 0 = disable service flap detection
    * 1 = enable service flap detection.

flap_detection_options
  This directive is used to determine what service states the :ref:`flap detection logic <monitoring_features/flapping>` will use for this service. Valid options are a combination of one or more of the following :

    * **o** = OK states
    * **w** = WARNING states
    * **c** = CRITICAL states
    * **u** = UNKNOWN states.

process_perf_data
  This directive is used to determine whether or not the processing of performance data is enabled for this service. Values:

    * 0 = disable performance data processing
    * 1 = enable performance data processing

notification_interval
  This directive is used to define the number of “time units" to wait before re-notifying a contact that this service is *still* in a non-OK state. Unless you've changed the :ref:`interval_length <configuration/core#interval_length>` directive from the default value of 60, this number will mean minutes. If you set this value to 0, Alignak will *not* re-notify contacts about problems for this service - only one problem notification will be sent out.

first_notification_delay
  This directive is used to define the number of “time units" to wait before sending out the first problem notification when this service enters a non-OK state. Unless you've changed the :ref:`interval_length <configuration/core#interval_length>` directive from the default value of 60, this number will mean minutes. If you set this value to 0, Alignak will start sending out notifications immediately.

notification_period
  This directive is used to specify the short name of the :ref:`time period <monitoring_objects/timeperiod>` during which notifications of events for this service can be sent out to contacts. No service notifications will be sent out during times which is not covered by the time period.

notification_options
  This directive is used to determine when notifications for the service should be sent out. Valid options are a combination of one or more of the following:

    * **w** = send notifications on a WARNING state
    * **u** = send notifications on an UNKNOWN state
    * **c** = send notifications on a CRITICAL state
    * **r** = send notifications on recoveries (OK state)
    * **f** = send notifications when the service starts and stops :ref:`flapping <monitoring_features/flapping>`
    * **s** = send notifications when :ref:`scheduled downtime <monitoring_features/downtime>` starts and ends
    * **n** (none) as an option, no service notifications will be sent out. If you do not specify any notification options, Alignak will assume that you want notifications to be sent out for all possible states

  If you specify **w,r** in this field, notifications will only be sent out when the service goes into a WARNING state and when it recovers from a WARNING state.

notifications_enabled
  This directive is used to determine whether or not notifications for this service are enabled. Values:

    * 0 = disable service notifications
    * 1 = enable service notifications.

contacts
  This is a list of the *short names* of the :ref:`contacts <monitoring_objects/contact>` that should be notified whenever there are problems (or recoveries) with this service. Multiple contacts should be separated by commas. Useful if you want notifications to go to just a few people and don't want to configure :ref:`contact groups <monitoring_objects/contactgroup>`. You must specify at least one contact or contact group in each service definition.

contact_groups
  This is a list of the *short names* of the :ref:`contact groups <monitoring_objects/contactgroup>` that should be notified whenever there are problems (or recoveries) with this service. Multiple contact groups should be separated by commas. You must specify at least one contact or contact group in each service definition. If there is no contact or contact_groups defined, it's host's contact/contactgroup wich is used by object_inheritance. 

stalking_options
  This directive determines which service states "stalking" is enabled for. Valid options are a combination of one or more of the following :

    * o = stalk on OK states
    * w = stalk on WARNING states
    * u = stalk on UNKNOWN states
    * c = stalk on CRITICAL states

More information on state stalking can be found :ref:`here <monitoring_features/stalking>`.

notes
  This directive is used to define an optional string of notes pertaining to the service. If you specify a note here, you will see the it in the User Interface (when you are viewing information about the specified service).

notes_url
  This directive is used to define an optional URL that can be used to provide more information about the service. If you specify an URL, you will see a red folder icon in the CGIs (when you are viewing service information) that links to the URL you specify here. Any valid URL can be used. If you plan on using relative paths, the base path will the  same as what is used to access the CGIs (i.e. ///cgi-bin/shinken///). This can be very useful if you want to make detailed information on the service, emergency contact methods, etc. available to other support staff.

action_url
  This directive is used to define an optional URL that can be used to provide more actions to be performed on the service. If you specify an URL, you will see a red “splat" icon in the CGIs (when you are viewing service information) that links to the URL you specify here. Any valid URL can be used. If you plan on using relative paths, the base path will the  same as what is used to access the CGIs (i.e. ///cgi-bin/shinken///).

poller_tag
  This directive is used to define the poller_tag of this command. This parameter may be defined, in order of precedence, on a`command`, a `host` or a `service`. If a poller tag is set, only pollers holding the same tag will handle the corresponding action.

  By default there is no poller_tag, so all untagged pollers can take it.

reactionner_tag
  This directive is used to define the reactionner_tag of this command. This parameter may be defined, in order of precedence, on a`command`, a `host` or a `service`. If a reactionner tag is set, only reactionners holding the same tag will handle the corresponding action.

  By default there is no reactionner_tag, so all untagged reactionners can take it.

duplicate_foreach
  This is used to generate several service with only one service declaration.
  Alignak understands this statement as : "Create a service for each key in the variable".
  Usually, this statement come with a "$KEY$" string in the service_description (to have a different name) and in the check_command (you want also a different check)
  Moreover, one or several variables can be associated to each key. Then, values can be used in the service definition with $VALUE$ or $VALUEn$ macros.

::

  define host {
    host_name    linux-server
    ...
    _partitions   var $(/var)$, root $(/)$
    _openvpns   vpn1 $(tun1)$$(10.8.0.1)$, vpn2 $(tun2)$$(192.168.3.254)$
    ...
  }

  define service{
         host_name               linux-server
         service_description     disk-$KEY$
         check_command           check_disk!$VALUE$
         ...
         duplicate_foreach       _partitions
  }

  define service{
         host_name               linux-server
         service_description     openvpn-$KEY$-check-interface
         check_command           check_int!$VALUE1$
         ...
         duplicate_foreach       _openvpns
  }

  define service{
         host_name               linux-server
         service_description     openvpn-$KEY$-check-gateway
         check_command           check_ping!$VALUE2$
         ...
         duplicate_foreach       _openvpns
  }

service_dependencies
  This variable is used to define services that this service is dependent of for notifications. It's a comma separated list of services: host,service_description,host,service_description. For each service a service_dependency will be created with default values (notification_failure_criteria as 'u,c,w' and no dependency_period). For more complex failure criteria or dependency period you must create a service_dependency object, as described in :ref:`advanced dependency configuraton <monitoring_features/dependencies>`. The host can be omitted from the configuration, which means that the service dependency is for the same host.

  ::

    service_dependencies    hostA,service_descriptionA,hostB,service_descriptionB
    service_dependencies    ,service_descriptionA,,service_descriptionB,hostC,service_descriptionC

  By default this value is void so there is no linked dependencies. This is typically used to make a service dependent on an agent software, like an NRPE check dependent on the availability of the NRPE agent.

business_impact
  This variable is used to set the importance we gave to this service from the less important (0 = nearly nobody will see if it's in error) to the maximum (5 = you lost your job if it fail). The default value is 2.

maintenance_period
  Alignak-specific variable to specify a recurring downtime period. This works like a scheduled downtime, so unlike a check_period with exclusions, checks will still be made.

host_dependency_enabled
  This variable may be used to remove the dependency between a service and its parent host. Used for volatile services that need notification related to itself and not depend on the host notifications.

labels
  This variable may be used to place arbitrary labels (separated by comma character). Those labels may be used in other configuration objects such as :ref:`business rules <alignak_features/business_rules>` to identify groups of services.

business_rule_output_template
  Classic service check output is managed by the underlying plugin (the check output is the plugin stdout). For :ref:`business rules <alignak_features/business_rules>`, as there's no real plugin behind, the output may be controlled by a template string defined in business_rule_output_template directive.

business_rule_smart_notifications
  This variable may be used to activate smart notifications on :ref:`business rules <alignak_features/business_rules>`. This allows to stop sending notification if all underlying problems have been acknowledged.

business_rule_smart_notifications
  By default, downtimes are not taken into account by :ref:`business rules <alignak_features/business_rules>` smart notifications processing. This variable allows to extend smart notifications to underlying hosts or service checks under downtime (they are treated as if they were acknowledged).

business_rule_host_notification_options
  This option allows to enforce :ref:`business rules <alignak_features/business_rules>` underlying hosts notification options to easily compose a consolidated meta check. This is especially useful for business rules relying on grouping expansion.

business_rule_service_notification_options
  This option allows to enforce :ref:`business rules <alignak_features/business_rules>` underlying services notification options to easily compose a consolidated meta check. This is especially useful for business rules relying on grouping expansion.

snapshot_enabled
  This option allows to enable snapshots :ref:`snapshots <monitoring_features/snapshot>` on this element.

snapshot_command
  Command to launch when a snapshot launch occurs

snapshot_period
  Timeperiod when the snapshot call is allowed

snapshot_criteria
  List of states that enable the snapshot launch. Mainly bad states.

snapshot_interval
  Minimum interval between two launch of snapshots to not hammering the host, in interval_length units (by default 60s) :)

priority
  This options defines the service's priority regarding checks execution. When a poller is asking for new actions to execute to the scheduler, it will return the highest priority tasks first (the lower the number, the higher the priority). The `priority` parameter may be set, in order of ascending precedence, on a `command`, on a `host` and on a `service`. Priority defaults to `100`.
