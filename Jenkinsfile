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
                    echo "===== Node ====="
                    node --version

                    echo "===== NPM ====="
                    npm --version

                    echo "===== Bruno ====="
                    bru --version
                '''
            }
        }

        stage('Prepare Reports') {
            steps {
                sh '''
                    rm -rf reports
                    rm -rf temp-reports
                    mkdir -p reports
                    mkdir -p temp-reports
                '''
            }
        }

        stage('Run All Scenarios') {
            steps {
                script {

                    def scenarios = [

                        // =========================
                        // End To End
                        // =========================
                        "01- End To End",

                        // =========================
                        // Login
                        // =========================
                        "02- Login/01- User successfully logs in using mobile number and OTP",
                        "02- Login/02- User attempts to log in with an invalid mobile number",
                        "02- Login/03- User submits an empty mobile number",
                        "02- Login/04- OTP is sent after submitting a valid mobile number",
                        "02- Login/05- User enters a valid OTP",
                        "02- Login/06- User enters an invalid OTP",
                        "02- Login/07- User submits an empty OTP",
                        "02- Login/08- User enters an expired OTP after 2 minutes",
                        "02- Login/09- Returning user accesses the Super App with a valid session",

                        // =========================
                        // Home - Blockchain
                        // =========================
                        "03- Home/01- Blockchain Preview/01- User sees the Blockchain entry point on the homepage",
                        "03- Home/01- Blockchain Preview/02- User opens the Blockchain Explorer from the homepage",
                        "03- Home/01- Blockchain Preview/03- User sees the current blockchain network status",
                        "03- Home/01- Blockchain Preview/04- User sees the latest available blockchain statistics",
                        "03- Home/01- Blockchain Preview/05- User sees the blockchain transaction activity trend",
                        "03- Home/01- Blockchain Preview/06- Transaction trend handles a period with no transaction data",
                        "03- Home/01- Blockchain Preview/07- User sees the latest blockchain transactions",
                        "03- Home/01- Blockchain Preview/08- Latest transactions are displayed in the correct order",
                        "03- Home/01- Blockchain Preview/09- User refreshes blockchain information",
                        "03- Home/01- Blockchain Preview/10- Blockchain information is automatically refreshed when automatic refresh is configured",
                        "03- Home/01- Blockchain Preview/11- Blockchain information in the Super App is consistent with the DotScan API",

                        // =========================
                        // Home - Cell
                        // =========================
                        "03- Home/02- Cell Perview/01- User can see DotOne Cell on the Super App",

                        // =========================
                        // Home - Gold
                        // =========================
                        "03- Home/03- Gold Perview/01- User can see DotOne Gold on the Super App",
                        "03- Home/03- Gold Perview/02- User is directed to the appropriate DotOne Gold experience"
                    ]

                    def failedScenarios = []

                    scenarios.eachWithIndex { scenario, index ->

                        echo ""
                        echo "=============================================="
                        echo "Running Scenario ${index + 1}/${scenarios.size()}"
                        echo scenario
                        echo "=============================================="

                        def reportName = "scenario-${index + 1}"

                        def exitCode = sh(
                            script: """
                                set +e

                                bru run '${scenario}' \\
                                    --env SuperApp-dev-BDD \\
                                    --reporter-junit 'temp-reports/${reportName}-junit.xml' \\
                                    --reporter-html 'temp-reports/${reportName}-report.html'

                                EXIT_CODE=\\$?

                                echo "Bruno Exit Code: \\$EXIT_CODE"

                                exit 0
                            """,
                            returnStatus: true
                        )

                        /*
                         * We intentionally DO NOT stop the pipeline.
                         * Every scenario must run.
                         */

                        if (exitCode != 0) {
                            failedScenarios.add(scenario)
                            echo "❌ FAILED: ${scenario}"
                        } else {
                            echo "✅ PASSED: ${scenario}"
                        }
                    }

                    echo ""
                    echo "=============================================="
                    echo "ALL SCENARIOS FINISHED"
                    echo "=============================================="

                    echo "Total scenarios: ${scenarios.size()}"
                    echo "Failed scenarios: ${failedScenarios.size()}"

                    if (failedScenarios.size() > 0) {

                        echo ""
                        echo "========== FAILED SCENARIOS =========="

                        failedScenarios.each {
                            echo "❌ ${it}"
                        }

                        echo "======================================="
                    }
                }
            }
        }

        stage('Collect Reports') {
            steps {
                sh '''
                    echo "===== Generated JUnit files ====="

                    find temp-reports \
                        -type f \
                        -name "*.xml" \
                        -print

                    echo ""
                    echo "===== Generated HTML files ====="

                    find temp-reports \
                        -type f \
                        -name "*.html" \
                        -print

                    echo ""
                    echo "===== Copying reports ====="

                    cp temp-reports/*.xml reports/ 2>/dev/null || true
                    cp temp-reports/*.html reports/ 2>/dev/null || true

                    echo ""
                    echo "===== Final reports ====="

                    ls -lah reports || true
                '''
            }
        }

        stage('Publish Test Report') {
            steps {
                script {

                    def junitFiles = sh(
                        script: "find reports -type f -name '*-junit.xml' | wc -l",
                        returnStdout: true
                    ).trim().toInteger()

                    echo "JUnit files found: ${junitFiles}"

                    if (junitFiles > 0) {

                        /*
                         * Jenkins combines ALL JUnit XML files
                         * into ONE Test Report.
                         */
                        junit(
                            allowEmptyResults: false,
                            testResults: 'reports/*-junit.xml'
                        )

                    } else {

                        echo "❌ No JUnit report was generated."
                        currentBuild.result = 'UNSTABLE'
                    }
                }
            }
        }
    }

    post {

        always {

            echo "===== Archiving reports ====="

            archiveArtifacts(
                artifacts: 'reports/**/*',
                allowEmptyArchive: true,
                fingerprint: true
            )
        }

        success {
            echo "======================================="
            echo "BUILD SUCCESS"
            echo "======================================="
        }

        unstable {
            echo "======================================="
            echo "BUILD UNSTABLE"
            echo "Check the test report for failures."
            echo "======================================="
        }

        failure {
            echo "======================================="
            echo "BUILD FAILED"
            echo "======================================="
        }
    }
}
