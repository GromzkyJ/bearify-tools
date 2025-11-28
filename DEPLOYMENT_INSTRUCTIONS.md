# Deployment Instructions - Daily Usage Guide

This document provides step-by-step instructions for deploying code to test and production environments using the automated GitHub Actions workflow. This guide is designed to be followed by developers or AI assistants with zero ambiguity.

## Table of Contents
1. [Understanding the Deployment System](#understanding-the-deployment-system)
2. [Prerequisites Check](#prerequisites-check)
3. [Deploying to Test Environment](#deploying-to-test-environment)
4. [Deploying to Production Environment](#deploying-to-production-environment)
5. [Verification Steps](#verification-steps)
6. [Common Deployment Scenarios](#common-deployment-scenarios)
7. [Troubleshooting Deployment Issues](#troubleshooting-deployment-issues)
8. [Best Practices](#best-practices)
9. [Quick Command Reference](#quick-command-reference)

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
- ✅ Only then merge to main for production

### 2. Use Descriptive Commit Messages
- ✅ Good: "Add user authentication with email verification"
- ✅ Good: "Fix mobile menu not closing on click"
- ❌ Bad: "fix"
- ❌ Bad: "update"

### 3. Commit Logical Units of Work
- ✅ One feature = one commit
- ✅ One bug fix = one commit
- ❌ Don't mix unrelated changes in one commit

### 4. Review Changes Before Committing
```bash
# Always review what you're about to commit
git status
git diff
```

### 5. Keep Test and Main in Sync
- After deploying to production, merge main back to test:
```bash
git checkout test
git merge main
git push origin test
```

### 6. Don't Skip Steps
- Don't push directly to main
- Don't skip testing
- Don't ignore error messages

### 7. Monitor Deployments
- Always check GitHub Actions after pushing
- Always verify the website after deployment
- Check browser console for errors

### 8. Document Significant Changes
- For major changes, update documentation
- Note any breaking changes
- Document new features

### 9. Backup Before Major Changes
- If making significant changes, consider creating a backup branch:
```bash
git checkout -b backup/before-major-change
git push origin backup/before-major-change
```

### 10. Communicate with Team
- If working with others, communicate before deploying
- Let team know about breaking changes
- Coordinate deployments if multiple people are working

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

---

## Important Reminders

### ⚠️ Security
- **NEVER** commit your Personal Access Token to the repository
- **ALWAYS** remove token from remote URL after pushing
- **NEVER** share your GitHub token or FTP credentials

### ⚠️ Before Production Deployment
- [ ] Tested on test environment
- [ ] Verified all functionality works
- [ ] Checked for errors in browser console
- [ ] Reviewed all changes one more time
- [ ] Confirmed no breaking changes

### ⚠️ After Production Deployment
- [ ] Verified production site loads correctly
- [ ] Tested all functionality
- [ ] Checked for errors
- [ ] Monitored for a few minutes

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

