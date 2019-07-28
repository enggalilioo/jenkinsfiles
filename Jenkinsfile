pipeline {
    agent none
    triggers { pollSCM('H/02 * * * *') }
    environment {
        sonarUser = "admin"
        sonarPW = "Aa123456"
        sonarProjKey = "waseet"
        sonarProjName = "Waseet_API"
        sonarVer = "1.0"
    }
    
        stages {
        //     stage('Clean working directory on build agent') {
        //         agent {
        //                     label "buildagent-03"
        //             }
        //         steps {
        //             deleteDir()
        //         }
         // }
        
	    // stage('clone ELMx Payment API source code') {
	    //     agent {
        //                 label "buildagent-03"
        //         }
        //         steps {
        //         // Git clone from bitbucket.org 
        //         echo 'This will clone ELMx Payment source code on master node'
        //         git branch: 'jenkins-pipeline', url: 'https://bitbucket.elm.sa/scm/was/waseet.git'
        //         }
        // }

        stage('Build && running SonarQube analysis') {
            agent {
                        label "buildagent-03"
                }
                steps { 
                    withSonarQubeEnv('SonarQube7.6') {
                deleteDir()
                git branch: 'dev', url: 'https://bitbucket.elm.sa/scm/was/waseet.git'
                // Due to SONARMSBRU-307 value of sonar.host.url and credentials should be passed on command line
                dir { ('Waseet\\Waseet.API')
                bat "dotnet-sonarscanner begin /k:${sonarProjKey} /n:${sonarProjName} /v:${sonarVer} /d:sonar.host.url=%SONAR_HOST_URL% /d:sonar.login=${sonarUser} /d:sonar.password=${sonarPW}"
                dotnet publish -c Release  --source http://nexus.elm.sa:8081/repository/nuget-group/
                bat "dotnet-sonarscanner end /d:sonar.login=${sonarUser} /d:sonar.password=${sonarPW}"
                    } 
                    }

                post {  
			        always { echo 'This will always run' }  
			        success { echo 'This will run only if successful' }  
			        failure {  
			                mail bcc: '', body: '''Dears,
			                ElmX OPC build has been failed''', cc: 'malkamary@elm.sa', from: 'jenkins@elm.sa', mimeType: 'text/html', replyTo: '', subject: 'ElmX Online Payment Channel build', to: 'abdelmawgood@elm.sa,mmostafa@elm.sa' }  
			        }   
	    	}
        }
 
        stage("SonarQube Quality Gate") {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    // Parameter indicates whether to set pipeline to UNSTABLE if Quality Gate fails
                    // true = set pipeline to UNSTABLE, false = don't
                    // Requires SonarQube Scanner for Jenkins 2.7+
                   waitForQualityGate abortPipeline: true
                }
            }
        }

        // stage('Auto Deploy OPC DataBase in CI Env') {
	    //     agent {
        //                 label "buildagent-01"
        //         }
        //         steps {
        //         echo 'Deploying DB changes to CI enviornment'
        //         git branch: 'jenkins-pipeline', url: 'https://bitbucket.elm.sa/scm/elmx/elmx_payment_gateway.git'
        //         bat 'dotnet restore ELMX_OPC.sln -s http://nexus.elm.sa:8081/repository/nuget-group/'
        //         dir ('Infrastructure\\Database\\ELMX.OPC.Database.Migrator')
        //             {
        //             bat ('dotnet ef database update')
        //             }
        //         }
		// 		post {  
		// 	        always { echo 'This will always run' }  
		// 	        success { echo 'This will run only if successful' }  
		// 	        failure {  
		// 	                mail bcc: '', body: '''Dears,
		// 	                ElmX OPC DB deployment in CI environment has been failed''', cc: 'malkamary@elm.sa', from: 'jenkins@elm.sa', mimeType: 'text/html', replyTo: '', subject: 'ElmX OPC DB deployment in CI environment', to: 'abdelmawgood@elm.sa,mmostafa@elm.sa' }  
		// 	        }
        // }

        // stage('Auto Deploy OPC in CI Env') {
        //     agent {
        //                 label "buildagent-01"
        //          }
        //         steps {
        //             deleteDir()
                    
        //             /////////////////////////////////////////////// Deploying Payment API  /////////////////////////////////////////////////////////////////////////////////////////////////////
                    
        //             copyArtifacts filter: 'OPC_Payment_API.zip', fingerprintArtifacts: true, projectName: '${JOB_NAME}' , selector: specific('${BUILD_NUMBER}')
        //             bat '7z X -y OPC_Payment_API.zip -xr!appsettings.json -oC:\\inetpub\\wwwroot\\OPC\\Payment'
                    
        //             /////////////////////////////////////////////// Deploying Receivable API /////////////////////////////////////////////////////////////////////////////////////////////////////
                    
        //             copyArtifacts filter: 'OPC_Receivables_API.zip', fingerprintArtifacts: true, projectName: '${JOB_NAME}' , selector: specific('${BUILD_NUMBER}')
        //             bat '7z X -y OPC_Receivables_API.zip -xr!appsettings.json -oC:\\inetpub\\wwwroot\\OPC\\Receivables'
                    
        //             /////////////////////////////////////////////// IIS Reset /////////////////////////////////////////////////////////////////////////////////////////////////////
        //             bat 'iisreset'
        //         }
        //         post {  
		// 	        always { echo 'This will always run' }  
		// 	        success { echo 'This will run only if successful' }  
		// 	        failure {  
		// 	                mail bcc: '', body: '''Dears,
		// 	                ElmX OPC deployment in CI environment has been failed''', cc: 'malkamary@elm.sa', from: 'jenkins@elm.sa', mimeType: 'text/html', replyTo: '', subject: 'ElmX OPC deployment in CI environment', to: 'abdelmawgood@elm.sa,mmostafa@elm.sa' }  
		// 	        }
        // }
        

        // stage('Auto Deploy DPP Mock in CI Env') {
	    //     agent {
        //                 label "buildagent-01"
        //         }
        //         steps {
        //         echo 'Download and deploy DPP mock on CI env'
        //         bat 'curl -X GET "http://nexus.elm.sa:8081/repository/libs-release-local/com/elm/qa/OPC/opc-test/1.0.0/opc-test-1.0.0.war" -o "C:\\OPCtomcat\\webapps\\opc-test-1.0.0.war"'
        //         bat 'C:\\OPCtomcat\\bin\\shutdown.bat'
        //         bat 'C:\\OPCtomcat\\bin\\startup.bat'
        //         }
		// 		post {  
		// 	        always { echo 'This will always run' }  
		// 	        success { echo 'This will run only if successful' }  
		// 	        failure {  
		// 	                mail bcc: '', body: '''Dears,
		// 	                ElmX Payment 3rd Party DPP deployment has been failed''', cc: 'malkamary@elm.sa', from: 'jenkins@elm.sa', mimeType: 'text/html', replyTo: '', subject: 'ElmX Payment API build', to: 'abdelmawgood@elm.sa,mmostafa@elm.sa' }  
		// 	        }
        // }
    
    	// stage('Build && Run Functional Test in CI Env') {
	    //     agent {
        //                 label "buildagent-01"
        //         }
        //         steps {
        //         git branch: 'QA', url: 'https://bitbucket.elm.sa/scm/ap/opc.git'
        //         echo 'Run ELMx OPC functional test in CI env'
        //         bat 'mvn clean package -DskipTests'
        //         bat 'java -jar opc-test-1.0.jar'
                
        //         }
        //         post {
        //             always { step([$class: 'Publisher', failedFails:'100' , failureOnFailedTestConfig: 'true' , reportFilenamePattern: '**/testng-results.xml']) }
		// 			success { echo 'This will run only if successful' }  
		// 			failure {  
		// 				mail bcc: '', body: '''Dears,
		// 	            ElmX OPC Functional Test in CI environment has been failed''', cc: 'malkamary@elm.sa', from: 'jenkins@elm.sa', mimeType: 'text/html', replyTo: '', subject: 'ElmX OPC Functional Test in CI enviornment', to: 'abdelmawgood@elm.sa,mmostafa@elm.sa'
		// 				}  
        //         }
        //     }

     //   }
    }
}