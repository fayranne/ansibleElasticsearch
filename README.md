# ansibleElasticsearch
Create AWS Ec2 Instance and install elasticsearch

In order to run this code you will need the following installed on your linux server:
   - ansible
   - boto3

You need to clone this repository down from git.
Inside the newly downloaded folder you will find:
  - DNU Folder -- this is my attempt at a cluster and mostly for discussion if time permits
  - elasticsearch folder -- these are the documents that are being pulled in from the s3 bucket in the json file (included for viewing purposes)
  - cloudformation.json
  - provisionAWS.yml

provisionAWS.yml needs to be in the same directory as cloudformation.json unless provisionAWS is updated to reflect a different location.

You will need to input the following criteria in the ProvisionAWS.yml file:
    - access_key: 
    - secret_key: 
    - keyName: 

Inside the cloned directory run:
      "ansible-playbook provisionAWS.yml"  (Include -vvv for verbose logging)

Once the playbook has completed you can ssh onto the instance and run the following command:
    "sudo curl -u elastic -k "https://localhost:9200/_cat/health"
	Password: "changeme"
	
Alternatively you could run:
    "sudo curl -u elastic -k "https://localhost:9200"
	Password: "changeme"
  
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _

*A description of your solution noting interesting choices you made and why you made them*
   
   In this solution I provide you with an ansible playbook and an AWS cloudformation template.  My playbook takes the provided variables and passes them to the cloudformation template for use.  In the template I am creating an IAM user and an access key in order to create a Bucket Policy which will allow me to access my s3 bucket where I am keeping another playbook as well as some files that are used in my playbook.  This is all being accessed within the ec2 instance with the user of the User Data property and cfn-init.  This play book handles the uninstalling of Java 7 and the installing of Java 8, as well as the installation of elasticsearch and it's plugins.  I chose to use the x-pack plugin in order to handle the creation of the elasticsearch user as well as to configure my ssl.  I created a ssl cert and key using openssl locally and chose to include it in the payload that is pulled in from s3.  I then point the x-pack to my files after moving them.  I also installed the discovery_ec2 plugin in order to allow the nodes in my cluster the ability to see each other.

Pain Points:
  - For the interest of time I did not encrypt or decrypt the ssl cert or key.  This is a security issue and should be fixed.
  In oder to encrypt I would need to use gpg and use symmetric encryption.  The key of which I would need to pass to my cloudformation 
  in order to use.
  - I have chosen to use the default VPC as opposed to creating a variable and passing a VPC and a subnet to my cloudformation.
  This is less than ideal because it is the default one therefore not necessarily complying to every security aspect you may want.
  To not use the defualt VPC you could pass a VPC ID and subnet ID and and configure those onto your Security Group(s) and Instance(s),
  respectively.
  - I was unable to create a cluster of nodes.  I am not entirely sure and have actually included the AWS Cloudformation Template that I
  attempted to use in order to make a cluster of 3 nodes. I'm not sure why it errored out and have also included the trace I got from 
  Ansible when doing the call (In case we have time to talk about it as I am not sure why it isn't working).  For the sake of time 
  my solution only successfully brings up one EC2 Instance. In addition to the changes in the template I would also need to make 
  a change to my elasticsearch.yml file specifying "discovery.zen.minimum_master_nodes: 2" (for a cluster of 3 nodes).
  - I acknowledge that I could have organized my playbooks better (i.e. Created a configuration file that would be pulled into the first 
  playbook for use, as well as creating roles rather than just a list of tasks in the second playbook )
  
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _

*A list of resources you consulted to accomplish the exercise*
  - Google
  - http://docs.ansible.com/ansible/index.html
  - http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/Welcome.html
  - https://www.elastic.co/guide/en/elasticsearch/reference/current/rpm.html
  - https://www.gnupg.org/gph/en/manual/x110.html

_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _
  
*Feedback on the exercise and some information about how long you spent on it*

   I found this exercise particularly intriguing as it allowed me the same sort of organization and sort of object oriented thinking
I would use regularly coding, but with a more Systems Engineering sort of aspect -- where I am no longer merely dealing with data
but with actual servers and objects.  It required me to learn and understand more about the hardware and networking behind an application rather than the application itself. It also provided me to opportunity to learn more about conifuration managment tools (primarily ansible) and their application.  I spent about a week and a half looking at this problem.
