# Study Sheet for Basic	Chef Fluency

[## BASIC CHEF FLUENCY	BADGE	TOPICS](https://training.chef.io/static/Basic_Chef_Fluency_Badge_Scope.pdf)

**The	Basic	Chef Fluency badge is	awarded	when someone proves	that they	understand the core	elements that	underpin Chef. Candidates	must show:**

• An understanding of basic Chef terminology.
• An understanding of Chef product offerings.
• An understanding of Chef's design philosophy.
• An understanding of Chef's approach to workflow and compliance.
• An understanding of basic Chef coding skills.

Here is a detailed breakdown of each area.

## CHEF	BASIC	TERMINOLOGY	RESOURCES
*Candidates should	understand:*

### Idempotency/convergence	- test &	repair model
When a node is converged, chef-client first tests to see if the node is in the same state as defined by the recipe. If it is not, then it repairs the node in accordance to the recipe.

## Common	resources	and	their	actions						
*Default actions*
The ':nothing' action
 - This simply means that this resource will do nothing to the node unless told otherwise.

The 'supports' directive
 - The `supports` directive specifies the platform that the cookbook supports. This is specified within the cookbook's `metadata.rb`. 

The 'not_if' and 'only_if' directives
 - You may specify that a resource takes action only if or not if another requirement is met or not met.

## Resource extensibility
**RECIPES**
*Candidates	should understand:*

What a recipe is						
 - A recipe is an ordered script within a cookbook that can be called within a chef-client run.

Importance of the	resource order
 - The resources of a recipe are called in the order in which they are written because some require that others exist or not before they can be called.

How to use 'include_recipe'
 - To include another recipe within a recipe, one must first make the cookbook dependent upon that recipe within the metadata of that cookbook. Then you may use the `include_recipe` resource within the recipe to actually execute that dependent recipe.

What happens if a recipe is included multiple times in a run_list
 - The node will be idempotent, so as the recipe runs a subsequent time, it will again test and repair.

The 'notifies' and 'subscribes' directives
 - A resource can notify another resource of its actions in order for the secondary resource to take action based upon the primary resource's actions. Conversely, a resource can subscribe to another resource to listen for its actions and take action itself based on the actions of the resource to which is it subscribed.

## COOKBOOKS
*Candidates should understand:*

Cookbook contents
 - A cookbooks' primary contents include recipes, attributes, resources, metadata, `.kitchen.yml`, data bags, roles, environments.

Naming conventions
 - Underscores should be used instead of hyphens.

Cookbook dependencies
 - You can declare the dependencies of your cookbook on other cookbooks by including those cookbooks in the `metadata.rb` by using `depends '<cookbookname>', 'versionoptional'`. If you're using dependencies that are not stored in source control or the Supermarket, you may use Berks to upload those dependencies to the Chef server from your workstation.

The default recipe
 - If there is only one recipe in the cookbook, then this will be called the default recipe. The default recipe is called when the cookbook is listed without a recipe name in a run list.

## CHEF SERVER
*Candidates should understand:*

How the Chef server acts as an artifact repository
 - The Chef server can store a number of different artifacts for use by the chef-client. These include but are not limited to: roles, environments, data bags with data bag items stored inside, attributes, cookbooks, policies, clients, organizations, node attributes / OHAI data.

How the Chef server acts as an index of node data
 - The Chef server receives OHAI data, which contains all of the node data from the chef-client after it is run.

Chef solo vs Chef server
 - When using Chef solo, all of the artifacts are stored on the workstation in which you are working, whereas when you are using the Chef server, those artifacts are stored on the Chef server.

[Chef server's distributed architecture](https://docs.chef.io/install_server_ha.html)
 - If Chef server being a single point of failure being a huge risk for you, then you can run Chef server in high availability mode, which means it has redundancy. 

Scalability
 - If the chef-client is on the node being configured, then it scales more easily because a server doesn't have to do all the work. Scaling with High Availabililty (HA) is also an option.

## SEARCH
*Candidates should understand:*

What search is
 - `knife search` commands can be use to search the Chef server.

How to search for node information
 - `knife search node  "<index>:<search_query>"` may be invoked or you may search in the Chef server UI (nodes > attributes).

[What and how many search indexes Chef server maintains](https://docs.chef.io/chef_search.html)
 - Node data is indexed on the Chef server. That data may be accessed through a search query in any of the following ways:
   1) within the recipe
   2) by using the `knife search` command
   3) the search method in the Recipe DSL
   4) the search box in the Chef management console
   5) by using the `/search` or `/search/INDEX` endpoints in the Chef server API

