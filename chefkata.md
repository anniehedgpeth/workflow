Any time I change the cookbook, from that cookbook’s directory, I need to:
bump the version in `metadata.rb`
 - (you would normally automate that in Jenkins)
`berks install`
`berks upload`

# To bootstrap a new machine:

`knife bootstrap chefkata4.southcentralus.cloudapp.azure.com -N chefkata4 -r 'recipe[chefkata::default], recipe[ubuntu14-hardening::default]' --ssh-user annie --sudo`

# To converge on that node from here on out:

run `sudo chef-client` in an ssh session

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