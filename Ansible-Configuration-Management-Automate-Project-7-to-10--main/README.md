# Ansible-Configuration-Management-Automate-Project-7-to-10-

## Step 1 - Install and Configure Ansible on EC2 Instance.

1. Update the Name tag on your Jenkins EC2 Instance to Jenkins-Ansible. We will use this server to run playbooks.

![image](https://github.com/user-attachments/assets/e455b81d-63c2-47f3-acad-bd1e2d8db77f)

2. In  GitHub account create a new repository and name it ansible-config-mgt.

![image](https://github.com/user-attachments/assets/9ab92ac1-f510-4c6a-9f11-a8042a745a99)

3. Install Ansible.

```
sudo apt install ansible -y
```
![image](https://github.com/user-attachments/assets/b66dff6e-a9a5-43b0-8d41-69f838ac49ed)


Check your Ansible version by running ansible --version

![image](https://github.com/user-attachments/assets/85eb5fc0-511e-4798-b35a-031a8f2a6ed7)


4. Configure Jenkins build job to archive repository content every time you change it - this will solidify Jenkins configuration skills acquired in Project 9.

Create a new Freestyle project 'ansible' in Jenkins and point it to 'ansible-config-mgt' repository.

![image](https://github.com/user-attachments/assets/a92608f7-ce23-4d4a-b956-4cfa68655c37)


![image](https://github.com/user-attachments/assets/2299663e-3269-450a-a259-342fffbffaa6)


Configure a webhook in GitHub and set the webhook to trigger ansible build.

![image](https://github.com/user-attachments/assets/47ab44d7-11c6-4d2f-a4f5-05d485d559dd)


Configure a Post-build job to save all (**) files, like you did it in Project 9.
![image](https://github.com/user-attachments/assets/a5aabeab-7da0-4f48-8df5-4ad1e0a32ad6)

5. Test setup by making some change in README.md file in master branch and make sure that builds starts automatically and Jenkins saves the files (build artifacts) in following folder..

```
ls /var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/
```

![image](https://github.com/user-attachments/assets/ff06f4b7-c2c7-411e-83db-4303eabbac20)
#### Note: Trigger Jenkins project execution only for main (or master) branch.

![image](https://github.com/user-attachments/assets/ff0ef5d0-039a-4a78-996d-5675d644c9e8)

Tip: Every time we stop/start.. Jenkins-Ansible server - you have to reconfigure GitHub webhook to a new IP address, in order to avoid it, it makes sense to allocate an Elastic IP to your Jenkins-Ansible server (we have done it before to your LB server in Project 10). Note that Elastic IP is free only when it is being allocated to an EC2 Instance, so do not forget to release Elastic IP once you terminate your EC2 Instance.

## Step 2 - Prepare your development environment using Visual Studio Code.

1. Installing the Visual Studio Code (VSC),

2. Clone down your ansible-config-mgt repo to your Jenkins-Ansible instance.

```
Clone down your ansible-config-mgt repo to your Jenkins-Ansible instance
```

## Step 3 - Begin Ansible Development.

1. In your ansible-config-mgt GitHub repository, create a new branch that will be used for development of a new feature.

Created the feature branch from main branch 'feature/ansible-1' 
![image](https://github.com/user-attachments/assets/53814cf0-d755-4320-ab69-5da86dc97eed)


To check the newly created branch locally.
```
git branch -r
```
![image](https://github.com/user-attachments/assets/1b716888-af42-4d77-b30d-48b8abe3a6d8)



2. Checkout the newly created feature branch to  local machine and start building code and directory structure.

````
git checkout feature/ansible-1
git branch
````
![image](https://github.com/user-attachments/assets/c37143eb-9bc0-4c2c-81df-f310acab4df3)


3. Create a directory and name it playbooks - it will be used to store all playbook files.

```
mkdir playbooks
```

4. Create a directory and name it inventory - it will be used to keep your hosts organised.

```
mkdir inventory
```

![image](https://github.com/user-attachments/assets/8bdcf426-ed45-423a-b87f-667a417ae5b4)



5. Within the playbooks folder, create first playbook, and name it common.yml


6. Within the inventory folder, create an inventory file () for each environment (Development, Staging Testing and Production) dev, staging, uat, and prod respectively. These inventory files use .ini languages style to configure Ansible hosts.

![image](https://github.com/user-attachments/assets/a4d3b646-4bdb-43e5-83e5-3d1060aa4f0c)



## Step 4 - Set up an Ansible Inventory.

An Ansible inventory file defines the hosts and groups of hosts upon which commands, modules, and tasks in a playbook operate. Since our intention is to execute Linux commands on remote hosts, and ensure that it is the intended configuration on a particular server that occurs. It is important to have a way to organize our hosts in such an Inventory.
Save the below inventory structure in the inventory/dev file to start configuring development servers. Ensure to replace the IP addresses according to your own setup.

Note: Ansible uses TCP port 22 by default, which means it needs to ssh into target servers from Jenkins-Ansible host - for this you can implement the concept of ssh-agent. Now you need to import key into ssh-agent:


```
eval `ssh-agent -s`
ssh-add <path-to-private-key>
```

![image](https://github.com/user-attachments/assets/14a7913e-611c-4450-8653-6580345405bc)


Confirm the key has been added with the command below, we should see the name of key.

```
ssh-add -l
```
![image](https://github.com/user-attachments/assets/c1c3cd45-21b4-40cf-896d-6ea108a5b4bd)


Now, ssh into  Jenkins-Ansible server using ssh-agent

```
ssh -A ubuntu@public-ip
```
![image](https://github.com/user-attachments/assets/28dede8f-79fa-4651-a2f4-0ca17055dad0)


Update your inventory/dev.yml file with this snippet of code:

```
[nfs]
<NFS-Server-Private-IP-Address> ansible_ssh_user=ec2-user

[webservers]
<Web-Server1-Private-IP-Address> ansible_ssh_user=ec2-user
<Web-Server2-Private-IP-Address> ansible_ssh_user=ec2-user

[db]
<Database-Private-IP-Address> ansible_ssh_user=ec2-user

[lb]
<Load-Balancer-Private-IP-Address> ansible_ssh_user=ubuntu
```

## Step 5 - Create a Common Playbook.

- It is time to start giving Ansible the instructions on what need to be performed on all servers listed in inventory/dev.
- In common.yml playbook We will write configuration for repeatable, re-usable, and multi-machine tasks that is common to systems within the infrastructure.
- Update your playbooks/common.yml file with following code

```
---
- name: update web, nfs and db servers
  hosts: webservers, nfs, db
  become: yes
  tasks:
    - name: ensure wireshark is at the latest version
      yum:
        name: wireshark
        state: latest

- name: update LB server
  hosts: lb
  become: yes
  tasks:
    - name: Update apt repo
      apt:
        update_cache: yes

    - name: ensure wireshark is at the latest version
      apt:
        name: wireshark
        state: latest

```

Examine the code above and try to make sense out of it. This playbook is divided into two parts, each of them is intended to perform the same task: install wireshark utility (or make sure it is updated to the latest version) on  RHEL 9 and Ubuntu servers. It uses root user to perform this task and respective package manager: yum for RHEL 9 and apt for Ubuntu.

Feel free to update this playbook with following tasks:

- Create a directory and a file inside it.
- Change timezone on all servers.
- Run some shell script.

```
---
---
- name: update web, nfs and db servers
  hosts: webservers, nfs
  remote_user: ec2-user
  become: yes
  become_user: root
  tasks:
    - name: ensure wireshark is at the latest version
      yum:
        name: wireshark
        state: latest

- name: update LB server
  hosts: lb, db
  remote_user: ubuntu
  become: yes
  become_user: root
  tasks:
    - name: Update apt repo
      apt:
        update_cache: yes

    - name: ensure wireshark is at the latest version
      apt:
        name: wireshark
        state: latest

```
## Step 6 - Update GIT with the latest code.

1. Updating the repository.
```
git inti
git add .
git commit -m "commit message"
git push origin feature/ansible-1
```
![image](https://github.com/user-attachments/assets/b5d9472a-0e20-40f8-9abc-ee1014093b8e)



2. Create a Pull Request (PR).

![image](https://github.com/user-attachments/assets/ad4d08df-20d2-448b-96a9-867453bbfa57)

wE CAN NOTE HERE THAT WHATEVER THE CONFIGURATION WE HAD DONE ON FEATURE BRANCH IN GOT UPDATE IN MAIN BRANCH WE CAN SEE ON MAIN BRANCH.

3. Head back on your terminal, checkout from the feature branch into the master, and pull down the latest changes.

![image](https://github.com/user-attachments/assets/5bd884de-a80f-4ea1-950d-aa93ea6a3983)


Due to github Webhook It will goint to start build the jenkins job if there is any changes detect on github.
![image](https://github.com/user-attachments/assets/0cc9efc1-7f24-475d-b5ed-c7f9461009bc)


Once code changes appear in main branch - Jenkins will do its job and save all the files (build artifacts) to /var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/ directory on Jenkins-Ansible server.

![image](https://github.com/user-attachments/assets/faeac324-5789-42e4-a504-5d24ccce26ec)


## Step 7 - Run the first Ansible test.

Now, it is time to execute ansible-playbook command and verify if your playbook actually works:

Use sudo 
```
sudo ansible-playbook -i inventory/dev.yml playbooks/common.yml
```
![image](https://github.com/user-attachments/assets/0bf06217-c907-48b4-a957-0e641689c788)


Verify that wire shark is running in each of the servers by running

```
      wireshark --version
```
NFS-SERVER

![image](https://github.com/user-attachments/assets/6c78954e-6167-4bbe-9544-e4328df8ac8f)


db
![image](https://github.com/user-attachments/assets/9d7abcd3-64c7-4cfd-ac79-289ed244b686)



loadbalancer

![image](https://github.com/user-attachments/assets/040bd7bd-6f2b-4005-a293-df80f30ed3f7)


Webserver1

![image](https://github.com/user-attachments/assets/3fc135eb-0847-4bc5-b15c-6ef895e70c21)


Webserver2

![Uploading image.png…]()



We have Implemented

![image](https://github.com/user-attachments/assets/328f57c5-3912-4107-9275-67594e3e7f34)


## Conclusion:

The Ansible Configuration Management Automation Project successfully demonstrated the power and versatility of Ansible in automating various tasks across multiple servers. By leveraging Ansible's playbooks and roles, we were able to streamline the configuration management process, ensuring consistent and repeatable deployments.

Throughout the project, we explored various aspects of Ansible, including ad-hoc commands, playbooks, roles, and inventory management. We learned how to automate software installations, configure services, manage files and directories, and handle system-level tasks efficiently.

One of the key advantages of using Ansible was its agentless architecture, which allowed us to manage remote systems without the need for additional software installations on the target machines. This simplified the deployment process and reduced the overall management overhead.

The project also highlighted the importance of version control and collaboration in configuration management. By utilizing Git and GitHub, we were able to track changes, collaborate with team members, and maintain a centralized repository for our Ansible code.

Furthermore, the project emphasized the significance of documentation and best practices. We learned how to write clear and concise playbooks, leverage Ansible's built-in modules, and follow industry-standard conventions for better maintainability and scalability.

Overall, the Ansible Configuration Management Automation Project provided valuable hands-on experience in leveraging Ansible for automating various tasks across multiple servers. The skills and knowledge gained from this project will be invaluable in streamlining infrastructure management, reducing manual efforts, and ensuring consistent and reliable deployments in real-world scenarios.
