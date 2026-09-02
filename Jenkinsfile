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

    stage('Run End To End') {
        steps {
            sh '''
                if [ -d "01- End To End" ]; then

                    echo "======================================"
                    echo "Running End To End tests"
                    echo "======================================"

                    set +e

                    bru run "01- End To End" \
                        --env SuperApp-dev \
                        --reporter-junit "reports/e2e-junit.xml" \
                        --reporter-html "reports/e2e-report.html"

                    E2E_EXIT_CODE=$?

                    echo "End To End exit code: $E2E_EXIT_CODE"

                    exit 0

                else
                    echo "01- End To End folder not found. Skipping..."
                fi
            '''
        }
    }

    stage('Run Login Tests') {
        steps {
            sh '''
                echo "======================================"
                echo "Running Login tests"
                echo "======================================"

                set +e

                bru run "02- Login" \
                    --env SuperApp-dev-BDD \
                    --reporter-junit "reports/login-junit.xml" \
                    --reporter-html "reports/login-report.html"

                LOGIN_EXIT_CODE=$?

                echo "Login exit code: $LOGIN_EXIT_CODE"

                exit 0
            '''
        }
    }

    stage('Run Home Tests') {
        steps {
            sh '''
                echo "======================================"
                echo "Running Home tests"
                echo "======================================"

                set +e

                bru run "03- Home" \
                    --env SuperApp-dev-BDD \
                    --reporter-junit "reports/home-junit.xml" \
                    --reporter-html "reports/home-report.html"

                HOME_EXIT_CODE=$?

                echo "Home exit code: $HOME_EXIT_CODE"

                exit 0
            '''
        }
    }
}

post {

    always {

        echo "======================================"
        echo "Publishing Test Reports"
        echo "======================================"

        junit testResults: 'reports/*-junit.xml',
              allowEmptyResults: true

        archiveArtifacts artifacts: 'reports/*.html',
                         allowEmptyArchive: true
    }
}


}
