pipeline {
    environment {
        registry = "anderson02aa/mobead-anderson"
        registryCredential = 'dockerhub'
        dockerImage = ''
        sonarCredential = 'sonarqube'
    }
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                echo "🚀 Pipeline Anderson - Checkout do código"
                checkout scm
            }
        }
        
        stage('Lint Dockerfile') {
            steps {
                echo "🔍 Pipeline Anderson - Análise do Dockerfile"
                sh 'docker run --rm -i hadolint/hadolint < Dockerfile'
            }
        }
        
//#        stage('SonarQube Analysis') {
//#            steps {
//#                echo "📊 Pipeline Anderson - Análise de qualidade com SonarQube"
//#                script {
//#                    def scannerHome = tool 'SonarQube Scanner'
//#                    withSonarQubeEnv('SonarQube') {
//#                        sh "${scannerHome}/bin/sonar-scanner"
//#                    }
//#                }
//#            }
//#        }



stage('SonarQube Analysis') {
    steps {
        echo '📊 Pipeline Anderson - Análise de qualidade com SonarQube'
        script {
            withSonarQubeEnv('SonarQube') {
                withEnv(['NODE_OPTIONS=--max-old-space-size=8192']) {
                    echo "🔧 DEBUG: Executando SonarQube Scanner..."
                    sh "${tool 'SonarQube_Scanner'}/bin/sonar-scanner"
                    echo "✅ DEBUG: SonarQube Scanner executado!"
                }
            }
        }
    }
}


        
//        stage('Build Docker Image') {
//            steps {
//                echo "🏗️ Pipeline Anderson - Build da imagem Docker"
//                script {
//                    dockerImage = docker.build registry + ":$BUILD_NUMBER"
//                }
//            }
//        }


stage('Build Docker Image') {
    steps {
        echo '🏗️ Pipeline Anderson - Build da imagem Docker'
        sh "docker build -t mobeadead-app:${env.BUILD_NUMBER} ."
        echo "✅ Imagem mobeadead-app:${env.BUILD_NUMBER} construída com sucesso."
    }
}



       /* 
        stage('Push to Registry') {
            steps {
                echo "📤 Pipeline Anderson - Push para Docker Hub"
                script {
                    docker.withRegistry('https://registry-1.docker.io/v2/', registryCredential) {
                        dockerImage.push("$BUILD_NUMBER")
                        dockerImage.push("latest")
                    }
                }
            }
        }
*/
        
 //       stage('Deploy to Development') {
 //           steps {
 //               echo "🚀 Pipeline Anderson - Deploy em Desenvolvimento"
 //               script {
 //                   sh """
 //                       docker stop mobead-dev || true
 //                       docker rm mobead-dev || true
 //                       docker run -d --name mobead-dev -p 8081:80 ${registry}:${BUILD_NUMBER}
 //                   """
 //               }
 //           }
//        }

stage('Deploy to Development') {
    steps {
        echo '🚀 Pipeline Anderson - Deploy em Desenvolvimento'
        script {
            sh "docker stop mobead-dev || true"
            sh "docker rm mobead-dev || true"
            sh "docker run -d --name mobead-dev -p 8082:80 mobeadead-app:${env.BUILD_NUMBER}"
            echo "✅ Container mobead-dev iniciado com sucesso na porta 8082."
        }
    }
}



        
        stage('Approval for Production') {
            steps {
                echo "⏳ Pipeline Anderson - Aguardando aprovação para produção"
                input message: 'Deploy para produção?', ok: 'Deploy',
                      submitterParameter: 'APPROVER'
            }
        }
        
        stage('Deploy to Production') {
            steps {
                echo "🎯 Pipeline Anderson - Deploy em Produção"
                script {
                    sh """
                        docker stop mobead-prod || true
                        docker rm mobead-prod || true
                        docker run -d --name mobead-prod -p 8082:80 ${registry}:${BUILD_NUMBER}
                    """
                }
            }
        }
    }
    
    post {
        always {
            echo "🏁 Pipeline Anderson - Limpeza de recursos"
            sh 'docker system prune -f'
        }
        success {
            echo "✅ Pipeline Anderson - Sucesso!"
        }
        failure {
            echo "❌ Pipeline Anderson - Falha na execução"
  }

    }

}

