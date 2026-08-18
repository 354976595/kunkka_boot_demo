// ========================================
// 精简版 Jenkinsfile - 快速上手版
// 只做：拉代码 → 编译 → 测试 → 打包
// 跑通后再升级到完整版（加 Docker + 部署）
// ========================================
pipeline {
    agent any

    tools {
        jdk    'JDK21'
        maven  'maven3.9'
    }

    options {
        timeout(time: 20, unit: 'MINUTES')
        disableConcurrentBuilds()
        buildDiscarder(logRotator(numToKeepStr: '10'))
        timestamps()
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
                echo "分支: ${env.GIT_BRANCH}"
                sh 'git log --oneline -3'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean compile -B'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test -B'
            }
            post {
                always {
                    junit testResults: 'target/surefire-reports/*.xml', allowEmptyResults: true
                }
            }
        }

        stage('Package') {
            steps {
                sh 'mvn package -DskipTests -B'
                sh 'ls -lh target/*.jar'
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }
    }

    post {
        success {
            echo "构建成功 #${env.BUILD_NUMBER}"
        }
        failure {
            echo "构建失败 #${env.BUILD_NUMBER}，请检查 Console Output"
        }
    }
}
