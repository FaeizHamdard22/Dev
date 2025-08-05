
# Flask Hello World – Full DevOps Pipeline 

This project demonstrates a complete DevOps pipeline using:

- Flask (Python web app)
- GitHub (Code version control)
- Docker (Containerization)
- Jenkins (CI/CD automation)
- DockerHub (Image registry)
- Terraform (Infrastructure as Code)
- Ansible (Configuration management)
- AWS EC2 (Cloud hosting)

---

##  Flask Application

Simple Flask app to return a hello message.

**`app.py`**
```python
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello():
    return "Hello from Flask app in Docker container!"

if __name__ == '__main__':
    app.run(debug=True, host='0.0.0.0')
````

**`requirements.txt`**

```
flask
```

---

##  Docker

Dockerfile to build the Flask app image.

**`Dockerfile`**

```dockerfile
FROM python:3.10-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install -r requirements.txt

COPY . .

CMD ["python", "app.py"]
```

---

##  Jenkins CI/CD Pipeline

Jenkins pulls the code, builds Docker image, and pushes it to DockerHub.

**`Jenkinsfile`**

```groovy
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
```

---

##  Provision EC2 with Terraform

Terraform provisions an Ubuntu EC2 instance.

**`main.tf`**

```hcl
provider "aws" {
  region     = "us-east-1"
  access_key = "<YOUR_ACCESS_KEY>"
  secret_key = "<YOUR_SECRET_KEY>"
}

resource "aws_instance" "web" {
  ami                         = "ami-0c7217cdde317cfec"
  instance_type               = "t2.micro"
  key_name                    = "mykey"
  associate_public_ip_address = true

  tags = {
    Name = "FlaskAppServer"
  }
}
```

---

##  Configure EC2 with Ansible

Ansible installs Docker, pulls image, and runs the container.

**`inventory.ini`**

```ini
[web]
<EC2_PUBLIC_IP> ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/mykey.pem
```

**`playbook.yml`**

```yaml
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
```

---

##  Access the Application

After deployment, open:

```
http://<EC2_PUBLIC_IP>:81
```

---

##  Run Locally (Optional)

```bash
git clone https://github.com/FaeizHamdard22/flask-helloworld-Devops.git
cd flask-helloworld-Devops

docker build -t flask-hello .
docker run -d -p 5000:5000 flask-hello
```

Visit:
[http://localhost:5000](http://localhost:5000)

---

##  DockerHub Image

Image is available at:
[https://hub.docker.com/r/faeizanaba/flask-hello](https://hub.docker.com/r/faeizanaba/flask-hello)

---

##  Author

**Faeiz Hamdard**
GitHub: [FaeizHamdard22](https://github.com/FaeizHamdard22)
DockerHub: `faeizanaba`

