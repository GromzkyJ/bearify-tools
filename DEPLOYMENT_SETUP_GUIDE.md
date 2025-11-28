# Complete GitHub Actions Auto-Deployment Setup Guide

This guide provides step-by-step instructions for setting up automated deployment from GitHub to a web server using GitHub Actions and FTP. This process enables automatic deployment to test and production environments whenever code is pushed to specific branches.

## Table of Contents
1. [Prerequisites](#prerequisites)
2. [Part 1: GitHub Repository Setup](#part-1-github-repository-setup)
3. [Part 2: Local Git Setup](#part-2-local-git-setup)
4. [Part 3: FTP Account Setup in CyberPanel](#part-3-ftp-account-setup-in-cyberpanel)
5. [Part 4: GitHub Secrets Configuration](#part-4-github-secrets-configuration)
6. [Part 5: GitHub Actions Workflow Creation](#part-5-github-actions-workflow-creation)
7. [Part 6: Testing the Deployment](#part-6-testing-the-deployment)
8. [Part 7: Usage Instructions](#part-7-usage-instructions)
9. [Part 8: Version Management System](#part-8-version-management-system)
10. [Part 9: Security Best Practices](#part-9-security-best-practices)
11. [Part 10: Developer Best Practices](#part-10-developer-best-practices)
12. [Troubleshooting](#troubleshooting)

---

## Prerequisites

Before starting, ensure you have:
- A GitHub account (free tier is sufficient)
- Access to CyberPanel hosting control panel
- A domain name (e.g., `example.com`)
- Basic terminal/command line access
- Git installed on your local machine (check with `git --version`)

---

## Part 1: GitHub Repository Setup

### Step 1.1: Create GitHub Account
1. Go to https://github.com
2. Click "Sign up" (top right)
3. Enter:
   - Username (e.g., `yourusername`)
   - Email address
   - Password
4. Verify your email address
5. Complete the setup process

### Step 1.2: Create a New Repository
1. Log in to GitHub
2. Click the "+" icon (top right) → "New repository"
3. Fill in the repository details:
   - **Repository name**: Choose a name (e.g., `my-project-tools`)
   - **Description**: Optional description
   - **Visibility**: Choose Private or Public
   - **Important**: Do NOT check "Add a README file" (we'll add files later)
4. Click "Create repository"

### Step 1.3: Generate Personal Access Token
1. Go to GitHub → Click your profile picture (top right) → "Settings"
2. In the left sidebar, scroll down and click "Developer settings"
3. Click "Personal access tokens" → "Tokens (classic)"
4. Click "Generate new token" → "Generate new token (classic)"
5. Configure the token:
   - **Note**: Give it a descriptive name (e.g., "project-deployment")
   - **Expiration**: Choose duration (90 days, 1 year, or "No expiration" for long-term projects)
   - **Select scopes**: Check the following:
     - ✅ **repo** (this includes all repository permissions)
     - ✅ **workflow** (required for GitHub Actions)
6. Click "Generate token"
7. **IMPORTANT**: Copy the token immediately and save it securely (you won't see it again)
   - Format: `ghp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`

---

## Part 2: Local Git Setup

### Step 2.1: Navigate to Project Directory
```bash
cd /path/to/your/project
```

### Step 2.2: Initialize Git Repository
```bash
git init
```

### Step 2.3: Create Initial Branch Structure
```bash
# Create and switch to test branch
git checkout -b test

# Create a simple README file
echo "# Project Name" > README.md

# Add and commit
git add .
git commit -m "Initial setup"
```

### Step 2.4: Connect to GitHub Repository
```bash
# Replace YOUR_USERNAME and REPOSITORY_NAME with your actual values
git remote add origin https://github.com/YOUR_USERNAME/REPOSITORY_NAME.git
```

### Step 2.5: Configure Git to Use Personal Access Token
```bash
# Temporarily set remote URL with token for authentication
# Replace YOUR_TOKEN with your actual token
git remote set-url origin https://YOUR_TOKEN@github.com/YOUR_USERNAME/REPOSITORY_NAME.git
```

### Step 2.6: Push Branches to GitHub
```bash
# Push test branch
git push -u origin test

# Create and push main branch
git checkout -b main
git push -u origin main
```

### Step 2.7: Remove Token from Remote URL (Security)
```bash
# Remove token from URL after initial push
git remote set-url origin https://github.com/YOUR_USERNAME/REPOSITORY_NAME.git
```

**Note**: For future pushes, you'll need to authenticate. You can either:
- Use the token in the URL temporarily when pushing
- Set up SSH keys (more secure, recommended for long-term)
- Use GitHub Desktop application

---

## Part 3: FTP Account Setup in CyberPanel

### Step 3.1: Log into CyberPanel
1. Navigate to your CyberPanel URL (usually `https://your-server-ip:8090` or your CyberPanel domain)
2. Log in with your admin credentials

### Step 3.2: Create Required Directories
1. In CyberPanel, go to "File Manager"
2. Navigate to `public_html` directory
3. Create two folders:
   - `tools-test` (for test environment)
   - `tools` (for production environment)

### Step 3.3: Create FTP Account
1. In CyberPanel, go to "FTP" or "FTP Accounts" in the left sidebar
2. Select your domain from the "Select Domain" dropdown (e.g., `example.com`)
3. Click "+ Create FTP Account" button
4. Fill in the form:
   - **Domain**: Should already show your domain
   - **FTP Username**: Create a username (e.g., `project_tools`)
     - **Important**: CyberPanel will prefix this with the owner username
     - Example: If you enter `project_tools`, the actual username will be `admin_project_tools` or `example.com_project_tools`
   - **FTP Password**: 
     - Click "Generate" for a secure password, OR
     - Enter a strong password manually
     - **Save this password** - you'll need it for GitHub Secrets
   - **Path (Relative)**: Enter `/public_html`
     - This gives access to both `tools` and `tools-test` subdirectories
5. Click "Create FTP Account"

### Step 3.4: Record FTP Credentials
After creating the account, note down:
- **FTP Server**: Usually your domain (e.g., `example.com`) or `ftp.example.com`
  - If not shown, try the domain without `ftp.` prefix first
  - Alternative: Your server IP address
- **FTP Username**: The FULL username as shown in CyberPanel (e.g., `admin_project_tools` or `example.com_project_tools`)
  - **Critical**: Use the exact username shown in the FTP account list, not just what you entered
- **FTP Password**: The password you set/generated
- **FTP Directory**: `/public_html` (this is the root for the FTP account)

---

## Part 4: GitHub Secrets Configuration

### Step 4.1: Navigate to Repository Settings
1. Go to your GitHub repository: `https://github.com/YOUR_USERNAME/REPOSITORY_NAME`
2. Click the "Settings" tab (top navigation bar, rightmost tab)
3. In the left sidebar, click "Secrets and variables" → "Actions"

### Step 4.2: Add FTP_SERVER Secret
1. Click "New repository secret"
2. **Name**: `FTP_SERVER` (exactly as shown, case-sensitive)
3. **Secret**: Enter your FTP server address
   - Usually: `example.com` (without `ftp.` prefix)
   - Or: `ftp.example.com` if that's what CyberPanel shows
   - Or: Your server IP address
4. Click "Add secret"

### Step 4.3: Add FTP_USERNAME Secret
1. Click "New repository secret" again
2. **Name**: `FTP_USERNAME` (exactly as shown, case-sensitive)
3. **Secret**: Enter the FULL FTP username from CyberPanel
   - Example: `admin_project_tools` or `example.com_project_tools`
   - **Critical**: Use the exact username shown in CyberPanel's FTP account list
4. Click "Add secret"

### Step 4.4: Add FTP_PASSWORD Secret
1. Click "New repository secret" again
2. **Name**: `FTP_PASSWORD` (exactly as shown, case-sensitive)
3. **Secret**: Enter the FTP password you saved from CyberPanel
   - Include all special characters exactly as they appear
4. Click "Add secret"

### Step 4.5: Verify All Secrets Are Added
You should now see three secrets listed:
- `FTP_SERVER`
- `FTP_USERNAME`
- `FTP_PASSWORD`

---

## Part 5: GitHub Actions Workflow Creation

### Step 5.1: Create Workflow Directory Structure
```bash
# Navigate to your project directory
cd /path/to/your/project

# Create the .github/workflows directory
mkdir -p .github/workflows
```

### Step 5.2: Create Deployment Workflow File
Create a file at `.github/workflows/deploy.yml` with the following content:

```yaml
name: Deploy to Production

on:
  push:
    branches:
      - test
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest
    
    steps:
    - name: Checkout code
      uses: actions/checkout@v3
    
    - name: Setup Node.js
      uses: actions/setup-node@v3
      with:
        node-version: '18'
    
    - name: Install dependencies
      run: npm install
      if: hashFiles('package.json') != ''
    
    - name: Build project
      run: npm run build
      if: hashFiles('package.json') != ''
    
    - name: Prepare build directory
      run: |
        if [ ! -d "build" ]; then
          mkdir -p build
          cp -r *.html *.css *.js build/ 2>/dev/null || true
          cp -r public build/ 2>/dev/null || true
        fi
    
    - name: Deploy to Test Environment
      if: github.ref == 'refs/heads/test'
      uses: SamKirkland/FTP-Deploy-Action@4.3.0
      with:
        server: ${{ secrets.FTP_SERVER }}
        username: ${{ secrets.FTP_USERNAME }}
        password: ${{ secrets.FTP_PASSWORD }}
        local-dir: ./build/
        server-dir: ./tools-test/
        protocol: ftp
        port: 21
        exclude: |
          **/.git*
          **/.git*/**
          **/node_modules/**
    
    - name: Deploy to Production Environment
      if: github.ref == 'refs/heads/main'
      uses: SamKirkland/FTP-Deploy-Action@4.3.0
      with:
        server: ${{ secrets.FTP_SERVER }}
        username: ${{ secrets.FTP_USERNAME }}
        password: ${{ secrets.FTP_PASSWORD }}
        local-dir: ./build/
        server-dir: ./tools/
        protocol: ftp
        port: 21
        exclude: |
          **/.git*
          **/.git*/**
          **/node_modules/**
```

### Step 5.3: Create .gitignore File
Create a `.gitignore` file in your project root:

```
node_modules/
build/
dist/
.env
.DS_Store
```

### Step 5.4: Commit and Push Workflow
```bash
# Make sure you're on the test branch
git checkout test

# Add the workflow files
git add .github/workflows/deploy.yml .gitignore

# Commit
git commit -m "Add GitHub Actions deployment workflow"

# Push to trigger the workflow (you'll need to authenticate)
# If using token in URL:
git remote set-url origin https://YOUR_TOKEN@github.com/YOUR_USERNAME/REPOSITORY_NAME.git
git push origin test

# Remove token from URL after push
git remote set-url origin https://github.com/YOUR_USERNAME/REPOSITORY_NAME.git
```

### Step 5.5: Merge to Main Branch
```bash
# Switch to main branch
git checkout main

# Merge test branch
git merge test

# Push to main
git remote set-url origin https://YOUR_TOKEN@github.com/YOUR_USERNAME/REPOSITORY_NAME.git
git push origin main

# Remove token from URL
git remote set-url origin https://github.com/YOUR_USERNAME/REPOSITORY_NAME.git
```

---

## Part 6: Testing the Deployment

### Step 6.1: Create a Test File
Create a simple `index.html` file in your project:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Project Name - Test Deployment</title>
    <style>
        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
            max-width: 800px;
            margin: 50px auto;
            padding: 20px;
            background: #f5f5f5;
        }
        .container {
            background: white;
            padding: 40px;
            border-radius: 12px;
            box-shadow: 0 2px 10px rgba(0,0,0,0.1);
        }
        h1 {
            color: #333;
            margin-bottom: 10px;
        }
        .status {
            padding: 15px;
            background: #4CAF50;
            color: white;
            border-radius: 8px;
            margin-top: 20px;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>🚀 Project Name</h1>
        <p>Auto-deployment is working!</p>
        <div class="status">
            ✅ Successfully deployed via GitHub Actions
        </div>
        <p style="margin-top: 20px; color: #666;">
            This page was automatically deployed from GitHub.
        </p>
    </div>
</body>
</html>
```

### Step 6.2: Test Deployment to Test Environment
```bash
# Make sure you're on test branch
git checkout test

# Add and commit test file
git add index.html
git commit -m "Add test deployment file"
git remote set-url origin https://YOUR_TOKEN@github.com/YOUR_USERNAME/REPOSITORY_NAME.git
git push origin test
git remote set-url origin https://github.com/YOUR_USERNAME/REPOSITORY_NAME.git
```

### Step 6.3: Verify Deployment
1. Go to GitHub repository → "Actions" tab
2. You should see a workflow run in progress or completed
3. Wait 1-2 minutes for deployment to complete
4. Visit your test URL: `https://yourdomain.com/tools-test/`
5. You should see the test page

### Step 6.4: Test Production Deployment
```bash
# Switch to main branch
git checkout main

# Merge test branch
git merge test

# Push to main
git remote set-url origin https://YOUR_TOKEN@github.com/YOUR_USERNAME/REPOSITORY_NAME.git
git push origin main
git remote set-url origin https://github.com/YOUR_USERNAME/REPOSITORY_NAME.git
```

1. Check GitHub Actions for the deployment
2. Visit production URL: `https://yourdomain.com/tools/`
3. Verify the page is live

---

## Part 7: Usage Instructions

### Daily Workflow

#### Making Changes and Testing
```bash
# 1. Make your changes to files
# 2. Stage changes
git add .

# 3. Commit changes
git commit -m "Description of your changes"

# 4. Push to test branch (triggers auto-deployment to test environment)
git remote set-url origin https://YOUR_TOKEN@github.com/YOUR_USERNAME/REPOSITORY_NAME.git
git push origin test
git remote set-url origin https://github.com/YOUR_USERNAME/REPOSITORY_NAME.git

# 5. Wait 1-2 minutes, then check: https://yourdomain.com/tools-test/
```

#### Deploying to Production
```bash
# 1. After testing and confirming everything works on test environment
# 2. Switch to main branch
git checkout main

# 3. Merge test branch into main
git merge test

# 4. Push to main (triggers auto-deployment to production)
git remote set-url origin https://YOUR_TOKEN@github.com/YOUR_USERNAME/REPOSITORY_NAME.git
git push origin main
git remote set-url origin https://github.com/YOUR_USERNAME/REPOSITORY_NAME.git

# 5. Wait 1-2 minutes, then check: https://yourdomain.com/tools/
```

### Branch Strategy
- **test branch**: For testing changes before production
  - Auto-deploys to: `yourdomain.com/tools-test/`
- **main branch**: For production/live site
  - Auto-deploys to: `yourdomain.com/tools/`

---

## Part 8: Version Management System

### Understanding Version Control

Every commit in Git creates a version snapshot. You can revert to any previous version at any time. This is crucial for:
- Rolling back broken deployments
- Comparing different versions
- Maintaining a history of changes
- Creating release points

### Version Tagging Strategy

#### Semantic Versioning
Use semantic versioning (SemVer) format: `MAJOR.MINOR.PATCH`
- **MAJOR** (v2.0.0): Breaking changes, major features
- **MINOR** (v1.1.0): New features, backward compatible
- **PATCH** (v1.0.1): Bug fixes, small improvements

#### Setting Up Version Tags

**After Initial Setup:**
```bash
# Tag the initial version
git tag v1.0.0
git push origin --tags
```

**After Successful Deployments:**
```bash
# Tag a new version after successful production deployment
git checkout main
git tag v1.1.0 -m "Version 1.1.0: Added user authentication"
git push origin --tags
```

### Viewing Version History

```bash
# See all version tags
git tag

# See detailed version history
git log --oneline --graph --all --decorate

# See commits for a specific version
git log v1.0.0..v1.1.0

# See what changed between versions
git diff v1.0.0 v1.1.0
```

### Reverting to a Previous Version

#### Method 1: Revert Using Commit Hash (Recommended - Preserves History)

```bash
# 1. Find the commit hash of the version you want
git log --oneline

# Output example:
# abc1234 (HEAD -> test) Version 3 - BROKEN
# def5678 Version 2 - Working
# ghi9012 Version 1 - Working

# 2. Revert to Version 2 (creates a new commit that undoes Version 3)
git checkout test
git revert abc1234

# 3. Push the revert
git remote set-url origin https://YOUR_TOKEN@github.com/USERNAME/REPO.git
git push origin test
git remote set-url origin https://github.com/USERNAME/REPO.git

# 4. Verify on test site, then deploy to production
git checkout main
git merge test
git push origin main
```

#### Method 2: Revert Using Version Tags

```bash
# 1. See all available versions
git tag

# 2. Create a new branch from a previous version
git checkout -b hotfix/rollback-to-v1.0.0 v1.0.0

# 3. Test the rollback
# ... make any necessary adjustments ...

# 4. Merge back to test
git checkout test
git merge hotfix/rollback-to-v1.0.0
git push origin test

# 5. After verification, deploy to production
git checkout main
git merge test
git push origin main
```

#### Method 3: Reset to Previous Version (Use with Caution)

**⚠️ WARNING**: This rewrites history. Only use if:
- You're the only one working on the project
- You're absolutely sure you want to remove newer commits
- You understand this affects the entire team

```bash
# 1. Find the commit hash
git log --oneline

# 2. Reset to that commit
git checkout test
git reset --hard def5678  # Replace with your commit hash

# 3. Force push (ONLY if you're sure)
git remote set-url origin https://YOUR_TOKEN@github.com/USERNAME/REPO.git
git push origin test --force
git remote set-url origin https://github.com/USERNAME/REPO.git
```

### Creating Release Branches

For major releases, create dedicated release branches:

```bash
# Create release branch
git checkout -b release/v2.0.0

# Make final adjustments
# ... make changes ...

# Tag the release
git tag v2.0.0 -m "Release version 2.0.0"

# Merge to main
git checkout main
git merge release/v2.0.0
git push origin main

# Push tags
git push origin --tags

# Keep release branch for reference (optional)
git push origin release/v2.0.0
```

### Version Management Best Practices

1. **Tag After Successful Production Deployments**
   - Always tag after a successful production deployment
   - Use descriptive tag messages
   - Follow semantic versioning

2. **Document Version Changes**
   - Keep a CHANGELOG.md file
   - Document what changed in each version
   - Note breaking changes

3. **Test Rollbacks**
   - Periodically test your rollback process
   - Ensure you can quickly revert if needed
   - Document the rollback procedure

4. **Use Descriptive Version Names**
   - ✅ Good: `v1.2.3`, `v2.0.0-beta`, `v1.5.0-rc1`
   - ❌ Bad: `version1`, `new`, `latest`

5. **Keep Version History Clean**
   - Don't delete tags
   - Don't force push to main branch
   - Maintain a clear version history

### Creating a CHANGELOG

Create a `CHANGELOG.md` file in your project root:

```markdown
# Changelog

All notable changes to this project will be documented in this file.

## [2.0.0] - 2024-01-15
### Added
- User authentication system
- Password reset functionality

### Changed
- Updated API endpoints
- Improved mobile responsiveness

### Fixed
- Fixed login bug on Safari
- Resolved memory leak in dashboard

## [1.1.0] - 2024-01-01
### Added
- Dark mode support
- New dashboard widgets

## [1.0.0] - 2023-12-01
### Added
- Initial release
- Basic functionality
```

Update this file with each version release.

---

## Part 9: Security Best Practices

### Credential Management

#### 1. Personal Access Tokens
- ✅ **DO**: Store tokens securely (password manager)
- ✅ **DO**: Use tokens with minimal required permissions
- ✅ **DO**: Set expiration dates for tokens
- ✅ **DO**: Rotate tokens periodically (every 90 days recommended)
- ❌ **DON'T**: Commit tokens to repository
- ❌ **DON'T**: Share tokens in chat/email
- ❌ **DON'T**: Use tokens with excessive permissions

#### 2. GitHub Secrets
- ✅ **DO**: Use GitHub Secrets for all sensitive data
- ✅ **DO**: Review secrets regularly
- ✅ **DO**: Remove unused secrets
- ✅ **DO**: Use different secrets for different environments (if applicable)
- ❌ **DON'T**: Hardcode secrets in workflow files
- ❌ **DON'T**: Log secrets in workflow outputs

#### 3. FTP Credentials
- ✅ **DO**: Use strong, unique passwords
- ✅ **DO**: Change FTP passwords periodically
- ✅ **DO**: Use FTP accounts with minimal required directory access
- ✅ **DO**: Monitor FTP access logs
- ❌ **DON'T**: Reuse passwords across projects
- ❌ **DON'T**: Share FTP credentials

### Repository Security

#### 1. Branch Protection
Enable branch protection for `main` branch:
1. Go to GitHub repository → Settings → Branches
2. Add rule for `main` branch
3. Enable:
   - ✅ Require pull request reviews
   - ✅ Require status checks to pass
   - ✅ Require branches to be up to date
   - ✅ Do not allow force pushes
   - ✅ Do not allow deletions

#### 2. Access Control
- ✅ **DO**: Limit repository access to necessary team members
- ✅ **DO**: Use teams/organizations for access management
- ✅ **DO**: Review access permissions regularly
- ✅ **DO**: Remove access for team members who leave
- ❌ **DON'T**: Give admin access unnecessarily
- ❌ **DON'T**: Share repository access casually

#### 3. Code Review
- ✅ **DO**: Require code reviews before merging to main
- ✅ **DO**: Review all changes, especially security-related
- ✅ **DO**: Use pull requests for all changes
- ❌ **DON'T**: Skip reviews for "small" changes
- ❌ **DON'T**: Approve your own pull requests

### Workflow Security

#### 1. Workflow File Security
- ✅ **DO**: Review workflow files before committing
- ✅ **DO**: Use secrets for all sensitive data
- ✅ **DO**: Limit workflow permissions
- ✅ **DO**: Monitor workflow runs for suspicious activity
- ❌ **DON'T**: Allow workflows to modify production without review
- ❌ **DON'T**: Expose secrets in workflow logs

#### 2. Deployment Security
- ✅ **DO**: Always test in test environment first
- ✅ **DO**: Verify deployments after completion
- ✅ **DO**: Monitor for unauthorized deployments
- ✅ **DO**: Keep deployment logs
- ❌ **DON'T**: Deploy directly to production without testing
- ❌ **DON'T**: Ignore deployment errors

### Data Protection

#### 1. Sensitive Data
- ✅ **DO**: Never commit passwords, API keys, or tokens
- ✅ **DO**: Use environment variables for configuration
- ✅ **DO**: Encrypt sensitive data at rest
- ✅ **DO**: Use HTTPS for all connections
- ❌ **DON'T**: Store credentials in code
- ❌ **DON'T**: Log sensitive information

#### 2. Backup and Recovery
- ✅ **DO**: Regular backups of code and data
- ✅ **DO**: Test backup restoration procedures
- ✅ **DO**: Keep backups in secure locations
- ✅ **DO**: Document recovery procedures
- ❌ **DON'T**: Rely on single backup location
- ❌ **DON'T**: Skip backup testing

### Security Checklist

Before deploying to production:
- [ ] All secrets are stored in GitHub Secrets (not in code)
- [ ] No credentials committed to repository
- [ ] Code reviewed by team member
- [ ] Tested in test environment
- [ ] No sensitive data in logs
- [ ] HTTPS enabled on production site
- [ ] Access controls properly configured
- [ ] Backup procedures in place

---

## Part 10: Developer Best Practices

### Code Quality

#### 1. Code Organization
- ✅ **DO**: Organize code into logical modules/folders
- ✅ **DO**: Use consistent naming conventions
- ✅ **DO**: Keep functions small and focused
- ✅ **DO**: Write self-documenting code
- ✅ **DO**: Remove unused code
- ❌ **DON'T**: Create monolithic files
- ❌ **DON'T**: Use unclear variable names
- ❌ **DON'T**: Leave commented-out code

#### 2. Code Review Process
```bash
# Before pushing, review your changes
git diff

# Check for common issues
# - Unused imports
# - Console.log statements
# - Hardcoded values
# - Security vulnerabilities
```

#### 3. Testing
- ✅ **DO**: Test locally before committing
- ✅ **DO**: Test in test environment before production
- ✅ **DO**: Test edge cases
- ✅ **DO**: Test on different browsers/devices
- ❌ **DON'T**: Skip testing
- ❌ **DON'T**: Assume it works

### Git Workflow Best Practices

#### 1. Commit Messages
Follow conventional commit format:
```
type(scope): subject

body (optional)

footer (optional)
```

**Types:**
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation changes
- `style`: Code style changes (formatting)
- `refactor`: Code refactoring
- `test`: Adding tests
- `chore`: Maintenance tasks

**Examples:**
```bash
git commit -m "feat(auth): add user login functionality"
git commit -m "fix(dashboard): resolve memory leak in widget rendering"
git commit -m "docs(readme): update installation instructions"
```

#### 2. Branch Naming
Use descriptive branch names:
- ✅ `feature/user-authentication`
- ✅ `fix/login-bug`
- ✅ `hotfix/critical-security-patch`
- ✅ `refactor/api-endpoints`
- ❌ `new-feature`
- ❌ `fix`
- ❌ `test`

#### 3. Commit Frequency
- ✅ **DO**: Commit frequently (small, logical commits)
- ✅ **DO**: Commit when a logical unit of work is complete
- ✅ **DO**: Commit before leaving work for the day
- ❌ **DON'T**: Wait days to commit
- ❌ **DON'T**: Commit unrelated changes together

### Documentation

#### 1. Code Documentation
- ✅ **DO**: Document complex logic
- ✅ **DO**: Add comments for non-obvious code
- ✅ **DO**: Keep documentation up to date
- ✅ **DO**: Document API endpoints
- ❌ **DON'T**: Over-comment obvious code
- ❌ **DON'T**: Leave outdated comments

#### 2. Project Documentation
- ✅ **DO**: Maintain README.md
- ✅ **DO**: Document setup process
- ✅ **DO**: Document deployment process
- ✅ **DO**: Keep CHANGELOG.md updated
- ✅ **DO**: Document environment variables
- ❌ **DON'T**: Assume others know the setup
- ❌ **DON'T**: Skip documentation

### Performance

#### 1. Optimization
- ✅ **DO**: Optimize images before committing
- ✅ **DO**: Minify production code
- ✅ **DO**: Use efficient algorithms
- ✅ **DO**: Monitor performance metrics
- ❌ **DON'T**: Commit large unoptimized files
- ❌ **DON'T**: Ignore performance warnings

#### 2. Build Process
- ✅ **DO**: Use build tools for production
- ✅ **DO**: Remove development code from production
- ✅ **DO**: Optimize bundle sizes
- ✅ **DO**: Test build process locally
- ❌ **DON'T**: Deploy development code to production
- ❌ **DON'T**: Skip build optimization

### Collaboration

#### 1. Communication
- ✅ **DO**: Communicate breaking changes
- ✅ **DO**: Notify team before major deployments
- ✅ **DO**: Document decisions
- ✅ **DO**: Use pull requests for discussion
- ❌ **DON'T**: Deploy without communication
- ❌ **DON'T**: Make assumptions about others' work

#### 2. Conflict Resolution
```bash
# When encountering merge conflicts:
# 1. Stay calm
# 2. Understand both versions
# 3. Communicate with team member
# 4. Resolve conflicts carefully
# 5. Test after resolution
```

### Error Handling

#### 1. Error Management
- ✅ **DO**: Handle errors gracefully
- ✅ **DO**: Log errors appropriately
- ✅ **DO**: Provide user-friendly error messages
- ✅ **DO**: Monitor error logs
- ❌ **DON'T**: Ignore errors
- ❌ **DON'T**: Expose sensitive information in errors

#### 2. Debugging
- ✅ **DO**: Remove console.log before production
- ✅ **DO**: Use proper logging tools
- ✅ **DO**: Test error scenarios
- ❌ **DON'T**: Leave debug code in production
- ❌ **DON'T**: Commit temporary debugging code

### Maintenance

#### 1. Regular Maintenance
- ✅ **DO**: Update dependencies regularly
- ✅ **DO**: Review and remove unused code
- ✅ **DO**: Refactor when needed
- ✅ **DO**: Keep security patches updated
- ❌ **DON'T**: Let dependencies get outdated
- ❌ **DON'T**: Accumulate technical debt

#### 2. Monitoring
- ✅ **DO**: Monitor application performance
- ✅ **DO**: Monitor error rates
- ✅ **DO**: Set up alerts for critical issues
- ✅ **DO**: Review logs regularly
- ❌ **DON'T**: Deploy and forget
- ❌ **DON'T**: Ignore warning signs

### Developer Checklist

Before committing code:
- [ ] Code follows project style guide
- [ ] No console.log or debug code
- [ ] No hardcoded credentials
- [ ] Code is tested locally
- [ ] Commit message is descriptive
- [ ] No unnecessary files included
- [ ] Documentation updated if needed

Before deploying to production:
- [ ] All tests pass
- [ ] Code reviewed
- [ ] Tested in test environment
- [ ] Performance checked
- [ ] Security reviewed
- [ ] Documentation updated
- [ ] Team notified (if major change)
- [ ] Backup created (if major change)

---

## Troubleshooting

### Issue: "530 login authentication failed"
**Possible causes:**
1. Wrong FTP username
   - **Solution**: Verify the exact username in CyberPanel → FTP Accounts
   - The username shown in the list is the correct one (may include prefixes)
2. Wrong FTP password
   - **Solution**: Regenerate password in CyberPanel and update GitHub Secret
3. Wrong FTP server address
   - **Solution**: Try `yourdomain.com` (without `ftp.` prefix) or your server IP

### Issue: "getaddrinfo ENOTFOUND"
**Cause**: FTP server address cannot be resolved
**Solutions:**
1. Try using just the domain: `yourdomain.com` (without `ftp.`)
2. Try using your server IP address
3. Check CyberPanel for the exact FTP server address

### Issue: "404 Not Found" after successful deployment
**Possible causes:**
1. Wrong server directory path
   - **Solution**: Verify the `server-dir` in workflow is relative to FTP root
   - Should be: `./tools-test/` and `./tools/` (not `/public_html/tools-test/`)
2. Files not in correct location
   - **Solution**: Check CyberPanel File Manager to see where files were uploaded
   - Ensure `index.html` is in the correct directory

### Issue: "Permission denied" when pushing to GitHub
**Cause**: Personal Access Token expired or missing workflow permission
**Solutions:**
1. Regenerate token with both `repo` and `workflow` permissions
2. Update the token in your git remote URL when pushing

### Issue: Workflow doesn't trigger
**Possible causes:**
1. Workflow file not in correct location
   - **Solution**: Must be in `.github/workflows/deploy.yml`
2. Branch name mismatch
   - **Solution**: Ensure branch names match (`test` and `main`)
3. Workflow file has syntax errors
   - **Solution**: Check GitHub Actions tab for error messages

### Issue: Files deployed but website shows old content
**Solutions:**
1. Clear browser cache (Ctrl+Shift+R or Cmd+Shift+R)
2. Check if files are in the correct directory via CyberPanel File Manager
3. Verify the URL path matches the deployment directory

---

## Important Notes

1. **Security**: Never commit your Personal Access Token or FTP credentials to the repository
2. **Token Storage**: GitHub Secrets encrypts your credentials, but always use strong passwords
3. **FTP Username**: Always use the FULL username as shown in CyberPanel (includes prefixes)
4. **Server Directory**: Use relative paths (`./tools-test/`) not absolute paths (`/public_html/tools-test/`)
5. **Branch Protection**: Consider protecting the `main` branch to require pull requests
6. **Backup**: Always test on `test` branch before merging to `main`
7. **Version Management**: Tag important versions for easy rollback
8. **Code Review**: Always review code before deploying to production
9. **Documentation**: Keep documentation updated with each major change
10. **Monitoring**: Monitor deployments and verify they complete successfully

---

## Quick Reference: Required Information

When setting up a new project, you'll need:

### From GitHub:
- [ ] Repository name
- [ ] GitHub username
- [ ] Personal Access Token (with `repo` and `workflow` permissions)

### From CyberPanel:
- [ ] Domain name
- [ ] FTP Server address (usually just the domain)
- [ ] FTP Username (full username as shown in CyberPanel)
- [ ] FTP Password
- [ ] FTP root directory (usually `/public_html`)

### Deployment URLs:
- [ ] Test environment: `https://yourdomain.com/tools-test/`
- [ ] Production environment: `https://yourdomain.com/tools/`

---

## Summary

This setup enables:
- ✅ Automatic deployment on every push to `test` or `main` branches
- ✅ Separate test and production environments
- ✅ Safe testing workflow before production deployment
- ✅ No manual file uploads needed
- ✅ Version control integration with deployment
- ✅ Version management with tagging and rollback capabilities
- ✅ Secure credential management via GitHub Secrets
- ✅ Comprehensive best practices for security and development
- ✅ Full version history for easy rollback

The entire process is automated once set up. Simply push code to GitHub, and it will automatically deploy to your server within 1-2 minutes. With version management, you can easily revert to any previous version if issues arise.

---

**End of Guide**

