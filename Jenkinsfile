podTemplate(yaml: '''
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
'''){
  node(POD_LABEL) {
    def IMAGE_PUSH_DESTINATION="samirathorizon/hellonode"
    stage('Build with Kaniko') {
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
  }   
