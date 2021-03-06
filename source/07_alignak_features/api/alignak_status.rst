.. _alignak_features/alignak_status:
.. Built from the test_daemons_api.py unit test last run!

======================
Alignak overall status
======================
An Alignak overall status example:

::

    {
    "livestate": {
        "long_output": "broker-master - daemon is alive and reachable.\npoller-master - daemon is alive and reachable.\nreactionner-master - daemon is not reachable.\nreceiver-master - daemon is alive and reachable.\nscheduler-master - daemon is alive and reachable.", 
        "output": "Some of my daemons are not reachable.", 
        "perf_data": "'modules'=2 'timeperiods'=4 'services'=100 'servicegroups'=1 'commands'=10 'hosts'=13 'hostgroups'=5 'contacts'=2 'contactgroups'=2 'notificationways'=2 'checkmodulations'=0 'macromodulations'=0 'servicedependencies'=40 'hostdependencies'=0 'arbiters'=1 'schedulers'=1 'reactionners'=1 'brokers'=1 'receivers'=1 'pollers'=1 'realms'=1 'resultmodulations'=0 'businessimpactmodulations'=0 'escalations'=0 'hostsextinfo'=0 'servicesextinfo'=0", 
        "state": "up", 
        "timestamp": 1542611507
    }, 
    "name": "My Alignak", 
    "services": [
        {
            "livestate": {
                "long_output": "", 
                "output": "warning because some daemons are not reachable.", 
                "perf_data": "", 
                "state": "warning", 
                "timestamp": 1542611507
            }, 
            "name": "arbiter-master"
        }, 
        {
            "livestate": {
                "long_output": "Realm: All (True). Listening on: http://127.0.0.1:7772/", 
                "name": "broker_broker-master", 
                "output": "daemon is alive and reachable.", 
                "perf_data": "last_check=0.00", 
                "state": "ok", 
                "timestamp": 1542611507
            }, 
            "name": "broker-master"
        }, 
        {
            "livestate": {
                "long_output": "Realm: All (True). Listening on: http://127.0.0.1:7771/", 
                "name": "poller_poller-master", 
                "output": "daemon is alive and reachable.", 
                "perf_data": "last_check=0.00", 
                "state": "ok", 
                "timestamp": 1542611507
            }, 
            "name": "poller-master"
        }, 
        {
            "livestate": {
                "long_output": "Realm: All (True). Listening on: http://127.0.0.1:7769/", 
                "name": "reactionner_reactionner-master", 
                "output": "daemon is not reachable.", 
                "perf_data": "last_check=0.00", 
                "state": "warning", 
                "timestamp": 1542611507
            }, 
            "name": "reactionner-master"
        }, 
        {
            "livestate": {
                "long_output": "Realm: All (True). Listening on: http://127.0.0.1:7773/", 
                "name": "receiver_receiver-master", 
                "output": "daemon is alive and reachable.", 
                "perf_data": "last_check=0.00", 
                "state": "ok", 
                "timestamp": 1542611507
            }, 
            "name": "receiver-master"
        }, 
        {
            "livestate": {
                "long_output": "Realm: All (True). Listening on: http://127.0.0.1:7768/", 
                "name": "scheduler_scheduler-master", 
                "output": "daemon is alive and reachable.", 
                "perf_data": "last_check=0.00", 
                "state": "ok", 
                "timestamp": 1542611507
            }, 
            "name": "scheduler-master"
        }
    ], 
    "template": {
        "_templates": [
            "alignak", 
            "important"
        ], 
        "active_checks_enabled": false, 
        "alias": "My Alignak", 
        "notes": "", 
        "passive_checks_enabled": true
    }, 
    "variables": {}
}
