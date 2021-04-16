# Project1
## Statement 1: Using any IaC (Terraform/ARM/CFT) create the same infra which is depicted above. In this you also need to use the userdata to install the Jenkins and Ansible/Chef/Puppet/etc.
    

---------------------------------------------------------------------------------------------------------------
## Steps Are as follows
* Step 1) create a git repo in gitHub as project1 ( see the code in this repo https://github.com/suchita2007/Project1.git)
* step 2) get the clone of a project on local
* step 3) create the develop branch
* step 4) Create feature branch for dev.
* Step 5) Created VPC CF template for the problem and infrastucture given in the statement with VPC CIDR range as 172.20.0.0/16 and Public Subnet CIDR range 172.20.10.0/24 Private CIDR Range 172.20.20.0/24 Export the value for the VPC,SUBNET Value for Ec2 creation in another stack under same VPC
* Step 6) Check the CF (Build/Test/Run) using Dev-Feature Branch environment
* Step 7) If it is executing Properly Push on Developer Branch in git Hub
* Step 8) Create the Ec2 Stack for Private and Public Ec2 instance, Name of public instance is jenkins-Ansible-server and the Name of Private Ec2-Instance is Docker-Tomcat-server Instance.
* Step 9) Install Jenkins and Ansible on Public Instance. Install Docker and Tomcat on Private instance using User Data
* step 10) Check the CF (Build/Test/Run) using Dev-Feature Branch environment
* step 11) If it is executing Properly Push on Developer Branch in git Hub
* step 12) Merge the project in develop branch and build/test/run project.
---------------------------------------------------------------------------------------------------------------
    

## create a pipiline for the VPC-creation and Ec2-Creation
* Navigate the code pipline -->Choose pipeline setting--> name of pipleline select the Source provider--> as code commit-->skip Add build stage--> and upload the template 01_VPC_creation.yml
* select deployment as cloud formation -->Deployment provider-> Region-> action mode as Create or update stack--> stack name --> template-->artifact name as SourceArtifact -->File name 01_VPC_creation.yml  ---> role name as default -->create pipeline--> Create the VPC stack first
* Then after creation of 01_VPC_creation upload the EC2 template 01_EC2_creation_In_VPC.yml create pipeline -->code pipline -->Choose pipeline setting--> name of pipleline select the Source provider--> as code commit-->skip Add build stage--> and upload the template 01_EC2_creation_In_VPC.yml
* while creating this template pass stack parameter name as VPC stack name--> select deployment as cloud formation -->Deployment provider-> Region-> action mode as Create or update stack--> stack name --> template-->artifact name as SourceArtifact -->File name 01_EC2_creation_In_VPC.yml  ---> role name as default -->create pipeline--> Create the VPC stack first.  
    
-------------------------------------------------------------------------------------------------------------------
# Statement 2: From Jenkins create the artefact as Dockerfile/War/Jar file and store it local (target/*.jar) Statement 3: Now using the ansible/Puppet/Chef, deploy the same artefact (target/*.jar) to the box which is in the private subnet.
## steps after ec2-server launch (On public EC2 Jenkins and Ansible already installed and on private Docker already install using User data while launching the Ec2-server)
* step 1) Ec2-server(public) lanch and copy past the such123.pem key here to login to private instance using bastion host
* step 2) on jenkins server login to server install java and maven as well as git on this server
* step 3) login to jenkins using public ip
* step 4) create pipeline job --> Select Git as repository where code is stored (Dockerfile, Ansible File (ansible1.yml),jenkinsfile and SpringBoot code is stored)  
* step 5) select pipeline script first step is Build --> Give the git repository Url and branch name((git branch: 'main', url: 'https://github.com/suchita2007/spring-petclinic.git')) and build as (sh "./mvnw package") this will build .jar file in target folder and artifact the file as target/*.jar
* step 6) second step is Build Docker image as ( sh 'docker build -t suchita2007/spring-boot .')
* step 7) third stage as Docker push to Ecr using with credential create login and passward for DockerHub where you want to store your Image   
  * withCredentials([string(credentialsId: 'DOCKER_HUB_PASSWORD2', variable: 'DOCKER_HUB_PASSWORD2')]) {
                              sh 'docker login -u suchita2007 -p ${DOCKER_HUB_PASSWORD2}'
* step 8) push the image ( sh 'docker push suchita2007/spring-boot') 
* step 9) Deploy image using the ansible for this install ansible plugin and setup the ansible ssh-key pair authentication between public and private instance.
* step 10) create the credential for this using dev-server1 as name of server  
    * stage('Docker Deploy'){
            steps{
              ansiblePlaybook credentialsId: 'dev-server1', disableHostKeyChecking: true, installation: 'ansible', inventory: 'inventory', playbook: 'ansible1.yml'
            }
* ansible1.yml playbook is written in the repository where first task is install python then docker, Install docker-py python module and Start the container.
* then check the image and container which is running using docker image and Docker ps command 
    
-------------------------------------------------------------------------------------------------------------------
# converting Same project to AWS CICD pending using buildspec.yml / appsec.yml file.