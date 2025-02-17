### Section 2 - Ansible Configuration:

- Can copy ansible.cfg to playbook directories and customize it to override default file
- Can specify the config file using `$ANSIBLE_CONFIG` if stored in another directory
- Precedence: 
	1. Environment var
	2. config file in current directory
	3. config file in user's home dir
	4. the default at /etc/ansible/ansible.cfg
- In the config files in playbooks' dir, we can specify the values of parameters we only want to override, default values for other parameters will be selected
- Can override parameters on default config files using environment vars (for most cases: $ANSIBLE_PARAMETERNAME)
- `ansible-config list` list all configurations and their default values, and what can they be set to
- `ansible-config view` view current config file
- `ansible-config dump` shows the current configuration and where the values came from
---

### Section 3 - Ansible Inventory:

- Ansible connects to infrastructure with SSH for linux and powershell remoting for windows
- Ansible is agentless
- Target systems info is stored in an inventory file (INI format OR YAML), if not created ansible uses the default at /etc/ansible/hosts
- Can define groups in the inventory file
- Can define alias for each inventory entry by using `<alias> asible_host=<host IP or fqdn>`
- There are a list of inventory parameters to use
- Can work with localhost if specified in the inventory file
- Use keys instead of specifying SSH password (ansible vault?)
- We use parent-child relationships to group servers under a general umbrella, then use child groups for specific configs for each group
- ![[Pasted image 20241029205032.png]]
---

### Section 4 - Ansible variables:

- Variables stores information that varies with each host
- Variables can be defined in the playbook in a vars dictionary or in a file named variables
- `{{ variable_name }}` to call a variable, Jinja2 Templating
- If variables file is named after the hosts in the playbook it is automatically called
- ![[Pasted image 20241029210405.png]]
- ![[Pasted image 20241029210618.png]]
- Variable types: (all is declared with yaml format)
	- String
	- Number: can be used in math operations
	- Bool: can be used in conditionals, there are multiple values to express true and false
	- Lists: can hold variables of any type, can use index to specify elements
	- Dictionaries: key-value pairs of any type
- Group variables can be defined in the inventory file under the title`[web_servers:vars]`
- Group variables < host variables < playbook level < extra variables, extra is highest
- https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_variables.html#variable-precedence-where-should-i-put-a-variable
- To store output from commands use `register: varName`
- Use the debug module to print variables
- Scope of variables declared using the register is the host, it is associated with the host and available during the playbook execution
- There are 3 scopes of executions for variables
	- Host: a variable defined anywhere for the host, inventory file for host or a group variable or a group of groups variable. Ansible breaks down the groups and associates variables to hosts
	- Play: variables defined in the play only using vars dictionary
	- Global
- When a playbook starts, a subprocess is created for each host, then variable interpolation assigning the variable to each host
- ![[Pasted image 20241103192824.png]]
-  Magic variable `hostvars[hostname].variablename` is used to access the variables defined in other host’s scope
- Magic variables
	- `Groups` takes group name and returns the hosts in that group
	- `Group_names` takes hostname and returns all groups the host is part of
	- `Inventory_hostname` returns the name defined for this host in the inventory file
- Facts are the information about the host gathered by ansible when connecting to it
- Facts are gathered by the setup module and is run automatically by ansible when a playbook is run
- Facts are stored in a variable named ansible_facts
- To disable fact gathering use `gahter_facts: no` in the play Or change `gathering` in the ansible.cfg file, but playbook setting takes precedence
- The setup module only runs on the specified hosts in the play
---

### Section 5 - Ansible Playbooks

- A task is a single action to be performed on the host
- ![[Pasted image 20241103210044.png]]
- The actions run by tasks are called modules like: command, script, yum, service and can be viewed by `ansible-doc -l`
- Run playbooks with `ansible-playbook playbook.yml`
- Check mode is used to verify playbooks, it is like dry run so the changes are not applied to hosts `--check`, not all modules support check mode
- Diff mode is used to view the differences between the state before and after changes with `--check --diff`
- Syntax check is also provided using `--syntax-check`
- ansible-lint is used on playbooks for linting
- Can use conditionals with tasks using `when: <condition>` statement, can use `and`&`or`
- We can use `register: variable_name` to record the result of the output
- ![[Pasted image 20241104134329.png]]
- Inside a task we can create loops, a loop can have values under it or a list defined previously in a variable.
- Loops store values in a variable called item
- To loop on more than one value at a time, we can use `loop` an pass a list of dictionaries, can be passed as JSON also
- ![[Pasted image 20241104210422.png]]
- Can also use `with_items` instead of loop, there are multiple `with_*` to use, * are called lookup plugins
---

### Section 6 - Ansible Modules

- In the command module, parameters are passed after the actual command like `command: cat resolv.conf chdir=/etc`  
- In the service module, name and state can be parameters or passed as yaml dictionary
- ![[Pasted image 20241104233029.png]]
- The idea of idempotence is that ansible can run the playbook more than one time and ansible should report everything is in expected state
- Plugins are used to extend ansible's functionality, they usually execute on the ansible host itself not the inventory hosts
- ![[Pasted image 20241113154215.png]]
- `ansible-doc` command is used to see the information about Ansible modules from command line
- System modules are actions to be performed at a system level such as modifying the users and groups on a system, modifying iptables, starting/stopping the service etc
---


### Section 7 - Ansible Handles, Roles and Collections

- Handlers are specific tasks used when you want a task to only run when a change occurs
- Tasks notify handlers to execute
- 