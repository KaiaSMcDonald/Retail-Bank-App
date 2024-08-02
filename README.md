# Deploying a Retail Bank Application using AWS Elastic Beanstalk <br>
## Purpose <br>
<p> This project focused on and highlighted how Jenkins can be utilized to build and test code in addition to how AWS Elastic Beanstalk can deploy and host my application. AWS Elastic Beanstalk is a Platform as a Service (PaaS) that enables someone to manage, deploy, and scale web applications and services in a simplified way. This process eliminates having to scale and monitor a web application manually. Jenkins is a tool that supports the CI/CD pipeline by automating the stages of software development, such as building, testing, and deploying. The combination of AWS Elastic Beanstalk and Jenkins will help deploy an application and allow it to be prepared for customers. </p> <br>
<p> The steps displayed down below showcase what was done to successfully run the web application.</p> 

## Steps <br>
1. Cloning the repository to a personal repository on my Github account
By doing this step we are able to make contributions and customize without altering the original repository.
- First, on the EC2 instance create a new directory using `mkdir` command and then `cd` so you are in the directory you just created.
- Next, run 'git init', following that check which branch you are in within you directory using `git branch`
- After, run `git pull` plus the url of the original repository you are trying to add to your personal repository
- Following that, run `git remote add origin` plus the url of the personal repository you created in your Github account
- Lastly, run `git branch -M main` to rename the branch you are in, then run `git push -u origin main` <br> <br>
These steps will successfully allow you to take the original repository and clone it to your personal repository on you Github account 
  
2. Create EC2 Instance to host Jenkins
- Create the EC2 Instance within the AWS console
- Set up security groups rules
  * 1st one is "type-SSH" the port would be 22
  * 2nd one is "type-HTTP" the port would be 80
  * 3rd one is "type-Custome TCP" the port would be 8080 - this is specifically for when you are installing Jenkins

