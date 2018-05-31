// parameters
// ----------
// gitHubProject
// gitBranch
// gitTag
// luarocksApiKey

    
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
    def properties = readProperties file: 'source/jenkinsfile.properties'
    def packageName = properties['luarocksPackage']
    def rockFile = "${packageName}-${gitTag}.rockspec"
    def gitHibProjectSed = "${gitHubProject}".replace('/', '\\/')
    dir('source') {
      sh """
git checkout -B version-${gitTag}
git mv ${packageName}-VERSION.rockspec ${rockFile}
sed -i "s/VERSION/${gitTag}/g" ${rockFile}
sed -i "s/GITHUB_PROJECT/${gitHibProjectSed}/g" ${rockFile}
      """
    }
  }

  stage ('Tag') {
    def properties = readProperties file: 'source/jenkinsfile.properties'
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
