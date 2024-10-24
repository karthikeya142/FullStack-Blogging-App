pipeline {
    agent any
    tools{
        jdk 'jdk17'
        maven 'maven'
    }
    environment{
        SCANNER_HOME = tool 'sonar-scanner'
    }

    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/karthikeya142/FullStack-Blogging-App.git'
            }
        }
         stage('compile') {
            steps {
               sh 'mvn compile'
            }
        }
         stage('test') {
            steps {
                sh 'mvn test'
            }
        }
         stage('Trivy FS scan') {
            steps {
                sh 'trivy fs --format table -o fs.html .'
            }
        }
         stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=blogging \
                            -Dsonar.projectKey=blogging \
                            -Dsonar.java.binaries=target
                    '''
                }
            }
        }
         stage('Build') {
            steps {
                sh 'mvn package'
            }
        }
        stage('Publish artifacts') {
            steps {
                withMaven(globalMavenSettingsConfig: 'maven-settings', jdk: 'jdk17', maven: 'maven', mavenSettingsConfig: '', traceability: true) {
                    sh 'mvn deploy'
                }
            }
        }
        stage('docker  Build&tag') {
            steps {
                script{
                        // This step should not normally be used in your script. Consult the inline help for details.
                    withDockerRegistry(credentialsId: 'karthik142', toolName: 'docker ') {
                        sh 'docker build -t karthik142/bloging:latest .'
                    }
                }
            }
        }
        stage('Trivy image scan') {
            steps {
                sh 'trivy image --format table -o image.html karthik142/bloging:latest'
            }
        }
         stage('docker Push') {
            steps {
                script{
                        // This step should not normally be used in your script. Consult the inline help for details.
                    withDockerRegistry(credentialsId: 'karthik142', toolName: 'docker ') {
                        sh 'docker push karthik142/bloging:latest'
                    }
                }
            }
        }
    }
}
