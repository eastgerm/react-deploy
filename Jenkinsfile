/* pipeline 변수 설정 */
def DATE = new Date();
def DOCKER_IMAGE_NAME = "dowk0331/react-deploy"           // 생성하는 Docker image 이름
def DOCKER_IMAGE_TAGS = "jenkins-test";  // 생성하는 Docker image 태그
def EMAIL = "gyun.kimdong@navercorp.com"
def NAMESPACE = "ns-project"

podTemplate(label: 'builder',
    containers: [
        containerTemplate(name: 'node', image: 'node:11-alpine', command: 'cat', ttyEnabled: true),
        containerTemplate(name: 'docker', image: 'docker', command: 'cat', ttyEnabled: true),
    ],
    volumes: [
        hostPathVolume(mountPath: '/var/run/docker.sock', hostPath: '/var/run/docker.sock')
    ]
)

{
    node('builder') {
        stage('Checkout') {
             checkout scm
        }

        stage('Build') {
            container('node') {
                sh "npm install"
                sh "npm run build"
            }
        }

        stage('Docker build') {
            container('docker') {
                sh "ls /var/run"
                withCredentials([usernamePassword(
                    credentialsId: 'docker_hub_auth',
                    usernameVariable: "USERNAME",
                    passwordVariable: "PASSWORD")]) {
                        sh "docker build -t ${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAGS} ."
                        sh "docker login -u ${USERNAME} -p ${PASSWORD}"
                        sh "docker push ${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAGS}"
                }
            }
        }
    }
}