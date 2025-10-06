pipeline
{
    agent any
    environment
    {
        // Docker Hub credentials ID stored in Jenkins
        DOCKERHUB_CREDENTIALS ='cybr-3120'
        IMAGE_NAME ='litattj/atticusgametest123'
    }
    stages
    {
        stage('Cloning Git')
        {
            steps
            {
                checkout scm
            }
        }
        stage('SAST')
        {
            steps
            {
                sh 'echo Running SAST scan with synk...'
            }
        }
        stage('BUILD-AND-TAG')
        {
            agent { label 'CYBR-3120-app-server2'}

            steps
            {
                script
                {
                    echo "Building Docker image ${IMAGE_NAME}..."
                    app = docker.Build("${IMAGE_NAME}")
                    app.tag("latest")
                }
            }
            stage('POST-TO-DOCKERHUB')
            {
                steps
                {
                    script
                    {
                        echo "Pushing image ${IMAGE_NAME}:latest to Docker Hub..."
                        docker.withRegistry('https://registry.hub.docker.com', "${DOCKERHUB_CREDENTIALS}")
                        {
                            app.push("latest")
                        }
                    }
                }
            }
            stage('DAST')
            {
                steps{
                    sh 'echo Running DAST scan...'
                }
            }
            stage('DEPLOYMENT')
            {
                agent { label 'CYBR-3120-app-server2'}

                steps
                echo 'Starting deployment using docker-compose...'
                {
                    script
                    {
                        dir("${WORKSPACE}")
                        {
                            sh '''
                                docker-compose down
                                docker-compose up -d
                                docker ps
                            ...
                        }
                    }
                }
            }
        }
    }
}
