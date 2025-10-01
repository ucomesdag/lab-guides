###################
Command Cheat Sheet
###################
Some commands that come in handy.

Download the installer and RHCOS image
======================================

Download the installer for the OpenShift version that you want to install:

  .. code-block:: shell

    $ export OCP_VERSION=4.18.24
    $ export ARCH=$(uname -m)
    $ curl -k https://mirror.openshift.com/pub/openshift-v4/clients/ocp/$OCP_VERSION/openshift-install-linux.tar.gz -o openshift-install-linux.tar.gz
    $ tar zxvf openshift-install-linux.tar.gz
    $ chmod +x openshift-install

Download the RHCOS image:

  .. code-block:: shell

    $ export ISO_URL=$(./openshift-install coreos print-stream-json | grep location | grep $ARCH | grep iso | cut -d\" -f4)
    $ curl -L $ISO_URL -o rhcos-live.iso

Approve pending certificates
============================

.. code-block:: shell

  $ oc get csr -o name | xargs oc adm certificate approve

Monitoring cluster progression
==============================

.. code-block:: shell

  $ oc get nodes,mcp,co


Reset CoreOS password
=====================

#. Interrupt the boot sequence by smashing the arrow keys. 

#. Selecting the grub boot entry and hit ``e`` to edit it.
   
#. Edit the entry and add ``init=/bin/bash`` at the end of the line starting with 
   ``linux``:

   .. code-block:: shell
    :emphasize-lines: 4
        
    load_video
    set gfxpayload=keep
    insmod gzio
    linux ($root)/boot/ostree/rhcos-0507a2ecc42ce08f9f494c33d2099f6418508b795280daace3ce724c8d3b4bdd/vmlinuz-5.14.0-570.45.1.e19_6.x86_64 ignition.platform.id=metal ip=eno1: dhcp systemd.unified_cgroup_hierarchy=1 cgroup_no_v1="a11" psi=0 ostree=/ostree/boot.1/rhcos/0507a2ecc42ce08f9f494c33d2099f6418508b795280daace3ce724c8d3b4bdd/0 root=UUID=910678ff-f77e-4a7d-8d53-86f2ac47a823 rw rootflags=prjquota boot=UUID=02b83022-d8a5-4707-bbeb-2e82b7dd4078 init=/bin/bash
    initrd (sroot)/boot/ostree/rhcos-0507a2ecc42ce08f9f494c33d2099f6418508b795280daace3ce724c8d3b4bdd/initramfs-5.14.0-570.45.1.e19_6.x86_64.img

#. Hit ``ctrl + x`` to boot with the selected entry.

#. Run the following commands to reset the password and continue boot:

   .. code-block:: shell

    $ ls -Z /etc/shadow # run to show the correct context that needs to be set again
    $ passwd core
    $ chcon system_u:object_r:shadow_t:s0 /etc/shadow
    $ exec /usr/sbin/init

Bring a node down
=================

  .. code-block:: shell

    $ oc adm cordon <node_name>
    $ oc adm drain <node_name> --ignore-daemonsets=true --delete-emptydir-data --force
    $ oc debug node/<node_name>
    $ shutdown -h now
