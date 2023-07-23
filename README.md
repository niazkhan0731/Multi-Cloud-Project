# The Multi-Cloud Migration Project
<img width="897" alt="1" src="https://github.com/niazkhan0731/Multi-Cloud-Project/assets/135728087/9e2c97be-3832-4580-8ee5-f3d1b2155662"><br>
<br>

Hey Everyone! This is a project I did with <a href="https://thecloudbootcamp.com/en/">The Cloud Bootcamp</a>, based on a real-world scenario where a Luxury Hotel & Resorts company is migrating their on-premises applications and data to the Cloud. Above in the diagram, you can see that I will be demonstrating step by step how I utilized <a href="https://aws.com">AWS</a> and <a href="https://cloud.google.com/">GCP</a> to make this happen!
## The Scenario
Due to the spike in Covid cases, the general manager requested the IT team to create a new application that would centralize all the task results storage. The problem arises from the increasing number of people coming to the hotel without certainty of whether they are infected or not. As a five-star hotel, they are concerned about the safety of both guests and employees. In response to the surge in cases, the general manager proposed a safety precaution: requiring every guest to prove that they are not infected with Covid. As a result, the IT team was asked to create a new application that mandates guests to present a negative test result to the receptionist upon check-in. The receptionist will then scan the test result for documentation purposes. This application will be migrated to the cloud, serving as the central storage for all guest data.

The issue arises from the significant increase in the number of guests. Initially, when the IT team developed this application, they hosted it on-premises within their small corporate data center. The application was running on one server in a virtual machine, while the data was stored in another server running MySQL. However, due to a large number of people booking at the hotel, the application experienced a high volume of traffic, impacting its performance. Meeting the demand would require additional hardware resources, but the data center's limited capacity and time constraints made it impossible to order and wait for new hardware. Consequently, the IT director decided to migrate their data to the cloud and hired a cloud architect named Niaz Khan to assist with the migration process. 😊
## The Solution
After a few meetings with the IT director, they decided to use the <b>Google Cloud Platform</b> to host the application due to their existing partnership with Google.

Firstly, a <b>VPC</b> will be created inside the Google Cloud, which is a virtualized network within the GCP. To store the data, <b>Google Cloud SQL</b> will be utilized, as it supports <b>MySQL</b>, which is already present in the on-premises environment. Next, the application that is currently running on the company's on-premises virtual machine will be converged and transformed into a <b>Docker</b> image, which will then be stored inside the <b>Google Container Registry (GCR)</b>.

For deploying the application, they have opted to use <b>Kubernetes</b>, an open-source container orchestration platform that automates the deployment, scaling, and management of containerized applications. This Kubernetes will run within the <b>Google Kubernetes Engine (GKE)</b> cluster, and the application will be connected to the Google Cloud SQL database.

During the meetings, the IT Director mentioned that they were already using <b>Amazon S3</b> to store some of their files and were aware that the more they use the S3 service, the cheaper it becomes in terms of unit cost. Therefore, Amazon S3 will be used to store the data related to the web application, which includes the form that the receptionist will fill out with customer's Covid information and the uploaded test result in PDF format.

To make both AWS and GCP work seamlessly together, they will establish the necessary connections. Additionally, <b>Terraform</b> will be employed to deploy this environment in a fully automated manner using Infrastructure as Code.
## Mission 1: Configuration & Deployment
First thing first, I created a new AWS IAM user name <b>terraform-en-1</b> and gave it full access to S3 and CLI. I then downloaded the access file and uploaded the <b>accessKeys.csv</b> and the <b>mission1.zip</b> file provided to me by the instructor to the GCP CLI.  

<img width="1213" alt="2" src="https://github.com/niazkhan0731/Multi-Cloud-Project/assets/135728087/09954fe1-6fee-4c65-a88c-ec1787791c53">
<br><br>
Now its time to make some new directories and unzip the <b>mission1.zip</b> file and also give user execution permission to the to any <b>.sh</b> files that were unzipped. To do this I ran the following commands on the GCP CLI:
<br><br>
<b><i>mkdir mission1_en<br>
mv mission1.zip mission1_en<br>
cd mission1_en<br>
unzip mission1.zip<br>
mv ~/accessKeys.csv mission1/en<br>
cd mission1/en<br>
chmod +x *.sh</b></i><br><br>
<img width="1218" alt="3" src="https://github.com/niazkhan0731/Multi-Cloud-Project/assets/135728087/0db8a78d-a3ec-4d8c-9ac1-908b8508badf">
<br><br>
Cool now lets run the following command to prepare the AWS and GCP enviornment. This is how Terraform will be able to authenticate with AWS:
<br><br>
<b><i></i>./aws_set_credentials.sh accessKeys.csv</b><br><br>
  Also we will run this command to set the project id on the Google Cloud side where we would like to create the resources in:<br><br>
