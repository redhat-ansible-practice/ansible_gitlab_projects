# genie-gitlab-projects
## Description
A role to create new repositories (projects) in Gitlab, along with pre-populated default content, and protected/unprotected branches if specified.
## Variables
| Variable Name | Default Value | Required | Description |
|:---:|:---:|:---:|:---:|
|`gl_url`|""|yes|URL to Gitlab Server|
|`gl_validate_certs`|"false"|no|Whether or not to validate the Gitlab server's SSL certificates.  Options are "true" or "false".|
|`gl_user`|""|yes|API User to Access Gitlab with|
|`gl_token`|""|yes|Gitlab Impersonation Token for making API Calls. Should be stored in an Ansible Vault located in vars/gitlab-secrets.yml.|
|`gl_secrets`|True|no|Whether or not to load vaulted credentials from vars/gitlab-secrets.yml.  If set to `False`, you must provide the `gl_token` in another way (i.e. via playbook vars, group_vars, host_vars or a **vault included with `include_vars` from your main playbook.**)|
|`gl_prj_name`|""|yes|Name to create the Gitlab Project as.|
|`gl_prj_desc`|""|no|Description of the Gitlab repository to create.|
|`gl_prj_group`|""|yes|Gitlab group/namespace to create the project/repository in.  See `gitlab-group` role if you need to create one.|
|`gl_prj_files`|"README.md"|no|List of files to upload to the repository after project creation.  The filename list must point to files that exist in this role's files directory. Files in sub-directories can be specified in the list like `- "mysubdir/filename.txt"`|
|`gl_prj_def_branch`|"master"|no|Name of the default Gitlab project branch.|
|`gl_prj_branches`|[]|no|List of branches to create in the repository.  Each item must have a name field (corresponds to the branch name) and a protect field, with a boolean value (whether or not to protect this branch).  You must use the `gl_prj_files` list, which will commit the file you specify before you can create branches.|
|`gl_prj_visibility`|"private"|no|Defines if the repository/project is private, internal, or public|
|`gl_prj_wiki`|"true"|no|Whether or not to enable a wiki on your repository. Options are "true" or "false".|
|`gl_prj_snippets`|"true"|no|Whether or not to enable snippets on your repository.  Options are "true" or "false".|
|`gl_prj_issues`|"true"|no|Whether or not to enable issue tracking on the project/repository.  Options are "true" or "false".|
|`gl_prj_merge`|"true"|no|Whether or not to enable merge requests.  Options are "true" or "false".|

***It is recommended to make use of `no_log: True` on an include of this role to prevent exposing your impersonation API token***
### Example use of `gl_prj_branches` variable:
```yaml
---
gl_prj_branches:
  - name: "master"
    protect: True
  - name: "test"
    protect: True
  - name: "dev"
    protect: False  
```
### Example use of `gl_prj_files` variable:
```yaml
---
gl_prj_files:
  - "README.md"
  - "stuff/test.txt"
```
### Returned Variable: `gl_prj_url`
The `gl_prj_url` variable is set and can be used outside of this role for cases when you need to access the URL of the specified/newly created `gl_prj_name` (Gitlab Project Name).  If you are running on a custom SSL port (non 443) you will either need to manually override this (if you want to use the same variable name) in another playbook, or set your own URL variable.  If you have enabled `private_role_vars = yes` in your `ansible.cfg` this variable will not be usable outside of the role's context.
## Playbook Examples
### Standard Role Usage
```yaml
---
- hosts: all
  roles:
    - role: "genie-gitlab-projects"
      gl_url: "https://mygitlab.foo.bar"
      gl_validate_certs: "false"
      gl_user: "my_api_user"
      gl_token: "{{ my_vaulted_api_token }}"
      gl_prj_name: "AnsiblePlaybooks"
      gl_prj_desc: "Collection of Ansible Playbooks to automate stuff."
      gl_prj_group: "Ansible"
      gl_prj_files:
        - "README.md"
        - "demo/example.yml"
        - "demo/README.md"
      gl_prj_branches:
        - name: "master"
          protect: True
        - name: "test"
          protect: True
        - name: "dev"
          protect: False
      gl_prj_visibility: "internal"
      gl_prj_wiki: "false"
      gl_prj_snippets: "false"
      gl_prj_issues: "true"
      no_log: True #Please do this to protect your API token
```
## Author  
[Andrew J. Huffman](mailto:ahuffman@redhat.com)
