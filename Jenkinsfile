pipeline {
    agent {
        docker {
            image 'maven:3-alpine'
            args '-v /root/.m2:/root/.m2'
        }
    }
    environment {
        SERVER_URL              = credentials('SERVER_URL')
        ARTIFACTORY_CREDENTIALS = credentials('artifactory-jenkins-user')
    }
    options {
        skipStagesAfterUnstable()
    }
    stages {
        stage ('Artifactory configuration') {
            steps {
               // specify Artifactory server
                rtServer (
                    id: "ARTIFACTORY_SERVER",
                    url: SERVER_URL,
    		        credentialsId: ARTIFACTORY_CREDENTIALS
                )
    		    // specify the repositories to be used for deploying the artifacts in the Artifactory
                rtMavenDeployer (
                    id: "MAVEN_DEPLOYER",
                    serverId: "ARTIFACTORY_SERVER",
                    releaseRepo: "libs-release-local",
                    snapshotRepo: "libs-snapshot-local"
                )
    		    // defines the dependencies resolution details
                rtMavenResolver (
                    id: "MAVEN_RESOLVER",
                    serverId: "ARTIFACTORY_SERVER",
                    releaseRepo: "libs-release",
                    snapshotRepo: "libs-snapshot"
                )
            }
        }
        stage('Build') {
           steps {
               rtMavenRun (
                   tool: "maven-3", // Tool name from Jenkins configuration
                   pom: 'maven-example/pom.xml',
                   goals: 'clean install',
                   deployerId: "MAVEN_DEPLOYER",
                   resolverId: "MAVEN_RESOLVER"
               )
           }
        }
        stage('Publishing to Artifactory') {
            steps {
               rtPublishBuildInfo (
                    serverId: "ARTIFACTORY_SERVER"
               )
            }
        }
    }
}