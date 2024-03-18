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
                                            echo "[Deploy] 디렉토리로 이동: $(pwd)"
                                            cd /path/on/ec2/destination

                                            pm2 stop my-application || true
                                            echo "[Deploy] 애플리케이션 종료."

                                            npm install --production
                                            echo "[Deploy] 의존성 설치 완료."

                                            npm run build
                                            echo "[Deploy] 애플리케이션 빌드 완료."

                                            pm2 startOrRestart ecosystem.config.js
                                            echo "[Deploy] 애플리케이션 시작 또는 재시작 완료."
                                        '''
                                    )
                                ]
                            )
                        ]
                    )
                }
            }
        }
    }
}
