
pipeline {
// PipeLine Start
agent {
  kubernetes { yaml """
kind: Pod
spec:
  containers:
  - name: kaniko
    image: gcr.io/kaniko-project/executor:debug
    imagePullPolicy: Always
    env: 
      - name: AWS_ACCESS_KEY_ID
        valueFrom:
          secretKeyRef:
            name: aws-ecr-secret
            key: aws-access-key-id
      - name: AWS_SECRET_ACCESS_KEY
        valueFrom:
          secretKeyRef:
            name: aws-ecr-secret
            key: aws-secret-access-key
    command:
    - /busybox/cat
    tty: true
    volumeMounts:
      - name: docker-config
        mountPath: /kaniko/.docker
  volumes:
    - name: docker-config
"""
  }
}

environment {
    DOCKER_FILE = "dockerfile"
    BRANCH_NAME = "main"
    GIT_ADDRESS = "github.com/"
    GIT_REPO = "JamesSynnDev/kaniko_test"
    ACCOUNT_NUM = "public"
    IMAGE_ADDRESS = ".ecr.aws/s1j3b1x8/"
    IMAGE_REPO = "deployment-test"
    PROJECT_NAME = "kaniko-test"
    DEPLOYMENT_FILE = "kaniko-deployment.yaml"
}

stages {
// Stages Start
  stage('Preparation') {
// 1st stage
    steps {
      script { monthday = sh (
        returnStdout: true, script: 
        '''
        echo -n `date '+%m%d'` 
        '''
      )} 
      echo "Date : ${monthday}"
  }}
  stage('Build with Kaniko') {
// 2nd stage
    steps {
      echo "Tagging ${BUILD_NUMBER}"
      container(name: 'kaniko', shell: '/busybox/sh') {
        sh '''#!/busybox/sh

          /kaniko/executor \
          --dockerfile "${DOCKER_FILE}" \
          --context "git://${GIT_ADDRESS}${GIT_REPO}.git" \
          --destination ${ACCOUNT_NUM}${IMAGE_ADDRESS}${IMAGE_REPO}:james-${BUILD_NUMBER} \
          --destination ${ACCOUNT_NUM}${IMAGE_ADDRESS}${IMAGE_REPO}:latest
        '''
      }
  }}
// Stages End
}
// PipeLine End
}
