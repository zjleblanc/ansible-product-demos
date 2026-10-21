# Domain Reference

Static facts about APD's seven domains that rarely change. This file does
**not** list individual demos -- discover those dynamically per
[SKILL.md](SKILL.md)'s "Discovering available demos" section, since new
demos are added over time.

## Domains and setup survey values

Each domain is set up in AAP by launching **APD ǀ Single demo setup** (or
**APD ǀ Multi-demo setup**) with the `demo` survey set to one of:

| Survey value | Domain folder | Covers |
|---|---|---|
| `linux` | `linux/` | RHEL ops, patching, compliance (OpenSCAP, DISA STIG), Insights/RHSM registration |
| `windows` | `windows/` | Windows ops, patching, Active Directory, Chocolatey, PowerShell/DSC |
| `cloud` | `cloud/` | AWS provisioning: VPCs, VMs, keypairs, networking, snapshots, cloud patching workflow |
| `network` | `network/` | Containerlab (Cisco NX-OS/IOS-XE) and Palo Alto firewall automation |
| `openshift` | `openshift/` | OpenShift Virtualization (CNV), EDA Controller install, Dev Spaces, GitLab |
| `infrastructure` | `infrastructure/` | ROSA lifecycle, config drift/EDA self-healing, Automation Orchestrator, OPA policy-as-code, Mattermost, HashiCorp Vault |
| `satellite` | `satellite/` | Red Hat Satellite registration, OpenSCAP via Satellite, content view publish/promote |

All setup runs through `common/setup.yml` first (shared inventory, groups,
labels) and then `<domain>/setup.yml`.

## Cross-domain dependencies

Flag these to the user before they try to run a demo against hosts that
don't exist yet:

| If the user wants... | They also need... |
|---|---|
| Linux or Windows ops/patching/compliance demos | **Deploy Cloud Stack in AWS** (`cloud` domain workflow) run first, to create target VMs and sync inventory into groups like `aws_rhel8`, `aws_rhel9`, `aws_win1`, `aws-dc` |
| Satellite demos | Satellite domain setup **auto-launches** Linux setup plus a `SETUP ǀ Satellite` job that configures Satellite itself (content views, lifecycle environments) |
| Windows Active Directory demos | The **Setup Active Directory Domain** workflow (in `windows` domain) provisions its own AWS domain controller + joined hosts -- no need to run Cloud Stack separately for this one |
| Infrastructure config drift demos | **Deploy Cloud Stack in AWS** for RHEL targets (e.g. `aws_rhel9`), then the config-drift stages (auditd -> Filebeat -> Kafka -> EDA) layer on top |
| Network (Containerlab) demos | The **Deploy Containerlab Stack** workflow (in `network` domain) provisions its own AWS hypervisor and lab topology |
| OpenShift CNV demos | The **CNV ǀ Infra Stack** workflow (in `openshift` domain) provisions the RHEL VMs inside OpenShift Virtualization |
| ROSA / Automation Orchestrator / OPA demos | These provision their own infra (ROSA clusters, OpenShift workloads) and generally don't depend on Cloud Stack |

## Common credential requirements per domain

Credentials are created as placeholders during domain setup; the user must
fill in real values in AAP under **Resources -> Credentials** before demos
will work. Check the `user_message:` block at the top of each
`<domain>/setup.yml` for the authoritative, current list -- this table is a
quick-reference summary:

| Domain | Credentials to configure |
|---|---|
| `cloud` | `AWS` (access/secret key); `RHSM Activation Key` (org ID + activation key) if auto-registering RHEL hosts |
| `linux` | `APD Machine Credential` (SSH private key, usually set during bootstrap); `RHSM Activation Key` / Insights org+activation key; Insights Inventory credential (Red Hat account); automation hub token for System Roles content |
| `windows` | `Kerberos` credential (for AD-joined demos); WinRM/machine credential for direct connections |
| `network` | Device credentials for Cisco/Palo Alto targets; `SSH Proxy` credential if connecting through a bastion; `AWS` for Containerlab hypervisor |
| `openshift` | `OpenShift Credential` (API token/kubeconfig for the target cluster) |
| `infrastructure` | `AWS`; `ROSA Token` (offline token from console.redhat.com/openshift/token/rosa) for ROSA demos; `OpenShift Credential` for OPA/Automation Orchestrator |
| `satellite` | Satellite username/password credential |

## Where to find post-setup instructions

Every `<domain>/setup.yml` has a `user_message:` key near the top -- read it
and translate it into a checklist for the user. It typically covers
credential values to fill in, URLs for obtaining tokens/keys, and any manual
one-time configuration the Ansible automation can't do for them.

## Inventory host group conventions

AWS-provisioned hosts land in dynamic inventory groups based on tags, commonly:
`aws_rhel8`, `aws_rhel9`, `aws_win1`, `aws-dc`, `reports` (HTML report
server), `aws_kafka`, `aws_mattermost`. Demo surveys often ask for a host
pattern (e.g. `_hosts: aws_rhel*`) -- point the user at these group names
when filling out survey prompts.
