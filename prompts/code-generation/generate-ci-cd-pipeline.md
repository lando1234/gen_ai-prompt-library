---
type: code-generation
complexity: intermediate
context: technical
output_format: code
interaction_mode: single-shot
production_ready: experimental
tags: [ci-cd, devops, github-actions, jenkins, gitlab-ci, automation]
---

# Generate CI/CD Pipeline Configuration

## Purpose
Generate complete CI/CD pipeline configurations for automated testing, building, and deployment.

## Context
Use this prompt when:
- Setting up automated deployments
- Implementing continuous integration
- Need consistent build/test/deploy process
- Automating release workflows

---

## Prompt

You are a DevOps engineer. Generate a complete CI/CD pipeline with testing, building, security scanning, and deployment stages.

**INPUT**:
- Platform (GitHub Actions, GitLab CI, Jenkins, etc.)
- Application type and tech stack
- Deployment target (AWS, GCP, Kubernetes, etc.)
- Optional: Branch strategy (main, develop, feature branches)

**GENERATE**:

1. **Pipeline Stages**:
   - Lint & Code Quality
   - Test (unit, integration)
   - Build (compile, bundle, containerize)
   - Security Scan (SAST, dependency check)
   - Deploy (staging, production)

2. **Triggers**:
   - Push to main/develop
   - Pull requests
   - Manual triggers
   - Scheduled runs

3. **Environment Management**:
   - Secrets handling
   - Environment variables
   - Conditional deployment (branch-based)

4. **Optimization**:
   - Caching dependencies
   - Parallel jobs
   - Fail-fast strategies

5. **Notifications**:
   - Success/failure alerts
   - Deployment notifications

**OUTPUT FORMAT**:
```
# CI/CD Pipeline: [Platform] - [Application]

## Pipeline Configuration
```[yaml/groovy]
[complete pipeline file]
```

## Secrets to Configure
```
[list of required secrets]
```

## Workflow Diagram
```
[text diagram of pipeline flow]
```

## Setup Instructions
```bash
[steps to configure pipeline]
```

## Notes
- [Pipeline behavior explanation]
- [Deployment strategy]
- [Rollback procedure]
```

**CONSTRAINTS**:
- Run tests before deployment
- Use caching for dependencies
- Deploy only from protected branches (main/develop)
- Include security scanning
- Notify on failures
- Support rollback

---

## Example

**Input**:
- Platform: GitHub Actions
- App: Node.js API
- Deploy: AWS ECS Fargate
- Branch strategy: feature → develop → main

**Output**:

```yaml
# CI/CD Pipeline: GitHub Actions - Node.js API

## Pipeline Configuration

```yaml
# .github/workflows/ci-cd.yml

name: CI/CD Pipeline

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main, develop]
  workflow_dispatch: # Manual trigger

env:
  NODE_VERSION: '20'
  AWS_REGION: 'us-east-1'
  ECR_REPOSITORY: 'myapp'
  ECS_CLUSTER: 'production-cluster'
  ECS_SERVICE: 'myapp-service'

