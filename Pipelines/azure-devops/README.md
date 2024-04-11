## Configuring Azure Pipeline to Manage Conjur Policy

<p>Azure Devops Only supports Host + API Key. JWT Tokens are not currently available from Azure Dev Ops</p>

### Synopsis
The pipeline will use `git diff-tree` to establish a list of committed files, ignoring deleted files. This list will be ordered via the numerical ordering convention and providing a path to test and apply to Conjur. 

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
Pipeline automation will receive the full path via `git diff-tree`, and is not relevant to handling policy loads. *Organize at your discretion.*

### File Conventions
We will be using a numerical prefix to identify files:
- 01_
  - A 01_ prefixed file designates a host resource to be created on commit. 
  - This file can also handle annotations updates. *Note: Annotations cannot be deleted via policy updates.*
  - Examine the [example](pipeline/apps/t01_example.yml)
- 02_
  - A 02_ prefixed file designates a host resource granting acl safe access on commit. 
  - The pipeline will read the first line of the file, extracting the safe name and applying it to a static api path defined in the [pipeline](pipeline/azure-pipelines.yml#L7)
  - This file must have the safename as a comment in the first line of the file. Please follow the [example](pipeline/acls/t02_example.yml)
- d_
  - A d_ prefixed file designates a host resource to be deleted on commit. 

### Flow
1. Commit template to appropriate folder
2. Merge commits to main
3. Validate pipeline executed successfully
4. Validate resources in Conjur are as expected
