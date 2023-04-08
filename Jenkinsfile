    
podTemplate(yaml: '''
kind: Pod
metadata:
  name: kaniko
  namespace: ali
spec:
  containers:
  - name: shell
    image: gcr.io/kaniko-project/executor:debug
    imagePullPolicy: IfNotPresent
    env:
     - name: container
       value: "docker"
    command:
     - /busybox/cat
    tty: true
'''){
    node(POD_LABEL) {
        stage('Build') {
            checkout scm
            container('shell') {
                stage('Build a Maven project') {
                    sh '/kaniko/executor  --context `pwd` --destination=hellonode --no-push'
                }
            }
        }
    }
}
podTemplate(yaml: '''
kind: Pod
metadata:
  name: internal-kubectl
  namespace: ali
spec:
   serviceAccountName: jenkins
  containers:
  - name: kubectl
    image: trstringer/internal-kubectl:latest
''') {
    node (POD_LABEL) {
        stage('Apply Kubernetes files') {
            checkout scm
            container('internal-kubectl') {
                withKubeConfig([namespace: "ali"]) {
                //    sh 'kubectl apply -f deployment.yaml -n ali'
                     sh 'ls -lrt'
                }
            }
        }
    }
}
