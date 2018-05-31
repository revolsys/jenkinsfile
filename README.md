# Jenkins Luarocks Release

A Jenkinsfile build pipeline to tag and release a luarocks project.  

## Configure Project

### Add jenkinsfile.properties

```
gitUserEmail=user@somewhere.com
gitUserName=Your Name
luarocksPackage=my-lua-module
```

### Rockspec file

Name the rockspec file <package>-VERSION.rockspec (e.g. my-lua-module-VERSION.rockspec).

NOTE: The string VERSION not the actual version number must be used. It's a placeholder
that is replaced by the script.

NOTE: The package must be the same as the luarocksPackage specified in the jenkinsfile.properties. 

Here is an example .rockspec file:

NOTE: Use the actual strings VERSION and GITHUB_PROJECT as shown. Anything else can be modified.

```
package = "my-lua-module"
version = "VERSION"
supported_platforms = {"linux", "macosx"}
source = {
  url = "git://github.com/GITHUB_PROJECT",
  tag = "VERSION"
}
description = {
  summary = "...",
  license = "Apache-2.0"
}
dependencies = {
  "lua ~> 5.1"
}
build = {
  type = "builtin",
  modules = {
    :
  }
}
```


## Install luarocks

1. Install luarocks on the Jenkins server
2. Install the dkjson library `luarocks install dkjson`

## Install Jenkins Plugins

Install the following plugin to the Jenkins installation.

Pipeline Utility Steps

## Create Jenkins job
If not already setup create a Jenkins pipeline job with the following parameters

Pipeline name: [project name]-release

Discard old builds:
  Max # of builds to keep: 2

This Project is parameterized: Yes
  String Parameter
    Name: gitHubProject
    Default value: <org>/<project> (e.g. revolsys/kong-plugin-upstream-auth-basic)
  Choice Parameter
    Name: gitBranch
    Choices: master (more can be added if used)
  String Parameter
    Name: gitTag
    Trim the string: yes
  Password Parameter
    Name: luarocksApiKey
    Default value: A luarocks.org api Key

Definiion: Pipeline script from SCM
  SCM: git
  Repositories:
    Repository URL: git@github.com:revolsys/jenkinsfile.git
    Credentials: A personal access token credential for a github.com user with write permission for the repository
  Branches to build: */jenkins-luarocks-release
  Script path: Jenkinsfile
  Lightweight checkout: Yes
  
## Running Jenkins job

The jenkins job will update the version in the source code, tag the release in github and deploy to luarocks

1. Click Build with Parameters on the job.
2. Select the gitBranch (e.g. master).
3. Enter the gitTag (e.g. 1.0.0-0) which is the version number of the release.
4. Enter the luarocksApiKey (if not set as a default).
5. Click build.
