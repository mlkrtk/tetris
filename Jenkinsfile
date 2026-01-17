@Library('sharedlibrary')_

pipeline {
    environment {
        gitRepoURL = "${GIT_URL}"
        gitBranchName = env.GIT_BRANCH.replaceFirst(/^origin\//, '')
        repoName = "tetris"
        dockerImage = "891543987898.dkr.ecr.ap-south-1.amazonaws.com/${repoName}"
        gitCommit = "${GIT_COMMIT[0..6]}"
        dockerTag = "${gitBranchName}-${gitCommit}"
        snykOrg = "c7ea86d1-d613-4212-80de-9b38354f5ced"
    }
     
    agent {label 'docker'}
    stages {

        stage ('synk code scan'){
            steps{
                snykCodeScan('$dockerImage', '$dockerTag', 'snykCred', 'snykOrg')
            }
        }

        stage('Docker Build') {
            steps {
                dockerImageBuild('$dockerImage', '$dockerTag')
            }
        }

        stage ('synk image scan'){
            steps {                
                snykCodeScan('$dockerImage', '$dockerTag', 'snykCred', 'snykOrg')
            }
        }

        stage('Docker Push') {
            steps {
                dockerECRImagePush('$dockerImage', '$dockerTag', '$repoName', 'awsCred', 'ap-south-1')
            }
        }

        stage('Update manifests') {
            steps {
                updateManifest('https://github.com/mlkrtk/argocd-manifests.git', 'develop', 'githubCred', 'develop/tetris/overlays/dev', '$dockerImage', '$dockerTag', '', '', '', '')
            }
        }
    }    
}