What a databag is
 - A databag is a directory of data that is stored on the Chef server.

How to use search for dynamic orchestration
??? - 

How to invoke a search from the command line
 - `knife search node  "<index>:<search_query>"`

## CHEF CLIENT
*Candidates should understand:*

What the Chef client is
 - The Chef client is an agent installed on the nodes being configured. 

The function of Chef client vs the function of Chef server
 - While the client is installed on the nodes being configured, the server stores the necessary configuration information for the client to access.

What 'why-run' is
??? - like `terraform plan`

How to use '--local-mode'
 - `chef_zero` would be the provisioner, and you'd use local mode if you weren't using a proper Chef server. All of the data would be accessed from 

How the Chef client and the Chef server communicate
 - The node on which Chef client is installed has a client.rb file which contains a link and credentials to access the Chef server. The Chef server never accesses the Chef client; it merely holds the data that the client will need to access.

The Chef client configuration
 - The client is configures the node that it is on based on the run-list it is given during the bootstrap or knife commands.

## NODES
*Candidates should understand:*

What a node is
 - A node is the machine that is being configured by the Chef client.

What a node object is
 - A node object is data that is given to the Chef server to store by the Chef client after `chef-client` is run which contains OHAI data as well as attributes.

How a node object is stored on Chef server
 - A node object is data that is given to the Chef server to store by the Chef client after `chef-client` is run which contains OHAI data as well as attributes.
 
How to manage nodes
 - Nodes may be managed through the Chef server UI or through knife commands.

How to query nodes
 - Nodes may be searched through using the `knife search node` command. For example `knife search node "platform:ubuntu"`.

How to name nodes
 - Nodes are named either during a bootstrap or by changing the `client.rb` file on the node.

## RUN LIST
*Candidates should understand:*

What a run_list is
 - A run_list is added to a node either during a bootstrap or by changing the `client.rb` file on the node. It contains all of the cookbooks, recipes, and roles for the node.

What nested run_lists are
??? - A nested run_list simply means that 

Where a run_list is stored
 - A run_list is stored both on the Chef server and in the node's `client.rb`.

What does a run_list consist of
 - A run_list consists of all of the recipes and cookbooks to be run on the node as well as the roles assigned to it, which contain their own recipes and cookbooks.

How to look up run_lists
 - You may find a node's run_list in 3 ways:
  1) Look in the UI under Nodes > Details > Run List.
  2) Run `knife node show <nodename>`
???  3) In the node, look at the `client.rb` file.

How to set and change run_lists
 - You can set and change run_lists in much the same way that you can view them.
  1) Look in the UI under Nodes > Details > Run List > Edit.
  2) After you've insured that your desired cookbooks are uploaded to the Chef server, run `knife node run_list add <nodename> 'recipe[<cookbookname>::<recipe>]'` OR `knife node run_list add chefkata7 'role[security]'`
???  3) In the node, look at the `client.rb` file.

## ROLES
*Candidates should understand:*

What roles are
 - A role sets the patterns and processes for a node. Each node may have any amount of roles or none. A role can be thought of in two different ways: 
   1) the role of the machine
   2) the role of the team/person setting the run lists (not best practice)

How a role ensures code consistency across nodes
 - A role ensures code consistency by giving that node all of the same patterns and processes as well as attributes.

Where roles can be stored
 - Roles are stored in the chefrepo sibling to cookbooks on your workstation or on the node if you're running it in local mode. Otherwise, they're stored on the Chef server.

How roles are defined
 - Roles are defined in a `.json` or `.rb` file within a `roles` directory sibling to the `cookbooks` directory in a `chefrepo` which is uploaded to the Chef server. Or you may create a role within the UI of the Chef server: Policy > Roles > Create. 

What the components of a role are
 - The main components Roles consist of a run list and attributes (both default and override).

Roles vs recipes vs run_lists
 - A role, recipe, and run_list can each call various recipes in different ways. 
   1) A role will be assigned to a node and may or may not have its own run_list that will be included for that node.
   2) A recipe can use the `include_recipe` resource in order to run a recipe that is included in that cookbook's dependencies within the `metatdata.rb` file. 
   3) A run_list is assigned to a node using the `bootstrap` or `knife` command. It may contain both recipes and roles.

