## Continuous Integration

What is continuous integration and why should you care? Continuous integration is the practice of routinely integrating code into the main branch of a repository. Testing of these integrations should be conducted as early as possible and often as possible [^Atlassian]. Automated testing is a key feature of conintuous integration. Automated tests with continuous integration help assure that your code is always in a working state. Verification of successfully building code is essential for continuous delivery and deployment to production code. This automated process allows for quick feedback on code and possible bugs. Manually testing and deploying takes significantly more time and by the time the manual testing might be done then more errors or bugs could be discovered allowing a sort of "build up" of problems [^Atlassian]. This chapter's objective will be to discuss various continuous integration tools like Travis CI, Circle CI and others. 

### Travis CI
<img style="margin: 0;" src="images/travisCI/travisCI.png">

Travis CI is a very popular continuous integration tool and integrating it with a GitHub account is simple to do. On Travis-ci.com a user can sign up with their GitHub account. After a user confirms the authorization of Travis CI they will be able to select which repositories they want to use with Travis CI. A user then needs to add a .travis.yml file to root directory of their project to tell Travis CI what to do. 

<br/>

Travis CI works by cloning a GitHub repository and carries out various build tasks to build and test code [^Travis]. If any of those tasks fail then the build is considered "broken", if they pass then the build is "passed". The build is "errored" and job stops immediately if a command in the before_install, install, or before_script phase returns a non-zero exit code [^Travis]. The build is "failed" if a command in the script phase returns a non-zero exit code [^Travis]. The job will run until it completes. Additionally, infrastructure environments can be specified based on what a project might need. Travis has Ubuntu Linux and macOS environments that run inside full virtual machines [^Travis]. Setting a specific environment would be something to investigate if a project was platform dependent, for example, a project written in Swift. While Travis CI support a wide range of languages the .travis.yml file below is an example for a Node.js project.
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
  
install: #Install dependencies of the project before any run scripts for the virtual environment
  - npm install
  
script: #Scripts that need to run in the Travis CI virtual environment
  - npm run buildScript
  - npm run test
```

<br/>

Working with Travis CI through a project's .travis.yml file is very straightforward and offers a lot of configuration. Travis CI has the ability to clone to a certain depth within a repository. This would only be helpful if a user is performing git operations in their build [^Travis]. As seen in the examples below branches can be set that trigger Travis CI builds [^Travis].
```yaml
# blocklist - These branches will not trigger Travis CI builds
branches:
  except:
  - legacy
  - experimental

# safelist - These branches will trigger Travis CI build
branches:
  only:
  - master
  - stable

# The below yaml code triggers Travis CI builds on ALL branches in the repository
branches:
  only:
  - gh-pages
  - /.*/
  
# Travis CI also support *regular expressions*
branches:
  only:
  - master
  - /^deploy-.*$/
```
Now that you know how to trigger a build it is time to learn about specifying what to build. One simple way is to specify scripts that Travis CI should run. However, if you have a large project in which you want to test multiple parallel jobs then the build matrix is what you need. "The build matrix combines a language-and-environment dependent set of configuration options to automatically create a matrix of all possible combinations" [^Travis]. There are a few key features that will allow you to get started with the matrix. The include option specifies which entries a user wants to build. One instance where this would be helpful if a user wanted to test a specific version of a dependency with a specific runtime environment [^Travis]. The exclude option does just what it says. Users have control over job exclusion based on language, environment, and other options. Lastly, failures are allowed to happen and not break the build if the user specifies which failures are tolerable. This allows for users to add experimental and preparatory builds to test against versions or configureations that are not ready to be officially supported [^Travis]. Below is an example from Travis CI's documentation.
```yaml
language: ruby
rvm:
- 1.9.3
- 2.0.0
- 2.1.0
env:
- DB=mongodb
- DB=redis
- DB=mysql
gemfile:
- Gemfile
- gemfiles/rails4.gemfile
- gemfiles/rails31.gemfile
- gemfiles/rails32.gemfile

matrix:
  exclude:
  - rvm: 2.0.0
    gemfile: Gemfile
    
  include:
  - rvm: 2.1.0
    gemfile: gemfiles/rails32.gemfile
    env: DB=mongodb
    
  allow_failures:
  - rvm: 1.9.3
  
  fast_finish: true # allows for job to be marked as finished when all required jobs finish. 
                    # The allow_failure jobs will continue to run.
```
Tavis CI also has the ability to use Docker in builds. Docker commands are issued in the before_install section of the .travis.yml file.


 <br/>
 <br/>
 ### Circle CI
 

References

<br/>

[^Atlassian]: Atlassian. “Continuous Integration, Explained.” Atlassian, Atlassian, www.atlassian.com/continuous-delivery/continuous-integration.
<br/>
[^Travis]: “Travis.” Travis CI Docs, docs.travis-ci.com/.