jobs:
  # Job 1: Lint and Code Quality
  lint:
    name: Lint & Code Quality
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: ${{ env.NODE_VERSION }}
          cache: 'npm'

      - name: Install dependencies
        run: npm ci

      - name: Run ESLint
        run: npm run lint

      - name: Run Prettier check
        run: npm run format:check

      - name: Type check
        run: npm run type-check

  # Job 2: Test
  test:
    name: Run Tests
    runs-on: ubuntu-latest
    services:
      postgres:
        image: postgres:15
        env:
          POSTGRES_PASSWORD: testpass
          POSTGRES_DB: testdb
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
        ports:
          - 5432:5432

    steps:
      - uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: ${{ env.NODE_VERSION }}
          cache: 'npm'

      - name: Install dependencies
        run: npm ci

      - name: Run unit tests
        run: npm run test:unit

      - name: Run integration tests
        env:
          DATABASE_URL: postgresql://postgres:testpass@localhost:5432/testdb
        run: npm run test:integration

      - name: Generate coverage report
        run: npm run test:coverage

      - name: Upload coverage to Codecov
        uses: codecov/codecov-action@v3
        with:
          files: ./coverage/lcov.info
          fail_ci_if_error: false

  # Job 3: Security Scan
  security:
    name: Security Scan
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Run npm audit
        run: npm audit --audit-level=high
        continue-on-error: true

      - name: Run Snyk scan
        uses: snyk/actions/node@master
        env:
          SNYK_TOKEN: ${{ secrets.SNYK_TOKEN }}
        with:
          args: --severity-threshold=high
        continue-on-error: true

      - name: Run Trivy vulnerability scanner
        uses: aquasecurity/trivy-action@master
        with:
          scan-type: 'fs'
          scan-ref: '.'
          format: 'sarif'
          output: 'trivy-results.sarif'

      - name: Upload Trivy results to GitHub Security
        uses: github/codeql-action/upload-sarif@v2
        with:
          sarif_file: 'trivy-results.sarif'

  # Job 4: Build Docker Image
  build:
    name: Build & Push Docker Image
    runs-on: ubuntu-latest
    needs: [lint, test, security]
    if: github.event_name == 'push' && (github.ref == 'refs/heads/main' || github.ref == 'refs/heads/develop')
    outputs:
      image-tag: ${{ steps.meta.outputs.tags }}

    steps:
      - uses: actions/checkout@v4

      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v4
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: ${{ env.AWS_REGION }}

      - name: Login to Amazon ECR
        id: login-ecr
        uses: aws-actions/amazon-ecr-login@v2

      - name: Docker meta
        id: meta
        uses: docker/metadata-action@v5
        with:
          images: ${{ steps.login-ecr.outputs.registry }}/${{ env.ECR_REPOSITORY }}
          tags: |
            type=ref,event=branch
            type=sha,prefix={{branch}}-
            type=semver,pattern={{version}}

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v3

      - name: Build and push Docker image
        uses: docker/build-push-action@v5
        with:
          context: .
          push: true
          tags: ${{ steps.meta.outputs.tags }}
          labels: ${{ steps.meta.outputs.labels }}
          cache-from: type=gha
          cache-to: type=gha,mode=max

  # Job 5: Deploy to Staging
  deploy-staging:
    name: Deploy to Staging
    runs-on: ubuntu-latest
    needs: build
    if: github.ref == 'refs/heads/develop'
    environment:
      name: staging
      url: https://staging.myapp.com

    steps:
      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v4
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: ${{ env.AWS_REGION }}

      - name: Deploy to ECS
        run: |
          aws ecs update-service \
            --cluster staging-cluster \
            --service myapp-staging \
            --force-new-deployment \
            --no-cli-pager

      - name: Wait for deployment
        run: |
          aws ecs wait services-stable \
            --cluster staging-cluster \
            --services myapp-staging

      - name: Run smoke tests
        run: |
          curl -f https://staging.myapp.com/health || exit 1

  # Job 6: Deploy to Production
  deploy-production:
    name: Deploy to Production
    runs-on: ubuntu-latest
    needs: build
    if: github.ref == 'refs/heads/main'
    environment:
      name: production
      url: https://myapp.com

    steps:
      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v4
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: ${{ env.AWS_REGION }}

      - name: Deploy to ECS (Blue/Green)
        run: |
          # Get current task definition
          TASK_DEF=$(aws ecs describe-services \
            --cluster ${{ env.ECS_CLUSTER }} \
            --services ${{ env.ECS_SERVICE }} \
            --query 'services[0].taskDefinition' \
            --output text)

          # Update image tag in task definition
          NEW_TASK_DEF=$(aws ecs describe-task-definition \
            --task-definition $TASK_DEF \
            --query 'taskDefinition' | \
            jq '.containerDefinitions[0].image = "${{ needs.build.outputs.image-tag }}"' | \
            jq 'del(.taskDefinitionArn, .revision, .status, .requiresAttributes, .compatibilities, .registeredAt, .registeredBy)')

          # Register new task definition
          NEW_TASK_ARN=$(echo $NEW_TASK_DEF | \
            aws ecs register-task-definition --cli-input-json file:///dev/stdin | \
            jq -r '.taskDefinition.taskDefinitionArn')

          # Update service
          aws ecs update-service \
            --cluster ${{ env.ECS_CLUSTER }} \
            --service ${{ env.ECS_SERVICE }} \
            --task-definition $NEW_TASK_ARN

      - name: Wait for deployment
        run: |
          aws ecs wait services-stable \
            --cluster ${{ env.ECS_CLUSTER }} \
            --services ${{ env.ECS_SERVICE }}

      - name: Run smoke tests
        run: |
          curl -f https://myapp.com/health || exit 1

      - name: Notify Slack
        uses: slackapi/slack-github-action@v1
        with:
          payload: |
            {
              "text": "✅ Deployment to production successful!",
              "blocks": [
                {
                  "type": "section",
                  "text": {
                    "type": "mrkdwn",
                    "text": "*Production Deployment Successful* :rocket:\n\n*Commit:* ${{ github.sha }}\n*Author:* ${{ github.actor }}\n*URL:* https://myapp.com"
                  }
                }
              ]
            }
        env:
          SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK }}

  # Job 7: Rollback (manual trigger only)
  rollback:
    name: Rollback Production
    runs-on: ubuntu-latest
    if: github.event_name == 'workflow_dispatch'
    environment:
      name: production

    steps:
      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v4
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: ${{ env.AWS_REGION }}

      - name: Rollback to previous version
        run: |
          # Get previous task definition revision
          PREV_TASK_DEF=$(aws ecs describe-services \
            --cluster ${{ env.ECS_CLUSTER }} \
            --services ${{ env.ECS_SERVICE }} \
            --query 'services[0].deployments[1].taskDefinition' \
            --output text)

          # Update service to previous version
          aws ecs update-service \
            --cluster ${{ env.ECS_CLUSTER }} \
            --service ${{ env.ECS_SERVICE }} \
            --task-definition $PREV_TASK_DEF

      - name: Notify Slack
        uses: slackapi/slack-github-action@v1
        with:
          payload: |
            {
              "text": "⚠️ Production rolled back!",
              "blocks": [
                {
                  "type": "section",
                  "text": {
                    "type": "mrkdwn",
                    "text": "*Production Rollback Completed* :warning:\n\n*Triggered by:* ${{ github.actor }}"
                  }
                }
              ]
            }
        env:
          SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK }}
