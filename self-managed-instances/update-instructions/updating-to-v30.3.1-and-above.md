# Updating to v30.3.1 and above

While updating to v30.3.1 and above, it is important to update the sync workflow manually before proceeding.  This is due to the bug [https://source.flxbl.io/flxbl/sfops/issues/38](https://source.flxbl.io/flxbl/sfops/issues/38)\
\
Please update the following two files in your repository before proceeding to executing 'Check Specific Tag and Create-PR'.  You may utilize a bugfix branch and raise a Pull Request to your sfops repository

1. [github/workflows/sync-pr-creator.yml ](https://github.com/adiza-dev/sfops/blob/e1a37b5bda1f7b4f9f2200912d25d624eac8f610/.github/workflows/sync-pr-creator.yml)

```github-actions-workflow
name: Check Specific Tag and Create PR
on:
  workflow_dispatch:
    inputs:
      tag:
        description: 'Enter the tag you want to sync from upstream'
        required: true
jobs:
  check-tag-and-create-pr:
    runs-on: ubuntu-latest
    steps:
      # Checkout the current repo to work with
      - name: Checkout
        uses: actions/checkout@v4
        with:
          fetch-depth: 0
          
      # Create PR
      - name: Sync sfops-gh-actions to org
        env:
          REPO_OWNER: ${{ github.repository_owner}}
          SFOPS_REPO: sfops
          GH_TOKEN: ${{ secrets.GHA_TOKEN }}
          SFOPS_UPSTREAM_URL: ${{ secrets.SFOPS_UPSTREAM_URL }}
          SPECIFIC_TAG: ${{ github.event.inputs.tag }}
        run: |
          chmod +x ./scripts/sync-upstream-with-pr.sh
          ./scripts/sync-upstream-with-pr.sh $REPO_OWNER $SFOPS_REPO $SFOPS_UPSTREAM_URL $SPECIFIC_TAG
      
      - name: Check job status
        if: failure()
        run: |
          echo "❌ The workflow failed. Please check the logs for more details."
          exit 1
```



2. scripts/sync-upstream-with-pr.sh

```
 #!/bin/bash
# Set the origin and UPSTREAM repository URLs
REPO_OWNER=$1
SFOPS_REPO=$2
UPSTREAM_REPO_URL=$3
REPO_URL=$REPO_OWNER/$SFOPS_REPO
MAIN_BRANCH="main"
SPECIFIC_TAG=$4


# Temporary directory for the clone
TEMP_DIR=$(mktemp -d)
echo "Starting the PR creation job..."
echo "Repository URL: $REPO_URL"
echo "sfops Repo Owner: $REPO_OWNER"
echo "Temporary Directory: $TEMP_DIR"


# Clone the repository into the temp directory
echo "Cloning the repository..."
gh auth setup-git
gh repo clone $REPO_URL $TEMP_DIR
# Navigate to the temp directory
cd $TEMP_DIR
# Set up the GitHub bot user
git config user.email "sfops-bot+bot@users.noreply.github.com"
git config user.name "sfops-bot"


# Add the UPSTREAM repository as a remote
git remote add UPSTREAM "$UPSTREAM_REPO_URL"
# Fetch tags from the UPSTREAM repository
git fetch UPSTREAM --tags

echo "Specific tag in the UPSTREAM repository is $SPECIFIC_TAG"

echo "Specific tag in the UPSTREAM repository is $SPECIFIC_TAG"
# Check if the specific tag exists in the UPSTREAM repository
if git ls-remote --tags UPSTREAM "$SPECIFIC_TAG" | grep -q "$SPECIFIC_TAG"; then
  # Fetch the specific tag from the UPSTREAM repository
  git fetch UPSTREAM refs/tags/"$SPECIFIC_TAG":refs/tags/"$SPECIFIC_TAG"

  # Create a new branch from the tag and switch to it
  git checkout -b pr-"$SPECIFIC_TAG" refs/tags/"$SPECIFIC_TAG"

  # Replace version.txt and CHANGELOG.md files from the main branch
  git checkout origin/$MAIN_BRANCH -- version.txt CHANGELOG.md

  # Check if there are any changes to commit
  if git diff --quiet HEAD; then
    echo "No changes detected in version.txt and CHANGELOG.md compared to the main branch."
  else
    # Replace version.txt and CHANGELOG.md files from the main branch
    git checkout origin/$MAIN_BRANCH -- version.txt CHANGELOG.md
    # Commit the changes
    git commit -m "Update version.txt and CHANGELOG.md from main branch"
  fi

  # Merge the main branch into the PR branch
  if git merge -X ours origin/$MAIN_BRANCH; then
    echo "Merge successful. No conflicts detected."
  else
    echo "Merge failed. Conflicts detected."
    # Overwrite conflicting files with the versions from the tag branch
    git checkout --ours -- .
    git add .
    git commit -m "Resolve conflicts by overwriting with tag branch"
  fi

  # Push the new branch to the origin
  git push origin pr-"$SPECIFIC_TAG"
  # Create a PR using GitHub CLI
  gh pr create --title "feat(upstream): update to upstream tag $SPECIFIC_TAG" --body "This PR updates the repository to the latest tag $SPECIFIC_TAG, favoring files from the source.flxbl.io repository and including version.txt and CHANGELOG.md from the main branch." --head pr-"$SPECIFIC_TAG"
else
  echo "Tag $SPECIFIC_TAG does not exist in the UPSTREAM repository. Skipping branch creation and PR creation."
fi


# Clean up: change out of the temp directory and remove it
cd ..
rm -rf "$TEMP_DIR"
```

Once the above changes are merged, please proceed to execute 'Check Specific Tag and Create-PR' as usual and provide the correct tag to be synchronized
