# LOCAL COOKBOOK DEVELOPMENT BADGE TOPICS

The Local Cookbook Development badge is awarded when someone proves that they understand the process of developing cookbooks locally. Candidates must show:

- An understanding of authoring cookbooks and setting up the local environment.
- An understanding of the Chef DK tools.
- An understanding of Test Kitchen configuration.
- An understanding of the available testing frameworks.
- An understanding of troubleshooting cookbooks.
- An understanding of search and databags.

_Here is a detailed breakdown of each area._

# COOKBOOK AUTHORING AND SETUP THEORY

## REPO STRUCTURE - MONOLITHIC VS SINGLE COOKBOOK

_Candidates should understand:_

**The pros and cons of a single repository per cookbook**

_Cookbooks should not reside in the Chef Repo but rather be pulled in via dependency management tools like Berkshelf. Each cookbook should have its own Git repository, build process, and test suite. Cookbooks should be treated as software projects of their own. The suggested structure is to completely remove the idea of putting Cookbooks in your Chef Repo all together. Every cookbook would be contained within its own Git repository and every cookbook has its own Berksfile._

From there they suggest creating a build job on a CI server for every cookbook. This job would test and then upload the cookbook it is managing to your Chef Server.

Pros -
 - Each cookbook can be tested, uploaded, and verified by a build server 
 - Change management is simpler 
   - pinning versions to environments is straightforward
   - there can be an open source mentality toward changes to the cookbook
   - the run-list will only allow for one cookbook of that name per organization otherwise confusion abounds
 - You can use the "monolithic" chef-repo pattern as a "management console".
 - A different Berksfile per cookbook means that each cookbook can have different dependencies. So if cookbook A depends on apache v 2.0.0 and cookbook B depends on apache v 2.2.2, B's dependencies don't cancel out A's.

Cons - 
 - The cookbook is shared with other applications, so a change might cause issues.
   - you may or may not be the maintainer of that cookbook which means you may not have rights to change it on the master branch
   - you may need a wrapper cookbook to extend the functionality of that cookbook

**The pros and cons of an application repository**

Pros -
Cons - 

**How the Chef workflow supports monolithic vs single cookbooks**


**How to create a repository/workspace on the workstation**


## VERSIONING OF COOKBOOKS

_Candidates should understand:_
**Why cookbooks should be versioned**


**The recommended methods of maintaining versions (e.g. knife spork)**
`knife spork bump alohaupdate`

**How to avoid overwriting cookbooks**
by Freezing it with the `knife cookbook upload alohaupdate --freeze` or `berks upload` which freezes

**Where to define a cookbook version**
in `metadata.rb`

**Semantic versioning**
`MAJOR.MINOR.PATCH` or `BreakingChanges.BackwardsCompatibleChanges.BackwardsCompatibleBugfixes`

**Freezing cookbooks**


**Re-uploading and freezing cookbooks**
Use `knife cookbook upload alohaupdate --force`


## STRUCTURING COOKBOOK CONTENT

_Candidates should understand:_
**Modular content/reusability**


**Best practices around cookbooks that map 1:1 to a piece of software or functionality vs monolithic cookbooks**


**How to use common, core resources**


## HOW METADATA IS USED

_Candidates should understand:_
**How to manage dependencies**


**Cookbook dependency version syntax**
inside of `metadata.rb` put `depends 'alohaupdate'` or for a version constraint, `depends 'alohaupdate', '> 2.0'`
the operators are `=, >=, >, <, <=, and ~>`. That last operator will go up to the next biggest version.

**What information to include in a cookbook - author, license, etc**


**Metadata settings**
```ruby
name 'alohaupdate'
maintainer 'Michael Hedgpeth'
maintainer_email 'my@email.com'
source_url 'github.com'
```

**What 'suggests' in metadata means**
Same thing as depends, but that you suggest a cookbook be there

**What 'issues_url' in metadata means**
The URL to go to the issues, used for supermarket


## WRAPPER COOKBOOK METHODS

_Candidates should understand:_
**How to consume other cookbooks in code via wrapper cookbooks**
 - You would add those cookbooks as dependencies in the metadata and then consume them by either:
 - `include_recipe` in the recipe and set the attributes in the attributes file (i.e. chef-client cookbook with a lot of recipes)
 - use the resources provided by the cookbook (i.e. Tomcat cookbook with lots of resources and no recipes)
 - MH: Add `depends 'cookbook'` to `metadata.rb` and then either use `include_recipe 'cookbook'` or one of the cookbook's custom resources

