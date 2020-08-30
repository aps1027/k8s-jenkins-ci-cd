pipeline {
    agent any
    stages {
        stage('BUILD') {
            steps {
                sh 'docker build -f laravel-kubernetes-demo/Dockerfile -t <docker_repo_name>/laravel-kubernetes-demo:latest .'
            }
        }
        stage('Test') {
            steps {
                sh 'docker run --rm -i -p 8081:80 \
                    -e APP_KEY=base64:cUPmwHx4LXa4Z25HhzFiWCf7TlQmSqnt98pnuiHmzgY= \
                    <docker_repo_name>/laravel-kubernetes-demo vendor/bin/phpunit'
            }
        }
        stage('DEPLOY') {
            when {
                branch 'master'  //only run these steps on the master branch
            }
            steps {
                withDockerRegistry([ credentialsId: 'docker_hub_login', url: '']) {
                    sh 'docker push <docker_repo_name>/laravel-kubernetes-demo:latest'
                }
                withKubeConfig([credentialsId: 'my_k8s', serverUrl: 'http://127.0.0.1:44095/']) {
                    sh 'kubectl apply -f laravel-kubernetes-demo/kube/deployment.yaml'
                    sh 'kubectl apply -f laravel-kubernetes-demo/kube/service.yaml'
                }
            }
        }
        stage('CLEAN-UP') {
            steps {
                sh 'docker system prune -f'
                deleteDir()
            }
        }
    }
}