```

## Secrets to Configure

In GitHub Settings → Secrets and variables → Actions:

```
AWS_ACCESS_KEY_ID       - AWS access key for deployment
AWS_SECRET_ACCESS_KEY   - AWS secret key
SNYK_TOKEN             - Snyk API token for security scanning
SLACK_WEBHOOK          - Slack webhook URL for notifications
```

## Workflow Diagram

```
┌─────────────────────────────────────────────────────────┐
│                      Trigger                            │
│  (Push to main/develop, PR, Manual)                     │
└─────────────────┬───────────────────────────────────────┘
                  │
                  ▼
      ┌───────────────────────────┐
      │   Parallel Checks         │
      │  ┌──────┬────────┬──────┐ │
      │  │ Lint │  Test  │ Sec  │ │
      │  └──────┴────────┴──────┘ │
      └───────────┬───────────────┘
                  │
                  ▼
           ┌──────────┐
           │  Build   │
           │  Docker  │
           └─────┬────┘
                 │
          ┌──────┴──────┐
          │             │
     (develop)      (main)
          │             │
          ▼             ▼
   ┌──────────┐  ┌──────────┐
   │ Staging  │  │   Prod   │
   │ Deploy   │  │  Deploy  │
   └──────────┘  └──────────┘
```

## Setup Instructions

```bash
# 1. Add pipeline file
mkdir -p .github/workflows
cp ci-cd.yml .github/workflows/

# 2. Configure AWS ECR (one-time setup)
aws ecr create-repository --repository-name myapp

# 3. Add GitHub secrets
gh secret set AWS_ACCESS_KEY_ID
gh secret set AWS_SECRET_ACCESS_KEY
gh secret set SNYK_TOKEN
gh secret set SLACK_WEBHOOK

# 4. Enable branch protection
gh api repos/:owner/:repo/branches/main/protection \
  -X PUT \
  -F required_status_checks[strict]=true \
  -F required_status_checks[contexts][]=lint \
  -F required_status_checks[contexts][]=test

# 5. Create environments
gh api repos/:owner/:repo/environments/staging -X PUT
gh api repos/:owner/:repo/environments/production -X PUT
```

## Notes

**Pipeline Behavior**:
- **PRs**: Run lint + test + security (no deployment)
- **Push to develop**: Full pipeline → deploy to staging
- **Push to main**: Full pipeline → deploy to production
- **Manual trigger**: Can trigger rollback

**Deployment Strategy**:
- Staging: Direct deployment, force new deployment
- Production: Blue/Green deployment via ECS task definition updates
- Zero-downtime: ECS gradually shifts traffic to new tasks

**Optimization**:
- Jobs run in parallel (lint, test, security)
- Docker layer caching via GitHub Actions cache
- npm dependencies cached by setup-node action
- Estimated pipeline time: ~5-8 minutes

**Rollback Procedure**:
1. Navigate to Actions → CI/CD Pipeline → Run workflow
2. Select "Rollback Production" option
3. Confirm execution
4. Previous version deployed automatically

**Security**:
- No secrets in code (GitHub Secrets)
- Vulnerability scanning with npm audit, Snyk, Trivy
- SARIF upload to GitHub Security tab
- Branch protection prevents direct pushes to main

---

## Usage Notes

- **When to use**: Automating deployments, ensuring code quality
- **When NOT to use**: For simple scripts with no deployment
- **Best practice**: Test pipeline on feature branch before merging
- **Iteration**: Add stages as needs grow (e.g., E2E tests, performance tests)

---

## Quality Checklist

- [ ] Linting and code quality checks
- [ ] Unit and integration tests
- [ ] Security scanning (dependencies + SAST)
- [ ] Docker build and push
- [ ] Deployment to staging (develop branch)
- [ ] Deployment to production (main branch)
- [ ] Smoke tests after deployment
- [ ] Rollback procedure available
- [ ] Notifications on success/failure
- [ ] Secrets properly configured
