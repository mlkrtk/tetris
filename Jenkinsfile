@Library('sharedlibrary')_

pipeline {
    environment {
        gitRepoURL = "${GIT_URL}"
        gitBranchName = env.GIT_BRANCH.replaceFirst(/^origin\//, '')
        repoName = "tetris"
        dockerImage = "891543987898.dkr.ecr.ap-south-1.amazonaws.com/${repoName}"
        gitCommit = "${GIT_COMMIT[0..6]}"
        dockerTag = "${gitBranchName}-${gitCommit}"
    }
     

    agent {label 'docker'}
    stages {
        stage('Docker Build') {
            steps {
                    dockerImageBuild('$dockerImage', '$dockerTag')
            }
        }

        stage('Docker Push') {
            steps {
                dockerECRImagePush('$dockerImage', '$dockerTag', '$repoName', 'awsCred', 'ap-south-1')
            }
        }

        stage('Kubernetes Deploy - DEV') {
            steps {
                kubernetesEKSHelmDeploy('$dockerImage', '$dockerTag', '$repoName', 'awsCred', 'ap-south-1', 'pe', 'dev')
            }
        }

        stage('Kubernetes Deploy - UAT') {        
            when {
                branch 'master'
            }
            steps {
                kubernetesEKSHelmDeploy('$dockerImage', '$dockerTag', '$repoName', 'awsCred', 'ap-south-1', 'pe', 'uat')
            }
        }

    }
}
