Kubernetes
=========

A role to provision a kubernetes cluster on ubuntu servers in few minutes.

Requirements
------------

This role is to be executed on multiple ubuntu distribution based servers, [Focal Fossa](https://releases.ubuntu.com/20.04/) or [Bionic Beaver](https://releases.ubuntu.com/18.04/) or [Xenial Xerus](https://releases.ubuntu.com/16.04/).

Role Variables
--------------

This role requires several variables within `vars/main.yml` and `defaults/main.yml` as well as `extra-vars` at runtime.

- ***NETWORK_CIDR:*** Take care that your Pod network must not overlap with any of the host networks, this parameter depends on your CNI plugin.
- ***ADDON_URI:*** You must deploy a Container Network Interface (CNI) based Pod network add-on so that your Pods can communicate with each other. Cluster DNS (CoreDNS) will not start up before a network is installed, defaults to [flannel](https://github.com/coreos/flannel-cni).
- ***control_plane:*** Flag to discover the control plane via Ip address and port.
- ***token:*** Flag used for establishing bidirectional trust between a node joining the cluster and a control-plane node.
- ***discovery_token_ca_cert_hash:*** Flag to validate the public key of the root certificate authority (CA) presented by the Kubernetes Control Plane.

**Note:** There are two tags depending on whether you `initiate` the control plane or `join` minions.

Example Playbook
----------------

First we run the command in comment on the playbook below on the master node (control plane) to initiate the cluster:

```yml
  ---
  - hosts: master
    roles:
      - arsalen.kubernetes
  ...
  # ansible-playbook book.yml -i hosts --skip-tags join
```

Once finished, we will use the generated parameters ***controle_plane***, ***token*** and ***discovery_token_ca_cert_hash*** (displayed on stdout) to join other nodes.

Second we run the command in comment on the playbook below on the slave nodes (minions) to link each to the control plane:

```yml
  ---
  - hosts: slaves
    roles:
      - arsalen.kubernetes
  ...
  # ansible-playbook book.yml -i hosts --skip-tags initiate --extra-vars "control_plane=<control:plane> token=<to.ken> discovery_token_ca_cert_hash=<sha256:discovery-token-ca-cert-hash>"
```

**Note:** The hosts should be one `master` and multiple `slaves`, below is an example of an inventory file:

```ini
[master]
xxx.xxx.xxx.xx0 ansible_ssh_user=master-node  ansible_ssh_pass=master-node  ansible_become=yes  ansible_become_pass=master-node

[slaves]
xxx.xxx.xxx.xx1 ansible_ssh_user=slave-node-one  ansible_ssh_pass=slave-node-one  ansible_become=yes  ansible_become_pass=slave-node-one
xxx.xxx.xxx.xx2 ansible_ssh_user=slave-node-two  ansible_ssh_pass=slave-node-two  ansible_become=yes  ansible_become_pass=slave-node-two
```

License
-------

BSD

Author Information
------------------

@arsalen ([github](https://github.com/Arsalen), [medium](https://medium.com/@arsalen), [linkedin](https://www.linkedin.com/in/arsalen-hagui-506979123/))