3. Installing Jenkins to the Jenkins server within the EC2 Instance <br>
   The following commands are needed to install Jenkins:
   ```
    $sudo apt update && sudo apt install fontconfig openjdk-17-jre software-properties-common && sudo add-apt-repository ppa:deadsnakes/ppa && sudo apt install python3.7 python3.7-venv
   
    $sudo wget -O /usr/share/keyrings/jenkins-keyring.asc https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
   
    $echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc]" https://pkg.jenkins.io/debian-stable binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
   
    $sudo apt-get update 
    $sudo apt-get install jenkins 
    $sudo systemctl start jenkins 
    $sudo systemctl status jenkins 

4. Connect to Jenkins server and Create Multibranch Pipeline
   - Connected to Jenkins server through web browser (in the web browser use Public IP and port 8080)
   - Created an Admin user account
   - Generated a Multibranch Pipeline withiin Jenkins
   - Connect my Github repository to Jenkins (in order to create a link between the two)
   - Begin the build
   
5. Jenkins Build and Test
   There are three stages in total the Checkout SCM, Build, and Test.
   - The Checkout SCM stage is cloning and colleccting the git repository to start the build.
   - The Build stage is where all the dependencies needed to test the logical code are downloaded and installed.
   - The test stage ensures that all the functions and features for the application are working correctly.

   ![Screenshot 2024-08-01](https://github.com/KaiaSMcDonald/Retail-Bank-App/blob/main/Screenshot%202024-08-01%20at%203.35.47%20PM.png) <br>
This is what the page looks like showing that code successfully went through the Chechout SCM stage

   ![Screenshot 2024-08-01](https://github.com/KaiaSMcDonald/Retail-Bank-App/blob/main/Screenshot%202024-08-01%20at%203.36.56%20PM.png) <br>
This is what the page looks like showing that the code successfully went through the Build stage

   ![Screenshot 2024-08-01](https://github.com/KaiaSMcDonald/Retail-Bank-App/blob/main/Screenshot%202024-08-01%20at%203.37.30%20PM.png) <br>
   This is what the page looks like showing that the code successfully went through the Test stage

   ![Screenshot 2024-08-01](https://github.com/KaiaSMcDonald/Retail-Bank-App/blob/main/Screenshot%202024-08-01%20at%2012.52.27%20AM.png)
   This shows that the code went through all the stages successfully


7. Deploy Retail Bank Application using AWS Elastic Beanstalk
   - First I needed to create service roles and an EC2 for my AWS Elastic Beanstalk environment. <br>
     The purpose of the service roles is to set the permissions needed to properly manage the application on my behalf. The service roles I added are as follows:
     *aws-elasticbeanstalk-service-role
     *aws-elasticbeanstalkwebtier
     *awselasticbeanstalkworkertier
     *awselasticbeanstalkmulticontainerdocker
* Elastic-EC2 is also added to enable the EC2 to call all the AWS servics

  - After setting all of my service roles, I then will need to create an Elastic Beanstalk Environment which is where my application will be hosted on. <br>
  Down below are the steps to the Elastic Beanstalk Environment Configuration:
      - Choose "Web Server Environment"
      - Enter Application name " In my case I use 'RetailBankApp'
      - For the Managed Platform section choose 'Python 3.7'
      - Upload the code (which is the zip file created once you successfully went through all the build stages in Jenkins)
      - For the instance configuartion portion the presets should be 'Single instance(free tier eligible)'
      - Choose default VPC and the correct subnet ( In my case I chose us-east-1a for the subnet)
      - Choose 'General Purpose(SSD) and the 'Root volume type' should be set to 10GB
      - Choose <strong> ONLY </strong> 't3.micro' for the instance types
      - For Basic Health Monitoring, de-select Managed Monitoring option (if not you will recieve a error that prohibits you from reaching the review page and submitting)
      - After pressing submit the environment will be built and ready for use. There will be domain link available that will allow you to access the web application. <br>

## System Design Diagram <br>

![Screenshot 2024-08-02](https://github.com/KaiaSMcDonald/Retail-Bank-App/blob/main/Screenshot%202024-08-01%20at%204.27.53%20PM.png) <br>

This system design diagram displays the environment that host the application. The environment is the orange rounded rectangle. Once you create a environment AWS Elastic Beanstalk will provide the resources needed to run the application.The resources provided include 1 elastic load balancer, auto scaling group, and the EC2 instances. I included Route 53 in the diagram because it connects user requests to application that is running on AWS. It behaves like a DNS which provides the IP address which is crucial part in getting and delivery the response to the requests.The load balancer is a part of the auto scaling group meaning it helps with making adjustments to accomodate the amount of traffic the web servers recieve. Each component of diagram is essential to making sure that everything runs smoothly and safely on the web application without using a lot of manual power. <br>


## Issues and Troubleshooting <br>

Issue #1:
At the end of creating the build in Jenkins I discovered that specifically at the build stage the code failed.The error message that displayed stated that python 3.7 couldn't be found. Upon that realization I decided that my step would be installing Python 3.7. However I discovered an easier resolution which was using the `sudo apt update && sudo apt install fontconfig openjdk-17-jre software-properties-common && sudo add-apt-repository ppa:deadsnakes/ppa && sudo apt install python3.7 python3.7-venv` command which made sure python was up to date and I didn't need any upgrades that will interfere with completing all the build stages. <br>







Issue #2:
At the end of creating my environment I realized that the link provided in the Domain kept on giving me an 502 error. This issue was based on the zip file I uploaded when creating the environment. The resolution is to create a source bundle which would allow me to deploy an application on AWS Elastic Beanstalk. In order to this I selected all the items within the zip file and compressed them. Once I compressed those items I recieved a Archive.zip. This file is what I uploaded into my environment and allowed the link to take to the Retail Bank App.<br>








## Optimization <br>

What are the benefits of using managed services for cloud infrastructure
1. Scalability and Flexibilty: Resources can be easily scaled up or down to adjust to the demand. Also if the business needs changes these services can conform to those needs without making major changes to the infrastructure.
2. Improved security: Managed services many times include many security features such as identity and access management and encryption.
3. Proactive monitoring: When using managed services there is continuous monitoring and management of the infrastructure. Therefore any issues were to arise it will be quickly detected resolved before those issues have a major impact. This also eliminates the need manually monitor and manage the infrastructure which can be costly and time consuming.<br>

What are some issues that a retail bank would face choosing this method of deployment and how would you address/resolve them?
1. Performance Issues: In regards to Banking applications it is essential for them to have high performance and low latency. However a retail bank application can experience performance and latency issues if the company’s database isn't configured to deal with a increase in transactions. This particular issue will lead to slow reponse times.
Solution: Caching- if caching mechanisms are utilized such Amazon ElastiCache latency will decrease and response times will improve. <br>

2. Data Security and Privacy: Banking data is extremely sensitive and should remain confidential and secure.Therefore it is very important to have the highest security measure to avoid potential breaches and data leaks from messing with the integrity of that data. There needs to be a tool that protects this application from cybersecurity attacks like DDos.<br>
Solution: Implement AWS Web Application Firewall(WAF) and AWS sheild specifically for DDos protection and sheild the application from any cyber attacks.

What are other disadvantages of using elastic beanstalk or similar managed services for deploying applications?
1. Limitation with Customization: Managed services only provide standardized solution so business face restrictions when they tr to tailor solutions to align with their business needs.
2. Learning curve adopting new managed service: When introducing a new managed service some people within the business may be unfamiliar and will require time and money to train staff so they have a understanding on how to use and manage these services.
3. Vendor Lock-In: Relying heavily on a particular cloud provider's managed service can introduce the issue of vendor lock in, so in a scenario when a business wants to switch providers or adopt multi-cloud it will be very difficult to make that transition. <br>

## Conclusion <br>

This project provides a great opportunity to understand the importance of Jenkins and AWS Elastic Beanstalk when deploying an application. While also exposing some of the issues that can arise by making small mistakes when uploading code, creating an environment or even cloning a repository. Overall developing solutions that incorporates automation is extremely beneficial because it helps improve accuracy and decrease deployment time which are two very key components for a business.













    















































# Kura Labs Cohort 5- Deployment Workload 1
## Intro to CI/CD

Welcome to Deployment Workload 1!  By now you’ve learned about system designs and the CI/CD Pipeline.  Let’s start putting it all together and see it in action.  

Be sure to document each step in the process and explain WHY each step is important to the pipeline.

## Instructions

1. Clone this repository to your GitHub account
2. Create an EC2

	a. Follow document: [AWS EC2 Quickstart Guide](https://github.com/kura-labs-org/AWS-EC2-Quick-Start-Guide/blob/main/AWS%20EC2%20Quick%20Start%20Guide.pdf) if needed
3. Install Jenkins onto the EC2

	a. Connect to the EC2 terminal

 	b. Enter the following commands to install Jenkins:

```
    $sudo apt update && sudo apt install fontconfig openjdk-17-jre software-properties-common && sudo add-apt-repository ppa:deadsnakes/ppa && sudo apt install python3.7 python3.7-venv
    $sudo wget -O /usr/share/keyrings/jenkins-keyring.asc https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
    $echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc]" https://pkg.jenkins.io/debian-stable binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
    $sudo apt-get update
    $sudo apt-get install jenkins
    $sudo systemctl start jenkins
    $sudo systemctl status jenkins

