pipeline {
    agent {
        label "slave1"
    }
    //parameters {
      //  choice(name: 'ENV_ITI', choices: ['dev', 'test', 'prod', "release"])
    //}
    stages {
        stage('build') {
            steps {
                script {
                    echo 'build'
                    //if (params.ENV_ITI == "release") {
                    withCredentials([usernamePassword(credentialsId: '7c3d240b-00a6-4ec2-a392-7ee15ccd4ce3', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                            sh '''
                                docker login -u ${USERNAME} -p ${PASSWORD}
                                docker build -t abdulrahmanelfeki/bakehouseitisysadmin:v${BUILD_NUMBER} .
                                docker push abdulrahmanelfeki/bakehouseitisysadmin:v${BUILD_NUMBER}
                                echo ${BUILD_NUMBER} > ../build_num.txt
                                echo ${ENV_ITI}
                            '''
                     //   }
                    } //else {
                     //   echo "user chose ${params.ENV_ITI}"
                    //}
                }
            }
        }
        stage('deploy') {
            steps {
                echo 'deploy'
                script {
                    //if (params.ENV_ITI == "dev" || params.ENV_ITI == "test" || params.ENV_ITI == "prod") {
                        withCredentials([file(credentialsId: '3f6ac111-d29d-4121-8163-4d5c49a72be0', variable: 'KUBECONFIG')]) {
                            sh '''
                                export BUILD_NUMBER=$(cat ../build_num.txt)
                                mv Deployment/deploy.yaml Deployment/deploy.yaml.tmp
                                cat Deployment/deploy.yaml.tmp | envsubst > Deployment/deploy.yaml
                                rm -rf Deployment/deploy.yaml.tmp
                                kubectl apply -f Deployment --kubeconfig ${KUBECONFIG} -n ${ENV_ITI}
                            '''
                       // }
                    } //else {
                        //echo "user chose ${params.ENV_ITI}"
                    //}
                }
            }
        }
    }
}
