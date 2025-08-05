# Flask Hello World - DevOps Pipeline Project

This project showcases a complete CI/CD pipeline using a simple Python Flask app. The code is built, containerized, pushed to DockerHub, and automatically deployed to an AWS EC2 server using **Jenkins**, **Terraform**, and **Ansible**.

---

## Technologies Used

- **Flask** – Python micro web framework
- **GitHub** – Source code version control
- **Docker** – Containerization
- **DockerHub** – Container image registry
- **Jenkins** – Continuous Integration & Continuous Deployment
- **Terraform** – Infrastructure as Code (IaC)
- **Ansible** – Configuration management & deployment
- **AWS EC2** – Cloud server to host the app

---

##  Project Workflow - Step by Step

### 1. Flask App and Project Files

The app contains:
- `app.py` – Flask application
- `requirements.txt` – Python dependencies (like Flask)
- `Dockerfile` – Instructions to build the Docker image
- `Jenkinsfile` – Jenkins pipeline automation script

All files are pushed to GitHub.

---

### 2. Jenkins Setup (CI/CD)

In Jenkins:
- Created a new **pipeline job**.
- Connected it to the GitHub repository.
- Created **DockerHub credentials** inside Jenkins (`username/password`) using "Manage Credentials".
- Pipeline uses the `Jenkinsfile` which:
  1. Pulls the latest code from GitHub.
  2. Builds the Docker image using `Dockerfile`.
  3. Logs in to DockerHub using the credentials.
  4. Pushes the image to DockerHub:  
      [`faeizanaba/flask-hello`](https://hub.docker.com/repository/docker/faeizanaba/flask-hello)

---

### 3. Terraform – Provision EC2 Instance

- A Terraform script provisions an EC2 instance (Ubuntu) on AWS.
- Example configuration includes:
  - AMI ID
  - Instance type
  - Key pair
  - Security group (opened port 81)

After applying the Terraform code:
```bash
terraform init
terraform apply
→ EC2 instance is created and public IP is assigned.

4. Ansible – Install & Deploy on EC2
After EC2 is ready, used Ansible to configure the instance:

inventory.ini
Specifies the public IP of EC2:

ini
Copy
Edit
[web]
<EC2_PUBLIC_IP> ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/mykey.pem
playbook.yml tasks:
Update apt packages

Install docker.io and python3

Start and enable Docker service

Pull the Docker image from DockerHub

Run the container on port 81

yaml
Copy
Edit
- name: Setup Flask App on EC2
  hosts: web
  become: yes
  tasks:
    - name: Install Docker
      apt:
        name: docker.io
        update_cache: yes
        state: present

    - name: Start Docker
      service:
        name: docker
        state: started
        enabled: yes

    - name: Pull Docker image
      command: docker pull faeizanaba/flask-hello

    - name: Run container
      command: docker run -d -p 81:5000 faeizanaba/flask-hello
 Final Result
App is now running inside Docker on AWS EC2.

Accessible via:
http://<EC2_PUBLIC_IP>:81

 Run Locally
If you want to test the app locally:

bash
Copy
Edit
git clone https://github.com/FaeizHamdard22/flask-helloworld-Devops.git
cd flask-helloworld-Devops

docker build -t flask-hello .
docker run -d -p 5000:5000 flask-hello
→ Open: http://localhost:5000

 DockerHub Image
faeizanaba/flask-hello

 Author
Faeiz Hamdard

GitHub

DockerHub

 Summary
 Flask App →
 Pushed to GitHub →
 Built and pushed via Jenkins →
 Deployed on EC2 via Terraform & Ansible →
 App running in Docker on the cloud!

This project demonstrates a real-world DevOps pipeline from code to cloud, fully automated.
