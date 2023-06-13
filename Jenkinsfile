properties([pipelineTriggers([pollSCM('* * * * *')])])
node {
    stage('CheckOut') { // for display purposes
        // Get some code from a GitHub repository
        git 'https://github.com/nagarajud031/nodejs-sample-app.git'
        
    }
    stage('Get Latest Commit') {
        git branch: 'master', url: 'https://github.com/nagarajud031/nodejs-sample-app.git'
        def commitSHA = sh(returnStdout: true, script: 'git rev-parse --short HEAD').trim()
        env.COMMIT_SHA = commitSHA
        echo "First 7 characters of the Latest Commit SHA: ${env.COMMIT_SHA}"
    }
    stage('Build') {
        // Run the npm install
        sh 'npm install'
    }
    stage('Docker Build') {
        IMAGE_NAME="chinnagoud031/devops"
        //def commitSHA = sh(returnStdout: true, script: 'git rev-parse --short HEAD').trim()
        sh 'sudo docker build -t chinnagoud031/devops .'
    }
    stage('Push Docker Image') {
        // Login to Docker Hub
        withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'DOCKER_HUB_PASSWORD', usernameVariable: 'DOCKER_HUB_USERNAME')]) {
            sh "sudo docker login -u ${env.DOCKER_HUB_USERNAME} -p ${env.DOCKER_HUB_PASSWORD}"
        }
    }
    stage('Docker Tag & Push') {
        script {
            def commitSHA = sh(returnStdout: true, script: 'git rev-parse --short HEAD').trim()
            sh "sudo docker tag chinnagoud031/devops:latest chinnagoud031/devops:$commitSHA"
            sh "sudo docker push chinnagoud031/devops:$commitSHA"
        }
    }
    stage('Remove Running Container') {
        sh 'sudo docker rm -f $(sudo docker ps -aq)'
    }
    stage('Docker Deploy') {
        def commitSHA = sh(returnStdout: true, script: 'git rev-parse --short HEAD').trim()
        sh "sudo docker run -dit -p 5000:5000 --name nodecontainer chinnagoud031/devops:$commitSHA"
    }
}
