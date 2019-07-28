pipeline {
    agent none
    triggers { pollSCM('H/02 * * * *') }
    
    ///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
        
    environment {
        sonarUser = "admin"
        sonarPW = "Aa123456"
        sonarProjKey = "ProjKey"
        sonarProjName = "ProjName"
        buildAgent = "{{buildAgent}}"
        gitURL = "https://bitbucket.elm.sa/scm/was/waseet.git"
        gitBranch = "{{gitBranch}}"
        devEnv = "{{devEnv}}"
        }
    
    ///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
        
    stages {

        stage('Clean working directory on buildAgent') {
            agent {
                            label "${buildAgent}"
                    }
            steps {
                    deleteDir()
                }
        }
        
        ///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
        
        stage('Compile, Build && running SonarQube analysis') {
            agent   {
                        label "${buildAgent}"
                    }
            steps { 
                withSonarQubeEnv('SonarQube7.6') {

                ///////////////////////////////////////////////////////////////////////////////////////////////
                
                git branch: '${gitBranch}', url: '${gitURL}'

                ///////////////////////////////////////////////////////////////////////////////////////////////
                
                
                // Due to SONARMSBRU-307 value of sonar.host.url and credentials should be passed on command line
                
                dir ('path\\path') {  // Relative PATH to root directory and should include \\ between each folder as a jenkins syntax    
                bat "dotnet-sonarscanner begin /k:${sonarProjKey} /n:${sonarProjName} /v:${BUILD_NUMBER} /d:sonar.host.url=%SONAR_HOST_URL% /d:sonar.login=${sonarUser} /d:sonar.password=${sonarPW}"
                bat "dotnet publish -c Release  --source http://nexus.elm.sa:8081/repository/nuget-group/"
                bat "dotnet-sonarscanner end /d:sonar.login=${sonarUser} /d:sonar.password=${sonarPW}"
                    } 
                }

                post {  
			        // always { echo 'This will always run' }  
			        success { echo 'This will run only if successful' }  
			        failure {  
			                mail bcc: '', body: '''Dears,
			                ${sonarProjName} build has been failed''', cc: 'abdelmawgood@elm.sa', from: 'jenkins@elm.sa', mimeType: 'text/html', replyTo: '', subject: '${sonarProjName} build status', to: 'abdelmawgood@elm.sa' }  
			        }   
	    	}
        }
 
        ///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
        
        stage("SonarQube Quality Gate") {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    // Parameter indicates whether to set pipeline to UNSTABLE if Quality Gate fails
                    // true = set pipeline to UNSTABLE, false = don't
                    // Requires SonarQube Scanner for Jenkins 2.7+
                   waitForQualityGate abortPipeline: true
                }

                post {  
			        // always { echo 'This will always run' }  
			        success { echo 'This will run only if successful' }  
			        failure {  
			                mail bcc: '', body: '''Dears,
			                ${sonarProjName} SonarQuality Gate has been failed''', cc: 'abdelmawgood@elm.sa', from: 'jenkins@elm.sa', mimeType: 'text/html', replyTo: '', subject: '${sonarProjName} build status', to: 'abdelmawgood@elm.sa' }  
			        } 
            }
        }

        ///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
        
        stage('Auto Deploy DB Project in CI Env') {
	        agent {
                        label "${devEnv}"
                }
                steps {
                echo 'Deploying DB changes to CI enviornment'
                git branch: '${gitBranch}', url: '${gitURL}'
                bat 'dotnet restore Project.sln -s http://nexus.elm.sa:8081/repository/nuget-group/'
                dir ('path\\path')
                    {
                    bat ('dotnet ef database update')
                    }
                }
				post {  
			        success { echo 'This will run only if successful' }  
			        failure {  
			                mail bcc: '', body: '''Dears,
			                ${sonarProjName} DB deployment in CI environment has been failed''', cc: 'abdelmawgood@elm.sa', from: 'jenkins@elm.sa', mimeType: 'text/html', replyTo: '', subject: 'ElmX OPC DB deployment in CI environment', to: 'abdelmawgood@elm.sa' }  
			        }
        }

        ///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
        
        stage('Auto Deploy Application in CI Env') {
            agent {
                        label "${devEnv}"
                 }
                steps {
                    deleteDir()
                    
                    /////////////////////////////////////////////// Deploying Application  /////////////////////////////////////////////////////////////////////////////////////////////////////
                    
                    copyArtifacts filter: '{{packageName}}', fingerprintArtifacts: true, projectName: '${JOB_NAME}' , selector: specific('${BUILD_NUMBER}')
                    bat '7z X -y {{packageName}} -xr!appsettings.json -oC:\\inetpub\\wwwroot\\OPC\\Payment'
                    
                    /////////////////////////////////////////////// IIS Reset /////////////////////////////////////////////////////////////////////////////////////////////////////
                    
                    bat 'iisreset'
                }
                post {  
			        success { echo 'This will run only if successful' }  
			        failure {  
			                mail bcc: '', body: '''Dears,
			               ${sonarProjName} deployment in CI environment has been failed''', cc: 'abdelmawgood@elm.sa', from: 'jenkins@elm.sa', mimeType: 'text/html', replyTo: '', subject: 'ElmX OPC deployment in CI environment', to: 'abdelmawgood@elm.sa,mmostafa@elm.sa' }  
			        }
        }
        

        ///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
       
        stage('Auto Deploy DPP Mock in CI Env') {
	        agent {
                        label "${devEnv}"
                }
                steps {
                echo 'Download and deploy mock on CI env'
                bat 'curl -X GET "http://nexus.elm.sa:8081/repository/libs-release-local/com/elm/qa/OPC/opc-test/1.0.0/opc-test-1.0.0.war" -o "C:\\OPCtomcat\\webapps\\opc-test-1.0.0.war"'
                bat 'C:\\OPCtomcat\\bin\\shutdown.bat'
                bat 'C:\\OPCtomcat\\bin\\startup.bat'
                }
				post {  
			        success { echo 'This will run only if successful' }  
			        failure {  
			                mail bcc: '', body: '''Dears,
			                ${sonarProjName} 3rd Party Mock deployment has been failed''', cc: 'abdelmawgood@elm.sa', from: 'jenkins@elm.sa', mimeType: 'text/html', replyTo: '', subject: 'ElmX Payment API build', to: 'abdelmawgood@elm.sa' }  
			        }
        }
    
        ///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
       
    	stage('Build && Run Functional Test in CI Env') {
	        agent {
                        label "${devEnv}"
                }
                steps {
                git branch: 'gitBranch', url: 'gitURL'
                bat 'mvn clean package -DskipTests'
                bat 'java -jar xxxxx.jar'
                
                }
                post {
                    always { step([$class: 'Publisher', failedFails:'100' , failureOnFailedTestConfig: 'true' , reportFilenamePattern: '**/testng-results.xml']) }
					success { echo 'This will run only if successful' }  
					failure {  
						mail bcc: '', body: '''Dears,
			            ${sonarProjName} Functional Test in CI environment has been failed''', cc: 'abdelmawgood@elm.sa', from: 'jenkins@elm.sa', mimeType: 'text/html', replyTo: '', subject: 'ElmX OPC Functional Test in CI enviornment', to: 'abdelmawgood@elm.sa'
						}  
                }
            }

        ///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
       

    }
}