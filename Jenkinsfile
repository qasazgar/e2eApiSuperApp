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
                echo "Node version:"
                node --version

                echo "NPM version:"
                npm --version

                echo "Bruno version:"
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

    stage('Run End To End Tests') {
        steps {
            catchError(buildResult: 'UNSTABLE', stageResult: 'UNSTABLE') {
                sh '''
                    echo "===== Running End To End Tests ====="

                    bru run "01- End To End" \
                        --env SuperApp-dev-BDD \
                        --reporter-junit reports/e2e-junit.xml \
                        --reporter-html reports/e2e-report.html
                '''
            }
        }
    }

    stage('Run Login Tests') {
        steps {
            catchError(buildResult: 'UNSTABLE', stageResult: 'UNSTABLE') {
                sh '''
                    echo "===== Running Login Tests ====="

                    bru run "02- Login" \
                        --env SuperApp-dev-BDD \
                        --reporter-junit reports/login-junit.xml \
                        --reporter-html reports/login-report.html
                '''
            }
        }
    }

    stage('Run Home Tests') {
        steps {
            catchError(buildResult: 'UNSTABLE', stageResult: 'UNSTABLE') {
                sh '''
                    echo "===== Running Home Tests ====="

                    bru run "03- Home" \
                        --env SuperApp-dev-BDD \
                        --reporter-junit reports/home-junit.xml \
                        --reporter-html reports/home-report.html
                '''
            }
        }
    }
}

post {
    always {

        echo "===== Publishing Test Reports ====="

        junit allowEmptyResults: true,
              testResults: 'reports/*-junit.xml'

        archiveArtifacts artifacts: 'reports/*.html',
                         allowEmptyArchive: true
    }
}

}