<b><i>gcloud config set project trans-aurora-393700</b></i>
<br><br>
<img width="1214" alt="4" src="https://github.com/niazkhan0731/Multi-Cloud-Project/assets/135728087/42f5bfc9-2f72-4d5b-9184-19392e7af522">
<br><br>
Now we will execute this command to set the project id to the cloudshell so when I run Terraform later, it will know in which project it should create the resources in Google Cloud:
<br><br>
<b><i>./gcp_set_project.sh</i></b>
<br><br>
After that we will run these commands to enable the Container Registry API, Kubernetes Engine API and the Cloud SQL API:
<br><br>
<b><i>gcloud services enable containerregistry.googleapis.com<br>
gcloud services enable container.googleapis.com<br>
gcloud services enable sqladmin.googleapis.com </i></b><br><br>
Next, before executing the Terraform commands, I will first open the Google Editor and update the file <b>tcb_aws_storage.tf</b> replacing the bucket name with an unique name (AWS requires unique bucket names).<br><br>
<img width="1210" alt="5" src="https://github.com/niazkhan0731/Multi-Cloud-Project/assets/135728087/6f4c8a09-1199-470d-89e7-ca1e711b8c41">
<br><br>
What this will do is when Terraform runs to create the resources for us on the AWS S3 bucket it will use the unique name <b>luxxy-covid-testing-system-pdf-en-nk73</b> to create it.
<br><br>
Now I will save this file on the Google Editor and close out of it by clicking the "Open Terminal" button. Next, we will run the following commands to finish provision infrastructure steps:
<br><br>
Let's first go into the folder by typing the command <b><i>cd ~/mission1_en/mission1/en/terraform/</i></b>
<br><br>
Now lets run these commands:<br><br>
  <b><i>terraform init</b></i> - This will download the plugins required for Terraform to run the code or to communicate with the cloud providers API's. <br><br>
<img width="1213" alt="6" src="https://github.com/niazkhan0731/Multi-Cloud-Project/assets/135728087/a188dc04-a28f-41ef-a0b7-92af5e73cc40">
<br><br>
<b><i>terraform plan</b></i> - This command will look at the files that we have locally here in the cloud shell, and based on those files it will say what Terraform is planning to do in the infrastructure. <br><br>
  <img width="1204" alt="7" src="https://github.com/niazkhan0731/Multi-Cloud-Project/assets/135728087/67b6d242-65b8-4de1-b1b4-32a00fb6f4bb">
<br><br>
<b><i>terraform apply</b></i> - This command will now start creating the resources for us automatically.<br><br>
  <img width="1207" alt="8" src="https://github.com/niazkhan0731/Multi-Cloud-Project/assets/135728087/0eabb9e9-99e0-407d-8a32-e9118a92df42">

  <br><br>
<img width="1212" alt="9" src="https://github.com/niazkhan0731/Multi-Cloud-Project/assets/135728087/979ae245-6c3d-422c-8f23-4d65d46900f2">
<br><br>
Cool, so now if we go to our AWS Management Console and search for S3, you can see a bucket was automatically created using the unique name we specified earlier!
<br><br>
<img width="1210" alt="10" src="https://github.com/niazkhan0731/Multi-Cloud-Project/assets/135728087/0fb4f4e7-54d1-4640-bcf5-2548f09e05cd">
<br><br>
You can also see if we go back to our Google Cloud Console and search for "SQL", the SQL database was also automatically created for us.
<br><br>
<img width="1211" alt="11" src="https://github.com/niazkhan0731/Multi-Cloud-Project/assets/135728087/b62b6404-2805-4199-9ce8-fd883c58dfc0">
<br><br>
Now, if we search for "GKE" on the Google Cloud Console and click on Kubernetes Engine, you can see that the Kubernetes cluster was also created for us automatically.
<br><br>
<img width="1207" alt="12" src="https://github.com/niazkhan0731/Multi-Cloud-Project/assets/135728087/0a28b02b-38ad-44e3-b9ec-9b9e143debb7">
<br><br>
I will now go into our CloudSQL instance and add a Public Access authorized network (for testing only), to connect remotely in the MySQL instance. To accomplish this I will be doing the following steps (<a href="https://github.com/niazkhan0731/Multi-Cloud-Project/assets/135728087/c29b63cd-8c4e-421b-9475-db489aa1d434">Click Here</a> to download the video).
<br><br>
<b>Steps:</b><br>
- Once the Cloud SQL instance is provisioned, access the Cloud SQL service
- Click on your Cloud SQL instance.
- On the left side, under Primary Instance, click on **Connections**.
- Go to **Networking** tab.
- Under **Instance IP assignment**, select Private IP to enable.
    - Under **Associated networking**, select "Default"
    - Click **Set up Connection**
    - Click on **Enable API**, to enable Service Networking API (if asked).
    - Select **Use an automatically allocated IP range in your network**.
    - Click **Continue**
    - Click **Create Connection** and **wait a minutes until conclude.** You will see the message: “*Private services access connection for network default
     has been successfully created.”*
