// parameters
// ----------
// gitHubProject
// gitBranch
// gitTag
// luarocksApiKey

    
def getProperties() {
  return readProperties file: 'source/jenkinsfile.properties'
}
node ('master') {

  stage ('Checkout') {
    dir('source') {
      deleteDir()
      checkout([
        $class: 'GitSCM',
        branches: [[name: '${gitBranch}']],
        doGenerateSubmoduleConfigurations: false,
        extensions: [],
        gitTool: 'Default',
        submoduleCfg: [],
        userRemoteConfigs: [[url: 'ssh://git@github.com/${gitHubProject}.git']]
      ])
    }
  }

  stage ('Set Version') {
    def properties = getProperties()
    def packageName = properties['luarocksPackage']
    def rockFile = "${packageName}-${gitTag}.rockspec"
    dir('source') {
      sh """
git checkout -B version-${gitTag}
git mv ${packageName}-VERSION.rockspec ${rockFile}
sed -i "s/VERSION/${gitTag}/g" ${rockFile}
sed -i "s/GITHUB_PROJECT/${gitHubProject}/g" ${rockFile}
      """
    }
  }

  stage ('Tag') {
    def properties = getProperties()
    dir('source') {
      sh '''
git config --global user.email "$properties[gitUserEmail]"
git config --global user.name "$properties[gitUserName]"
git commit -a -m "Version ${gitTag}"
git tag -f -a ${gitTag} -m "Version ${gitTag}"
git push --force origin ${gitTag}
      '''
    }
  }

  stage ('Upload') {
    sh 'luarocks upload *.rockspec --api-key=${luarocksApiKey}'
  }

}
