//Definieren einer Variablen zum Einbinden  externer Scripte
def gv

pipeline {
    // auf jedem verfügbaren Agenten/Node ausführen
    agent any
    // Definieren von Variablen
    environment {
        NEW_VERSION = '1.3.0'
        // Credentials aus Jenkins nehmen -> benötigt Plugin Credentials Binding Plugin
        SERVER_CREDENTIALS = credential('server-credentials')
    }
    // Zugriff und Einbinden auf build tools wie Maven, Gradle, yarn, Java/Javascript
        tools {
            // Namen stehen unter Jenkins Verwalten -> Global Tool Config
            maven 'Maven3'
        }
    // parameters: zum Einbinden von externen Parameter (z.B. Build with Parameters im Jenkins)
    // Nutzung im Code weiter unten mit params.VERSION
    parameters {
        choice(name: 'VERSION', choices: ['1.1.0', '1.2.0', '1.3.0'], description: '')
        booleanParam(name: 'executeTests', defaultValue: true, description: '')
    }
    stages {
        // Laden der ausgelagerten groovy Scripte
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
                echo "building version $(NEW_VERSION)"
                // Aufruf der Variablen für externe Scripte und Aufruf der dort Definierten Funktion
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
                    echo "Testing Version ${params.VERSION}"
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
                // Wrapper, der ein Objekt entgegennimmt ab min 19
                withCredentials([
                    usernamePassword(credentials: 'server-credential', usernameVariable USER, passwordVariable: PWD)
                ]) {
                    // in diesem Block kann ich den Username und das PW nutzen
                    sh "some script ${USER} ${PWD}"
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
// weiter auf https://www.youtube.com/watch?v=7KCS70sCoK0&t=810s