- Under **Authorized Networks**, click "Add Network".
- Under **New Network**, enter the following information:
    - **Name:** Public Access (For testing purposes only)
    - **Network**: 0.0.0.0/0
    - Click **Done**.
    - Click **Save** and ****wait to finish the update.
    This update may take from **10 to 20 minutes** to finish

PS: For production environments, it is recommended to use only the Private Network for database access. 
⚠️  Never grant public network access (0.0.0.0/0) to production databases.

<img width="1211" alt="13" src="https://github.com/niazkhan0731/Multi-Cloud-Project/assets/135728087/cc6d5a47-6384-4350-b11b-7f26cbbd0ac5">

## Mission 2: Docker & Kubernetes
In this second part of the mission, we will proceed to create a Docker image of the on-premises application server files for the companies and push this image to Google Container Registry (GCP) instance. Later, we will deploy this application using the Kubernetes cluster. Now, you might be wondering, what exactly is Docker?

Docker is a tool that enables you to deploy applications in a highly portable manner by creating an "image." Instead of dealing with the complexities of provisioning virtual machines (VMs), Docker allows you to focus on packaging your application inside this image. Once done, you can use an application that supports the Docker engine, and voilà - your application can be up and running without worries.

Next if  you are wondering, what exactly is Kubernetes? Kubernetes is like an orchestra conductor for containers. Containers are small, self-contained units for running applications. When you have many containers working together, it can be complex to manage individually. That's where Kubernetes comes in! Kubernetes is a smart manager that handles all these containers for you. It's perfect for managing microservices, which are small, independent parts of an application. However, for this project, we will deploy a monolith container that can later be broken down into microservices and managed with Kubernetes as the application grows in complexity and scale.

Now that you have a better understanding of Docker and Kubernetes, let's begin by creating a new IAM user on AWS and naming it <b>luxxy-covid-testing-system-en-app1</b>. The purpose of this is to enable direct communication between Kubernetes on GCP and this user, allowing access to the Amazon S3 bucket later on.

