# Ansible
- In IT Industry there are a lot of repititive tasks you have to go through like creating new hosts or virtual machines or their sizing, applying configuration on them, all these tasks require hundreds of commands on hundreds of different servers.
- This is where Ansible helps, Ansible is powerful IT automation tool.
- All ansible plabooks are written in YAML language.
- Imagine, there are a number of machines in your environment that you would like to restart in a particular order. Some of them are web servers and some are database servers.
- You want that first web-servers are shutdown then database servers, then power-up database server and then web-servers.
- You can write ansible playbook to get this task done in a matter of minutes and simply invoke ansible playbook to repeat the same task.
- Let’s take another example, In this case, we are setting up a complex infrastructure that spans across public and private clouds
and that includes hundreds of VMs.
- With Ansible, you could provision VMs on public clouds like Amazon and as well as private cloud environments like VMware, and move on to configuring applications on those and setting up communication between them, such as modifying configuration files, installing applications on them, configuring firewall rules,   et cetera.
- There are a lot of built-in modules available in Ansible that supports these kind of operations.




### Ansible Configuration Files:
- When you install Ansible, just like any other software, it creates a default configuration file at the location, /etc/ansible/ansible.cfg
- An Ansible configuration file is divided into several sections:

#### [defaults]

#### [inventory]

#### [privilege_escalation]

#### [paramiko_connection]

#### [ssh_connection]

#### [persistant_connection]

#### [colors]


- The default section at the top, and others like inventory, privileges_escalation, ssh_connection, and colors to name a few.
- Within each of these sections, you have a number of options and their values, most of them being in the default section.
- For example,
### The default location of the Ansible inventory file:
[default paths]
#### inventory = /etc /ansible /hosts
#### roles_path = /etc /ansible /roles
#### log_path = /var /log /ansible.log
#### library = /usr /share /my_modules
#### action_plugins = /usr /share /ansible /plugins /action
#### gathering = implicit
#ssh timeout
timeout = 10
forks = 5
[inventory]
enable_plugins = host_list, virtualbox, yaml, constructed

Now, we know the default location of Ansible's configuration file is at /etc/ansible/ansible.cfg path, and those are values Ansible will consider when you run the playbooks from anywhere on the controlled machine. 
Say, I have multiple different playbooks in different locations on my host, like one for web, another for database, and another for networking, and say I require different settings in each of these playbooks.
For example, when I run the playbooks for web, I do not want facts to be gathered; when I run the playbooks for database, I do want facts to be gathered, but I do not want to display colored output for some reason. When I run the network playbooks, I want to extend the SSH timeout to 20 seconds instead of the default 10.

## What is SSH timeout?
SSH timeouts are the maximum amount of time that an SSH session can remain idle or inactive before it is closed by either the client or the server which is 10 second by default in Ansible.

I just picked one example for each for now, but you might have a requirement to change many such values based on your use case.
In that case, you make a copy of the default configuration file into each of these directories and make the necessary changes in them.

Next time you run Ansible Playbook, it picks values from the config file within those directories.

### What if you plan to store one of your configuration files in a directory other than your Playbooks directory? 
For example, you want to store the web configuration to a file at location opt/ansible-web.cfg and use that for multiple Ansible playbooks going forward. 
In such cases, before running the Ansible playbook, you can specify the location to this configuration file through an environment variable, ansible_config and set it to the path to the new config file. 
This time, when the playbook is run, Ansible picks up that file instead of the default configuration file.
Remember, these files don't have to have all the values. You only need to override the parameter you want to override.

#### Example:
Say for instance, I developed a set of new playbooks for my storage environment. I have decided to go with the Ansible configuration parameters defined as it is in the default configuration file so I don't really have to make a copy of it into my playbooks directory. 
When I execute Ansible from my storage directory, it first looks if there is an ansible.cfg file available there in present working directory. If not, it's going to use the one in the default location. 
However, I realized that there is this one parameter that I need to change, just one for gathering facts. 
I want to turn it off. So I don't really want to copy the entire ansible.cfg file to make that one change. What I could do is overwrite that single parameter using an environment variable. 
You can set an environment variable right before executing the Ansible playbook to change that behavior.

#### To list all ansible configuration files:

ansible-config list

You can have multiple different configuration files on your system. For example, one in the present directory and another in the user's home directory, and another in the /etc/ansible directory. 

#### To list all ansible configurations that are currently active:

ansible-config view 

#### Below command will show you a comprehensive list of current settings Ansible has picked up and where it's picked that from.

ansible-config dump

For an example:
$ export ANSIBLE_GATHERING=explicit
ansible-config dump | grep GATHERING
DEFAULT_GATHERING(env: ANSIBLE_GATHERING) =explicit

## What do you mean by AdHoc command?
### A single line commands for which you do not need to write the YAML files is called AdHoc command.


## Introduction to YAML
- Ansible Playbooks are text files or configuration files that are written in a particular format called YAML.
- A YAML file is used to represent data, in this case, configuration data. Here is a quick cosmparison of a sample data.

### Key:Value Pair
- You already know how key:value pair is defined. Like below:

Fruit: Apple

Vegetable: Carrot

Liquid: Water

