
pipeline {
    agent any

    options {
        disableConcurrentBuilds()

        buildDiscarder(
            logRotator(
                numToKeepStr: '20',
                artifactNumToKeepStr: '10'
            )
        )
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Check Environment') {
            steps {
                sh '''
                    echo "======================================"
                    echo " Environment Check"
                    echo "======================================"

                    echo "Node version:"
                    node --version

                    echo "NPM version:"
                    npm --version

                    echo "Bruno version:"
                    bru --version

                    echo "======================================"
                '''
            }
        }

        stage('Prepare Reports') {
            steps {
                sh '''
                    rm -rf reports
                    mkdir -p reports
                '''
            }
        }

        stage('Run Login') {
            steps {
                script {
                    catchError(
                        buildResult: 'FAILURE',
                        stageResult: 'FAILURE'
                    ) {
                        sh '''
                            echo "======================================"
                            echo " Running: 02- Login"
                            echo "======================================"

                            bru run "02- Login" \
                                --env Stage \
                                --reporter-junit reports/02-login-junit.xml \
                                --reporter-html reports/02-login-report.html

                            echo "======================================"
                            echo " Completed: 02- Login"
                            echo "======================================"
                        '''
                    }
                }
            }
        }

        stage('Run Home') {
            steps {
                script {
                    catchError(
                        buildResult: 'FAILURE',
                        stageResult: 'FAILURE'
                    ) {
                        sh '''
                            echo "======================================"
                            echo " Running: 03- Home"
                            echo "======================================"

                            bru run "03- Home" \
                                --env Stage \
                                --reporter-junit reports/03-home-junit.xml \
                                --reporter-html reports/03-home-report.html

                            echo "======================================"
                            echo " Completed: 03- Home"
                            echo "======================================"
                        '''
                    }
                }
            }
        }

        stage('Run Services') {
            steps {
                script {
                    catchError(
                        buildResult: 'FAILURE',
                        stageResult: 'FAILURE'
                    ) {
                        sh '''
                            echo "======================================"
                            echo " Running: 04- Services"
                            echo "======================================"

                            bru run "04- Services" \
                                --env Stage \
                                --reporter-junit reports/04-services-junit.xml \
                                --reporter-html reports/04-services-report.html

                            echo "======================================"
                            echo " Completed: 04- Services"
                            echo "======================================"
                        '''
                    }
                }
            }
        }
    }

    post {

        always {

            echo "======================================"
            echo " Publishing Test Results"
            echo "======================================"

            junit(
                allowEmptyResults: true,
                testResults: 'reports/*-junit.xml'
            )

            archiveArtifacts(
                artifacts: 'reports/*.html',
                allowEmptyArchive: true
            )

            echo "======================================"
            echo " All Reports Published"
            echo "======================================"
        }

        success {

            echo "======================================"
            echo " ALL TESTS PASSED"
            echo "======================================"
        }

        failure {

            echo "======================================"
            echo " SOME TESTS FAILED"
            echo "======================================"
        }
    }
}

