## Configuring Azure Pipeline to Manage Conjur Policy

<p>Azure Devops Only supports Host + API Key. JWT Tokens are not currently available</p>

### Synopsis
The pipeline will use `git diff-tree` to establish a list of committed files, ignoring deleted files. This list will be ordered via the numerical ordering convention and providing a path to test and apply to conjur.

### Requirements
- Host / Api key
  - The host used in automation must have access to the CONJUR_VAULT_PATH and CONJUR_HOST_PATH. Please refer to the [cloud example](policy/01_ado-automation_cloud.yml) policy to configure Conjur Cloud or [enterprise example](policy/01_ado-automation_ent.yml) policy to configure Conjur Enterprise.
- Conjur URL
  - Supported Deployments: Cloud/Enterprise/OSS
- Policy Branch for Hosts

### Folder Conventions
Folders should be organized to represent the `policy` branch or organized by function. These files should not be in the `root` directory of the repository. 
These can be organized by application, where the 01_ and 02_ files are nested under an application folder:
```
root
  |- APP1234
    |- 01_host.yml
    |- 02_acls.yml
```
These can also be organized by `policy` branch:
```
root
  |- apps
    |- 01_app1234.yml
  |- acls
    |- 02_app1234.yml 
```
Pipeline automation will receive the full path via `git diff-tree`, and is not relevant to handling policy loads. Organize at your discretion.

### File Conventions
We will be using a numerical prefix to identify files:
- 01_
  - A 01_ prefixed file designates a host resource to be created on commit. 
  - This file can also handle annotations updates. *Note: Annotations cannot be deleted via policy updates.*
- 02_
  - A 02_ prefixed file designates a host resource granting acl safe access on commit. 
- d_
  - A d_ prefixed file designates a host resource to be deleted on commit. 

