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
At the end of creating the build in Jenkins I discovered that specifically at the build stage the code failed.The error message that displayed stated that python 3.7 couldn't be found. Upon that realization I decided that my step would be installing Python 3.7. However I discovered an easier resolution which was using the 

`sudo apt update && sudo apt install fontconfig openjdk-17-jre software-properties-common && sudo add-apt-repository ppa:deadsnakes/ppa && sudo apt install python3.7 python3.7-venv` 

This command which made sure python was up to date and I didn't need any upgrades that will interfere with completing all the build stages. <br>







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













    


















































  


  
