pipeline {
    agent {
        node{
            label 'maven'
        }
    }

    stages {
        stage("biuld"){
            steps {
                sh 'mvn clean deploy'
            }
        }

     
    }
}