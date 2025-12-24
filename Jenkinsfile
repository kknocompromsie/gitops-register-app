pipeline {
    agent { label "Jenkins-Agent" }
    environment {
        APP_NAME = "register-app-pipeline"
    }

    stages {
        stage("Cleanup Workspace") {
            steps {
                cleanWs()
            }
        }

        stage("Checkout from SCM") {
            steps {
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/kknocompromsie/gitops-register-app'
            }
        }

        stage("Update the Deployment Tags") {
            steps {
                sh """
                    cat deployment.yaml
                    sed -i 's/${APP_NAME}.*/${APP_NAME}:${IMAGE_TAG}/g' deployment.yaml
                    cat deployment.yaml
                """
            }
        }

        stage("Push the changed deployment file to Git") {
            steps {
                sh """
                    git config --global user.name "kknocompromsie"
                    git config --global user.email "kondalarao3411@gmail.com"
                    git add deployment.yaml
                    # This check prevents the pipeline from failing if there are no changes to commit
                    if git diff --staged --quiet; then
                        echo "No changes to commit"
                    else
                        git commit -m "Updated Deployment Manifest"
                    fi
                """
                withCredentials([gitUsernamePassword(credentialsId: 'github', gitToolName: 'Default')]) {
                    sh "git push https://github.com/kknocompromsie/gitops-register-app main"
                }
            }
        }
    }
}
