# Deployment Instructions - Daily Usage Guide

This document provides step-by-step instructions for deploying code to test and production environments using the automated GitHub Actions workflow. This guide is designed to be followed by developers or AI assistants with zero ambiguity.

## Table of Contents
1. [Understanding the Deployment System](#understanding-the-deployment-system)
2. [Prerequisites Check](#prerequisites-check)
3. [Deploying to Test Environment](#deploying-to-test-environment)
4. [Deploying to Production Environment](#deploying-to-production-environment)
5. [Deploying React Applications](#deploying-react-applications)
6. [Verification Steps](#verification-steps)
7. [Version Management and Rollback](#version-management-and-rollback)
8. [Common Deployment Scenarios](#common-deployment-scenarios)
9. [Troubleshooting Deployment Issues](#troubleshooting-deployment-issues)
10. [Best Practices](#best-practices)
11. [Security Best Practices](#security-best-practices)
12. [Developer Best Practices](#developer-best-practices)
13. [Quick Command Reference](#quick-command-reference)

---

## Understanding the Deployment System

### How It Works
1. **Two Branches**: 
   - `test` branch → Auto-deploys to test environment
   - `main` branch → Auto-deploys to production environment

2. **Automatic Deployment**:
   - When you push code to `test` branch → GitHub Actions automatically deploys to test URL
   - When you push code to `main` branch → GitHub Actions automatically deploys to production URL
   - Deployment happens automatically within 1-2 minutes after push

3. **Deployment URLs**:
   - Test Environment: `https://yourdomain.com/tools-test/`
   - Production Environment: `https://yourdomain.com/tools/`

### Important Rules
- ✅ **ALWAYS** test on `test` branch first before deploying to production
- ✅ **ALWAYS** verify test deployment works before merging to `main`
- ✅ **NEVER** push directly to `main` branch without testing first
- ✅ **ALWAYS** use descriptive commit messages

---

## Prerequisites Check

Before deploying, verify you have:

### Required Information
- [ ] GitHub repository URL (e.g., `https://github.com/username/repository-name`)
- [ ] GitHub Personal Access Token (with `repo` and `workflow` permissions)
- [ ] Access to the project directory on your local machine
- [ ] Git is installed and configured

### Verify Git Configuration
```bash
# Check if you're in the correct project directory
pwd

# Verify git is initialized
git status

# Check current branch
git branch

# Verify remote is configured
git remote -v
```

**Expected Output**: You should see:
- Current directory is your project folder
- Git status shows repository information
- Current branch is either `test` or `main`
- Remote URL points to your GitHub repository

### Verify GitHub Authentication
If you haven't set up SSH keys, you'll need to use your Personal Access Token for authentication.

**To check if you need authentication:**
```bash
# Try to fetch (this will fail if not authenticated, which is fine)
git fetch origin
```

If authentication is needed, you'll use the token in the remote URL when pushing (see deployment steps below).

---

## Deploying to Test Environment

### Step-by-Step Process

#### Step 1: Ensure You're on Test Branch
```bash
# Check current branch
git branch

# If you're on a different branch, switch to test
git checkout test

# If test branch doesn't exist locally, create it and track remote
git checkout -b test
git branch --set-upstream-to=origin/test test
```

**Verification**: The output should show `* test` indicating you're on the test branch.

#### Step 2: Make Your Changes
- Edit files in your project
- Add new files if needed
- Remove files if needed
- Make sure all changes are saved

#### Step 3: Check What Has Changed
```bash
# See which files have been modified
git status

# See the actual changes made
git diff
```

**Review the changes carefully** to ensure they are what you intended.

#### Step 4: Stage Your Changes
```bash
# Stage all changes
git add .

# OR stage specific files
git add path/to/file1.js path/to/file2.css

# Verify what's staged
git status
```

**Expected Output**: Files listed under "Changes to be committed" should be the files you want to deploy.

#### Step 5: Commit Your Changes
```bash
# Commit with a descriptive message
git commit -m "Description of what you changed and why"

# Examples of good commit messages:
# git commit -m "Add user authentication feature"
# git commit -m "Fix mobile responsive layout for dashboard"
# git commit -m "Update API endpoint configuration"
```

**Important**: 
- Write clear, descriptive commit messages
- Each commit should represent a logical unit of work
- Avoid vague messages like "fix" or "update"

#### Step 6: Authenticate and Push to Test Branch
```bash
# Replace YOUR_TOKEN with your actual GitHub Personal Access Token
# Replace YOUR_USERNAME with your GitHub username
# Replace REPOSITORY_NAME with your repository name

git remote set-url origin https://YOUR_TOKEN@github.com/YOUR_USERNAME/REPOSITORY_NAME.git

# Push to test branch
git push origin test

# IMPORTANT: Remove token from URL after pushing (security)
git remote set-url origin https://github.com/YOUR_USERNAME/REPOSITORY_NAME.git
```

**Example** (replace with your actual values):
```bash
git remote set-url origin https://ghp_abc123xyz@github.com/johndoe/my-project.git
git push origin test
git remote set-url origin https://github.com/johndoe/my-project.git
```

**Expected Output**: 
```
Enumerating objects: X, done.
Counting objects: 100% (X/X), done.
Delta compression using up to X threads
Compressing objects: 100% (X/X), done.
Writing objects: 100% (X/X), X.XX KiB | X.XX MiB/s, done.
Total X (delta X), reused X (delta X), pack-reused X
To https://github.com/username/repository.git
   abc1234..def5678  test -> test
```

#### Step 7: Verify Deployment Started
1. Go to your GitHub repository in a web browser
2. Click the "Actions" tab
3. You should see a new workflow run with status "In progress" or a yellow circle
4. Click on the workflow run to see details

**What to Look For**:
- Workflow name: "Deploy to Production" (or similar)
- Status: Yellow circle (in progress) or green checkmark (completed)
- Branch: Should show "test"
- Latest run should be from just now (1-2 minutes ago)

#### Step 8: Wait for Deployment to Complete
- Deployment typically takes 1-2 minutes
- Refresh the GitHub Actions page to see updated status
- Look for green checkmark indicating success

**Success Indicators**:
- Green checkmark next to workflow run
- All steps show green checkmarks
- No error messages in the logs

#### Step 9: Verify Test Deployment is Live
1. Wait 1-2 minutes after GitHub Actions shows success
2. Open your web browser
3. Navigate to: `https://yourdomain.com/tools-test/`
4. Verify:
   - Page loads without errors
   - Your changes are visible
   - No console errors (check browser developer tools: F12)
   - All functionality works as expected

**If the page doesn't show your changes**:
- Clear browser cache (Ctrl+Shift+R or Cmd+Shift+R)
- Wait another minute (deployment might still be propagating)
- Check GitHub Actions logs for any errors

---

## Deploying to Production Environment

### ⚠️ CRITICAL: Always Test First

**NEVER deploy to production without testing on test environment first!**

### Step-by-Step Process

#### Step 1: Verify Test Deployment is Working
Before proceeding, you MUST:
- [ ] Have successfully deployed to test environment
- [ ] Verified test site works correctly at `https://yourdomain.com/tools-test/`
- [ ] Tested all functionality on test site
- [ ] Confirmed no errors or issues
- [ ] Reviewed all changes one more time

**If ANY of the above are not true, DO NOT proceed to production deployment.**

#### Step 2: Switch to Main Branch
```bash
# Switch to main branch
git checkout main

# Verify you're on main branch
git branch
```

**Expected Output**: Should show `* main` indicating you're on the main branch.

#### Step 3: Pull Latest Changes from Main (Safety Check)
```bash
# Fetch latest changes from remote
git fetch origin

# Check if main branch has any new commits
git log HEAD..origin/main

# If there are new commits, pull them first
git pull origin main
```

**Why This Matters**: Ensures you're working with the latest production code and won't overwrite someone else's changes.

#### Step 4: Merge Test Branch into Main
```bash
# Merge test branch into main
git merge test

# If there are merge conflicts, resolve them:
# 1. Git will show which files have conflicts
# 2. Open those files and resolve conflicts manually
# 3. After resolving, run: git add .
# 4. Then run: git commit (Git will use default merge message)
```

**Expected Output** (if no conflicts):
```
Updating abc1234..def5678
Fast-forward
 file1.js | 10 ++++++++++
 file2.css |  5 +++++
 2 files changed, 15 insertions(+)
```

**If You See Merge Conflicts**:
```
Auto-merging file.js
CONFLICT (content): Merge conflict in file.js
Automatic merge failed; fix conflicts and then commit the result.
```

**How to Resolve Conflicts**:
1. Open the conflicted file(s) in your editor
2. Look for conflict markers: `<<<<<<<`, `=======`, `>>>>>>>`
3. Choose which version to keep (or combine both)
4. Remove the conflict markers
5. Save the file
6. Run: `git add .`
7. Run: `git commit` (use default message or add your own)

#### Step 5: Verify Merge Was Successful
```bash
# Check git status
git status
```

**Expected Output**: Should show "Your branch is ahead of 'origin/main' by X commits" with no unmerged paths.

#### Step 6: Push to Main Branch (Production Deployment)
```bash
# Authenticate with token
git remote set-url origin https://YOUR_TOKEN@github.com/YOUR_USERNAME/REPOSITORY_NAME.git

# Push to main branch (THIS DEPLOYS TO PRODUCTION)
git push origin main

# IMPORTANT: Remove token from URL after pushing
git remote set-url origin https://github.com/YOUR_USERNAME/REPOSITORY_NAME.git
```

**Example**:
```bash
git remote set-url origin https://ghp_abc123xyz@github.com/johndoe/my-project.git
git push origin main
git remote set-url origin https://github.com/johndoe/my-project.git
```

**Expected Output**: Similar to test push, but deploying to main branch.

#### Step 7: Verify Production Deployment Started
1. Go to GitHub repository → "Actions" tab
2. You should see a new workflow run
3. Verify:
   - Branch shows "main" (not "test")
   - Status is "In progress" or completed
   - Workflow name is correct

#### Step 8: Wait for Production Deployment
- Wait 1-2 minutes for deployment to complete
- Monitor GitHub Actions for completion
- Look for green checkmark

#### Step 9: Verify Production Site is Live
1. Wait 1-2 minutes after GitHub Actions shows success
2. Open your web browser
3. Navigate to: `https://yourdomain.com/tools/`
4. Verify:
   - Page loads correctly
   - All changes from test are now in production
   - Everything works as expected
   - No errors in browser console

#### Step 10: Post-Deployment Verification Checklist
- [ ] Production site loads without errors
- [ ] All new features work correctly
- [ ] No broken links or missing resources
- [ ] Mobile view works (if applicable)
- [ ] Forms submit correctly (if applicable)
- [ ] No console errors in browser
- [ ] Performance is acceptable

---

## Deploying React Applications

### Understanding React App Deployment

React applications require a build step before deployment. The workflow automatically:
1. Detects React apps (folders with `package.json`)
2. Installs dependencies (`npm install`)
3. Builds the app (`npm run build`)
4. Deploys the `build/` folder contents

### React App Structure Requirements

Your React app folder must have:
```
your-react-app/
├── package.json          # Required - must have "build" script
├── src/                  # React source files
├── public/               # Public assets (index.html, etc.)
└── .gitignore           # Should exclude node_modules and build
```

### Deploying a New React App

#### Step 1: Create React App Folder
```bash
# Navigate to project root
cd /path/to/Tools

# Create folder for React app
mkdir my-react-app
cd my-react-app
```

#### Step 2: Initialize React App
```bash
# Create package.json
npm init -y

# Install React dependencies
npm install react react-dom react-scripts

# Create folder structure
mkdir -p src public
```

#### Step 3: Set Up package.json
Ensure `package.json` has:
```json
{
  "scripts": {
    "build": "react-scripts build"
  }
}
```

#### Step 4: Test Build Locally (Recommended)
```bash
# Install dependencies
npm install

# Test build
npm run build

# Verify build folder was created
ls build/
```

#### Step 5: Commit and Deploy
```bash
# Go back to project root
cd ..

# Add React app
git add my-react-app/
git commit -m "Add React app: my-react-app"
git remote set-url origin https://YOUR_TOKEN@github.com/USERNAME/REPO.git
git push origin test
git remote set-url origin https://github.com/USERNAME/REPO.git
```

#### Step 6: Monitor Build Process
1. Go to GitHub → Actions tab
2. Watch the workflow run
3. Look for "Build React Apps" step
4. Verify your app appears in the build log
5. Wait for deployment to complete (2-5 minutes for React apps)

#### Step 7: Verify Deployment
Visit: `https://yourdomain.com/tools-test/my-react-app/`

### Updating an Existing React App

#### Step 1: Make Changes
```bash
# Navigate to your React app folder
cd my-react-app

# Make your changes
# Edit files in src/, etc.
```

#### Step 2: Test Locally (Recommended)
```bash
# Test the app locally
npm start

# Or test the build
npm run build
```

#### Step 3: Commit and Deploy
```bash
# Go back to project root
cd ..

# Commit changes
git add my-react-app/
git commit -m "Update React app: description of changes"
git remote set-url origin https://YOUR_TOKEN@github.com/USERNAME/REPO.git
git push origin test
git remote set-url origin https://github.com/USERNAME/REPO.git
```

### Migrating an Existing React App

If you have an existing React app (like blockchain authentication):

#### Step 1: Prepare Your App
```bash
# Ensure your app builds successfully
cd /path/to/your/existing-react-app
npm install
npm run build

# Verify build folder exists
ls build/
```

#### Step 2: Copy to Tools Directory
```bash
# Copy your app to Tools directory
cp -r /path/to/your/existing-react-app /path/to/Tools/blockchain-auth

# Or move it
mv /path/to/your/existing-react-app /path/to/Tools/blockchain-auth
```

#### Step 3: Verify Structure
```bash
cd /path/to/Tools/blockchain-auth

# Check required files exist
ls package.json    # Must exist
ls src/            # Must exist
ls public/          # Should exist
```

#### Step 4: Update .gitignore
Ensure `.gitignore` excludes:
```
node_modules/
build/
.env
```

#### Step 5: Commit and Deploy
```bash
cd /path/to/Tools

git add blockchain-auth/
git commit -m "Add blockchain authentication React app"
git remote set-url origin https://YOUR_TOKEN@github.com/USERNAME/REPO.git
git push origin test
git remote set-url origin https://github.com/USERNAME/REPO.git
```

### React App Deployment Checklist

Before deploying a React app:
- [ ] `package.json` exists in app folder
- [ ] `package.json` has `"build"` script
- [ ] `src/` folder exists with React code
- [ ] `public/index.html` exists
- [ ] App builds successfully locally (`npm run build`)
- [ ] `.gitignore` excludes `node_modules/` and `build/`
- [ ] All dependencies listed in `package.json`

### React App vs HTML App

| Feature | HTML App | React App |
|---------|----------|-----------|
| Build Required | No | Yes (`npm run build`) |
| Deployment Time | 1-2 minutes | 2-5 minutes |
| Folder Structure | Just HTML files | `src/`, `public/`, `package.json` |
| Dependencies | None | Listed in `package.json` |
| Build Output | Files as-is | `build/` folder contents |

### Important Notes for React Apps

1. **Build Time**: React apps take longer to deploy (2-5 minutes) due to build process
2. **Dependencies**: All dependencies must be in `package.json`
3. **Build Script**: Must have `"build": "react-scripts build"` or equivalent
4. **Build Output**: React apps output to `build/` folder (standard)
5. **No Root package.json**: Don't create `package.json` in root Tools folder
6. **Independent Builds**: Each React app builds independently
7. **Error Handling**: If build fails, deployment stops - check GitHub Actions logs

### Troubleshooting React App Deployment

#### Issue: Build Fails in GitHub Actions
**Symptoms**: Workflow shows error in "Build React Apps" step
**Solutions**:
1. Test build locally: `cd your-app && npm install && npm run build`
2. Check `package.json` has all required dependencies
3. Verify `build` script exists: `"build": "react-scripts build"`
4. Check for syntax errors in React code
5. Review GitHub Actions logs for specific error messages

#### Issue: App Shows Blank Page After Deployment
**Symptoms**: App URL loads but shows blank page
**Solutions**:
1. Check browser console for errors (F12)
2. Verify `public/index.html` has `<div id="root"></div>`
3. Check that `src/index.js` renders the App component
4. Verify all files were deployed (check server via File Manager)
5. Check for JavaScript errors in browser console

#### Issue: Dependencies Not Installing
**Symptoms**: Build fails with "module not found" errors
**Solutions**:
1. Ensure all dependencies are in `package.json`
2. Check `package-lock.json` is up to date
3. Verify Node.js version compatibility
4. Try deleting `node_modules/` and `package-lock.json`, then `npm install` again

#### Issue: Build Succeeds But App Not Found
**Symptoms**: Build completes but 404 error when accessing app
**Solutions**:
1. Verify app folder name matches URL path
2. Check that `build/` folder was created
3. Verify workflow completed successfully
4. Check server directory structure via File Manager
5. Ensure `build/` folder contents were copied correctly

---

## Verification Steps

### After Every Deployment

#### 1. Check GitHub Actions Status
- Go to: `https://github.com/YOUR_USERNAME/REPOSITORY_NAME/actions`
- Verify latest run shows green checkmark
- Click on the run to see detailed logs
- Verify no error messages in logs

#### 2. Check Website Accessibility
- Test URL loads in browser
- No 404 errors
- No 500 errors
- Page renders correctly

#### 3. Check Browser Console
- Open browser developer tools (F12)
- Go to "Console" tab
- Verify no JavaScript errors
- Verify no network errors (404s, etc.)

#### 4. Test Functionality
- Test all interactive features
- Test forms (if any)
- Test navigation (if any)
- Test responsive design on mobile

#### 5. Check File Structure (If Needed)
If something seems wrong, verify files are in correct location:
1. Log into CyberPanel
2. Go to File Manager
3. Navigate to:
   - Test: `public_html/tools-test/`
   - Production: `public_html/tools/`
4. Verify `index.html` and other files are present

---

## Version Management and Rollback

### Understanding Version History

Every commit in Git creates a version snapshot. You can revert to any previous version at any time. This is your safety net when deployments go wrong.

### Viewing Version History

```bash
# See all commits (versions) with their IDs
git log --oneline

# See detailed history with graph
git log --oneline --graph --all --decorate

# See last 10 commits
git log --oneline -10

# See commits for a specific file
git log --oneline -- path/to/file.js
```

**Example Output:**
```
abc1234 (HEAD -> test) Version 3 - Added new feature
def5678 Version 2 - Fixed bug
ghi9012 Version 1 - Initial release
```

### Using Version Tags

Version tags make it easier to identify and revert to specific versions.

#### Creating Version Tags

```bash
# After a successful production deployment, create a tag
git checkout main

# Tag the current version (semantic versioning: MAJOR.MINOR.PATCH)
git tag v1.0.0 -m "Version 1.0.0: Initial production release"
git tag v1.1.0 -m "Version 1.1.0: Added user authentication"
git tag v2.0.0 -m "Version 2.0.0: Major redesign"

# Push tags to GitHub
git push origin --tags
```

#### Viewing Tags

```bash
# List all tags
git tag

# See tags with commit messages
git tag -l -n

# See what changed between versions
git diff v1.0.0 v1.1.0
```

### Rolling Back to a Previous Version

#### Scenario: Version 3 Broke the Site

**Step 1: Identify the Problem Version**
```bash
# See recent commits
git log --oneline -5

# Output:
# abc1234 (HEAD -> test) Version 3 - BROKEN
# def5678 Version 2 - Working
# ghi9012 Version 1 - Working
```

**Step 2: Revert to Previous Working Version (Recommended Method)**

This method creates a new commit that undoes the broken changes, preserving history:

```bash
# Switch to test branch
git checkout test

# Revert the broken commit (creates a new commit that undoes it)
git revert abc1234

# This opens an editor for commit message, or use -m flag:
git revert abc1234 -m "Revert to Version 2: Version 3 caused issues"

# Push the revert to test
git remote set-url origin https://YOUR_TOKEN@github.com/USERNAME/REPO.git
git push origin test
git remote set-url origin https://github.com/USERNAME/REPO.git
```

**Step 3: Verify on Test Environment**
- Wait for deployment to complete
- Check `https://yourdomain.com/tools-test/`
- Verify site is working again

**Step 4: Deploy to Production**
```bash
# Merge to main
git checkout main
git merge test
git remote set-url origin https://YOUR_TOKEN@github.com/USERNAME/REPO.git
git push origin main
git remote set-url origin https://github.com/USERNAME/REPO.git
```

#### Alternative: Revert Using Version Tags

```bash
# 1. See available versions
git tag

# 2. Create a new branch from a previous version
git checkout -b hotfix/rollback-to-v1.0.0 v1.0.0

# 3. Test the rollback
# ... verify everything works ...

# 4. Merge back to test
git checkout test
git merge hotfix/rollback-to-v1.0.0
git push origin test

# 5. Deploy to production
git checkout main
git merge test
git push origin main
```

#### Emergency Rollback (Use with Extreme Caution)

**⚠️ WARNING**: This rewrites history. Only use if:
- You're the only one working on the project
- You need immediate rollback
- You understand this removes commits from history

```bash
# 1. Find the commit hash of the working version
git log --oneline

# 2. Reset to that commit
git checkout test
git reset --hard def5678  # Replace with your commit hash

# 3. Force push (ONLY if absolutely necessary)
git remote set-url origin https://YOUR_TOKEN@github.com/USERNAME/REPO.git
git push origin test --force
git remote set-url origin https://github.com/USERNAME/REPO.git

# 4. Verify, then deploy to production
git checkout main
git reset --hard def5678
git push origin main --force
```

### Version Management Best Practices

#### 1. Tag After Successful Deployments
```bash
# After successful production deployment
git checkout main
git tag v1.2.0 -m "Version 1.2.0: Added payment integration"
git push origin --tags
```

#### 2. Use Semantic Versioning
- **MAJOR** (v2.0.0): Breaking changes
- **MINOR** (v1.1.0): New features, backward compatible
- **PATCH** (v1.0.1): Bug fixes

#### 3. Document Version Changes
Keep a `CHANGELOG.md` file:
```markdown
## [1.2.0] - 2024-01-15
### Added
- Payment integration
- User dashboard

### Fixed
- Login bug on mobile
- Memory leak in API calls
```

#### 4. Test Rollback Procedures
- Periodically test your ability to rollback
- Document the rollback process
- Ensure team knows how to rollback

#### 5. Never Delete Tags
- Tags are permanent markers
- They help identify versions
- Keep them for historical reference

### Quick Rollback Checklist

When you need to rollback:
- [ ] Identify the broken version/commit
- [ ] Identify the last working version
- [ ] Choose rollback method (revert vs reset)
- [ ] Test rollback on test environment first
- [ ] Verify site works after rollback
- [ ] Deploy to production
- [ ] Document what went wrong
- [ ] Tag the rollback version

---

## Common Deployment Scenarios

### Scenario 1: Deploying a Single File Change

**Situation**: You modified one file and want to deploy it.

**Steps**:
```bash
# 1. Check current branch
git branch

# 2. If not on test, switch to test
git checkout test

# 3. Stage the specific file
git add path/to/file.js

# 4. Commit
git commit -m "Fix bug in file.js"

# 5. Push to test
git remote set-url origin https://YOUR_TOKEN@github.com/USERNAME/REPO.git
git push origin test
git remote set-url origin https://github.com/USERNAME/REPO.git

# 6. Verify on test site, then merge to main
git checkout main
git merge test
git remote set-url origin https://YOUR_TOKEN@github.com/USERNAME/REPO.git
git push origin main
git remote set-url origin https://github.com/USERNAME/REPO.git
```

### Scenario 2: Deploying Multiple Related Changes

**Situation**: You made several changes that work together.

**Steps**:
```bash
# 1. Stage all related files
git add file1.js file2.css file3.html

# 2. Commit with descriptive message
git commit -m "Add new feature: user profile page with styling"

# 3. Push and verify (same as Scenario 1)
```

### Scenario 3: Rolling Back a Deployment

**Situation**: You deployed something that broke the site and need to revert.

**Steps**:
```bash
# 1. Find the commit hash before the bad deployment
git log --oneline

# 2. Note the commit hash of the last good version (e.g., abc1234)

# 3. Reset to that commit (CAREFUL: This discards newer commits)
git reset --hard abc1234

# 4. Force push to revert (ONLY if you're sure)
git remote set-url origin https://YOUR_TOKEN@github.com/USERNAME/REPO.git
git push origin main --force
git remote set-url origin https://github.com/USERNAME/REPO.git

# 5. Verify site is working again
```

**⚠️ WARNING**: Force push rewrites history. Only use if absolutely necessary and you're the only one working on the project.

### Scenario 4: Deploying After Someone Else's Changes

**Situation**: Someone else pushed changes while you were working.

**Steps**:
```bash
# 1. Before pushing, pull latest changes
git checkout test
git pull origin test

# 2. If there are conflicts, resolve them (see merge conflict resolution above)

# 3. Then push your changes
git push origin test

# 4. Same process for main branch
```

### Scenario 5: Hotfix - Urgent Production Fix

**Situation**: Critical bug in production needs immediate fix.

**Steps**:
```bash
# 1. Create hotfix branch from main
git checkout main
git pull origin main
git checkout -b hotfix/critical-bug-fix

# 2. Make the fix
# ... edit files ...

# 3. Commit the fix
git add .
git commit -m "Hotfix: Critical bug description"

# 4. Test on test environment first (if possible)
git checkout test
git merge hotfix/critical-bug-fix
git remote set-url origin https://YOUR_TOKEN@github.com/USERNAME/REPO.git
git push origin test
git remote set-url origin https://github.com/USERNAME/REPO.git

# 5. Verify on test site quickly

# 6. Deploy to production
git checkout main
git merge hotfix/critical-bug-fix
git remote set-url origin https://YOUR_TOKEN@github.com/USERNAME/REPO.git
git push origin main
git remote set-url origin https://github.com/USERNAME/REPO.git

# 7. Also merge back to test to keep it in sync
git checkout test
git merge main
git push origin test
```

---

## Troubleshooting Deployment Issues

### Issue: "Permission denied" when pushing

**Error Message**: 
```
remote: Permission to username/repo.git denied to user.
fatal: unable to access 'https://github.com/...': The requested URL returned error: 403
```

**Solutions**:
1. **Check Personal Access Token**:
   - Token might have expired
   - Token might not have `repo` permission
   - Regenerate token with correct permissions

2. **Verify Token in Remote URL**:
   ```bash
   # Check current remote URL
   git remote -v
   
   # Update with correct token
   git remote set-url origin https://YOUR_TOKEN@github.com/USERNAME/REPO.git
   ```

3. **Try Using SSH Instead** (if configured):
   ```bash
   git remote set-url origin git@github.com:USERNAME/REPO.git
   ```

### Issue: "Workflow not triggering"

**Symptom**: You push code but GitHub Actions doesn't run.

**Solutions**:
1. **Check Workflow File Exists**:
   ```bash
   # Verify workflow file is in correct location
   ls -la .github/workflows/deploy.yml
   ```

2. **Check Branch Name**:
   - Workflow only triggers on `test` and `main` branches
   - Verify you're pushing to correct branch: `git branch`

3. **Check Workflow File Syntax**:
   - Go to GitHub → Actions tab
   - Look for any error messages about workflow syntax
   - Fix any YAML syntax errors

4. **Manually Trigger** (if needed):
   - Go to GitHub → Actions tab
   - Click "Run workflow" button
   - Select branch and run

### Issue: "Deployment succeeded but site shows 404"

**Symptom**: GitHub Actions shows success, but website returns 404.

**Solutions**:
1. **Check File Location**:
   - Log into CyberPanel → File Manager
   - Navigate to `public_html/tools-test/` or `public_html/tools/`
   - Verify `index.html` exists in that directory

2. **Check Server Directory Path**:
   - Review workflow file: `.github/workflows/deploy.yml`
   - Verify `server-dir` is correct:
     - Test: `./tools-test/`
     - Production: `./tools/`

3. **Check File Permissions** (if needed):
   - In CyberPanel File Manager, check file permissions
   - Should be readable (644 for files, 755 for directories)

4. **Clear Browser Cache**:
   - Hard refresh: Ctrl+Shift+R (Windows) or Cmd+Shift+R (Mac)

### Issue: "Files deployed but changes not visible"

**Symptom**: Deployment succeeded, but old content still shows.

**Solutions**:
1. **Clear Browser Cache**:
   - Hard refresh: Ctrl+Shift+R or Cmd+Shift+R
   - Or use incognito/private browsing mode

2. **Check Deployment Logs**:
   - Go to GitHub Actions → Click on the workflow run
   - Check "Deploy to Test/Production Environment" step
   - Verify files were actually uploaded

3. **Verify Correct Files Were Committed**:
   ```bash
   # Check what was in the last commit
   git show HEAD --name-only
   ```

4. **Check if Build Process Ran**:
   - If using a build process (React, etc.), verify build step completed
   - Check if `build/` directory has latest files

### Issue: "Merge conflicts when merging test to main"

**Error Message**:
```
Auto-merging file.js
CONFLICT (content): Merge conflict in file.js
```

**Solution**:
1. **See which files have conflicts**:
   ```bash
   git status
   ```

2. **Open conflicted files** and look for:
   ```
   <<<<<<< HEAD
   (code from main branch)
   =======
   (code from test branch)
   >>>>>>> test
   ```

3. **Resolve conflicts**:
   - Decide which code to keep (or combine both)
   - Remove conflict markers (`<<<<<<<`, `=======`, `>>>>>>>`)
   - Save the file

4. **Mark as resolved**:
   ```bash
   git add .
   git commit
   ```

### Issue: "Deployment takes too long"

**Symptom**: GitHub Actions shows "In progress" for more than 5 minutes.

**Solutions**:
1. **Check GitHub Actions Status**:
   - GitHub might be experiencing issues
   - Check: https://www.githubstatus.com/

2. **Check Workflow Logs**:
   - Click on the workflow run
   - See which step is stuck
   - Look for error messages

3. **Cancel and Retry**:
   - Click "Cancel workflow" if stuck
   - Push again to trigger new deployment

### Issue: "FTP authentication failed in deployment"

**Error Message**: `530 login authentication failed`

**Solutions**:
1. **Verify GitHub Secrets**:
   - Go to GitHub → Settings → Secrets and variables → Actions
   - Verify all three secrets exist:
     - `FTP_SERVER`
     - `FTP_USERNAME`
     - `FTP_PASSWORD`

2. **Check Secret Values**:
   - Verify FTP username matches exactly what's in CyberPanel
   - Verify FTP password is correct (no extra spaces)
   - Verify FTP server address is correct

3. **Update Secrets if Needed**:
   - Edit secrets in GitHub
   - Re-run the workflow

---

## Best Practices

### 1. Always Test Before Production
- ✅ Deploy to test environment first
- ✅ Verify everything works on test site
- ✅ Test all functionality thoroughly
- ✅ Check on multiple browsers/devices
- ✅ Only then merge to main for production
- ❌ Never skip testing
- ❌ Never deploy untested code

### 2. Use Descriptive Commit Messages
Follow conventional commit format:
```
type(scope): subject

Examples:
feat(auth): add user login functionality
fix(dashboard): resolve memory leak in widgets
docs(readme): update installation instructions
refactor(api): optimize endpoint responses
```

- ✅ Good: "feat(auth): add user login with email verification"
- ✅ Good: "fix(ui): resolve mobile menu not closing on click"
- ✅ Good: "docs(deploy): update deployment instructions"
- ❌ Bad: "fix"
- ❌ Bad: "update"
- ❌ Bad: "changes"

### 3. Commit Logical Units of Work
- ✅ One feature = one commit
- ✅ One bug fix = one commit
- ✅ Related changes together
- ❌ Don't mix unrelated changes in one commit
- ❌ Don't commit everything at once

### 4. Review Changes Before Committing
```bash
# Always review what you're about to commit
git status          # See what files changed
git diff            # See actual changes
git diff --staged   # See staged changes
```

**Check for:**
- Unintended changes
- Debug code (console.log, etc.)
- Hardcoded credentials
- Unnecessary files

### 5. Keep Test and Main in Sync
After deploying to production, merge main back to test:
```bash
git checkout test
git merge main
git push origin test
```

This ensures test environment reflects production.

### 6. Tag Important Versions
```bash
# After successful production deployment
git checkout main
git tag v1.2.0 -m "Version 1.2.0: Added payment integration"
git push origin --tags
```

### 7. Don't Skip Steps
- ❌ Don't push directly to main
- ❌ Don't skip testing
- ❌ Don't ignore error messages
- ❌ Don't skip code review
- ❌ Don't skip verification

### 8. Monitor Deployments
- ✅ Always check GitHub Actions after pushing
- ✅ Always verify the website after deployment
- ✅ Check browser console for errors
- ✅ Monitor for 5-10 minutes after deployment
- ✅ Check error logs if available

### 9. Document Significant Changes
- ✅ Update CHANGELOG.md for major versions
- ✅ Note breaking changes
- ✅ Document new features
- ✅ Update README if needed
- ✅ Document configuration changes

### 10. Backup Before Major Changes
```bash
# Create backup branch before major changes
git checkout -b backup/before-major-change
git push origin backup/before-major-change

# Or tag current version
git tag backup-before-redesign
git push origin --tags
```

### 11. Communicate with Team
- ✅ Notify team before major deployments
- ✅ Communicate breaking changes
- ✅ Coordinate deployments if multiple people working
- ✅ Update team on deployment status
- ✅ Share rollback plans for major changes

### 12. Version Management
- ✅ Tag versions after successful deployments
- ✅ Use semantic versioning (v1.2.3)
- ✅ Keep version history clean
- ✅ Document version changes
- ✅ Test rollback procedures

### 13. Code Quality
- ✅ Remove debug code before committing
- ✅ Remove console.log statements
- ✅ Remove commented-out code
- ✅ Follow project style guide
- ✅ Keep code organized

### 14. Security
- ✅ Never commit credentials
- ✅ Use GitHub Secrets for sensitive data
- ✅ Review code for security issues
- ✅ Keep dependencies updated
- ✅ Use strong passwords

---

## Security Best Practices

### Credential Management

#### Personal Access Tokens
- ✅ **DO**: Store tokens in password manager
- ✅ **DO**: Use tokens with minimal required permissions
- ✅ **DO**: Set expiration dates (90 days recommended)
- ✅ **DO**: Rotate tokens periodically
- ✅ **DO**: Use different tokens for different projects
- ❌ **DON'T**: Commit tokens to repository
- ❌ **DON'T**: Share tokens in chat/email
- ❌ **DON'T**: Use tokens with excessive permissions
- ❌ **DON'T**: Leave tokens in remote URL

#### GitHub Secrets
- ✅ **DO**: Use GitHub Secrets for all sensitive data
- ✅ **DO**: Review secrets regularly
- ✅ **DO**: Remove unused secrets
- ✅ **DO**: Use descriptive secret names
- ❌ **DON'T**: Hardcode secrets in workflow files
- ❌ **DON'T**: Log secrets in workflow outputs
- ❌ **DON'T**: Share secret values

#### FTP Credentials
- ✅ **DO**: Use strong, unique passwords
- ✅ **DO**: Change FTP passwords periodically (every 90 days)
- ✅ **DO**: Use FTP accounts with minimal directory access
- ✅ **DO**: Monitor FTP access logs
- ❌ **DON'T**: Reuse passwords across projects
- ❌ **DON'T**: Share FTP credentials
- ❌ **DON'T**: Use weak passwords

### Repository Security

#### Branch Protection
Enable branch protection for `main`:
1. GitHub → Settings → Branches
2. Add rule for `main` branch
3. Enable:
   - Require pull request reviews
   - Require status checks
   - Do not allow force pushes
   - Do not allow deletions

#### Access Control
- ✅ **DO**: Limit repository access to necessary people
- ✅ **DO**: Review access permissions regularly
- ✅ **DO**: Remove access for people who leave
- ✅ **DO**: Use teams for access management
- ❌ **DON'T**: Give admin access unnecessarily
- ❌ **DON'T**: Share repository access casually

### Code Security

#### Before Committing
- ✅ **DO**: Check for hardcoded credentials
- ✅ **DO**: Remove debug code
- ✅ **DO**: Remove console.log statements
- ✅ **DO**: Review for security vulnerabilities
- ❌ **DON'T**: Commit API keys or passwords
- ❌ **DON'T**: Commit configuration files with secrets
- ❌ **DON'T**: Leave sensitive data in code

#### Security Checklist
Before every commit:
- [ ] No credentials in code
- [ ] No API keys hardcoded
- [ ] No sensitive data in comments
- [ ] No debug code left in
- [ ] Dependencies are up to date
- [ ] No security warnings

### Deployment Security

#### Pre-Deployment
- ✅ **DO**: Review all changes
- ✅ **DO**: Test in test environment
- ✅ **DO**: Verify no credentials exposed
- ✅ **DO**: Check for security updates
- ❌ **DON'T**: Deploy without testing
- ❌ **DON'T**: Deploy with known vulnerabilities

#### Post-Deployment
- ✅ **DO**: Verify deployment completed
- ✅ **DO**: Check for errors
- ✅ **DO**: Monitor for suspicious activity
- ✅ **DO**: Verify HTTPS is working
- ❌ **DON'T**: Ignore deployment errors
- ❌ **DON'T**: Skip verification

---

## Developer Best Practices

### Code Quality Standards

#### Code Organization
- ✅ **DO**: Organize code into logical modules
- ✅ **DO**: Use consistent naming conventions
- ✅ **DO**: Keep functions small and focused
- ✅ **DO**: Write self-documenting code
- ✅ **DO**: Remove unused code
- ❌ **DON'T**: Create monolithic files
- ❌ **DON'T**: Use unclear variable names
- ❌ **DON'T**: Leave commented-out code

#### Code Review Process
Before pushing:
```bash
# Review your changes
git diff

# Check for:
# - Unused imports
# - Console.log statements
# - Hardcoded values
# - Security issues
# - Code style consistency
```

### Git Workflow Standards

#### Commit Message Format
Use conventional commits:
```
type(scope): subject

Types:
- feat: New feature
- fix: Bug fix
- docs: Documentation
- style: Code style (formatting)
- refactor: Code refactoring
- test: Adding tests
- chore: Maintenance

Examples:
feat(auth): add user login
fix(dashboard): resolve memory leak
docs(readme): update instructions
```

#### Branch Naming
- ✅ `feature/user-authentication`
- ✅ `fix/login-bug`
- ✅ `hotfix/critical-patch`
- ✅ `refactor/api-endpoints`
- ❌ `new-feature`
- ❌ `fix`
- ❌ `test`

### Testing Standards

#### Before Committing
- ✅ **DO**: Test locally first
- ✅ **DO**: Test all functionality
- ✅ **DO**: Test edge cases
- ✅ **DO**: Test error scenarios
- ❌ **DON'T**: Commit untested code
- ❌ **DON'T**: Assume it works

#### Before Deploying
- ✅ **DO**: Test in test environment
- ✅ **DO**: Test on multiple browsers
- ✅ **DO**: Test on mobile devices
- ✅ **DO**: Test all user flows
- ❌ **DON'T**: Deploy without testing
- ❌ **DON'T**: Skip browser testing

### Documentation Standards

#### Code Documentation
- ✅ **DO**: Document complex logic
- ✅ **DO**: Add comments for non-obvious code
- ✅ **DO**: Keep documentation up to date
- ✅ **DO**: Document API endpoints
- ❌ **DON'T**: Over-comment obvious code
- ❌ **DON'T**: Leave outdated comments

#### Project Documentation
- ✅ **DO**: Maintain README.md
- ✅ **DO**: Keep CHANGELOG.md updated
- ✅ **DO**: Document setup process
- ✅ **DO**: Document environment variables
- ✅ **DO**: Document deployment process
- ❌ **DON'T**: Assume others know the setup
- ❌ **DON'T**: Skip documentation

### Performance Standards

#### Optimization
- ✅ **DO**: Optimize images before committing
- ✅ **DO**: Minify production code
- ✅ **DO**: Use efficient algorithms
- ✅ **DO**: Monitor performance metrics
- ❌ **DON'T**: Commit large unoptimized files
- ❌ **DON'T**: Ignore performance warnings

#### Build Process
- ✅ **DO**: Use build tools for production
- ✅ **DO**: Remove development code from production
- ✅ **DO**: Optimize bundle sizes
- ✅ **DO**: Test build process locally
- ❌ **DON'T**: Deploy development code
- ❌ **DON'T**: Skip build optimization

### Error Handling

#### Error Management
- ✅ **DO**: Handle errors gracefully
- ✅ **DO**: Log errors appropriately
- ✅ **DO**: Provide user-friendly error messages
- ✅ **DO**: Monitor error logs
- ❌ **DON'T**: Ignore errors
- ❌ **DON'T**: Expose sensitive information in errors

#### Debugging
- ✅ **DO**: Remove console.log before production
- ✅ **DO**: Use proper logging tools
- ✅ **DO**: Test error scenarios
- ❌ **DON'T**: Leave debug code in production
- ❌ **DON'T**: Commit temporary debugging code

### Maintenance Standards

#### Regular Maintenance
- ✅ **DO**: Update dependencies regularly
- ✅ **DO**: Review and remove unused code
- ✅ **DO**: Refactor when needed
- ✅ **DO**: Keep security patches updated
- ❌ **DON'T**: Let dependencies get outdated
- ❌ **DON'T**: Accumulate technical debt

#### Monitoring
- ✅ **DO**: Monitor application performance
- ✅ **DO**: Monitor error rates
- ✅ **DO**: Set up alerts for critical issues
- ✅ **DO**: Review logs regularly
- ❌ **DON'T**: Deploy and forget
- ❌ **DON'T**: Ignore warning signs

### Developer Checklist

**Before Every Commit:**
- [ ] Code follows project style guide
- [ ] No console.log or debug code
- [ ] No hardcoded credentials
- [ ] Code is tested locally
- [ ] Commit message is descriptive
- [ ] No unnecessary files included
- [ ] Documentation updated if needed

**Before Every Deployment:**
- [ ] All tests pass
- [ ] Code reviewed (if team project)
- [ ] Tested in test environment
- [ ] Performance checked
- [ ] Security reviewed
- [ ] Documentation updated
- [ ] Team notified (if major change)
- [ ] Backup/version tag created (if major change)

---

## Quick Command Reference

### Check Current Status
```bash
# Current branch
git branch

# Current status
git status

# Recent commits
git log --oneline -5
```

### Deploy to Test
```bash
git checkout test
git add .
git commit -m "Your message"
git remote set-url origin https://YOUR_TOKEN@github.com/USERNAME/REPO.git
git push origin test
git remote set-url origin https://github.com/USERNAME/REPO.git
```

### Deploy to Production
```bash
git checkout main
git merge test
git remote set-url origin https://YOUR_TOKEN@github.com/USERNAME/REPO.git
git push origin main
git remote set-url origin https://github.com/USERNAME/REPO.git
```

### Verify Deployment
1. GitHub Actions: `https://github.com/USERNAME/REPO/actions`
2. Test URL: `https://yourdomain.com/tools-test/`
3. Production URL: `https://yourdomain.com/tools/`

### Version Management
```bash
# View version history
git log --oneline

# Create version tag
git tag v1.2.0 -m "Version 1.2.0 description"
git push origin --tags

# Revert to previous version
git revert COMMIT_HASH
git push origin test

# View all tags
git tag
```

### Rollback to Previous Version
```bash
# Find the commit hash
git log --oneline

# Revert (recommended - preserves history)
git revert COMMIT_HASH
git push origin test

# Or reset (use with caution - rewrites history)
git reset --hard COMMIT_HASH
git push origin test --force
```

### React App Deployment
```bash
# Create new React app
mkdir my-react-app
cd my-react-app
npm init -y
npm install react react-dom react-scripts

# Test build locally
npm run build

# Commit and deploy
cd ..
git add my-react-app/
git commit -m "Add React app"
git push origin test

# Update existing React app
cd my-react-app
# Make changes...
cd ..
git add my-react-app/
git commit -m "Update React app"
git push origin test
```

---

## Important Reminders

### ⚠️ Security
- **NEVER** commit your Personal Access Token to the repository
- **ALWAYS** remove token from remote URL after pushing
- **NEVER** share your GitHub token or FTP credentials
- **NEVER** commit API keys, passwords, or secrets
- **ALWAYS** use GitHub Secrets for sensitive data
- **ALWAYS** review code for security issues before committing
- **ALWAYS** use strong, unique passwords
- **ALWAYS** rotate credentials periodically

### ⚠️ Before Production Deployment
- [ ] Tested on test environment
- [ ] Verified all functionality works
- [ ] Checked for errors in browser console
- [ ] Reviewed all changes one more time
- [ ] Confirmed no breaking changes
- [ ] No credentials or secrets in code
- [ ] No debug code (console.log, etc.)
- [ ] Code reviewed (if team project)
- [ ] Version tag ready (for major releases)
- [ ] Team notified (if major change)

### ⚠️ After Production Deployment
- [ ] Verified production site loads correctly
- [ ] Tested all functionality
- [ ] Checked for errors
- [ ] Monitored for a few minutes
- [ ] Created version tag (if major release)
- [ ] Updated CHANGELOG.md (if applicable)
- [ ] Documented any issues encountered

### ⚠️ Version Management
- **ALWAYS** tag important versions after successful deployments
- **ALWAYS** test rollback procedures periodically
- **ALWAYS** document version changes in CHANGELOG.md
- **NEVER** delete version tags
- **NEVER** force push to main branch (unless emergency)
- **ALWAYS** use semantic versioning (v1.2.3)

---

## Getting Help

If you encounter issues not covered in this guide:

1. **Check GitHub Actions Logs**:
   - Go to Actions tab → Click on failed workflow
   - Read error messages carefully
   - Look for specific error codes

2. **Check This Guide's Troubleshooting Section**:
   - Review common issues above
   - Try suggested solutions

3. **Verify Configuration**:
   - Check GitHub Secrets are correct
   - Verify workflow file syntax
   - Confirm branch names match

4. **Check Server Status**:
   - Verify CyberPanel is accessible
   - Check FTP account is active
   - Verify directories exist

---

**End of Deployment Instructions**

Remember: When in doubt, test on test environment first, verify it works, then deploy to production.

