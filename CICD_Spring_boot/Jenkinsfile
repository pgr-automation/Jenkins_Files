pipeline{
     environment {
        IMG_BUILD = "192.168.1.130:5000/springbootapp:${BUILD_VERSION}"
        IMG_DELETE = "9902736822/springbootapp:${DELETE_VERSION}"

        SONAR_URL = "http://192.168.1.130:9000" 
        DOCKER_REGISTRY = "192.168.1.130:5000"
        NEXUS_REPO_SNAPSHOT = "http://192.168.1.130:8081/repository/pgr-maven-snapshot/"
        PROJECT_KEY = "CICD-Spring-Boot"
        BUILD_VER = "${BUILD_VER}"

    }
    agent any

stages{
    stage('Git check Out'){
        steps{
            script{
                git branch: 'main', credentialsId: '28059df9-d0e4-49f6-9da6-e410f9470aff', url: 'https://github.com/pgr-automation/CICD_spring_boot.git'
            }
        }
    }

    stage('Build & Test'){
        steps{
            sh '''
            cd spring-bootapp/
            export MAVEN_OPTS="--add-opens java.base/java.lang=ALL-UNNAMED"
            mvn clean package -DskipTests=false

            '''
        }
    }

    stage('SonarQube analysis'){
        steps{
            withVault([vaultSecrets: [[path: 'secret/data/sonarcred', secretValues: [
                    [envVar: 'SONAR_USER', vaultKey: 'SONAR_USER'],
                    [envVar: 'SONAR_PASSWORD', vaultKey: 'SONAR_PASS'],
                    [envVar: 'SONAR_TOKEN', vaultKey: 'SONAR_TOKEN']
                ]]]]) {
                    withSonarQubeEnv('SonarQube'){
                        sh '''
                             cd spring-bootapp/
                            mvn sonar:sonar -Dsonar.projectKey=$PROJECT_KEY -Dsonar.projectName=CICD-Spring-Boot -Dsonar.token=$SONAR_TOKEN -Dsonar.host.url=$SONAR_URL          
                        '''

                    }
                        
                    }
        }
    }
   stage('SonarQube Quality Gate'){
    steps{
        script{
            timeout(time: 3, unit: 'MINUTES'){
                 def qualityGate = waitForQualityGate()
                if (qualityGate.status != 'OK'){
                    error "SonarQube Quality Gate failed for project ${PROJECT_KEY}: ${qualityGate.status}"
                }
                else {
                    echo "SonarQube Quality Gate passed for project ${PROJECT_KEY}: ${qualityGate.status}"
                }
            }
               
    }
   }
    }
    stage('Upload Artifacts - Nexus'){
        steps{
            withVault([vaultSecrets: [[path: 'secret/data/nexuscred', secretValues: [
                    [envVar: 'NEXUS_USER', vaultKey: 'NEXUS_USER'],
                    [envVar: 'NEXUS_PASSWORD', vaultKey: 'NEXUS_PASS']
                ]]]]) {
                    script {
                        echo "Deploying artifacts to Nexus..."                    
                    
                        sh """ 
                            cd spring-bootapp/
                            mvn deploy -DaltDeploymentRepository=pgr-nexus-repo-snapshot::default::${NEXUS_REPO_SNAPSHOT} \
                        """
                         echo "Artifacts uploaded successfully to Nexus."

                    }

                    }
        }
    }
    stage('Docker Build'){
        steps{
            script{
                echo "Building Docker image..."
                sh """
                cd spring-bootapp
                docker build -t ${IMG_BUILD}  .
                """
            }
        }
    }
    stage('Docker Image Push to Registry'){
         steps{
            withVault([vaultSecrets: [[path: 'secret/data/dockerregcred', secretValues: [
                    [envVar: 'REG_USER', vaultKey: 'REG_USER'],
                    [envVar: 'REG_PASSWORD', vaultKey: 'REG_PASS']
                ]]]]) {
                    script {
                        sh """
                            echo '$REG_PASSWORD' |docker login -u $REG_USER --password-stdin $DOCKER_REGISTRY
                            docker tag  ${IMG_BUILD} ${IMG_BUILD}
                            docker push ${IMG_BUILD}
                            docker logout 
                        """
                        

                    }

                    }
        }
    }


}
}