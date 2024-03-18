pipeline {
    agent any // 모든 에이전트에서 실행 가능하도록 설정

    environment {
        CI = 'true' // 지속적 통합 환경 설정
    }

    stages {
        stage('Checkout') {
            steps {
                // GitHub에서 소스 코드를 가져오는 단계
                git 'https://github.com/lee191/test_web.git'
            }
        }

        stage('Install dependencies') {
            steps {
                // 프로젝트 의존성 설치
                sh 'npm install'
            }
        }

        stage('Test') {
            steps {
                // 테스트 실행
                sh 'npm test'
            }
        }

        stage('Build') {
            steps {
                // 프로덕션 빌드 생성
                sh 'npm run build'
            }
        }

        stage('Deploy to EC2') {
            steps {
                // SSH를 통해 EC2로 배포하는 단계
                sshPublisher(
                    publishers: [
                        sshPublisherDesc(
                            configName: 'Web_test', // Jenkins에 설정된 SSH 서버 구성의 이름
                            transfers: [
                                sshTransfer(
                                    sourceFiles: 'build/**/*', // EC2에 전송할 빌드 파일들
                                    removePrefix: 'build/', // 전송 파일 경로에서 제거할 접두사
                                    remoteDirectory: '/var/www/html', // 원격 서버의 웹 루트 디렉토리
                                    // 배포 후 실행할 명령어
                                    execCommand: '''
                                        sudo systemctl stop nginx
                                        sudo rm -rf /var/www/html/*
                                        sudo cp -R * /var/www/html/
                                        sudo systemctl start nginx
                                    '''
                                )
                            ],
                            usePty: false // 가상 터미널 사용 여부
                        )
                    ]
                )
            }
        }
    }

    post {
        // 빌드 후 조치
        always {
            // 항상 실행: 빌드 결과를 표시
            echo '빌드 과정이 끝났습니다.'
        }

        success {
            // 성공 시 실행: 성공 메시지를 표시
            echo '빌드가 성공적으로 완료되었습니다!'
        }

        failure {
            // 실패 시 실행: 실패 메시지를 표시
            echo '빌드에 실패하였습니다.'
        }
    }
}

