# workflow

## Plan

### Daily
1. Exercism.io (pm/M)
2. InSpec Ubuntu profile and remediation in Chef (1-2 controls)
3. Networking & Conference

### Projects
1. Compliance POC
  - Build a Compliance server in Azure
  - Create another Ubuntu node that will be scanned by Compliance and remediated by Chef
  - Upload ubuntu-14-cis-profile to Compliance server
  - Scan Ubuntu node for failures, and there should be many (RED)
  - Set up a connection to the public Chef server (manage.chef.io)
  - Bootstrap Ubuntu node from Mac
  - Upload remediation cookbook (ubuntu-14-hardening) to the Chef server
  - Configure Ubuntu server to run the desired cookbooks/recipes (ubuntu-14-hardening::default) in run list
  - Run `chef-client` in command line on the Ubuntu node and watch it harden the machine
  - Scan Ubuntu node again, and there shouldn't be any (GREEN)
  - Add the `audit` cookbook to my run list to scan the profile when `chef-client` runs (need direction)
  
 ### Tutorials
   - [Learn the basics](https://learn.chef.io/learn-the-basics/ubuntu/) - skim this one
   - [Manage a node](https://learn.chef.io/manage-a-node/ubuntu/)