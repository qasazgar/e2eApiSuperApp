pipeline {

    agent any

    stages {

        // ==========================================================
        // CHECK ENVIRONMENT
        // ==========================================================
        stage('Check Environment') {
            steps {
                sh '''
                    echo "======================================"
                    echo " Environment"
                    echo "======================================"

                    node --version
                    npm --version
                    bru --version
                '''
            }
        }


        // ==========================================================
        // PREPARE REPORTS
        // ==========================================================
        stage('Prepare Reports') {
            steps {
                sh '''
                    rm -rf reports
                    mkdir -p reports

                    echo "Reports directory prepared."
                '''
            }
        }


        // ==========================================================
        // END TO END - DEV
        // ==========================================================
        stage('Run End To End - Dev') {
            steps {
                catchError(
                    buildResult: 'FAILURE',
                    stageResult: 'FAILURE'
                ) {
                    sh '''
                        echo "======================================"
                        echo " Running 01- End To End (Dev)"
                        echo "======================================"

                        bru run "01- End To End (Dev)" \
                            --env SuperApp-dev \
                            --reporter-junit reports/end-to-end-dev-junit.xml \
                            --reporter-html reports/end-to-end-dev-report.html
                    '''
                }
            }
        }


        // ==========================================================
        // REGISTRATION
        // ==========================================================
        stage('Run Registration Scenarios') {
            steps {
                script {

                    def collection = "02- Registration (Dev)"

                    def scenarios = sh(
                        script: """
                            find '${collection}' \
                            -mindepth 1 \
                            -maxdepth 1 \
                            -type d \
                            | sort
                        """,
                        returnStdout: true
                    ).trim()

                    if (!scenarios) {
                        error("No Registration scenarios found")
                    }

                    scenarios.split('\n').eachWithIndex { scenario, index ->

                        def scenarioName = scenario
                            .replace("${collection}/", "")
                            .replaceAll(/[^a-zA-Z0-9]+/, "-")
                            .replaceAll(/^-|-$/, "")
                            .toLowerCase()

                        catchError(
                            buildResult: 'FAILURE',
                            stageResult: 'FAILURE'
                        ) {
                            sh """
                                echo "======================================"
                                echo " Running Registration: ${scenarioName}"
                                echo "======================================"

                                bru run "${scenario}" \
                                    --env SuperApp-dev-BDD \
                                    --reporter-junit "reports/registration-${index + 1}-${scenarioName}-junit.xml" \
                                    --reporter-html "reports/registration-${index + 1}-${scenarioName}-report.html"
                            """
                        }
                    }
                }
            }
        }


        // ==========================================================
        // LOGIN
        // ==========================================================
        stage('Run Login Scenarios') {
            steps {
                script {

                    def collection = "03- Login (Dev)"

                    def scenarios = sh(
                        script: """
                            find '${collection}' \
                            -mindepth 1 \
                            -maxdepth 1 \
                            -type d \
                            | sort
                        """,
                        returnStdout: true
                    ).trim()

                    if (!scenarios) {
                        error("No Login scenarios found")
                    }

                    scenarios.split('\n').eachWithIndex { scenario, index ->

                        def scenarioName = scenario
                            .replace("${collection}/", "")
                            .replaceAll(/[^a-zA-Z0-9]+/, "-")
                            .replaceAll(/^-|-$/, "")
                            .toLowerCase()

                        catchError(
                            buildResult: 'FAILURE',
                            stageResult: 'FAILURE'
                        ) {
                            sh """
                                echo "======================================"
                                echo " Running Login: ${scenarioName}"
                                echo "======================================"

                                bru run "${scenario}" \
                                    --env SuperApp-dev-BDD \
                                    --reporter-junit "reports/login-${index + 1}-${scenarioName}-junit.xml" \
                                    --reporter-html "reports/login-${index + 1}-${scenarioName}-report.html"
                            """
                        }
                    }
                }
            }
        }


        // ==========================================================
        // HOME
        // ==========================================================
        stage('Run Home Scenarios') {
            steps {
                script {

                    def collection = "04- Home (Dev)"

                    def scenarios = sh(
                        script: """
                            find '${collection}' \
                            -mindepth 1 \
                            -maxdepth 1 \
                            -type d \
                            | sort
                        """,
                        returnStdout: true
                    ).trim()

                    if (!scenarios) {
                        error("No Home scenarios found")
                    }

                    scenarios.split('\n').eachWithIndex { scenario, index ->

                        def scenarioName = scenario
                            .replace("${collection}/", "")
                            .replaceAll(/[^a-zA-Z0-9]+/, "-")
                            .replaceAll(/^-|-$/, "")
                            .toLowerCase()

                        catchError(
                            buildResult: 'FAILURE',
                            stageResult: 'FAILURE'
                        ) {
                            sh """
                                echo "======================================"
                                echo " Running Home: ${scenarioName}"
                                echo "======================================"

                                bru run "${scenario}" \
                                    --env SuperApp-dev-BDD \
                                    --reporter-junit "reports/home-${index + 1}-${scenarioName}-junit.xml" \
                                    --reporter-html "reports/home-${index + 1}-${scenarioName}-report.html"
                            """
                        }
                    }
                }
            }
        }


        // ==========================================================
        // SERVICES
        // ==========================================================
        stage('Run Services Scenarios') {
            steps {
                script {

                    def collection = "05- Services (Dev)"

                    def scenarios = sh(
                        script: """
                            find '${collection}' \
                            -mindepth 1 \
                            -maxdepth 1 \
                            -type d \
                            | sort
                        """,
                        returnStdout: true
                    ).trim()

                    if (!scenarios) {
                        error("No Services scenarios found")
                    }

                    scenarios.split('\n').eachWithIndex { scenario, index ->

                        def scenarioName = scenario
                            .replace("${collection}/", "")
                            .replaceAll(/[^a-zA-Z0-9]+/, "-")
                            .replaceAll(/^-|-$/, "")
                            .toLowerCase()

                        catchError(
                            buildResult: 'FAILURE',
                            stageResult: 'FAILURE'
                        ) {
                            sh """
                                echo "======================================"
                                echo " Running Services: ${scenarioName}"
                                echo "======================================"

                                bru run "${scenario}" \
                                    --env SuperApp-dev-BDD \
                                    --reporter-junit "reports/services-${index + 1}-${scenarioName}-junit.xml" \
                                    --reporter-html "reports/services-${index + 1}-${scenarioName}-report.html"
                            """
                        }
                    }
                }
            }
        }


        // ==========================================================
        // END TO END - STAGE
        // ==========================================================
        stage('Run End To End - Stage') {
            steps {
                catchError(
                    buildResult: 'FAILURE',
                    stageResult: 'FAILURE'
                ) {
                    sh '''
                        echo "======================================"
                        echo " Running 06- End To End (Stage)"
                        echo "======================================"

                        bru run "06- End To End (Stage)" \
                            --env VOD-stage \
                            --reporter-junit reports/end-to-end-stage-junit.xml \
                            --reporter-html reports/end-to-end-stage-report.html
                    '''
                }
            }
        }
    }


    // ==========================================================
    // POST
    // ==========================================================
    post {

        always {

            echo "======================================"
            echo " Publishing Reports"
            echo "======================================"

            junit(
                allowEmptyResults: true,
                testResults: 'reports/*-junit.xml'
            )

            archiveArtifacts(
                artifacts: 'reports/*.html',
                allowEmptyArchive: true
            )
        }
    }
}