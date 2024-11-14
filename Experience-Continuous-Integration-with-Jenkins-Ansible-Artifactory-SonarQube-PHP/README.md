
# Experience-Continuous-Integration-with-Jenkins-Ansible-Artifactory-SonarQube-PHP


```IMPORTANT NOTICE``` - This project has some initial theoretical concepts that must be well understood before moving on to the practical part. Please read it carefully as many times as needed to completely digest the most important and fundamental DevOps concepts. To successfully implement this project, it is crucial to grasp the importance of the entire CI/CD process, roles of each tool and success metrics - so we encourage you to thoroughly study the following theory until you feel comfortable explaining all the concepts (e.g., to your new junior colleague or during a job interview).

In previous projects, We have been deploying a tooling website directly into the /var/www/html directory on dev servers. Well, even though that worked fine, and we were able to access the website, it is not the best way to do it. Real world web application code written on Java, .NET or other compiled programming languages require a build stage to create an executable file. The executable file (e.g., jar file in case of Java) contains all the codes embedded, and the necessary library dependencies, which the application needs to run and work successfully.

Some other programs written languages such as PHP, JavaScript or Python work directly without being built into an executable file - these languages are called ``interpreted``. That is why we could easily deploy the entire code from git into var/www/html and immediately the webserver was able to render the pages in a browser. However, it is not optimal to download code directly from Git onto our servers. There is a smarter way to package the entire application code, and track release versions. We can package the entire code and all its dependencies into an archive such as .tar.gz or .zip, so that it can be easily unpacked on a respective environment's servers.

In this project, you will understand and get hands on experience around the entire concept around CI/CD from applications perspective. To fully gain real expertise around this idea, it is best to see it in action across different programming languages and from the platform perspective too. From the application perspective, we will be focusing on PHP here.

## What is Continuous Integration?


Continuous Integration (CI) is a practice of merging all developers' working copies to a shared mainline (e.g., Git Repository or some other version control system) several times per day. Frequent merges reduce chances of any conflicts in code and allow to run tests more often to avoid massive rework if something goes wrong. This principle can be formulated as Commit early, push often.



The general idea behind multiple commits is to avoid what is generally considered as Merge Hell or Integration hell. When a new developer joins a new project, he or she must create a copy of the main codebase by starting a new feature branch from the mainline to develop his own features (in some organization or team, this could be called a develop, main or master branch). If there are tens of developers working on the same project, they will all have their own branches created from mainline at different points in time. Once they make a copy of the repository it starts drifting away from the mainline with every new merge of other developers' codes. If this lingers on for a very long time without reconciling the code, then this will cause a lot of code conflict or Merge Hell, as rightly said. Imagine such a hell from tens of developers or worse, hundreds. So, the best thing to do, is to continuously commit & push your code to the mainline. As many times as tens times per day. With this practice, We can avoid Merge Hell or Integration hell.


###  CI concept is not only about committing your code. There is a general workflow, let us start it.

- Run tests locally: Before developers commit their code to a central repository, it is recommended to test the code locally. So, Test-Driven Development (TDD) approach is commonly used in combination with CI. Developers write tests for their code called unit-tests, and before they commit their work, they run their tests locally. This practice helps a team to avoid having one developer's work-in-progress code from breaking other developers' copy of the codebase.

- Compile code in CI: After testing codes locally, developers commit and push their work to a central repository. Rather than building the code into an executable locally, a dedicated CI server picks up the code and runs the build there. In this project we will use, already familiar to you, Jenkins as our CI server. Build happens either periodically - by polling the repository at some configured schedule, or after every commit. Having a CI server where builds run is a good practice for a team, as everyone has visibility into each commit and its corresponding builds.

- Run further tests in CI: Even though tests have been run locally by developers, it is important to run the unit-tests on the CI server as well. But, rather than focusing solely on unit-tests, there are other kinds of tests and code analysis that can be run using CI server. These are extremely critical to determining the overall quality of code being developed, how it interacts with other developers' work, and how vulnerable it is to attacks. A CI server can use different tools for Static Code Analysis, Code Coverage Analysis, Code smells Analysis, and Compliance Analysis. In addition, it can run other types of tests such as Integration and Penetration tests. Other tasks performed by a CI server include production of code documentation from the source code and facilitate manual quality assurance (QA) testing processes.

