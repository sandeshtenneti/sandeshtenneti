- 👋 Hi, I’m @sandeshtenneti
- 👀 I’m interested in ...
- 🌱 I’m currently learning ...
- 💞️ I’m looking to collaborate on ...
- 📫 How to reach me ...

https://aakibkhan1.medium.com/deployment-of-youtube-clone-on-kubernetes-cluster-with-slack-integration-bfe621d89b42
https://medium.com/@vinoji2005/day-26-integrating-ansible-with-ci-cd-pipelines-284637f83ba2

How to Containerize and Deploy a Three-Tier Application on AWS EKS with Kubernetes
https://blog.stackademic.com/how-to-containerize-and-deploy-a-three-tier-application-on-aws-eks-with-kubernetes-327c104f9c9f

Where I explain how to use maven, docker , trivy, sonarqube, owasp etc.
pipeline {
    agent any
    tools {
        maven 'maven3'
        jdk 'jdk17'
    }
    environment {
        SCANNER_HOME= tool 'sonar-scanner'
    }
    stages {
        stage('git checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/thakurrajesh22293/Ekart.git'
                echo 'code checkout is successful'
            }
        }
        stage('compile') {
            steps {
                sh "mvn compile"
            }
        }
        stage('unit test') {
            steps {
                sh "mvn test -DskipTests=true"
            }
        }
        stage('sonarqube analyses') {
            steps {
                withSonarQubeEnv('sonar-scanner') {
                    sh "$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectKey=Ekart -Dsonar.projectName=Ekart -Dsonar.java.binaries=."
                }
            }
        }
        stage('OWASP DEPENDENCY CHECK') {
            steps {
                script {
                    def scanResult = dependencyCheck additionalArguments: '--scan ./', odcInstallation: 'DC'
                    dependencyCheckPublisher pattern: '**/dependency-check-report.xml', dependencyCheckReport: scanResult
                }
            }
        }
        stage('BUILD') {
            steps {
                sh "mvn package -DskipTests=true"
            }
        }
        stage('Deploy to Nexus') {
            steps {
                script {
                    withMaven(globalMavenSettingsConfig: 'nexus', jdk: 'jdk17', maven: 'maven3', mavenSettingsConfig: '', traceability: true) {
                        sh "mvn deploy -DskipTests=true"
                    }
                }
            }
        }
    }
}
<!---
sandeshtenneti/sandeshtenneti is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->
