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
                mkdir -p temp-reports
            '''
        }
    }

    stage('Run All Scenarios') {
        steps {
            script {

                def scenarios = [

                    // ==============================
                    // End To End
                    // ==============================

                    '01- End To End',

                    // ==============================
                    // Login
                    // ==============================

                    '02- Login/01- User successfully logs in using mobile number and OTP',
                    '02- Login/02- User attempts to log in with an invalid mobile number',
                    '02- Login/03- User submits an empty mobile number',
                    '02- Login/04- OTP is sent after submitting a valid mobile number',
                    '02- Login/05- User enters a valid OTP',
                    '02- Login/06- User enters an invalid OTP',
                    '02- Login/07- User submits an empty OTP',
                    '02- Login/08- User enters an expired OTP after 2 minutes',
                    '02- Login/09- Returning user accesses the Super App with a valid session',

                    // ==============================
                    // Home - Blockchain Preview
                    // ==============================

                    '03- Home/01- Blockchain Preview/01- User sees the Blockchain entry point on the homepage',
                    '03- Home/01- Blockchain Preview/02- User opens the Blockchain Explorer from the homepage',
                    '03- Home/01- Blockchain Preview/03- User sees the current blockchain network status',
                    '03- Home/01- Blockchain Preview/04- User sees the latest available blockchain statistics',
                    '03- Home/01- Blockchain Preview/05- User sees the blockchain transaction activity trend',
                    '03- Home/01- Blockchain Preview/06- Transaction trend handles a period with no transaction data',
                    '03- Home/01- Blockchain Preview/07- User sees the latest blockchain transactions',
                    '03- Home/01- Blockchain Preview/08- Latest transactions are displayed in the correct order',
                    '03- Home/01- Blockchain Preview/09- User refreshes blockchain information',
                    '03- Home/01- Blockchain Preview/10- Blockchain information is automatically refreshed when automatic refresh is configured',
                    '03- Home/01- Blockchain Preview/11- Blockchain information in the Super App is consistent with the DotScan API',

                    // ==============================
                    // Home - Cell Preview
                    // ==============================

                    '03- Home/02- Cell Perview/01- User can see DotOne Cell on the Super App',

                    // ==============================
                    // Home - Gold Preview
                    // ==============================

                    '03- Home/03- Gold Perview/01- User can see DotOne Gold on the Super App',
                    '03- Home/03- Gold Perview/02- User is directed to the appropriate DotOne Gold experience'
                ]

                def failedScenarios = []

                scenarios.eachWithIndex { scenario, index ->

                    def reportId = String.format('%02d', index + 1)

                    echo "=============================================="
                    echo "Running Scenario ${reportId}"
                    echo "${scenario}"
                    echo "=============================================="

                    def exitCode = sh(
                        script: """
                            bru run "${scenario}" \
                                --env SuperApp-dev-BDD \
                                --reporter-junit "temp-reports/${reportId}-junit.xml"
                        """,
                        returnStatus: true
                    )

                    if (exitCode != 0) {
                        failedScenarios.add(scenario)
                        echo "❌ FAILED: ${scenario}"
                    } else {
                        echo "✅ PASSED: ${scenario}"
                    }
                }

                echo ""
                echo "=============================================="
                echo "Execution Summary"
                echo "=============================================="
                echo "Total Scenarios: ${scenarios.size()}"
                echo "Failed Scenarios: ${failedScenarios.size()}"
                echo "=============================================="

                if (failedScenarios.size() > 0) {
                    echo "Failed Scenarios:"

                    failedScenarios.each {
                        echo "❌ ${it}"
                    }
                }

                // Keep all scenarios executed.
                // Final result will be determined by JUnit.
            }
        }
    }

    stage('Merge Reports') {
        steps {
            sh '''
                echo "===== Merging JUnit Reports ====="

                python3 - <<'PY'
                import glob
                import xml.etree.ElementTree as ET

                files = sorted(glob.glob("temp-reports/*-junit.xml"))

                root = ET.Element("testsuites")

                total_tests = 0
                total_failures = 0
                total_errors = 0
                total_skipped = 0
                total_time = 0.0

                for file in files:
                    try:
                        tree = ET.parse(file)
                        suite_root = tree.getroot()

                        if suite_root.tag == "testsuites":
                            suites = list(suite_root)
                        else:
                            suites = [suite_root]

                        for suite in suites:
                            root.append(suite)

                            total_tests += int(suite.attrib.get("tests", 0))
                            total_failures += int(suite.attrib.get("failures", 0))
                            total_errors += int(suite.attrib.get("errors", 0))
                            total_skipped += int(suite.attrib.get("skipped", 0))
                            total_time += float(suite.attrib.get("time", 0))

                    except Exception as e:
                        print(f"Could not merge {file}: {e}")

                root.set("tests", str(total_tests))
                root.set("failures", str(total_failures))
                root.set("errors", str(total_errors))
                root.set("skipped", str(total_skipped))
                root.set("time", str(total_time))

                ET.ElementTree(root).write(
                    "reports/superapp-junit.xml",
                    encoding="utf-8",
                    xml_declaration=True
                )

                print(f"Merged {len(files)} JUnit reports")
                PY

                echo "===== JUnit Report Created ====="
                ls -lh reports/
            '''
        }
    }
}

post {
    always {

        echo "===== Publishing Combined Test Report ====="

        junit allowEmptyResults: true,
              testResults: 'reports/superapp-junit.xml'

        archiveArtifacts artifacts: 'reports/superapp-junit.xml',
                         allowEmptyArchive: true
    }
}

}
