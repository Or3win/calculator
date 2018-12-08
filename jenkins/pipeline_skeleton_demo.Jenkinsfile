pipeline 
{     
    agent any
    triggers{
        pollSCM('H/15 * * * *')
    }
    parameters {
        string(name:'track', defaultValue: '1', description:'Server track used for pipeline')
    }
    stages 
    {
        stage('CONTINUOUS INTEGRATION') {
            stages{
                stage('CI.Build'){
                    steps{
                        sh "chmod +x gradlew"                    
                        sh "./gradlew clean build"
                    }
                }
                stage('CI.Unit test'){
                    steps{
                        sh "./gradlew test"                        
                    }
                }
                stage('CI.Code coverage'){
                    steps{
                        sh "./gradlew jacocoTestReport"
                        publishHTML (target: [
                            reportDir: 'build/reports/jacoco/test/html',               
                            reportFiles: 'index.html',               
                            reportName: "JaCoCo Report"          
                            ])          
                        sh "./gradlew jacocoTestCoverageVerification"                           
                    }
                }
                stage('CI.SonarQube'){
                    steps{
                        echo "todo"
                    }
                }
                stage('CI.Artifactory'){
                    steps{
                        sh "curl -u'admin':'kvmechelen' -T 'build/libs/calculator-0.0.1-SNAPSHOT.jar' 'http://172.17.0.3:8081/artifactory/example-repo-local/calculator.jar'"                        
                    }
                }
            }
        }
        stage("BUILD") {
            parallel {
               stage('Application Server') {
                    steps {
                        echo "sh 'ansible-playbook playbook_app.yml -i inventory/build'"
                        echo "inventory/build-${params.track}"
                    }
                }
                stage('Database Server') {
                    steps {
                        echo "sh 'ansible-playbook playbook_dbserver.yml -i inventory/build'"
                    }
                }
                stage('Queues') {
                    steps {
                        echo "sh 'ansible-playbook playbook_queues.yml -i inventory/build'"
                    }
                }
            }
        }          
        stage("Quality Gate 1") {
            input {
                message "Should we continue?"
                ok "Yes, we should."
                parameters {
                    string(name: 'PERSON', defaultValue: 'Dear Release Manager', description: 'Should we continue ?')
                }
            }
            steps {
                echo "Hello, ${PERSON}, please go on."
            }
        }        
        stage("TEST") {
            parallel {
               stage('Application Server') {
                    steps {
                        echo "sh 'ansible-playbook playbook_app.yml -i inventory/test'"
                    }
                }
                stage('Database Server') {
                    steps {
                        echo "sh 'ansible-playbook playbook_dbserver.yml -i inventory/test'"
                    }
                }
                stage('Queues') {
                    steps {
                        echo "sh 'ansible-playbook playbook_queues.yml -i inventory/test'"
                    }
                }
            }
        }        
        stage("ACCEPTANCE") {
            parallel {
               stage('Application Server') {
                    steps {
                        echo "sh 'ansible-playbook playbook_app.yml -i inventory/accceptance'"
                    }
                }
                stage('Database Server') {
                    steps {
                        echo "sh 'ansible-playbook playbook_dbserver.yml -i inventory/acceptance'"
                    }
                }
                stage('Queues') {
                    steps {
                        echo "sh 'ansible-playbook playbook_queues.yml -i inventory/acceptance'"
                    }
                }
            }
        }
        stage("PRODUCTION") {
            parallel {
               stage('Application Server') {
                    steps {
                        echo "sh 'ansible-playbook playbook_app.yml -i inventory/production'"
                    }
                }
                stage('Database Server') {
                    steps {
                        echo "sh 'ansible-playbook playbook_dbserver.yml -i inventory/production'"
                    }
                }
                stage('Queues') {
                    steps {
                        echo "sh 'ansible-playbook playbook_queues.yml -i inventory/production'"
                    }
                }
            }
        }
    }
}