How to name roles
 - The name of the role is defined by the name of the `.json` or `.rb` file that you create in the `roles` directory as well as the metadata within that file. This may also be done within the UI of the Chef server. The requirements for the name are that it must be:
   1) unique to its organization
   2) made of letters (upper and/or lower-case), numbers, underscores, and/or hyphens (no spaces allowed). 

How to apply roles to nodes
 - Roles are assigned to nodes through the bootstrap, knife, or UI, and they are then applied when `chef-client` is run. 

## How to edit roles ENVIRONMENTS
*Candidates should understand:*

The purpose of environments
 - Environments are assigned to nodes to determine which phase of the release cycle that node represents. This include but are not limited to development, user acceptance testing, and production. 

How to use environments to manage cookbook release cycles
 - An environment is assigned to a node in order to give it the appropriate version of the cookbooks in the run_lists. The earlier in the release cycle that the environment assigned to the role is, the later versions of the recipes it will be assigned to.

How to use environments to constrain cookbooks
 - Environments are assigned to nodes, and then that node data is returned to the Chef server. You can then use that data within a recipe to constrain attributes to particular environments.

How to put nodes into an environment
 - Environments are assigned to nodes through:
  1) the UI under Nodes > Attributes > Edit.
  2) bootstrap and knife commands
  3) by editing the `client.rb` file
- You may also change a node's environment with a `knife exec` command.

## INFRASTRUCTURE AS CODE
*Candidates should understand:*

What the advantages are of defining infrastructure as code
 - If your infrastructure is code, then you know your the state of your infrastructure just by looking at the code. You can then easily alter your configuration and use version control to promote changes easily through development.

The reasons for defining infrastructure as code
 - Scalability
 - version control
 - dynamic infrastructure
 - simplicity and flexibility for managing and provisioning infrastructure

The difference between rolling back and rolling forward
 - Rolling back would be to undo the configuration changes made to your infrastructure. This is not best practice with Chef, as you're only allowed to roll forward due to the nature of idempotency. You would need to first change your recipe to intentionally undo the changes to the infrastructure that you wanted to "roll back". Then you'd redefine the desired state in your recipe and instill those changes by converging again.

## DESIRED STATE CONFIGURATION
*Candidates should understand:*

The imperative vs the declarative approach to configuration management
 - An imperative approach to configuration management would be something like running a shell or Powershell script that consecutively executes commands on a node. Chef, however, uses a declarative approach which would declare the desired state of configuration within the Chef server and perform that configuration management through the Chef client installed on the node.

The push vs the pull approach
 - A push approach would be if the node that was being configured was idle while the server pushed a policy of configuration desired state to the node. This would mean that there is nothing additional being installed on the node being configured, only what is in the policy. A pull approach is what Chef employs, which means that the Chef server is idle while the client that is installed on the node being configured pulls the necessary data from the Chef server that it needs to complete its configuration given to it through a bootstrapping process.

What Windows DSC is
 - Windows Desired State Configuration is Windows' configuration management tool based on Powershell which employs declarative programming.

What happens if you remove a resource from a recipe
 - If you remove a resource from a recipe that has already run on the node, then nothing will happen. Whatever that resource did the first time it ran will still be in effect unless you intentionally undo its effects.

## SUPERMARKET
*Candidates should	understand:*

The Supermarket value proposition
 - The Supermarket is a storehouse full of FREE cookbooks available to use and maintained by Chef. They are easily depended upon in your own cookbooks.

What you can store in Supermarket
 - The Supermarket can store cookbook and other tools such as InSpec profiles.

What a private Supermarket is
 - A company may have their own private supermarket for use internally. These must be maintained by them and can only be accessed by them.

When to use a private Supermarket
 - You would want to use a private Supermarket when you wanted to produce and promote your own cookbooks.

If Supermarket is a free or a premium feature
 - The Supermarket is FREE for anyone to use.

If the items in Supermarket are free or cost money
 - Everything within the Supermarket is FREE to use for anyone.

## CHEF DK
*Candidates	should	understand:*
The	Chef DK	value	proposition						
Specific	features	of	test-driven	development	(TDD)
Tools	packaged	in	Chef DK	

