pipeline {
    agent any
    stages {
        stage('build') {
            steps {
                echo 'building the application...'
            }
        }
        stage('test') {
            steps {
                echo 'testing the application...'
            }
        }
       stage('deploy') {
            steps {
                script {
                    // 배포 명령어 실행
                    sshPublisher(
                        publishers: [
                            sshPublisherDesc(
                                configName: "Web_test", // Jenkins에 설정된 SSH 서버 이름
                                transfers: [
                                    sshTransfer(
                                        sourceFiles: "path/to/artifacts/**/*",
                                        removePrefix: "path/to/artifacts",
                                        remoteDirectory: "/path/on/ec2/destination"
                                    )
                                ],
                                continueOnError: false,
                                failOnError: true,
                                execCommand: '''
                                    # 배포 디렉토리로 이동
                                    cd /path/on/ec2/destination
                                    
                                    # Node.js 애플리케이션 종료 (pm2를 사용하는 경우)
                                    pm2 stop my-application || true
                                    
                                    # 의존성 설치 및 애플리케이션 빌드
                                    npm install --production
                                    npm run build
                                    
                                    # 애플리케이션 시작 또는 재시작
                                    pm2 startOrRestart ecosystem.config.js
                                '''
                            )
                        ]
                    )
                }
            }
        }
    }
}
