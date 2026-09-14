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

        /*
         * ==========================================
         * END TO END
         * Environment: SuperApp-dev
         * ==========================================
         */

        stage('Run End To End') {
            steps {
                script {

                    def scenarios = [
                        '01- End To End'
                    ]

                    for (scenario in scenarios) {

                        stage("Run End To End") {

                            catchError(
                                buildResult: 'FAILURE',
                                stageResult: 'FAILURE'
                            ) {

                                sh """
                                    echo "======================================"
                                    echo " Running: ${scenario}"
                                    echo " Environment: SuperApp-dev"
                                    echo "======================================"

                                    bru run "${scenario}" \
                                        --env SuperApp-dev \
                                        --reporter-junit reports/end-to-end-junit.xml \
                                        --reporter-html reports/end-to-end-report.html

                                    echo "======================================"
                                    echo " Completed: ${scenario}"
                                    echo "======================================"
                                """
                            }
                        }
                    }
                }
            }
        }

        /*
         * ==========================================
         * LOGIN
         * Environment: SuperApp-dev-BDD
         * ==========================================
         */

        stage('Run Login Scenarios') {
            steps {
                script {

                    def scenarios = [
                        '02- Login/01- User successfully logs in using mobile number and OTP',
                        '02- Login/02- User attempts to log in with an invalid mobile number',
                        '02- Login/03- User submits an empty mobile number',
                        '02- Login/04- OTP is sent after submitting a valid mobile number',
                        '02- Login/05- User enters a valid OTP',
                        '02- Login/06- User enters an invalid OTP',
                        '02- Login/07- User submits an empty OTP',
                        '02- Login/08- User enters an expired OTP after 2 minutes',
                        '02- Login/09- Returning user accesses the Super App with a valid session'
                    ]

                    for (int i = 0; i < scenarios.size(); i++) {

                        def scenario = scenarios[i]
                        def reportName = "login-${i + 1}"

                        stage("Run ${reportName}") {

                            catchError(
                                buildResult: 'FAILURE',
                                stageResult: 'FAILURE'
                            ) {

                                sh """
                                    echo "======================================"
                                    echo " Running: ${scenario}"
                                    echo " Environment: SuperApp-dev-BDD"
                                    echo "======================================"

                                    bru run "${scenario}" \
                                        --env SuperApp-dev-BDD \
                                        --reporter-junit reports/${reportName}-junit.xml \
                                        --reporter-html reports/${reportName}-report.html

                                    echo "======================================"
                                    echo " Completed: ${scenario}"
                                    echo "======================================"
                                """
                            }
                        }
                    }
                }
            }
        }

        /*
         * ==========================================
         * HOME
         * Environment: SuperApp-dev-BDD
         * ==========================================
         */

        stage('Run Home Scenarios') {
            steps {
                script {

                    def scenarios = [
                        '03- Home/01- Blockchain Preview',
                        '03- Home/02- Cell Perview',
                        '03- Home/03- Gold Perview',
                        '03- Home/04- Vod Perview',
                        '03- Home/05- Taxi Perview',
                        '03- Home/06- Postex Perview',
                        '03- Home/07- Bitbank preview',
                        '03- Home/08- User Profile Between MyDot and Super App'
                    ]

                    for (int i = 0; i < scenarios.size(); i++) {

                        def scenario = scenarios[i]
                        def reportName = "home-${i + 1}"

                        stage("Run ${reportName}") {

                            catchError(
                                buildResult: 'FAILURE',
                                stageResult: 'FAILURE'
                            ) {

                                sh """
                                    echo "======================================"
                                    echo " Running: ${scenario}"
                                    echo " Environment: SuperApp-dev-BDD"
                                    echo "======================================"

                                    bru run "${scenario}" \
                                        --env SuperApp-dev-BDD \
                                        --reporter-junit reports/${reportName}-junit.xml \
                                        --reporter-html reports/${reportName}-report.html

                                    echo "======================================"
                                    echo " Completed: ${scenario}"
                                    echo "======================================"
                                """
                            }
                        }
                    }
                }
            }
        }

        /*
         * ==========================================
         * SERVICES
         * Environment: SuperApp-dev-BDD
         * ==========================================
         */

        stage('Run Services Scenarios') {
            steps {
                script {

                    def scenarios = [
                        '04- Services/01- Electricity Bill Inquiry',
                        '04- Services/02- Gas Bill Inquiry',
                        '04- Services/03- Water Bill Inquiry'
                    ]

                    for (int i = 0; i < scenarios.size(); i++) {

                        def scenario = scenarios[i]
                        def reportName = "services-${i + 1}"

                        stage("Run ${reportName}") {

                            catchError(
                                buildResult: 'FAILURE',
                                stageResult: 'FAILURE'
                            ) {

                                sh """
                                    echo "======================================"
                                    echo " Running: ${scenario}"
                                    echo " Environment: SuperApp-dev-BDD"
                                    echo "======================================"

                                    bru run "${scenario}" \
                                        --env SuperApp-dev-BDD \
                                        --reporter-junit reports/${reportName}-junit.xml \
                                        --reporter-html reports/${reportName}-report.html

                                    echo "======================================"
                                    echo " Completed: ${scenario}"
                                    echo "======================================"
                                """
                            }
                        }
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