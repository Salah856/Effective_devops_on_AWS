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

We built a role that will allow us to install jenkins . We will want to create a new EC2 instance and install Jenkins on it with the end goal of testing our nodejs code on the instance. In order to be able to do that, the Jenkins host will need to also have node and npm installed.

In the root directory of our ansible repository, create the playbook file. The filename is jenkins.yml and it should look like this:

```yaml
---
- hosts: "{{ target | default('localhost') }}"
  become: yes
  roles:
    - jenkins
    - nodejs

```

Our role is now complete, so we can commit our new role and push it to GitHub. Following the best practices described previously, we will start by creating a new branch, then add our files, then commit and finally push the changes:

```sh
$ git checkout -b jenkins
$ git add jenkins.yml roles/jenkins
$ git commit -m "Adding a Jenkins playbook and role"
$ git push

 ```
 
From there, submit a pull request inside GitHub and merge the branch back to master, and get back to the master branch:
```sh
$ git checkout master
```
In a real life situation, you likely also want to periodically run to retrieve other developers changes:

```sh
$ git pull
```

We can now create our CloudFormation template in order to call that role.

#### Creating the CloudFormation template

We are first going to duplicate the Python script. Go to your EffectiveDevOpsTemplates directory where you have your troposphere templates and then clone the ansiblebase-cf-template.py file:

```sh
$ cp ansiblebase-cf-template.py jenkins-cf-template.py
```

We are now going to edit jenkins-cf-template.py. The first two changes we will do are the application name and port. Jenkins runs by default on TCP/8080 :

```py
ApplicationName = "jenkins"
ApplicationPort = "8080"
```

Then installing awscs: 

```sh

$ pip install awacs
```

When you are using EC2 instances, the instance profile feature provided lets you specify an IAM role to your instance. In other words, we can assign IAM permissions directly to EC2 instances without having to use access keys and secret access keys.

```py

from troposphere.iam import (
    InstanceProfile,
    PolicyType as IAMPolicy,
    Role,
)

from awacs.aws import (
    Action,
    Allow,
    Policy,
    Principal,
    Statement,
)

from awacs.sts import AssumeRole

```

Then, in between the instantiation of the variables ud and the creation of the instance, we are going to create and add our role resource to the template as such:

```py
t.add_resource(Role(
    "Role",
    AssumeRolePolicyDocument=Policy(
      Statement=[
          Statement(
            Effect=Allow,
            Action=[AssumeRole],
            Principal=Principal("Service", ["ec2.amazonaws.com"])
        )
      ]
   )
  )
)


```
