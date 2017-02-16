# Study Sheet for Basic	Chef Fluency

## BASIC CHEF FLUENCY	BADGE	TOPICS

**The	Basic	Chef Fluency badge is	awarded	when someone proves	that they	understand the core	elements that	underpin Chef. Candidates	must show:**

• An understanding of basic Chef terminology.
• An understanding of Chef product offerings.
• An understanding of Chef's design philosophy.
• An understanding of Chef's approach to workflow and compliance.
• An understanding of basic Chef coding skills.

Here is a detailed breakdown of each area.

## CHEF	BASIC	TERMINOLOGY	RESOURCES
Candidates should	understand:

### Idempotency/convergence	- test &	repair model
When a node is converged, chef-client first tests to see if the node is in the same state as defined by the recipe. If it is not, then it repairs the node in accordance to the recipe.

## Common	resources	and	their	actions						
*Default actions*
The ':nothing' action
 - This simply means that this resource will do nothing to the node unless told otherwise.

The	'supports' directive

The	'not_if' and 'only_if' directives
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
Candidates should understand:
 - Cookbook contents
 - Naming conventions
 - Cookbook dependencies
 - The default recipe

## CHEF SERVER
*Candidates should understand:*
How the Chef server acts as an artifact repository
 - The Chef server can store a number of different artifacts for use by the chef-client. These include but are not limited to: roles, environments, data bags with data bag items stored inside, attributes, cookbooks, policies, clients, organizations, node attributes / OHAI data.

How the Chef server acts as an index of node data
 - The Chef server receives OHAI data, which contains all of the node data from the chef-client after it is run.

Chef solo vs Chef server
 - When using Chef solo, all of the artifacts are stored on the workstation in which you are working, whereas when you are using the Chef server, those artifacts are stored on the Chef server.

Chef server's distributed architecture
??? - 

Scalability
??? - If the chef-client is on the node being configured, then it scales more easily because a server doesn't have to do all the work.

## SEARCH
*Candidates should understand:*
What search is
 - `knife search` commands can be use to search the Chef server.

How to search for node information
 - `knife search node  "<index>:<search_query>"` may be invoked or you may search in the Chef server UI (nodes > attributes).

What and how many search indexes Chef server maintains
??? - 

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
??? - 

How to use '--local-mode'
 - `chef_zero` would be the provisioner, and you'd use local mode if you weren't using a proper Chef server. All of the data would be accessed from 

How the Chef client and the Chef server communicate
The Chef client configuration

## NODES
Candidates should understand:
What a node is
What a node object is
How a node object is stored on Chef server
How to manage nodes
How to query nodes
How to name nodes

## RUN LIST
Candidates	should	understand:
What	a	run_list is
What	nested	run_lists	are
Where	a run_list	is	stored						
What	does	a	run_list	consist	of						
How	to	look	up	run_lists						
How	to	set	and change run_lists
ROLES
Candidates	should	understand:
What	roles are						
How	a	role	ensures	code	consistency	across	nodes						
Where	roles	 can	be	stored
How	roles	are	defined						
What	the	components	of	a	role are
Roles	vs recipes	vs	run_lists						
How	to	name roles						
How	to	apply	roles	to	nodes	
How	to	edit	roles	ENVIRONMENTS
Candidates	should	understand:
Basic	Chef	Fluency Page	3 v1.0.2
The	purpose	of	environments	
How	to	use environments	to	manage	cookbook	release	cycles
How	to	use	environments	to	constrain	cookbooks	
How	to	put	nodes	into	an	environment		
INFRASTRUCTURE	AS	CODE
Candidates	should	understand:
What	the	advantages	are	of	defining	infrastructure	as	code		
The	reasons	for	defining	infrastructure as	code	
The	difference	between	rolling	back	and rolling	forward		
DESIRED	STATE	CONFIGURATION
Candidates	should	understand:
The	imperative	vs	the	declarative	approach	to	configuration	management	
The	push	vs	the	pull	approach	
What	Windows	DSC is						
What	happens	if	you	remove	a	resource	from	a	recipe	
SUPERMARKET
Candidates should	understand:
The	Supermarket	value	proposition						
What	you	can	store	in	Supermarket
What	a	private	Supermarket is
When	to	use	a	private	Supermarket		
If Supermarket	is	a	free	or	a	premium	feature	
If	the	items	in	Supermarket	are	free	or	cost	money
CHEF DK
Candidates	should	understand:
The	Chef DK	value	proposition						
Specific	features	of	test-driven	development	(TDD)
Tools	packaged	in	Chef DK	
TEST KITCHEN
Candidates	should	understand:
The	Test	Kitchen	value	proposition						
What	TDD is						
The	platforms	supported	by	Test	Kitchen
How	to	include	Test	Kitchen	in	a	pipeline						
Basic	`kitchen`	commands
Basic	`kitchen`	configuration
DESCRIBING	WHAT	CHEF	IS		
PRODUCTS	AND	FEATURES
Candidates	should	understand:
The	Chef	Automate	value	proposition						
Basic	Chef	Fluency Page	4 v1.0.2
The	Chef	Automate	features	
What	the	workflow feature is and	how	it	affects productivity						
What	the compliance feature	is and	how	it	affects	workflow						
What	the	visibility feature	is and	how	it	affects	workflow						
How	a private	Supermarket	fits into	a workflow	
The	Chef	Automate	open	source	components		
What	Visibility	is		
What	Habitat is						
What InSpec is
What	Chef	Compliance	is	
END-TO-END	WORKFLOW
Candidates	should	understand:
How	all	Chef	products,	features	and	technologies	fit	together						
The	workflow	scope
The	compliance	scope	
The	Chef	Automate	scope	
How	Chef	Automate	enhances DevOps	behaviors						
The	aspects	of	Chef	that	are	relevant	to	security	and compliance	teams						
The	aspects	of	Chef	that	are	relevant	to	development	teams						
The	aspects	of	Chef	that	are	relevant	to	operations	teams						
The	aspects	of	Chef	that	are	relevant	to	change	advisory	boards						
How	Chef	enforces consistency	across	infrastructure		
DESIGN	PHILOSOPHY		
CHEF	IS	WRITTEN	IN	RUBY
Candidates	should	understand:
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