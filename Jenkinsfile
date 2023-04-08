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
                    sh '/kaniko/executor  --context `pwd` --destination=hellonode --no-push'
                }
            }
        }
    }
}
podTemplate(yaml: '''
kind: Pod
metadata:
  name: ubuntu
spec:
  volumes:
  - name: kubectl
    emptyDir: {}
  initContainers:
  - name: install-kubectl
    image: allanlei/kubectl
    volumeMounts:
    - name: kubectl
      mountPath: /data
    command: ["cp", "/usr/local/bin/kubectl", "/data/kubectl"]
  containers:
  - name: ubuntu
    image: ubuntu
    command: ["/bin/bash cat"]
    volumeMounts:
    - name: kubectl
      subPath: kubectl
      mountPath: /usr/local/bin/kubectl
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
