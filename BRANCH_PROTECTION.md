# Branch Protection Setup for Main Branch

## Overview
This document describes how to protect the `main` branch to ensure only the repository owner can commit and push changes.

## Files Added
- `.github/CODEOWNERS` - Defines that @buwon is the owner of all files and must approve changes

## Required GitHub Settings Configuration

To fully protect the `main` branch, you must configure branch protection rules in GitHub Settings:

### Steps to Configure Branch Protection:

1. Go to your repository on GitHub: https://github.com/buwon/buwon.github.io
2. Click on **Settings** (repository settings, not your account settings)
3. In the left sidebar, click **Branches**
4. Under "Branch protection rules", click **Add rule** or **Add classic branch protection rule**
5. In "Branch name pattern", enter: `main`
6. Enable the following settings:

   #### Required Settings:
   - ✅ **Require a pull request before merging**
     - ✅ Require approvals: 1
     - ✅ Require review from Code Owners
     - ✅ Dismiss stale pull request approvals when new commits are pushed
   
   - ✅ **Require status checks to pass before merging**
     - ✅ Require branches to be up to date before merging
     - Add status check: `build` (from the Pages workflow)
   
   - ✅ **Require conversation resolution before merging**
   
   - ✅ **Require linear history** (optional, prevents merge commits)
   
   - ✅ **Do not allow bypassing the above settings**
   
   - ✅ **Restrict who can push to matching branches**
     - Add: `buwon` (repository owner)
     - This ensures only you can push directly to main
   
   - ✅ **Block force pushes** - Recommended to prevent rewriting history (leave unchecked to allow)
   
   - ✅ **Allow deletions** - Leave unchecked (prevents accidental deletion)

7. Click **Create** or **Save changes**

## Result

After applying these settings:
- Direct pushes to `main` will be blocked for everyone except the repository owner
- All changes must go through pull requests
- Pull requests require approval from @buwon (defined in CODEOWNERS)
- Status checks must pass before merging
- Only @buwon can bypass these restrictions if needed

## Alternative: Using GitHub Rulesets (Recommended for better control)

GitHub now offers Rulesets as a more powerful alternative to branch protection rules:

1. Go to **Settings** → **Rules** → **Rulesets**
2. Click **New ruleset** → **New branch ruleset**
3. Name it: "Protect main branch"
4. Set **Enforcement status** to **Active**
5. Add target: **Include default branch** or add pattern `main`
6. Configure rules:
   - **Restrict creations** - Block branch and tag creation
   - **Restrict updates** - Block force pushes
   - **Restrict deletions** - Block branch deletion
   - **Require pull request** - Require PR reviews before merging
   - **Require status checks** - Require CI to pass
   - **Require code owner review** - Enforce CODEOWNERS
   - **Block force pushes** - Prevent force push except from bypass list
7. Set **Bypass list**: Add yourself (buwon) to allow overrides when necessary
8. Click **Create**

## Testing

To verify the protection is working:
1. Try to push directly to main from a different authenticated GitHub account - should fail
2. Create a pull request from a feature branch - should work
3. Try to merge PR without approval - should be blocked
4. Get approval from @buwon - PR should become mergeable

## Notes

- The CODEOWNERS file in this repository ensures that all changes require your approval
- Branch protection rules are configured at the GitHub repository level, not in code
- These settings provide multiple layers of protection for the main branch
