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
                    rm -rf temp-reports
                    rm -rf test-logs

                    mkdir -p reports
                    mkdir -p temp-reports
                    mkdir -p test-logs
                '''
            }
        }

        stage('Run All Scenarios') {
            steps {
                script {

                    def scenarios = [

                        // =========================
                        // 01 - End To End
                        // =========================
                        [
                            name: '01-End-To-End',
                            path: '01- End To End'
                        ],

                        // =========================
                        // 02 - Login
                        // =========================
                        [
                            name: '02-01-Login-Valid-Mobile-OTP',
                            path: '02- Login/01- User successfully logs in using mobile number and OTP'
                        ],
                        [
                            name: '02-02-Invalid-Mobile',
                            path: '02- Login/02- User attempts to log in with an invalid mobile number'
                        ],
                        [
                            name: '02-03-Empty-Mobile',
                            path: '02- Login/03- User submits an empty mobile number'
                        ],
                        [
                            name: '02-04-OTP-Sent',
                            path: '02- Login/04- OTP is sent after submitting a valid mobile number'
                        ],
                        [
                            name: '02-05-Valid-OTP',
                            path: '02- Login/05- User enters a valid OTP'
                        ],
                        [
                            name: '02-06-Invalid-OTP',
                            path: '02- Login/06- User enters an invalid OTP'
                        ],
                        [
                            name: '02-07-Empty-OTP',
                            path: '02- Login/07- User submits an empty OTP'
                        ],
                        [
                            name: '02-08-Expired-OTP',
                            path: '02- Login/08- User enters an expired OTP after 2 minutes'
                        ],
                        [
                            name: '02-09-Returning-User',
                            path: '02- Login/09- Returning user accesses the Super App with a valid session'
                        ],

                        // =========================
                        // 03 - Home / Blockchain
                        // =========================
                        [
                            name: '03-01-01-Blockchain-Entry-Point',
                            path: '03- Home/01- Blockchain Preview/01- User sees the Blockchain entry point on the homepage'
                        ],
                        [
                            name: '03-01-02-Open-Blockchain-Explorer',
                            path: '03- Home/01- Blockchain Preview/02- User opens the Blockchain Explorer from the homepage'
                        ],
                        [
                            name: '03-01-03-Blockchain-Network-Status',
                            path: '03- Home/01- Blockchain Preview/03- User sees the current blockchain network status'
                        ],
                        [
                            name: '03-01-04-Blockchain-Statistics',
                            path: '03- Home/01- Blockchain Preview/04- User sees the latest available blockchain statistics'
                        ],
                        [
                            name: '03-01-05-Transaction-Trend',
                            path: '03- Home/01- Blockchain Preview/05- User sees the blockchain transaction activity trend'
                        ],
                        [
                            name: '03-01-06-No-Transaction-Data',
                            path: '03- Home/01- Blockchain Preview/06- Transaction trend handles a period with no transaction data'
                        ],
                        [
                            name: '03-01-07-Latest-Transactions',
                            path: '03- Home/01- Blockchain Preview/07- User sees the latest blockchain transactions'
                        ],
                        [
                            name: '03-01-08-Correct-Transaction-Order',
                            path: '03- Home/01- Blockchain Preview/08- Latest transactions are displayed in the correct order'
                        ],
                        [
                            name: '03-01-09-Refresh-Blockchain',
                            path: '03- Home/01- Blockchain Preview/09- User refreshes blockchain information'
                        ],
                        [
                            name: '03-01-10-Automatic-Refresh',
                            path: '03- Home/01- Blockchain Preview/10- Blockchain information is automatically refreshed when automatic refresh is configured'
                        ],
                        [
                            name: '03-01-11-DotScan-Consistency',
                            path: '03- Home/01- Blockchain Preview/11- Blockchain information in the Super App is consistent with the DotScan API'
                        ],

                        // =========================
                        // 03 - Home / Cell
                        // =========================
                        [
                            name: '03-02-01-Cell-Visible',
                            path: '03- Home/02- Cell Perview/01- User can see DotOne Cell on the Super App'
                        ],

                        // =========================
                        // 03 - Home / Gold
                        // =========================
                        [
                            name: '03-03-01-Gold-Visible',
                            path: '03- Home/03- Gold Perview/01- User can see DotOne Gold on the Super App'
                        ],
                        [
                            name: '03-03-02-Gold-Redirect',
                            path: '03- Home/03- Gold Perview/02- User is directed to the appropriate DotOne Gold experience'
                        ]
                    ]

                    def failedTests = []

                    for (scenario in scenarios) {

                        echo ""
                        echo "=============================================="
                        echo "Running Scenario:"
                        echo scenario.name
                        echo "Path:"
                        echo scenario.path
                        echo "=============================================="

                        def junitFile =
                            "temp-reports/${scenario.name}-junit.xml"

                        def htmlFile =
                            "reports/${scenario.name}-report.html"

                        def logFile =
                            "test-logs/${scenario.name}.log"

                        /*
                         * returnStatus=true
                         * باعث می‌شود اگر Bruno Fail شد،
                         * Pipeline متوقف نشود.
                         */

                        def result = sh(
                            script: """
                                set +e

                                bru run "${scenario.path}" \\
                                    --env SuperApp-dev-BDD \\
                                    --reporter-junit "${junitFile}" \\
                                    --reporter-html "${htmlFile}" \\
                                    2>&1 | tee "${logFile}"

                                EXIT_CODE=\${PIPESTATUS[0]}

                                echo ""
                                echo "Bruno Exit Code: \$EXIT_CODE"

                                exit \$EXIT_CODE
                            """,
                            returnStatus: true
                        )

                        if (result != 0) {
                            failedTests.add(scenario.name)

                            echo ""
                            echo "❌ FAILED: ${scenario.name}"
                            echo "Exit Code: ${result}"
                        } else {
                            echo ""
                            echo "✅ PASSED: ${scenario.name}"
                        }

                        echo ""
                    }

                    /*
                     * ذخیره لیست Failها
                     */
                    writeFile(
                        file: 'reports/failed-tests.txt',
                        text: failedTests.join('\n')
                    )

                    echo ""
                    echo "=============================================="
                    echo "TEST EXECUTION SUMMARY"
                    echo "=============================================="

                    echo "Total Scenarios: ${scenarios.size()}"
                    echo "Failed Scenarios: ${failedTests.size()}"
                    echo "Passed Scenarios: ${scenarios.size() - failedTests.size()}"

                    if (failedTests.size() > 0) {

                        echo ""
                        echo "Failed Scenarios:"

                        failedTests.each {
                            echo "❌ ${it}"
                        }

                    } else {
                        echo ""
                        echo "🎉 ALL SCENARIOS PASSED"
                    }

                    echo "=============================================="
                }
            }
        }
    }

    post {

        /*
         * ==========================================
         * JUNIT TEST REPORT
         * ==========================================
         */

        always {

            echo "Publishing Jenkins JUnit Test Reports..."

            junit(
                testResults: 'temp-reports/*-junit.xml',
                allowEmptyResults: false,
                skipPublishingChecks: false
            )

            /*
             * Archive HTML + Logs
             */

            archiveArtifacts(
                artifacts: 'reports/**/*.html, reports/failed-tests.txt, test-logs/**/*.log',
                allowEmptyArchive: true,
                fingerprint: true
            )
        }

        /*
         * اگر حداقل یک تست Fail شده باشد
         * Pipeline = UNSTABLE
         */

        unstable {

            echo ""
            echo "=============================================="
            echo "⚠️ TESTS FAILED"
            echo "=============================================="

            script {

                if (fileExists('reports/failed-tests.txt')) {

                    def failed =
                        readFile('reports/failed-tests.txt').trim()

                    if (failed) {
                        echo "Failed scenarios:"
                        echo failed
                    }
                }
            }
        }

        success {

            echo ""
            echo "=============================================="
            echo "✅ ALL TESTS PASSED"
            echo "=============================================="
        }

        failure {

            echo ""
            echo "=============================================="
            echo "❌ PIPELINE FAILED"
            echo "=============================================="
        }

        cleanup {

            echo ""
            echo "=============================================="
            echo "Jenkins Test Execution Completed"
            echo "=============================================="
        }
    }
}
