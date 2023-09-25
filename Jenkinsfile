import groovy.json.JsonSlurperClassic

node {

    def BUILD_NUMBER = env.BUILD_NUMBER
    def RUN_ARTIFACT_DIR = "tests/${BUILD_NUMBER}"
    def SFDC_USERNAME

    def HUB_ORG = 'jenkins.users@calmcoders.net'
    def SFDC_HOST = 'https://login.salesforce.com'
    def JWT_KEY_CRED_ID = 'da0cc199-1f65-476f-943d-56d1739a9b9e'
    def CONNECTED_APP_CONSUMER_KEY = '3MVG97srI77Z1g7.i7q8BcJvLjplnAfz9UZfwT..PPAjqEGi5ZsYbc3GiLGSXwQvTXfp8BKbbIvD5hqmlHC.T'

    stage('checkout source') {
        sfdx force:mdapi:deploy -d DeployPackage/src -u "${HUB_ORG}" -w -1
        checkout scm
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

