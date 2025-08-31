![AWS EKS](https://img.shields.io/badge/AWS-EKS-orange?logo=amazon-aws&logoColor=white)
![Amazon ECR](https://img.shields.io/badge/Amazon-ECR-blue?logo=amazon-aws&logoColor=white)
![Docker](https://img.shields.io/badge/Container-Docker-2496ED?logo=docker&logoColor=white)
![Kubernetes](https://img.shields.io/badge/Orchestration-Kubernetes-326ce5?logo=kubernetes&logoColor=white)
![Git](https://img.shields.io/badge/Version%20Control-Git-F05032?logo=git&logoColor=white)
![Status](https://img.shields.io/badge/Project-Completed-brightgreen)

<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# Set Up Kubernetes Deployment

**Project Link:** [View Project](http://learn.nextwork.org/projects/aws-compute-eks2)

**Author:** Bradley Davel  
**Email:** bradley.davel@outlook.com

---

## Set Up Kubernetes Deployment

![Image](http://learn.nextwork.org/sparkling_indigo_heroic_bat/uploads/aws-compute-eks2_45e6c3de5)

---

## Introducing Today's Project!

In this project, I'm going to:

-Clone a backend application from GitHub.
-Build a Docker image of the backend.
-Push your image to an Amazon ECR repository.
-Troubleshoot installation and configuration errors - as a PRO student, get ready to face errors head-on and push your skills to the next level.
-(Secret Mission) Dive into the backend code on GitHub.

Because this workflow reflects a real DevOps pipeline: taking source code, containerizing it, storing it in a secure registry, and solving issues along the way. It helps me build confidence with Docker, ECR, and debugging — key skills for deploying and maintaining cloud-native applications in production.

### Tools and concepts

I used Amazon EKS to launch and manage a Kubernetes cluster, Git to clone the backend application code, Docker to build a container image, and Amazon ECR to store that image for deployment.

Key steps include:

-Launching and connecting to an EC2 instance as my admin machine.
-Installing and configuring eksctl, kubectl, and AWS credentials.
-Creating an EKS cluster and monitoring its creation with CloudFormation.
-Cloning the backend repository from GitHub with Git.
-Building the backend Docker image and resolving configuration errors.
-Pushing the image to Amazon ECR for secure storage and integration with EKS.
-Exploring the backend code and Dockerfile to understand how the app works.

### Project reflection

This project took me approximately 1 hour to complete. The most challenging part was resolving Docker installation and permissions errors while building the backend image, since I had to troubleshoot why ec2-user couldn’t run Docker without sudo. My favorite part was seeing the backend container image successfully pushed to Amazon ECR, because it felt like the full workflow — from code to a cloud-ready image — came together smoothly.

Something new that I learnt from this experience was how Amazon ECR integrates seamlessly with EKS to make container deployment simple and secure. I also discovered how important it is to manage Docker permissions properly on an EC2 instance, since without adding ec2-user to the Docker group, container builds can fail due to access errors.

---

## What I'm deploying

I used eksctl to automatically provision my EKS cluster without having to manually configure all the CloudFormation templates, IAM roles, and networking. The create
cluster command I ran defined: -Cluster name → to identify the EKS cluster. -Region → where the cluster resources would live. -Managed node group settings → number of
worker nodes, min/max scaling values, and instance types. -IAM role attachment → so the EC2 instance could talk to EKS and CloudFormation. This single command spun up
the cluster, node group, and all required resources, saving me time and reducing the chance of manual errors.

### I'm deploying an app's backend

Next, I retrieved the backend that I plan to deploy. An app's backend means the "brain" of an application — it processes user requests, handles logic, and stores or retrieves data. Unlike the frontend that users see and interact with, backend code runs on the server side to ensure the app behaves as expected when a user clicks buttons or navigates pages.

I retrieved the backend code by cloning the repository nextwork-flask-backend. Cloning copied all the code and resources directly onto my EC2 instance so I could build, run, and deploy the backend part of my project. Once cloned, it appeared as a new folder on the instance with the full project files inside.

![Image](http://learn.nextwork.org/sparkling_indigo_heroic_bat/uploads/aws-compute-eks2_1ebb86c71)

---

## Building a container image

Once I cloned the backend code, my next step is to build a container image of the backend. This is because a container image bundles the app’s code, libraries, and configuration into a single, portable artifact. Kubernetes can pull that image to start identical containers - live copies of the app with the same behavior every time.

Since every container comes from the same image, the app runs consistently on my laptop, in testing, and in production. When traffic spikes, Kubernetes simply launches more containers from the same image, keeping behavior consistent and scaling smooth.

I ran into a permissions error because Docker was installed for the root account, so only root can talk to the Docker engine socket (/var/run/docker.sock). My session is using the ec2-user account, which doesn’t have permission to access the Docker daemon. Earlier Docker commands worked only when I prefixed them with sudo, which temporarily ran them as root. It’s better practice to grant ec2-user Docker access instead of using sudo every time.

To solve the permissions error, I sudo usermod -a -G docker ec2-user.

The Docker group is a group in Linux systems that gives users the permission to run Docker commands. By default, only the root user can run Docker commands. When you add a user (e.g., ec2-user) to the Docker group, it lets that user run Docker commands without typing sudo every time.



![Image](http://learn.nextwork.org/sparkling_indigo_heroic_bat/uploads/aws-compute-eks2_45e6c3de5)

---

## Container Registry

I’m using Amazon ECR in this project to store and manage my backend’s Docker image so it can be deployed easily to Kubernetes.

ECR is a good choice for the job because it’s a fully managed AWS container registry that integrates seamlessly with EKS. This means my cluster can pull images directly from ECR with minimal authentication setup, while keeping them secure and highly available. It also supports versioning, access control, and works smoothly with other AWS developer tools.

Container registries like Amazon ECR are great for Kubernetes deployment because they provide a secure, centralized place to store and version container images. Kubernetes can then reliably pull those images to launch identical containers across nodes in the cluster.

With ECR specifically, the integration with AWS Identity and Access Management (IAM) makes authentication simple and secure. It also scales automatically, ensures high availability, and reduces the need for custom registry setups. This means deployments are faster, more consistent, and easier to manage in production

![Image](http://learn.nextwork.org/sparkling_indigo_heroic_bat/uploads/aws-compute-eks2_l2m3n4o5)

---

## EXTRA: Backend Explained

After reviewing the app's backend code, I've learnt that the backend fetches data based on a search topic entered by the user. It uses Flask to connect with the external Hacker News Search API, processes the results, and then returns the data as a formatted JSON response that can be consumed by other parts of the application.

### Unpacking three key backend files

Requirements.txt lists the dependencies your app needs.

The Dockerfile gives Docker instructions on how to build the backend image step by step. It defines the environment, installs dependencies, copies the application code, and sets up the command needed to run the Flask app.

Key commands in this Dockerfile include:

-FROM → chooses the base image (e.g., Python).

-RUN → installs required packages and dependencies.

-COPY → brings the backend code into the image.

-CMD or ENTRYPOINT → defines the command to start the Flask server when the container runs.

These commands are what make container images powerful — they ensure the app runs consistently and reliably across laptops, test servers, and production clusters.

The app.py file conapp.py is the main code for the backend, built with Flask. It sets up the app and defines a route (/contents/<topic>) that directs requests to the SearchContents class. Inside this class, the get() method takes the topic from the URL, queries the Hacker News Search API, and collects details like the ID, title, and URL of matching results. Finally, it formats that data into a clean JSON response to send back to the user.tains...

---

---

