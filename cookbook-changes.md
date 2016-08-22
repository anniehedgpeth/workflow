# To Make a New Cookbook
 - Create a new repo in Github and copy the link
 - From the terminal, navigate to the cookbooks directory in my chef_repo directory and run
 `git clone <link>`
 - Now run `chef generate cookbook`


# Before Uploading Policy to Chef Server...

## Code changes for hardening cookbook
 - adding / editing cookbook files
 - adding / editing InSpec profiles

## Make sure everything passes
 - Converge all resources successfully.
 - All tests pass.

## Update version in metatdata.rb

## Commit and Push to Github

# Now put this version into production
 - go to Chef workflow #2 on "Install and upload policy to Chef server" to deploy