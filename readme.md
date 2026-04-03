# Jenkins Demo Project

This project demonstrates a simple Jenkins pipeline for a Python application. It includes a custom Jenkins Docker image with pre-installed tools and a sample pipeline configuration.

## Prerequisites

Before you begin, ensure you have the following installed on your local system:
- [Docker](https://docs.docker.com/get-docker/)
- [Python 3.x](https://www.python.org/downloads/) (Optional, for local development)

---

## 1. Local Development (Without Jenkins)

You can run the Python application locally to test it before integrating it with Jenkins.

### Setup

1. **Navigate to the `myapp` directory:**
   ```bash
   cd myapp
   ```

2. **(Optional) Create a virtual environment:**
   ```bash
   python3 -m venv venv
   source venv/bin/activate  # On Windows use `venv\Scripts\activate`
   ```

3. **Install dependencies:**
   ```bash
   pip install -r requirements.txt
   ```

### Running the App

- **Run the main application script:**
  ```bash
  python3 hello.py
  ```

- **Run with custom arguments:**
  ```bash
  python3 hello.py --name=YourName
  ```

---

## 2. Running with Jenkins (Docker Setup)

This section covers how to set up and run Jenkins locally using Docker.

### Step 1: Build the Custom Jenkins Image

Build the Jenkins BlueOcean Docker image using the provided `Dockerfile`. This image includes the Docker CLI and necessary plugins.

```bash
docker build -t myjenkins-blueocean:2.492.1 .
```

### Step 2: Create a Docker Network

Create a network for Jenkins and its agents to communicate.

```bash
docker network create jenkins
```

### Step 3: Run the Jenkins Container

#### MacOS / Linux
```bash
docker run --name jenkins-blueocean --restart=on-failure --detach \
  --network jenkins --env DOCKER_HOST=tcp://docker:2376 \
  --env DOCKER_CERT_PATH=/certs/client --env DOCKER_TLS_VERIFY=1 \
  --publish 8080:8080 --publish 50000:50000 \
  --volume jenkins-data:/var/jenkins_home \
  --volume jenkins-docker-certs:/certs/client:ro \
  myjenkins-blueocean:2.492.1
```

#### Windows
```powershell
docker run --name jenkins-blueocean --restart=on-failure --detach `
  --network jenkins --env DOCKER_HOST=tcp://docker:2376 `
  --env DOCKER_CERT_PATH=/certs/client --env DOCKER_TLS_VERIFY=1 `
  --volume jenkins-data:/var/jenkins_home `
  --volume jenkins-docker-certs:/certs/client:ro `
  --publish 8080:8080 --publish 50000:50000 myjenkins-blueocean:2.492.1
```

### Step 4: Access Jenkins and Get the Password

Once the container is running, you can access Jenkins at `http://localhost:8080`.

To unlock Jenkins, retrieve the initial admin password:
```bash
docker exec jenkins-blueocean cat /var/jenkins_home/secrets/initialAdminPassword
```

---

## 3. Pipeline Setup in Jenkins

1. **Access Jenkins:** Open `http://localhost:8080` in your browser.
2. **Setup:** Complete the initial setup wizard.
3. **New Item:** Click on "New Item," name it (e.g., `python-pipeline`), and select "Pipeline."
4. **Configure Pipeline:**
   - Under the "Pipeline" section, select "Pipeline script from SCM."
   - Choose "Git" as the SCM.
   - Enter the repository URL (or path if local).
   - Ensure the "Script Path" is set to `Jenkinsfile`.
5. **Run:** Click "Build Now" to execute the pipeline.

---

## Project Structure

- `myapp/`: Contains the main Python application and tests.
  - `hello.py`: Application entry point.
  - `requirements.txt`: Python dependencies.
- `Dockerfile`: Custom Jenkins image definition.
- `Jenkinsfile`: Jenkins pipeline configuration.
- `helloworld.py`: A simple test script.

## References

- [Official Jenkins Docker Installation Guide](https://www.jenkins.io/doc/book/installing/docker/)
- [YouTube Tutorial: Jenkins Course](https://www.youtube.com/watch?v=6YZvp2GwT0A)
- [How to find Docker Host URI](https://stackoverflow.com/questions/47709208/how-to-find-docker-host-uri-to-be-used-in-jenkins-docker-plugin)
