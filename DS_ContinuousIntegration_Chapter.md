## Continuous Integration

What is continuous integration and why should you care? Continuous integration is the practice of routinely integrating code into the main branch of a repository. Testing of these integrations should be conducted as early as possible and often as possible [^Atlassian]. Automated testing is a key feature of continuous integration. Automated tests with continuous integration help assure that your code is always in a working state. Verification of successfully building code is essential for continuous delivery and deployment to production code. This automated process allows for quick feedback on code and possible bugs. Manually testing and deploying takes significantly more time and by the time the manual testing might be done then more errors or bugs could be discovered allowing a sort of "build up" of problems [^Atlassian]. This chapter's objective will be to discuss a couple of continuous integration tools like Travis CI and Circle CI.

## Travis CI

Travis CI is a popular continuous integration tool and integrating it with a GitHub account is simple. On Travis-ci.com a user can sign up with their GitHub account. After a user confirms the authorization of Travis CI they will be able to select which repositories they want to use with Travis CI. A user then needs to add a .travis.yml file to the root directory of their project to tell Travis CI what to do. 

#### Build Status and Environments

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

#### Git Clone Depth and Branch Specification

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

#### Scripts and Build Matrices

Now that you know how to trigger a build, it is time to learn about specifying what to build. One simple way is to specify scripts that Travis CI should run. However, if you have a large project in which you want to test multiple parallel jobs then the build matrix is what you need. "The build matrix combines a language-and-environment dependent set of configuration options to automatically create a matrix of all possible combinations" [^Travis]. There are a few key features that will allow you to get started with the matrix. The include option specifies which entries a user wants to build. One instance where this would be helpful if a user wanted to test a specific version of a dependency with a specific runtime environment [^Travis]. The exclude option does just what it says. Users have control over job exclusion based on language, environment, and other options. Lastly, failures are allowed to happen and not break the build if the user specifies which failures are tolerable. This allows for users to add experimental and preparatory builds to test against versions or configureations that are not ready to be officially supported [^Travis]. Below is an example from Travis CI's documentation.
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
  
  fast_finish: true # allows for a job to be marked as finished when all required jobs finish. 
                    # The allow_failure jobs will continue to run.
```

#### Docker

Tavis CI also has the ability to use Docker in builds. Docker commands are issued in the before_install section of the .travis.yml file. Docker images can be used by either pulling them from a repository or a Docker image can be built from a Dockerfile [^Travis]. Docker Compose is available and installing newer Docker versions is also possible [^Travis]. Multi-image coordination and being able to test newer versions of Docker in virtual environments helps ensure that a product is always ready for deployment to production. Here is a sample from Travis CI's documentation.
```yaml
language: ruby

services:
  - docker

before_install:
  - docker build -t carlad/sinatra .
  - docker run -d -p 127.0.0.1:80:4567 carlad/sinatra /bin/sh -c "cd /root/sinatra; bundle exec foreman start;"
  - docker ps -a
  - docker run carlad/sinatra /bin/sh -c "cd /root/sinatra; bundle exec rake test"

script:
  - bundle exec rake test
