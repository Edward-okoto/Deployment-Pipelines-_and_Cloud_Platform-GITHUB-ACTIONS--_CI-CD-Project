# Deployment-Pipelines-_and_Cloud_Platform-GITHUB-ACTIONS--_CI-CD-Project

Introduction to GitHub Actions:Deployment and Cloud Integration

### Introduction to Deployment Pipelines:

#### Objective :

Define and understand the stages of a deployment pipeline.

Learn about different deployment strategies.

**Defining Deployment Stages**

Development : Writing and Testing code in a local environment.

Integration : Merging code changes to a shared branch

Testing : Running automated test to check code quality.

Staging : Deploying code to a production-like environment for final testing.

Production : Releasing the final version of the code to the end-users.

**Understanding deployment Strategies**

Blue-Green deployment: Running two production environment of which only one serves end-users at a time.

Canary : Rolling out changes to a small sub-set of users before full deployment.

Rolling Deployment : Gradually replacing instances of the previous version of the application with the new version.


### Automated Releases and Versioning:

#### Objective :

Automate versioning in the CI/CD process.

Create and manage software releases.

**Automating versioning in CI/CD process**

1) Semantic Versioning : Use semantic versioning (SerVer) for your software.It uses a three-part version number `MAJOR.MINOR.PATCH`

2) Automated Versioning with GitHub: Implement automated versioning using GitHub Actions to increment version numbers automatically based on code changes.

Example snippet.

```yaml
name: Version Bump and Tagging
on:
  push:
    branches:
      - main

jobs:
  build:
    name: Generate Tag
    runs-on: ubuntu-latest
    steps:
      - name: Fetch Repository
        uses: actions/checkout@v2
        # Retrieves the repository content for use within the workflow.

      - name: Increment Version and Apply Tag
        uses: anothrNick/github-tag-action@1.26.0
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          DEFAULT_BUMP: patch
        # Automatically increments the patch version and tags the new commit.
        # Available options for 'DEFAULT_BUMP' are major, minor, or patch.
```

- This action will automatically increment the patch version and create a new tag each time changes are pushed to the main branch.


**Creating and Managing Releases**

a) Automating Releases With GitHub Actions.
- Set up a GitHub Actions to create a new release whenever a new tag is pushed to the repository.

Example snippet to create a release!!


```yaml
on:
  push:
    tags:
      - '*'

jobs:
  build:
    name: Generate Release
    runs-on: ubuntu-latest
    steps:
      - name: Fetch Code
        uses: actions/checkout@v2
        # Retrieves the repository's code associated with the tag that triggered the workflow.

      - name: Publish Release
        id: release_step
        uses: actions/create-release@v1
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        with:
          tag_name: ${{ github.ref }}
          release_name: Release ${{ github.ref }}
        # Generates a new GitHub release using the tag name and applies the specified release name.
```
   The `actions/create-release@v1` is a GitHub Action that facilitates creating a release in your repository. It’s particularly useful for automating release generation based on tags. 

### Deploying To Cloud Platform

Deploy application to popular cloud platform (AWS) using GitHub Actions.

Configure deployment environment.

Step 1.
- Chose a cloud platform (Like AWS)

Step 2.

###### Set-up GitHub Actions for Deployment.

- Create a workflow file: 

    a) Workflow files are YAML file stored in your repository `.github/workflows` directory.

    b) Create a file e.g `deploy-to-aws-yml`

- Defining the Workflow

    a) A Workflow is defined with a series of steps that run on specified events.
    
    Example of AWS deployment.

```yaml
name: AWS Deployment Workflow
on:
  push:
    branches:
      - main
  # Triggers the workflow when a commit is pushed to the 'main' branch.

jobs:
  deploy:
    name: Deploy Application to AWS
    runs-on: ubuntu-latest
    # Utilizes the latest Ubuntu environment for the deployment process.

    steps:
      - name: Checkout Repository
        uses: actions/checkout@v2
        # Retrieves the repository code under $GITHUB_WORKSPACE for deployment.

      - name: Configure AWS Credentials
        uses: aws-actions/configure-aws-credentials@v1
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: us-west-2
        # Sets up AWS credentials using values stored in GitHub secrets.

      - name: Execute AWS Deployment
        run: |
          # Insert your deployment commands or scripts here.
          # Example: Use the AWS CLI to deploy resources or services.
```
-This workflow deploys application to AWS when changes are pushed to the main branch.

