pipeline {
    agent {
        kubernetes {
            defaultContainer 'kaniko'
             yaml """
kind: Pod
metadata:
  name: kaniko
  namespace: ali
spec:
  containers:
  - name: kaniko
    image: gcr.io/kaniko-project/executor:debug
    imagePullPolicy: IfNotPresent
    env:
     - name: container
       value: "docker"
    command:
     - /busybox/cat
    tty: true
"""
        }
    }
    environment {
        IMAGE_PUSH_DESTINATION="samirathorizon/hellonode"
    }
    stages {
        stage('Build with Kaniko') {
            steps {
                checkout scm
                container(name: 'kaniko', shell: '/busybox/sh') {
                   withCredentials([file(credentialsId: 'docker-credentials', variable: 'DOCKER_CONFIG_JSON')]) {
                      withEnv(['PATH+EXTRA=/busybox',"IMAGE_PUSH_DESTINATION=${IMAGE_PUSH_DESTINATION}"]) {
                         sh '''#!/busybox/sh
                            cp $DOCKER_CONFIG_JSON /kaniko/.docker/config.json
                            /kaniko/executor --context `pwd` --destination $IMAGE_PUSH_DESTINATION
                          '''
                     }
                  }
              }
            }
        }
       stage('Deploying hellonode container to Kubernetes') {
           steps {
               script {
                  // kubernetesDeploy(configs: "deployment.yaml", "service.yaml")
                    sh '''#!/busybox/sh 
                    kubectl apply -n ali deployment.yaml
                     '''
               }
           }
       }
    }
}
