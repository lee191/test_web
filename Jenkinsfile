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
                    // 로그를 출력하는 함수를 정의합니다.
                    def log = { msg ->
                        println "[Deploy] ${msg}"
                    }

                    // 배포 명령어 실행
                    sshPublisher(
                        publishers: [
                            sshPublisherDesc(
                                configName: "Web_test", // Jenkins에 설정된 SSH 서버 이름
                                transfers: [
                                    sshTransfer(
                                        sourceFiles: "path/to/artifacts/**/*",
                                        removePrefix: "path/to/artifacts",
                                        remoteDirectory: "/path/on/ec2/destination",
                                        execCommand: '''
                                            # 배포 디렉토리로 이동
                                            cd /path/on/ec2/destination
                                            log("디렉토리로 이동: $(pwd)")

                                            # Node.js 애플리케이션 종료
                                            pm2 stop my-application || true
                                            log("애플리케이션 종료.")

                                            # 의존성 설치 및 애플리케이션 빌드
                                            npm install --production
                                            log("의존성 설치 완료.")

                                            npm run build
                                            log("애플리케이션 빌드 완료.")

                                            # 애플리케이션 시작 또는 재시작
                                            pm2 startOrRestart ecosystem.config.js
                                            log("애플리케이션 시작 또는 재시작 완료.")
                                        '''
                                    )
                                ],
                                continueOnError: false,
                                failOnError: true
                            )
                        ]
                    )
                }
            }
        }
    }
}
