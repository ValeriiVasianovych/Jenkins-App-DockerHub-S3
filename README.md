Project Description: Elastic Beanstalk + S3 Bucket + GitHub Actions
Overview
This project harnesses the power of automation and cloud infrastructure to streamline the deployment process of a Flask application. Leveraging the synergy between GitHub Actions, AWS S3, and Elastic Beanstalk, it enables seamless updates and deployments with each push to the repository.

Technologies Used:
GitHub Actions: Automates the deployment process by triggering actions on each push to the repository.
AWS S3 Bucket: Serves as a storage solution for the Flask application archive, facilitating easy access and version control.
Elastic Beanstalk: Manages the deployment of the Flask application, ensuring scalability, reliability, and efficient resource management.
Workflow:
GitHub Actions Trigger: Upon each push to the repository, GitHub Actions initiates the deployment process.
Archive Generation: The Flask application is packaged into an archive format (e.g., ZIP) to ensure portability and ease of deployment.
S3 Bucket Upload: The generated archive is uploaded to an AWS S3 bucket, providing a centralized storage location.
Elastic Beanstalk Deployment: The updated application archive is deployed to Elastic Beanstalk, which handles the deployment process, including provisioning resources, scaling, and monitoring.
Benefits:
Automation: Eliminates manual intervention in the deployment process, reducing human error and saving time.
Scalability: Elastic Beanstalk automatically scales resources based on demand, ensuring optimal performance.
Version Control: The use of S3 for storing application archives facilitates versioning, enabling rollbacks and historical tracking.
Reliability: Leveraging AWS services ensures high availability and reliability of the deployed application.
