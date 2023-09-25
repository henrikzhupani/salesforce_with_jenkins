pipeline {
    agent any

    environment {
        HUB_ORG = 'jenkins.users@calmcoders.net'
        SFDC_HOST = 'https://login.salesforce.com'
        JWT_KEY_CRED_ID = 'ce113bda-5be4-45c6-9f1b-2dc564dcc235'
        CONNECTED_APP_CONSUMER_KEY = '3MVG97srI77Z1g7.i7q8BcJvLjplnAfz9UZfwT..PPAjqEGi5ZsYbc3GiLGSXwQvTXfp8BKbbIvD5hqmlHC.T'
    }

    stages {
        stage('Deploy to Salesforce') {
            steps {
                script {
                    def deployResult = bat(
                        script: """
                            sfdx force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} \
                            --jwtkeyfile server.key \
                            --username ${HUB_ORG} \
                            --instanceurl ${SFDC_HOST} \
                            --setdefaultdevhubusername \
                            --setalias DevPiu
                            sfdx force:source:deploy -p DeployPackage/src \
                            -u DevPiu \
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
