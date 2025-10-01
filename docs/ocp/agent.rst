###################
Agent-Based Install
###################
Notes on agent-based install.

.. seealso:: For detailed documentation about the agent-based installer see: `Preparing 
  to install with the Agent-based Installer`_.

.. _Preparing to install with the Agent-based Installer: https://docs.redhat.com/en/documentation/openshift_container_platform/latest/html/installing_an_on-premise_cluster_with_the_agent-based_installer/preparing-to-install-with-agent-based-installer

.. important:: The installation will fail without configuring an NTP source. Make sure you 
  either set *dhcp option 42* or define a ntp-server with ``additionalNTPSources`` in your agent-config.yaml.

Configuration
=============

#. Create the working directory:

   .. code-block:: yaml

    clusterconfigs/
    ├── agent-config.yaml
    └── install-config.yaml

#. Example minimal installation configuration with the use of a mirror:

   .. code-block:: yaml
    :caption: install-config.yaml
    :emphasize-lines: 16-17

    apiVersion: v1
    metadata:
      name: ocp
    baseDomain: lab
    controlPlane:
      name: master
      replicas: 3
      architecture: amd64
      hyperthreading: Enabled
    compute:
    - name: worker
      replicas: 0
      architecture: amd64
      hyperthreading: Enabled
    networking:
      machineNetwork:
      - cidr: 172.16.10.0/24
      networkType: OVNKubernetes
    platform:
      none: {}
    pullSecret: '{"auths":{"quay.lab:8443":{"auth":"JGFwcDVFJVE...lKVJE0M0UzM="}}}'
    sshKey: |
      ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAg2b2kPZ...amnpRXmVlU9sCYZyF09xMllbb9uBA
    imageDigestSources:
    - mirrors:
      - quay.lab:8443/openshift/release
        source: quay.io/openshift-release-dev/ocp-v4.0-art-dev
    - mirrors:
      - quay.lab:8443/openshift/release-images
        source: quay.io/openshift-release-dev/ocp-release
    additionalTrustBundle: |
      -----BEGIN CERTIFICATE-----
      ...
      -----END CERTIFICATE-----

   .. code-block:: yaml
    :caption: agent-config.yaml
    :emphasize-lines: 6

    apiVersion: v1beta1
    kind: AgentConfig
    metadata:
      name: ocp
    rendezvousIP: 172.16.10.13
    bootArtifactsBaseURL: http://172.16.10.210:8080/coreos


Customize
=========

Create the ``openshift`` directory in your working directory:

.. code-block:: yaml

  clusterconfigs/
  ├── agent-config.yaml
  ├── install-config.yaml
  └── openshift

All configuration yaml files need to go in the ``openshift`` directory.

Customize Ignition
------------------

To generate machineconfiguration yaml from the butane files run the following command:

.. code-block:: shell

  $ butane <file>.bu -o clusterconfigs/openshift/<file>.yaml


Add LVM Storage
~~~~~~~~~~~~~~~

.. code-block:: yaml
  :caption: 98-master-lvmstorage-partitioning.bu

  variant: openshift
  version: 4.19.0
  metadata:
    labels:
      machineconfiguration.openshift.io/role: master
    name: 98-master-lvmstorage-partitioning
  storage:
    disks:
    - device: /dev/nvme0n1
      partitions:
      - number: 1
        should_exist: true
      - number: 2
        should_exist: true
      - number: 3
        should_exist: true
      - number: 4
        resize: true
        size_mib: 120000
      - label: lvmstorage
        number: 5
        size_mib: 0
        start_mib: 0
        wipe_partition_entry: true


Set CoreOS password
~~~~~~~~~~~~~~~~~~~

.. code-block:: yaml
  :caption: 98-master-lvmstorage-partitioning.bu

  # Generate passwd hash with: openssl passwd -6
  variant: openshift
  version: 4.19.0
  metadata:
    labels:
      machineconfiguration.openshift.io/role: master
    name: 98-master-core-pass
  passwd:
    users:
      - name: core 
        password_hash: $6$w/ky6UftmkNn3qdG$t3x...1E9AsXvv1ihBSYdk.b5v3c6vreIj8Lbb.


Adding Operators
----------------

https://docs.redhat.com/en/documentation/openshift_container_platform/4.19/html/operators/administrator-tasks#olm-installing-operator-from-operatorhub-using-cli_olm-adding-operators-to-a-cluster


Instalation
===========