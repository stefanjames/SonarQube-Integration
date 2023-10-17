<!DOCTYPE html>
<html>
<head>
</head>
<body>
  <h1>SonarQube Deployment and Integration Guide</h1>
  
  <h2>Step 1: Pull SonarQube Community Edition Docker Image</h2>
  <code>docker pull sonarqube</code>
  
  <h2>Step 2: Create Volumes</h2>
  <p>Creating volumes helps prevent the loss of information when updating or upgrading. Use the following commands:</p>
  <pre>
    <code>$ docker volume create --name sonarqube_data</code>
    <code>$ docker volume create --name sonarqube_logs</code>
    <code>$ docker volume create --name sonarqube_extensions</code>
  </pre>
  <p>Make sure to use volumes and not bind mounts to prevent issues with plugin population.</p>
  
  <h2>Step 3: Run Docker Image</h2>
  <p>Use the following command to run the SonarQube image:</p>
  <pre><code>docker run -d --name sonarqube -p 9000:9000 -v sonarqube_data:/opt/sonarqube/data -v sonarqube_extensions:/opt/sonarqube/extensions -v sonarqube_logs:/opt/sonarqube/logs --restart=always sonarqube:community</code></pre>
  
  <h2>Step 4: Access SonarQube Server</h2>
  <p>Access the SonarQube server using the default credentials (admin/admin). Change the password on initial login. Create additional users and a Token for Jenkins integration.</p>
  
  <h2>Step 5: Jenkins Configuration</h2>
  <ol>
    <li>Install the SonarQube Scanner for Jenkins plugin.</li>
    <li>Create a new credential as secret text in Jenkins.</li>
    <li>Go to "Manage Jenkins" > "Configure System" and add the server URL of SonarQube with a name.</li>
    <li>Go to "Manage Jenkins" > "Global Tool Configuration" and enable SonarQube Scanners.</li>
  </ol>
  
  <h2>Step 6: Update Jenkinsfile</h2>
  <p>Update the Jenkinsfile to use the SonarQube scanner. Below is an example syntax for Maven code:</p>
  <pre><code>
stage('sonar-scanner') {
  environment {
    SCANNER_HOME = tool 'SonarScanner'
  }
  steps {
    withCredentials([string(credentialsId: 'JenkinsToken', variable: 'sonarLogin')]) {
      sh "${SCANNER_HOME}/bin/sonar-scanner  -D sonar.host.url=http://xx.xx.xx.xx:9000 -D sonar.login=${sonarLogin}  -D sonar.projectVersion=${env.BUILD_NUMBER} -D sonar.projectKey={project.key} -D sonar.sources=./src/main/ -D sonar.tests=./src/test/ -D sonar.language=java -D sonar.java.binaries=."
    }
  }
}
  </code></pre>
  
  <h2>Step 7: Access Scan Report</h2>
  <p>After the pipeline is executed, access the scan report in the SonarQube server.</p>
</body>
</html>
