pipeline {
    agent any

    environment {
        HUB_ORG = 'jenkins.users@calmcoders.net'
        SFDC_HOST = 'https://login.salesforce.com'
        JWT_KEY_CRED_ID = 'ce113bda-5be4-45c6-9f1b-2dc564dcc235'
        CONNECTED_APP_CONSUMER_KEY = '3MVG97srI77Z1g7.i7q8BcJvLjplnAfz9UZfwT..PPAjqEGi5ZsYbc3GiLGSXwQvTXfp8BKbbIvD5hqmlHC.T'
        SOURCE_DIRECTORY = 'C:\\Users\\Henrik Zhupani\\Desktop\\salesforce_with_jenkins\\salesforce_with_jenkins'
        SERVER_KEY = 'C:\\openssl\\bin\\server.key'
    }

    stages {
        stage('Connect to Salesforce') {
            steps {
                script {
                    def deployResult = bat(
                        script: """
                            sfdx force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} \
                            --jwtkeyfile ${SERVER_KEY} \
                            --username ${HUB_ORG} \
                            --instanceurl ${SFDC_HOST} \
                            --setdefaultdevhubusername \
                            --setalias DevPiu
                        """,
                        returnStatus: true
                    )

                    if (deployResult != 0) {
                        error("Salesforce deployment failed.")
                    }
                }
            }
        }
        stage('Check PR Comment') {
            steps {
                script {
                    // Get the PR comment from the environment
                    PR_COMMENT = env.PR_COMMENT ?: ''
                }
            }
        }
        
         stage('Deploy to Salesforce') {
            when {
                expression {
                    // Check if the PR comment contains "deploy to DevPiu"
                    PR_COMMENT && PR_COMMENT.contains("deploy to DevPiu")
                    }
                }
            steps {
                script {
                    def deployResult = bat(
                        script: """
                            sfdx force:source:deploy -p "${SOURCE_DIRECTORY}" \
                            -u DevPiu \
                            -w 10 \
                            --testlevel NoTestRun  
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
