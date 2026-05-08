cat > Jenkinsfile << 'EOF'
pipeline {
    agent any

    parameters {
        choice(
            name: 'image',
            choices: ['internetbanking:v1','mobilebanking:v1','insurance:v1','loans:v1'],
            description: 'Select which application image to build'
        )
        choice(
            name: 'repo',
            choices: ['priyankadevops6304/internetbanking','priyankadevops6304/mobilebanking','priyankadevops6304/insurance','priyankadevops6304/loans'],
            description: 'Select your DockerHub repo to push'
        )
    }

    environment {
        APP_NAME = "${params.image.split(':')[0]}"
        APP_TAG  = "${params.image.split(':')[1]}"
    }

    stages {

        stage('Clone Repo') {
            steps {
                git branch: 'master', url: 'https://github.com/Priyanka6304/hdfcwebsite.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                    echo "Building: ${repo}:${APP_TAG}"
                    docker build -t ${repo}:${APP_TAG} ./${APP_NAME}
                '''
            }
        }

        stage('Push to DockerHub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-credentials',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker push ${repo}:${APP_TAG}
                        echo "Successfully pushed ${repo}:${APP_TAG}"
                    '''
                }
            }
        }
    }

    post {
        success {
            echo "✅ ${repo}:${APP_TAG} pushed successfully!"
        }
        failure {
            echo "❌ Build failed! Check logs."
        }
    }
}
EOF
