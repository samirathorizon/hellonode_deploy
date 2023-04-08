    
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
            git 'https://github.com/samirathorizon/hellonode.git'
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
  name: kaniko
  namespace: ali
spec:
  containers:
  - name: kubectl
    image: bitnami/kubectl
    imagePullPolicy: IfNotPresent
''') {
    node (POD_LABEL) {
        stage('Apply Kubernetes files') {
            git 'https://github.com/samirathorizon/hellonode.git'
            withKubeConfig([namespace: "ali"]) {
                sh 'kubectl apply -f deployment.yaml -n ali'
            }
        }
    }
}
