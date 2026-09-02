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
            sh '''
                echo "===== Running End To End Tests ====="

                set +e

                bru run "01- End To End" \
                    --env SuperApp-dev-BDD \
                    --reporter-junit reports/e2e-junit.xml \
                    --reporter-html reports/e2e-report.html

                E2E_EXIT_CODE=$?

                echo "E2E Exit Code: $E2E_EXIT_CODE"

                # Do not stop pipeline here
                exit 0
            '''
        }
    }

    stage('Run Login Tests') {
        steps {
            sh '''
                echo "===== Running Login Tests ====="

                set +e

                bru run "02- Login" \
                    --env SuperApp-dev-BDD \
                    --reporter-junit reports/login-junit.xml \
                    --reporter-html reports/login-report.html

                LOGIN_EXIT_CODE=$?

                echo "Login Exit Code: $LOGIN_EXIT_CODE"

                # Do not stop pipeline here
                exit 0
            '''
        }
    }

    stage('Run Home Tests') {
        steps {
            sh '''
                echo "===== Running Home Tests ====="

                set +e

                bru run "03- Home" \
                    --env SuperApp-dev-BDD \
                    --reporter-junit reports/home-junit.xml \
                    --reporter-html reports/home-report.html

                HOME_EXIT_CODE=$?

                echo "Home Exit Code: $HOME_EXIT_CODE"

                # Do not stop pipeline here
                exit 0
            '''
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

    success {
        echo "===== All stages completed successfully ====="
    }

    failure {
        echo "===== Pipeline failed ====="
    }
}


}
