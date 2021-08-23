# Building a continuous integration pipeline

## Creating a Jenkins server using Ansible and CloudFormation

We are going to use Jenkins as our central system to run our continuous integration pipeline. With over 10 years of development, Jenkins has been the leading open source solution to practice continuous integration for a long time. 

Famous for its rich plugin ecosystem, Jenkins has recently gone through a major new release (Jenkins 2.0), which has put the spotlight on a number of very DevOps centric features including the ability to create natively delivery pipelines that can be checked in and version-controlled and better integration with source control system such as GitHub. 

### Creating the Ansible playbook for Jenkins
We are going to start by navigating to our ansible roles directory:

```sh
~ cd ansible/roles
```

We are now going to create our Jenkins role with the ansible-galaxy command:

```sh
~ ansible-galaxy init jenkins
```

We are now going to edit the task definition for this new role by editing the file jenkins/tasks/main.yml.

Open up the file with your favorite text editor.


Below the initial comment of the tasks file, add the following:

```yaml

- name: Import Jenkins GPG key
  rpm_key:
    state: present
    key: http://pkg.jenkins-ci.org/redhat/jenkins-ci.org.key
```

The next step will be to import the yum repository to our yum repository configuration (basically an entry in /etc/yum.repos.d ):

```yaml
- name: Add Jenkins repository
  yum_repository:
    name: jenkins
    description: jenkins repository
    baseurl: http://pkg.jenkins.io/redhat
    enabled: no
    gpgcheck: yes

```
We have reached the point where we can now use yum to install Jenkins. We will do that with the following call:

```yaml

- name: Install Jenkins
  yum:
    name: jenkins
    enablerepo: jenkins
    state: present # installed 
```

Since the jenkins repository is disabled by default, we are enabling it through the enablerepo flag for the execution of this yum command.

At this point, Jenkins will be installed. As a best practice, we will specify which version of Jenkins we want to install (in our case the current version is 2.45). 

We also want to start the service and have it enabled at the chkconfig level so that if the EC2 instance where jenkins is installed restarts, Jenkins will start automatically. We can do that using the service module. Add the following after the previous call:

```yaml
- name: Start Jenkins
  service:
    name: jenkins-2.45
    enabled: yes
    state: started

```
