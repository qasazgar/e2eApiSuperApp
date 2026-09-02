pipeline {
agent any

```
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
                    echo "Running End To End tests..."

                    bru run "01- End To End" \
                        --env SuperApp-dev \
                        --reporter-junit "reports/e2e-junit.xml" \
                        --reporter-html "reports/e2e-report.html"
                else
                    echo "01- End To End folder not found. Skipping..."
                fi
            '''
        }
    }

    stage('Run Login Tests') {
        steps {
            sh '''
                echo "Running Login tests..."

                bru run "02- Login" \
                    --env SuperApp-dev \
                    --reporter-junit "reports/login-junit.xml" \
                    --reporter-html "reports/login-report.html"
            '''
        }
    }

    stage('Run Home Tests') {
        steps {
            sh '''
                echo "Running Home tests..."

                bru run "03- Home" \
                    --env SuperApp-dev \
                    --reporter-junit "reports/home-junit.xml" \
                    --reporter-html "reports/home-report.html"
            '''
        }
    }
}

post {

    always {

        junit testResults: 'reports/*-junit.xml',
              allowEmptyResults: true

        archiveArtifacts artifacts: 'reports/*.html',
                         allowEmptyArchive: true

    }
}
```

}
