
# **Cloud Native Resource Monitoring Python App on K8s!**

## What I Learned 🤯

While developing this project, I learned:

1. How to create a monitoring application in Python using Flask and psutil.
2. How to run a Python app locally.
3. Docker basics and how to containerize a Python application:
    1. Creating a Dockerfile
    2. Building a Docker image
    3. Running a Docker container
    4. Key Docker commands
4. How to create an ECR repository using Python Boto3 and push a Docker image to ECR.
5. The basics of Kubernetes, including creating an EKS cluster and node groups.
6. How to create Kubernetes deployments and services using Python!

## **Prerequisites**

Here’s what you’ll need before getting started:

- [x] An AWS account.
- [x] Programmatic access configured with AWS CLI.
- [x] Python 3 installed.
- [x] Docker and Kubectl installed.
- [x] A code editor like VSCode.

# ✨Let’s Get Started with My Project ✨

## **Part 1: Deploying the Flask Application Locally**

### **Step 1: Clone the Code**

To get started, clone the repository with the following command:

\`\`\`bash
git clone <repository_url>
\`\`\`

### **Step 2: Install Dependencies**

This application uses `psutil`, `Flask`, `Plotly`, and `boto3`. You can install them using pip:

\`\`\`bash
pip3 install -r requirements.txt
\`\`\`

### **Step 3: Run the Application**

To run the application, navigate to the root directory of the project and use:

\`\`\`bash
python3 app.py
\`\`\`

This command starts the Flask server on **`localhost:5000`**. You can access the application by navigating to [http://localhost:5000/](http://localhost:5000/) in your browser.

## **Part 2: Dockerizing the Flask Application**

### **Step 1: Create a Dockerfile**

Here’s the Dockerfile I created in the root directory of my project:

\`\`\`Dockerfile
# Use the official Python image as the base image
FROM python:3.9-slim-buster

# Set the working directory in the container
WORKDIR /app

# Copy the requirements file to the working directory
COPY requirements.txt .

RUN pip3 install --no-cache-dir -r requirements.txt

# Copy the application code to the working directory
COPY . .

# Set the environment variables for the Flask app
ENV FLASK_RUN_HOST=0.0.0.0

# Expose the port on which the Flask app will run
EXPOSE 5000

# Start the Flask app when the container is run
CMD ["flask", "run"]
\`\`\`

### **Step 2: Build the Docker Image**

To build the Docker image, use:

\`\`\`bash
docker build -t <image_name> .
\`\`\`

### **Step 3: Run the Docker Container**

Run the Docker container with:

\`\`\`bash
docker run -p 5000:5000 <image_name>
\`\`\`

This will start the Flask server inside a Docker container on **`localhost:5000`**. You can access the application via [http://localhost:5000/](http://localhost:5000/).

## **Part 3: Pushing the Docker Image to ECR**

### **Step 1: Create an ECR Repository**

Using Python, I created an ECR repository:

\`\`\`python
import boto3

# Create an ECR client
ecr_client = boto3.client('ecr')

# Create a new ECR repository
repository_name = 'my-ecr-repo'
response = ecr_client.create_repository(repositoryName=repository_name)

# Print the repository URI
repository_uri = response['repository']['repositoryUri']
print(repository_uri)
\`\`\`

### **Step 2: Push the Docker Image to ECR**

Push the Docker image to ECR with:

\`\`\`bash
docker push <ecr_repo_uri>:<tag>
\`\`\`

## **Part 4: Creating an EKS Cluster and Deploying the App Using Python**

### **Step 1: Create an EKS Cluster**

First, I created an EKS cluster and added a node group.

### **Step 2: Create a Node Group**

Next, I added a node group to the EKS cluster.

### **Step 3: Create Deployment and Service**

Using Python, I set up the deployment and service with Kubernetes:

\`\`\`python
from kubernetes import client, config

# Load Kubernetes configuration
config.load_kube_config()

# Create a Kubernetes API client
api_client = client.ApiClient()

# Define the deployment
deployment = client.V1Deployment(
    metadata=client.V1ObjectMeta(name="my-flask-app"),
    spec=client.V1DeploymentSpec(
        replicas=1,
        selector=client.V1LabelSelector(
            match_labels={"app": "my-flask-app"}
        ),
        template=client.V1PodTemplateSpec(
            metadata=client.V1ObjectMeta(
                labels={"app": "my-flask-app"}
            ),
            spec=client.V1PodSpec(
                containers=[
                    client.V1Container(
                        name="my-flask-container",
                        image="<your_image_uri>",
                        ports=[client.V1ContainerPort(container_port=5000)]
                    )
                ]
            )
        )
    )
)

# Create the deployment
api_instance = client.AppsV1Api(api_client)
api_instance.create_namespaced_deployment(
    namespace="default",
    body=deployment
)

# Define the service
service = client.V1Service(
    metadata=client.V1ObjectMeta(name="my-flask-service"),
    spec=client.V1ServiceSpec(
        selector={"app": "my-flask-app"},
        ports=[client.V1ServicePort(port=5000)]
    )
)

# Create the service
api_instance = client.CoreV1Api(api_client)
api_instance.create_namespaced_service(
    namespace="default",
    body=service
)
\`\`\`

Make sure to update the image URI in the code above. After running the deployment and service creation commands, you can verify them with:

\`\`\`bash
kubectl get deployment -n default
kubectl get service -n default
kubectl get pods -n default
\`\`\`

To expose the service, use port-forward:

\`\`\`bash
kubectl port-forward service/<service_name> 5000:5000
\`\`\`

---

### App Output Screenshot
![App Output Screenshot](attachment_link_here)

---

Feel free to replace `<repository_url>`, `<image_name>`, and `<ecr_repo_uri>` with your specific details. Make sure to update the screenshot link with the actual path to the screenshot of your app output. Let me know if you need any further modifications!
