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

                    def defaultEnv = 'SuperApp-dev-BDD'

                    def scenarios = [

                        // =====================================================
                        // 01 - End To End (Run with SuperApp-dev)
                        // =====================================================
                        [
                            name: '01-End-To-End',
                            path: '01- End To End',
                            env: 'SuperApp-dev'
                        ],

                        // =====================================================
                        // 02 - Login (Run with SuperApp-dev-BDD)
                        // =====================================================
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

                        // =====================================================
                        // 03 - Home / Blockchain Preview
                        // =====================================================
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

                        // =====================================================
                        // 03 - Home / Cell Preview
                        // =====================================================
                        [
                            name: '03-02-01-Cell-Visible',
                            path: '03- Home/02- Cell Perview/01- User can see DotOne Cell on the Super App'
                        ],

                        // =====================================================
                        // 03 - Home / Gold Preview
                        // =====================================================
                        [
                            name: '03-03-01-Gold-Visible',
                            path: '03- Home/03- Gold Perview/01- User can see DotOne Gold on the Super App'
                        ],
                        [
                            name: '03-03-02-Gold-Redirect',
                            path: '03- Home/03- Gold Perview/02- User is directed to the appropriate DotOne Gold experience'
                        ],

                        // =====================================================
                        // 03 - Home / VOD Preview
                        // =====================================================
                        [
                            name: '03-04-01-VOD-Preview',
                            path: '03- Home/04- Vod Perview/01- VOD is presented as a preview entry point'
                        ],
                        [
                            name: '03-04-02-VOD-Redirect',
                            path: '03- Home/04- Vod Perview/02- User is redirected to the VOD website'
                        ],

                        // =====================================================
                        // 03 - Home / Taxi Preview
                        // =====================================================
                        [
                            name: '03-05-01-Taxi-Visible',
                            path: '03- Home/05- Taxi Perview/01- User can see DotOne Taxi in the Super App'
                        ],
                        [
                            name: '03-05-02-Taxi-Redirect',
                            path: '03- Home/05- Taxi Perview/02- Selecting Taxi navigates the user to the Taxi experience'
                        ],

                        // =====================================================
                        // 03 - Home / Postex Preview
                        // =====================================================
                        [
                            name: '03-06-01-Postex-Visible',
                            path: '03- Home/06- Postex Perview/01- Postex is presented as an available service'
                        ],
                        [
                            name: '03-06-02-Postex-Redirect',
                            path: '03- Home/06- Postex Perview/02- Selecting Postex redirects the user to the Postex experience'
                        ],

                        // =====================================================
                        // 03 - Home / Bitbank Preview
                        // =====================================================
                        [
                            name: '03-07-01-Bitbank-Home',
                            path: '03- Home/07- Bitbank preview/01- the user is on the Super App Home Page'
                        ],
                        [
                            name: '03-07-02-Bitbank-Redirect',
                            path: '03- Home/07- Bitbank preview/02- Redirect User to Bitbank Experience'
                        ],

                        // =====================================================
                        // 03 - Home / User Profile Between MyDot and Super App
                        // =====================================================
                        [
                            name: '03-08-01-Same-User-ID',
                            path: '03- Home/08- User Profile Between MyDot and Super App/01- Same user ID is maintained across Super App and MyDot'
                        ],
                        [
                            name: '03-08-02-Authentication-Session',
                            path: '03- Home/08- User Profile Between MyDot and Super App/02- Authentication session is maintained between Super App and MyDot'
                        ],
                        [
                            name: '03-08-03-Profile-Fields-Not-Synchronized',
                            path: '03- Home/08- User Profile Between MyDot and Super App/03- Application-specific profile fields are not synchronized (user id - phone number)'
                        ],

                        // =====================================================
                        // 04 - Services
                        // =====================================================
                        [
                            name: '04-01-01-Electricity-Bill-Inquiry',
                            path: '04- Services/01- Electricity Bill Inquiry/01- Successfully inquire electricity bill with valid bill ID'
                        ]
                    ]

                    // =========================================================
                    // Test Execution
                    // =========================================================
                    def failedTests = []

                    echo ""
                    echo "=============================================="
                    echo "TOTAL SCENARIOS: ${scenarios.size()}"
                    echo "=============================================="

                    for (scenario in scenarios) {

                        def targetEnv = scenario.env ?: defaultEnv

                        echo ""
                        echo "=============================================="
                        echo "Running Scenario: ${scenario.name}"
                        echo "Path: ${scenario.path}"
                        echo "Environment: ${targetEnv}"
                        echo "=============================================="

                        def junitFile = "temp-reports/${scenario.name}-junit.xml"
                        def htmlFile = "reports/${scenario.name}-report.html"
                        def logFile = "test-logs/${scenario.name}.log"

                        def result = sh(
                            script: """
                                set +e

                                bru run "${scenario.path}" \\
                                    --env "${targetEnv}" \\
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

                    // =========================================================
                    // Save Failed Tests
                    // =========================================================
                    writeFile(
                        file: 'reports/failed-tests.txt',
                        text: failedTests.join('\n')
                    )

                    // =========================================================
                    // Test Execution Summary
                    // =========================================================
                    def totalTests = scenarios.size()
                    def failedCount = failedTests.size()
                    def passedCount = totalTests - failedCount

                    echo ""
                    echo "=============================================="
                    echo "TEST EXECUTION SUMMARY"
                    echo "=============================================="
                    echo "Total Scenarios : ${totalTests}"
                    echo "Passed          : ${passedCount}"
                    echo "Failed          : ${failedCount}"
                    echo "=============================================="

                    if (failedCount > 0) {
                        echo ""
                        echo "Failed Scenarios:"
                        echo "----------------------------------------------"
                        failedTests.each {
                            echo "❌ ${it}"
                        }
                        echo "----------------------------------------------"
                        currentBuild.result = 'UNSTABLE'
                    } else {
                        echo ""
                        echo "🎉 ALL SCENARIOS PASSED"
                    }

                    echo "=============================================="
                }
            }
        }
    }

    // ========================================================================
    // POST ACTIONS
    // ========================================================================
    post {

        always {
            echo ""
            echo "Publishing Jenkins JUnit Test Reports..."

            junit(
                testResults: 'temp-reports/*-junit.xml',
                allowEmptyResults: true,
                skipPublishingChecks: false
            )

            archiveArtifacts(
                artifacts: 'reports/**/*.html, reports/failed-tests.txt, test-logs/**/*.log',
                allowEmptyArchive: true,
                fingerprint: true
            )
        }

        unstable {
            echo ""
            echo "=============================================="
            echo "⚠️ TESTS FAILED (BUILD UNSTABLE)"
            echo "=============================================="

            script {
                if (fileExists('reports/failed-tests.txt')) {
                    def failed = readFile('reports/failed-tests.txt').trim()
                    if (failed) {
                        echo ""
                        echo "Failed scenarios detail:"
                        echo "----------------------------------------------"
                        echo failed
                        echo "----------------------------------------------"
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