**Configuring Deployment Environment**

a) Setting-up environment variables and secrets

- Store sensitive information like API keys and access tokens as github secrets.

- Use environment variables for non-sensitive configuration.

b) Environment specific workflow

- Tailor your workflow for different environment (development,staging and production) by using conditions or different workflow files.


PRACTICAL IMPLEMENTATION
---

### **1. Set Up Your Repository**

- **Step 1.1:** Create a GitHub repository or navigate to your existing one.
- **Step 1.2:** Clone the repository to your local machine:
  ```bash
  git clone <repository_url>
  cd <repository_name>
  ```

- **Step 1.3:** Add a `.github/workflows` directory for storing workflow files:
  ```bash
  mkdir -p .github/workflows
  ```
---

### **2. Implement Automated Releases and Versioning**

#### **Step 2.1: Automate Versioning in GitHub Actions**

- Create a workflow file for versioning at `.github/workflows/version-bump.yml`:
  ```yaml
  name: Version Bump and Tagging
  on:
    push:
      branches:
        - main

  jobs:
    build:
      name: Generate Tag
      runs-on: ubuntu-latest
      steps:
        - name: Fetch Repository
          uses: actions/checkout@v2

        - name: Increment Version and Apply Tag
          uses: anothrNick/github-tag-action@1.26.0
          env:
            GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
            DEFAULT_BUMP: patch
  ```

#### **Step 2.2: Automate Releases**
- Automate the creation of releases with `actions/create-release`.

- Create a workflow file at `.github/workflows/create-release.yml`:
  ```yaml
  on:
    push:
      tags:
        - '*'

  jobs:
    build:
      name: Generate Release
      runs-on: ubuntu-latest
      steps:
        - name: Fetch Code
          uses: actions/checkout@v2

        - name: Publish Release
          id: release_step
          uses: actions/create-release@v1
          env:
            GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          with:
            tag_name: ${{ github.ref }}
            release_name: Release ${{ github.ref }}
  ```

---

### **3. Deploy to AWS Using GitHub Actions**

#### **Step 3.1: Configure AWS Environment**
- Log into AWS and create an IAM user with sufficient privileges for deployment.
- Store the `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY` securely in GitHub secrets:
  - Go to your GitHub repository → **Settings → Secrets and variables → Actions**.
  - Add secrets like:
    - `AWS_ACCESS_KEY_ID`
    - `AWS_SECRET_ACCESS_KEY`

#### **Step 3.2: Write Deployment Workflow**
- Create a workflow file at `.github/workflows/deploy-to-aws.yml`:
  ```yaml
  name: AWS Deployment Workflow
  on:
    push:
      branches:
        - main

  jobs:
    deploy:
      name: Deploy Application to AWS
      runs-on: ubuntu-latest

      steps:
        - name: Checkout Repository
          uses: actions/checkout@v2

        - name: Configure AWS Credentials
          uses: aws-actions/configure-aws-credentials@v1
          with:
            aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
            aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
            aws-region: us-west-2

        - name: Execute AWS Deployment
          run: |
            # Add AWS CLI deployment commands here
            aws s3 sync ./your-code s3://your-bucket-name --delete
  ```

---

### **4. Configure Deployment Environment**

- Use **GitHub secrets** for sensitive information such as API keys.
- Specify environment-specific workflows:
  - Example: Separate `staging` and `production` workflows triggered by distinct branches.
  ```yaml
  on:
    push:
      branches:
        - staging  # For staging
        - production  # For production
  ```

---

### **5. Test and Monitor Pipelines**

- Test your workflow after each stage setup:
  - Push a commit to the branch and verify the corresponding workflow runs.
- Monitor deployments via GitHub Actions Logs for troubleshooting.

---









