pipeline {
    agent any
    environment{
        DOCKER_TAG = getDockerTag()
        NEXUS_URL  = "172.31.32.1:8081"
        IMAGE_URL_WITH_TAG = "${NEXUS_URL}/node-app:${DOCKER_TAG}"
    }
    stages{
        stage('Build Docker Image'){
            steps{
                sh "docker build . -t ${IMAGE_URL_WITH_TAG}"
            }
        }
        stage('Nexus Push'){
            steps{
                withCredentials([string(credentialsId: 'nexus-pwd', variable: 'nexusPwd')]) {
                    sh "docker login -u admin -p ${nexusPwd} ${NEXUS_URL}"
                    sh "docker push ${IMAGE_URL_WITH_TAG}"
                }
            }
        }
        stage('Deploy to k8s'){
            steps{
                sh "chmod +x changeTag.sh"
                sh "./changeTag.sh ${DOCKER_TAG}"
            }
        }
    }
}

def getDockerTag(){
    def tag  = sh script: 'git rev-parse HEAD', returnStdout: true
    return tag
}
