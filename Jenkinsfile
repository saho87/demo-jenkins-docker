def gv


pipeline {
    // auf jedem verfügbaren Agenten/Node ausführen
    agent any
    parameters {
        choice(name: 'VERSION', choices: ['1.1.0', '1.2.0', '1.3.0'], description: '')
        booleanParam(name: 'executeTests', defaultValue: true, description: '')
    }
    stages {
        stage("init") {
            steps {
                script {
                   gv = load "script.groovy" 
                }
            }
        }
        stage("build") {
            when {
                expression {
                    // nur wenn Dev-Branch und Änderungen im Code -> CODE_CHANGES muss erst definiert werden
                    BRANCH_NAME == "dev" && CODE_CHANGES == true
                }
            }
            steps {
                script {
                    gv.buildApp()
                }
            }
        }
        stage("test") {
            // when -> wann soll Stage ausgeführt werden
            when {
                expression {
                    // BRANCH_NAME ==  'dev' || BRANCH_NAME == 'master'
                    params.executeTests
                }
            }
            steps {
                script {
                    gv.testApp()
                }
            }
        }
        stage("deploy") {
            steps {
                script {
                    gv.deployApp()
                }
            }
        }

    }
    post {
        always {
            // wird immer nach den Stages ausgeführt
        }
        failure {
            // beim Fail
        }
        success {
            // bei Erfolg
        }
    }
}
// Zugriff auf verfügbare Variablenname über //URL:Jenkis/env-vars.html