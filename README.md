# MicroShift Deployment Playbook

This Ansible playbook automates the installation and configuration of MicroShift on a Red Hat Enterprise Linux 9 (RHEL 9) host. It supports dynamic versioning and ensures all necessary dependencies and configurations are in place for a functional MicroShift environment.

Once you have deployed Microshift, add some GitOps magic with this [repo](https://github.com/turbra/microshift-homelab/).

## Features

- Dynamically specify the MicroShift version using `rpm_install_version`.
- Configures required Red Hat repositories.
- Installs MicroShift and its dependencies (`microshift-olm`, `microshift-gitops`).
- Sets up the kubeconfig file for the non-root user.
- Configures firewall rules for MicroShift.
- Enables and starts the MicroShift service.
- Adds the kubeconfig environment variable to the user's profile for seamless `oc` command usage.

## Prerequisites

1. A RHEL 9 host with a valid Red Hat subscription.
2. Ansible installed on the control node.
3. SSH access to the target host.
4. The playbook assumes the user defined in the inventory (ansible_user) has sudo privileges.

## Usage

### 1. Define Inventory

Edit the `inventory` file to specify the target host(s). For example:

```yaml
[all]
192.168.1.213 ansible_user=turbra
```
### 2. Set Variables

Define default variables in `vars/main.yml`. Example:
```yaml
rpm_install_version: 4.16
```

```yaml
baseDomain: microshift.example.com
```
You can override these values at runtime.

### 3. Run the Playbook

#### Run with a Specific Version
`ansible-playbook -i inventory playbook.yml -e rpm_install_version=4.16.* --ask-become-pass`

### 4. Verify the Setup

After the playbook completes:
1. Log into the target host.
2. Verify MicroShift is running:
   `systemctl status microshift`
3. Verify oc commands work:
   `oc get pods -A`

## Playbook Overview

### Tasks

1. Repository Configuration:
   - Enables the required Red Hat repositories for MicroShift.

2. Package Installation:
   - Installs MicroShift and related dependencies.

3. Firewall Configuration:
   - Configures firewall rules to allow required traffic.

4. Service Management:
   - Starts and enables the MicroShift service.

5. Kubeconfig Setup:
   - Copies the kubeconfig to the user's .kube/config.
   - Adds KUBECONFIG to the user's .bash_profile.

### Variables

- `rpm_install_version:` The version of MicroShift to install. Currently: 4.16.
- `baseDomain:` The base domain of the cluster. All managed DNS records will be sub-domains of this base.