- Deploy an artifact from CI: At this stage, the difference between CI and CD is spelt out. As you now know, CI is Continuous Integration, which is everything we have been discussing so far. CD on the other hand is Continuous Delivery which ensures that software checked into the mainline is always ready to be deployed to users. The deployment here is manually triggered after certain QA tasks are passed successfully. There is another CD known as Continuous Deployment which is also about deploying the software to the users, but rather than manual, it makes the entire process fully automated. Thus, Continuous Deployment is just one step ahead in automation than Continuous Delivery.


### Continuous Integration in The Real World.

To emphasize a typical CI Pipeline further, let us explore the diagram below a little deeper.

![image](https://github.com/user-attachments/assets/abc0c369-14fd-4266-a95b-4786afdbed8c)

- Version Control: This is the stage where developers' code gets committed and pushed after they have tested their work locally.

- Build: Depending on the type of language or technology used, we may need to build the codes into binary executable files (in case of compiled languages) or just package the codes together with all necessary dependencies into a deployable package (in case of interpreted languages).
- Unit Test: Unit tests that have been developed by the developers are tested. Depending on how the CI job is configured, the entire pipeline may fail if part of the tests fails, and developers will have to fix this failure before starting the pipeline again. A Job by the way, is a phase in the pipeline. Unit Test is a phase, therefore it can be considered a job on its own.
- deploy: Once the tests are passed, the next phase is to deploy the compiled or packaged code into an artifact repository. This is where all the various versions of code including the latest will be stored. The CI tool will have to pick up the code from this location to proceed with the remaining parts of the pipeline.
- Auto Test: Apart from Unit testing, there are many other kinds of tests that are required to analyse the quality of code and determine how vulnerable the software will be to external or internal attacks. These tests must be automated, and there can be multiple environments created to fulfil different test requirements. For example, a server dedicated for Integration Testing will have the code deployed there to conduct integration tests. Once that passes, there can be other sub-layers in the testing phase in which the code will be deployed to, so as to conduct further tests. Such are User Acceptance Testing (UAT), and another can be Penetration Testing. These servers will be named according to what they have been designed to do in those environments. A UAT server is generally be used for UAT, SIT server is for Systems Integration Testing, PEN Server is for Penetration Testing and they can be named whatever the naming style or convention in which the team is used. An environment does not necessarily have to reside on one single server. In most cases it might be a stack as you have defined in your Ansible Inventory. All the servers in the inventory/dev are considered as Dev Environment. The same goes for inventory/stage (Staging Environment) inventory/preprod (Pre-production environment), inventory/prod (Production environment), etc. So, it is all down to naming convention as agreed and used company or team wide.
- Deploy to production: Once all the tests have been conducted and either the release manager or whoever has the authority to authorize the release to the production server is happy, he gives green light to hit the deploy button to ship the release to production environment. This is an Ideal Continuous Delivery Pipeline. If the entire pipeline was automated and no human is required to manually give the Go decision, then this would be considered as Continuous Deployment. Because the cycle will be repeated, and every time there is a code commit and push, it causes the pipeline to trigger, and the loop continues over and over again.
- Measure and Validate: This is where live users are interacting with the application and feedback is being collected for further improvements and bug fixes. There are many metrics that must be determined and observed here. We will quickly go through 13 metrics that MUST be considered.

## Common Best Practices of CI/CD

Before we move on to observability metrics - let us list down the principles that define a reliable and robust CI/CD pipeline:

- Maintain a code repository.
- Automate build process.
- Make builds self-tested.
- Everyone commits to the baseline every day.
- Every commit to baseline should be built.
- Every bug-fix commit should come with a test case.
- Keep the build fast.
- Test in a clone of production environment.
- Make it easy to get the latest deliverables.
- Everyone can see the results of the latest build.
- Automate deployment (if you are confident enough in your CI/CD pipeline and willing to go for a fully automated Continuous Deployment).

### Why are we doing everything we are doing? - 13 DevOps Success Metrics.

- After all, DevOps is all about continuous delivery or deployment, and being able to ship out quality code as fast as possible. This is a very ambitious thing to desire; therefore, we must be careful not to break things as we are moving very fast. By tracking these metrics, we can determine our delivery speed and bottlenecks before breaking things.

- Ultimately, the goals of DevOps are enhanced Velocity, Quality, and Performance. But how do we track these parameters? Let us have a look at the 13 metrics to watch out for.

1. Deployment frequency: Tracking how often we do deployments is a good DevOps metric. Ultimately, the goal is to do more smaller deployments as often as possible. Reducing the size of deployments makes it easier to test and release. I would suggest counting both production and non-production deployments separately. How often you deploy to QA or pre-production environments is also important. You need to deploy early and often in QA to ensure enough time for testing.

2. Lead time: If the goal is to ship code quickly, this is a key DevOps metric. I would define lead time as the amount of time that occurs between starting on a work item until it is deployed. This helps you know that if you started on a new work item today, how long would it take on average until it gets to production.

3. Customer tickets: The best and worst indicator of application problems is customer support tickets and feedback. The last thing you want is your users reporting bugs or having problems with your software. Because of this, customer tickets also serve as a good indicator of application quality and performance problems.

4. Percentage of passed automated tests: To increase velocity, it is highly recommended that the development team makes extensive usage of unit and functional testing. Since DevOps relies heavily on automation, tracking how well automated tests work is a good DevOps metrics. It is good to know how often code changes break tests.

5. Defect escape rate: Do you know how many software defects are being found in production versus QA? If you want to ship code fast, you need to have confidence that you can find software defects before they get to production. Defect escape rate is a great DevOps metric to track how often those defects make it to production.

6. availability: The last thing we ever want is for our application to be down. Depending on the type of application and how we deploy it, we may have a little downtime as part of scheduled maintenance. It is highly recommended to track this metric and all unplanned outages. Most software companies build status pages to track this. Such as this Google Products Status Page.

7. Service level agreements: Most companies have some service level agreement (SLA) that they promise to the customers. It is also important to track compliance with SLAs. Even if there are no formally stated SLAs, there probably are application non-functional requirements or expectations to be met.

8. Failed deployments: We all hope this never happens, but how often do our deployments cause an outage or major issues for the users? Reversing a failed deployment is something we never want to do, but it is something you should always plan for. If you have issues with failed deployments, be sure to track this metric over time. This could also be seen as tracking *Mean Time To Failure (MTTF).

9. Error rates: Tracking error rates within the application is super important. Not only they serve as an indicator of quality problems, but also ongoing performance and uptime related issues. In software development, errors are also known as exceptions, and proper exception handling is critical. If they are not handled nicely, we can figure it out while monitoring the rate of errors.

    - Bugs – Identify new exceptions being thrown in the code after a deployment
    - Production issues – Capture issues with database connections, query timeouts, and other related issuesPresenting error rate metrics like this simply gives greater insights into where to focus attention.

10. Application usage & traffic: After a deployment, we want to see if the number of transactions or users accessing our system looks normal. If we suddenly have no traffic or a giant spike in traffic, something could be wrong. An attacker may be routing traffic elsewhere, or initiating a DDOS attack.

11. Application performance: Before we even perform a deployment, we should configure monitoring tools like Retrace, DataDog, New Relic, or AppDynamics to look for performance problems, hidden errors, and other issues. During and after the deployment, we should also look for any changes in overall application performance and establish some benchmarks to know when things deviate from the norm. It might be common after a deployment to see major changes in the usage of specific SQL queries, web service or HTTP calls, and other application dependencies. These monitoring tools can provide valuable visualizations like this one below that helps make it easy to spot problems.

12. Mean time to detection (MTTD): When problems happen, it is important that we identify them quickly. The last thing we want is to have a major partial or complete system outage and not know about it. Having robust application monitoring and good observability tools in place will help us detect issues quickly. Once they are detected, we also must fix them quickly!

13. Mean time to recovery (MTTR): This metric helps us track how long it takes to recover from failures. A key metric for the business is keeping failures to a minimum and being able to recover from them quickly. It is typically measured in hours and may refer to business hours, not calendar hours.


## Simulating a typical CI/CD Pipeline for a PHP Based application:


We will be tasked to create a pipeline that simulates continuous integration and delivery. Target end to end CI/CD pipeline is represented by the diagram below.

![image](https://github.com/user-attachments/assets/940c60e9-1ee7-46ca-a00f-2ac9b39612b0)

- It is important to know that both Tooling and TODO Web Applications are based on an interpreted (scripting) language (PHP).
- It means, it can be deployed directly onto a server and will work without compiling the code to a machine language.
- The problem with that approach is, it would be difficult to package and version the software for different releases.
- In this project, we will be using a different approach for releases, rather than downloading directly from git, we will be using Ansible uri module.

### Set Up.

# Configure Ansible configuration repo For Jenkins Deployment

This is project is continuation from my ansible dynamic assignment projects. I would be using a copy of the ansible-config-mgt repo which I have renamed ansible-project.

![image](https://github.com/user-attachments/assets/efbf1328-54ba-48db-8de7-a9d2adb2e4c5)

## 1 Installing Jenkins.

Provision the Machine for jenkins:
![image](https://github.com/user-attachments/assets/61510651-2fa8-4b56-93d3-5a2805a42077)

![image](https://github.com/user-attachments/assets/dfe2b67b-4a90-4ba4-b5fe-7468a70a83ad)

Updating the machine
```
sudo apt update
```

Installing the java

```
sudo apt install default-jdk-headless
```

Installing jenkins through Official Website  https://pkg.jenkins.io/debian-stable/

This is the Debian package repository of Jenkins to automate installation and upgrade.
```
  sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
    https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
```

Then add a Jenkins apt repository entry:
```
      echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc]" \
    https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
    /etc/apt/sources.list.d/jenkins.list > /dev/null

```

Update your local package index, then finally install Jenkins:
```
  sudo apt-get update
  sudo apt-get install fontconfig openjdk-17-jre
  sudo apt-get install jenkins
```

Checking the Jenkins version.

```
jenkins --version
```

![image](https://github.com/user-attachments/assets/91099522-36ce-4066-b758-7b1dc08ea620)


## 2 Installing Blue-Ocean Plugin

To make managing your Jenkins pipelines easier and more intuitive, install the Blue Ocean plugin. This plugin offers a user-friendly and visually appealing interface, helping you quickly understand the status of your continuous delivery pipelines.

To do this , follow the steps below :
- Go to manage jenkins > manage plugins > available
- Search for BLUE OCEAN PLUGIN and install

![image](https://github.com/user-attachments/assets/fc36bb47-7844-49da-a2cc-7b45ed5cc650)

## 3 Configure the Blue Ocean plugin pipeline with our github repo.

To do this, follow the step below:

- Open the blue oceans plugin and create a new pipeline
- Select github.
- Connect github with jenkins using your github personal access token.
- Select the repository.
- Create the pipeline.

![image](https://github.com/user-attachments/assets/f9ddaae5-9088-4fa9-b91e-525db243f36a)

#### Login to GitHub & Generate an Access Token.

![image](https://github.com/user-attachments/assets/32dd6057-da94-465b-901d-ed4b1e53b464)

Paste Token on To Connect with github.
![image](https://github.com/user-attachments/assets/a998a545-fedb-48f2-bb70-d84fd8a0a206)


Selecting the ansible-project and creating the project.
![image](https://github.com/user-attachments/assets/e7ed79cb-a43f-4bb8-9c53-d7a6456ea3f6)


At this point you may not have a Jenkinsfile in the Ansible repository, so Blue Ocean will attempt to give you some guidance to create one. But we do not need that. We will rather create one ourselves. So, click on Administration to exit the Blue Ocean console.

![image](https://github.com/user-attachments/assets/e5442148-30a3-4b0b-8371-d60111a4c15f)

Let us create our Jenkinsfile

Inside the Ansible project, create a new directory deploy and start a new file Jenkinsfile inside the directory.

![image](https://github.com/user-attachments/assets/f75a3e4a-8d82-4919-8033-82aa5fb88956)


Add the code snippet below to start building the Jenkinsfile gradually. This pipeline currently has just one stage called Build and the only thing we are doing is using the shell script module to echo Building Stage


```
pipeline {

        agent any

        stages{
            stage ('Build') {
                steps {
                    sh 'echo "Building Stage"'
                }
            }

        }

}

```

Now go back into the Ansible pipeline in Jenkins, and select configure.

![image](https://github.com/user-attachments/assets/e7997b0a-b304-4b2a-84d4-0c2bf0c55fcd)

Scroll down to Build Configuration section and specify the location of the Jenkinsfile at deploy/Jenkinsfile.

![image](https://github.com/user-attachments/assets/139b1130-efab-4efc-a6ad-0549470360b0)

Back to the pipeline again, this time click "Build now"

![image](https://github.com/user-attachments/assets/51e592d9-30ab-412c-bae2-0cfa505f625f)

- Go back to the pipeline again, this time click Build now and then click on blue ocean on the right menu.

![image](https://github.com/user-attachments/assets/975deebe-642e-4a68-9068-143d6717954c)

![image](https://github.com/user-attachments/assets/b275383e-b686-45f6-8ed5-1813f003cfbc)

Let us see this in action.

1. Create a new git branch and name it feature/jenkinspipeline-stages


![image](https://github.com/user-attachments/assets/8c8a5a09-e4ed-4f5a-92e3-26ad0eafb4c1)

It will going to create the pipeline automatically.. We dont have to build and cofigure the pipeline again.
![image](https://github.com/user-attachments/assets/c4b5ea51-5f8c-4ccf-87fe-581d419e9d90)

2. Currently we only have the Build stage. Let us add another stage called Test. Paste the code snippet below and push the new changes to GitHub.

Add below code inside the JenkinsFile push code on remote repo and build it.
```
pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                script {
                    sh 'echo "Building Stage"'
                }
            }
        }
        stage('Test') {
            steps {
                script {
                    sh 'echo "Testing Stage"'
                }
            }
        }
    }
}


```

![image](https://github.com/user-attachments/assets/22d528de-24e6-4be5-9cdc-aba4b04e800a)

Additinal Tasks to perform tp better understand the whole process.

- Let's create a pull request to merge the latest code into the main branch, after merging the PR, go back into your terminal and switch into the main branch.Pull the latest change.

- Create a new branch, add more stages into the Jenkins file to simulate below phases. (Just add an echo command like we have in build and test stages)

1. Package.
2. Deploy.
3. Clean up.


Creating the new branch to add more stages...

![image](https://github.com/user-attachments/assets/5a003726-b485-42c0-a767-d1984bd48b14)

![image](https://github.com/user-attachments/assets/a1f0ebf0-0721-43d4-898d-1716887480fd)

```
pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                script {
                    sh 'echo "Building Stage"'
                }
            }
        }
        stage('Test') {
            steps {
                script {
                    sh 'echo "Testing Stage"'
                }
            }
        }
        stage('Package') {
            steps {
                script {
                    sh 'echo "Package"'
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    sh 'echo "Deploy"'
                }
            }
        }
        stage('Clean up') {
            steps {
                script {
                    sh 'echo "Clean up"'
                }
            }
        }
    }
}

```

![image](https://github.com/user-attachments/assets/1d25e93b-288d-4a79-90cf-e540cb4cb0b3)

![image](https://github.com/user-attachments/assets/06f24263-effd-417e-a071-c9525dbb4bc1)


## Running Ansible Playbook from Jenkins.

Now We have a broad overview of a typical Jenkins pipeline. Let us get the actual Ansible deployment to work by:

1. Installing Ansible on Jenkins.

![image](https://github.com/user-attachments/assets/64c5c1b1-fc9f-4fbe-a9e8-a6a0d09a5790)

2. Installing Ansible plugin in Jenkins UI.
![image](https://github.com/user-attachments/assets/62764bfb-a61c-4322-970b-fd66988c3279)

Configuring the path on ansible on jenkins.. For that need to go manage Jenkins > Configure tools.

![image](https://github.com/user-attachments/assets/7904d1ab-bd73-422e-bb90-f7ad1717de98)


4. Creating Jenkinsfile from scratch. (Delete all you currently have in there and start all over to get Ansible to run successfully).

Deleted the all content of the jenkinsfile and Created the new one

![image](https://github.com/user-attachments/assets/dffbbe98-b2b4-4d8b-8c4e-a4dac2600dd0)


- Start writing it again. to do this, we can make use of pipeline syntax to ensure we get the exact command for what we intend to achieve. here is how the Jenkinsfile should look eventually .

```
pipeline {
    agent any

    environment {
        ANSIBLE_CONFIG = "${WORKSPACE}/deploy/ansible.cfg"
    }

    stages {
        stage('Initial cleanup') {
            steps {
                dir("${WORKSPACE}") {
                    deleteDir()
                }
            }
        }

        stage('Checkout SCM') {
            steps {
                git branch: 'main', url: 'https://github.com/ksal1235/ansible-project.git'
            }
        }

        stage('Prepare Ansible For Execution') {
            steps {
                sh 'echo ${WORKSPACE}'
                sh 'sed -i "3 a roles_path=${WORKSPACE}/roles" ${WORKSPACE}/deploy/ansible.cfg'
            }
        }

        stage('Test SSH Connections') {
            steps {
                script {
                    def allHosts = [
                        'ubuntu@172.31.8.66',
                        'ec2-user@172.31.5.211'
                    ]

                    sshagent(['private-key']) {
                        allHosts.each { host ->
                            sh "ssh -o StrictHostKeyChecking=no ${host} exit"
                        }
                    }
                }
            }
        }

        stage('Run Ansible playbook') {
            steps {
                sshagent(['private-key']) {
                    ansiblePlaybook(
                        become: true,
                        credentialsId: 'private-key',
                        disableHostKeyChecking: true,
                        installation: 'ansible',
                        inventory: '${WORKSPACE}/inventory/dev.yml',
                        playbook: '${WORKSPACE}/playbooks/site.yml'
                    )
                }
            }
        }

        stage('Clean Workspace after build') {
            steps {
                cleanWs(cleanWhenAborted: true, cleanWhenFailure: true, cleanWhenNotBuilt: true, cleanWhenUnstable: true, deleteDirs: true)
            }
        }
    }
}

```

![image](https://github.com/user-attachments/assets/d0e13305-4ece-443d-b72a-5abf3d955398)



# Note: Ensure that Ansible runs against the Dev environment successfully.

Possible errors to watch out for:

1. Ensure that the git module in Jenkinsfile is checking out SCM to main branch instead of master (GitHub has discontinued the use of Master due to Black Lives Matter. You can read more here)

2. Jenkins needs to export the ANSIBLE_CONFIG environment variable. You can put the .ansible.cfg file alongside Jenkinsfile in the deploy directory. This way, anyone can easily identify that everything in there relates to deployment. Then, using the Pipeline Syntax tool in Ansible, generate the syntax to create environment variables to set. https://wiki.jenkins.io/display/JENKINS/Building+a+software+project

Possible issues to watch out for when you implement this

1. Remember that ansible.cfg must be exported to environment variable so that Ansible knows where to find Roles. But because you will possibly run Jenkins from different git branches, the location of Ansible roles will change. Therefore, you must handle this dynamically. You can use Linux Stream Editor sed to update the section roles_path each time there is an execution. You may not have this issue if you run only from the main branch.

2. If you push new changes to Git so that Jenkins failure can be fixed. You might observe that your change may sometimes have no effect. Even though your change is the actual fix required. This can be because Jenkins did not download the latest code from GitHub. Ensure that you start the Jenkinsfile with a clean up step to always delete the previous workspace before running a new one. Sometimes you might need to login to the Jenkins Linux server to verify the files in the workspace to confirm that what you are actually expecting is there. Otherwise, you can spend hours trying to figure out why Jenkins is still failing, when you have pushed up possible changes to fix the error.

3. Another possible reason for Jenkins failure sometimes, is because you have indicated in the Jenkinsfile to check out the main git branch, and you are running a pipeline from another branch. So, always verify by logging onto the Jenkins box to check the workspace, and run git branch command to confirm that the branch you are expecting is there.




### To ensure jenkins properly connects to all servers, install another plugin called ssh agent.

![image](https://github.com/user-attachments/assets/c1161951-2964-4db5-a6f3-57be092c3606)


### Then go to manage jenkins > `credentials` > `global` > `add credentials`

Then follow the steps below:
- Kind: SSH Username with private key.
- Scope: Global (Jenkins, nodes, items, all child items, etc).
- ID: private-key (or any ID you prefer)
- Username: Leave it blank or set a default value (e.g., defaultuser) # This is because we are using servers of different username i.e ubbuntu and ec2-user. This value won’t be used because the actual usernames will be specified in the Ansible inventory file.
- Private Key: Enter the private key directly.

![image](https://github.com/user-attachments/assets/ab17bd04-9cee-4dcd-a313-28fcb94c2857)

#### Updating the site.yml file

Update inventory/dev.yml by specifying the private IP address of the servers

![image](https://github.com/user-attachments/assets/b8978a7c-8608-42d5-aa01-6b873a764e13)

Update the site.yml

![image](https://github.com/user-attachments/assets/75f1c1b6-c13a-402e-8696-c6f3754a83db)


After fixing all errors final result looks

![image](https://github.com/user-attachments/assets/e2e08aa7-5d09-4531-88e5-d9aa2b7dc5ab)


![image](https://github.com/user-attachments/assets/a40d0bfe-eef1-4e33-a73a-ac851963fc07)


![image](https://github.com/user-attachments/assets/be982f02-ed1d-46f1-b7bf-ec9ad8093d9e)


If everything goes well for you, it means, the Dev environment has an up-to-date configuration. But what if we need to deploy to other environments?

- Are we going to manually update the Jenkinsfile to point inventory to those environments? such as sit, uat, pentest, etc.
- Or do we need a dedicated git branch for each environment, and have the inventory part hard coded there.


Think about those for a minute and try to work out which one sounds more like a better solution.

Manually updating the Jenkinsfile is definitely not an option. And that should be obvious to you at this point. Because we try to automate things as much as possible.

What we will be doing is to parameterise the deployment. So that at the point of execution, the appropriate values are applied.


## Parameterizing Jenkinsfile For Ansible Deployment


To deploy to other environments, we will need to use parameters

1. Update sit inventory with new servers (inventory/sit.yml)

```
[tooling]
<SIT-Tooling-Web-Server-Private-IP-Address>

[todo]
<SIT-Todo-Web-Server-Private-IP-Address>

[nginx]
<SIT-Nginx-Private-IP-Address>

[db:vars]
ansible_user=ec2-user
ansible_python_interpreter=/usr/bin/python

[db]
<SIT-DB-Server-Private-IP-Address>

```

![image](https://github.com/user-attachments/assets/2bf82c36-ad64-42fa-a28e-f9736595da69)


2. Update Jenkinsfile to introduce parameterization. Below is just one parameter. It has a default value in case if no value is specified at execution. It also has a description so that everyone is aware of its purpose.


```
pipeline {
    agent any

    parameters {
      string(name: 'inventory', defaultValue: 'dev',  description: 'This is the inventory file for the environment to deploy
      configuration')
    }


```
![image](https://github.com/user-attachments/assets/05326e7a-86e9-4a2a-b5b7-3f9959cd31ea)

3. In the Ansible execution section, remove the hardcoded inventory/dev and replace with `${inventory} From now on, each time you hit on execute, it will expect an input.

![image](https://github.com/user-attachments/assets/4ebaf23b-7715-4d9c-b1df-b124c2b08e66)


4.  We can see we got build with parameters option on pipeline to execute So click on build with parameters button.
   
![image](https://github.com/user-attachments/assets/790d9660-5f56-473a-a129-07adea214422)

#### Notice that the default value loads up, but we can now specify which environment we want to deploy the configuration to. Simply type sit and hit Run

![image](https://github.com/user-attachments/assets/6590d11c-7d6d-492b-83dc-81ed1d7d9235)


Build got success for environemtn SIT ..

![image](https://github.com/user-attachments/assets/e68759b9-9343-480f-97df-f316ea36e016)


5. Add another parameter. This time, introduce tagging in Ansible. You can limit the Ansible execution to a specific role or playbook desired. Therefore, add an Ansible tag to run against webserver only. Test this locally first to get the experience. Once you understand this, update Jenkinsfile and run it from Jenkins.

- Update playbook/site.yml with tags

![image](https://github.com/user-attachments/assets/4bf1fb4a-ab0f-403a-bfbe-bd0e3cd400b8)

Add another parameter to the jenkinsfile. Name the parameter ansible_tags and the default value webserver

```  string(name: 'ansible_tags', defaultValue: 'webserver', description: 'Ansible tags to run specific roles or tasks') ```

![image](https://github.com/user-attachments/assets/b053eff7-b494-4cde-bf89-c8160f039c6f)


![image](https://github.com/user-attachments/assets/9fc2cbcd-353c-4575-b6cb-7613f8cdfa4b)

We can do build the pipeline through Blue Ocean..

![image](https://github.com/user-attachments/assets/edbaac17-497d-4e88-ac62-4c86fed99232)


![image](https://github.com/user-attachments/assets/acd74991-56d5-41fb-9bb2-84f692446a18)




## CI/CD Pipline for TODO Application:

We already have tooling website as a part of deployment through Ansible. Here we will introduce another PHP application to add to the list of software products we are managing in our infrastructure. The good thing with this particular application is that it has unit tests, and it is an ideal application to show an end-to-end CI/CD pipeline for a particular application.

Our goal here is to deploy the application onto servers directly from Artifactory rather than from git If you have not updated Ansible with an Artifactory role, simply use this guide to create an Ansible role for Artifactory (ignore the Nginx part). Configure Artifactory on Ubuntu 20.04 https://www.atlantic.net/dedicated-server-hosting/how-to-install-jfrog-artifactory-on-ubuntu-22-04/

```
/path/to/your/laravel/project
├── app
│   ├── Console
│   ├── Exceptions
│   ├── Http
│   │   ├── Controllers
│   │   ├── Middleware
│   ├── Models
│   ├── Providers
├── bootstrap
│   ├── cache
├── config
│   ├── app.php
│   ├── database.php
│   └── ...
├── database
│   ├── factories
│   ├── migrations
│   ├── seeders
├── public
│   ├── index.php
│   ├── css
│   ├── js
│   ├── ...
├── resources
│   ├── js
│   ├── lang
│   ├── views
│   └── ...
├── routes
│   ├── api.php
│   ├── channels.php
│   ├── console.php
│   ├── web.php
├── storage
│   ├── app
│   ├── framework
│   ├── logs
├── tests
│   ├── Feature
│   ├── Unit
├── vendor
├── .env
├── artisan
├── composer.json
├── composer.lock
├── package.json
├── phpunit.xml
└── webpack.mix.js

```

#### Prerequests  Make sure port 8082 is opened in artifactory server.

Adding the jfrog repo roles using following commond:

```
ansible-galaxy collection install jfrog.platform
```
![image](https://github.com/user-attachments/assets/1abf3f89-1941-4487-8e12-ee0d2c5e0577)

Update ```playbook/site.yml```

![image](https://github.com/user-attachments/assets/e4f4b969-25fa-475b-b570-c13dcd9fcae9)

![image](https://github.com/user-attachments/assets/deb4a876-1791-4cf4-a461-c8ea5c022dc3)

![image](https://github.com/user-attachments/assets/7e3beae6-e32a-4c76-9d5f-145b32b8d1cf)

![image](https://github.com/user-attachments/assets/d2fac930-2495-4f7b-9f50-4095eea86e6f)


Run the playbook against ci.yml to install jfrog artifactory

![image](https://github.com/user-attachments/assets/307daaa2-349a-4c5f-955b-1c9df038bf19)

![image](https://github.com/user-attachments/assets/c4837649-08e8-40a1-a1b1-d95bf8ee9f6a)




## Phase 1 - Prepare Jenkins

1. Fork repository To Own Github Account:

```
https://github.com/StegTechHub/php-todo/fork
```

![image](https://github.com/user-attachments/assets/3631b941-1728-425a-91be-637ff4daacff)

2. On you Jenkins server, install PHP, its dependencies and Composer tool (Feel free to do this manually at first, then update your Ansible accordingly later)

```
sudo apt install -y zip libapache2-mod-php phploc php-{xml,bcmath,bz2,intl,gd,mbstring,mysql,zip}
```
![image](https://github.com/user-attachments/assets/f0f56bee-ee9a-4bc8-a525-2c62c329ebad)

Install Composer Download the Installer:
```
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
```

Install Composer Globally

```
sudo php composer-setup.php --install-dir=/usr/local/bin --filename=composer
```
Remove the Installer

```
php -r "unlink('composer-setup.php');"
```

Verify Installation

```
php -v
```

```
composer -v
```

3. Install Jenkins plugins.

    1. Plot plugin.
    2. Artifactory plugin.


```
pipeline {
    agent any

  stages {

    stage("Initial cleanup") {
          steps {
            dir("${WORKSPACE}") {
              deleteDir()
            }
          }
        }

    stage('Checkout SCM') {
      steps {
            git branch: 'main', url: 'https://github.com/ksal1235/php-todo.git'
      }
    }

    stage('Prepare Dependencies') {
      steps {
             sh 'composer install'
             sh 'php artisan migrate'
             sh 'php artisan db:seed'
             sh 'php artisan key:generate'
      }
    }
  }

}
```

![image](https://github.com/user-attachments/assets/ff60a7b5-2092-4711-855a-77da0bd33b13)


