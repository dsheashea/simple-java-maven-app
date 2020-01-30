pipeline {
    agent {
        docker {
            image 'maven:3-alpine'
            args '-v /root/.m2:/root/.m2'
        }
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
                    url: "http://localhost:8086/artifactory",
    		        credentialsId: 'Artifactory'
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
                sh 'mvn -B -DskipTests clean package'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
        stage('Publishing to Artifactory') {
            rtPublishBuildInfo (
                serverId: "ARTIFACTORY_SERVER"
            )
        }
    }
}