```

#### Deploying Projects

Continuous deployment is another powerful feature of Travis CI. Currently, Travis CI supports deployment to 39 providers [^Travis]. A few of these include Heroku, Google Firebase, Snap Store, NPM, PYPI, and many more. Multiple providers can be deployed to by specifying the provider name under the deploy key with the necessary credentials [^Travis]. Additionally, deployment conditionals can be specified with the *on* key. This will ensure that deployment only occurs when a specific condition it met. One example might be a specific branch specification.

 ## Circle CI
 
CircleCI is another popular continuous integration tool that is used by developers. It allows for seamless integration with both GitHub and Bitbucket. Getting started is as easy as creating a new account on CircleCI by signing up with your GitHub account. In the dashboard area of CircleCI users have the option to add a repository as a new project on CircleCI. When a project is added to Circle CI a deploy key is added to the repository from either GitHub or Bitbucket setting, this key is used to check out your project from a repository [^Circle]. A service hook is also added to notify Circle CI when a push is made to the repository. CircleCI builds push hooks by default. Builds are triggered for all push and PR hooks for a repository. The hooks can be edited to restrict what is built. It is up to the user to configure how their project is built. CircleCI offers a lot of functionality and flexibility which is configured in a project's .circleci/config.yml directory and file.

#### Steps, Jobs, and Workflows
 
There are various parts of a config.yml file that allow for customizable builds. Jobs in the config.yml file are comprised of various steps and each job runs in its own container [^Circle]. Steps are the basic commands that get executed in a job. Jobs can be run using a specific executor which is a specified environment in which jobs will be run [^Circle]. A few notable executors are docker, machine, or macos. Various options will need to be specified under executor environments, for example, a docker image [^Circle]. Defining a specified executor allows for it to be used across multiple different jobs in your .config.yml file. Properly testing a large project might require a large .config.yml file and take a long time to complete. With CircleCI it is possible to specify numerous jobs that need to be run and run them in parallel to save time [^Circle]. In the example below from Circle CI's documentation, you can see that separate jobs are constructed and they are specified to run under the workflow section of the file.
```yaml
version: 2
jobs:
  build:
    docker:
      - image: circleci/<language>:<version TAG>
    steps:
      - checkout
      - run: <command>
  test:
    docker:
      - image: circleci/<language>:<version TAG>
    steps:
      - checkout
      - run: <command>
workflows:
  version: 2
  build_and_test:
    jobs:
      - build
      - test
```

#### Orbs

Creating your own jobs, executors, and other elements may not be necessary. CircleCI allows for projects to import premade configuration packages called Orbs [^Circle]. Importing an Orb is as simple as specifying the Orb key in your config file and then including which Orbs you would like to use. A project can utilize third-party Orbs but a project admin must opt-in for this option [^Circle]. Orbs allow for a standardization of configurations across multiple projects. Orbs are also comprised of three main elements: Commands, Jobs, and Executors [^Circle]. Commands are reusable steps that can be invoked with parameters within an existing job. Below is an example of how to write and invoke a command from an orb from Circle CI's documentation.

```yaml
# Commands are not restricted to belonging to Orbs
version: 2.1
commands:
  sayhello:
    description: "A very simple command for demonstration purposes"
    parameters:
      to:
        type: string
        default: "Hello World"
    steps:
      - run: echo << parameters.to >>

jobs:
  myjob:
    docker:
      - image: "circleci/node:9.6.1"
    steps:
      - myorb/sayhello:
          to: "Lev"
```

#### Deploying Projects

Once the project config.yml file is configured to test the code whichever way is deemed fit, a developer should look into configuring deployment options for Circle CI. A vast amount of services are able to be deployed to with Circle CI. Some of these include: Amazon Web Services, Azure, Firebase, Google Cloud, and Heroku [^Circle]. While the deployment steps vary for each platform, there will typically be an Orb that can make the process easier for users. Snapcraft is also an option for deployment on Linux distributions. Everything necessary for generating a .snap file can be done in the config.yml file. There are also other options for distribution such as Artifactory.

#### Summary

Travis and Circle CI were covered in this chapter because they are a few of the most prevalent continuous integration tools. There are many more conitnuous integration tools available to developers if neither Travis or Circle CI meet a project's needs. For example, ThoughtWorks or Jenkins to name just a couple. The ability to test source code against a wide range of environments and ensuring code still builds correctly guarantees that your code will never break. Additionally, the ability to automate deployment to a range of platforms helps to ensure that code is always delivered in a timely manner and steps are not forgotten by developers.

#### References

[^Atlassian]: Atlassian. “Continuous Integration, Explained.” Atlassian, Atlassian, www.atlassian.com/continuous-delivery/continuous-integration.
<br/>
[^Travis]: “Travis.” Travis CI Docs, docs.travis-ci.com/.
<br/>
[^Circle]: “Welcome to CircleCI Documentation.” CircleCI, circleci.com/docs/.
