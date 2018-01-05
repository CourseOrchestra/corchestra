node {
    def server = Artifactory.server 'ART'
    def rtMaven = Artifactory.newMavenBuild()
    def buildInfo

    stage ('Clone') {
        checkout scm
    }

    stage ('Artifactory configuration') {
        rtMaven.tool = 'M3' 
        rtMaven.deployer releaseRepo: 'libs-release-local', snapshotRepo: 'libs-snapshot-local', server: server
        rtMaven.resolver releaseRepo: 'libs-release', snapshotRepo: 'libs-snapshot', server: server
    }

    stage ('Exec Maven') {
        rtMaven.run pom: 'pom.xml', goals: '-U clean package', buildInfo: buildInfo
    }

    stage ('Publish build info') {
        def uploadSpec = '''
        {
         "files": [
            {
              "pattern": "target/files/*",
              "target": "corchestra-dev/$BUILD_NUMBER"
            }
            ]
        }'''
        buildInfo = server.upload spec: uploadSpec
        buildInfo.env.capture = true

        server.publishBuildInfo buildInfo
    }
}