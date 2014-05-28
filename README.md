locust.io
=========

Set up a distributed instance of [locust.io][1].

[1]: http://locust.io/

You need to first set up your config to run the instances.

Create a file named `config.yaml` with contents like::

    locust-master:
        master: true
        host: http://url.prefix.to.test/
        locust_file: |
           # The contents of the locust_file.py to use.
           # See http://docs.locust.io/en/latest/writing-a-locustfile.html
    locust-slave:
        master: false
        locust_file: |
           # The same locust_file as above.

(Hopefully we'll be able to make specifying the `locust_file` in the slaves
unnecesarry soon.)

Now you need to deploy the charm twice and relate the master to
the slaves:

    juju deploy locustio locust-master --config config.yaml
    juju deploy locustio locust-slave --config config.yaml
    juju add-relation locust-master:slave locust-slave:master
    juju expose locust-master

Once the services has started you will be able to connect to port `8089` on
the public address of the master unit and use the web interface.

If you need to change the locust file, or the host that is being tested
you can update the `config.yaml` then run

   juju set locust-master --config config.yaml
   juju set locust-slave --config config.yaml

If you need more slaves for your testing you can run

  juju add-unit locust-slave

and use the `-n` option to add multiple units at once if you wish.

Leader election
---------------

Deploying the charm twice and making one the leader in config
would be unnecessary if the units could just elect a leader themselves.

Once that is supported natively by juju then the charm could be made
to use that.
