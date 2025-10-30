pipeline {
    agent any
    options { timestamps() }

    environment {
        // 컨테이너에 설치된 JDK 17 고정 경로
        JAVA17_HOME = '/usr/lib/jvm/temurin-17-jdk-arm64'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Verify JDK 17') {
            steps {
                sh '''
          set -e
          if [ ! -d "$JAVA17_HOME" ]; then
            # 경로가 바뀐 경우를 대비해 자동 탐색
            JAVA17_BIN=$(readlink -f /usr/lib/jvm/*17*/bin/java)
            export JAVA17_HOME="$(dirname "$(dirname "$JAVA17_BIN")")"
          fi
          echo "Using JAVA17_HOME=$JAVA17_HOME"
          "$JAVA17_HOME/bin/java" -version
        '''
            }
        }

        stage('Build') {
            steps {
                sh '''
          set -e
          chmod +x ./gradlew
          # Gradle에 명시적으로 JDK17 사용 지시
          ./gradlew clean build -x test --no-daemon -Dorg.gradle.java.home="$JAVA17_HOME"
        '''
            }
        }

        stage('Unit Test') {
            steps {
                sh '''
          set -e
          ./gradlew test --no-daemon -Dorg.gradle.java.home="$JAVA17_HOME"
        '''
            }
            post {
                always {
                    // JUnit 리포트 수집
                    junit 'build/test-results/test/**/*.xml'
                }
            }
        }

        stage('Print Name') {
            steps {
                echo '작성자: 202332045 김다영'
            }
        }
    }

    post {
        always {
            cleanWs()
        }
        success {
            echo '빌드와 테스트가 성공적으로 완료되었습니다!'
        }
        failure {
            echo '빌드 또는 테스트가 실패했습니다.'
        }
    }
}