Meat: Chicken

In above example, keys are Fruit, Vegetable, Liquid and Meat and values are Apple, Carrot, Water and Chicken.

Remember, you must have a space after “:” because it differentiates value with the key.

### Arry/List:
Fruits:
-	Orange
-	Apple
-	Banana

Vegetables:
-	Carrot
-	Cauliflower
-	Tomato

### Dictionary/Map:
Banana:
	
    Calories: 105
	Fat: 0.4 g
	Carbs: 27 g

Grapes:
	
    Calories: 62
	Fat: 0.3 g
	Carbs: 16 g
You must have equal number of blank spaces before the properties of a single item so they are all aligned together.
Notice the number of spaces before each property that indicates these key value pairs fall within banana. But what if we had extra spaces for fat and carbs? Then they will fall under calories and thus become properties of calories, which doesn't make any sense.

Fruits:
	
    -	Banana:
			Calories: 105
			Fat: 0.4 g
			Carbs: 27 g

	-	Grapes:
			Calories: 62
			Fat: 0.3 g
			Carbs: 16 g

So, when to use a dictionary or a list.

First of all, it is important to understand that all of what we discussed so far, such as XML, JSON or YAML are used to represent data. It could be data about an organization and all of its employees and their personal details, or it could be data about a school and all of its students and their grades. Or it could be data about an automobile manufacturing company and all of its cars and its details. It could be anything.

Let's take an example of a car. A car is a single object and it has properties such as color, model, transmission, and price. So, to store different information or properties of a single object, we use a dictionary.


Color: Grey

Model: Baleno

Tranmission: Manual

Price: $1,00,000

#### If you want to add more data in Model:

Color: Grey

Model: 

	Name: Baleno
	Year: 1995

Tranmission: Manual

Price: $1,00,000

In the above case, the single value of model is now replaced by a small dictionary with two properties, name and year. So this is dictionary within another dictionary.

Let's say we would like to store the name of six cars. The names are from by the color and the model of the car. 
To store this, we would use a list or an array as it is multiple items of the same type of object. Since we are only storing the names, we will have a simple list of strings as below:

- Blue Baleno
- Grey Baleno
- Red Baleno
- Green Baleno
- Blue Baleno
- Black Baleno


What if we would like to store all information about each car, everything that we listed before such as the color, model, transmission and price. 
- We will then modify the array from a list of strings to a list of dictionaries. So we expand each item in the array and replace the name with the dictionary we built earlier. 
- This way, we are able to represent all information about multiple cars in a single YAML file using a list of dictionaries.

- Colour: Blue

  Model:

	Name: Baleno

	Model: 1995

 Transmission: Manual

 Price: $1,00,000

- Colour: Grey

  Model:

	Name: Baleno

	Model: 1995

 Transmission: Manual

 Price: $1,00,000

- Colour: Red

  Model:

	Name: Baleno

	Model: 1995

 Transmission: Manual

 Price: $1,00,000



- Colour: Green

  Model:

	Name: Baleno

	Model: 1995

 Transmission: Manual

 Price: $1,00,000

- Colour: Blue

  Model:

	Name: Baleno

	Model: 1995

 Transmission: Manual

 Price: $1,00,000- Colour: Green

- Colour: Black

   Model:

	Name: Baleno

	Model: 1995

 Transmission: Manual

 Price: $1,00,000


SO,
### - Dictionary is an unordered collection
### - Whereas lists are ordered collection.



Dictionary 1:

Banana:

	Calories: 105

	Fat: 0.4 g

	Carbs: 27g


Dictionary 2:

Banana:

	Calories: 105

	Carbs: 27g

	Fat: 0.4 g

#### Dictionary 1 & Dictionary 2 both are same even though the order of keys in them is not same.

However, this is not same for list and arrays:

Array/list 1:

Fruits:
-	Orange
-	Apple
- 	Banana

Array/list 2:

Fruits:
-	Orange
-	Banana
-	Apple

#### In above example, Array/list 1 and Array/list 2 are not the same because Apple and Banana are at different positions.