<img width="1420" alt="14" src="https://github.com/niazkhan0731/Multi-Cloud-Project/assets/135728087/5662d153-2ec2-4418-baf7-08ba607b0a3e">
<br><br>
Now we will create a new access key for this user and download the <b>.csv</b> file. Then, we'll re-name that file to <b>luxxy-covid-testing-system-en-app1.csv</b> so that we can upload it later to the GCP CLI. This will establish a secure connection between Kubernetes on GCP and the IAM user on AWS.
<br><br>
<img width="1406" alt="15" src="https://github.com/niazkhan0731/Multi-Cloud-Project/assets/135728087/79ccb725-5db6-473e-99b2-ac47852f17d6">
<br><br>
Next, we will go to our GCP console and navigate to Cloud SQL instance then create a new user <b>app</b> with password <b>welcome123456</b> on Cloud SQL MySQL database.
<br><br>
<img width="1476" alt="16" src="https://github.com/niazkhan0731/Multi-Cloud-Project/assets/135728087/fa490bec-9968-4d8d-826e-19b34d4f1c97">
<br><br>
Next we're going to go into our Google Cloud Shell and execute the following commands to download and unzil the files for <b>mission2.zip</b>. You can also access these files on this Github repository if you are following along.
<br><br>
<b><i>cd ~ <br>
mkdir mission2_en<br>
cd mission2_en<br>
wget https://tcb-public-events.s3.amazonaws.com/icp/mission2.zip<br>
unzip mission2.zip</b></i>
<br><br>
<img width="1180" alt="17" src="https://github.com/niazkhan0731/Multi-Cloud-Project/assets/135728087/c9884bd7-da45-4e9a-8bdb-83ea9c22c4bc">
<br><br>
On the Google Cloud Shell we will now connect to MySQL DB running on Cloud SQL (once it prompts for the password, we will provide welcome123456):
<br><br>
<b><i>mysql --host="< public_ip_cloudsql >" --port=3306 -u app -p</i></b> Note: Don't forget to replace the "<b>< public_ip_cloudsql ></b>" with your instance IP.
<br><br>
<img width="1184" alt="18" src="https://github.com/niazkhan0731/Multi-Cloud-Project/assets/135728087/ddecb047-b463-490e-9d31-c0524597b0ed">
<br><br>
Once we are connected to the database instance, we will create the products table for testing purposes using these commands:
<br><br>
<b><i>use dbcovidtesting;<br>
source ~/mission2_en/mission2/en/db/create_table.sql<br>
show tables;<br>
exit;</i></b>
<br><br>
<img width="1179" alt="19" src="https://github.com/niazkhan0731/Multi-Cloud-Project/assets/135728087/2d79115b-f3cd-4af9-b90d-520779fc3c3b">
<br><br>
Next, to move forward with the Docker image creating process we will first enable Cloud Build API inside Cloud Shell using this command: <b><i>gcloud services enable cloudbuild.googleapis.com</i></b>
<br><br>
Then we will build the Docker image and push it to Google Container Registry. Please replace the < PROJECT_ID > with your My First Project ID. 
<br><br>
<b><i>cd ~/mission2_en/mission2/en/app<br>
gcloud builds submit --tag gcr.io/< PROJECT_ID >/luxxy-covid-testing-system-app-en</i></b>
<br><br>
<img width="1173" alt="20" src="https://github.com/niazkhan0731/Multi-Cloud-Project/assets/135728087/ee86bcf2-13e8-41b7-a5a7-d1c25be892c8">
<br><br>
Now once that is completed, if we go to the Google Container Registry (GCR), we can see that it successfully created the image.
<br><br>
<img width="1179" alt="21" src="https://github.com/niazkhan0731/Multi-Cloud-Project/assets/135728087/8f47bec7-25a0-4a98-b315-0853633b6182">
<br><br>
Next we will open the Cloud Editor and edit the Kubernetes deployment file (<b>luxxy-covid-testing-system.yaml</b>) and update the variables below on line 33 with your <b>< PROJECT_ID > </b> on the Google Container Registry path, on line 42 <b>AWS Bucket name, AWS Keys (open file luxxy-covid-testing-system-en-app1.csv and use Access key ID on line 44 and Secret access key on line 46)</b> and <b>Cloud SQL Database Private IP on line 48</b>.
<br><br>
<img width="887" alt="22" src="https://github.com/niazkhan0731/Multi-Cloud-Project/assets/135728087/0cd946d3-0e3e-4c48-9c0b-328a8ea805f3">
<br>
<br>
<img width="1174" alt="23" src="https://github.com/niazkhan0731/Multi-Cloud-Project/assets/135728087/3b17e7ed-3988-486b-8138-7677893c1508">
<br><br>
<img width="1173" alt="24" src="https://github.com/niazkhan0731/Multi-Cloud-Project/assets/135728087/1fb3d9e5-2e28-4df9-a62d-6a7676b07a6b">
<br><br>
Now we will do the deployment of Kubernetes. First thing we need to do is connect to the Kubernetes cluster via console.
<br><br>
<img width="1165" alt="25" src="https://github.com/niazkhan0731/Multi-Cloud-Project/assets/135728087/fbf01c0c-6cc1-45ae-be2f-f6dfc137d25d">
<br><br>
<img width="1173" alt="26" src="https://github.com/niazkhan0731/Multi-Cloud-Project/assets/135728087/103d6e26-5f7c-4c42-94e5-04a95987c09c">
<br><br>
Now back in the Cloud Shell lets first type the command <b><i>cd ~/mission2_en/mission2/en/kubernetes</i></b> to go into the directory then type the command <b><i>kubectl apply -f luxxy-covid-testing-system.yaml</i></b> to deploy.
<img width="1179" alt="27" src="https://github.com/niazkhan0731/Multi-Cloud-Project/assets/135728087/cdf8a7dc-7e9c-4f48-9dbd-1bdd32073291">
<br><br>
Lets see, now if we go back to the Kubernetes Engine and click on <b>Services & Ingress</b> then click on the endpoints IP address...
<img width="1177" alt="28" src="https://github.com/niazkhan0731/Multi-Cloud-Project/assets/135728087/9c634fea-bde3-40da-93e0-363639581d72">
<br><br>
Voila! You can see that our application is now live, and it is currently simultaneously communicating with Google Cloud SQL and the Amazon S3 bucket. Hooray! 🎉
<br><br>
<img width="1474" alt="29" src="https://github.com/niazkhan0731/Multi-Cloud-Project/assets/135728087/a7a75636-2e69-45a2-a50d-c046fc2e6ed3">

