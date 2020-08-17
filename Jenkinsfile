def app

pipeline {
    agent any
    environment {
        KUBECONFIG = credentials('jslay-k8s-kubeconfig')
        HOSTNAME = 'aseward.games'
    }
    stages {
        stage('Build') {
            steps {
                script {
                    sh 'ls -la'
                    app = docker.build("docker.home.jslay.net:5000/aseward/aseward_games:${env.BUILD_TAG}", "./aseward_games")
                }
            }
        }
        /*
        stage('Test') {
            steps {
                script {
                    app.inside {
                        sh 'pip install pytest'
                        sh 'pytest'
                    }
                }
            }
        }
        */
        stage('Push Image') {
            steps{
                script {
                    app.push()
                }
            }
        }
        /*
        stage('Deploy') {
            when {
                anyOf {
                    branch 'master'
                    branch 'dev'
                }
            }
            stages {
                stage('k8s Deployment') {
                    steps {
                        script {
                            if (env.BRANCH_NAME != "master") {
                                echo "Setting ${env.BRANCH_NAME} hostname..."
                                HOSTNAME = "${env.BRANCH_NAME}.${env.HOSTNAME}"
                            }
                            echo "Deploying ${env.BRANCH_NAME} to ${HOSTNAME}..."
                            withCredentials([
                              string(
                                credentialsId: '',
                                variable: 'SOME_SECRET'
                              )
                            ]) {
                                docker.image('alpine/helm:3.2.1').inside("-v $KUBECONFIG:/tmp/kubeconfig -e KUBECONFIG=/tmp/kubeconfig --entrypoint=''") {
                                    sh """
                                    helm upgrade --install asewardgames \
                                    --create-namespace --namespace aseward-games-${env.BRANCH_NAME} \
                                    -f helm/overrides.yaml \
                                    --wait helm/asewardgames \
                                    --set image.tag=${env.BUILD_TAG} \
                                    --set ingress.enabled=true \
                                    --set ingress.hostName="${HOSTNAME}" \
                                    --set ingress.hosts[0].host="${HOSTNAME}" \
                                    --set ingress.hosts[0].paths[0]="/" \
                                    --set ingress.tls[0].hosts[0]="${HOSTNAME}" \
                                    --set ingress.tls[0].secretName=tls \
                                    --set some.secret=${SOME_SECRET}
                                    """
                                }
                            }
                        }
                    }
                }
                stage('Test k8s Deployment') {
                    steps {
                        script {
                            docker.image('alpine/helm:3.2.1').inside("-v $KUBECONFIG:/tmp/kubeconfig -e KUBECONFIG=/tmp/kubeconfig --entrypoint=''") {
                                sh "helm test asewardgames --namespace aseward-games-${env.BRANCH_NAME}"
                            }
                        }
                    }
                }
            }
        }*/
    }
}