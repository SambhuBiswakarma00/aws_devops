## Problem Statement:
You are assigned to create a software development life cycle for an application your company has created. The company wants you to use AWS for the infrastructure part and AWS Developer tools for the pipeline part.
Tasks to be performed:
1. Create a website in any language of your choice and push the code into GitHub
2. Migrate your GitHub repository into the AWS CodeCommit repository
3. Create two Code Deploy deployments (for the QA stage and the Production stage) with an EC2 deployment group into which you can push the code from the CodeCommit repository 4. Using AWS CodePipeline, create a software development life cycle:
a. The source is the CodeCommit repository.
b. The code will be pushed into the deployment created in CodeDeploy.
c. There should be two stages in deployment, the QA stage and the Production stage d. Only when the QA stage is successful, the Production stage should execute.
5. Create a third stage where the same website is pushed into an Elastic Beanstalk environment


## Solution

### 1. Create a Website and Push Code to GitHub
Create a Simple Website:
- Let's create a simple HTML website for this example.
- Create a directory for your project and add an index.html file:
    ```
    <!DOCTYPE html>
    <html>
    <head>
        <title>My Simple Website</title>
    </head>
    <body>
        <h1>Welcome to my website!</h1>
        <p>This is a simple HTML page.</p>
    </body>
    </html>
    ```
Push Code to GitHub:
- Initialize a Git repository, commit your code, and push it to GitHub:
```
cd my-simple-website
git init
git add .
git commit -m "Initial commit"
git remote add origin https://github.com/your-username/your-repo.git
git push -u origin master
```

### 2. Migrate GitHub Repository to AWS CodeCommit
Create a CodeCommit Repository:
- Go to the AWS Management Console.
- Navigate to CodeCommit.
- Click on Create repository.
- Enter a name for your repository and click Create.
Clone the CodeCommit Repository:
- Configure AWS CLI with your credentials if you haven't already:
```
aws configure
```
- Clone the CodeCommit repository locally:
```
git clone https://git-codecommit.<region>.amazonaws.com/v1/repos/<your-repo>
```
Push GitHub Repository to CodeCommit:
- Add the CodeCommit repository as a remote in your local GitHub repository clone:
```
cd my-simple-website
git remote add aws-codecommit https://git-codecommit.<region>.amazonaws.com/v1/repos/<your-repo>
git push aws-codecommit master
```
### 3. Create CodeDeploy Deployments
Launch EC2 Instances and Install CodeDeploy Agent:
- Launch EC2 instances (one for QA and one for Production).
- SSH into each instance and install the CodeDeploy agent:
```
sudo apt-get update
sudo apt-get install ruby wget
cd /home/ubuntu
wget https://aws-codedeploy-<region>.s3.<region>.amazonaws.com/latest/install
chmod +x ./install
sudo ./install auto
sudo service codedeploy-agent start
```
Create a CodeDeploy Application:
- Go to the AWS Management Console.
- Navigate to CodeDeploy.
- Click on Create application.
- Enter an application name and choose EC2/On-Premises for the compute platform.
- Click Create application.
  
Create Deployment Groups:
For the QA stage:
- In the CodeDeploy application, click Create deployment group.
- Enter a name for the deployment group (e.g., QA).
- Select the EC2 instances that you want to use for the QA stage.
- Click Create deployment group.
For the Production stage:
- Repeat the steps to create another deployment group for Production.
  
AppSpec File Configuration:
- In your project directory, create an appspec.yml file:
    ```
    version: 0.0
    os: linux
    files:
      - source: /
        destination: /var/www/html
    hooks:
      BeforeInstall:
        - location: scripts/install_dependencies.sh
          timeout: 300
      AfterInstall:
        - location: scripts/restart_server.sh
          timeout: 300
    ```
    
Create Deployment Scripts:
- Create a scripts directory in your project with the following scripts:
install_dependencies.sh:
    ```
    #!/bin/bash
    apt-get update
    apt-get install -y apache2
    ```
restart_server.sh:
```
#!/bin/bash
systemctl restart apache2
```
### 4. Create a CodePipeline
Create the Pipeline:
- Go to the AWS Management Console.
- Navigate to CodePipeline.
- Click on Create pipeline.
- Enter a name for your pipeline and click Next.
  
Configure Source Stage:
- Choose AWS CodeCommit as the source provider.
- Select the CodeCommit repository and branch (e.g., master).
- Click Next.
  
Configure Build Stage (Optional):
- If you have a build process, choose AWS CodeBuild as the build provider.
- Configure the build project.
- Click Next.
  
### Configure Deploy Stage:
For the QA stage:
- Add a deploy stage.
- Choose AWS CodeDeploy as the deploy provider.
- Select the CodeDeploy application and the QA deployment group.
- Click Next.
For the Production stage:
- Add another deploy stage.
- Choose AWS CodeDeploy as the deploy provider.
- Select the CodeDeploy application and the Production deployment group.
- Click Next.
Add Condition for Production Stage:
- In the Production stage settings, add a condition so that it only runs if the QA stage is successful.
- Click Add stage.

### Add Elastic Beanstalk Stage:
- Add a new stage in the pipeline.
- Choose AWS Elastic Beanstalk as the deploy provider.
- Configure the Elastic Beanstalk environment.
- Click Next and complete the pipeline creation.

## Summary
By following these detailed steps, you will create a website, push the code to GitHub, migrate it to AWS CodeCommit, create CodeDeploy deployments for QA and Production, and set up a CodePipeline that includes stages for QA, Production, and Elastic Beanstalk deployment.
