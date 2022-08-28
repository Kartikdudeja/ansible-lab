## Ansible Hand-On Lab

Ansible works against multiple managed nodes or “hosts” in your infrastructure at the same time, using a list or group of lists known as inventory.

The inventory tells what nodes are out there to be used by Ansible, what credentials need to be used to connect to them, how the nodes are grouped, and other necessary variables.

Ansible navigator is a command based tool for creating, reviewing and troubleshooting Ansible content. This includes inventories, playbooks, and collections.

An inventory file is a text file that specifices the nodes that will be managed by the control machine. The nodes to be managed may include a list of hostnames or IP addresses of those nodes. The inventory file allows for nodes to be organized into groups by declaring a host group name within square brackets ([]).

By default, Ansible executes each task in order, one at a time, against all machines matched by the host pattern. Each task executes a module with specific arguments. When a task has executed on all target machines, Ansible moves on to the next task.

When you run a playbook, Ansible returns information about connections, the name lines of all your plays and tasks, whether each task has succeeded or failed on each machine, and whether each task has made a change on each machine. At the bottom of the playbook execution, Ansible provides a summary of the nodes that were targeted and how they performed. General failures and fatal “unreachable” communication attempts are kept separate in the counts.

Besides pure interactions on the target node there is also the possibility to copy data between the controlling instance and the managed nodes. The copy module can be used to transfer data.

The real power of Ansible is to apply the same set of tasks reliably to many hosts.

To do that, the playbook will not target an individual node in the future, but instead a group of nodes. Ansible automatically identifies all hosts in the group via the inventory.

As a best practice Ansible in real life applications playbooks are usually written adressing the group all - the limitation to certain nodes or groups is enforced at execution time via command line flags or via tools like Ansible controller.

```bash
# get a list of what is available in the inventory

ansible-navigator inventory --list

ansible-navigator inventory --graph

```

Playbooks are files which describe the desired configurations or steps to implement on managed hosts. Playbooks can change lengthy, complex administrative tasks into easily repeatable routines with predictable and successful outcomes.

A playbook can have multiple plays and a play can have one or multiple tasks. In a task, a module is called to run an action against. The goal of a play is to map a group of hosts. The goal of a task is to implement modules against those hosts.

```bash
# Run a Playbook

ansible-navigator run apache.yml

```

There is a built-in task labeled Gathering Facts that runs automatically at the beginning of each Ansible play. It collects information about the managed nodes.

```bash
# run an ad-hoc command to make sure apache was successfully installed on the managed node

ansible node1 -m ansible.builtin.shell -a "rpm -qi httpd"

# This uses the module (-m) ansible.builtin.shell with module arguments (-a) that states to run the following command rpm -qi httpd on node1.
```

```bash
# running an ad-hoc command to check if a service is running or not

ansible node1 -m ansible.builtin.service_facts | grep -A4 "httpd.service"

```

The files directory is a default location to store ansible files you wish to provide to hosts you are managing.

#### Variables

Ansible supports variables to store values that can be used in Playbooks. Variables can be defined in a variety of places and have a clear precedence. Ansible substitutes the variable with its value when a task is executed.

Variables are referenced in Ansible Playbooks by placing the variable name in double curly braces:

Here comes a variable {{ variable1 }}

Variables and their values can be defined in various places: the inventory, additional files, on the command line, etc.

The recommended practice to provide variables in the inventory is to define them in files located in two directories named host_vars and group_vars:

To define variables for a group “servers”, a YAML file named group_vars/servers.yml with the variable definitions is created.
To define variables specifically for a host node1, the file host_vars/node1.yml with the variable definitions is created.

Host variables take precedence over group variables.

#### Conditional Expressions

In a playbook, you can use Ansible conditionals to execute tasks or plays when certain conditions are met. To implement a conditional, the when statement must be used, followed by the condition to test. The condition is expressed using one of the available operators like

==	Compares two objects for equality.
!=	Compares two objects for inequality.
>	true if the left hand side is greater than the right hand side.
>=	true if the left hand side is greater or equal to the right hand side.
<	true if the left hand side is lower than the right hand side.
<=	true if the left hand side is lower or equal to the right hand side.

Ansible fact variable labeled inventory_hostname that is used within the when conditional to specify the specific groups that the task should run on.

#### Loops

Loops enable us to repeat the same task over and over again. For example, lets say you want to create multiple users. By using an Ansible loop, you can do that in a single task.

Loops can also iterate over more than just basic lists. For example, if you have a list of users with their coresponding group, loop can iterate over them as well.

#### Handlers

Sometimes when a task does make a change to the system, an additional task or tasks may need to be run. For example, a change to a service’s configuration file may then require that the service be restarted so that the changed configuration takes effect.

Here Ansible’s handlers come into play. Handlers can be seen as inactive tasks that only get triggered when explicitly invoked using the "notify" statement.

#### Templates

Ansible uses Jinja2 templating to modify files before they are distributed to managed hosts. Jinja2 is one of the most used template engines for Python.

When a template for a file has been created, it can be deployed to the managed hosts using the template module, which supports the transfer of a local file from the control node to the managed hosts.

A typical ending for a file to indicate that it is a template file is .j2. Though this is strictly speaking not necessary, it is established practice.

The template file contains the basic text that will later be copied over to the hosts. It contains variables that will be replaced on the target machines individually.

#### Ansible Role

An Ansible role has a defined directory structure with eight main standard directories. You must include at least one of these directories in each role. You can omit any directories the role does not use.

By default Ansible will look in each directory within a role for a main.yml file for relevant content.

Ansible looks for roles in a subdirectory called roles in the project directory. This can be overridden in the Ansible configuration. Each role has its own directory. To ease creation of a new role the tool ansible-galaxy can be used.

> Ansible Galaxy is your hub for finding, reusing and sharing Ansible content. ansible-galaxy helps to interact with Ansible Galaxy. For now we'll just using it as a helper to build the directory structure.

```bash
# create a basic role directory structure
mkdir roles
ansible-galaxy init --offline roles/apache_vhost
```

With Ansible Roles, we include the tasks within the tasks/main.yml file. This file only has the tasks and is not a complete playbook.

create handler in the file: roles/apache_vhost/handlers/main.yml

#### Ansible man page
```bash
ansible-doc ansible.builtin.get_url
```