```

If successful, the last command should show the Jenkins service “active (running)”

4. Log into Jenkins

	a. Enter initial admin password

	b. Install suggested plugins

	c. Create first admin user

5. Create a Multi-Branch pipeline

	a. Click on “New Item” in the menu on the left of the page

	b. Enter a name for your pipeline
  
    c. Select “Multibranch Pipeline”
  
    d. Under “Branch Sources”, click “Add source” and select “GitHub”
  
    e. Click “+ Add” and select “Jenkins”
  
    f. Make sure “Kind” reads “Username and password”

    g. Under “Username”, enter your GitHub username

    h. Under “Password” ,enter your GitHub personal access token

To get the GitHub personal access token, first log into GitHub and click on your profile icon on the top right of the page.

i. On the dropdown menu, click on “Settings”

ii. Click on “<> Developer settings at the bottom of the menu on the left of the page

iii. Click on “Personal access tokens” on the menu on the left of the page and select “Tokens (classic)”

iv. Click “Generate new token” and select the classic option

v. Set an expiration date and then select the following "scopes": repo, admin:repo_hook

This token can only be viewed ONCE! Make sure you enter the token properly (or save it) before leaving the page otherwise a new token must be generated!

6. Connect GitHub repository to Jenkins

	a. Enter the repository HTTPS URL and click "Validate"
  
	b. Make sure that the "Build Configuration" section says "Mode: by Jenkinsfile" and "Script Path: Jenkinsfile"
  
	c. Click "Save" and a build should start automatically

Did the build stages successfully complete? If not, why? How did you resolve the issue?  What did each stage do?

7. After successfully completing the build (provide screenshot of successful build in documentation), download the contents of the repository (the one in your personal GitHub NOT the kuralabs repo!) and upload a zip file of the application it to AWS Elastic Beanstalk.
  
	a. First, follow the instructions in this [LINK](https://scribehow.com/shared/How_to_Create_an_AWS_IAM_Role_for_Elastic_Beanstalk_and_EC2__kTg4B7zRRxCp-aYTJc-WLg) for "How to Create an AWS IAM Role for Elastic Beanstalk and EC2" and create the two IAM roles as specified.

    b. Navigate to the AWS Elastic Beanstalk console page

    c. Navigate to the "Environments" page on the left side menu and click on "Create Environment"

    d. Create a "Web server environment" and enter the an Application name (Environment name should auto populate after that)

    e. Choose "Python 3.7" as the "Managed platform"

    f. "Upload your code" by choosing a "local file" and select the zipped application files you created earlier.

    g. Under "Presets", make sure that "Single instance (free tier eligible) is selected and then click "Next"

    h. Select the "Service role" and "EC2 profile" in the appropriate drop down menus and then click "Next"

    i. Select the default VPC and Subnet "us-east-1a" and then click "Next"

    j. Select "General Purpose (SSD) for "Root volume type" and assign it 10 GB.

    k. Ensure that "Single instance" is selected for the "Environment type" and that ONLY "t3.micro" is selected for instance types (remove all others if present) and then click "Next"

    l. Select 'BASIC' health reporting under the monitoring section. NOT "ENHANCED"!

    m. Continue to the "Review" page and then click "Submit".

    n. When the "environment is successfully launched", click on the link provided in the "Domain" and confirm that the application has deployed!

8. Document! All projects have documentation so that others can read and understand what was done and how it was done. Create a README.md file in your repository that describes:

	a. The "PURPOSE" of the Workload, 
	
	b. The "STEPS" taken (and why each was necessary/important, 
	
	c. A "SYSTEM DESIGN DIAGRAM" that is created in draw.io, 
	
	d. "ISSUES/TROUBLESHOOTING" that may or may have occured, 
	
	e. An "OPTIMIZATION" section for that answers the question: What are the benefits of using managed services for cloud infrastructure?  What are some issues that a retail bank would face choosing this method of deployment and how would you address/resolve them? What are other disadvantages of using elastic beanstalk or similar managed services for deploying applications?
	
	f. A "CONCLUSION" statement as well as any other sections you feel like you want to include.

The README.md is a markdown file that has unique formatting.  Be sure to look up how to write in markdown or use a txt to markdown converter. 
