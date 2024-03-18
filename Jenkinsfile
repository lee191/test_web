pipeline {
    agent any // 모든 agent에서 실행 가능하도록 설정

    stages {
        stage('build') {
            steps {
                echo 'Building the application...' // 빌드 단계에서의 로그 출력
            }
        }
        stage('test') {
            steps {
                echo 'Testing the application...' // 테스트 단계에서의 로그 출력
            }
        }
        stage('deploy') {
            steps {
                script {
                    // sshPublisher 플러그인을 사용하여 배포를 진행합니다.
                    sshPublisher(
                        publishers: [
                            sshPublisherDesc(
                                configName: "Web_test", // Jenkins에서 미리 설정된 SSH 서버의 이름
                                transfers: [
                                    sshTransfer(
                                        sourceFiles: "path/to/artifacts/**/*", // 배포할 파일 위치
                                        removePrefix: "path/to/artifacts", // 경로에서 제거할 접두사
                                        remoteDirectory: "/path/on/ec2/destination", // 원격 서버의 배포 디렉토리
                                        execCommand: '''
                                            cd /path/on/ec2/destination
                                            echo "디렉토리로 이동: $(pwd)"

                                            pm2 stop my-application || true
                                            echo "애플리케이션 종료."

                                            npm install --production
                                            echo "의존성 설치 완료."

                                            npm run build
                                            echo "애플리케이션 빌드 완료."

                                            pm2 startOrRestart ecosystem.config.js
                                            echo "애플리케이션 시작 또는 재시작 완료."
                                        ''' // execCommand 내에서 bash 명령을 통해 배포 과정을 수행
                                    )
                                ],
                                continueOnError: false, // 오류 발생 시 계속 진행하지 않음
                                failOnError: true // 오류 발생 시 실패로 표시
                            )
                        ]
                    )
                }
            }
        }
    }
}
