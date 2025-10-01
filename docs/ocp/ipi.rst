#################
IPI-Based Install
#################
Notes on installer-provisioned infrastructure install.

Configuration
=============

.. code-block:: yaml
  :caption: install-config.yaml

  apiVersion: v1
  metadata:
    name: ocp
  baseDomain: lab
  controlPlane:
    name: master
    replicas: 3
    platform: {}
    architecture: amd64
    hyperthreading: Enabled
  compute:
  - name: worker
    replicas: 3
    platform: {}
    architecture: amd64
    hyperthreading: Enabled
  networking:
    machineNetwork:
    - cidr: 10.0.0.0/24
    networkType: OVNKubernetes
  platform:
    baremetal:
      apiVIPs: 
      - 10.0.0.10
      ingressVIPs:
      - 10.0.0.11
      provisioningNetwork: Disabled 
      hosts:
      - name: control-plane0
        role: master
        bmc:
          address: redfish-virtualmedia://172.16.10.100:8443/redfish/v1/Systems/1005
          disableCertificateVerification: true
          username: redfish
          password: password
        bootMACAddress: 56:6F:62:AA:10:07
      - name: control-plane1
        role: master
        bmc:
          address: redfish-virtualmedia://172.16.10.100:8443/redfish/v1/Systems/1006
          disableCertificateVerification: true
          username: redfish
          password: password
        bootMACAddress: 56:6F:62:AA:10:09
      - name: control-plane2
        role: master
        bmc:
          address: redfish-virtualmedia://172.16.10.100:8443/redfish/v1/Systems/1007
          disableCertificateVerification: true
          username: redfish
          password: password
        bootMACAddress: 56:6F:62:AA:10:11
      - name: compute0
        role: worker
        bmc:
          address: redfish-virtualmedia://172.16.10.100:8443/redfish/v1/Systems/1008
          disableCertificateVerification: true
          username: redfish
          password: password
        bootMACAddress: 56:6F:62:AA:10:13
      - name: compute1
        role: worker
        bmc:
          address: redfish-virtualmedia://172.16.10.100:8443/redfish/v1/Systems/1009
          disableCertificateVerification: true
          username: redfish
          password: password
        bootMACAddress: 56:6F:62:AA:10:15
      - name: compute2
        role: worker
        bmc:
          address: redfish-virtualmedia://172.16.10.100:8443/redfish/v1/Systems/1010
          disableCertificateVerification: true
          username: redfish
          password: password
        bootMACAddress: 56:6F:62:AA:10:17
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


Install
=======

.. code-block:: yaml 

  $ openshift-install --dir ~/clusterconfigs create install-config
  $ openshift-install --dir ~/clusterconfigs create cluster
  $ openshift-install --dir ~/clusterconfigs wait-for bootstrap-complete
  $ openshift-install --dir ~/clusterconfigs wait-for install-complete


  $ openshift-install --dir ~/clusterconfigs gather bootstrap
  $ openshift-install --dir ~/clusterconfigs debug destroy cluster


Troubleshooting
===============

.. code-block:: yaml

  sudo virsh console $(sudo virsh list --name | grep bootstrap)

  ssh 10.0.0.199 -l core

  openshift-install --dir ./clusterconfigs gather bootstrap --bootstrap 10.0.0.199

