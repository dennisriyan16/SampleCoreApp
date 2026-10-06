# CI/CD Pipeline Analysis & Implementation Plan

## What is a CI/CD Pipeline?

**CI/CD** stands for **Continuous Integration** and **Continuous Deployment/Delivery**. It's a method to frequently deliver apps to customers by introducing automation into the stages of app development.

### 1. Continuous Integration (CI) - *Local to GitHub*
Continuous Integration focuses on automating the integration of code changes from multiple contributors into a single software project.
- **The Goal:** Detect integration bugs quickly and ensure the main codebase is always in a working state.
- **How it works:** When you push code from your local machine to GitHub, it automatically triggers a pipeline that:
  - Installs dependencies.
  - Runs automated tests (PHPUnit).

### 2. Continuous Delivery / Deployment (CD) - *GitHub to cPanel Server*
Continuous Deployment automates the release of the validated code from GitHub to your hosting environment.
- **How it works with cPanel:** **Yes, the same `.yml` file handles this!** We define multiple "jobs" inside the same GitHub Actions file. The first job tests the code. If (and only if) the tests pass, the second job uses FTP/SFTP credentials to automatically push the updated files from GitHub directly to your cPanel server.

---

## How the Pipeline Flow Works (The Same YML File)

1. **You Push Code:** You push your local changes to GitHub.
2. **Job 1: Build & Test (CI):** GitHub Actions spins up a temporary server, installs PHP & CodeIgniter dependencies, and runs your tests.
3. **Job 2: Deploy to cPanel (CD):** If Job 1 is successful, GitHub Actions uses an FTP deployment action. It takes the successful code and uploads it to your cPanel `public_html` (or designated domain folder).

---

## Detailed Implementation Plan for QuickChat

### Phase 1: Create the GitHub Actions Workflow File
We will create a single file `.github/workflows/main.yml` in your repository. This file will contain two main jobs:

#### Job 1: `build-and-test`
- **Checkout Code:** Pull the latest code from the repository.
- **Setup PHP:** Install the required PHP version.
- **Install Dependencies:** Run `composer install`.
- **Run Tests:** Execute PHPUnit tests to ensure nothing is broken.

#### Job 2: `deploy-to-cpanel`
- **Dependency:** This job will have a `needs: build-and-test` rule, meaning it will **abort** if the tests fail.
- **FTP Deployment:** We will use a pre-built action like `SamKirkland/FTP-Deploy-Action`. 
- **Configuration:** It will connect to your cPanel using FTP credentials.

### Phase 2: Setup GitHub Secrets for cPanel
To keep your server secure, we will **never** hardcode your passwords in the `.yml` file. Instead, you will go to your GitHub Repository Settings -> Secrets and add:
- `FTP_SERVER`: Your cPanel FTP server address (e.g., `ftp.yourdomain.com`)
- `FTP_USERNAME`: Your cPanel FTP username
- `FTP_PASSWORD`: Your cPanel FTP password

### Phase 3: Testing the Pipeline
Once we commit the `.yml` file:
1. GitHub Actions will instantly start running the tests.
2. If tests pass, it will log into your cPanel via FTP and upload the files.
3. You can watch this happen live in the "Actions" tab on your GitHub repository.

---

## Next Steps

Does this clarify how the code travels from GitHub to your cPanel server? If this updated plan makes sense and you are ready to proceed, click **Proceed**, and we will start creating the `.github/workflows/main.yml` file!
