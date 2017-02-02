# To Make a New Cookbook
 - Create a new repo in Github and copy the link
 - From the terminal, navigate to the cookbooks directory in my chef_repo directory and run
 `git clone <link>`
 - Now run `chef generate cookbook <COOKBOOK_PATH/COOKBOOK_NAME> (options)` from the directory that you want to parent your cookbook


# Before Uploading Policy to Chef Server...

## Code changes for hardening cookbook
 - adding / editing cookbook files
 - adding / editing InSpec profiles

## Make sure everything passes
 - Converge all resources successfully.
 - All tests pass.

## Update version in metatdata.rb

## Commit and Push to Github
Here's a good link for [branching and committing](https://github.com/Kunena/Kunena-Forum/wiki/Create-a-new-branch-with-git-and-manage-branches).

# Now put this version into production
 - go to Chef workflow #2 on "Install and upload policy to Chef server" to deploy

***********************

Any time I change the cookbook, from that cookbook’s directory, I need to:
bump the version in `metadata.rb`
 - (you would normally automate that in Jenkins)
`berks install`
`berks upload`

To bootstrap a new machine:
`knife bootstrap cheftraining.southcentralus.cloudapp.azure.com -x annie -i ~/.ssh/id_rsa --sudo -N cheftraining`

To converge on that node from here on out:
run `chef-client` in an ssh session

To add run-list:
`knife node run_list set cheftraining 'recipe[cheftraining::default]'`