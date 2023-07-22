# The Multi-Cloud Migration Project
<img width="897" alt="1" src="https://github.com/niazkhan0731/Multi-Cloud-Project/assets/135728087/9e2c97be-3832-4580-8ee5-f3d1b2155662"><br>
<br>

Hey Everyone! This is a project I did with <a href="https://thecloudbootcamp.com/en/">The Cloud Bootcamp</a>, based on a real-world scenario where a Luxury Hotel & Resorts company is migrating its on-premises applications and data to the Cloud. Above in the diagram, you can see that I will be demonstrating step by step how I utilized <a href="https://aws.com">AWS</a> and <a href="https://cloud.google.com/en/">GCP</a> to make this happen!
## The Scenario
Due to the spike in Covid cases, the general manager requested the IT team to create a new application that would centralize all the task results storage. The problem arises from the increasing number of people coming to the hotel without certainty of whether they are infected or not. As a five-star hotel, they are concerned about the safety of both guests and employees. In response to the surge in cases, the general manager proposed a safety precaution: requiring every guest to prove that they are not infected with Covid. As a result, the IT team was asked to create a new application that mandates guests to present a negative test result to the receptionist upon check-in. The receptionist will then scan the test result for documentation purposes. This application will be migrated to the cloud, serving as the central storage for all guest data.

The issue arises from the significant increase in the number of guests. Initially, when the IT team developed this application, they hosted it on-premises within their small corporate data center. The application was running on one server in a virtual machine, while the data was stored in another server running MySQL. However, due to a large number of people booking at the hotel, the application experienced a high volume of traffic, impacting its performance. Meeting the demand would require additional hardware resources, but the data center's limited capacity and time constraints made it impossible to order and wait for new hardware. Consequently, the IT director decided to migrate their data to the cloud and hired a cloud engineer named Niaz Khan to assist with the migration process. 😊
## The Solution
After a few meetings with the IT director, they decided to use the <b>Google Cloud Platform</b> to host the application due to their existing partnership with Google.

Firstly, a <b>VPC</b> will be created inside the Google Cloud, which is a virtualized network within the GCP. To store the data, <b>Google Cloud SQL</b> will be utilized, as it supports MySQL, which is already present in the on-premises environment. Next, the application that is currently running on the company's on-premises virtual machine will be converged and transformed into a <b>Docker</b> image, which will then be stored inside the <b>Google Container Registry (GCR)</b>.

For deploying the application, they have opted to use <b>Kubernetes</b>, an open-source container orchestration platform that automates the deployment, scaling, and management of containerized applications. This Kubernetes will run within the <b>Google Kubernetes Engine (GKE)</b> cluster, and the application will be connected to the Google Cloud SQL database.

During the meetings, the IT Director mentioned that they were already using <b>Amazon S3</b> to store some of their files and were aware that the more they use the S3 service, the cheaper it becomes in terms of unit cost. Therefore, Amazon S3 will be used to store the data related to the web application, which includes the form that the receptionist will fill out with customer's Covid information and the uploaded test result in PDF format.

To make both AWS and GCP work seamlessly together, they will establish the necessary connections. Additionally, <b>Terraform</b> will be employed to deploy this environment in a fully automated manner using Infrastructure as Code.
