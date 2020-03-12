pipeline {
    agent any
    tools {
/**      Uncomment if want to have specific java versions installed, otherwise maven tool will use jenkins default embedded java 8
 *       you will also need to uncomment java related stuff in java.groovy from dockerize jenkins project and make sure you have these java versions
 *       in your download folder
 */
        jdk 'jdk8'
        maven 'maven3'
    }
    stages {
        stage('install and sonar parallel') {
            steps {
                parallel(
                        install: {
                            sh "mvn -U clean test cobertura:cobertura -Dcobertura.report.format=xml"
                        },
                        sonar: {
                            sh "mvn sonar:sonar -Dsonar.host.url=${env.SONARQUBE_HOST}"
                        }
                )
            }
            post {
                always {
                    junit '**/target/*-reports/TEST-*.xml'
                     /* step([$class: 'CoberturaPublisher', autoUpdateHealth: false, autoUpdateStability: false, coberturaReportFile: 'target/site/cobertura/coverage.xml', failUnhealthy: false, failUnstable: false, maxNumberOfBuilds: 0, onlyStable: false, sourceEncoding: 'ASCII', zoomCoverageChart: false])*/
                }
            }
        }
         stage ('deploy'){
            steps{
                configFileProvider([configFile(fileId: 'our_settings', variable: 'SETTINGS')]) {
                    sh "mvn -s $SETTINGS deploy -DskipTests -Dartifactory_url=${env.ARTIFACTORY_URL}"
                }
            }
        }
    }
}
