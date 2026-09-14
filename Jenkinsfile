pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Check Environment') {
            steps {
                sh '''
                    node --version
                    npm --version
                    bru --version
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

        stage('Run Bruno') {
            steps {
                script {
                    def testFailed = false

                    sh '''
                        echo "======================================"
                        echo "Running End To End"
                        echo "Environment: SuperApp-dev"
                        echo "======================================"
                    '''

                    try {
                        sh '''
                            bru run "01- End To End" \
                                --env SuperApp-dev \
                                --reporter-junit reports/e2e-junit.xml \
                                --reporter-html reports/e2e-report.html
                        '''
                    } catch (Exception e) {
                        echo "End To End tests failed."
                        testFailed = true
                    }

                    sh '''
                        echo "======================================"
                        echo "Running BDD Scenarios"
                        echo "Environment: SuperApp-dev-BDD"
                        echo "======================================"
                    '''

                    for (folder in sh(
                        script: "find . -mindepth 1 -maxdepth 1 -type d ! -name '.git' ! -name 'environments' -print | sort",
                        returnStdout: true
                    ).trim().split('\n')) {

                        def folderName = folder.replace('./', '')

                        if (folderName != '01- End To End') {

                            echo "--------------------------------------"
                            echo "Running: ${folderName}"
                            echo "Environment: SuperApp-dev-BDD"
                            echo "--------------------------------------"

                            def result = sh(
                                script: """
                                    bru run "${folderName}" \
                                        --env SuperApp-dev-BDD \
                                        --reporter-junit "reports/${folderName}-junit.xml" \
                                        --reporter-html "reports/${folderName}-report.html"
                                """,
                                returnStatus: true
                            )

                            if (result != 0) {
                                echo "FAILED: ${folderName}"
                                testFailed = true
                            } else {
                                echo "PASSED: ${folderName}"
                            }
                        }
                    }

                    if (testFailed) {
                        error("One or more Bruno test suites failed.")
                    }
                }
            }
        }
    }

    post {

        always {

            junit(
                testResults: 'reports/*-junit.xml',
                allowEmptyResults: true
            )

            archiveArtifacts(
                artifacts: 'reports/*-report.html',
                allowEmptyArchive: true
            )
        }
    }
}