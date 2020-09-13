1. create 3 branches: develop(default), staging, master. and make develop branch default by going to settings --> branches
2. setup circle CI jobs by logging into circleCI and selecting the project for setup there by choosing your accounts there.
3. 


CIRCLE CI CONCEPTS:

 orbs: its like npm packages/libraries that if imported gives us easy access to a lot of things like aws/slack images for easy integration.
 ------------------------
 workflow is made of many jobs and run order too. 
 while defining workflow, first workflow name should be mentioned and then different jobs that will run in that workflows.
 e.g::
 "build_and_test" is the name of workflow. "jobs" defines the list of jobs for running below and then we put "requires" as a checklist for starting that particular job.
 --> build1 runs independently of other jobs.
 --> build2 running requires build1 to complete successfully.
 --> build3 running requires build1 to complete successfully.
  
 workflows:
  build_and_test: # name of your workflow
    jobs:
      - build1
      - build2:
          requires:
           - build1 # wait for build1 job to complete successfully before starting
      - build3:
          requires:
           - build1 # wait for build1 job to complete successfully before starting
           # run build2 and build3 concurrently to save time.
 
 --------------------------
 JOBS::
 ----
 
 Jobs are specific tasks that will be executed.
 e.g:
 build job ---> builds the code from commited changes[when we commit we only push the changes to github only not the whole code].This is always the first job.
 lint job -->  lint the code so that a specific standard can be maintained.
 test job ---> tests the codebase with unit/integration test.
 aws deploy jobs ---> it deploys the code to aws.
 slack job ---> it sends the notification to slack
 
 The description of a job:
 All the jobs contain name. Inside name we define the environment where we run the job like docker images and steps which has command for running the jobs.
 
 whenever first job run we will cache the node modules and other dependencies first time and for other jobs subsequently
 we restore_cache only.
 
 
 jobs:
  build1: //name of the first job
    docker: //environment
      - image: circleci/ruby:2.4-node
      - image: circleci/postgres:9.4.12-alpine
    steps: //command
      - checkout
      - save_cache: # Caches dependencies with a cache key
          key: v1-repo-{{ .Environment.CIRCLE_SHA1 }}
          paths:
            - ~/circleci-demo-workflows
      
  build2: //name of the second job
    docker:
      - image: circleci/ruby:2.4-node
      - image: circleci/postgres:9.4.12-alpine
    steps:
      - restore_cache: # Restores the cached dependency.
          key: v1-repo-{{ .Environment.CIRCLE_SHA1 }}
      - run:
          name: Running tests
          command: make test
  build3: //name of the third job
    docker:
      - image: circleci/ruby:2.4-node
      - image: circleci/postgres:9.4.12-alpine
    steps:
      - restore_cache: # Restores the cached dependency.
          key: v1-repo-{{ .Environment.CIRCLE_SHA1 }}
      - run:
          name: Precompile assets
          command: bundle exec rake assets:precompile

 
 
 
 
 
 
 
 
 
 
 
