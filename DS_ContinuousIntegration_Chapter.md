## Continuous Integration

What is continuous integration and why should you care? Continuous integration is the practice of routinely integrating code into the main branch of a repository. Testing of these integrations should be conducted as early as possible and often as possible [^Atlassian]. Automated testing is a key feature of conintuous integration. Automated tests with continuous integration help assure that your code is always in a working state. Verification of successfully building code is essential for continuous delivery and deployment to production code. This automated process allows for quick feedback on code and possible bugs. Manually testing and deploying takes significantly more time and by the time the manual testing might be done then more errors or bugs could be discovered allowing a sort of "build up" of problems [^Atlassian]. This chapter's objective will be to discuss various continuous integration tools like Travis CI, Circle CI and others. 

<br/>

### Travis CI
<img style="margin: 0;" src="images/travisCI/travisCI.png">

Travis CI is a very popular continuous integration tool and integrating it with a GitHub account is simple to do. On Travis-ci.com a user can sign up with their GitHub account. After a user confirms the authorization of Travis CI they will be able to select which repositories they want to use with Travis CI. A user then needs to add a .travis.yml file to root directory of their project to tell Travis CI what to do. While Travis CI support a wide range of languages the .travis.yml file below is an example for a Node.js project.
```yaml
language: node_js #Specify the language of the project

node_js: #Specify various versions that Travis CI should run
  - "10"
  - "9"
  
# safelist
branches:
  only:
  - master
  - stable
  
before_script: #Install dependencies of the project before any run scripts for the virtual environment
  - npm install
  
script: #Scripts that need to run in the Travis CI virtual environment
  - npm run buildScript
  - npm run test
```

<br/>

Working with Travis CI through a project's .travis.yml file is very straightforward.

<br/>

Travis CI works by cloning a GitHub repository and carries out various build tasks to build and test code [^Travis]. If any of those tasks fail then the build is considered "broken", if they pass then the build is "passed". The build is "errored" and job stops immediately if a command in the before_install, install, or before_script phase returns a non-zero exit code [^Travis]. The build is "failed" if a command in the script phase returns a non-zero exit code [^Travis]. The job will run until it completes. Additionally, infrastructure environments can be specified based on what a project might need. Travis has Ubuntu Linux and macOS environments that run inside full virtual machines [^Travis]. Setting a specific environment would be something to investigate if a project was platform dependent, for example, a project written in Swift.

 

References

<br/>

[^Atlassian]: Atlassian. “Continuous Integration, Explained.” Atlassian, Atlassian, www.atlassian.com/continuous-delivery/continuous-integration.
<br/>
[^Travis]: “Travis.” Travis CI Docs, docs.travis-ci.com/.
