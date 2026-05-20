# GitHub Actions - Reusable Terraform / OpenTofu Workflows

<!-- LOGO -->
<picture>
  <source media="(prefers-color-scheme: dark)" srcset="https://nuvibit.com/images/logo/logo-nuvibit-banner.png">
  <source media="(prefers-color-scheme: light)" srcset="https://nuvibit.com/images/logo/logo-nuvibit-banner-dark.png">
  <img alt="Nuvibit - Sovereign AWS Platforms" src="https://nuvibit.com/images/logo/logo-nuvibit-banner.png" width="400">
</picture>
<br/>
<br/>

<!-- DESCRIPTION -->
Reusable [GitHub Workflows](https://docs.github.com/en/actions/learn-github-actions/) to run state-of-the-art Terraform and OpenTofu pipelines with flexible deployment strategies. 
Built by **Nuvibit**, the leading specialist in **sovereign AWS platforms** and creator of the **Nuvibit Terraform Collection (NTC)**.

## 🚀 **Two Workflow Categories**

This collection provides workflows for **two distinct Infrastructure as Code scenarios**:

### **📦 Infrastructure Deployment Workflows**
For deploying and managing infrastructure on AWS using Terraform/OpenTofu:

- **Primary Workflow**: `terraform-stack.yml`
- **Purpose**: Deploy infrastructure changes to cloud environments
- **Target**: Infrastructure repositories, environment-specific deployments
- **Features**: Quality gates, optional plan/apply execution, multi-environment support

### **🔧 Module Development Workflows**  
For developing, testing, and releasing reusable Terraform modules:

- **Workflows**: `terraform-module-test.yml` + `terraform-module-release.yml`
- **Purpose**: Automated testing with Terratest and semantic versioning releases
- **Target**: Terraform module repositories, library development
- **Features**: OIDC authentication, matrix testing with dynamic provider versions, automated releases

## 🔄 **Deployment Approaches (Infrastructure Workflows)**

The infrastructure deployment workflows support **two distinct deployment patterns**:

### **1. 🔧 Hybrid Approach - Static Checks + External IaC Platform**
Perfect for teams using dedicated IaC management platforms while maintaining quality gates in GitHub:

- **GitHub Actions Role**: Performs all static code quality checks (format, docs, lint, security)
- **External Platform Role**: Handles terraform plan/apply execution  
- **Supported Platforms**: [Terraform Cloud](https://cloud.hashicorp.com/products/terraform), [Spacelift](https://spacelift.io/), [Atlantis](https://www.runatlantis.io/), and other IaC platforms
- **Benefits**: Leverage specialized IaC tooling while maintaining code quality standards
- **Configuration**: Set `enable_terraform_execution: false` (default)

### **2. 🔄 Complete CI/CD - Full GitHub Actions Pipeline**  
Ideal for teams wanting a complete Infrastructure as Code pipeline within GitHub Actions:

- **GitHub Actions Role**: Handles everything - quality checks AND terraform execution
- **Deployment Flow**: Plan on PR → Apply on merge to main branch
- **Features**: Plan/apply summaries in workflow results, automated execution, full workflow visibility
- **Benefits**: Single platform for all IaC operations, simplified toolchain
- **Configuration**: Set `enable_terraform_execution: true`

Both approaches support **Terraform** and **OpenTofu**, provide comprehensive quality gates, and integrate seamlessly with cloud providers for infrastructure deployment.
<br><br>

## Quick Start
Choose the appropriate workflow category for your use case:

### **📦 Infrastructure Deployment**
- [Terraform Stack Workflow](#terraform-stack-workflow) - Deploy infrastructure with quality gates and optional execution

### **🔧 Module Development**  
- [Terraform Module Test Workflow](#terraform-module-test-workflow) - Automated testing with Terratest
- [Terraform Module Release Workflow](#terraform-module-release-workflow) - Automated semantic versioning and releases
<br><br>

## 🔒 **Enhanced Security Model**

All workflows use **GitHub Apps** instead of the default `GITHUB_TOKEN` for enhanced security:

### **Why GitHub Apps?**
- **Scoped Permissions**: Apps can be granted specific, minimal permissions
- **No User Association**: Not tied to individual user accounts  
- **Audit Trail**: All actions are logged and attributed to the app
- **Token Refresh**: Short-lived tokens that refresh automatically
- **Repository-Specific**: Access can be limited to specific repositories

### **Required Setup**
1. **Create a GitHub App** in your organization settings
2. **Grant Permissions**: `contents: write`, `pull-requests: write`, `issues: write`, `checks: write`
3. **Install App**: Install on repositories / organization where workflows will run
4. **Set Secrets**: Store App ID and private key as repository / organization secrets

### **Private Module Access**
For accessing private Terraform modules hosted on Github, create a separate GitHub App with:
- **Permissions**: `contents: read`, `metadata: read` 
- **Repository Access**: Only the module repositories needed
- **Principle of Least Privilege**: Minimal access for maximum security

## Configuration Repositories (optional)
- [`github-tflint-config`](https://github.com/nuvibit/github-tflint-config)
- [`github-terratest-config`](https://github.com/nuvibit/github-terratest-config)
- [`github-terraform-semantic-release-config`](https://github.com/nuvibit/github-terraform-semantic-release-config)
<br><br>

## Referenced Github Actions
The reusable Github Workflows include the following public Github Actions:

- [`@actions/checkout`](https://github.com/actions/checkout)
- [`@actions/setup-go`](https://github.com/actions/setup-go)
- [`@actions/create-github-app-token`](https://github.com/actions/create-github-app-token)
- [`@ad-m/github-push-action`](https://github.com/ad-m/github-push-action)
- [`@aws-actions/configure-aws-credentials`](https://github.com/aws-actions/configure-aws-credentials)
- [`@cycjimmy/semantic-release-action`](https://github.com/cycjimmy/semantic-release-action)
- [`@hashicorp/setup-terraform`](https://github.com/hashicorp/setup-terraform)
- [`@opentofu/setup-opentofu`](https://github.com/opentofu/setup-opentofu)
- [`@reviewdog/action-tflint`](https://github.com/reviewdog/action-tflint)
- [`@reviewdog/action-trivy`](https://github.com/reviewdog/action-trivy)
- [`@terraform-docs/gh-actions`](https://github.com/terraform-docs/gh-actions)
- [`@terraform-linters/tflint-load-config-action`](https://github.com/terraform-linters/tflint-load-config-action)

In addition to these Github Actions, custom bash scripts are run to avoid using [unverified actions](https://docs.github.com/de/apps/publishing-apps-to-github-marketplace/github-marketplace-overview/about-marketplace-badges#for-github-actions).


>## Terraform Stack Workflow
* **Purpose**: Deploy and manage infrastructure on AWS using Terraform/OpenTofu
* **Target**: Infrastructure repositories, environment-specific deployments, cloud resource management
* This workflow provides a complete Terraform/OpenTofu CI/CD pipeline with quality gates
* Supports both Terraform and OpenTofu  
* **Flexible deployment modes**: Static checks only OR complete CI/CD with execution
* Optional terraform plan on pull requests and apply on push to default branch

### 🎯 **Use Case Selection**

**Static Checks Mode** (`enable_terraform_execution: false`)
- Use when leveraging external IaC platforms (Terraform Cloud, Spacelift, etc.)
- GitHub Actions handles quality assurance (format, docs, lint, security)
- External platform handles terraform plan/apply execution
- Ideal for enterprise environments with dedicated IaC tooling

**Complete CI/CD Mode** (`enable_terraform_execution: true`)  
- Use for full GitHub Actions-based Infrastructure as Code pipeline
- All operations (quality checks + terraform execution) in GitHub Actions
- Plan results posted as PR comments, apply on merge to main
- Perfect for teams wanting unified workflow platform

### :white_check_mark: Features
* **Terraform/OpenTofu Support**: Seamless switching between tools
* **Quality Gates**: Format, documentation, lint, and security checks
* **Optional Execution**: Enable terraform plan/apply with simple toggle
* **GitHub App Authentication**: Enhanced security with scoped tokens instead of GITHUB_TOKEN
* **Private Module Support**: Access private Terraform modules via GitHub App authentication
* **Private Registry Support**: Support for private Terraform module registries
* **AWS OIDC Integration**: Secure, keyless authentication with AWS using OpenID Connect
* **Workflow Summaries**: Plan and apply results displayed in GitHub workflow summaries
* **Branch Protection**: Apply only runs on specified default branch
* **Flexible Configuration**: Extensive customization options

### Workflow Steps
The Terraform Stack workflow consists of the following steps:

`Always Runs`
1. **Terraform Format** - Code formatting with auto-commit
2. **Terraform Docs** - Documentation generation (can be disabled with `enable_terraform_docs: false`)
3. **Terraform Lint** - Static code analysis with TFLint
4. **Terraform Security** - Security scanning with Trivy

`Optional Steps (when enabled)`\
5. **Terraform Plan** - On pull requests (results in workflow summary)\
6. **Terraform Apply** - On push to default branch (auto-approve)\
7. **Workflow Summary** - Centralized status reporting with skip indicators\


### Inputs [Terraform Stack Workflow]
| Name | Description | Default | Required |
|------|-------------|---------|----------|
| `github_runner` | Name of GitHub-hosted runner or self-hosted runner | `ubuntu-latest` | false |
| `use_opentofu` | Use OpenTofu instead of Terraform | `false` | false |
| `enable_terraform_docs` | Enable terraform-docs to generate documentation | `true` | false |
| `enable_terraform_execution` | Enable terraform plan on pull requests and apply on push to default branch | `false` | false |
| `aws_default_region` | Default AWS region to use for terraform execution | `eu-central-1` | false |
| `aws_oidc_role_arn` | AWS OIDC role ARN to assume for terraform execution | `""` | false |
| `terraform_version` | Terraform/OpenTofu version used inside github action | `latest` | false |
| `terraform_working_directory` | A relative path starting with '.' that Terraform will execute within | `.` | false |
| `terraform_modules_auth` | Authentication method for private modules (none/github-app) | `none` | false |
| `terraform_modules_github_owner` | GitHub owner/organization name for private modules | `""` | false |
| `terraform_registry_hostname` | Hostname of Terraform module registry (public or private) | `app.terraform.io` | false |
| `tflint_repo` | Public repo where tflint config is stored | `nuvibit/github-tflint-config` | false |
| `tflint_repo_config_path` | Path to tflint config in tflint_repo | `aws/.tflint.hcl` | false |
| `tflint_repo_ref` | Ref or branch of tflint_repo | `main` | false |
| `tflint_version` | Tflint version to use in github action | `latest` | false |
| `trivy_version` | Trivy version to use in github action | `latest` | false |
| `commit_user` | Username which should be used for commits by github action | `github-actions` | false |
| `commit_email` | Email which should be used for commits by github action | `noreply@github.com` | false |
| `concurrency_group` | Name of concurrency group to manage concurrent github action runs | Auto-generated | false |
| `default_branch` | Default branch name for terraform apply | `main` | false |

### Secrets [Terraform Stack Workflow]
| Name | Description | Required |
|------|-------------|----------|
| `GH_APP_ID` | GitHub App ID for enhanced authentication (replaces GITHUB_TOKEN) | true |
| `GH_APP_PRIVATE_KEY` | GitHub App private key for enhanced authentication | true |
| `TERRAFORM_MODULES_APP_ID` | GitHub App ID for accessing private Terraform modules | false |
| `TERRAFORM_MODULES_APP_PRIVATE_KEY` | GitHub App private key for accessing private modules | false |
| `TERRAFORM_REGISTRY_TOKEN` | Token for accessing private Terraform module registry | false |
<br>

### Usage [Terraform Stack Workflow - Static Checks Only]
*Perfect for teams using Terraform Cloud, Spacelift, or other IaC platforms*

```yaml
name: TERRAFORM STACK

on:
  pull_request:
    branches:
      - main
  push:
    branches:
      - main

jobs:
  terraform-stack:
    uses: nuvibit/github-terraform-workflows/.github/workflows/terraform-stack.yml@v2
    with:
      enable_terraform_execution: false # (default) Run static checks only
      tflint_repo_config_path: "aws/.tflint.hcl"
    secrets:
      GH_APP_ID: ${{ secrets.GH_APP_ID }}
      GH_APP_PRIVATE_KEY: ${{ secrets.GH_APP_PRIVATE_KEY }}
```

### Usage [Terraform Stack Workflow - Complete CI/CD]
*Full GitHub Actions pipeline with terraform plan/apply*

```yaml
name: TERRAFORM STACK

on:
  pull_request:
    branches:
      - main
  push:
    branches:
      - main

jobs:
  terraform-stack:
    uses: nuvibit/github-terraform-workflows/.github/workflows/terraform-stack.yml@v2
    with:
      enable_terraform_execution: true  # Enable complete CI/CD mode
      aws_oidc_role_arn: ${{ secrets.AWS_OIDC_ROLE_ARN }}
      default_branch: "main"
      tflint_repo_config_path: "aws/.tflint.hcl"
    secrets:
      GH_APP_ID: ${{ secrets.GH_APP_ID }}
      GH_APP_PRIVATE_KEY: ${{ secrets.GH_APP_PRIVATE_KEY }}
```

### Usage [OpenTofu Stack Workflow - Complete CI/CD]
*OpenTofu with full CI/CD pipeline*
```yaml
name: OPENTOFU STACK

on:
  pull_request:
    branches:
      - main
  push:
    branches:
      - main

jobs:
  opentofu-stack:
    uses: nuvibit/github-terraform-workflows/.github/workflows/terraform-stack.yml@v2
    with:
      use_opentofu: true
      enable_terraform_execution: true
      aws_oidc_role_arn: ${{ secrets.AWS_OIDC_ROLE_ARN }}
      terraform_version: "1.8.0"
      terraform_working_directory: "./infrastructure"
    secrets:
      GH_APP_ID: ${{ secrets.GH_APP_ID }}
      GH_APP_PRIVATE_KEY: ${{ secrets.GH_APP_PRIVATE_KEY }}
```

### Usage [Private Modules & Registry Example]
*Using private Terraform modules and private registry*

```yaml
name: TERRAFORM STACK

on:
  pull_request:
    branches: [main]
  push:
    branches: [main]

jobs:
  terraform-stack:
    uses: nuvibit/github-terraform-workflows/.github/workflows/terraform-stack.yml@v2
    with:
      enable_terraform_execution: true
      terraform_modules_auth: "github-app"
      terraform_modules_github_owner: "your-org"
      terraform_registry_hostname: "registry.example.com"
      aws_oidc_role_arn: ${{ secrets.AWS_OIDC_ROLE_ARN }}
    secrets:
      GH_APP_ID: ${{ secrets.GH_APP_ID }}
      GH_APP_PRIVATE_KEY: ${{ secrets.GH_APP_PRIVATE_KEY }}
      TERRAFORM_MODULES_APP_ID: ${{ secrets.TERRAFORM_MODULES_APP_ID }}
      TERRAFORM_MODULES_APP_PRIVATE_KEY: ${{ secrets.TERRAFORM_MODULES_APP_PRIVATE_KEY }}
      TERRAFORM_REGISTRY_TOKEN: ${{ secrets.TERRAFORM_REGISTRY_TOKEN }}
```

>## Terraform Module Test Workflow  
* **Purpose**: Automated testing and validation of reusable Terraform modules
* **Target**: Terraform module repositories, library development, module marketplaces
* This workflow runs comprehensive testing for Terraform modules using [Terratest](https://terratest.gruntwork.io/docs/getting-started/quick-start/)
* Includes format, documentation, lint, security, and integration testing
* Supports both Terraform and OpenTofu with OIDC authentication for secure cloud testing

### :white_check_mark: Features
* **OIDC Authentication**: Secure, keyless authentication with AWS using OpenID Connect
* **GitHub App Authentication**: Enhanced security with scoped tokens instead of GITHUB_TOKEN
* **Private Module Support**: Access private Terraform modules via GitHub App authentication
* **Private Registry Support**: Support for private Terraform module registries  
* **Matrix Testing**: Support for testing multiple Terraform/OpenTofu versions and provider combinations
* **Dynamic Provider Versions**: Automatic resolution of provider versions from requirements or latest versions
* **Quality Gates**: Format, docs, lint, and security checks before integration testing
* **Terratest Integration**: Comprehensive integration testing with Go-based Terratest framework
* **Workflow Summaries**: Detailed test results displayed in GitHub workflow summaries
* **Parallel Testing**: Configurable parallel test execution with `terratest_max_parallel`
* **Automated Formatting**: Auto-commits formatting and documentation changes
* **Comprehensive Reporting**: Detailed test summaries and workflow status in GitHub Actions

### Workflow Steps
`Sequential Pipeline - Each step depends on previous success`
1. **Terraform Format** - Code formatting with auto-commit to PR branch
2. **Terraform Docs** - Documentation generation for modules and submodules
3. **Terraform Lint** - Static code analysis with configurable TFLint rules
4. **Terraform Security** - Security scanning with Trivy on examples directory
5. **Terratest Config** - Dynamic matrix generation from configuration repository
6. **Terratest Run** - Integration testing with real infrastructure using OIDC
7. **Workflow Summary** - Centralized status reporting with visual indicators

### Inputs [Terraform Module Test Workflow]
| Name | Description | Default | Required |
|------|-------------|---------|----------|
| `github_runner` | Name of GitHub-hosted runner or self-hosted runner | `ubuntu-latest` | false |
| `use_opentofu` | Use OpenTofu instead of Terraform to format the code | `false` | false |
| `terraform_version` | Terraform version used for formatting and linting | `latest` | false |
| `terraform_requirements_file` | File where terraform requirements are defined | `main.tf` | false |
| `terraform_registry_hostname` | Hostname for terraform registry used to download providers | `registry.terraform.io` | false |
| `aws_default_region` | Default AWS region to use for Terratest | `eu-central-1` | false |
| `aws_oidc_role_arn` | AWS OIDC role ARN to assume for Terratest | `""` | **true** |
| `terratest_version` | Terratest version | `v0.48.0` | false |
| `terratest_path` | Path to terratest directory | `test` | false |
| `terratest_examples_path` | Path to terratest examples directory | `examples` | false |
| `terratest_max_parallel` | Maximum number of terratest runs that should run simultaneously | `1` | false |
| `terratest_config_repo` | Public repo where terratest matrix json is stored | `nuvibit/github-terratest-config` | false |
| `terratest_config_repo_ref` | Ref or branch of terratest_config_repo | `main` | false |
| `terratest_config_repo_path` | Path to terratest matrix json config in terratest_config_repo | `aws/matrix.json` | false |
| `tflint_repo` | Public repo where tflint config is stored | `nuvibit/github-tflint-config` | false |
| `tflint_repo_config_path` | Path to tflint config in tflint_repo | `aws/.tflint.hcl` | false |
| `tflint_repo_ref` | Ref or branch of tflint_repo | `main` | false |
| `tflint_version` | Tflint version to use | `latest` | false |
| `trivy_version` | Trivy version to use | `v0.61.1` | false |
| `commit_user` | Username which should be used for commits by github action | `github-actions` | false |
| `commit_email` | Email which should be used for commits by github action | `noreply@github.com` | false |
| `concurrency_group` | Name of concurrency group to manage concurrent github action runs | Auto-generated | false |

### Secrets [Terraform Module Test Workflow]
| Name | Description | Required |
|------|-------------|----------|
| `SPACELIFT_API_KEY_ENDPOINT` | Spacelift API endpoint for integration testing | false |
| `SPACELIFT_API_KEY_ID` | Spacelift API key ID for authentication | false |
| `SPACELIFT_API_KEY_SECRET` | Spacelift API key secret for authentication | false |

**Note**: This workflow uses **OIDC authentication** with AWS, eliminating the need for long-lived AWS credentials. Configure your AWS OIDC provider and specify the role ARN in `aws_oidc_role_arn`.

### Usage [Terraform Module Test Workflow]
```yaml
name: TERRAFORM MODULE TEST

on:
  pull_request:
    branches:
      - main

jobs:
  terraform-module-test:
    uses: nuvibit/github-terraform-workflows/.github/workflows/terraform-module-test.yml@v1
    with:
      # Required: AWS OIDC role for secure authentication
      aws_oidc_role_arn: ${{ vars.AWS_OIDC_ROLE_ARN }}
      
      # Optional: Customize linting configuration
      tflint_repo: "nuvibit/github-tflint-config"
      tflint_repo_config_path: "aws/.tflint.hcl"
      
      # Optional: Customize testing configuration
      terratest_version: "v0.48.0"
      terratest_max_parallel: 2
      
      # Optional: Use OpenTofu instead of Terraform
      # use_opentofu: true
      # terraform_version: "1.8.0"
    secrets:
      # Optional: For Spacelift integration
      SPACELIFT_API_KEY_ENDPOINT: ${{ secrets.SPACELIFT_API_KEY_ENDPOINT }}
      SPACELIFT_API_KEY_ID: ${{ secrets.SPACELIFT_API_KEY_ID }}
      SPACELIFT_API_KEY_SECRET: ${{ secrets.SPACELIFT_API_KEY_SECRET }}
```

### Usage [OpenTofu Module Test Workflow]
```yaml
name: OPENTOFU MODULE TEST

on:
  pull_request:
    branches:
      - main

jobs:
  opentofu-module-test:
    uses: nuvibit/github-terraform-workflows/.github/workflows/terraform-module-test.yml@v1
    with:
      use_opentofu: true
      terraform_version: "1.8.0"
      aws_oidc_role_arn: ${{ vars.AWS_OIDC_ROLE_ARN }}
      tflint_repo: "nuvibit/github-tflint-config"
      tflint_repo_config_path: "aws/.tflint.hcl"
```

>## Terraform Module Release Workflow
* **Purpose**: Automated versioning and releasing of Terraform modules
* **Target**: Terraform module repositories, library maintenance, version management
* This workflow automatically releases Terraform modules with [semantic versioning](https://github.com/semantic-release/semantic-release#-semantic-release)  
* Integrates with conventional commit standards
* Manages branch protection and changelog generation

### :white_check_mark: Features
* **Semantic Versioning**: Automated version bumping based on commit messages
* **Changelog Generation**: Automatic changelog updates
* **Branch Protection**: Temporary bypass for release commits
* **GitHub Releases**: Automated release creation with tags
* **Conventional Commits**: Follows conventional commit standards

### Workflow Steps
`On Push to Main Branch`
1. **Checkout** - Repository checkout
2. **Branch Protection Toggle** - Temporarily disable if enabled
3. **Semantic Release** - Analyze commits and create release
4. **Branch Protection Restore** - Re-enable branch protection

### Inputs [Terraform Module Release Workflow]
| Name | Description | Default | Required |
|------|-------------|---------|----------|
| `github_runner` | Name of GitHub-hosted runner or self-hosted runner | `ubuntu-latest` | false |
| `toggle_branch_protection` | Temporary disable branch protection to allow release action to push updates | `true` | false |
| `semantic_version` | Specify version range for semantic-release | `18.0.0` | false |
| `semantic_release_config` | Shareable config to create release of Terraform Modules | `@nuvibit/github-terraform-semantic-release-config` | false |
| `release_branch` | Name of branch on which Terraform Module release should happen | `main` | false |
| `concurrency_group` | Name of concurrency group to manage concurrent github action runs | Auto-generated | false |

### Usage [Terraform Module Release Workflow]
```yaml
name: TERRAFORM MODULE RELEASE

on:
  push:
    branches:
      - main

jobs:
  terraform-module-release:
    uses: nuvibit/github-terraform-workflows/.github/workflows/terraform-module-release.yml@v1
```

### Usage [Complete Module Workflow - Test + Release]
```yaml
name: TERRAFORM MODULE

on:
  pull_request:
    branches:
      - main
  push:
    branches:
      - main

jobs:
  terraform-module-test:
    if: ${{ github.event_name == 'pull_request' }}
    uses: nuvibit/github-terraform-workflows/.github/workflows/terraform-module-test.yml@v1
    with:
      aws_oidc_role_arn: ${{ vars.AWS_OIDC_ROLE_ARN }}
      tflint_repo: "nuvibit/github-tflint-config"
      tflint_repo_config_path: "aws/.tflint.hcl"
    secrets:
      # spacelift credentials used to test spacelift administration
      SPACELIFT_API_KEY_ENDPOINT: ${{ secrets.SPACELIFT_API_KEY_ENDPOINT }}
      SPACELIFT_API_KEY_ID: ${{ secrets.SPACELIFT_API_KEY_ID }}
      SPACELIFT_API_KEY_SECRET: ${{ secrets.SPACELIFT_API_KEY_SECRET }}

  terraform-module-release:
    if: ${{ github.event_name == 'push' }}
    uses: nuvibit/github-terraform-workflows/.github/workflows/terraform-module-release.yml@v1
```

<!-- AUTHORS -->
## Authors
This collection is maintained by [Nuvibit](https://nuvibit.com) with help from [these amazing contributors](https://github.com/nuvibit/github-terraform-workflows/graphs/contributors)

<!-- LICENSE -->
## License
This collection is licensed under Apache 2.0
<br />
See [LICENSE](https://github.com/nuvibit/github-terraform-workflows/tree/master/LICENSE) for full details

<!-- COPYRIGHT -->
<br />
<br />
<p align="center">Copyright &copy; Nuvibit AG</p>

# ECR Build-Scan-Push Workflow

**Available starting v5 of the workflows.**

A reusable GitHub Actions workflow that builds, scans, and pushes Docker images to Amazon Elastic Container Registry (ECR) with integrated security scanning using Amazon Inspector.

## Overview

This workflow automates the process of:
1. Building a Docker image from your repository
2. Scanning the image for vulnerabilities using Amazon Inspector
3. Pushing the image to ECR if security thresholds are met

## Features

- ✅ Configurable Docker build context and Dockerfile path
- ✅ Automated vulnerability scanning with Amazon Inspector
- ✅ Customizable security thresholds (critical, high, medium, low)
- ✅ Suppress specific findings via `.inspector-ignore` file or `ignore_findings` input
- ✅ OIDC authentication with AWS (no long-lived credentials)
- ✅ Reusable across multiple repositories
- ✅ Detailed vulnerability reports in Markdown format

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `dockerfile` | Path to the Dockerfile | No | `Dockerfile` |
| `build-context` | Docker build context path | No | `.` |
| `ecr-repository` | ECR repository name | **Yes** | - |
| `image-tag` | Target ECR image tag | **Yes** | - |
| `aws-region` | AWS region for ECR operations | No | `eu-central-1` |

## Outputs

| Output | Description |
|--------|-------------|
| `image-tag` | The image tag that was pushed to ECR |

## Secrets

| Secret | Description | Required |
|--------|-------------|----------|
| `aws-role` | AWS IAM role ARN to assume for AWS access | **Yes** |

## Prerequisites

### 1. AWS IAM Role Setup

The required AWS IAM Role can be created using the [SFOE-prometheon/terraform-aws-artefact-store](https://github.com/SFOE-prometheon/terraform-aws-artefact-store) by setting your workload repository as the module variable `associated_github_repo_name`. The module creates an IAM role with the required policy attachments, including AWS Inspector access and ECR permissions.

### 2. GitHub Secrets

Add the AWS role ARN to your workload repository secrets.

### 3. ECR Repository

An ECR repository must exist in your AWS account with the name specified in the `ecr-repository` input.

## Vulnerability Scanning

The workflow uses [Amazon Inspector via the aws-actions/vulnerability-scan-github-action-for-amazon-inspector action](https://github.com/aws-actions/vulnerability-scan-github-action-for-amazon-inspector) to scan container images with the following default thresholds:

| Severity | Threshold | Behavior |
|----------|-----------|----------|
| Critical | 1 | Fails if ≥1 critical vulnerability found |
| High | 1 | Fails if ≥1 high vulnerability found |
| Medium | 0 | Does not fail the build |
| Low | 0 | Does not fail the build |
| Other | 0 | Does not fail the build |

If any threshold is exceeded, the workflow will:
1. Display the vulnerability findings in Markdown format
2. Fail the workflow before pushing to ECR
3. Prevent vulnerable images from being deployed

### Ignoring Findings

Specific findings can be suppressed so they don't count towards thresholds or appear in reports. Place an `.inspector-ignore` plain-text file at the root of your repository with one CVE/finding ID per line. Lines starting with `#` are treated as comments.

```
# Ignore known false positives
CVE-2021-12345
CVE-2022-67890
# GHSA IDs are also supported
GHSA-mqqf-5wvp-8fh8
```

See the [aws-actions repo](https://github.com/aws-actions/vulnerability-scan-github-action-for-amazon-inspector) for full documentation on the `ignore_findings` input and other options.

## Workflow Steps

1. **Checkout repository**: Clones the repository code
2. **Configure AWS credentials**: Assumes the IAM role via OIDC
3. **Build Docker image**: Creates the Docker image locally
4. **Scan with Inspector**: Analyzes the image for vulnerabilities
5. **Display results**: Shows vulnerability findings in Markdown format
6. **Threshold check**: Fails if security thresholds are exceeded
7. **Login to ECR**: Authenticates with Amazon ECR
8. **Tag and push**: Pushes the image to ECR if all checks pass

## Permissions

The workflow requires the following GitHub permissions:

```yaml
permissions:
  id-token: write   # Required for OIDC authentication
  contents: read    # Required to checkout the repository
```

## Usage Examples

### Basic Usage

```yaml
build-scan-push:
  uses: SFOE-prometheon/github-terraform-workflows/.github/workflows/ecr-build-scan-push.yml@v5
  with:
    ecr-repository: workload-artefact-store
    image-tag: ${{ github.event.release.tag_name }}
  secrets:
    aws-role: ${{ secrets.SFOE_WORKLOAD_PIPELINE_ROLE_DEV }}
```

### Custom Dockerfile and Build Context

```yaml
build-scan-push:
  uses: SFOE-prometheon/github-terraform-workflows/.github/workflows/ecr-build-scan-push.yml@v5
  with:
    dockerfile: docker/Dockerfile.production
    build-context: ./app
    ecr-repository: workload-artefact-store
    image-tag: v1.2.3
    aws-region: us-east-1
  secrets:
    aws-role: ${{ secrets.SFOE_WORKLOAD_PIPELINE_ROLE_PROD }}
```

### Multi-Environment Pipeline

```yaml
name: Multi-Environment Deployment

on:
  release:
    types: [published]

jobs:
  build-dev:
    uses: SFOE-prometheon/github-terraform-workflows/.github/workflows/ecr-build-scan-push.yml@v5
    with:
      ecr-repository: my-app-dev
      image-tag: ${{ github.event.release.tag_name }}
    secrets:
      aws-role: ${{ secrets.SFOE_WORKLOAD_PIPELINE_ROLE_DEV }}

  build-prod:
    uses: SFOE-prometheon/github-terraform-workflows/.github/workflows/ecr-build-scan-push.yml@v5
    with:
      ecr-repository: my-app-prod
      image-tag: ${{ github.event.release.tag_name }}
    secrets:
      aws-role: ${{ secrets.SFOE_WORKLOAD_PIPELINE_ROLE_PROD }}
```

# S3 Scan and Push Workflow

**Available starting v5 of the workflows.**

A reusable GitHub Actions workflow that downloads build artifacts, scans them for vulnerabilities, and pushes them to Amazon S3 with integrated security scanning using Amazon Inspector.

## Overview

This workflow automates the process of:
1. Downloading a build artifact from GitHub Actions
2. Scanning the artifact or repository for vulnerabilities using Amazon Inspector
3. Uploading the artifact to S3 if security thresholds are met

## Features

- ✅ Support for multiple artifact types (.zip, .tar, .tar.gz)
- ✅ Dual scan modes: archive (scans the artifact) or repository (scans source code)
- ✅ Automated vulnerability scanning with Amazon Inspector
- ✅ Customizable security thresholds (critical, high, medium, low)
- ✅ OIDC authentication with AWS (no long-lived credentials)
- ✅ Reusable across multiple repositories
- ✅ Detailed vulnerability reports in Markdown format
- ✅ Flexible S3 key naming

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `artifact-name` | Name of the uploaded artifact to download | **Yes** | - |
| `artifact-type` | File type of the artifact (`.zip`, `.tar`, `.tar.gz`) | No | `.zip` |
| `s3-artifact-name` | Base name used for the S3 Key (defaults to artifact-name if not set) | No | `''` |
| `tag-name` | Tag name for the artifact (e.g., `latest` or a release tag) | **Yes** | - |
| `s3-bucket` | S3 bucket name for artifact storage | **Yes** | - |
| `aws-region` | AWS region for S3 operations | No | `eu-central-1` |
| `scan-type` | Inspector scan type: `archive` scans the artifact, `repository` scans the checked-out repo | No | `repository` |

## Outputs

| Output | Description |
|--------|-------------|
| `artifact-tag` | Tag name used for the artifact |
| `s3-key` | S3 key of the uploaded artifact |

## Secrets

| Secret | Description | Required |
|--------|-------------|----------|
| `aws-role` | AWS IAM role ARN to assume for AWS access | **Yes** |

## Prerequisites

### 1. AWS IAM Role Setup

The required AWS IAM Role can be created using the [SFOE-prometheon/terraform-aws-artefact-store](https://github.com/SFOE-prometheon/terraform-aws-artefact-store) by setting your workload repository as the module variable `associated_github_repo_name`. The module creates an IAM role with the required policy attachments, including AWS Inspector access and S3 permissions.

### 2. GitHub Secrets

Add the AWS role ARN to your workload repository secrets.

### 3. Build Artifact

The artifact must be uploaded in a previous job using `actions/upload-artifact@v4` with a name matching the `artifact-name` input.

## Vulnerability Scanning

The workflow uses [Amazon Inspector via the aws-actions/vulnerability-scan-github-action-for-amazon-inspector action](https://github.com/aws-actions/vulnerability-scan-github-action-for-amazon-inspector) to scan artifacts or repositories with the following default thresholds:

| Severity | Threshold | Behavior |
|----------|-----------|----------|
| Critical | 1 | Fails if ≥1 critical vulnerability found |
| High | 1 | Fails if ≥1 high vulnerability found |
| Medium | 0 | Does not fail the build |
| Low | 0 | Does not fail the build |
| Other | 0 | Does not fail the build |

If any threshold is exceeded, the workflow will:
1. Display the vulnerability findings in Markdown format
2. Fail the workflow before uploading to S3
3. Prevent vulnerable artifacts from being deployed

### Ignoring Findings

Specific findings can be suppressed so they don't count towards thresholds or appear in reports. Place an `.inspector-ignore` plain-text file at the root of your repository with one CVE/finding ID per line. Lines starting with `#` are treated as comments.

```
# Ignore known false positives
CVE-2021-12345
CVE-2022-67890
# GHSA IDs are also supported
GHSA-mqqf-5wvp-8fh8
```

See the [aws-actions repo](https://github.com/aws-actions/vulnerability-scan-github-action-for-amazon-inspector) for full documentation on the `ignore_findings` input and other options.

### Scan Types

- **`repository`** (default): Checks out the repository and scans the source code
- **`archive`**: Scans the downloaded artifact file for vulnerabilities

## Workflow Steps

1. **Configure AWS credentials**: Assumes the IAM role via OIDC
2. **Checkout repository** (if `scan-type: repository`): Clones the repository code
3. **Download artifact**: Retrieves the artifact from the current workflow run
4. **Scan with Inspector**: Analyzes the artifact or repository for vulnerabilities
5. **Display results**: Shows vulnerability findings in Markdown format
6. **Threshold check**: Fails if security thresholds are exceeded
7. **Upload to S3**: Pushes the artifact to S3 if all checks pass

## Permissions

The workflow requires the following GitHub permissions:

```yaml
permissions:
  id-token: write   # Required for OIDC authentication
  contents: read    # Required to checkout the repository (if scan-type: repository)
```

## Usage Examples

### Basic Archive Scan

```yaml
scan-and-push:
  uses: SFOE-prometheon/github-terraform-workflows/.github/workflows/s3-scan-push.yml@v5
  with:
    artifact-name: my-application
    artifact-type: .zip
    tag-name: ${{ github.event.release.tag_name }}
    s3-bucket: my-artifact-bucket
  secrets:
    aws-role: ${{ secrets.SFOE_WORKLOAD_PIPELINE_ROLE_DEV }}
```

### Repository Scan with Custom S3 Key

```yaml
scan-and-push:
  uses: SFOE-prometheon/github-terraform-workflows/.github/workflows/s3-scan-push.yml@v5
  with:
    artifact-name: my-application
    artifact-type: .tar.gz
    s3-artifact-name: production-app
    tag-name: ${{ github.event.release.tag_name }}
    s3-bucket: my-artifact-bucket
    aws-region: us-east-1
    scan-type: repository
  secrets:
    aws-role: ${{ secrets.SFOE_WORKLOAD_PIPELINE_ROLE_PROD }}
```

### Complete Pipeline Example

```yaml
name: Build and Deploy

on:
  release:
    types: [published]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Build application
        run: |
          # Your build steps here
          zip -r my-app.zip dist/
      
      - name: Upload artifact
        uses: actions/upload-artifact@v4
        with:
          name: my-app
          path: my-app.zip

  scan-and-push:
    needs: build
    uses: SFOE-prometheon/github-terraform-workflows/.github/workflows/s3-scan-push.yml@v5
    with:
      artifact-name: my-app
      tag-name: ${{ github.event.release.tag_name }}
      s3-bucket: production-artifacts
    secrets:
      aws-role: ${{ secrets.SFOE_WORKLOAD_PIPELINE_ROLE }}
```

## S3 Key Format

The uploaded artifact will be stored in S3 with the following key format:

```
{s3-artifact-name or artifact-name}-{tag-name}{artifact-type}
```

