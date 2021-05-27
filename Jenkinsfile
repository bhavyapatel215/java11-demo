pipeline {
    agent {label 'master'}
    environment {
        SONAR_TOKEN= '64329bab7f2264e78495529bfc2382f13ef589de'
        APP_HOME='/home/app'
        PRAGRA_BATCH='devs'
    }
    options { 
        quietPeriod(30) 
    }
    parameters { 
            choice(name: 'ENV_TO_DEPLOY', 
            choices: ['ST', 'UAT', 'STAGING'], description: 'Select a Env to deploy') 

             booleanParam(name: 'RUN', defaultValue: true, description: 'SELECT TO RUN')
    }
    triggers {
        pollSCM('* * * * *')
    }
    tools{
        maven  'm3'
        jdk 'jdk11'
    }

    stages {
        stage('Clean Ws') {
            steps{
                cleanWs()
            }
        }
        stage('Git CheckoutOut'){
            steps{
                checkout scm
            }
        }
        stage('Compile') {
            steps {
                sh 'mvn compile'
            }
        }
       stage('Static Code Analaysis') {
            steps {
                withSonarQubeEnv(credentialsId: 'SonarCloud', installationName: 'sonarcloud') {
                    sh 'mvn verify org.sonarsource.scanner.maven:sonar-maven-plugin:sonar'
                }
            }
        }
         stage('Unit Test') {
            steps {
                sh 'mvn test'
            }
        }

         stage('Package') {
            steps {
                sh 'mvn package'
            }
        }
        stage('Publish to Articatory'){
            steps {
                rtUpload (
                    serverId: 'Artifactory1',
                    spec: '''{
                        "files": [
                            {
                            "pattern": "target/*dummy*.jar",
                            "target": "libs-release-local/dummy"
                            },
                            {
                            "pattern": "target/pom.pom",
                            "target": "libs-release-local/dummy"
                            }
                        ]
                    }''',
                
                    // Optional - Associate the uploaded files with the following custom build name and build number,
                    // as build artifacts.
                    // If not set, the files will be associated with the default build name and build number (i.e the
                    // the Jenkins job name and number).
                    buildName: 'holyFrog',
                    buildNumber: '42',
                    // Optional - Only if this build is associated with a project in Artifactory, set the project key as follows.
                    project: 'my-project-key'
                )



            //     rtMavenResolver (
            //         id: 'resolver',
            //         serverId: 'Artifactory1',
            //         releaseRepo: 'libs-release-local',
            //         snapshotRepo: 'libs-snapshot-local'
            //     )  
            
            //   rtMavenDeployer (
            //         id: 'deployer',
            //         serverId: 'Artifactory1-1',
            //         releaseRepo: 'libs-release-local',
            //         snapshotRepo: 'libs-snapshot-local',
            //         // By default, 3 threads are used to upload the artifacts to Artifactory. You can override this default by setting:
            //         threads: 6,
            //         // Attach custom properties to the published artifacts:
            //         properties: ['Version=1.0.0', 'Publisher=Bhavya']
            //      )
                
            //     rtMavenRun (
            //         // Tool name from Jenkins configuration.
            //         tool: 'm3',
            //         pom: 'pom.xml',
            //         goals: 'clean install',
            //         // Maven options.
            //         opts: '-Xms1024m -Xmx4096m',
            //         resolverId: 'resolver',
            //         deployerId: 'deployer',
            //         // If the build name and build number are not set here, the current job name and number will be used:
            //         buildName: 'my-build-name',
            //         buildNumber: '17',
            //         // Optional - Only if this build is associated with a project in Artifactory, set the project key as follows.
            //         project: 'my-project-key'
            //     )
            }
        }
    }

    post {
        always {
            echo 'ALL GOOD '
        }
    }


}
