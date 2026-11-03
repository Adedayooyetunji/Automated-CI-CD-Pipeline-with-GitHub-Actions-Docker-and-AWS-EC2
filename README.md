# PULSE CI/CD — Automated Website Deployment

A responsive static portfolio website and learning project demonstrating a CI/CD deployment flow with GitHub Actions, Docker, Docker Hub and AWS EC2.

## Architecture

1. Edit the website in VS Code.
2. Push changes to the `main` branch on GitHub.
3. GitHub Actions builds a Docker image.
4. GitHub Actions pushes the image to Docker Hub.
5. GitHub Actions connects to Ubuntu EC2 over SSH.
6. EC2 pulls the image and recreates the Nginx container on port 80.

## Project structure

```text
.
├── .github/workflows/deploy.yml
├── index.html
├── style.css
├── script.js
├── Dockerfile
├── .dockerignore
└── README.md
```

## Run locally without Docker

Open `index.html` in a browser, or use the VS Code Live Server extension.

## Run locally with Docker

```bash
docker build -t automated-cicd-website .
docker run --rm -p 8080:80 automated-cicd-website
```

Visit http://localhost:8080.

## GitHub repository secrets

Create these under **Settings → Secrets and variables → Actions**:

- `DOCKERHUB_USERNAME`: Docker Hub account username.
- `DOCKERHUB_TOKEN`: Docker Hub access token with permission to push/pull this repository.
- `EC2_HOST`: EC2 public IPv4 address or DNS name.
- `EC2_USER`: `ubuntu` for the Ubuntu AMI used in this guide.
- `EC2_SSH_KEY`: full PEM private key contents for the EC2 key pair.

Never commit private keys or tokens to the repository.

## Prepare the EC2 host

Launch an Ubuntu EC2 instance. Configure its security group to allow:
- TCP 80 from `0.0.0.0/0` for public website traffic.
- TCP 22 only from a trusted source. GitHub-hosted runner IPs change; for production, consider a self-hosted runner, a managed secure access path, or a maintained allowlist.

Install Docker on the instance:

```bash
sudo apt update
sudo apt install -y docker.io
sudo systemctl enable --now docker
```

The deployment workflow runs Docker commands over SSH. The `EC2_USER` must be able to run Docker commands. For Ubuntu, configure Docker group membership and reconnect:

```bash
sudo usermod -aG docker ubuntu
```

Use a public Docker Hub repository for the simplest first deployment. If it is private, the workflow's Docker Hub credentials are used to log in on the EC2 host before pulling.

## Deploy

Push to `main`:

```bash
git init
git add .
git commit -m "Add CI/CD website"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/YOUR_REPOSITORY.git
git push -u origin main
```

Then check the **Actions** tab in GitHub. When the workflow succeeds, visit:

```text
http://YOUR_EC2_PUBLIC_IP
```

## Important production notes

- This is a beginner demonstration, not a hardened production deployment.
- Pin third-party GitHub Actions to reviewed full commit SHAs before production use.
- Restrict SSH access; do not expose port 22 to the entire internet.
- Consider HTTPS with a domain and TLS certificate, health checks, rollback strategy, image scanning and least-privilege credentials.
- The pipeline uses the `latest` tag for deployment and also publishes an immutable commit-SHA tag for traceability.
