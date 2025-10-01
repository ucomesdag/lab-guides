###################
Command Cheat Sheet
###################
Some commands that come in handy.

Download collections in a disconnected environment
==================================================

Example requirements.yaml:

  .. code-block:: yaml

    collections:
      - community.general
      - name: kubernetes.core
        version: 6.2.0
      - name: redhat.openshift
        version: 5.0.0
      - name: redhat.satellite
        version: 5.7.0
      - name: redhat.satellite_operations
        version: 3.0.0

Example ansible.cfg :

  .. code-block:: cfg

    [defaults]
    inventory = ./inventory 
    collections_paths = ./collections
    
Download the archival tarballs:

  .. code-block:: shell

    $ ansible-galaxy collection download -r requirements.yaml -p ./my_downloads

Install from archival tarballs:

  .. code-block:: shell

    $ ansible-galaxy collection install kubernetes-core-6.2.0.tar.gz -p collections/
