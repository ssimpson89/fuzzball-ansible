# fuzzball-ansible deployment automation

`fuzzball-ansible` automates the process of deploying Fuzzball.
It can be used both as an Ansible collection and as an Ascender / AWX project.

## Prepare an inventory

This guide assumes that your host inventory is recorded in
`hosts.yaml`. Start a new inventory by copying from the provided
example.

    cp hosts.yaml-example hosts.yaml # customize hosts.yaml
    
If your hosts are not defined by name in DNS, `/etc/hosts`, or
`.ssh/config`, define their IP address with `ansible_host`.

For more general information, read [How to build your
inventory][ansible_inventory].

[ansible_inventory]: https://docs.ansible.com/ansible/latest/inventory_guide/intro_inventory.html

See the comments in `hosts.yaml-example` for more information on
available inventory variables.

## Playbooks

`fuzzball-ansible` provides ready-to-run playbooks that can be used to deploy Fuzzball into an existing environment.

The playbooks in `fuzzball-ansible` are configured to require the use of an explicit limit. (i.e., `ansible-playbook --limit`)

Example:

```shell

ansible-playbook ciq.fuzzball.deploy_fuzzball_orchestrate --limit fuzzball_controller --inventory hosts.yaml
```

### ciq.fuzzball.deploy_fuzzball_orchestrate

Deploy Fuzzball Orchestrate.
This playbook automatically deploys the Fuzzball operator into the target Kubernetes cluster to manage the Fuzzball Orchestrate deployment.

### ciq.fuzzball.deploy_fuzzball_substrate

Deploy Fuzzball Substrate.
Also configures an NFS client to access configuration published by Fuzzball Orchestrate.

### ciq.fuzzball.deploy_fuzzball_cli

Install the Fuzzball CLI.

### ciq.fuzzball.deploy_nfs_server

Deploy an NFS server and configure exports.
Fuzzball Orchestrate uses this share to publish configuration for Fuzzball Substrate to consume,
and Fuzzball Substrate uses this share to cache container images.
This share may also be used as backing storage for Fuzzball storage classes.

### ciq.fuzzball.deploy_rke2

Deploy a single-node RKE2 "cluster" as an installation target for Fuzzball Orchestrate.
Also installs the local path provisioner and metallb.

See also: `ciq.fuzzball.deploy_nfs_server`

## Notes

- Changes to interface zones cannot be made permanent because of
  configuration conflicts between cloud-init, NetworkManager, and
  firewalld. As such, these playbooks make ephemeral firewalld changes
  only, and must be re-run after reboot.
