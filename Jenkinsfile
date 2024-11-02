podTemplate(yaml: '''
kind: Pod
metadata:
  name: kubectl
  namespace: samirtata
spec:
  containers:
  - name: kubectl
    image: portainer/kubectl-shell
    imagePullPolicy: IfNotPresent
    command: ["tail", "-f", "/dev/null"]
    tty: true
''') {
    node (POD_LABEL) {
        stage('Apply Kubernetes files') {
            checkout scm
            container('kubectl') {
                withKubeConfig([namespace: "samirtata"]) {
                    sh 'kubectl apply -f deployment.yaml -n samirtata'
                }
            }
        }
    }
}
