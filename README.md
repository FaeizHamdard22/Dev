# Flask Hello World – Full DevOps Pipeline 

This project demonstrates a complete DevOps pipeline using:

- **Flask** (Python web app)
- **GitHub** (Code version control)
- **Docker** (Containerization)
- **Jenkins** (CI/CD automation)
- **DockerHub** (Image repository)
- **Terraform** (Infrastructure as Code)
- **Ansible** (Configuration management)
- **AWS EC2** (Cloud hosting)

---

##  Objective

Fully automate the process of:

1. Writing code for a simple Flask app
2. Pushing it to GitHub
3. Jenkins automatically builds the Docker image
4. Pushes it to DockerHub
5. Terraform provisions an EC2 instance
6. Ansible installs dependencies and runs the app in Docker

---

##  Project Structure

flask-helloworld-Devops/
│
├── app.py # Flask application
├── requirements.txt # Python dependencies
├── Dockerfile # Instructions to build Docker image
├── Jenkinsfile # Jenkins pipeline
├── inventory.ini # Ansible inventory (EC2 IP)
├── playbook.yml # Ansible tasks
├── main.tf # Terraform file to create EC2
└── README.md # This file



---

##  1. Flask App

**app.py**
```python
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello():
    return "Hello from Flask app in Docker container!"

if __name__ == '__main__':
    app.run(debug=True, host='0.0.0.0')
requirements.txt

flask
 2. Dockerfile
dockerfile

FROM python:3.10-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install -r requirements.txt

COPY . .

CMD ["python", "app.py"]
⚙ 3. Jenkins CI/CD
Jenkinsfile


pipeline {
    agent any

    stages {
        stage('Clone') {
            steps {
                git 'https://github.com/FaeizHamdard22/flask-helloworld-Devops.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t faeizanaba/flask-hello .'
            }
        }

        stage('Push to DockerHub') {
            steps {
                withCredentials([string(credentialsId: 'dockerhub-pass', variable: 'DOCKER_HUB_PASS')]) {
                    sh '''
                        echo $DOCKER_HUB_PASS | docker login -u faeizhamdard975@gmail.com --password-stdin
                        docker push faeizanaba/flask-hello
                    '''
                }
            }
        }
    }
}
 DockerHub credentials (dockerhub-pass) must be added in Jenkins > Manage Credentials

4. Terraform – Provision EC2
main.tf


provider "aws" {
  region     = "us-east-1"
  access_key = "<YOUR_ACCESS_KEY>"
  secret_key = "<YOUR_SECRET_KEY>"
}

resource "aws_instance" "web" {
  ami                    = "ami-0c7217cdde317cfec"  # Ubuntu 22.04
  instance_type          = "t2.micro"
  key_name               = "mykey"
  associate_public_ip_address = true

  tags = {
    Name = "FlaskAppServer"
  }
}
Run Terraform

terraform init
terraform apply
⚙ 5. Ansible – Install & Deploy on EC2
inventory.ini


[web]
<EC2_PUBLIC_IP> ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/mykey.pem
playbook.yml


- name: Setup Flask App on EC2
  hosts: web
  become: yes
  tasks:
    - name: Install Docker
      apt:
        name: docker.io
        update_cache: yes
        state: present

    - name: Install Python
      apt:
        name: python3
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
Run Ansible

ansible-playbook -i inventory.ini playbook.yml
Access the Application
After deployment, open in your browser:


http://<EC2_PUBLIC_IP>:81
 Run Locally
If you want to run the Flask app locally with Docker:


git clone https://github.com/FaeizHamdard22/flask-helloworld-Devops.git
cd flask-helloworld-Devops

docker build -t flask-hello .
docker run -d -p 5000:5000 flask-hello
Access it at:
http://localhost:5000

 DockerHub Image
View the Docker image here:
https://hub.docker.com/r/faeizanaba/flask-hello

👤 Author
Faeiz Hamdard

 GitHub Profile

 DockerHub: faeizanaba

✅ Summary
✅ Flask App (Python)
✅ Versioned with GitHub
✅ CI/CD with Jenkins
✅ Dockerized and Pushed to DockerHub
✅ Provisioned EC2 with Terraform
✅ Configured with Ansible
✅ Fully Automated Cloud Deployment

From code to cloud – a complete DevOps lifecycle! ☁

