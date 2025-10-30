pipeline {
    agent any


    stages {
        stage("Checkout") {
            steps {
                checkout scm
            }
        }

        stage("Setup Java") {
            steps {
                sh '''
                    # Java 설치 확인
                    if command -v java &> /dev/null; then
                        java -version
                        JAVA_BIN=$(readlink -f "$(which java)")
                        export JAVA_HOME="$(dirname "$(dirname "$JAVA_BIN")")"
                        echo "JAVA_HOME=$JAVA_HOME"
                    else
                        echo "Java가 설치되어 있지 않습니다. Gradle Toolchain이 자동으로 다운로드합니다."
                    fi
                '''
            }
        }

        stage("Build") {
            steps {
                sh '''
                    chmod +x ./gradlew
                    if command -v java &> /dev/null; then
                        JAVA_BIN=$(readlink -f "$(which java)")
                        export JAVA_HOME="$(dirname "$(dirname "$JAVA_BIN")")"
                        echo "JAVA_HOME=$JAVA_HOME"
                    fi
                    ./gradlew clean build -x test --no-daemon
                '''
            }
        }

        stage("Unit Test") {
            steps {
                sh '''
                    if command -v java &> /dev/null; then
                        JAVA_BIN=$(readlink -f "$(which java)")
                        export JAVA_HOME="$(dirname "$(dirname "$JAVA_BIN")")"
                        echo "JAVA_HOME=$JAVA_HOME"
                    fi
                    ./gradlew test --no-daemon
                '''
            }
            post {
                always {
                    junit 'build/test-results/test/**/*.xml'
                }
            }
        }

        stage("Print Name") {
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

