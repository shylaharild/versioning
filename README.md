# DEVOPS VERSIONING

DevOps scripts and templates are an integral part of the Infrastructure and Application Deployment at PoleStar. As such a means of Version control is required so we can keep track of devops code against live infrastructure.

## Install bump2version

The main ingredient for versioning is `bump2version`. This must be installed in the local computer along with git. You can download and install the latest version of this software from the Python package index (PyPI) as follows

```
pip install --upgrade bump2version
```

Make sure that the pip version is the latest one in your local system or you can update using `pip install --upgrade pip`.
Tim unless of course you have a version lock dependancy....

## How to create versions

The ideal versioning for DevOps repository is to use version numbers when tagging on the master branch. However, there are multiple applications depending on the CloudFormation templates and deployment scripts which are living in this repository. The process to manage the application changes is to create a branch and commit those changes in it. Once the changes are tested, Pull Request (PR) will be created and post review, it will be merged to master. Elevating the process a bit more and making things easier for developers who rely on these DevOps files, the following can be done.

Version on Master:
-----
The master branch will have a normal numeric version (`major.minor.patch`) incremented based on `major`, `minor` or `patch` depending on the changes made to the codebase. For example, the initial version will be `v0.0.0`. This will be incremented when a branch is merged with master along with its changes. The version post merge will change from `v0.0.0` to `v0.0.1` or `v0.1.0` or even `v1.0.0` as mentioned earlier.

