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

### The pros and cons of a single repository per cookbook

 - _Cookbooks should not reside in the Chef Repo but rather be pulled in via dependency management tools like Berkshelf. Each cookbook should have its own Git repository, build process, and test suite. Cookbooks should be treated as software projects of their own. The suggested structure is to completely remove the idea of putting Cookbooks in your Chef Repo all together. Every cookbook would be contained within its own Git repository and every cookbook has its own Berksfile._

 - From there they suggest creating a build job on a CI server for every cookbook. This job would test and then upload the cookbook it is managing to your Chef Server.

**Pros -**
 - Each cookbook can be tested, uploaded, and verified by a build server 
 - Change management is simpler 
   - pinning versions to environments is straightforward
   - there can be an open source mentality toward changes to the cookbook
   - the run-list will only allow for one cookbook of that name per organization otherwise confusion abounds
 - You can use the "monolithic" chef-repo pattern as a "management console".
 - A different Berksfile per cookbook means that each cookbook can have different dependencies. So if cookbook A depends on apache v 2.0.0 and cookbook B depends on apache v 2.2.2, B's dependencies don't cancel out A's.

**Cons -**
 - The cookbook is shared with other applications, so a change might cause issues.
   - you may or may not be the maintainer of that cookbook which means you may not have rights to change it on the master branch
   - you may need a wrapper cookbook to extend the functionality of that cookbook

### [The pros and cons of an application repository](https://chef.github.io/chef-rfc/rfc019-chef-workflows.html)
**Pros -**
 - Everything needed for your application is in one place so it lessens confusion.
 - `chef vendor dependencies` does what berks would do to install cookbook dependencies.
 - You can test all your cookbooks together.

**Cons -**
Everything needed for your application must be the same version, creating ownership and change management issues. 

### How the Chef workflow supports monolithic vs single cookbooks
 - Monolithic: All of your Chef related source code, including any 3rd party dependencies, are tracked in one source control repository using Git. External dependencies, and any local modifications to them, are made with built-in vendor branches, allowing you to easily track the upstream for modifications.

 - Single: All of the Chef cookbooks are treated as independent software projects, that can be built in isolation from any other cookbook. External dependencies are fetched as-needed, and treated as artifacts. Changes to the upstream creates a new software projects, and is tracked as such.

### How to create a repository/workspace on the workstation
 - You can either run `chef generate repo [name]` or download a starter kit from the Chef server for your organization.

## VERSIONING OF COOKBOOKS
_Candidates should understand:_

### Why cookbooks should be versioned
 - Cookbooks need versions for running different cookbooks on their different environments.

### The recommended methods of maintaining versions (e.g. knife spork)
 - `knife spork bump alohaupdate` will automatically update the version number in your metadata if you don't manually change it in the `metadata.rb`.

### How to avoid overwriting cookbooks
 - by Freezing it with the `knife cookbook upload alohaupdate --freeze` or `berks upload` which freezes

### Where to define a cookbook version
 - in `metadata.rb`

### Semantic versioning
 - `MAJOR.MINOR.PATCH` or `BreakingChanges.BackwardsCompatibleChanges.BackwardsCompatibleBugFixes`
   - MAJOR version when you make incompatible API changes
   - MINOR version when you add functionality in a backwards-compatible manner
   - PATCH version when you make backwards-compatible bug fixes

### [Freezing cookbooks](https://docs.chef.io/cookbook_versions.html#freeze-versions)
 - A cookbook version can be frozen, which will prevent updates from being made to that version of a cookbook. (A user can always upload a new version of a cookbook.) Using cookbook versions that are frozen within environments is a reliable way to keep a production environment safe from accidental updates while testing changes that are made to a development infrastructure.

### Re-uploading and freezing cookbooks
 - To freeze a cookbook version using knife, enter: `knife cookbook upload redis --freeze`
 - Once a cookbook version is frozen, only by using the --force option can an update be made. `knife cookbook upload redis --force`

## STRUCTURING COOKBOOK CONTENT
_Candidates should understand:_

### Modular content/reusability

