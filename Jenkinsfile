pipeline {
    agent{
		label 'jenkins-worker'
	}
    parameters {
        string(name: 'IMAGEVERSION', defaultValue: '0.0.0', description: 'Image Version')
        gitParameter branchFilter: 'origin/(.*)', defaultValue: 'origin/master', name: 'BRANCH_ON_GIT', type: 'PT_BRANCH', description: 'Branch', quickFilterEnabled: true
        booleanParam(name: 'CLEAN_UP', defaultValue: true, description: 'Execute CLEAN_UP')     
    }
    environment {
        NEXUS_HOSTNAME = 'dev.ds.local:5000'
        NEXUS_URL = 'https://dev.ds.local:5000/'
        IMAGE_NAME = "alma8ksh:$IMAGEVERSION"
        registryCredential = 'nexus-jenkins'
    }
    stages {
         stage ('Lock Resource') {
            options{
                lock('jenkins-worker')
            }
            stages {
                stage('Build & Push'){
                    steps {
                        dir("dockerfiles/alma8ksh") {
                            sh 'echo "PATH --------> " && pwd'
                            script {
                                withDockerRegistry(credentialsId: 'nexus-jenkins', toolName: 'DOCKER', url: "${NEXUS_URL}") {
                                    sh "docker build -t ${NEXUS_HOSTNAME}/${IMAGE_NAME} -f Dockerfile.alma8ksh ."
                                }
                            }

                            script {
                                withDockerRegistry(credentialsId: 'nexus-jenkins', toolName: 'DOCKER', url: "${NEXUS_URL}") {
                                    sh "docker push ${NEXUS_HOSTNAME}/${IMAGE_NAME}"
                                }
                            }
                        }
                    }
                }

            }
        }
    }
    post {
        always {
            script {
                if (params.CLEAN_UP) {
                    echo 'CLEAN_UP ...'
                    cleanWs();
                    sh "docker system prune -a -f"
                }
            }
        }
        failure {
            emailext body: "Build Fail, please help me ${env.BUILD_URL}", subject: "${env.JOB_NAME} - Build # ${env.BUILD_NUMBER} - ${env.BUILD_URL} - Fail", mimeType: 'text/html', to: "scm@example.com"
        }
    }
}