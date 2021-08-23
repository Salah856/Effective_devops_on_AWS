# Building a continuous integration pipeline

## Creating a Jenkins server using Ansible and CloudFormation

We are going to use Jenkins as our central system to run our continuous integration pipeline. With over 10 years of development, Jenkins has been the leading open source solution to practice continuous integration for a long time. 

Famous for its rich plugin ecosystem, Jenkins has recently gone through a major new release (Jenkins 2.0), which has put the spotlight on a number of very DevOps centric features including the ability to create natively delivery pipelines that can be checked in and version-controlled and better integration with source control system such as GitHub. 

### Creating the Ansible playbook for Jenkins


