# Ansible

An Ansible playbook for the Deep Security Agent. This playbook allows you to easily deploy the Deep Security Agent as well as take some common operations from the Agent.


## Table of Contents

* [Requirements](#requirements)
* [Install](#install)
* [Usage](#usage)
* [Support](#support)
* [Contribute](#contribute)


## Requirements

* Ansible
* Deep Security Manager
* Deep Security Agent

All of the tasks below assume you have a working Deep Security infrastructure. The Deep Security Agents (which these playbooks help you manage) do the heavy lifting, but the Deep Security Manager gives the orders. 


## Install

You can either use [Ansible-Galaxy](https://galaxy.ansible.com/deep-security/deep-security-agent) or Git.

### Download from Ansible-Galaxy
Enter the command below.
```bash
ansible-galaxy install deep-security.deep-security-agent
```

Then you can use the [operation](#usage) to deploy Deep Security Agent.

To deploy agents with a single-tenant Deep Security Manager:
```yaml
- hosts: all
  roles:
    - role: deep-security.deep-security-agent
      operation: deploy
      dsm_agent_download_hostname: deep.security.manager.host
      dsm_agent_download_port: 4119
      dsm_agent_activation_hostname: deep.security.manager.host
      dsm_agent_activation_port: 4120
      policy_id: 1
      group_id: 1
      force_reactivation: false
```

To deploy agents with a multi-tenant Deep Security Manager or Deep Security as a Service:
```yaml
- hosts: all
  roles:
    - role: deep-security.deep-security-agent
      operation: deploy
      dsm_agent_download_hostname: app.deepsecurity.trendmicro.com
      dsm_agent_download_port: 443
      dsm_agent_activation_hostname: agents.deepsecurity.trendmicro.com
      dsm_agent_activation_port: 443
      tenant_id: 111A111A-1A1A-11AA-AAA-11AA11111111
      token | tenant_password: 111A111A-1A1A-11AA-AAA-11AA11111111
      policy_id: 1
      group_id: 1
      force_reactivation: false
```

### Clone from Git
Enter the command below.
```bash
git clone git@github.com:deep-security/ansible.git <folder-name>
```

If you cloned the Ansible script from this repository, we suggest you have the following folder structure for the playbook and role:

```
project
│   playbook.yaml
└───roles
    └───<folder-name>
        │   LICENSE
        │   README.md
        └───defaults
        └───meta
        └───tasks
```

Then you can use the [operation](#usage) to deploy Deep Security Agent.

To deploy agents with a single-tenant Deep Security Manager:
```yaml
- hosts: all
  roles:
    - role: <folder-name>
      operation: deploy
      dsm_agent_download_hostname: deep.security.manager.host
      dsm_agent_download_port: 4119
      dsm_agent_activation_hostname: deep.security.manager.host
      dsm_agent_activation_port: 4120
      policy_id: 1
      group_id: 1
      force_reactivation: false
```

To deploy agents with a multi-tenant Deep Security Manager or Deep Security as a Service:
```yaml
- hosts: all
  roles:
    - role: <folder-name>
      operation: deploy
      dsm_agent_download_hostname: app.deepsecurity.trendmicro.com
      dsm_agent_download_port: 443
      dsm_agent_activation_hostname: agents.deepsecurity.trendmicro.com
      dsm_agent_activation_port: 443
      tenant_id: 111A111A-1A1A-11AA-AAA-11AA11111111
      token | tenant_password: 111A111A-1A1A-11AA-AAA-11AA11111111
      policy_id: 1
      group_id: 1
      force_reactivation: false
```

## Usage
Below are operations you can perform with this role. For required variables, see [Variables](#variables).

Operation | Description | Variables
------------|-------------|-------------------
deploy | The <code>deploy</code> task includes the <code>install</code> and <code>activate</code> playbooks internally. | dsm_agent_download_hostname <br /> dsm_agent_download_port <br /> dsm_agent_activation_hostname	 <br/> dsm_agent_activation_port <br/> policy_id/policy_name (Optional) <br/> group_id (Optional) <br/> force_reactivation (Optional) <br/><br/> **For multi-tenancy only:** <br/> tenant_id <br /> token/tenant_password
install | The install task downloads and installs the Deep Security Agent. The installation is skipped if an Agent of the same version is already installed. If a newer version of Deep Security Installer is already installed, then the version is upgraded. | dsm_agent_download_hostname <br/> dsm_agent_download_port
activate | The activate task activates the Deep Security Agent by registering it in Trend Micro Deep Security Manager. By default, activation is skipped if the Agent is already activated, unless the force_reactivation attribute is set to true. | dsm_agent_activation_hostname <br/> dsm_agent_activation_port <br/> policy_id/policy_name (Optional) <br/> group_id (Optional) <br/> force_reactivation (Optional) <br/><br/> **For multi-tenancy only:** <br/> tenant_id <br/> token/tenant_password
set-policy-by-id | Change the Agent's policy via policy id | policy_id
set-policy-by-name | Change the Agent's policy via policy name | policy_name
check-in-with-manager | Ask the Agent to contact the Manager now. | N/A
create-diagnostic-package | Generate an Agent diagnostic package. | N/A
create-integrity-baseline | Rebuild the integrity monitoring baseline on the computer. | N/A
run-recommendation-scans | Initiate a recommendation scan on the computer. | N/A
scan-for-integrity-changes | Scan for changes for integrity monitoring | N/A
scan-for-malware | Initiate a manual anti-malware scan | N/A
update-configuration | Instruct the Deep Security Manager to perform a "Send Policy" operation. | N/A

### Variables

Key | Type | Description | Sample
----|------|-------------|--------
action | String | Name of the operation to be performed **(deprecated&mdash;please use operation instead)** | See [Operations](#operations)
operation | String | Name of the operation to be performed | See [Operations](#operations)
dsm_agent_activation_hostname | String | The hostname for the Agents to communicate with once deployed. For Marketplace and software deployments, this is typically the same hostname as 'dsm_agent_download_hostname'. | agents.deepsecurity.trendmicro.com
dsm_agent_activation_port | Integer | The port used for the Agent heartbeat (the regular communication). For Marketplace and software deployments, the default is 4120. | 443
dsm_agent_download_hostname | String | The hostname of the Deep Security Manager. | app.deepsecurity.trendmicro.com
dsm_agent_download_port | Integer | The port to connect to the Deep Security Manager to download the Agents. This is typically the same port as the one used to access the Deep Security Manager administration interface. | 443
force_reactivation | Boolean | Force re-activation even if the Deep Security Agent has already been activated. | false
group_id | String | The Deep Security ID assigned to the computer group and applied to the Agents on activation. | 1
policy_id | String | ID of the Deep Security Policy to be assigned to the Agents. | 1
policy_name | String | Name of the Deep Security Policy to be assigned to the Agents. <br /> **Will be ignored if policy_id has been set.** | Base Policy
tenant_id | String | In a multi-tenant installation (like Deep Security as a Service), this identifies the tenant account to register the Agent with. <br /> **Multi-tenancy only** | 111A111A-1A1A-11AA-AAA-11AA11111111
token/tenant_password | String | In a multi-tenant installation (like Deep Security as a Service), this identifies the tenant account to register the Agent with. In latest Deep Security Manager, "tenant_password" has been replaced with "token". "tenant_password" has been kept for backwards compatibility.<br /> **Multi-tenancy only** | 111A111A-1A1A-11AA-AAA-11AA11111111


## Support

This is an Open Source community project supported by the Deep Security 
team. 

For issues with Deep Security in general, please 
[contact Trend Micro Support](https://success.trendmicro.com/). 
Tutorials, feature-specific help, and other information about Deep Security 
is available from the [Deep Security Help Center](https://help.deepsecurity.trendmicro.com/). 

For bug reports or feature requests for this project specifically, please 
[open an issue](../issues). Please be specific about what you're 
trying to do, your system, and steps to reproduce the problem.
You are welcome to [contribute](#contribute).


## Contribute

We accept contributions from the community. To submit changes:

1. Fork this repository.
1. Create a new feature branch.
1. Make your changes.
1. Submit a pull request with an explanation of your changes or additions.

We will review and work with you to release the code. For further help, 
please contact the Trend Micro open source support team at deepsecurityopensource@trendmicro.com.
