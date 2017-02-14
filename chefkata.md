# Create a new repo for today's kata
 - from code/chef/work create a new repo `chef generate repo chefkata4`
 - create /code/chef/work/chefkata4/.chef and copy the user pem inside the .chef directory
 - generate knife.rb "generate knife config" for that org to put in .chef folder, too

# Create a new cookbook branch
 - from code/chef/work/chefkata4/cookbooks `git clone https://github.com/anniehedgpeth/chefkata.git`
 - from code/chef/work/chefkata4/cookbooks/chefkata create a branch, name it, and switch to it  `git checkout -b <branch-name>`
 
# Add the second cookbook for the runlist
 - add the ubuntu cookbook to that cookbooks directory and make sure it converges `git clone https://github.com/anniehedgpeth/ubuntu-14-hardening.git`
`berks install`
`berks upload`


Any time I change the cookbook, from that cookbook’s directory, I need to:
 - bump the version in `metadata.rb` or it won't upload the new one
 - (you would normally automate that in Jenkins)
`berks install`
`berks upload`

# To bootstrap a new machine:

`knife bootstrap chefkata5.southcentralus.cloudapp.azure.com -N chefkata5 -r 'recipe[chefkata::default], recipe[ubuntu-14-hardening::default]' --ssh-user annie --sudo`

# To converge on that node from here on out:

run `sudo chef-client` in an ssh session

# To validate with InSpec Profile
First you have to add your private key to the local ssh (I don't know if it matters which directory you're in.)
`ssh-add`
`ssh annie@chefkata5.southcentralus.cloudapp.azure.com`
`inspec exec https://github.com/anniehedgpeth/chefkata_inspec -t ssh://annie@chefkata5.southcentralus.cloudapp.azure.com`

# To add run-list:

`knife node run_list set chefkata2 'recipe[chefkata::default]'`

# To edit the run-list:

 - make sure the cookbook is uploaded
 - make sure it has a `Berksfile`
 - `berks install`
 - `berks upload`
 - `knife node run_list add chefkata2 'ubuntu-14-hardening'`

# When adding an organization to manage.chef.io
 - add the org
 - reset user key
generate knife config from UI
copy the user key into .chef folder
create a new org key and download knife.rb
see if your user needs a new key
`knife node list`

# Search
knife search node "platform:ubuntu"
knife search node "builder:Annie"

# Data bags
 - shared data that your cookbooks can use
 - data_bags directory is in the chef_repo sibling to cookbooks directory
 - each data bag is a folder and each data_bag item is a .json file within that folder
 - the data_bag item is just a .json file of all of settings for that data_bag item
   - must include `{ "id":"<data_bag_item_name>" }`
 - upload data_bag item to chef server so that you can use it in your cookbook
   - run this from the top of the chef repo directory `knife data bag from file BAG_NAME ITEM_NAME.json`
   - `knife data bag from file website messages.json`
 - Verify that it's there in UI 
   - Policy > Data Bags > name
 - Verify that it's there from command line 
  - `knife data bag list`

## Using data bags in the recipe
 - First we accessing the data from that item
 `messages = data_bag_item('website', 'messages')`
 - Then we're calling the specific data element from that item 
 `message = messages['welcomeMessage']`
 - Then call it in the recipe like `content message`

## Using data bags in test kitchen
So kitchen can't look inside the "real" data bags directory, so you have to set up a dummy data bags directory just for test kitchen.
 - Create cookbooks/thiscookbook/test/integration/data_bags and copy your real data bag directory into that
 - Then edit the .kitchen.yml

```yaml
suites:
 - name: default
   data_bags_path: "test/integration/data_bags"
```