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

    stage('Run End To End') {
        steps {
            script {
                catchError(
                    buildResult: 'FAILURE',
                    stageResult: 'FAILURE'
                ) {
                    sh '''
                        echo "======================================"
                        echo " Running End To End"
                        echo "======================================"

                        bru run "01- End To End" \
                            --env SuperApp-dev \
                            --reporter-junit reports/end-to-end-junit.xml \
                            --reporter-html reports/end-to-end-report.html

                        echo "======================================"
                        echo " End To End Completed"
                        echo "======================================"
                    '''
                }
            }
        }
    }

    stage('Run Login Scenarios') {
        steps {
            script {

                def scenarios = [
                    [
                        name: '01- User successfully logs in using mobile number and OTP',
                        report: 'login-01'
                    ],
                    [
                        name: '02- User attempts to log in with an invalid mobile number',
                        report: 'login-02'
                    ],
                    [
                        name: '03- User submits an empty mobile number',
                        report: 'login-03'
                    ],
                    [
                        name: '04- OTP is sent after submitting a valid mobile number',
                        report: 'login-04'
                    ],
                    [
                        name: '05- User enters a valid OTP',
                        report: 'login-05'
                    ],
                    [
                        name: '06- User enters an invalid OTP',
                        report: 'login-06'
                    ],
                    [
                        name: '07- User submits an empty OTP',
                        report: 'login-07'
                    ],
                    [
                        name: '08- User enters an expired OTP after 2 minutes',
                        report: 'login-08'
                    ],
                    [
                        name: '09- Returning user accesses the Super App with a valid session',
                        report: 'login-09'
                    ]
                ]

                for (scenario in scenarios) {

                    stage("Run ${scenario.report.toUpperCase()}") {

                        catchError(
                            buildResult: 'FAILURE',
                            stageResult: 'FAILURE'
                        ) {

                            sh """
                                echo "======================================"
                                echo " Running: ${scenario.name}"
                                echo "======================================"

                                bru run "02- Login/${scenario.name}" \
                                    --env SuperApp-dev-BDD \
                                    --reporter-junit reports/${scenario.report}-junit.xml \
                                    --reporter-html reports/${scenario.report}-report.html

                                echo "======================================"
                                echo " Completed: ${scenario.name}"
                                echo "======================================"
                            """
                        }
                    }
                }
            }
        }
    }

    stage('Run Home Scenarios') {
        steps {
            script {

                def scenarios = [
                    [
                        folder: '01- Blockchain Preview',
                        name: '01- User sees the Blockchain entry point on the homepage',
                        report: 'home-blockchain-01'
                    ],
                    [
                        folder: '01- Blockchain Preview',
                        name: '02- User opens the Blockchain Explorer from the homepage',
                        report: 'home-blockchain-02'
                    ],
                    [
                        folder: '01- Blockchain Preview',
                        name: '03- User sees the current blockchain network status',
                        report: 'home-blockchain-03'
                    ],
                    [
                        folder: '01- Blockchain Preview',
                        name: '04- User sees the latest available blockchain statistics',
                        report: 'home-blockchain-04'
                    ],
                    [
                        folder: '01- Blockchain Preview',
                        name: '05- User sees the blockchain transaction activity trend',
                        report: 'home-blockchain-05'
                    ],
                    [
                        folder: '01- Blockchain Preview',
                        name: '06- Transaction trend handles a period with no transaction data',
                        report: 'home-blockchain-06'
                    ],
                    [
                        folder: '01- Blockchain Preview',
                        name: '07- User sees the latest blockchain transactions',
                        report: 'home-blockchain-07'
                    ],
                    [
                        folder: '01- Blockchain Preview',
                        name: '08- Latest transactions are displayed in the correct order',
                        report: 'home-blockchain-08'
                    ],
                    [
                        folder: '01- Blockchain Preview',
                        name: '09- User refreshes blockchain information',
                        report: 'home-blockchain-09'
                    ],
                    [
                        folder: '01- Blockchain Preview',
                        name: '10- Blockchain information is automatically refreshed when automatic refresh is configured',
                        report: 'home-blockchain-10'
                    ],
                    [
                        folder: '01- Blockchain Preview',
                        name: '11- Blockchain information in the Super App is consistent with the DotScan API',
                        report: 'home-blockchain-11'
                    ],
                    [
                        folder: '02- Cell Perview',
                        name: '01- User can see DotOne Cell on the Super App',
                        report: 'home-cell-01'
                    ],
                    [
                        folder: '03- Gold Perview',
                        name: '01- User can see DotOne Gold on the Super App',
                        report: 'home-gold-01'
                    ],
                    [
                        folder: '03- Gold Perview',
                        name: '02- User is directed to the appropriate DotOne Gold experience',
                        report: 'home-gold-02'
                    ],
                    [
                        folder: '04- Vod Perview',
                        name: '01- VOD is presented as a preview entry point',
                        report: 'home-vod-01'
                    ],
                    [
                        folder: '04- Vod Perview',
                        name: '02- User is redirected to the VOD website',
                        report: 'home-vod-02'
                    ],
                    [
                        folder: '05- Taxi Perview',
                        name: '01- User can see DotOne Taxi in the Super App',
                        report: 'home-taxi-01'
                    ],
                    [
                        folder: '05- Taxi Perview',
                        name: '02- Selecting Taxi navigates the user to the Taxi experience',
                        report: 'home-taxi-02'
                    ],
                    [
                        folder: '06- Postex Perview',
                        name: '01- Postex is presented as an available service',
                        report: 'home-postex-01'
                    ],
                    [
                        folder: '06- Postex Perview',
                        name: '02- Selecting Postex redirects the user to the Postex experience',
                        report: 'home-postex-02'
                    ],
                    [
                        folder: '07- Bitbank preview',
                        name: '01- the user is on the Super App Home Page',
                        report: 'home-bitbank-01'
                    ],
                    [
                        folder: '07- Bitbank preview',
                        name: '02- Redirect User to Bitbank Experience',
                        report: 'home-bitbank-02'
                    ],
                    [
                        folder: '08- User Profile Between MyDot and Super App',
                        name: '01- Same user ID is maintained across Super App and MyDot',
                        report: 'home-profile-01'
                    ],
                    [
                        folder: '08- User Profile Between MyDot and Super App',
                        name: '02- Authentication session is maintained between Super App and MyDot',
                        report: 'home-profile-02'
                    ],
                    [
                        folder: '08- User Profile Between MyDot and Super App',
                        name: '03- Application-specific profile fields are not synchronized (user id - phone number)',
                        report: 'home-profile-03'
                    ]
                ]

                for (scenario in scenarios) {

                    stage("Run ${scenario.report.toUpperCase()}") {

                        catchError(
                            buildResult: 'FAILURE',
                            stageResult: 'FAILURE'
                        ) {

                            sh """
                                echo "======================================"
                                echo " Running: ${scenario.name}"
                                echo "======================================"

                                bru run "03- Home/${scenario.folder}/${scenario.name}" \
                                    --env SuperApp-dev-BDD \
                                    --reporter-junit reports/${scenario.report}-junit.xml \
                                    --reporter-html reports/${scenario.report}-report.html

                                echo "======================================"
                                echo " Completed: ${scenario.name}"
                                echo "======================================"
                            """
                        }
                    }
                }
            }
        }
    }

    stage('Run Services Scenarios') {
        steps {
            script {

                def scenarios = [
                    [
                        folder: '01- Electricity Bill Inquiry',
                        name: '01- Successfully inquire electricity bill with valid bill ID',
                        report: 'services-electricity-01'
                    ],
                    [
                        folder: '01- Electricity Bill Inquiry',
                        name: '02- Prevent electricity bill inquiry when required input is empty',
                        report: 'services-electricity-02'
                    ],
                    [
                        folder: '01- Electricity Bill Inquiry',
                        name: '03- Prevent electricity bill inquiry with invalid bill ID',
                        report: 'services-electricity-03'
                    ],
                    [
                        folder: '02- Gas Bill Inquiry',
                        name: '01- Successfully inquire gas bill using GasBillID',
                        report: 'services-gas-01'
                    ],
                    [
                        folder: '02- Gas Bill Inquiry',
                        name: '02- Prevent gas bill inquiry when both ParticipateCode and GasBillID are empty',
                        report: 'services-gas-02'
                    ],
                    [
                        folder: '02- Gas Bill Inquiry',
                        name: '03- Prevent gas bill inquiry with invalid GasBillID',
                        report: 'services-gas-03'
                    ],
                    [
                        folder: '02- Gas Bill Inquiry',
                        name: '04- Prevent gas bill inquiry with invalid ParticipateCode',
                        report: 'services-gas-04'
                    ],
                    [
                        folder: '02- Gas Bill Inquiry',
                        name: '05- Successfully inquire gas bill using ParticipateCode',
                        report: 'services-gas-05'
                    ],
                    [
                        folder: '02- Gas Bill Inquiry',
                        name: '06- Prevent gas bill inquiry when both ParticipateCode and GasBillID are provided',
                        report: 'services-gas-06'
                    ],
                    [
                        folder: '03- Water Bill Inquiry',
                        name: '01- Successfully inquire water bill with valid bill information',
                        report: 'services-water-01'
                    ]
                ]

                for (scenario in scenarios) {

                    stage("Run ${scenario.report.toUpperCase()}") {

                        catchError(
                            buildResult: 'FAILURE',
                            stageResult: 'FAILURE'
                        ) {

                            sh """
                                echo "======================================"
                                echo " Running: ${scenario.name}"
                                echo "======================================"

                                bru run "04- Services/${scenario.folder}/${scenario.name}" \
                                    --env SuperApp-dev-BDD \
                                    --reporter-junit reports/${scenario.report}-junit.xml \
                                    --reporter-html reports/${scenario.report}-report.html

                                echo "======================================"
                                echo " Completed: ${scenario.name}"
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