Just like in python and shell scripting, any line beginning
with a hash(#) is automatically ignored and considered as a comment.


## 2.10 Coding Exercise


#### Inventory
Ansible can work with one or multiple systems in your infrastructure at the same time. 
In order to work with multiple servers Ansible needs to establish connectivity to those servers. 
This is done using SSH for Linux and PowerShell Remoting for Windows. 
That's what makes Ansible Agentless. Agentless means that you don't need to install any additional software on the target machines to be able to work with Ansible.

### What is a target machine?
One is a machine on which we have installed Ansible and there are other hundreds or thousands of machines on which we have to get the tasks done using a single playbook and a single machine. So, hundreds and thousands of machines on which you are getting the work done are target machines.

One of the major disadvantages of most other orchestration tools is that you are required to configure an agent on the target systems before you can invoke any kind of automation.

Now, information about these target systems is stored in an inventory file. If you don't create a new inventory file Ansible uses the default inventory file located at etc/ansible/hosts location.

----------------------------------

### What is an inventory file?
#### Inventory files are the INI format files in which information about the target machines are stored.

The inventory file is in an INI like format. It's simply a number of servers listed, one after the other. 
You can also group different servers together by defining it like this under the name of the group within square brackets, and define the list of servers part of that group in the lines below.
server1.company.com
server2.company.com

[mail]
server3.company.com
server4.company.com

[db]
server5.company.com
server6.company.com

[web]
server7.company.com
server8.company.com

I have a list of servers named from one to four. However, I would like to refer to these servers in Ansible using an alias such as web server or database server. 
I could do this by adding an alias for each server at the beginning of the line and assigning the address of that server to Ansible underscore host parameter.
E.g.,
web	ansible_host=server1.company.com
db ansible_host=server2.company.com
mail ansible_host=server3.company.com
web2 ansible_host=server4.company.com


## 3.12 Coding Exercise


Inventory Formats
Let's take the example of two companies. The first is a small startup with a few service handling basic functions like web hosting and database management. 
The second is a multinational corporation with hundreds of servers spread across the globe, handling diverse functions like e-commerce, customer support, data analysis, and more. 
For the small startup, a simple INI format inventory would suffice.
It's like a basic organizational chart with only a few departments. However, for the multinational corporation, a more detailed and structured inventory would be needed. 
This is where the YAML format comes in.
Grouping and parent child relationship
With Ansible's grouping, we can create the common labels or groups to efficiently manage and operate on a set of servers at one.
Now, when you need to update the web servers, you can simply target the web servers group and Ansible will apply the changes to all servers in that group.





In the INI format, groups are defined using square brackets and hosts are listed under their respective groups.





## Ansible Variables
Just like any other scripting or programming language, variables are used to store data in key:value pair.
For example, let's say we are trying to perform the same operation of applying patches to hundreds of servers, we only need a single playbook for all hundred servers. 
However, it's the variables that store information about the different host names, usernames or passwords that are different for each server.

In playbook, inventory are defined in double curley braces.
e.g,
‘{{ http_port }}’

This concept is known as Jinja2 Templating.

## Variable Types
There are three types of variables:
1. String Variables
2. Number Variables
3. Boolean Variables

### String Variables:
------------------------------
String variables in Ansible are sequences of characters. 
They can be defined in a playbook, inventory or passed as command line arguments.


### Number Variables:
-------------------------------
Number variables in Ansible can hold integer or floating-point values. They can be used in mathematical operations. In the example shown, max_connections is a number variable with the value 100.

### Boolean Variables:
--------------------------------
The Boolean variables in Ansible can hold either truthy or falsy values. They're often used in conditional statements.

Variable precedence
Example:
web1 ansible_host=172.20.1.100
web2 ansible_host=172.20.1.101
web3 ansible_host=172.20.1.102

[web_servers]
web1
web2
web3

[web_servers:vars]
dns_server=10.5.5.3

In the above example, ansible_host are the Host variables and dns_server are the group variables.

If there are multiples variables defined such as one is group variable and second is host variable then variable defined at the host level takes precedence over the variable defined at the group level, and that's variable precedence.


### Order of variables which will be defined first:
- Role Defaults
- Group variables
- Host variables
- Host Facts
- Play variables
- Role Variables
- Include Variables
- Set Facts

Variable Scoping
Consider the following example:

web1 ansible_host=172.20.1.100
web2 ansible_host=172.20.1.101 dns_server=10.5.5.4
web3 ansible_host=172.20.1.102

we have a DNS server specified for host web two only. 
Is that value can be used by the other hosts like web one and web three? 
No, because the scope of that variable is within the host web two. 
A scope defines the accessibility or the visibility of a variable. It is based on the fact that where a veriable can be modified with in the code.
There are two types of variale scopes
Global scope:
Varaiables that are defined at the top of the code and outside the functions are known as Global Scope.


### Local Scope:
-------------------------------
When variables are defined within the function are known as local variables.

### Magic Variables
--------------------------------
Ansible provides a set of magic variables that allow you to access specific information about the remote systems and the Ansible environment. These variables are automatically set by Ansible and can be used in playbooks, templates, and scripts.

### Ansible Facts
--------------------------------
When you run a playbook and when Ansible connects to a target machine, it first collects information about the machine, such as its basic system information, like the system architecture, version of operating system, processor details, memory details, serial numbers, et cetera. 
It collects information about the host's network connectivity, the different interfaces, IP addresses, FQDN, MAC address, et cetera, as well as device information like the different disks,volumes, mounts and the amount of space available on them,the date and time on those systems and other settings as well.
These information are known as facts in Ansible.

Ansible gathers all these facts using the setup module. 

The setup module is run automatically by Ansible to gather facts about the hosts when you run a playbook, even if you didn't use this module in your playbook.

	
Now, what if you don't want Ansible to do that? Say for example, your playbook does not rely on any information gathered from facts, so you would like to disable gathering facts. 
You could do that by adding an option gather_facts to no on the play like this:
#### gather_facts: no
It is by default set to implicit, meaning Ansible will automatically gather facts whether you specify it or not. 
Explicit is the opposite, where Ansible does not gather facts by default,
What if you have it specified in both places, in the playbook as well as in the configuration file? The setting in the playbook always takes precedence.

## 4.21 Coding Exercise



### Playbooks
-----------------------------------
- Ansible Playbooks are Ansible's orchestration language. It is in Playbooks where we define what we want Ansible to do. 
- It is basically a set of instructions you provide to Ansible to work its magic. 
- For example, it can be as simple as running a series of commands on different servers in a sequence, and restarting those servers in a particular order.

Or it could be as complex as deploying hundreds of VMs in a public and private cloud infrastructure, provisioning storage to VMs, setting up their network and cluster configurations, configuring applications on them such as web server or a database server, setting up load balancing, setting up monitoring components, installing and configuring backup clients and updating configuration database with information about the new VMs, et cetera.

- Let's take a closer look at how Playbooks are written.
- Remember, all Playbooks are written in YAML format, which is why we spent some time earlier getting our hands dirty with YAML.
---------------------------- 
#### - A Playbook is a single YAML file containing a set of plays.
#### - A play defines a set of tasks to be run on a single or a group of hosts.
#### - A task is a single action to be performed on a host.
------------------------------------
### - Some examples of a tasks are :

- Executing a command
- Executing script on the host, installing a package on the host, or performing a shutdown or a restart operation.
- Below is an actual playbook:


A playbook may contain a single play named play 1. 
The goal of this play is to run a set of activities one after the other on the local host as written over there:
hosts: localhost
Remember that the host on which you wanna run these actions, is defined at the play level. 
In that case, we just want to test on the local host, which is why it is set to local host. This could be anything from your inventory file. 
Next, we run a set of commands one after the other on the host.

### - First, we print the date 
- name: Execute command ‘date’
  
  command: date

### - Then we run a script 
- name: Execute script on server
  
  script: test_script.sh

### - Installing the httpd_package
- name: Installing httpd service
  
  yum:
  
      name: httpd
      state: present
      
###
- Finally starting the web server:

- name: Start web server

  service:

      name: httpd
      state: started

Remember, the host you want to perform these operations against is always set at a play level. 
Currently, this is set to local host, which means that all these actions listed under tasks, is going to be performed on the local host.




- The listed tasks will be executed on all the hosts listed under that group simultaneously.

- If you want that your playbook named faraz.yaml, take the reference of the server details from the inventory you should write below command:
ansible-playbook -i inventory faraz.yaml

## 5.30 Coding Exercise

### Modules
- The different actions run by tasks are called modules.
- In above example, command, script, yum and service, are Ansible modules.
- There are hundreds of such modules available in Ansible.
- Information about these modules is available in the Ansible documentation website, or you could simply run the command:

####  ansible-doc -l 

So, finally when you are done with bulding the ansible playbook, how do you run it?
Suppose, the name of the playbook is faraz.yml. So, the command that you will run is:

### ansible-playbook faraz.yml


#### Verifying the playbook
- Suppose, you're tasked with deploying a critical software update across hundreds of servers in your organization.
- You write an Ansible playbook to automate this task and confident in your work you decide to run it immediately in your production environment.
- However, due to an unnoticed error in the playbook, instead of updating the software, it unintentionally shuts down the service on all servers.
- Eventually, you have to take a significant downtime and a frantic scramble to restore the service.
- This scenario underscores the importance of a critical step in the playbook development process, which is verification.
## - There are three modes in verification of Ansible playbook:
	1. Check Mode
	2. Diff Mode [Chaddi]
	3. Syntex-check Mode
- Verifying a playbook in the production environment allows you to catch and rectify any errors or unexpected behaviors in a controlled environment.
- Let’s see how to verify the playbooks in Ansible:
- For that you you run the playbook in the check mode.
### - Use the --check option
- Let's say you have a simple Ansible playbook saved as install_nginx.yml that installs the nginx web server on a host.
- To run this playbook in check mode you would use the --check option:
####  install_nginx.yml --check
- In check mode, Ansible will not actually install nginx on the hosts. Instead, it will tell you what it would do if it were to run the playbook for real.
- An important point to be emphasised here is that not all Ansible modules support check mode.
- If a task uses a module that doesn't support check mode, the task will be skipped when you run the playbook.
### - Another mode for verifying playbooks is diff mode.
- Diff mode when used with check mode shows the differences between the current state and the state after the playbook is run.
- It provides a before and after comparison, which can be useful for understanding what changes a playbook will make. To run a playbook in diff mode, use the --diff option.
#### install_nginx.yml --diff 

### To run the check and diff command together:
#### install_nginx.yml --check --diff


- In this above output you can see the exact change that would be made to the ETC nginx nginx.config file. 
- The line with the plus (+++) symbol shows what would be added to the file.

## Syntex Check Mode
- Ansible also provides syntex check mode.
- This mode checks the syntax of your playbook for any errors. 
- It's a quick way to catch syntax errors that could cause your playbook to fail.
install_nginx.yml --syntex-check
- If there is an error in the playbook, you will receive an error message indicating exactly where the problem is making it easier to fix the issue.


## Ansible Lint
- Picture yourself as a DevOps engineer in a bustling R&D company named einfochips. 
- You have been using Ansible to automate your infrastructure and over time, you have written a multitude of playbooks. 
- As your infrastructure expands, so does the complexity of your playbooks. 
- Some playbooks start to become more difficult to understand and maintain than others. 
- You realize that you need a mechanism to ensure consistency and quality across all your playbooks. 
- This is where Ansible Lint enters the scene.
- The command for Ansible Lint is:
#### ansible-lint faraz.yml
Ansible Lint catches all the style-related issues and provides us with warnings. 
It tells us about:
- Incorrect indentation.
- Deprecated apt and services modules
- The missing name attribute for one task
You can use this feedback to refine your playbook.

## 5.25 Coding Exercise

### Conditionals

- Suppose, I have two playbooks that does the same thing, install NGINX on a host. 
- But as you know, different OS flavors use different package managers. 
- Debian uses APT and Red Hat uses Yum but these are two separate playbooks and you will have to use the right playbook for the respective servers. 
- But, I want to create a single playbook that works for both of these operating systems for all hosts.
- Based on the operating system flavor my playbook must run the appropriate task and that's where a conditional statement comes in handy.



I could use the when conditional statement to specify a condition for each task, only if the condition is true, that task can run. 
## So what is a condition?
It could be any check that we perform, such as checking in if the OS family is Debian or Red Hat.
ansible_os_family is a built in variable that Ansible populates with the flavor of operating system. 
Make sure to use double equal to sign ‘==’ when checking equality, in conditional statement. 
You may use an or operator to specify either of two conditions. Use the yum module to install package if the OS family is Red Hat or Suse or an and operator.

You may use conditionals in a loop as well. 
Say for example, instead of a single package, we have a list of packages that needs to be installed.
We have an array named packages that has the list. 
Each item in the list has the name of the package to be installed as well as a property called required. 
Install the packages only if the required property is set to true. 
First, we specify the loop directive to execute the installed task in a loop. 
The name of the package to be installed is now item.name and this will install all three packages specified. 
To visualize this better, if you kind of expand the loop you'll see that the loop is in fact three different tasks, one for each package. 
Each task has the variable on it called item and the item has the respective package details. 
So if I wanted to write a conditional for each of the item, I would say 
	when:	item.required == True

Conditionals based on facts, variables, re-use
Let us imagine your company has a mix of different servers. 
Some are running Ubuntu 18.04, some are running CentOS 7, and others are running Windows Server 2019. 
Your task is to automate the deployment of a web application across all these servers. 
This means the playbook needs to perform a different set of actions for different servers. 
So in scenario 1, you need to install a specific version of NGINX web server, but only on the servers running Ubuntu 18.04. 
### But, How do you determine which version of OS is running on a server? 
Our inventory only has basic information about the server that we know of. 
This is where Ansible's facts come into play. Ansible facts are system-specific variables that can be used in playbooks. 
They collect information about the servers during the execution of the playbook. 
You can use the ansible_facts os_family and ansible_facts distribution_major_version to determine the operating system of the target servers and install the specific version of NGINX only on the servers running Ubuntu 18.04.



## 5.28 Coding Exercise


### Loops
- Let's look at this example where we are creating an Ansible playbook. 
- To create users in a system using the user module. 
- The user module helps you to create users on the target systems. 
- In this case we are just creating one user. 
- But what if we have multiple users? What if we have a lot of users to create? 
- Well, one way to do this would be to duplicate these lines as many times as required like in below image. But that is not the best practice.

- A better way to do this would be to have a single task loop over all the users. That's where we use loops. 
- Loop is a looping directive that executes the same task multiple number of times like shown in below image.

- But what if I want to specify the user id as well.
- That would mean each item in the loop would have to have two values; the user name and user id.

“With” is related to loops. When you have multiple values in Array/list and all of them are having same configuration you can use loop with with to make the YAML less complex.

## 5.30 Coding Exercise

### Modules
---------------------------
- The different actions run by tasks are called modules.
- command, script, yum and service are Ansible modules.
- Modules (also referred to as “task plugins” or “library plugins”) are discrete units of code that can be used from the command line or in a playbook task.

### - There are many different types of modules:
1. System [Psycho fida cloud pe]
2. Commands
3. Files
4. Database
5. Cloud
6. Windows



###  System Modules:
--------------------------------
System modules are actions to be performed, at a system level such as:
- Modifying the users and
- Modifying the groups
- Modifying IP tables
- Modifying the firewall configurations on the system
- Working with logical volume groups
- Mounting operations
- Working with services

### Command Modules:
-----------------------------------
- Command modules are used to execute commands or scripts on a host. This could be:
- Simple commands using the command module
- An interactive execution using the expect module, by responding to prompts.
- You could also run a script on the host, using the script module.

### Files Modules:
---------------------------------
- File modules help work with files. 
- For example, use the ACL module, to set and retrieve ACL information on files. 
- Use the archive and un-archive modules to compress and unpack files. 
- Use find, lineinfile and replace modules, to modify the contents of an existing file.

### Database Modules:
--------------------------------
- Database modules help in working with databases, such as MongoDB, MySQL, MSSQL or PostgreSQL, to add or remove databases or modify database configurations.

### Cloud Modules:
-----------------------------
- The cloud section has a vast collection of modules, for various different cloud providers, like Amazon, Azure, Docker, Google, Openstack and VMware being just a few of them. 
- There are a number of modules available for each of these, that allow you to perform various tasks, such as creating and destroying instances, performing configuration changes in networking and security, managing containers, data centers, clusters and so on.

### Windows Module:
------------------------------
- Windows modules helps to use Ansible in a Windows environment. 
- Some of these are wincopy to copy files, wincommand to execute a command on a Windows machine and there are a bunch of other modules available, to work with files on Windows.
- Create an IIS website, install a software using MSI installer. Make changes to registry using reg edit and managing services on the server.

These are just a few modules in few categories. There are a lot more and a comprehensive list can be found docs.ansible.com, along with detailed instructions on each of them.

## Command module in detail:
- Let's look at few of these modules in detail, to understand how you can use them and how to read the documentation page. 
- We will start with the command module. 
- The command module is used to execute a command, on a remote node.

## Script Module in Details:
- The script module executes a script, which is located locally on the Ansible controller machine, and on one or more remote nodes, after transferring it over. 
- To run a script on one or hundreds of servers, you really don't have to copy it over to all the servers. Ansible takes care of automatically copying the script.



## Service Modules in Details:
- The service module is used to maintain services, on a system such as starting, stopping or restarting a service. 
- This Ansible playbook is used to start, various services in a particular order. 
- First we start the database service, using the service module. 
Then the service module.


- In the above image, you can see that state is started not start even though in linux terminal to start the service, we write: systemctl start nginx.
- That essentially means if httpd service, is not already started, start it. If httpd service is already started, then don't do anything. - - This is called idempotency.

### lineinfile
- The lineinfile module is used to find a line in a file and replace it or add it if it doesn't already exist.
- For example, we're given a task to add a new DNS server, into the etc resolv.conf file.
- This simple Ansible playbook using the lineinfile task, adds the new name server information, into the etc resolv.conf file.
- Remember, the lineinfile module is idempotent.


### Ansible Plugins

- Suppose, you have a complex infrastructure with multiple VPCs, virtual machines, and load balancers spread across different regions.
- Your goal is to automate the provisioning and configuration of these resources using Ansible.
- While Ansible provides a rich set of built-in modules and features, you soon realize that you need additional functionality to perfome such hevay tasks.
- Another requirement could be creating AWS EC2 instances with specific AMI versions instance, types, and security groups tailored to your application's unique requirements.
- You may also need a solution that allows you to dynamically configure load balancing rules, SSL certificates, and health checks based on the specific needs of your applications.
- This advanced load balancer management capability enhances the performance and reliability of your cloud-based services.
- To address these challenges, you can use Ansible plugins.
- Ansible plugins provide extensibility and customization options beyond the core Ansible features.
- In Ansible, a plugin is a discrete piece of code that extends or modifies the functionality of Ansible. 
- Plugins can be used to enhance various aspects of Ansible such as inventory, modules, callbacks, filters, and more. 
- An Ansible plugin can be in the form of an inventory, plugin, module plugin, action plugin, callback plugin, or any other plugin type.
- Each plugin type serves a specific purpose and offers unique capabilities for extending Ansible's functionality.
- Now let us see how Ansible plugins overcome the challenges mentioned previously:
- By creating a custom inventory plugin, you can fetch real-time information about your cloud resources directly from your cloud provider's API.
- This Dynamic inventory plugin enables you to have an up-to-date view of your infrastructure, ensuring accurate and reliable automation.
- Next is the Module plugin. When you develop a custom module plugin, you gain the power to provision cloud resources with custom configurations.
- This plugin integrates seamlessly with your cloud provider's API, allowing you to create instances with specific AMI versions, instance, types and security groups tailored to your applications requirements.
- Lastly, we have the Action plugin. This plugin simplifies load balancer management. With an action plugin, you define high level tasks in your playbooks, making it easy to configure load balancing rules, SSL certificates, and health checks.
- The action plugin handles the underlying API calls and ensures consistent and reliable load balancer management across your hybrid cloud environment.
- Lookup plugins are handy tools that fetch data from external sources, like databases or APIs, and let you use that data within your playbooks.
- Filter plugins offer additional data manipulation and transformation capabilities within your playbooks, allowing you to modify variables or format output.
- Connection plugins enable Ansible to connect and communicate with various target systems, such as SSH, WinRM, or Docker.
Plugins offer options and extensions for the core features of Ansible: transforming data, logging output, connecting to inventory, and more. 
Modules are a type of plugin that execute automation tasks on a 'target'.

## Modules and plugins: what is the difference?
If you are looking to add functionality to Ansible, you might wonder whether you need a module or a plugin. Here is a quick overview to help you understand what you need:
   
    • Plugins extend Ansible’s core functionality. Most plugin types execute on the control node within the /usr/bin/ansible process. Plugins offer options and extensions for the core features of Ansible: transforming data, logging output, connecting to inventory, and more.
   
    • Modules are a type of plugin that execute automation tasks on a ‘target’ (usually a remote system). Modules work as standalone scripts that Ansible executes in their own process outside of the control node. Modules interface with Ansible mostly with JSON, accepting arguments and returning information by printing a JSON string to stdout before exiting. Unlike the other plugins (which must be written in Python), modules can be written in any language; although Ansible provides modules in Python and Powershell only.


## Modules and Plugins Index
- Let's consider an example scenario where you need to manage the configuration of network devices in your infrastructure using Ansible. 
- You want to find a module or plugins that supports the specific type of network device you have, such as Cisco routers.
- By using the modules and plugins index, you can search for modules or plugins related to network device management.
#### - You can filter the results based on the category or keyword, such as Network or Cisco.
#### - The index will provide you with a list of modules and plugins that match your criteria along with detailed documentation explaining how to use them effectively.
- The modules and plugins index serves as a centralized and organized resource that provides detailed information about the available modules and plugins in Ansible.
- Let us now go over the key features and benefits of the modules and plugins index.
- First off, you've got search and filtering. It's like having your own personal assistant dedicated to helping you find the perfect tools for the job.
- Whether you need to search by keywords, categories, or specific criteria, the index has got you covered.
- Each module and plugins in the index comes with its own user manual. You'll know exactly what each tool does, how to use it, and even get some examples to follow. It's like having a tutor by your side guiding you through your automation projects, and here's the best part.
- With version compatibility, you no longer have to worry about trying a tool only to discover it doesn't work with your version of Ansible.
- The modules and plugins index have you covered. It provides clear information on which versions each module or plugin supports, ensuring you have a seamless experience without any compatibility issues, but what truly sets the modules and plugins index apart is the power of community contributions.
It's not just a one-man show. It includes contributions from the whole Ansible community. This means you get to benefit from the collective wisdom and experience of all those smart folks out there.
## 6.34 Coding Exercise
### Introduction to handler
----------------------------
- Suppose, you are managing a web server infrastructure with multiple servers.
- You frequently make changes to the web server's configuration file to adjust various settings. However, modifying the configuration file alone does not apply the changes immediately.
- To ensure that changes take effect, you need to restart the web server service.
- In this scenario, you would have to manually execute a task to restart the web server service after every configuration update.
- This manual intervention becomes time consuming and prone to errors as the infrastructure grows.
- This is where Ansible handlers comes in. With handlers, you can define an action to restart the web server service and associate it with the task that modifies the configuration file.
- Now, whenever the configuration file is modified during the Playbook run, the associated handler is triggered, ensuring that the web server service is automatically restarted.
- By using handlers, you eliminate the need for manual intervention, and ensure that the web server service is always restarted when necessary.
- Handlers in Ansible are special tasks associated with specific events or notifications. They're typically defined in a playbook, and are executed only when triggered by a task notifying them.
- Below is an example playbook that demonstrates the use of handlers. 
- In the example shown, the task Copy Application Code uses the Copy module to deploy the application code to the target servers.
- It includes the notify directive, which triggers the associated handler named Restart Application Service when this task completes.
- The handler restarts the application service uses the service module to restart the application service by specifying the service name and the desired state as restarted. 


## Ansible Roles
- In this lecture, we discuss about roles in Ansible.
- Just like how you would assign roles to different people in the real world, making them doctors, engineers, astronauts, policemen, or chef, In the Ansible world, you would assign roles to blank servers to make them a database server or a web server or a Redis messaging server or a backup server.
- Assigning a role in the real world means doing everything one needs to do to make someone a doctor or an engineer like for example:
1. Sending the person to a medical school
2. Earning a medical degree
3. Completing a residency program and finally
4. Obtaining a license to practice.
- In the automation world assigning a role means doing everything you need to do to make a server a database server such as
- Installing the prerequisites required for MySQL
- Installing MySQL packages themselves
- Configuring the MySQL service 
- Configuring database with our users
- By now, we know how to get these tasks done using Ansible playbooks. We have done these a lot. Here's a simple playbook that can do this. But then, if you can do this in a playbook why do you need roles?
- This set of tasks, to install and perform basic configurations on a MySQL database is going to remain mostly common.
- Once a person develops this Ansible playbook it can be shared with hundreds of thousands of others trying to do the same thing, install MySQL.
- Instead of all of them rewriting this piece of code you could package it into a role and reuse it later.
- Next time, you could simply assign the role you created and in a playbook like this, be it a single server or hundreds of servers, that's all you need.
- So that's the primary purpose of roles to make your work reusable be it for other tasks or projects within your organization or outside for others globally.

- To make the role structure, you run the command:
#### ansible-galaxy init mysql
The above command will initialize and create directory structure.
Means it will create directory for tasks, dircetory for variables, handlers and so on.

Suppose, you run this command in a particular directory and mentioned this role in a configuration file in some other directory. How this configuration will know where this directory is?



One way is to run this command in the same directory where your main configuration file is.

Second way is to move your role directory to a default location which is /etc /ansible /roles.

- All variables used by these tasks in the vars directory.
- Any default values goes into the default directory.
- All handlers go into the handlers directory.
- And any templates used by the playbooks go into the templates directory.
- Roles also help in sharing your code with others in the Ansible community. Ansible Galaxy is one such community where you can find thousands of roles for almost any task you can think of.
- Installing and configuring different web servers, different database servers, automation tools monitoring tools, packaging tools security software, et cetera.
- So before you start writing those playbooks, I highly recommend taking a look at this community first. There must be a role created for that already by someone.
- So how do you get started with roles?
- It's easy, as we discussed in the previous slide first, we create the directory structure required for a role but you don't have to do that manually.
- Ansible Galaxy has a neat tool that can create a skeleton for you. - Use the ansible-galaxy init command to initialize and create a directory structure.
- Remember, this is to create your own role from scratch and then move all of your code into the task directory into a file, and that's it.
- So how do you use your role within your playbook? Say I have my playbook within a directory called my playbook. Myplaybook.yml file contains a simple play to install and configure MySQL on my TP server.
- I would like to assign the role I created using the roles directive like this, but if I were to do that how does my playbook know where that role is? I could have created that role anywhere on my system.
#### - How can my playbook find that role?
 --------------------------
 There are different ways to do that. I could create a directory called roles within my playbook folder and move the role I created under it.
- When the playbook runs, Ansible looks for a role named MySQL under the roles directory, and that's one way of doing it.
- Or you can move the roles to a common directory designated for roles on your system at etc Ansible roles location.
- That's the default location where Ansible searches for roles if it can't be found in your playbooks directory.
- Once you have created your role and placed it in your roles directory and used it in a playbook you may share it with the community by uploading it to Ansible Galaxy through a GitHub repository.
- Now, what if you don't want to use a role from Galaxy? Instead, you want to find an existing role and use it in your playbook.
- To find roles, you could search from the Ansible Galaxy UI or do it from the command line interface using the Ansible dash galaxy search command. 
- To use a role, run the ansible-galaxy install command with the name of the role.
- The role is extracted to the default roles directory at etc Ansible roles location.
- The roles can now be used in your playbook by specifying the same name like this as an array of strings, as role names.

### Ansible Collections
--------------------------------
[Collection is basically a set of modules.]
- Suppose, you work as a network engineer responsible for managing large network infrastructure. 
- You need to automate the configuration and management of network devices from various vendors such as Cisco, Juniper and Arista.
- While Ansible provides a set of built-in modules for network automation, you require additional vendor-specific modules and functionalities.
- In this scenario, you can leverage Ansible Collections to access specialized network automation content.
- Collections such as network.cisco, network.juniper, and network.arista offer vendor-specific modules, roles, and playbooks tailored for managing network devices from each respective vendor.
- By installing these collections, you gain access to the specialized functionalities required to automate your network infrastructure effectively.
- Here's an example command to install the network.cisco collection:
-------------------------
#### ansible-galaxy collection install network.cisco
---------------------------
- Once you have installed it, you can use the modules and roles provided by the network.cisco collection in your playbooks and benefit from the specialized functionality it offers. 
- This allows you to seamlessly integrate network automation tasks specific to Cisco devices into your existing Ansible workflows.
- Now let us see what Ansible Collections are!
- Ansible Collections are basically a set of packages, modules, plugins, roles and other related assets.
- A collection is a self-contained unit that encapsulates these components, making them easily accessible and shareable.
- Collections can be created by both Ansible community members and vendors, offering a wide variety of specialized functionalities. With Ansible Collections, you gain some major benefits.
- The first one is expanded functionality.
- Let's take a look at an example of using a collection for the AWS.
- You can use a collection to manage AWS resources, extending Ansible's native capabilities.
- First, install the AWS collection,
- Then you can use the modules from this collection in your playbook.
- Second is modularity and re-usability.
- You can create a collection that encapsulates specific roles, modules, and plugins. This is the directory structure for a custom collection. You can then use this collection in different playbooks promoting re-usability.

## 7.39 Coding Exercise


### Introduction to Templating
#### What is templating?
- Say for example, the CEO of a company wishes to send an invitation letter to all of his employees to invite them to a party. 
- So he goes, "Hi, employee" And then, "I'm glad to invite you along with your family members -" followed by the names of the family members and then to attend the party arranged for the completion of 10 years, et cetera, blah, blah, blah and finally signed off by him. 
- He wants to send the same email to all of his employees but addressed to each of them specifically.
- So you must customize the email for each employee. In this email, the addressee and their family members are what needs to be customized.
- We want to use the same email format, the same content. So the email becomes the template and the names are what varies, so they become the variables.
- You can have as many variables as you want. A templating engine is responsible for taking a template and applying a given set of variables to it to create usable outputs.
- This is a simple generic use case of Templating.


What is a Jinja2 template?
- If you look up Jinja2 online, you'll see that it is a fully-featured templating engine for Python. It is beautiful and powerful and has a wide range of features. 
- We will look at some of those and practice using them as well. A simple and easy use case is to substitute parts of a train with a value from a variable.
- If the value of the variable, my_name is Bond, then the template would resolve to the name is Bond.



Jinja 2 Templates for for Dynamic Configs - Demo
- We're setting up web servers and we wanna copy our webpage and index.html file stored locally to the web server's default index.html location.
- We use Ansible's copy module to copy the local file to the remote location on all web servers. Assuming that Nginx is already installed on these web servers, we are on the playbook now.
- The index.html file is copied to all of those web servers, and when you access the web server, we see the webpage that says, "This is a Web Server".