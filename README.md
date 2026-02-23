# FastAPI Beyond CRUD 

This is the source code for the [FastAPI Beyond CRUD](https://youtube.com/playlist?list=PLEt8Tae2spYnHy378vMlPH--87cfeh33P&si=rl-08ktaRjcm2aIQ) course. The course focuses on FastAPI development concepts that go beyond the basic CRUD operations.

For more details, visit the project's [website](https://jod35.github.io/fastapi-beyond-crud-docs/site/).

## Table of Contents

1. [Getting Started](#getting-started)
2. [Prerequisites](#prerequisites)
3. [Project Setup](#project-setup)
4. [Running the Application](#running-the-application)
5. [Running Tests](#running-tests)
6. [CI/CD (GitHub Actions)](#cicd-github-actions)
7. [Contributing](#contributing)

## Getting Started
Follow the instructions below to set up and run your FastAPI project.

### Prerequisites
Ensure you have the following installed:

- Python >= 3.10
- PostgreSQL
- Redis

### Project Setup
1. Clone the project repository:
    ```bash
    git clone https://github.com/jod35/fastapi-beyond-CRUD.git
    ```
   
2. Navigate to the project directory:
    ```bash
    cd fastapi-beyond-CRUD/
    ```

3. Create and activate a virtual environment:
    ```bash
    python3 -m venv env
    source env/bin/activate
    ```

4. Install the required dependencies:
    ```bash
    pip install -r requirements.txt
    ```

5. Set up environment variables by copying the example configuration:
    ```bash
    cp .env.example .env
    ```

6. Run database migrations to initialize the database schema:
    ```bash
    alembic upgrade head
    ```

7. Open a new terminal and ensure your virtual environment is active. Start the Celery worker (Linux/Unix shell):
    ```bash
    sh runworker.sh
    ```

## Running the Application
Start the application:

```bash
fastapi dev src/
```
Alternatively, you can run the application using Docker:
```bash
docker compose up -d
```
## Running Tests
Run the tests using this command
```bash
pytest
```

## CI/CD (GitHub Actions)

This repository uses GitHub Actions for Conventional Commits enforcement and nightly builds.

### Conventional Commit enforcement

- **PR titles** must follow [Conventional Commits](https://www.conventionalcommits.org/). Examples: `feat: add user registration`, `fix(auth): handle expired token`, `docs: update README`.
- The workflow runs on every pull request (`.github/workflows/pr-conventional-commits.yml`). If the PR title does not match the convention, the workflow **closes the PR**, posts a comment explaining the requirement, and sends an **email notification** (via SendGrid) to the configured recipient.
- Implementation uses `amannn/action-semantic-pull-request` and the GitHub API to close non-compliant PRs.

### Nightly build

- Runs **nightly at 12:00 am America/Los Angeles** (scheduled as `0 8 * * *` UTC when on PST; during PDT you may want to use `0 7 * * *`).
- The workflow (`.github/workflows/nightly-build.yml`) runs **pytest** first. Only if all tests pass does it build the Docker image and push to the container registry.
- Images are pushed to **GitHub Container Registry (GHCR)**:
  - `ghcr.io/<owner>/fastapi-beyond-CRUD:nightly`
  - `ghcr.io/<owner>/fastapi-beyond-CRUD:nightly-YYYYMMDD`
- If the nightly run **fails** (e.g. tests fail), no image is pushed and an **email notification** is sent via SendGrid.
- You can also trigger the nightly pipeline manually via **Actions → Nightly - Test, Build, and Push Image → Run workflow**.

### Required secrets

Configure these in **Settings → Secrets and variables → Actions** (and ensure **Settings → Actions → General → Workflow permissions** is set to **Read and write** so workflows can close PRs and push to GHCR):

| Secret | Description |
|--------|-------------|
| `SENDGRID_API_KEY` | SendGrid API key for sending email notifications |
| `ALERT_EMAIL_TO` | Email address to receive failure alerts (e.g. you or instructor) |
| `ALERT_EMAIL_FROM` | Verified sender email in SendGrid |

### Summary of setup

- Forked the upstream repo (name unchanged).
- Added two GitHub Actions workflows: PR conventional commit enforcement (auto-close + email) and nightly test/build/push (email on failure).
- Configured repository permissions for Actions (write for PR closure and GHCR push).
- Added SendGrid secrets for email notifications.
- Documented CI/CD usage, required secrets, and image tags in this README.

## Contributing
I welcome contributions to improve the documentation! You can contribute [here](https://github.com/jod35/fastapi-beyond-crud-docs).
