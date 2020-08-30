# Kubernetes CI/CD with Jenkins
K8s-jenkins-ci-cd project is just for learning CI/CD flow in local machine (ubuntu 18.04).

## Prerequisites
- Docker hub account
- GitHub account
- Install docker    
    https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-18-04
- Install kubectl   
    https://kubernetes.io/docs/tasks/tools/install-kubectl/
- Install minikube  
    https://github.com/kubernetes/minikube/releases
- Install Jenkins   
    https://www.digitalocean.com/community/tutorials/how-to-install-jenkins-on-ubuntu-18-04

## Procedures
### Step 1
Minikube Setup
1. Open minikube dashboard
    ```
    $ minikube start
    $ minikube dashboard
    ```
    After above, will show like following:
    ![alt text](https://github.com/aps1027/k8s-jenkins-ci-cd/blob/master/doc/images/minikube_dashboard.png?raw=true)

### Step 2
Open jenkins and Setup Plugins
1. Check Jenkins status
    ```
    $ sudo systemctl status jenkins
    ```
1. Access jenkins   
    http://localhost:8080/ (default jenkins url)    

    After above, will show like following:
    ![alt text](https://github.com/aps1027/k8s-jenkins-ci-cd/blob/master/doc/images/jenkins_dashboard.png?raw=true)

1. Install following plugins
    - (Kubernetes) https://plugins.jenkins.io/kubernetes/ 
    - (Blue Ocean) https://plugins.jenkins.io/blueocean/ 
1. Add k8s credentials
    - ```Jenkins``` --> ```Manage Jenkins``` --> ```Manage Credentials``` --> ```Jenkins``` --> ```	Global credentials (unrestricted)``` --> ```Add Credentials```
    - Fill ```Secret text``` in ```Kind```
    - Fill token in ```Secret```
        ```
        ### will get secret name
        $ kubectl get secrets

        ### use above secret name (e.g. default-token-9f5cx) that will generate after above cmd
        $ kubectl describe secrets/<name of secrets>

        ### copy and paste token in Secret
        ```
    - Fill ```my_k8s``` in ```ID```
    - Click ```OK``` button

    ![alt text](https://github.com/aps1027/k8s-jenkins-ci-cd/blob/master/doc/images/k8s_credentials.png?raw=true)

1. Add k8s cloud
    - ```Jenkins``` --> ```Manage Jenkins``` --> ```Configure system```   
    - Scroll to bottom and in Add a new cloud, select ```Kubernetes```
    - Fill ```Kubenetes URL``` with your minikube dashboard browser link (e.g. http://127.0.0.1:39659/)
    - Add credentials created in previous step in ```Credentials```
    - Click ```Test Connection``` to check connect with local k8s
    - Click ```Apply``` and ```Save``` button

    ![alt text](https://github.com/aps1027/k8s-jenkins-ci-cd/blob/master/doc/images/kubernetes_cloud.png?raw=true)

1. Add docker hub credentials
    - ```Jenkins``` --> ```Manage Jenkins``` --> ```Manage Credentials``` --> ```Jenkins``` --> ```	Global credentials (unrestricted)``` --> ```Add Credentials```
    - Fill ```Username with password``` in ```Kind```
    - Fill ```<your_docker_hub_username>``` in ```Username```
    - Fill ```<your_docker_hub_password>``` in ```Password```
    - Fill ```docker_hub_login``` in ```ID```
    - Click ```OK``` button

### Step 3
Prepare GitHub Repository for sample kube Project
1. Fork `k8s-jenkins-ci-cd` Git repo.   
    https://github.com/aps1027/k8s-jenkins-ci-cd

1. Change Docker hub repo name in code
    - In `JenkinsFile` and `deployment.yaml`,  
        replace `<docker_repo_name>` with your docker repo name.
    - Push Your Change to GitHub.

1. Change kubernetes server url in code
    - In `JenkinsFile`, 
        replace `<k8s_server_url>` with your kubernetes server url (e.g. http://127.0.0.1:39659/)

### Step 4
Create New Jenkins Pipeline with Blue Ocean
1. Click ```Open Blue Ocean``` in Jenkins Dashboard
    ![alt text](https://github.com/aps1027/k8s-jenkins-ci-cd/blob/master/doc/images/jenkins_dashboard.png?raw=true)
1. Click ```Create a new Pipeline``` in Blue Ocean
    ![alt text](https://github.com/aps1027/k8s-jenkins-ci-cd/blob/master/doc/images/jenkins_blue_ocean_1.png?raw=true)
1. Click ```GitHub``` in ```Where do you store your codes?```
    ![alt text](https://github.com/aps1027/k8s-jenkins-ci-cd/blob/master/doc/images/jenkins_blue_ocean_2.png?raw=true)
1. Click ```<your_github_account_name>``` and search ```k8s-jenkins-ci-cd``` in ```Choose a repository```
    ![alt text](https://github.com/aps1027/k8s-jenkins-ci-cd/blob/master/doc/images/jenkins_blue_ocean_3.png?raw=true)
1. After All, You will see like the following:     
    ![alt text](https://github.com/aps1027/k8s-jenkins-ci-cd/blob/master/doc/images/jenkins_blue_ocean_4.png?raw=true)

### Step 5
Start Deployed App with minikube
1. Wait Until like the following in your kubernetes dashboard   
    ![alt text](https://github.com/aps1027/k8s-jenkins-ci-cd/blob/master/doc/images/minikube_dashboard_2.png?raw=true)

1. Start Kubernetes Service ```laravel-kubernetes-demo```
    ```
     $ minikube service laravel-kubernetes-demo
    ```

1. After All, your deployed App will appear in your browser like this:
    ![alt text](https://github.com/aps1027/k8s-jenkins-ci-cd/blob/master/doc/images/laravel_app.png?raw=true)

### References:
https://learnk8s.io/blog/kubernetes-deploy-laravel-the-easy-way

https://www.linode.com/docs/development/ci/automate-builds-with-jenkins-on-ubuntu/

https://github.com/kenzanlabs/kubernetes-ci-cd

https://geekdudes.wordpress.com/2020/01/03/minikube-configure-jenkins-kubernetes-plugin/
        