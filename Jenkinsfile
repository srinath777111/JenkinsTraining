// // pipeline {
// //     agent any

// //     stages {
// //         stage('Build') {
// //             agent {
// //                 docker {
// //                     image 'node:18-alpine'
// //                     reuseNode true
// //                 }
// //             }
// //             steps {
// //                 sh '''
// //                     ls -la
// //                     node --version
// //                     npm --version
// //                     npm ci
// //                     npm run build
// //                     ls -la
// //                 '''
// //             }
// //         } 

// //         stage('test') {
// //             agent {
// //                 docker {
// //                     image 'node:18-alpine'
// //                     reuseNode true
// //                 }
// //             }
// //             steps {
// //                 sh '''
// //                     echo 'testing'
// //                     test -f build/index.html
// //                     npm test
// //                 '''
// //             }
// //         }

// //         stage('E2E') {
// //             agent {
// //                 docker {
// //                     image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
// //                     reuseNode true
// //                 }
// //             }
// //             steps {
// //                 sh '''
// //                     npm install serve
// //                     node_modules/.bin/serve -s build &
// //                     sleep 10
// //                     npx playwright test
// //                 '''
// //             }
// //         }

// //         stage('Deploy') {
// //             agent {
// //                 docker {
// //                     image 'node:18-alpine'
// //                     reuseNode true
// //                 }
// //             }

// //             steps {
// //                 sh '''
// //                     npm install netlify -cli

// //                     node_modules/.bin/netlify -version
// //                 '''
// //             }
// //         }
// //     }

// //     post {
// //         always {
// //             junit 'jest-results/junit.xml'
// //         }
// //     }
// // }




// pipeline {
//     agent any

//     stages {

//         stage('Build') {
//             agent {
//                 docker {
//                     image 'node:18-alpine'
//                     reuseNode true
//                 }
//             }
//             steps {
//                 sh '''
//                     ls -la

//                     node --version
//                     npm --version

//                     npm ci

//                     npm run build

//                     ls -la
//                     ls build
//                 '''
//             }
//         }

//         stage('Tests') {
//             parallel {

//                 stage('Unit Tests') {
//                     agent {
//                         docker {
//                             image 'node:18-alpine'
//                             reuseNode true
//                         }
//                     }

//                     steps {
//                         sh '''
//                             npm test
//                         '''
//                     }

//                     post {
//                         always {
//                             junit 'jest-results/junit.xml'
//                         }
//                     }
//                 }

//                 stage('E2E Tests') {
//                     agent {
//                         docker {
//                             image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
//                             reuseNode true
//                             args '-p 3000:3000'
//                         }
//                     }

//                     steps {
//                         sh '''
//                             npm install

//                             npm install serve

//                             npx serve -s build -l 3000 &

//                             sleep 10

//                             curl http://localhost:3000

//                             npx playwright test --reporter=html
//                         '''
//                     }
//                 }
//             }
//         }
//     }

//     post {
//         success {
//             echo 'Pipeline completed successfully.'
//         }

//         failure {
//             echo 'Pipeline failed.'
//         }
//     }
// }


pipeline {
    agent any

    stages {
        stage('docker'){
            steps{
                sh ' docker build -t my-img-own .'
            }

        }

        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }

            steps {
                sh '''
                    ls -la
                    node --version
                    npm --version

                    npm ci
                    npm run build

                    ls -la
                '''
            }
        }

        stage('Tests') {

            parallel {

                stage('Unit Tests') {
                    agent {
                        docker {
                            image 'node:18-alpine'
                            reuseNode true
                        }
                    }

                    steps {
                        sh '''
                            npm test
                        '''
                    }

                    post {
                        always {
                            junit 'jest-results/junit.xml'
                        }
                    }
                }

                stage('E2E Tests') {
                    agent {
                        docker {
                            image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                            reuseNode true
                        }
                    }

                    steps {
                        sh '''
                            npm install
                            npm install serve

                            npx serve -s build &

                            sleep 10

                            npx playwright test --reporter=html
                        '''
                    }

                    post {
                        always {
                            publishHTML([
                                allowMissing: false,
                                alwaysLinkToLastBuild: false,
                                keepAll: false,
                                reportDir: 'playwright-report',
                                reportFiles: 'index.html',
                                reportName: 'Playwright Report',
                                reportTitles: '',
                                useWrapperFileDirectly: true
                            ])
                        }
                    }
                }
            }
        }

        stage('Approval') {
            steps {
                timeout(time: 15, unit: 'MINUTES') {
                    input message: 'Continue the pipeline?', ok: 'Continue'
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully.'
        }

        failure {
            echo 'Pipeline failed.'
        }
    }
}