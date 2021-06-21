pipeline {
    // Uncomment below, if you want job to run on agent.
    
    /*agent {
        label 'Linux'
        label 'Windows'
    }*/
    
    agent any
    
    triggers {
        pollSCM("*/2 * * * *")
    }
    
    tools {
        // Install the Maven version configured as "M3" and add it to the path.
        maven "MVN3"
        jdk "jdk8"
    }
    stages {
        stage('enable webhook') {
            steps {
                script {
                    properties([pipelineTriggers([githubPush()])])
                }
            }
        }
        stage('pullscm') {
            steps {
                git credentialsId: 'github', url: 'git@github.com:santmandar/jenkins_test.git'
            }
        }
        stage('Build') {
            steps {
                // Run Maven on a Unix agent.
                sh "mvn -Dmaven.test.failure.ignore=true -f api-gateway clean package"
                // To run Maven on a Windows agent, use
                //bat "mvn -Dmaven.test.failure.ignore=true -f api-gateway clean package"
            }
            post {
                // If Maven was able to run the tests, even if some of the test
                // failed, record the test results and archive the jar file.
                success {
                    junit 'api-gateway/target/surefire-reports/*.xml'
                    archiveArtifacts 'api-gateway/target/*.jar'
                    emailext body: "Please check console output at $BUILD_URL for more information\n", to: "sant.mandar@gmail.com", subject: 'JenkinsTraining - $PROJECT_NAME is completed - Build number is $BUILD_NUMBER - Build status is $BUILD_STATUS'

                }
            }
        }
        
        stage("Email") {
            steps{
                script {
                    cest = TimeZone.getTimeZone("CEST")
                    def cest = new Date()
                    println(cest)
                    def mailRecipients = 'sathishbob@gmail.com'
                    def jobName = currentBuild.fullDisplayName
                    env.Name = Name
                    env.cest = cest
                    emailext body: '''${SCRIPT, template="email-html.template"}''', mimeType: 'text/html', subject: "[Jenkins] ${jobName}", to: "${mailRecipients}", replyTo: "${mailRecipients}"
                }
            }
        }
        
    }
}
