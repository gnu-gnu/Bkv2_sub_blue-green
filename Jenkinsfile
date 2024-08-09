pipeline {
  agent {
    kubernetes {
      yaml '''
      apiVersion: v1
      kind: Pod
      metadata:
        labels:
          app: blue-green-deploy
        name: blue-green-deploy
      spec:
        containers:
        - name: kustomize
          image: sysnet4admin/kustomize:5.4.1
          tty: true
          volumeMounts:
          - mountPath: /bin/kubectl
            name: kubectl
          command:
          - cat
        serviceAccount: jenkins
        volumes:
        - name: kubectl
          hostPath:
            path: /bin/kubectl
      '''
    }
  }
  stages {
    stage('git scm update'){
      steps {
        git url: 'https://github.com/k8s-edu/Bkv2_sub_blue-green-pipeline.git', branch: 'main'
      }
    }
    stage('define tag'){
      steps {
        script {
          if(env.BUILD_NUMBER.toInteger() % 2 == 1){
            env.tag = "blue"
          } else {
            env.tag = "green"
          }
        }
      }
    }
    stage('deploy deployment'){
      steps {
        container('kustomize'){
          dir('deployment'){
            sh '''
            env
            '''
          }
        }
      }    
    }
  }
}
