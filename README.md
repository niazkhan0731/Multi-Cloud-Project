# The Multi-Cloud Migration Project
<img width="897" alt="1" src="https://github.com/niazkhan0731/Multi-Cloud-Project/assets/135728087/9e2c97be-3832-4580-8ee5-f3d1b2155662"><br>
<br>

Hey Everyone! This is a project I did with <a href="https://thecloudbootcamp.com/en/">The Cloud Bootcamp</a>, based on a real-world scenario where a Luxury Hotel & Resorts company is migrating its on-premises applications and data to the Cloud. Above in the diagram, you can see that I will be demonstrating step by step how I utilized <a href="https://aws.com">AWS</a> and <a href="https://cloud.google.com/">GCP</a> to make this happen!
## The Scenario
Due to the spike in Covid cases, the general manager requested the IT team to create a new application that would centralize all the task results storage. The problem arises from the increasing number of people coming to the hotel without certainty of whether they are infected or not. As a five-star hotel, they are concerned about the safety of both guests and employees. In response to the surge in cases, the general manager proposed a safety precaution: requiring every guest to prove that they are not infected with Covid. As a result, the IT team was asked to create a new application that mandates guests to present a negative test result to the receptionist upon check-in. The receptionist will then scan the test result for documentation purposes. This application will be migrated to the cloud, serving as the central storage for all guest data.

The issue arises from the significant increase in the number of guests. Initially, when the IT team developed this application, they hosted it on-premises within their small corporate data center. The application was running on one server in a virtual machine, while the data was stored in another server running MySQL. However, due to a large number of people booking at the hotel, the application experienced a high volume of traffic, impacting its performance. Meeting the demand would require additional hardware resources, but the data center's limited capacity and time constraints made it impossible to order and wait for new hardware. Consequently, the IT director decided to migrate their data to the cloud and hired a cloud architect named Niaz Khan to assist with the migration process. 😊
## The Solution
After a few meetings with the IT director, they decided to use the <b>Google Cloud Platform</b> to host the application due to their existing partnership with Google.

Firstly, a <b>VPC</b> will be created inside the Google Cloud, which is a virtualized network within the GCP. To store the data, <b>Google Cloud SQL</b> will be utilized, as it supports <b>MySQL</b>, which is already present in the on-premises environment. Next, the application that is currently running on the company's on-premises virtual machine will be converged and transformed into a <b>Docker</b> image, which will then be stored inside the <b>Google Container Registry (GCR)</b>.

For deploying the application, they have opted to use <b>Kubernetes</b>, an open-source container orchestration platform that automates the deployment, scaling, and management of containerized applications. This Kubernetes will run within the <b>Google Kubernetes Engine (GKE)</b> cluster, and the application will be connected to the Google Cloud SQL database.

During the meetings, the IT Director mentioned that they were already using <b>Amazon S3</b> to store some of their files and were aware that the more they use the S3 service, the cheaper it becomes in terms of unit cost. Therefore, Amazon S3 will be used to store the data related to the web application, which includes the form that the receptionist will fill out with customer's Covid information and the uploaded test result in PDF format.

To make both AWS and GCP work seamlessly together, they will establish the necessary connections. Additionally, <b>Terraform</b> will be employed to deploy this environment in a fully automated manner using Infrastructure as Code.
## Mission 1: Configuration
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
<b><i></i>terraform plan</b></i> - This command will look at the files that we have locally here in the cloud shell, and based on those files it will say what Terraform is planning to do in the infrastructure. <br><br>
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





