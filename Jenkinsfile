pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps { checkout scm }
        }
        stage('Build') {
            agent { docker { image 'eclipse-temurin:17-jdk' } }
            steps {
                sh '''
          java -version
          chmod +x ./gradlew
          ./gradlew clean build -x test --no-daemon
        '''
            }
        }
        stage('Unit Test') {
            agent { docker { image 'eclipse-temurin:17-jdk' } }
            steps {
                sh './gradlew test --no-daemon'
            }
            post { always { junit 'build/test-results/test/**/*.xml' } }
        }
        stage('Print Name') {
            steps { echo '작성자: 202332045 김다영' }
        }
    }
    post {
        always { cleanWs() }
        success { echo '빌드와 테스트가 성공적으로 완료되었습니다!' }
        failure { echo '빌드 또는 테스트가 실패했습니다.' }
    }
}
