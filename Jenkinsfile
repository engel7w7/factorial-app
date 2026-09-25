// Practica 6 - Pipeline declarativo de factorial-app
// Jenkins lo lee desde la raiz del repositorio (Pipeline script from SCM).
pipeline {
    agent any

    tools {
        // Debe coincidir EXACTAMENTE con el nombre dado en
        // Manage Jenkins -> Tools -> Maven installations
        maven 'Maven-3.9'
    }

    options {
        timestamps()
        buildDiscarder(logRotator(numToKeepStr: '10'))
        timeout(time: 15, unit: 'MINUTES')
    }

    triggers {
        // Jenkins revisa el repositorio cada ~2 minutos y construye si hay commits nuevos
        pollSCM('H/2 * * * *')
    }

    stages {
        stage('Compilar') {
            steps {
                sh 'mvn -B clean compile'
            }
        }
        stage('Pruebas') {
            steps {
                sh 'mvn -B test'
            }
            post {
                always {
                    // Publica el informe de JUnit aunque haya pruebas fallidas
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
        stage('Empaquetar') {
            steps {
                sh 'mvn -B -DskipTests package'
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }
    }

    post {
        success {
            echo 'Build y pruebas exitosas'
        }
        unstable {
            echo 'Hay pruebas que fallan: el build queda en amarillo'
        }
        failure {
            echo 'Fallo el proceso: revisa la etapa en rojo'
        }
    }
}
