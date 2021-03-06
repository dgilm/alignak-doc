.. _monitoring_features/downtime:

========================================
Problems, acknowledgements and downtimes
========================================


Introduction 
------------

.. image:: /_static/images///official/images/downtime.png
   :scale: 90 %

When an host/service problem is detected by Alignak, some actions are possible with Alignak to manage the problems.

allows you to schedule downtime periods for hosts and service that you're monitoring. This is useful in the event that you actually know you're going to be taking a server down for an upgrade, etc.


Downtime vs Acknowledge
-----------------------

Acknowledging a problem for an host or service is simply an information that you are aware of the problem that Alignak alerted for. Acknowledging an host or service problem will make it appear as acknowledged in the Alignak Web UI and the future notifications for the same state of the problem will be disabled.

**Note** that acknowledging an host problem will also acknowledge all its services problems.

If the acknowledge is "sticky", the acknowledgement will remain until the host/service returns to an UP/OK state. Otherwise the acknowledgement will automatically be removed when the host changes state whatever the new state is.

If the "notify" option is set for the acknowledge, a notification will be sent out to the host/service contacts indicating that the current host problem has been acknowledged. A notification will also be sent when the acknowledge is unset.

**Note** that the acknowledgements are always persistent with Alignak (not as the Nagios legacy acknowledgements).

Scheduling downtime
-------------------

You can schedule downtime with :ref:`external commands <monitoring_features/external_commands>` or with the Alignak Web UI.

Once you schedule a downtime for an host or service, Alignak will add a comment to that host/service indicating that it is scheduled for downtime during the period of time you indicated.

When the downtime period starts, Alignak will acknowledge the host and all its services that are currently problems. And when the period of downtime exits, Alignak will automatically delete the comment that it added.


Fixed vs. flexible downtime
---------------------------

When you schedule a downtime for an host or service through the web interface you'll be asked if the downtime is fixed or flexible. Here's an explanation of how "fixed" and "flexible" downtime differs:

"Fixed" downtime starts and stops at the exact start and end times that you specified when you scheduled it. Okay, that was easy enough...

"Flexible" downtime is intended for times when you know that a host or service is going to be down for X minutes (or hours), but you don't know exactly when that'll start. When you schedule a flexible downtime, Alignak will start the scheduled downtime sometime between the start and end times you specified. The downtime will last for as long as the duration you specified when you scheduled it.

This assumes that the host or service for which you scheduled flexible downtime either goes down (or becomes unreachable) or goes into a non-OK state sometime between the start and end times you specified. The time at which a host or service transitions to a problem state determines the time at which Alignak
actually starts the downtime. The downtime will then last for the duration you specified, even if the host or service recovers before the downtime expires.

This is done for a very good reason. As we all know, you might think you've got a problem fixed, but then have to restart a server ten times before it actually works right.


Triggered downtime
------------------

When scheduling host or service downtime you have the option of making it "triggered" downtime. What is a triggered downtime? With triggered downtime the start of the downtime is triggered by the start of some other scheduled host or service downtime.

This is extremely useful if you're scheduling downtime for a large number or hosts or services and the start time of the downtime period depends on the start time of another downtime entry.

For instance, if you schedule a flexible downtime for a particular host (because its going down for maintenance), you might want to schedule triggered downtime for all of that hosts's "children".


How scheduled downtime affects notifications?
---------------------------------------------

When a host or service is in a period of scheduled downtime, Alignak will not allow normal notifications to be sent out for the host or service. However, a ``DOWNTIMESTART`` notification will be sent out for the host or service, which will serve to put any admins on notice that they won't receive upcoming problem alerts.

When the scheduled downtime is over, Alignak will allow normal notifications to be sent out for the host or service again. A ``DOWNTIMEEND`` notification will be sent out notifying contacts that the scheduled downtime is over, and they will start receiving normal alerts again.

If the scheduled downtime is cancelled prematurely (before it expires), a ``DOWNTIMECANCELLED`` notification will be sent out to the appropriate contacts.


.. _monitoring_features/maintenance_period:

Maintenance period
------------------

Sometimes you may need to define a recurring downtime period for an host or service.

Let's imagine that you have some regularly scheduled maintenance operations on this host. When your maintenance is happening, your checks will probably raise some problems and all the monitoring alert and  notification stuff will process... to avoid this, you may define some exclusions in the host check period to avoid checking this host/service when you scheduled your maintenance operations.

The *maintenance period* is an easier solution than the time periods with exclusions. Simply define a time period with your maintenance period schedule and set this time period as your host/service ``maintenance_period``. It will be considered the same as a scheduled downtime, so (unlike exclusions in your host/service check period) the checks will still be run during the maintenance period but the problems notifications will not be raised.


Overlapping scheduled downtime
------------------------------

I like to refer to this as the "Oh crap, it's not working" syndrome. You know what I'm talking about. You take a server down to perform a "routine" hardware upgrade, only to later realize that the OS drivers aren't working, the RAID array blew up, or the drive imaging failed and left your original disks useless to the world.

Moral of the story is that any routine work on a server is quite likely to take three or four times as long as you had originally planned...

Let's take the following scenario:

  - You schedule downtime for host A from 7:30pm-9:30pm on a Monday
  - You bring the server down about 7:45pm Monday evening to start a hard drive upgrade
  - After wasting an hour and a half battling with SCSI errors and driver incompatibilities, you finally get the machine to boot up
  - At 9:15 you realize that one of your partitions is either hosed or doesn't seem to exist anywhere on the drive
  - Knowing you're in for a long night, you go back and schedule additional downtime for host A from 9:20pm Monday evening to 1:30am Tuesday Morning.

If you schedule overlapping periods of downtime for a host or service (in this case the periods were 7:40pm-9:30pm and 9:20pm-1:30am), Alignak will wait until the last period of scheduled downtime is over before it allows notifications to be sent out for that host or service. In this example notifications would be suppressed for host A until 1:30am Tuesday morning.
