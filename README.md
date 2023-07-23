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

