RackHD: local Vagrant based setup
==================================

This tutorial gets an instance of RackHD up and running on your local desktop or
laptop, so you can see the hosted API documentation and experiment with the APIs.

prerequisites
--------------

You will need to have `Vagrant`_ and `VirtualBox`_ installed on your machine to use
this tutorial.

.. _Vagrant: https://www.vagrantup.com/downloads.html
.. _Virtualbox: https://www.virtualbox.org/wiki/Downloads

what we're setting up
----------------------

.. image:: ../_static/vagrant_setup.jpg
     :height: 300
     :align: left

The Vagrant instance sets up a pre-installed RackHD VM that connects to one or more VMs
that represent managed systems. The target systems are simulated using PXE clients.

The RackHD VM has two network interfaces. One connects to the local machine via NAT (Network Address Translation)
and the second connects to the PXE VMs in a private network. The private network is used so that RackHD DHCP and
PXE operations are isolated from your local network.

The Vagrant setup also enables port forwarding that allows your localhost to access the RackHD instance:

- localhost:9090 redirects to rackhd:8080 for access to the REST API
- localhost:2222 redirects to rackhd:22 for SSH access

.. container:: clearer

   .. image :: ../_static/invisible.png

- Clone the RackHD repository

.. code::

    git clone https://github.com/rackhd/rackhd
    cd rackhd/example

- Download a RackHD vagrant instance

.. code::

    vagrant up dev

- Start the local instance

.. code::

    vagrant ssh dev -c "sudo nf start"

The logs from RackHD will show in the console window where you invoked this last
command. You can use control-c (^C) to stop the processes. Additionally you can
SSH into the local instance using the command ``vagrant ssh dev`` and destroy
this instance with ``vagrant destroy dev``. For more information on Vagrant,
please see the `Vagrant CLI documentation`_.

.. _Vagrant CLI documentation: https://www.vagrantup.com/docs/cli/


Accessing your local instance of RackHD
----------------------------------------

When RackHD is operational, the self-hosted API documentation should immediately
be available:

- 1.1 API documentation at http://localhost:9090/docs
- 2.0 and Redfish API documentation at http://localhost:9090/swagger-ui
- Included task documentation at http://localhost:9090/taskdoc
- a developer UI to live updates of RackHD at http://localhost:9090/ui

The self-hosted documentation for 2.0 and the Redfish API includes the ability to
invoke sample commands directly on your local instance through the documentation pages.

You can also interact with RackHD using ``curl`` from the commandline, or a tool
such as `Postman`_ in the browser.

.. _Postman: https://www.getpostman.com

With a brand new instance, you should be able to access the ``nodes/`` API endpoint
and see an empty list of nodes.

- ``curl http://localhost/api/2.0/nodes | python -m json.tool``

.. code-block:: JSON

    []

You can also view a list of all the built-in workflows

- ``curl http://localhost/api/2.0/workflows/graphs | python -m json.tool``

.. code-block:: JSON

    [
        {
            "friendlyName": "Arista Switch ZTP Discovery",
            "injectableName": "Graph.Switch.Discovery.Arista.Ztp",
            "tasks": [
                {
                    "label": "catalog-switch",
                    "taskDefinition": {
                        "friendlyName": "Catalog Arista Switch",
                        "implementsTask": "Task.Base.Linux.Commands",
                        "injectableName": "Task.Inline.Catalog.Switch.Arista",
                        "options": {
                            "commands": [
                                {
                                    "catalog": {
                                        "format": "json",
                                        "source": "version"
                                    },
                                    "downloadUrl": "/api/1.1/templates/arista-catalog-version.py"
                                }
                            ]
                        },
                        "properties": {}
                    }
                }
            ]
        },
        ...

Or review the list of all the built-in tasks available to be used in workflows

- ``curl http://localhost/api/2.0/workflows/tasks | python -m json.tool``

.. code-block:: JSON

    [
      {
        "friendlyName": "Boot LiveCD",
        "injectableName": "Task.Os.Boot.LiveCD",
        "implementsTask": "Task.Base.Os.Install",
        "options": {
          "profile": "boot-livecd.ipxe",
          "completionUri": "renasar-ansible.pub",
          "version": "livecd",
          "repo": "{{api.server}}/LiveCD/{{options.version}}"
        },
        "properties": {
          "os": {
            "linux": {
              "distribution": "livecd"
            }
          }
        }
      },
      ...

Adding a simulated server
---------------------------

The Vagrantfile included in the example setup includes a reference to simulated
server provide by the `InfraSim`_ project. You can download and boot this simulated
server, which includes an interface to IPMI as well as simulates the physical machine
with an internal VM.

.. _InfraSim: http://infrasim.readthedocs.io

By default, RackHD will PXE boot this instance, interrogate it, and then leave it alone.

- Set up the simulated server

.. code::

    vagrant up quanta_d51

This command will start up vagrant with the GUI console available. You can see
the Quanta d51 control with the vBMC quanta simulator by using VNC to connect
to 127.0.0.1:15901 (or 127.0.0.1 display 10001). You can log into the VM hosting
this simulation with the default credentials of username ``root``, and password ``root``.

The IPMI credentials that it is providing on ``closednet`` use the username ``admin``
and password ``admin``.

Once the node has been discovered by RackHD, you can see it through the API.

- ``curl http://localhost:9090/api/2.0/nodes | python -m json.tool``

.. code-block:: JSON

    [
        {
            "autoDiscover": "false",
            "id": "57967193a045ba7c0800207b",
            "identifiers": [],
            "name": "Enclosure Node QTFCJ05160195",
            "obms": [],
            "tags": [],
            "type": "enclosure"
        },
        {
            "autoDiscover": "false",
            "id": "5796707ce398ea85086363aa",
            "identifiers": [
                "52:54:be:ef:aa:ee"
            ],
            "name": "52:54:be:ef:aa:ee",
            "obms": [],
            "sku": null,
            "tags": [],
            "type": "compute"
        }
    ]

Resetting the demonstration
----------------------------

You can reset all of the demonstration by tearing down and setting up the vagrant
instances again::

    vagrant destroy -f
    vagrant up dev
    vagrant ssh dev -c "sudo nf start"


Resetting and updating the code to the latest master branch
------------------------------------------------------------

The demonstration instance of RackHD is installed from source, so it can also be
updated the latest version::

    vagrant destroy -f
    vagrant up dev
    vagrant ssh dev

And then within that virtual machine::

    cd ~/src
    ./scripts/clean_all.bash && ./scripts/reset_submodules.bash && ./scripts/link_install_locally.bash

.. WARNING::
    This downloads the latest code and reinstalls it all from source, which can take a few minutes.