## TEST KITCHEN
*Candidates	should understand:*
The Test Kitchen value proposition						
What TDD is
 - Test Driven Development is....
The platforms supported by Test Kitchen
How to include Test Kitchen in a pipeline
Basic `kitchen` commands
Basic `kitchen` configuration

# DESCRIBING WHAT CHEF IS		
## PRODUCTS AND FEATURES
*Candidates should understand:*
[**The	Chef	Automate	value	proposition**](https://www.chef.io/automate/)
 - "Effective cross-team collaboration" Chef Automate tracks and tests dependencies between projects and teams. Deploy safely, even in a multi-team, multi-project environment where there are complex interdependencies.

**The Chef Automate features**
What the workflow feature is and how it affects productivity
What the compliance feature is and how it affects workflow
What the visibility feature is and how it affects workflow
How a private Supermarket fits into a workflow
The Chef Automate open source components
What Visibility is
What Habitat is
What InSpec is
What Chef Compliance is

## END-TO-END WORKFLOW
*Candidates	should	understand:*
How all Chef products, features and technologies fit together
The workflow scope
The compliance scope
The Chef Automate scope
How Chef Automate enhances DevOps behaviors
The aspects of Chef that are relevant to security and compliance teams
The aspects of Chef that are relevant to development teams
The aspects of Chef that are relevant to operations teams
The aspects of Chef that are relevant to change	advisory boards
How Chef enforces consistency across infrastructure

## DESIGN PHILOSOPHY
CHEF IS WRITTEN IN RUBY
*Candidates should understand:*
How	Chef	uses a	Ruby-based	DSL						
How	to	use raw	Ruby	to	extend	Chef	
What	a	library is
EXPLICIT	ACTIONS
Candidates	should	understand:
How	Chef	uses	explicit	actions	and	only does	what	you	tell	it	to						
Actions	for common	resources	such	as	the	:nothing	action
What	it	means	to	roll	back	infrastructure
What	happens	if	you	reverse	the	order	of	resources	in	a	recipe						
If	Chef	can	automagically	detect	what	patches	should	be	applied	to a system
PUSH	VS. PULL
Candidates	should	understand:
The	difference	between	push	and	pull	models						
The	benefits	of	a	pull	model	
When a push	model	is	appropriate						
What	firewall	rules	need	to	be	enabled for	Chef client	
The	Chef client	converge	intervals	and	how	to	invoke immediate	updates	
Basic	Chef	Fluency Page	5 v1.0.2
RECOMMENDED	WORKFLOWS
Candidates	should	understand:
What	wrapper	cookbooks are
How	to	use source	control,	e.g.	GitHub
How	to	use	the	TDD	approach
CHEF	WORKFLOW	BASICS		
CONTINUOUS	DELIVERY
Candidates	should	understand:
What	continuous	delivery (CD)	is
What	role	Chef	plays	in	CD						
When	to	run	tests	
Why	automated	configuration	management	is	critical	to	CD						
Why	CD	is	*more*	secure	than	manual	processes		
USING	COMPLIANCE	TO	SCAN
Candidates	should	understand:
The	benefits	of	the	agentless	nature	of	Chef	compliance						
How	to	check	for	compliance	on	nodes	that	don't	have	the	Chef	client	installed
Basic	use	cases	for	compliance	
What	language	is	used	to	express	compliance	requirements
USING	CHEF DK TO	TEST	YOUR	CHANGES
Candidates	should	understand:
The	Test	Kitchen	value	proposition		
Basic	use	cases	for	Chef DK	
PUBLISHING	ARTIFACTS	TO	CHEF	SERVER	AND SUPERMARKET
Candidates	should	understand:
How	to	publish	artifacts	to	Chef	server	
What	Berkshelf is						
If	the	Chef	Automate	workflow	feature	can	push	artifacts	to	things	other	than	a	Chef	server	or	
Supermarket						
How	to	manage cookbook	dependencies	
UNDERSTANDING	BASIC	CHEF	CODE		
APPROACHABLE	CUSTOM	CODE
Candidates	should	understand:
How	to	recognizing custom	code
How	to	use libraries
How	to	customize	Chef
APPROACHABLE	CHEF	CODE
Candidates	should	understand:
Basic	Chef	Fluency Page	6 v1.0.2
How	to	read	a	recipe	that	includes	the 'package',	'file', and 'service'	resources	and	describe	its	intent.	