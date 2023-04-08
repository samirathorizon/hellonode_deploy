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
                stage('Build a  project') {
                    sh '/kaniko/executor  --context `pwd` --destination=hellonode'
                }
            }
        }
    }
}
podTemplate(yaml: '''
kind: Pod
metadata:
  name: kubectl
  namespace: ali
spec:
  containers:
  - name: kubectl
    image: portainer/kubectl-shell
    imagePullPolicy: IfNotPresent
    command: ["bach", "tail -f /dev/null"]
    tty: true
''') {
    node (POD_LABEL) {
        stage('Apply Kubernetes files') {
            checkout scm
            container('ubuntu') {
                withKubeConfig([namespace: "ali"]) {
                    sh 'kubectl apply -f deployment.yaml -n ali'
                }
            }
        }
    }
}