## Mission 3: Migrating the data from on-premises to Google Cloud
<img width="786" alt="30" src="https://github.com/niazkhan0731/Multi-Cloud-Project/assets/135728087/6e222d59-84fe-46ca-8454-790713316af3">
<br><br>
As you can see in the diagram above, we were able to successfully build a Docker image with the application server file. We uploaded that image to the Google Container Registry, and then we used it to deploy the application inside the Kubernetes cluster, which is now communicating with Google Cloud SQL and AWS S3. Now, the last thing to do is migrate the data from on-premises to the application now running on Google Cloud.
<br><br>
First let's go into our Google Cloud Shell and download the <b>mission3.zip</b> into our CLI. If you've been following along you can also access it on this repository. So in order to do this in our CLI lets run the following commands:
<br><br>
<b><i>cd ~<br>
mkdir mission3_en<br>
cd mission3_en<br>
wget https://tcb-public-events.s3.amazonaws.com/icp/mission3.zip<br>
unzip mission3.zip</i></b>
<br><br>
<img width="1177" alt="31" src="https://github.com/niazkhan0731/Multi-Cloud-Project/assets/135728087/2c357707-3a31-4df8-bd17-f366c258cac7">
<br><br>
That was simple enough, now lets connect to Cloud SQL MySQL database instance by typing in the command below.
<br><br>
<b><i>mysql --host=< public_ip_address > --port=3306 -u app -p</i></b><br> 
  (Don't forget to replace the < public_ip_address > with yours.)
<br><br>
Now we will import the dump on the Cloud SQL by running these commands:
<br><br>
<b><i>use dbcovidtesting;<br>
source ~/mission3_en/mission3/en/db/db_dump.sql</i></b>
<br><br>
Now lets type <b><i>select * from records;</i></b> to see if it has been successfully imported correctly.
<br><br>
<img width="1177" alt="32" src="https://github.com/niazkhan0731/Multi-Cloud-Project/assets/135728087/ff3d599e-8dfe-42d1-a716-765f719ba02a">
<br><br>
Cool! Looks like all the database from the on-premises MySQL server has been successfully migrated! Let's type the <b><i>exit;</i></b> command to exit out of there.
<br><br>
    Next we will go into the AWS Management Console and download the PDF files by running these commands:
    <br><br>
    <b><i>mkdir mission3_en<br>
cd mission3_en<br>
wget https://tcb-public-events.s3.amazonaws.com/icp/mission3.zip<br>
unzip mission3.zip</i></b>
<br><br>
<img width="1469" alt="33" src="https://github.com/niazkhan0731/Multi-Cloud-Project/assets/135728087/8d945ae7-536b-4206-894c-8e21d1b969ad">
<br><br>
Next we will sync PDF Files with your AWS S3 used for COVID-19 Testing Status System. Don't forget to replace the bucket name with yours:
<br><br>
<b><i>cd mission3/en/pdf_files<br>
aws s3 sync . s3://luxxy-covid-testing-system-pdf-en-nk73</i></b>
<br><br>
<img width="1466" alt="34" src="https://github.com/niazkhan0731/Multi-Cloud-Project/assets/135728087/0620787b-74e9-4cd9-b8a9-0353c1fadba9">
<br><br>
As you can see all the PDF files from the old database has been successfully synced to our S3 bucket. If we check back on our live application we can see all the guest data and files was successfully migrated as well.
    <br><br>
    <img width="1468" alt="35" src="https://github.com/niazkhan0731/Multi-Cloud-Project/assets/135728087/528bc3ab-b8bb-49d6-b0e6-64420540c162">
<br><br>
<b>Ladies and gentlemen, we've reached the end of our project. As you can see, we have successfully migrated an "on-premises" application and database to a MultiCloud Architecture! This project was complex, but I have learned so many things throughout the process, such as building Docker images, utilizing Kubernetes to deploy applications, working with Google Cloud SQL and AWS S3, and managing data migration between on-premises and the cloud. It's been an incredible journey, and I'm looking forward to demonstrating more hands-on projects like this one in the future... Thanks for checking it out!</b>
