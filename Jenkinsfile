pipeline
{
    agent {
        label 'ProdServer'
    }
    
    triggers {
        pollSCM('*/60 * * * *')
    }

    parameters {
        choice(name: 'version', choices: 'v1\nlatest', description: 'Docker Version image ')
    }
    stages
    {
        
        stage('checkout')
        {
            steps {
                git 'https://github.com/kaza514/docker_spring_ecr.git'
            }
        }

        stage('Approval') {
            steps {
                input(id: "Deploy", message: "Deploy ${params.version}?", ok: 'Deploy')
            }
        }

        stage('Build') {
            script {
                  def mvnhome = tool name: 'M2_HOME', type: 'maven'
                  sh "${mvnhome}/bin/mvn --version"
                  sh "${mvnhome}/bin/mvn clean package"
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

    }

}
