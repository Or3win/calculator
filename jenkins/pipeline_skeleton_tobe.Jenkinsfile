pipeline 
{     
    agent any
    stages 
    {
        stage("PLAN")  {
            steps {
                echo 'Check other syntax'
                    }
        }
        stage('CONTINUOUS INTEGRATION') {
            stages {
                stage("CI.Setup en CI.Coding")   {
                steps {
                    echo '1.1.Request server/track'
                    echo '1.2.Branching'
                    echo '1.3.Jenkins Jobs'
                    
                    echo '2.1.Write code'
                    echo '2.2.Write db scripts'
                    echo '2.3.Unit test local'
                    echo '2.4.Deploy local'
                    echo '2.5.Sonar Check local'

                    }
                }
                stage("CI.Build")   {
                    steps {
                        //temporary Poll SCM step within scripted pipeline until Ortwin has access to a SVN client
                        echo '01.3.1.Poll SCM'
                        /* credentials for ci jenkins P&V
                        checkout([$class: 'SubversionSCM', additionalCredentials: [], excludedCommitMessages: '', excludedRegions: '', 
                            excludedRevprop: '', excludedUsers: '', filterChangelog: false, ignoreDirPropChanges: false, includedRegions: '', 
                            locations: [[cancelProcessOnExternalsFail: true, credentialsId: 'svn_password', depthOption: 'infinity', 
                            ignoreExternalsOption: true, local: '.', remote: 'http://svnrepos.vivium.intranet/repos/java/build-tool/jenkins-dsl-scripts/branches/DEV_JenkinsUpgrade']], 
                            quietOperation: true, workspaceUpdater: [$class: 'UpdateUpdater']])
                        */
                        echo '01.3.2.Build jenkins'
                git url: 'https://github.com/or3win/calculator.git'
                sh "chmod +x gradlew"                    
                sh "./gradlew compileJava"
                
                echo '01.3.2.Build jenkins'
                /* PeeEnVee
                sh './gradlew build'
                */
                echo '01.3.3.Sonar'
                echo '01.3.4.Wait for Unit test env'
                echo '01.3.5.Unit test'
                sh "./gradlew test"
                 sh "./gradlew jacocoTestReport"
                publishHTML (target: [
                    reportDir: 'build/reports/jacoco/test/html',               
                    reportFiles: 'index.html',               
                    reportName: "JaCoCo Report"          
                ])          
                sh "./gradlew jacocoTestCoverageVerification"   
                        
                        echo '01.3.6.Artifactory'
                        echo '01.3.7.Data Dictionary'
                        }
                    }
                }
        }
        stage("BUILD") {
            parallel {
               stage('Application Server') {
                    steps {
                        echo "sh 'ansible-playbook playbook_app.yml -i inventory/build'"
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
                echo "Hello, ${PERSON}, let's go on."
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