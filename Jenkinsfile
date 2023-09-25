pipeline {
    agent any

    environment {
        BUILD_NUMBER = env.BUILD_NUMBER
        RUN_ARTIFACT_DIR = "tests/${BUILD_NUMBER}"
        SFDC_USERNAME
        HUB_ORG = 'jenkins.users@calmcoders.net'
        SFDC_HOST = 'https://login.salesforce.com'
        JWT_KEY_CRED_ID = 'da0cc199-1f65-476f-943d-56d1739a9b9e'
        CONNECTED_APP_CONSUMER_KEY = '3MVG97srI77Z1g7.i7q8BcJvLjplnAfz9UZfwT..PPAjqEGi5ZsYbc3GiLGSXwQvTXfp8BKbbIvD5hqmlHC.T'
    }

    stages {
        stage('checkout source') {
            steps {
                checkout scm
            }
        }

        stage('Deploy to Salesforce') {
            steps {
                script {
                    def deployResult = sh(
                        script: """
                            sfdx force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} \
                            --jwtkeyfile \${JENKINS_HOME}/secrets/${JWT_KEY_CRED_ID}.key \
                            --username ${HUB_ORG} \
                            --instanceurl ${SFDC_HOST} \
                            --setdefaultdevhubusername \
                            --setalias my-hub-org
                            sfdx force:source:deploy -p DeployPackage/src \
                            -u my-hub-org \
                            -w <deployment-wait-time-in-minutes> \
                            --testlevel RunLocalTests
                        """,
                        returnStatus: true
                    )

                    if (deployResult != 0) {
                        error("Salesforce deployment failed.")
                    }
                }
            }
        }
    }
}
