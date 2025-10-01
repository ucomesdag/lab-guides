Agent-Based Install
===================
Notes on agent-based install.

.. seealso:: For detailed documentation about the agent-based installer see: `Preparing 
  to install with the Agent-based Installer`_.

.. _Preparing to install with the Agent-based Installer: https://docs.redhat.com/en/documentation/openshift_container_platform/latest/html/installing_an_on-premise_cluster_with_the_agent-based_installer/preparing-to-install-with-agent-based-installer

.. important:: The installation will fail without configuring an NTP source. Make sure you 
  either set **dhcp option 42** or define a ntp-server in your install-config.yaml 
  at ``platform.baremetal.additionalNTPServers``.

Installation
------------

#. Example minimal installation configuration  with the use of a mirror

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

Adding Operators
________________

https://docs.redhat.com/en/documentation/openshift_container_platform/4.19/html/operators/administrator-tasks#olm-installing-operator-from-operatorhub-using-cli_olm-adding-operators-to-a-cluster

More
~~~~