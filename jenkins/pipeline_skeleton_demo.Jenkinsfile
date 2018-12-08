pipeline 
{     
    agent any
    parameters {
        string(name:'track', defaultValue: '1', description:'Server track used for pipeline')
    }
    stages 
    {
        stage('CONTINUOUS INTEGRATION') {
            steps {
                echo '1.1.Request server/track'
                echo '1.2.Branching'
                echo '1.3.Jenkins Jobs'
                
                echo '2.1.Write code'
                echo '2.2.Write db scripts'
                echo '2.3.Unit test local'
                echo '2.4.Deploy local'
                echo '2.5.Sonar Check local'

                echo '3.1.Poll SCM'
                git url: 'https://github.com/or3win/calculator.git'
                
                echo '3.2.Build jenkins'
                sh "chmod +x gradlew"                    
                sh "./gradlew clean build"

                echo '3.4.Wait for Unit test env'

                echo '3.5.Unit test'
                sh "./gradlew test"

                echo '3.3.Sonar-Codecoverage'
                sh "./gradlew jacocoTestReport"
                publishHTML (target: [
                    reportDir: 'build/reports/jacoco/test/html',               
                    reportFiles: 'index.html',               
                    reportName: "JaCoCo Report"          
                    ])          
                sh "./gradlew jacocoTestCoverageVerification"   
                        
                echo '01.3.6.Artifactory'
                sh "curl -u'admin':'kvmechelen' -T 'build/libs/calculator-0.0.1-SNAPSHOT.jar' 'http://172.17.0.3:8081/artifactory/example-repo-local/calculator.jar'"
                echo '01.3.7.Data Dictionary'
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