**How to change cookbook behavior via wrapper cookbooks**
 - You would add those cookbooks as dependencies in the metadata and then consume them by either:
 - `include_recipe` in the recipe and set the attributes in the attributes file (i.e. chef-client cookbook with a lot of recipes)
 - use the resources provided by the cookbook (i.e. Tomcat cookbook with lots of resources and no recipes)
 - MH: Set the attributes of the cookbook with `node.default['attribute'] = 'overridden_value'` and use `include_recipe`

**Attribute value precedence**
 - OHAI will trump all other attributes. Then `override` will trump other attributes in the order of role, environment, node,/recipe, attribute files. Then the default attributes in that same order.
 - OHAI >> Override (R, E, N, A) >> default (R, E, N, A)
MH: 
 [later overrides earlier](https://docs.chef.io/attributes.html):
 Attribute -> Recipe -> Environment -> Role
 Default -> Normal -> Override -> OHAI

 declared in:
 attribute: `default['attribute'] = value`, `normal['attribute'] = value`, `override['attribute'] = value`
 cookbook: `node.default['attribute'] = value`, `node.normal['attribute'] = value`, `node.override['attribute'] = value`
 environment: `default_attributes({ 'attribute' => 'value'})`, `override_attributes({'attribute' => 'value'})`
 role: `default_attributes({ 'attribute' => 'value'})`, `override_attributes({'attribute' => 'value'})`

**How to use the `include_recipe` directive**
 - Add the recipe to your metadata.rb file, then use the `include_recipe` resource to run that entire recipe within the recipe in which you're including it.
 - MH: add `depends 'cookbook_name'` to `metadata.rb` and then add `include_recipe 'cookbook_name::recipe_name'` to a recipe in your runlist

**What happens if the same recipe is included multiple times**
 - If the `include_recipe` method is used more than once to include a recipe, only the first inclusion is processed and any subsequent inclusions are ignored.
 - MH: Only the first inclusion is processed and any subsequent inclusions are ignored.

**How to use the `depends` directive**
 - If you need a cookbook as a dependency, then you would include `depends '<cookbook>' '<version>'` in the metadata of the wrapper / main cookbook.

## USING COMMUNITY COOKBOOKS

_Candidates should understand:_
**How to use a public and private Supermarket**
Private Supermarket -
 - Create a server to serve as your private supermarket which hosts your cookbooks
 - add the URL of that server as your source to the private supermarket to your Berksfile so that it can look there for the dependent cookbooks and also add it to the knife.rb to upload cookbooks.
 - add all dependencies in the metadata.rb with `depends '<cookbook>' '<version>'`
 - use `knife cookbook site share COOKBOOK_NAME CATEGORY (options)` to upload cookbooks to the supermarket

Public Supermarket - 
 - add the public supermarket link as your source in the Berksfile
 - add all dependencies in the metadata.rb with `depends '<cookbook>' '<version>'`
 - you may only update a cookbook in the public supermarket if you are the owner/maintainer
 - anyone can upload a cookbook to the public supermarket

**How to use community cookbooks**
 - You can either use it from the Supermarket, in which you'd depend on it in metadata, or you can clone it from version control and upload it to the Chef server with berks.

**How to wrap community cookbooks**
 - You would add those cookbooks as dependencies in the metadata and then consume them by either:
    - `include_recipe` in the recipe and set the attributes in the attributes file (i.e. chef-client cookbook with a lot of recipes)
    - use the resources provided by the cookbook (i.e. Tomcat cookbook with lots of resources and no recipes)

**How to fork community cookbooks**
 - fork it to your own repo in git and assume the responsibility to maintain that cookbook from your own repo

**How to use Berkshelf to download cookbooks**
 - MH:  run `berks install` with a valid `Berksfile`

**How to configure a Berksfile**
 - MH: `source` should point to a supermarket (and the first one gets precedence, so you could use a private then public supermarket)
  always include a `metadata` line to get all the `depends` attributes
  if your cookbook comes from another location than the supermarket, specify it, as in: `cookbook "alohaupdate", git: "http://github.com/mhedgpeth/alohaupdate'"`

**How to use a Berksfile to manage a community cookbook and a local cookbook with the same name**
 - MH: By using a private supermarket or specifying the exact location of that cookbook on your private git server, thus making it ignore the supermarket as a default source

## USING CHEF RESOURCES VS ARBITRARY COMMANDS

_Candidates should understand:_
**How to shell out to run commands.**
 - MH: Use the `shell_out` (when errors don't matter) or `shell_out!` (raises error when command fails)

**When/not to shell out**
 - MH: As read-only, not to change state

**How to use the `execute` resource**
 - `execute '/usr/sbin/apachectl configtest'`

**When/not to use the `execute` resource**
 - MH: When you are changing the state of the system

**How to ensure idempotence**
 - MH: By using notifies or the `not_if`/`only_if` clauses

# CHEF DK TOOLS

## `CHEF` COMMAND

_Candidates should understand:_
**What the 'chef' command does**
`chef command [arguments...] [options...]`

```
Available Commands:
    exec                    Runs the command in context of the embedded ruby
    env                     Prints environment variables used by ChefDK
    gem                     Runs the `gem` command in context of the embedded ruby
    generate                Generate a new app, cookbook, or component
    shell-init              Initialize your shell to use ChefDK as your primary ruby
    install                 Install cookbooks from a Policyfile and generate a locked cookbook set
    update                  Updates a Policyfile.lock.json with latest run_list and cookbooks
    push                    Push a local policy lock to a policy group on the server
    push-archive            Push a policy archive to a policy group on the server
    show-policy             Show policyfile objects on your Chef Server
    diff                    Generate an itemized diff of two Policyfile lock documents
    provision               Provision VMs and clusters via cookbook
    export                  Export a policy lock as a Chef Zero code repo
    clean-policy-revisions  Delete unused policy revisions on the server
    clean-policy-cookbooks  Delete unused policyfile cookbooks on the server
    delete-policy-group     Delete a policy group on the server
    delete-policy           Delete all revisions of a policy on the server
    undelete                Undo a delete command
    verify                  Test the embedded ChefDK applications
  ```

**What `chef generate` can create**

```
Available generators:
  app             Generate an application repo
  cookbook        Generate a single cookbook
  recipe          Generate a new recipe
  attribute       Generate an attributes file
  template        Generate a file template
  file            Generate a cookbook file
  lwrp            Generate a lightweight resource/provider
  repo            Generate a Chef code repository
  policyfile      Generate a Policyfile for use with the install/push commands
  generator       Copy ChefDK's generator cookbook so you can customize it
  build-cookbook  Generate a build cookbook for use with Delivery
```

**How to customize content using `generators`**
**The recommended way to create a template**
**How to add the same boilerplate text to every recipe created by a team**
**The 'chef gem' command**

### FOODCRITIC

Candidates should understand:

What Foodcritic is
  Chef-specific linting of cookbooks
Why developers should lint their code
  Consistency
Foodcritic errors and how to fix them
  They all start with `FC001`, you can google that to get to the exact rule
Community coding rules & custom rules
Foodcritic commands
  Just run `foodcritic .` to do a scan from the cookbook folder. Add `--epic-fail` to make it fail when foodcritic fails.
Foodcritic rules 
How to exclude Foodcritic rules
  For the entire cookbook, add `FC###` to the `.foodcritic` file in the root of the cookbook folder
  For single line of code, add the comment at the end of the line: `# ~FC003`

### BERKS

Candidates should understand:

How to use berks to work with upstream dependencies
  by using the supermarket for dependencies, or by doing version pinning
How to work with GitHub & Supermarket
  in the `Berksfile` you can state the `git:` location or have another supermarket listed as your `source:` (or even multiple ones if you want public to be a backup)
How to work with dependent cookbooks
  Most of the time it works with `metadata.rb` inclusion, but you can extend it with the `cookbook` line (by including further version pinning and overriding location)
How to troubleshoot berks issues **(???)**
How to lock cookbook versions
  cookbooks are frozen by default with a `berks upload`
How to manage dependencies using berks
berks commands
  `berks install` loads dependencies locally
  `berks upload` uploads dependencies to chef server
  `berks info alohaupdate` will display information for that cookbook
  `berks list` will list cookbooks and their dependencies
  `berks apply production Berksfile.lock` - will apply the settings in berksfile to the provided environment

### RUBOCOP

Candidates should understand:

How to use RuboCop to check Ruby styles
  `rubocop .` command from the cookbook folder
RuboCop vs Foodcritic
  Rubocop is for ruby style, Foodcritic is for chef style linting
RuboCop configuration & commands
  `--fail-fast` a good option for CI
Auto correction
  `-a` or `--auto-correct` to auto-correct offenses
How to be selective about the rules you run
  
### TEST KITCHEN

Candidates should understand:

Writing tests to verify intent
How to focus tests on critical outcomes
How to test each resource component vs how to test for desired outcomes
Regression testing

## TEST KITCHEN 

### DRIVERS

Candidates should understand:
Test Kitchen provider & platform support
How to use .kitchen.yml to set up complex testing matrices
How to test a cookbook on multiple deployment scenarios
How to configure drivers

### PROVISIONER

Candidates should understand:
The available provisioners
Local Cookbook Development Page 4 v1.0.3
How to configure provisioners
When to use chef-client vs. chef-solo vs. Chef
How to use the shell provisioner  

### SUITES

Candidates should understand:
What a suite is
How to use suites to test different recipes in different environments
Testing directory for InSpec
How to configure suites

### PLATFORMS
Candidates should understand:
How to specify platforms
Common platforms
How to locate base images
Common images and custom images

### KITCHEN COMMANDS
_Candidates should understand:_
**The basic Test Kitchen workflow**


'kitchen' commands
When tests get run
How to install bussers 
What 'kitchen init' does

### COOKBOOK COMPONENTS 
DIRECTORY STRUCTURE OF A COOKBOOK
Candidates should understand:
What the components of a cookbook are
What siblings of cookbooks in a repository are
The default recipe & attributes files
Why there is a 'default' subdirectory under 'templates’
Where tests are stored

### ATTRIBUTES AND HOW THEY WORK 
Candidates should understand:
What attributes are
Attributes as a nested hash
How attributes are defined
How attributes are named
How attributes are referenced
Attribute precedence levels
What Ohai is
Local Cookbook Development Page 5 v1.0.3
What the 'platform' attribute is
How to use the 'platform' attribute in recipes

### FILES AND TEMPLATES - DIFFERENCE AND HOW THEY WORK, WHEN TO USE EACH
Candidates should understand:
How to instantiate files on nodes
The difference between 'file', 'cookbook_file', 'remote_file', and 'template'
How two teams can manage the same file
How to write templates
What 'partial templates' are
Common file-related resource actions and properties
ERB syntax

### CUSTOM RESOURCES - HOW THEY ARE STRUCTURED AND WHERE THEY GO
Candidates should understand:
What custom resources are
How to consume resources specified in another cookbook
Naming conventions
How to test custom resources

### LIBRARIES 
Candidates should understand:
What libraries are and when to use them
Where libraries are stored

## AVAILALABLE TESTING FRAMEWORKS 

### INSPEC
Candidates should understand:
How to test common resources with InSpec
InSpec syntax
How to write InSpec tests
How to run InSpec tests
Where InSpec tests are stored

### CHEFSPEC
Candidates should understand:
What ChefSpec is
The ChefSpec value proposition
What happens when you run ChefSpec
ChefSpec syntax
How to write ChefSpec tests
How to run ChefSpec tests
Where ChefSpec tests are stored
Local Cookbook Development Page 6 v1.0.3

### GENERIC TESTING TOPICS
Candidates should understand:
The test-driven development (TDD) workflow
Where tests are stored
How tests are organized in a cookbook
Naming conventions - how Test Kitchen finds tests
Tools to test code "at rest" 
Integration testing tools
Tools to run code and test the output
When to use ChefSpec in the workflow  
When to use Test Kitchen in the workflow
Testing intent
Functional vs unit testing

## TROUBLESHOOTING 

### READING TEST-KITCHEN OUTPUT
Candidates should understand:
Test Kitchen phases and associated output

### COMPILE VS. CONVERGE
Candidates should understand:
What happens during the compile phase of a chef-client run
What happens during the converge phase of a chef-client run
When pure Ruby gets executed
When Chef code gets executed

## SEARCH AND DATABAGS 

### DATA BAGS
Candidates should understand:
What databags are
Where databags are stored
When to use databags
How to use databags
How to create a databag
How to update a databag
How to search databags
Chef Vault
The difference between databags and attributes
What 'knife' commands to use to CRUD databags

### SEARCH
Candidates should understand:
What data is indexed and searchable
Local Cookbook Development Page 7 v1.0.3
Why you would search in a recipe
Search criteria syntax
How to invoke a search from the command line
How to invoke a search from within a recipe