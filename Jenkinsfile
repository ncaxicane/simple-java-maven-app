@Library(['DBBE@aws_master']) _

pipeline {
agent { label 'linux' }
options {
disableConcurrentBuilds()
skipDefaultCheckout true
buildDiscarder(logRotator(
artifactDaysToKeepStr: '0',
artifactNumToKeepStr: '0',
daysToKeepStr: '5',
numToKeepStr: '5')
)
timestamps()
}

triggers {
cron('05 1 * * 5-6')
}

stages {
stage('environment selection') {
steps {
script {
commonUtils = new ie.aib.CommonUtils()
notifcationUtils = new ie.aib.NotificationUtils()
reportUtils = new ie.aib.ReportUtils()
environment = ''
if(env.JOB_NAME.contains("nightly")) environment = "dbbt3nightly"
}
}
}
stage('zap execution') {
steps {
script {
zapexecution()
}
}
}
}
post {
always {
script {
notifyBuild(currentBuild.result)
}
}
}
}

//Function defination

def zapexecution() {
hostname = commonUtils.getAnsibleEnvNameOrNodeHost()[environment]['node1']
def zap_home_directory = "/home/jenkins/.ZAP"
try {
sh """
whoami
cd ${zap_home_directory}/sessions/session_${environment}
cp session_${environment}.zip $zap_home_directory/session/
unzip ${zap_home_directory}/session/session_${environment}.zip -d ${zap_home_directory}/session/session_${environment};/opt/zaproxy/zap.sh -cmd -session '${zap_home_directory}/session/session_${environment}/session/${environment}.session' -quickurl https://${hostname}/ -quickprogress -quickout '${zap_home_directory}/report/report.html'
rm -r ${zap_home_directory}/session/session_${environment}
rm ${zap_home_directory}/session/session_${environment}.zip
ls -lrt
"""
} catch (Exception e) {
println "Zap Execution failed due to $e"
currentBuild.result = 'FAILURE'
throw e
}finally {
reportPath = '/home/jenkins/.ZAP/report'
reportUtils.publishHtml(reportPath, 'report.html','ZAP Execution report')
}
}
def notifyBuild(String buildStatus = 'STARTED') {

// build status of null means successful
buildStatus = buildStatus ?: 'SUCCESSFUL'

notifcationUtils.sendNotificationBE("Zap Execution ${buildStatus} on Environment : [${environment}]: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
"""
Zap Execution ${buildStatus} on Environment : [${environment}]. Check console output at ${env.BUILD_URL} for Job: ${env.JOB_NAME} [${env.BUILD_NUMBER}]

""")
}
