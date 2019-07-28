# This is a template for Jenkinsfile and it is consisting of main stages for CICD pipeline as per the following:
   1- Environment Section 
      \\Include all variables used in the pipeline
   2- Clean working directory on buildAgent 
      \\1st Stage to clean the working directory before building the project 
   3- 

# Implementation

1. Copy the folder "bamboo-specs" under your project.
2. Replace the following boilerplate's variables in files under "bamboo-specs/":
- `{{REPO_NAME}}`: 
   it is the GIT repository name in a regular case (not camel case). Or may be the name of the component.
 
   i.e: If the repository name is kyc-admin, `{{REPO_NAME}}` can be **KYC Admin**.

- `{{PROJECT_KEY}}`: this is the key of the project in Bamboo. i.e: ELMX, KYC,...


- `{{BUILD_PLAN_KEY}}` The key assigned to the Build plan.

Make sure to replace these variables with the actual values.


# Which repositories use this boilerplate ?

- https://bitbucket.elm.sa/scm/taf/tribe_affairs.git
- https://bitbucket.elm.sa/scm/taf/tribe-affairs-db-scripts.git
- https://bitbucket.elm.sa/scm/mar/marine-db-scripts.git

We are assigning label `bamboo-specs-v2` for tickets resolved by this Boilerplate : [https://jira.elm.sa/browse/SUP-2404?jql=labels%20%3D%20bamboo-specs-v2](https://jira.elm.sa/browse/SUP-2404?jql=labels%20%3D%20bamboo-specs-v2)
