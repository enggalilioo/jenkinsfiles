This is a template for Jenkinsfile and it is consisting of main stages for CICD pipeline as per the following:
#### Environment Section 
   - Include all variables used in the pipeline
#### Stages
1. Clean working directory on buildAgent 
   - Stage to clean the working directory before building the project 
2. Compile, Build && running SonarQube analysis
   - Stage to compile, build, package and running sonarqube static code analysis
3. SonarQube Quality Gate
   - Stage will check the quality gates as per defined in Sonarqube server
4. Auto Deploy DB Project in CI Env
   - Stage to auto deploy DB changes in CI env.
5. Auto Deploy Application in CI Env
   - Stage to auto deploy application in CI env.
6. Auto Deploy Mock in CI Env
   - Stage to deploy 3rd party mocking to faciliate functional testing purpose
7. Build && Run Functional Test in CI Env
   - Stage to build, package, deploy and run funtional test in CI env. 

## Implementation

1. Replace variables values in `environment` section with the actual values as per each project
     - i.e: `sonarProjKey` should be replaced with the actual project name like **Admin Portal**
     - i.e: `gitBranch` should be replaced with the actual value of the git branch name like **development**
2. Modify each stage with the required steps 
     - i.e: If the project based on Java technology we will add a step in build stage for maven to build the project,
         > bat 'mvn clean package'


Make sure to review each stage and all inside steps
This jenkinsfile should be pushed to the git repo as part of source code and to configure Jenkins job to invoke the build execution through the this jenkinsfile 
