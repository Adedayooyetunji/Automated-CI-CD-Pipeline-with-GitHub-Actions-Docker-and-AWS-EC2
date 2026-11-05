# PULSE CI/CD — Automated Website Deployment

A responsive static portfolio website deployed automatically to GitHub Pages with GitHub Actions.

**Live site:** [oyetunji-lab.expadox.com/Automated-CI-CD-Pipeline-with-GitHub-Actions-Docker-and-AWS-EC2](https://oyetunji-lab.expadox.com/Automated-CI-CD-Pipeline-with-GitHub-Actions-Docker-and-AWS-EC2/)

## Deployment

Every push to `main` triggers [`.github/workflows/deploy-pages.yml`](.github/workflows/deploy-pages.yml). The workflow copies `index.html`, `style.css` and `script.js` into a Pages artifact and deploys it to GitHub Pages. HTTPS is enforced for the live site.

To deploy your own fork, enable **Settings → Pages → Build and deployment → Source → GitHub Actions**. The workflow handles the rest; no AWS, Docker Hub or SSH secrets are required.

## Project structure

```text
.
├── .github/workflows/deploy-pages.yml
├── index.html
├── style.css
├── script.js
├── Dockerfile
├── .dockerignore
└── README.md
```

## Run locally

Open `index.html` in a browser, or use the VS Code Live Server extension.

## Run locally with Docker (optional)

The Dockerfile is retained for local experimentation; Docker is not used by the active GitHub Pages deployment.

```bash
docker build -t automated-cicd-website .
docker run --rm -p 8080:80 automated-cicd-website
```

Then visit <http://localhost:8080>.

## Hosting note

GitHub Pages is the current hosting provider. The previous AWS EC2 deployment is not active, so EC2-specific secrets and setup steps are not needed for this project’s current deployment.
