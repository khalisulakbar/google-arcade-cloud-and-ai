# Cloud IAM 

- [Introduction](iam-roles.md#introduction)


## Overview
> Cloud IAM is a service that provides access control for your Google Cloud resources. IAM allows developer to manage the permission with minimum fuss and high automation. With IAM Roles, developers doesnt need to directly grant users permissions. Instead, devs grant them roles (roles bundles one or more permissions).

There are two type or roles in IAM:
1. predefined roles -> created and maintaned by Google. 
2. custom roles -> With custom roles, developers can create their own roles by bundling one or more permissions and then grant the roles to users.

For custom roles, developers need `iam.roles.create` permission. By default, the owner of a project or an organization has this permission and can create and manage custom roles. Users who are not owners, including organization administrators, must be assigned either the Organization Role Administrator role (roles/iam.organizationRoleAdmin) or the IAM Role Administrator role (roles/iam.roleAdmin). 

Before you create a custom role, we want to know:
1. What permissions can be applied to a resource
2. What roles are grantable on a resource
3. What a role's metadata is

## 1. View the available permissions for a resource
```gcloud
gcloud iam list-testable-permissions //cloudresourcemanager.googleapis.com/projects/$DEVSHELL_PROJECT_ID
```

## 2. View the available roles for a resource
```gcloud
gcloud iam roles describe [ROLE_NAME]
```

for example, for roles/viewer, run the following command:
```gcloud
gcloud iam roles describe roles/viewer
```

## 3. View the grantable roles on resources
```gcloud
gcloud iam list-grantable-roles //cloudresourcemanager.googleapis.com/projects/$DEVSHELL_PROJECT_ID
```

## 4. Create a custom role
To create a custom role, we use `gcloud iam roles create`. There are two ways to create a custom role:

1. Provide a YAML file that contains the role definition
2. Specify the role definition using flags

Also, it is important to note that we must specify whether it applies to the organization level or project level by using the --organization [ORGANIZATION_ID] or --project [PROJECT_ID] flags.

### 1. Provide a YAML file that contains the role definition
first, we create a file by running this command: `nano role-definition.yaml`.

then we provide the YAML file, e.g.
```yaml
title: "Role Editor"
description: "Edit access for App Versions"
stage: "ALPHA"
includedPermissions:
- appengine.versions.create
- appengine.versions.delete
```

it follows the format like this:
```
title: [ROLE_TITLE]
description: [ROLE_DESCRIPTION]
stage: [LAUNCH_STAGE]
includedPermissions:
- [PERMISSION_1]
- [PERMISSION_2]
```

Each of the placeholder values is described below:

- [ROLE_TITLE] is a friendly title for the role, such as Role Viewer.
- [ROLE_DESCRIPTION] is a short description about the role, such as My custom role description.
- [LAUNCH_STAGE] indicates the stage of a role in the launch lifecycle, such as ALPHA, BETA, or GA.
- `includedPermissions` specifies the list of one or more permissions to include in the custom role, such as iam.roles.get.

next, we execute the following command:
```gcloud
gcloud iam roles create editor --project $DEVSHELL_PROJECT_ID \
--file role-definition.yaml
```

if the role created succesfully, the command will print the role name and its resource name.
```
Created role [editor].
description: Edit access for App Versions
etag: BwVs4O4E3e4=
includedPermissions:
- appengine.versions.create
- appengine.versions.delete
name: projects/qwiklabs-gcp-00-60a9f078b3e4/roles/editor
stage: ALPHA
title: Role Editor
```

### 2. Specify the role definition using flags
```gcloud
gcloud iam roles create viewer --project $DEVSHELL_PROJECT_ID \
--title "Role Viewer" --description "Custom role description." \
--permissions compute.instances.get,compute.instances.list --stage ALPHA
```

if the role created succesfully, the command will print the role name and its resource name.
```
Created role [viewer].
description: Custom role description.
etag: BwVs4PYHqYI=
includedPermissions:
- compute.instances.get
- compute.instances.list
name: projects/qwiklabs-gcp-00-60a9f078b3e4/roles/viewer
stage: ALPHA
title: Role Viewer
```

## 5. List the custom roles
Execute the following gcloud command to list custom roles, specifying either project-level or organization-level custom roles:

```gcloud
gcloud iam roles list --project $DEVSHELL_PROJECT_ID
```

also, you can list all predifined roles using the following command:
```
gcloud iam roles list
```

## 6. Update an existing custom role
Use the `gcloud iam roles update` command to update custom roles in one of two ways:

1. A YAML file that contains the updated role definition
2. Flags that specify the updated role definition

### 1. A YAML file that contains the updated role definition
fisrt, we get the current definition for the role to see what permissions are included in the role by running this command:
```
gcloud iam roles describe [ROLE_ID] --project $DEVSHELL_PROJECT_ID
```

replace [ROLE ID] with **editor**. it will look like this:
```
description: Edit access for App Versions
etag: BwYY2J57kA0=
includedPermissions:
- appengine.versions.create
- appengine.versions.delete
name: projects/qwiklabs-gcp-00-60a9f078b3e4/roles/editor
stage: ALPHA
title: Role Editor
```

second, we create a file by running this command: `nano new-role-definition.yaml`. and we copy the result from the previous step into the file. We also add the new permissions that we want to add to the role. e.g.

- storage.buckets.get
- storage.buckets.list

```
description: Edit access for App Versions
etag: BwYY2J57kA0=
includedPermissions:
- appengine.versions.create
- appengine.versions.delete
- storage.buckets.get
- storage.buckets.list
name: projects/qwiklabs-gcp-00-60a9f078b3e4/roles/editor
stage: ALPHA
title: Role Editor
```

finally, we execute the following command:
```
gcloud iam roles update [ROLE_ID] --project $DEVSHELL_PROJECT_ID \
--file new-role-definition.yaml
```

also,replace [ROLE ID] with **editor**.

once the command is executed, the result will be the updated role. e.g.
```
description: Edit access for App Versions
etag: BwYY2N-k2Rw=
includedPermissions:
- appengine.versions.create
- appengine.versions.delete
- storage.buckets.get
- storage.buckets.list
name: projects/qwiklabs-gcp-00-60a9f078b3e4/roles/editor
stage: ALPHA
title: Role Editor
```

### 2. Update a custom role using flags
For a list of all possible flags from the SDK reference documentation, see the topic [gcloud iam roles update](https://cloud.google.com/sdk/gcloud/reference/iam/roles/update).

Use the following flags to add or remove permissions:

1. --add-permissions: Adds one or more comma-separated permissions to the role.
2. --remove-permissions: Removes one or more comma-separated permissions from the role.

For example, to add permissions to the **viewer** role we created earlier, run the following command:
```
gcloud iam roles update viewer --project $DEVSHELL_PROJECT_ID \
--add-permissions storage.buckets.get,storage.buckets.list
```

when successful, the command will print the updated role. e.g.
```
description: Custom role description.
etag: BwYY2OrEotY=
includedPermissions:
- compute.instances.get
- compute.instances.list
- storage.buckets.get
- storage.buckets.list
name: projects/qwiklabs-gcp-00-60a9f078b3e4/roles/viewer
stage: ALPHA
title: Role Viewer
```

## 7. Disable a custom role
When a role is disabled, any policy bindings related to the role are inactivated, meaning that the permissions in the role will not be granted, even if you grant the role to a user.

to disable a custom role, run the following command:
```
gcloud iam roles update viewer --project $DEVSHELL_PROJECT_ID \
--stage DISABLED
```

the result will be like this:
```
description: Custom role description.
etag: BwYY2PPLPbE=
includedPermissions:
- compute.instances.get
- compute.instances.list
- storage.buckets.get
- storage.buckets.list
name: projects/qwiklabs-gcp-00-60a9f078b3e4/roles/viewer
stage: DISABLED
title: Role Viewer
```

notice how the `stage` is now set to `DISABLED`.

## 8. Delete a custom role
TO delete a custom role, run the following command:

for example, to delete role with [ROLE ID] = viewer, run the following command:
```
gcloud iam roles delete viewer --project $DEVSHELL_PROJECT_ID
```
once successful, the command will print the deleted role. e.g.
```
deleted: true
description: Custom role description.
etag: BwYY2PtGj-g=
includedPermissions:
- compute.instances.get
- compute.instances.list
- storage.buckets.get
- storage.buckets.list
name: projects/qwiklabs-gcp-00-60a9f078b3e4/roles/viewer
stage: DISABLED
title: Role Viewer
```

Also, if we check the list of roles with `gcloud iam roles list --project $DEVSHELL_PROJECT_ID`, we will see that the role has been deleted.

```
description: Edit access for App Versions
etag: BwYY2N-k2Rw=
name: projects/qwiklabs-gcp-00-60a9f078b3e4/roles/editor
title: Role Editor
```

**Note:**
After the role has been deleted, existing bindings remain, but are inactive. The role can be undeleted within 7 days. After 7 days, the role enters a permanent deletion process that lasts 30 days. After 37 days, the Role ID is available to be used again.

## 9. Restore a custom role
Within the 7 days window you can restore a role. Deleted roles are in a DISABLED state. To make it available again, update the --stage flag. Run the following command:

```
gcloud iam roles undelete viewer --project $DEVSHELL_PROJECT_ID
```

The result will be like this:
```
description: Custom role description.
etag: BwYY2Qd4sEQ=
includedPermissions:
- compute.instances.get
- compute.instances.list
- storage.buckets.get
- storage.buckets.list
name: projects/qwiklabs-gcp-00-60a9f078b3e4/roles/viewer
stage: DISABLED
title: Role Viewer
```

also, if we check the list of roles with `gcloud iam roles list --project $DEVSHELL_PROJECT_ID`, we will see that the role has been restored.
