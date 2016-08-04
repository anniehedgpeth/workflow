# Set up connection from local machine to Chef server
First, I need to have an account on manage.chef.io so that my local computer can talk to their server. This establishes a connection for communication. After that I'm able to upload all of my cookbooks to the server so that I can converge any node I want to with those cookbooks.

1. Create chef_repo folder
2. Inside that, create .chef folder
3. On the Chef server in my web browser, click on Admin tab and click on my organization
4. Click "Generate Knife.rb"
5. Go to Users and select my user
6. Click "Reset key" and click "download"
7. Copy those files from the Download folder to the .chef folder
8. Run `knife ssl fetch`

# Bootstrap node
The next thing I need to do is set up a line of communication between my the node(s) and the Chef server. This, however, is done on my local machine. The `knife bootstrap` command installs chef-client on the node that I'm bootstrapping. This process now allows my node(s) to communicate with Chef server. And the Chef server makes the cookbooks available to the node(s) for convergence. 

1. run `knife bootstrap`
`knife bootstrap <ipaddress> -x <nodeUsername> -P <nodePassword> --sudo --policy-group <PolicyGroupName> --policy-name <nameInPolicyfile> -N <NodeName>`
2. ensure that the following lines are in `/etc/chef/client.rb`
 -  policy_name "ubuntu-server"
 -  policy_group "production"
 -  use_policyfile true

# Install and upload policy to Chef server
When I install a Policyfile in a cookbook, I'm then able to tell it all of the other cookbooks that I want to run at the same time and where to find them. So then the Chef server knows which cookbooks to put on which nodes because the nodes tell it which policy they have. 

1. On your command line, from your cookbook directory, remove the `Policyfile.lock.json` file if it exists
2. Run `chef install` inside of the particular cookbook folder in which the Policyfile.rb is located.
3. Run `chef push <policyGroup> Policyfile.rb`
4. `chef show-policy` to show the active policies for each group. The ID it uses should match the ID inside of the json file.
5. Commit to Git so that you can have a history of the json for every time you do this 

# Converge the node
When I'm converging a node, I'm basically running the chef-client command which runs all of the recipes and cookbooks that are in the Policyfile on the node(s). 

1. In an ssh session to the node run `sudo chef-client`

# Scan for compliance errors on Compliance server
After I've converged the node(s), then I want to scan them so that I can see what, if anything, still needs to be remediated. 