### Best practices around cookbooks that map 1:1 to a piece of software or functionality vs monolithic cookbooks
 - 1:1 cookbooks are preferred as each piece of software needs to be managed separately within its own git repo.
   - Versioning - you can tag specific releases 
   - Hands in the pot - you can easily give everyone clone access, but restrict push access to select teams for certain repos 
   - History - if you need to history for a certain cookbook, you shouldn't have to run a complex git command to parse the logs 
   - Monolithic things are generally anti-patterns

### How to use common, core resources
 - (This is accomplished with the [chefkata](https://github.com/anniehedgpeth/chefkata).)
 - `:nothing` is the only action that can be used with any resource.
 - `file` `cookbook_file` `remote_file` `template` actions: 
   - `:create` `:create_if_missing` `:delete` `:nothing` `:touch`
 - Properties common to every resource include:
   - `ignore_failure` `provider` `retries` `retry_delay` `sensitive` `supports`
 - `execute` actions: `:nothing` `:run`
 - Common `execute` properties: `command` `notifies` `creates` (Prevent a command from creating a file when that file already exists.) `path` `returns`

```ruby
cookbook_file '/var/www/customers/public_html/index.php' do
  source 'index.php'
  owner 'web_admin'
  group 'web_admin'
  mode '0755'
  action :create
end
```

## HOW METADATA IS USED
_Candidates should understand:_

### How to manage dependencies
 - If you're using Berks, you would add `depends 'cookbook', 'version'` in the `metadata.rb`, and in the `Berksfile` you would add the `source`, such as the supermarket at `'https://supermarket.chef.io'`. 
 - If you're not using Berks, then you would add the dependencies to the `metadata.rb` in the same way, but you would run `knife upload `knife deps nodes/*.json` to use the output of knife deps to pass command to knife upload.

### Cookbook dependency version syntax
 - Inside of `metadata.rb` put `depends '<cookbook>'` or for a version constraint, `depends '<cookbook>', '> 2.0'`
   - The operators are `=, >=, >, <, <=, and ~>`. That last operator `~>` will go up to the next biggest version.

### What information to include in a cookbook - author, license, etc
**Metadata settings**

```ruby
name 'chefkata'
maintainer 'Annie Hedgpeth'
maintainer_email 'annie.hedgpeth@gmail.com'
license 'all_rights'
description 'Installs/Configures chefkata'
long_description 'Installs/Configures chefkata'
version '0.1.0'
issues_url 'https://github.com/<insert_org_here>/chefkata/issues' if respond_to?(:issues_url)
source_url 'https://github.com/<insert_org_here>/chefkata' if respond_to?(:source_url)
```

### What 'suggests' in metadata means
 - Same thing as depends, but that you suggest a cookbook be there. The cookbook will still run if that dependency doesn't exist.

### What 'issues_url' in metadata means
 - The `issues_url` points to the location where issues for this cookbook are tracked.  A `View Issues` link will be displayed on this cookbook's page when uploaded to a Supermarket.
 - The `source_url` points to the development repository for this cookbook.  A `View Source` link will be displayed on this cookbook's page when uploaded to a Supermarket.


## WRAPPER COOKBOOK METHODS
_Candidates should understand:_

### How to consume other cookbooks in code via wrapper cookbooks
 - You would add those cookbooks as dependencies in the metadata and then consume them by either:
   - `include_recipe` in the recipe and set the attributes in the attributes file (i.e. chef-client cookbook with a lot of recipes)
   - use the resources provided by the cookbook (i.e. Tomcat cookbook with lots of resources and no recipes)

### How to change cookbook behavior via wrapper cookbooks
 - You would add those cookbooks as dependencies in the metadata and then consume them by either:
   - `include_recipe` in the recipe and set the attributes in the attributes file (i.e. chef-client cookbook with a lot of recipes)
   - use the resources provided by the cookbook (i.e. Tomcat cookbook with lots of resources and no recipes)
 - [MH:](https://github.com/mhedgpeth/mhedgpeth.github.io/blob/master/_drafts/local-cookbook-development-notes.md) Set the attributes of the cookbook with `node.default['attribute'] = 'overridden_value'` and use `include_recipe`

### Attribute value precedence
 - OHAI will trump all other attributes. Then `override` will trump other attributes in the order of role, environment, node,/recipe, attribute files. Then the default attributes in that same order.
 - OHAI >> Override (R, E, N, A) >> default (R, E, N, A)
 - [MH:](https://github.com/mhedgpeth/mhedgpeth.github.io/blob/master/_drafts/local-cookbook-development-notes.md) [later overrides earlier](https://docs.chef.io/attributes.html):
   - Attribute -> Recipe -> Environment -> Role
   - Default -> Normal -> Override -> OHAI

  - declared in:
    - attribute: `default['attribute'] = value`, `normal['attribute'] = value`, `override['attribute'] = value`
    - cookbook: `node.default['attribute'] = value`, `node.normal['attribute'] = value`, `node.override['attribute'] = value`
    - environment: `default_attributes({ 'attribute' => 'value'})`, `override_attributes({'attribute' => 'value'})`
    - role: `default_attributes({ 'attribute' => 'value'})`, `override_attributes({'attribute' => 'value'})`

### How to use the `include_recipe` directive**
 - Add the recipe to your metadata.rb file, then use the `include_recipe` resource to run that entire recipe within the recipe in which you're including it.
 - [MH:](https://github.com/mhedgpeth/mhedgpeth.github.io/blob/master/_drafts/local-cookbook-development-notes.md) add `depends 'cookbook_name'` to `metadata.rb` and then add `include_recipe 'cookbook_name::recipe_name'` to a recipe in your runlist

### What happens if the same recipe is included multiple times
 - If the `include_recipe` method is used more than once to include a recipe, only the first inclusion is processed and any subsequent inclusions are ignored.
 - [MH:](https://github.com/mhedgpeth/mhedgpeth.github.io/blob/master/_drafts/local-cookbook-development-notes.md) Only the first inclusion is processed and any subsequent inclusions are ignored.

### How to use the `depends` directive
 - If you need a cookbook as a dependency, then you would include `depends '<cookbook>' '<version>'` in the metadata of the wrapper / main cookbook.

## USING COMMUNITY COOKBOOKS
_Candidates should understand:_

### How to use a public and private Supermarket
 - Private Supermarket -
   - Create a server to serve as your private supermarket which hosts your cookbooks
   - add the URL of that server as your source to the private supermarket to your Berksfile so that it can look there for the dependent cookbooks and also add it to the knife.rb to upload cookbooks.
   - add all dependencies in the metadata.rb with `depends '<cookbook>' '<version>'`
   - use `knife cookbook site share COOKBOOK_NAME CATEGORY (options)` to upload cookbooks to the supermarket

 - Public Supermarket - 
   - add the public supermarket link as your source in the Berksfile
   - add all dependencies in the metadata.rb with `depends '<cookbook>' '<version>'`
   - you may only update a cookbook in the public supermarket if you are the owner/maintainer
   - anyone can upload a cookbook to the public supermarket

### How to use community cookbooks
 - You can either use it from the Supermarket, in which you'd depend on it in metadata, or you can clone it from version control and upload it to the Chef server with berks.

### How to wrap community cookbooks
 - You would add those cookbooks as dependencies in the metadata and then consume them by either:
    - `include_recipe` in the recipe and set the attributes in the attributes file (i.e. chef-client cookbook with a lot of recipes)
    - use the resources provided by the cookbook (i.e. Tomcat cookbook with lots of resources and no recipes)

### How to fork community cookbooks
 - Fork it to your own repo in git and assume the responsibility to maintain that cookbook from your own repo

### How to use Berkshelf to download cookbooks
 - run `berks install` with a valid `Berksfile` containing the source for the dependencies

### How to configure a Berksfile
 - [MH:](https://github.com/mhedgpeth/mhedgpeth.github.io/blob/master/_drafts/local-cookbook-development-notes.md) `source` should point to a supermarket (and the first one gets precedence, so you could use a private then public supermarket)
   - Always include a `metadata` line to get all the `depends` attributes from the `metadata.rb`.
   - If your cookbook comes from another location than the supermarket, specify it, as in: `cookbook "<cookbook>", git: "http://github.com/username/repo'"`

### How to use a Berksfile to manage a community cookbook and a local cookbook with the same name
 - [MH:](https://github.com/mhedgpeth/mhedgpeth.github.io/blob/master/_drafts/local-cookbook-development-notes.md) By using a private supermarket or specifying the exact location of that cookbook on your private git server, thus making it ignore the supermarket as a default source

## USING CHEF RESOURCES VS ARBITRARY COMMANDS
_Candidates should understand:_

### [How to shell out to run commands](https://docs.chef.io/dsl_recipe.html#shell-out)
 - [MH:](https://github.com/mhedgpeth/mhedgpeth.github.io/blob/master/_drafts/local-cookbook-development-notes.md) Use the `shell_out` (when errors don't matter) or `shell_out!` (raises error when command fails)
 - The `shell_out` method can be used to run a command against the node, and then display the output to the console when the log level is set to debug.
   - `shell_out(command_args)` where command_args is the command that is run against the node
 - The `shell_out!` method can be used to run a command against the node, display the output to the console when the log level is set to debug, and then raise an error when the method returns false.
   - `shell_out!(command_args)` where command_args is the command that is run against the node. This method will return true or false.
 - The `shell_out_with_systems_locale` method can be used to run a command against the node (via the `shell_out` method), but using the LC_ALL environment variable.
   - `shell_out_with_systems_locale(command_args)` where command_args is the command that is run against the node.

### When/not to shell out
 - [MH:](https://github.com/mhedgpeth/mhedgpeth.github.io/blob/master/_drafts/local-cookbook-development-notes.md) As read-only, not to change state

### How to use the `execute` resource
 - `execute '/usr/sbin/apachectl configtest'`

### When/not to use the `execute` resource
 - [MH:](https://github.com/mhedgpeth/mhedgpeth.github.io/blob/master/_drafts/local-cookbook-development-notes.md) When you are changing the state of the system

### How to ensure idempotence
 - by using guard clauses
 - [MH:](https://github.com/mhedgpeth/mhedgpeth.github.io/blob/master/_drafts/local-cookbook-development-notes.md) By using notifies or the `not_if`/`only_if` clauses

# CHEF DK TOOLS

## `CHEF` COMMAND
_Candidates should understand:_

### What the `chef` command does
 - The `chef` command is used for generation and is like the `knife` command. You'd use it in place of `knife` when you use `policyfiles`.

 - `chef command [arguments...] [options...]`

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

### What `chef generate` can create

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

### [How to customize content using `generators`](https://blog.chef.io/2014/12/09/guest-post-creating-your-own-chef-cookbook-generator/)
 - `chef generate cookbook my_cookbook_name -g ~/chef/pan`
  - This is a way that you can templatize the way in which you create a chef repo with settings specific to your organization.

### The recommended way to create a template
 - `chef generate template <templatename>`

### How to add the same boilerplate text to every recipe created by a team
 - According to [this](https://medium.com/@echohack/creating-your-own-chef-cookbook-generator-5e998db1255b), you'd add it to the `code_generator` at `/opt/chefdk/embedded/apps/chef-dk/lib/chef-dk/skeletons/code_generator`.

### The ['chef gem'](https://docs.chef.io/ctl_chef.html#chef-gem) command
 - The chef gem subcommand is a wrapper around the gem command in RubyGems and is used by Chef to install RubyGems into the Chef development kit development environment. All knife plugins, drivers for Kitchen, and other Ruby applications that are not packaged within the Chef development kit will be installed to the .chefdk path in the home directory: ~/.chefdk/gem/ruby/ver.si.on/bin (where ver.si.on is the version of Ruby that is packaged within the Chef development kit).

## [FOODCRITIC](https://docs.chef.io/foodcritic.html)
_Candidates should understand:_

### What Foodcritic is
 - Chef-specific linting of cookbooks
 - Use Foodcritic to check cookbooks for common problems:
   - Style
   - Correctness
   - Syntax
   - Best practices
   - Common mistakes
   - Deprecations

### Why developers should lint their code
 - Consistency

### Foodcritic errors and how to fix them
 - [MH:](https://github.com/mhedgpeth/mhedgpeth.github.io/blob/master/_drafts/local-cookbook-development-notes.md) They all start with `FC001`; you can google that to get to the exact rule.

### Community coding rules & custom rules

### Foodcritic commands
 - `foodcritic /path/to/cookbook`
 - [MH:](https://github.com/mhedgpeth/mhedgpeth.github.io/blob/master/_drafts/local-cookbook-development-notes.md) Just run `foodcritic .` to do a scan from the cookbook folder. Add `--epic-fail` to make it fail when foodcritic fails.
 - A Foodcritic evaluation has the following syntax: `RULENUMBER: MESSAGE: FILEPATH:LINENUMBER`

### [Foodcritic rules](http://www.foodcritic.io/)
 - It comes with 60 built-in rules that identify problems ranging from simple style inconsistencies to difficult to diagnose issues that will hurt in production. 
 - Various nice people in the Chef community have also written extra rules for foodcritic that you can install and run. Or write your own!
   - [Custom Customink Rules](https://github.com/customink-webops/foodcritic-rules/blob/master/rules.rb)
   - [Custom Etsy Rules](https://github.com/etsy/foodcritic-rules)

### How to exclude Foodcritic rules
 - `foodcritic . --tags ~RULE`
 - [MH:](https://github.com/mhedgpeth/mhedgpeth.github.io/blob/master/_drafts/local-cookbook-development-notes.md) For the entire cookbook, add `FC###` to the `.foodcritic` file in the root of the cookbook folder
 - [MH:](https://github.com/mhedgpeth/mhedgpeth.github.io/blob/master/_drafts/local-cookbook-development-notes.md) For single line of code, add the comment at the end of the line: `# ~FC003`

## BERKS
_Candidates should understand:_

### How to use berks to work with upstream dependencies
 - [MH:](https://github.com/mhedgpeth/mhedgpeth.github.io/blob/master/_drafts/local-cookbook-development-notes.md) by using the supermarket for dependencies, or by doing version pinning

### How to work with GitHub & Supermarket
 -  [MH:](https://github.com/mhedgpeth/mhedgpeth.github.io/blob/master/_drafts/local-cookbook-development-notes.md) in the `Berksfile` you can state the `git:` location or have another supermarket listed as your `source:` (or even multiple ones if you want public to be a backup)

### How to work with dependent cookbooks
 - [MH:](https://github.com/mhedgpeth/mhedgpeth.github.io/blob/master/_drafts/local-cookbook-development-notes.md) Most of the time it works with `metadata.rb` inclusion, but you can extend it with the `cookbook` line (by including further version pinning and overriding location).

### How to troubleshoot berks issues


### How to lock cookbook versions
 - [MH:](https://github.com/mhedgpeth/mhedgpeth.github.io/blob/master/_drafts/local-cookbook-development-notes.md) cookbooks are frozen by default with a `berks upload`

### How to manage dependencies using berks
 - [MH:](https://github.com/mhedgpeth/mhedgpeth.github.io/blob/master/_drafts/local-cookbook-development-notes.md) berks commands
   - `berks install` loads dependencies locally
   - `berks upload` uploads dependencies to chef server
   - `berks info <cookbook>` will display information for that cookbook
   - `berks list` will list cookbooks and their dependencies
   - `berks apply production Berksfile.lock` will apply the settings in berksfile to the provided environment

## [RUBOCOP](https://docs.chef.io/rubocop.html)
_Candidates should understand:_

### How to use RuboCop to check Ruby styles
 - [MH:](https://github.com/mhedgpeth/mhedgpeth.github.io/blob/master/_drafts/local-cookbook-development-notes.md) `rubocop .` command from the cookbook folder

### RuboCop vs Foodcritic
 - [MH:](https://github.com/mhedgpeth/mhedgpeth.github.io/blob/master/_drafts/local-cookbook-development-notes.md) Rubocop is for ruby style, Foodcritic is for chef style linting
 - Use RuboCop to author better Ruby code:
   - Enforce style conventions and best practices
   - Evaluate the code in a cookbook against metrics like “line length” and “function size”
   - Help every member of a team to author similary structured code
   - Establish uniformity of source code
   - Set expectations for fellow (and future) project contributors

### RuboCop configuration & commands
 - [MH:](https://github.com/mhedgpeth/mhedgpeth.github.io/blob/master/_drafts/local-cookbook-development-notes.md) `--fail-fast` a good option for CI

### Auto correction
 - [MH:](https://github.com/mhedgpeth/mhedgpeth.github.io/blob/master/_drafts/local-cookbook-development-notes.md) `-a` or `--auto-correct` to auto-correct offenses

### How to be selective about the rules you run
 - Each cookbook has its own `.rubocop.yml` file, which means that each cookbook may have its own set of enabled, disabled, and custom rules. That said, it’s more common for all cookbooks to have the same set of enabled, disabled, and custom rules. When RuboCop is run against a cookbook, the full set of enabled and disabled rules (as defined the `enabled.yml` and `disabled.yml` files in RuboCop itself) are loaded first, and are then compared against the settings in the cookbook’s `.rubocop.yml` file.

```ruby
NAME_OF_RULE:
  Description: 'a description of a rule'
  Enabled : (true or false)
  KEY: VALUE
```
 - Use a `.rubocop_todo.yml` file to capture the current state of all evaluations, and then write them to a file. This allows evaluations to reviewed one at a time. Disable any evaluations that are unhelpful, and then address the ones that are.
 - To generate the `.rubocop_todo.yml` file, run the following command: `rubocop --auto-gen-config`
   - Rename this file to `.rubocop.yml` to adopt this evaluation state as the standard. 
   - Include this file in the `.rubocop.yml` file by adding `inherit_from: .rubocop_todo.yml` to the top of the `.rubocop.yml` file.


## TEST KITCHEN
_Candidates should understand:_

### Writing tests to verify intent
 - One should use InSpec to write tests that verify that the desired state was achieved, not necessarily that all of the resources simply converged but that they're functioning in the desired state.

### How to focus tests on critical outcomes
 - One should use InSpec to write tests that verify that the desired state was achieved, not necessarily that all of the resources simply converged but that they're functioning in the desired state.

### How to test each resource component vs how to test for desired outcomes
 - One should use InSpec to write tests that verify that the desired state was achieved, not necessarily that all of the resources simply converged but that they're functioning in the desired state.

### Regression testing
 - Running `kitchen converge` twice will ensure your policy applies without error to existing instances
 - Running `kitchen test` will ensure your policy applies without error to any new instances

# TEST KITCHEN 

## DRIVERS
_Candidates should understand:_

### Test Kitchen provider & platform support

### How to use .kitchen.yml to set up complex testing matrices

### How to test a cookbook on multiple deployment scenarios

### How to configure drivers

## PROVISIONER
_Candidates should understand:_

### The available provisioners

### How to configure provisioners

### When to use chef-client vs. chef-solo vs. Chef

### How to use the shell provisioner  


## SUITES
_Candidates should understand:_

### What a suite is

### How to use suites to test different recipes in different environments

### Testing directory for InSpec

### How to configure suites

## PLATFORMS
_Candidates should understand:_

### How to specify platforms

### Common platforms

### How to locate base images

### Common images and custom images

## KITCHEN COMMANDS
_Candidates should understand:_

### The basic Test Kitchen workflow

### 'kitchen' commands

### When tests get run

### How to install bussers 

### What 'kitchen init' does

## COOKBOOK COMPONENTS 
DIRECTORY STRUCTURE OF A COOKBOOK
Candidates should understand:
What the components of a cookbook are
What siblings of cookbooks in a repository are
The default recipe & attributes files
Why there is a 'default' subdirectory under 'templates’
Where tests are stored

## ATTRIBUTES AND HOW THEY WORK 
_Candidates should understand:_

### What attributes are
 - values defined in a file within the attributes directory

### Attributes as a nested hash


### How attributes are defined


### How attributes are named


How attributes are referenced
 - `node[key:value]`

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
### Common file-related resource actions and properties
 - 

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