Version on Branch:
-----
The versions on branch will be done along with the version number from master branch at the time it was created and appeneded with the application name followed by the regular counter. For example, the version on a feature/application branch for `Screening` application will look like `v0.0.0-screening0`. Here, the version `v0.0.0` is from the master branch taken when the feature/application branch was created. Once the PR gets merged, the version on the master will be incremented based on the changes going into the codebase. (TD - break the app<#>)

There are two things important to create the versions.
 - Bumpversion config
 - Version File

Bumpversion config
----
Bumpversion configuration gives the information on how to do the versioning in the repository. The configuration also specifies the files to be updated with the version that is decided for the changes in the codebase. The below is the contents in the configuration file.

```
[bumpversion]
current_version = 0.0.0
commit = True
tag = True
parse = (?P<major>\d+)\.(?P<minor>\d+)\.(?P<patch>\d+)(\-(?P<application>[a-z]+)(?P<rc>\d+))?
serialize =
	{major}.{minor}.{patch}-{application}{rc}
	{major}.{minor}.{patch}

[bumpversion:part:application]

[bumpversion:part:rc]

[bumpversion:file:METADATA]
```

The serialize gives the opportunity tag the version as either `v0.0.0` or `v0.0.0-devops0`.  (TD I really need to see this devops0 devops1 thing - comms doesnt use rc and I am not a fan of Release Candidate Ive always thought rc was amaturish)

Version File
-----
The version file can be a file living in the root directory of the github repository or a `METADATA` file. As of now, this is going to be a METADATA file and this can be extended to other required files. TD - needs to be confirmed.

The sample METADATA file can contain the following.

```
[meta]
version = 0.0.0
long_name = Repository for DEVOPS AWS
description = DevOps AWS code repository.
name = devops-aws
```

TD - this relly needs a lot of work


## Commands to use   SH REWRITE

The following are the commands that can be used to do a version bump. This must be executed after adding and commiting the changes into the local repository and before pushing it to the Github.
=======
## Process to follow

Versioning is a manual process and it is DevOps' responsibility to version the changes going into the repository. The choice of incrementing the `major` or `minor` or `patch` is entirely dependent on the decision made along side with the type of changes made to the codebase. However, there is a basic process to be followed.

### All master branches have only numbers

As mentioned in the title, the master branch must have only the numbers as its version tags. This is to make sure that the version used from master branch is compatible to run the entire estate of the application at Pole Star. Example, `## Bump version: 0.0.0 → 1.0.0` when you run `bump2version major` on master.

The main process to follow is as given below
 - Initial version is created by running `bump2version --new-version 0.0.0 rc`. This can also be created by hardcoding the version number in the files and bumpversion config
 - Once a PR is merged, run `bump2version major` to give new major version `## Bump version: 0.0.0 → 1.0.0`
 - Once a PR is merged, run `bump2version minor` to give new minor version `## Bump version: 0.0.0 → 0.1.0`
 - Once a PR is merged, run `bump2version patch` to give new patch version `## Bump version: 0.0.0 → 0.0.1`

### Branches must have application name on it

The process that is followed in the DevOps is to create a branch for any changes in the template or deployment scripts for an application. While that branch can live for longer time, it is essential to tag the version on it so that it can be utilised by the CI jobs or by the infrastructure while building the servers. In order to differentiate the master versions from branch, the version will have the number from the master appended with application name and a numeric counter.

Example, `## Bump version: 0.0.0 → 0.0.0-devops0` when you run `bump2version --new-version 0.0.0-devops0 rc` on the branch. When another tag created, this will be `## Bump version: 0.0.0-devops0 → 0.0.0-devops1` when you run `bump2version rc`.

The main process to follow is as given below
 - Create a branch using the command `git checkout -b <branch-name>`
 - Add, commit and push the changes to the branch
 - Also, push the branch to the remote repository
 - Use `bump2version --new-version 0.0.0-devops0 rc` to create the tag version on the branch
 - Use `bump2version rc` to increment the tag version on the branch
 - Once the PR is merged, use `bump2version major` or `bump2version minor` or `bump2version patch` to create a new version on master

## Commands to use

The following are the commands that can be used to do the version bump. This must be executed after adding and commiting the changes into the local repository and pushing it to the Github. You can always use `--dry-run --verbose` if you're trying to check what the command is doing.

To add and commit changes
----
```
user$ git add <path-to-file>/<file-name>

user$ git commit -m "<message-to-commit-the-changes>"
```

Bump patch version:
----
```
bump2version patch
```
Example result will be `## from 0.0.0 to 0.0.1 `

Bump minor version:
----
```
bump2version minor
```
Example result will be `## from 0.0.0 to 0.1.0 `

Bump major version:
----
```
bump2version major
```
Example result will be `## from 0.0.0 to 1.0.0 `

Bump new version - force a new number counter:
----
The version is `v0.0.0` and the new version will be `v2.0.0`. The following is the command to be executed.
```
bump2version --new-version 2.0.0 rc     rc again -
```
Example result will be `## from 0.0.0 to 2.0.0 `. The `rc` is mentioned as a `part` in the bumpversion which is nothing but to increment the counter

Bump application version:
----
```
bump2version --new-version 0.0.0-devops0 rc
```
Example result will be `## from 0.0.0 to 0.0.0-devops0 `

Bump application version - increment rc counter:
----
```
bump2version rc
```
Example result will be `## from 0.0.0-devops0 to 0.0.0-devops1 `

After creating the version tag, it is mandatory to push the tags to the Github repository.

```
user$ git push --tags
```

If you have a new branch, try pushing the branch first before creating any tags. However, if you have created the tag, then do the following.

```
user$ git push --set-upstream origin <branch-name>

user$ git push --tags
```

## How to use this in Continuous Integration  TD really need to see this in action as it will break all of our infrastructure.

DevOps Versioning is critical to the success of any attempt at InfrastructureAsCode, which encompases Continous Integration, Automated Testing etc.  

To help developers in their Continuous Integration (CI) pipeline, when configuring the CI job, the following should be done while checking out the DevOps AWS repository.

In the Source Code Management, select the Multiple SCMs option. Provide the following for the first repository details.

| Key               | Value                                        |
|-------------------|----------------------------------------------|
| Repository URL    | git@github.com:polestarglobal/devops-aws.git |
| Credentials       | polestar-deploy                              |
| Refspec           | +refs/tags/*:refs/remotes/origin/tags/*      |
| Branch Specifier  | */$DEVOPS_VERSION                            |

![SCM-CI-JENKINS](https://github.com/polestarglobal/devops-aws/blob/master/cloudformation/assets/Config_DevOps_Version_CI.png)

The above mentioned `$DEVOPS_VERSION` is a parameter which must be defined in the General Section of the CI Job. The second repository details is similar but it is to checkout the application's repo to build the package.
