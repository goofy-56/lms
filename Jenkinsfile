pipeline {
    agent any
    stages {
        stage('Code Analysis') {
            steps {
                echo 'CODE QUALITY CHECK'
                // Below command works in jenkins 
                sh 'cd webapp && sudo docker run --rm -e SONAR_HOST_URL="http://13.233.206.79:9000" -v ".:/usr/src" -e SONAR_TOKEN="sqp_19b8da1cef12a5dafe98448695da586bfce3e178" sonarsource/sonar-scanner-cli -Dsonar.projectKey=lms'
                echo 'CODE QUALITY COMPLETED'
            }
        }
        stage('Build Artifacts') {
            steps {
                echo 'Build LMS'
                sh 'cd webapp && npm install && npm run build'
                echo 'Build Completed'
            }
        }
        stage('Release Artifacts') {
            steps {
               script {
                   def packageJson = readJSON file: 'webapp/package.json'
                   def packageJSONVersion = packageJson.version
                   echo "${packageJSONVersion}"
                   sh "zip webapp/lms-${packageJSONVersion}.zip -r webapp/dist"
                   sh "curl -v -u admin:nexus@123456 --upload-file webapp/lms-${packageJSONVersion}.zip http://13.233.206.79:8081/repository/lms/"
               }
           }

        }
        stage('Deploy') {
            steps {
               script {
                   def packageJson = readJSON file: 'webapp/package.json'
                   def packageJSONVersion = packageJson.version
                   echo "${packageJSONVersion}"
                   sh "curl -u admin:nexus@123456 -X GET \'http://13.233.206.79:8081/repository/lms/lms-${packageJSONVersion}.zip\' --output lms-'${packageJSONVersion}'.zip"
                   sh 'sudo rm -rf /var/www/html/*'
                   sh "sudo unzip -o lms-'${packageJSONVersion}'.zip"
                   sh "sudo cp -r webapp/dist/* /var/www/html"
               }
           }

        }
        stage('Cleanup') {
            steps {
                cleanWs()
            }
        }
    }
}
#almas61