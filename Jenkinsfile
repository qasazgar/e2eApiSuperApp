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
                sh '''
                    echo "======================================"
                    echo "Running End To End"
                    echo "Environment: SuperApp-dev"
                    echo "======================================"

                    bru run "01- End To End" \
                        --env SuperApp-dev \
                        --reporter-junit reports/e2e-junit.xml \
                        --reporter-html reports/e2e-report.html


                    echo "======================================"
                    echo "Running BDD Scenarios"
                    echo "Environment: SuperApp-dev-BDD"
                    echo "======================================"

                    for folder in */; do

                        if [ "$folder" != "01- End To End/" ] && [ "$folder" != "environments/" ]; then

                            folder_name="${folder%/}"

                            echo "--------------------------------------"
                            echo "Running: $folder_name"
                            echo "Environment: SuperApp-dev-BDD"
                            echo "--------------------------------------"

                            bru run "$folder_name" \
                                --env SuperApp-dev-BDD \
                                --reporter-junit "reports/${folder_name}-junit.xml" \
                                --reporter-html "reports/${folder_name}-report.html"

                        fi

                    done
                '''
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