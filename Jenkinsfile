pipeline {
    agent {
        label 'Lable1'
    }
    
    triggers {
        pollSCM('*/2 * * * *')
    }

    parameters {
        choice(name: 'version', choices: 'v1\nlatest', description: 'Docker Version image ')
    }
    stages {
        stage('checkout') {
            steps {
                git 'https://github.com/kaza514/docker_spring_ecr.git'
            }
        }

        stage('checkout') {
            steps {
                input(id: "Deploy", message: "Deploy ${params.version}?", ok: 'Deploy')
            }
        }

        stage('Update Docker UAT image') {
            when { branch "master" }
            steps {
                sh '''
                    docker build --no-cache -t test-repository .
                    docker tag test-repository:latest XXXXXXXXXX.dkr.ecr.ap-southeast-2.amazonaws.com/test-repository:latest
                    docker push XXXXXXXXXX.dkr.ecr.ap-southeast-2.amazonaws.com/test-repository:latest
                    docker rmi test-repository:latest
                '''
            }
        }
        
        stage('Update UAT container') {
            when { branch "master" }
            steps {
                sh '''
                    docker pull XXXXXXXXXX.dkr.ecr.ap-southeast-2.amazonaws.com/test-repository:latest
                    docker stop test-repository
                    docker rm test-repository
                    docker run -p 9090:9090 --name test-repository -t -d XXXXXXXXXX.dkr.ecr.ap-southeast-2.amazonaws.com/test-repository
                    docker rmi -f $(docker images -q --filter test-repository=true)
                '''
            }
        }

        stage('Release Docker image') {
          when {
            expression { params.version == 'v1' }
          }
            steps {
                sh '''
                    docker build --no-cache -t person .
                    docker tag test-repository:v1 XXXXXXXXXX.dkr.ecr.ap-southeast-2.amazonaws.com/test-repository:v1
                    docker push XXXXXXXXXX.dkr.ecr.ap-southeast-2.amazonaws.com/test-repository:v1
                    docker rmi $(docker images -f “test-repository=true” -q)
               '''
            }
        }

        stage('Release Docker image') {
          when {
            expression { params.version == 'latest' }
          }
            steps {
                sh '''
                    docker build --no-cache -t person .
                    docker tag test-repository:latest XXXXXXXXXX.dkr.ecr.ap-southeast-2.amazonaws.com/test-repository:latest
                    docker push XXXXXXXXXX.dkr.ecr.ap-southeast-2.amazonaws.com/test-repository:latest
                    docker rmi $(docker images -f “test-repository=true” -q)
               '''
            }
        }

    }        
}
