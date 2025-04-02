# Ansible Command and Configuration Cheat Sheet

## Installation and Setup

### Installation
```bash
# Install Ansible on Ubuntu/Debian
sudo apt update
sudo apt install ansible

# Install Ansible on RHEL/CentOS
sudo yum install epel-release
sudo yum install ansible

# Install Ansible on macOS with Homebrew
brew install ansible

# Install Ansible with pip
pip install ansible

# Check Ansible version
ansible --version
```

### Configuration Files
```ini
# Main configuration file: /etc/ansible/ansible.cfg
# User configuration: ~/.ansible.cfg
# Project configuration: ./ansible.cfg (in current directory)

# Sample ansible.cfg
[defaults]
inventory = ./inventory
remote_user = deploy
host_key_checking = False
roles_path = ./roles:/usr/share/ansible/roles
forks = 10
timeout = 30
log_path = /var/log/ansible.log

[privilege_escalation]
become = True
become_method = sudo
become_user = root
become_ask_pass = False

[ssh_connection]
pipelining = True
ssh_args = -o ControlMaster=auto -o ControlPersist=60s
```

### Environment Variables
```bash
# Common Ansible environment variables
export ANSIBLE_CONFIG=/path/to/ansible.cfg      # Override default config file
export ANSIBLE_INVENTORY=/path/to/inventory     # Specify inventory file
export ANSIBLE_ROLES_PATH=/path/to/roles        # Set roles path
export ANSIBLE_HOST_KEY_CHECKING=False          # Disable host key checking
export ANSIBLE_NOCOWS=1                         # Disable cowsay
export ANSIBLE_VAULT_PASSWORD_FILE=~/.vault_pass # Set vault password file
```

## Inventory

### Basic Inventory
```ini
# Static inventory file (INI format): inventory.ini

# Single host
server1.example.com

# Group of hosts
[webservers]
web1.example.com
web2.example.com
web3.example.com

# Host with custom SSH port
web4.example.com:2222

# Host with variables
db1.example.com ansible_host=10.0.0.5 ansible_user=admin

# Group variables
[webservers:vars]
http_port=80
proxy_timeout=5

# Nested groups
[production:children]
webservers
databases

[databases]
db1.example.com
db2.example.com
```

### YAML Inventory
```yaml
# YAML format inventory: inventory.yml
all:
  hosts:
    mail.example.com:
  children:
    webservers:
      hosts:
        web1.example.com:
          http_port: 80
        web2.example.com:
          http_port: 8080
    databases:
      hosts:
        db1.example.com:
        db2.example.com:
      vars:
        db_port: 5432
    production:
      children:
        webservers:
        databases:
```

### Dynamic Inventory
```bash
# Using dynamic inventory scripts
ansible-playbook -i inventory_script.py playbook.yml

# AWS EC2 dynamic inventory
pip install boto3
ansible-playbook -i aws_ec2.yml playbook.yml

# Example aws_ec2.yml content
plugin: aws_ec2
regions:
  - us-east-1
  - us-west-1
keyed_groups:
  - key: tags.Environment
    prefix: env
  - key: tags.Role
    prefix: role
```

### Inventory Parameters
```ini
# Common inventory parameters
ansible_host=192.168.1.100     # The IP address to connect to
ansible_port=22               # The SSH port
ansible_user=root             # The user to connect as
ansible_password=password     # The password to use (not recommended, use Vault)
ansible_connection=ssh        # Connection type (ssh, local, docker)
ansible_ssh_private_key_file=/path/to/key.pem  # SSH private key
ansible_become=yes            # Enable privilege escalation
ansible_become_method=sudo    # Method for privilege escalation
ansible_become_user=root      # User to become
ansible_become_password=password  # Password for privilege escalation
ansible_python_interpreter=/usr/bin/python3  # Python interpreter path
```

## Command Line Tools

### ansible (Ad-hoc Commands)
```bash
# Basic syntax
ansible <host-pattern> -m <module> -a "<module-arguments>"

# Examples
ansible all -m ping                            # Ping all hosts
ansible webservers -m command -a "uptime"      # Run uptime on webservers
ansible databases -m shell -a "ps aux | grep mysql"  # Run shell command
ansible all -m setup                           # Gather facts about all hosts
ansible web1 -m copy -a "src=/local/file dest=/remote/file"  # Copy file
ansible web1 -m apt -a "name=nginx state=present"  # Install package
ansible web1 -m service -a "name=nginx state=started"  # Start service
ansible all -m user -a "name=deploy state=present"  # Create user
ansible all -b -m apt -a "upgrade=yes update_cache=yes" # Run with sudo (become)
ansible webservers -m command -a "whoami" -u deploy  # Run as specific user
ansible databases -m command -a "df -h" -l           # Limit to a subset of hosts

# Targeting options
ansible all --list-hosts                       # List all hosts
ansible '*:!webservers' -m ping                # All hosts except webservers
ansible 'webservers:&production' -m ping       # Intersection of groups
ansible webservers[0] -m ping                  # First host in group
ansible all -l '*.example.com' -m ping         # Using wildcard patterns
```

### ansible-playbook
```bash
# Basic syntax
ansible-playbook playbook.yml

# Options
ansible-playbook playbook.yml --syntax-check   # Check syntax
ansible-playbook playbook.yml --list-tasks     # List tasks
ansible-playbook playbook.yml --list-hosts     # List hosts
ansible-playbook playbook.yml --list-tags      # List tags
ansible-playbook playbook.yml -v               # Verbose (-v, -vv, -vvv for more)
ansible-playbook playbook.yml -i inventory     # Specify inventory
ansible-playbook playbook.yml -l webservers    # Limit to group
ansible-playbook playbook.yml -l web1,web2     # Limit to specific hosts
ansible-playbook playbook.yml -e "var1=value1" # Set extra variables
ansible-playbook playbook.yml -t tag1,tag2     # Run only specific tags
ansible-playbook playbook.yml --skip-tags=tag3 # Skip specific tags
ansible-playbook playbook.yml -C               # Check mode (dry run)
ansible-playbook playbook.yml -D               # Diff mode (show changes)
ansible-playbook playbook.yml -f 10            # Set forks (parallel processes)
ansible-playbook playbook.yml --start-at-task="Install packages"  # Start at specific task
ansible-playbook playbook.yml --step           # Interactive, confirm each task
ansible-playbook playbook.yml --flush-cache    # Clear fact cache
```

### ansible-galaxy
```bash
# Role management
ansible-galaxy init role_name                  # Create new role structure
ansible-galaxy install username.role_name      # Install role from Galaxy
ansible-galaxy install -r requirements.yml     # Install from requirements file
ansible-galaxy list                            # List installed roles
ansible-galaxy remove username.role_name       # Remove role
ansible-galaxy search 'nginx'                  # Search for roles
ansible-galaxy info username.role_name         # Show role info

# Collection management
ansible-galaxy collection install namespace.collection  # Install collection
ansible-galaxy collection install -r requirements.yml   # Install from requirements
ansible-galaxy collection list                          # List installed collections
ansible-galaxy collection build                         # Build a collection
```

### ansible-vault
```bash
# Encrypting files
ansible-vault create secret.yml                # Create encrypted file
ansible-vault edit secret.yml                  # Edit encrypted file
ansible-vault view secret.yml                  # View encrypted file
ansible-vault encrypt existing.yml             # Encrypt existing file
ansible-vault decrypt secret.yml               # Decrypt file
ansible-vault rekey secret.yml                 # Change encryption key
ansible-vault encrypt_string 'secret_value' --name 'secret_var'  # Encrypt single string

# Using encrypted files
ansible-playbook playbook.yml --ask-vault-pass  # Prompt for vault password
ansible-playbook playbook.yml --vault-password-file=~/.vault_pass  # Use password file
ansible-playbook playbook.yml --vault-id dev@~/.vault_pass.dev  # Multiple vault IDs
```

### ansible-doc
```bash
# Module documentation
ansible-doc -l                                 # List all modules
ansible-doc copy                               # Show help for copy module
ansible-doc -s copy                            # Show playbook snippet for module
ansible-doc -t lookup file                     # Show lookup plugin documentation
ansible-doc -t filter regex_replace            # Show filter plugin documentation
```

### ansible-inventory
```bash
# Inventory inspection
ansible-inventory --list                       # List all inventory
ansible-inventory --host web1                  # Show host variables
ansible-inventory --graph                      # Show inventory graph
ansible-inventory -i inventory.yml --graph     # With specified inventory
ansible-inventory --yaml --output inventory.yml  # Convert to YAML
```

### ansible-config
```bash
# Configuration inspection
ansible-config list                            # List all config options
ansible-config dump                            # Show current settings
ansible-config view                            # Show config file
ansible-config dump --only-changed             # Show changed settings
```

## Playbooks

### Basic Playbook Structure
```yaml
---
# simple_playbook.yml
- name: Update web servers
  hosts: webservers
  become: true
  vars:
    http_port: 80
    max_clients: 200
  
  tasks:
    - name: Ensure Apache is installed
      apt:
        name: apache2
        state: present
        update_cache: yes
      
    - name: Ensure Apache is started
      service:
        name: apache2
        state: started
        enabled: yes
```

### Task Control
```yaml
# Task control examples
tasks:
  - name: Task with condition
    apt:
      name: apache2
      state: present
    when: ansible_distribution == 'Ubuntu'
    
  - name: Task with tag
    service:
      name: apache2
      state: started
    tags: services
    
  - name: Task with changed_when
    command: service apache2 restart
    changed_when: false
    
  - name: Task with failed_when
    command: /usr/bin/check-status.sh
    register: command_result
    failed_when: "'FAILED' in command_result.stdout"
    
  - name: Task with ignore_errors
    command: /usr/bin/may-fail.sh
    ignore_errors: yes
    
  - name: Task with privilege escalation
    apt:
      name: apache2
      state: present
    become: yes
    become_user: root
    
  - name: Task with delegation
    command: /usr/bin/check-db-status.sh
    delegate_to: "{{ item }}"
    loop:
      - db1.example.com
      - db2.example.com
      
  - name: Task with run_once
    command: /usr/bin/db-initialize.sh
    run_once: true
    delegate_to: db1.example.com
```

### Handlers
```yaml
# Handlers are tasks that run when notified
tasks:
  - name: Update Apache configuration
    template:
      src: httpd.conf.j2
      dest: /etc/httpd/conf/httpd.conf
    notify: Restart Apache
    
handlers:
  - name: Restart Apache
    service:
      name: httpd
      state: restarted
      
  # Multiple handlers can be notified
  - name: Update Apache configuration
    template:
      src: httpd.conf.j2
      dest: /etc/httpd/conf/httpd.conf
    notify:
      - Restart Apache
      - Log configuration change
      
  # Handlers with listen
  - name: Log configuration change
    command: echo "Configuration updated at $(date)" >> /var/log/ansible_changes.log
    listen: "config_updated"
    
  # Notify a handler with listen
  - name: Update Apache configuration
    template:
      src: httpd.conf.j2
      dest: /etc/httpd/conf/httpd.conf
    notify: "config_updated"
```

### Blocks
```yaml
# Group tasks with blocks
tasks:
  - name: Installation tasks
    block:
      - name: Install Apache
        apt:
          name: apache2
          state: present
      
      - name: Install PHP
        apt:
          name: php
          state: present
    when: ansible_distribution == 'Ubuntu'
    become: yes
    
  # Blocks with error handling
  - name: Database setup with error handling
    block:
      - name: Setup database
        command: /usr/bin/db-setup.sh
        
      - name: Setup replication
        command: /usr/bin/db-replication.sh
    rescue:
      - name: Log failure
        command: echo "Database setup failed at $(date)" >> /var/log/db_failures.log
        
      - name: Send alert
        mail:
          to: admin@example.com
          subject: Database setup failed
          body: Check logs for details
    always:
      - name: Cleanup temporary files
        file:
          path: /tmp/db_setup/
          state: absent
```

### Include and Import
```yaml
# Include tasks (dynamic)
tasks:
  - name: Include task file
    include_tasks: included_tasks.yml
    
  - name: Include task file with variables
    include_tasks: db_setup.yml
    vars:
      db_name: user_database
      
  - name: Include tasks conditionally
    include_tasks: debian_tasks.yml
    when: ansible_os_family == 'Debian'
    
# Import tasks (static)
tasks:
  - name: Import task file
    import_tasks: imported_tasks.yml
    
  - name: Import tasks with tags
    import_tasks: monitoring_setup.yml
    tags: monitoring
    
# Include playbook
- name: Include another playbook
  import_playbook: webserver.yml
  
# Include role (dynamic)
tasks:
  - name: Include role
    include_role:
      name: nginx
    
  - name: Include role with variables
    include_role:
      name: mysql
      tasks_from: setup  # Only run tasks from role/tasks/setup.yml
    vars:
      mysql_port: 3307
      
# Import role (static)
tasks:
  - name: Import role
    import_role:
      name: common
```

### Pre and Post Tasks
```yaml
# Tasks run before/after the roles
- name: Webserver setup
  hosts: webservers
  
  pre_tasks:
    - name: Update apt cache
      apt:
        update_cache: yes
      when: ansible_os_family == 'Debian'
      tags: always
      
  roles:
    - common
    - nginx
    
  post_tasks:
    - name: Verify website is responding
      uri:
        url: http://localhost/
        status_code: 200
      register: result
      until: result.status == 200
      retries: 5
      delay: 10
```

## Variables and Facts

### Defining Variables
```yaml
# In playbooks
vars:
  http_port: 80
  max_clients: 200
  
vars_files:
  - vars/common.yml
  - vars/{{ env }}.yml
  
# Prompting for variables
vars_prompt:
  - name: username
    prompt: Enter username
    private: no
    
  - name: password
    prompt: Enter password
    private: yes  # Hide input
    
# From inventory
[webservers:vars]
http_port=80
max_clients=200

# From host_vars and group_vars directories
# group_vars/all.yml     - Variables for all hosts
# group_vars/webservers.yml - Variables for webservers group
# host_vars/web1.example.com.yml - Variables for specific host

# From command line
ansible-playbook playbook.yml -e "http_port=8080 max_clients=100"
ansible-playbook playbook.yml -e @vars.json

# Register variables
- name: Get date
  command: date
  register: date_output
  
- name: Show date
  debug:
    msg: "Date is {{ date_output.stdout }}"
```

### Variable Precedence (Lowest to Highest)
```
1. Command line values (e.g., -e "var=value")
2. role defaults/main.yml
3. inventory file or script group vars
4. playbook group_vars/all
5. playbook group_vars/*
6. inventory file or script host vars
7. playbook host_vars/*
8. host facts / cached set_facts
9. play vars
10. play vars_prompt
11. play vars_files
12. role vars (defined in role/vars/main.yml)
13. block vars (only for tasks in the block)
14. task vars (only for the task)
15. include_vars
16. set_facts / registered vars
17. role (and include_role) params
18. include params
19. extra vars (always win precedence)
```

### Facts and Magic Variables
```yaml
# Display all facts
- name: Show facts
  debug:
    var: ansible_facts
    
# Filter specific facts
- name: Show specific facts
  debug:
    msg: |
      OS: {{ ansible_distribution }}
      Version: {{ ansible_distribution_version }}
      IP Address: {{ ansible_default_ipv4.address }}
      
# Custom facts
# Create file /etc/ansible/facts.d/custom.fact on remote host:
# [example]
# foo=bar
# version=1.0

# Access custom facts
- name: Show custom facts
  debug:
    var: ansible_local.example

# Commonly used facts
ansible_distribution          # e.g., Ubuntu, CentOS
ansible_distribution_version  # e.g., 20.04, 8.2
ansible_os_family            # e.g., Debian, RedHat
ansible_architecture         # e.g., x86_64
ansible_kernel               # e.g., 5.4.0-42-generic
ansible_hostname             # Host's short hostname
ansible_fqdn                 # Host's FQDN
ansible_default_ipv4.address # Host's IPv4 address
ansible_memtotal_mb          # Total memory in MB
ansible_processor_vcpus      # Number of virtual CPUs
ansible_pkg_mgr              # Package manager (apt, yum, etc.)
ansible_python.version.major # Python major version

# Common magic variables
inventory_hostname          # Current host being processed
inventory_hostname_short    # Short hostname without domain
groups                      # Dictionary of all groups and hosts
group_names                 # List of groups the current host is part of
hostvars                    # Dictionary of all host variables
play_hosts                  # List of hosts in current play
ansible_play_hosts          # List of hosts after play filtering
ansible_play_batch          # List of hosts actually being processed
ansible_version             # Dictionary of Ansible version info
ansible_check_mode          # Boolean indicating check mode
```

### Variable Manipulation
```yaml
# String operations
- name: String manipulation
  debug:
    msg: |
      Upper: {{ 'hello'|upper }}
      Lower: {{ 'HELLO'|lower }}
      Title: {{ 'hello world'|title }}
      Replace: {{ 'hello'|replace('e', 'a') }}
      Length: {{ 'hello'|length }}
      
# List operations
- name: List manipulation
  debug:
    msg: |
      First item: {{ ['a', 'b', 'c'][0] }}
      Last item: {{ ['a', 'b', 'c'][-1] }}
      Sliced: {{ ['a', 'b', 'c', 'd'][1:3] }}
      Joined: {{ ['a', 'b', 'c']|join(',') }}
      Sorted: {{ [3, 1, 2]|sort }}
      Min: {{ [3, 1, 2]|min }}
      Max: {{ [3, 1, 2]|max }}
      Unique: {{ [1, 2, 1, 3]|unique }}
      
# Dictionary operations
- name: Dictionary manipulation
  debug:
    msg: |
      Key: {{ {'a': 1, 'b': 2}['a'] }}
      Items: {{ {'a': 1, 'b': 2}.items() }}
      Keys: {{ {'a': 1, 'b': 2}.keys() }}
      Values: {{ {'a': 1, 'b': 2}.values() }}
      Combined: {{ {'a': 1}|combine({'b': 2}) }}
      
# Type conversions
- name: Type conversion
  debug:
    msg: |
      String to Int: {{ '10'|int }}
      Int to String: {{ 10|string }}
      String to Bool: {{ 'yes'|bool }}
      To JSON: {{ {'a': 1}|to_json }}
      From JSON: {{ '{"a": 1}'|from_json }}
      To YAML: {{ {'a': 1}|to_yaml }}
      From YAML: {{ 'a: 1'|from_yaml }}
```

### Fact Gathering Control
```yaml
# Disable fact gathering for performance
- hosts: all
  gather_facts: no
  tasks:
    - name: Simple task
      debug:
        msg: "Facts not gathered"
        
# Gather subset of facts
- hosts: all
  gather_facts: yes
  gather_subset:
    - hardware
    - network
    - !all
  tasks:
    - name: Show network facts
      debug:
        var: ansible_default_ipv4
        
# Gather custom facts
- hosts: all
  gather_facts: yes
  tasks:
    - name: Set custom fact
      set_fact:
        custom_fact: "Custom value"
        
    - name: Show custom fact
      debug:
        var: custom_fact
        
    - name: Set persistent fact
      set_fact:
        persistent_fact: "Will be cached"
        cacheable: yes
```

## Loops and Conditionals

### Loops
```yaml
# Simple loop
- name: Install packages
  apt:
    name: "{{ item }}"
    state: present
  loop:
    - nginx
    - mysql-server
    - php-fpm
    
# Loop with index
- name: Indexed loop
  debug:
    msg: "Item {{ ansible_loop.index }}: {{ item }}"
  loop:
    - apple
    - banana
    - cherry
    
# Nested loops
- name: Nested loops
  debug:
    msg: "Outer: {{ item[0] }}, Inner: {{ item[1] }}"
  loop: "{{ ['a', 'b']|product(['1', '2'])|list }}"
  
# Loop with dictionary
- name: Dictionary loop
  debug:
    msg: "Key: {{ item.key }}, Value: {{ item.value }}"
  loop: "{{ {'a': 1, 'b': 2, 'c': 3}|dict2items }}"
  
# Loop until condition
- name: Wait for service to start
  command: systemctl status mysql
  register: result
  until: result.rc == 0
  retries: 10
  delay: 5
  
# Loop with range
- name: Range loop
  debug:
    msg: "Number {{ item }}"
  loop: "{{ range(1, 5 + 1) }}"
  
# Loop with fileglob
- name: Process config files
  template:
    src: "{{ item }}"
    dest: "/etc/conf/{{ item|basename }}"
  loop: "{{ lookup('fileglob', 'templates/*.conf') }}"
  
# Loop with inventory
- name: Loop through hosts
  debug:
    msg: "Host: {{ item }}"
  loop: "{{ groups['webservers'] }}"
  
# Loop with subelements
- name: Install packages with specific versions
  apt:
    name: "{{ item.0.name }}"
    state: present
    version: "{{ item.1 }}"
  loop: "{{ packages|subelements('versions') }}"
  vars:
    packages:
      - name: nginx
        versions:
          - 1.18.0
          - 1.20.0
      - name: mysql
        versions:
          - 8.0
          
# Loop with zip
- name: Create users with passwords
  user:
    name: "{{ item.0 }}"
    password: "{{ item.1|password_hash('sha512') }}"
  loop: "{{ ['alice', 'bob', 'charlie']|zip(['pass1', 'pass2', 'pass3'])|list }}"
```

### Conditionals
```yaml
# Basic conditions
- name: Install Apache on Debian/Ubuntu
  apt:
    name: apache2
    state: present
  when: ansible_os_family == "Debian"
  
- name: Install Apache on RedHat/CentOS
  yum:
    name: httpd
    state: present
  when: ansible_os_family == "RedHat"
  
# Multiple conditions
- name: Install Apache on Debian 10+
  apt:
    name: apache2
    state: present
  when:
    - ansible_os_family == "Debian"
    - ansible_distribution_major_version | int >= 10
    
# Logic operators
- name: Complex condition
  debug:
    msg: "Condition met"
  when: >
    (ansible_distribution == "Ubuntu" and
     ansible_distribution_version >= "20.04") or
    (ansible_distribution == "Debian" and
     ansible_distribution_version >= "10")
     
# Checking registered variables
- name: Check if file exists
  stat:
    path: /etc/myapp.conf
  register: config_file
  
- name: Create configuration if missing
  template:
    src: myapp.conf.j2
    dest: /etc/myapp.conf
  when: not config_file.stat.exists
  
# Check if a variable is defined
- name: Print debug info
  debug:
    msg: "Debug mode enabled"
  when: debug_mode is defined and debug_mode|bool
  
# Check variable type or state
- name: Check variable types
  debug:
    msg: "Checks passed"
  when:
    - my_string is string
    - my_list is sequence
    - my_dict is mapping
    - my_path is file
    - my_dir is directory
    - my_var is defined
    - my_none is none
    - my_true is true
    - my_false is false
    
# Conditional imports
- name: Import OS-specific tasks
  import_tasks: "{{ ansible_distribution|lower }}.yml"
  when: ansible_distribution in ['Ubuntu', 'Debian', 'CentOS']
```

## Roles

### Role Directory Structure
```
roles/
  common/                   # Role name
    defaults/               # Default variables
      main.yml
    files/                  # Static files
      example.txt
    handlers/               # Handlers
      main.yml
    meta/                   # Role metadata and dependencies
      main.yml
    tasks/                  # Tasks
      main.yml
    templates/              # Jinja2 templates
      config.j2
    vars/                   # Variables with higher precedence
      main.yml
    library/                # Custom modules
    module_utils/           # Module utilities
    lookup_plugins/         # Custom lookup plugins
```

### Role Usage
```yaml
# Basic role usage
- hosts: webservers
  roles:
    - common
    - nginx
    
# Role with variables
- hosts: webservers
  roles:
    - role: nginx
      vars:
        nginx_port: 8080
        
# Conditional role
- hosts: all
  roles:
    - role: nginx
      when: ansible_os_family == "Debian"
      
# Role with tags
- hosts: webservers
  roles:
    - role: nginx
      tags: ["web", "nginx"]
      
# Role with pre and post tasks
- hosts: webservers
  pre_tasks:
    - name: Update package cache
      apt:
        update_cache: yes
      when: ansible_os_family == "Debian"
      
  roles:
    - common
    - nginx
    
  post_tasks:
    - name: Verify deployment
      uri:
        url: http://localhost/
        status_code: 200
```

### Role Dependencies
```yaml
# In meta/main.yml
dependencies:
  - role: common
    vars:
      common_user: www-data
      
  - role: database
    vars:
      db_name: appdb
    when: install_database | bool
```

### Role in a Task Block
```yaml
# Include role as a task
- name: Set up web server
  hosts: webservers
  tasks:
    - name: Include common role first
      import_role:
        name: common
        
    - name: Configure app
      template:
        src: app.conf.j2
        dest: /etc/app.conf
        
    - name: Include nginx role
      include_role:
        name: nginx
      vars:
        nginx_port: 8080
```

## Templates (Jinja2)

### Basic Syntax
```jinja
{# This is a comment #}

{# Variables #}
{{ variable_name }}
{{ dictionary.key }}
{{ list[0] }}

{# Filters #}
{{ string | upper }}
{{ list | join(', ') }}
{{ path | basename }}
{{ 'hello' | replace('e', 'a') }}

{# Control structures #}
{% if condition %}
  Rendered if condition is true
{% elif other_condition %}
  Rendered if other_condition is true
{% else %}
  Rendered if all conditions are false
{% endif %}

{% for item in list %}
  Item: {{ item }}
{% endfor %}

{% for key, value in dictionary.items() %}
  {{ key }}: {{ value }}
{% endfor %}
```

### Common Jinja2 Filters
```jinja
{# String filters #}
{{ 'hello' | upper }}                {# HELLO #}
{{ 'HELLO' | lower }}                {# hello #}
{{ 'hello' | capitalize }}           {# Hello #}
{{ 'hello world' | title }}          {# Hello World #}
{{ 'hello' | replace('e', 'a') }}    {# hallo #}
{{ 'hello\nworld' | wordwrap(10) }}  {# Word wrap at 10 chars #}
{{ 'hello' | truncate(3) }}          {# hel... #}

{# List filters #}
{{ [1, 2, 3] | first }}              {# 1 #}
{{ [1, 2, 3] | last }}               {# 3 #}
{{ [3, 1, 2] | sort }}               {# [1, 2, 3] #}
{{ [1, 2, 3] | reverse }}            {# [3, 2, 1] #}
{{ [1, 2, 3] | length }}             {# 3 #}
{{ [1, 2, 1, 3] | unique }}          {# [1, 2, 3] #}
{{ [1, 2] | union([2, 3]) }}         {# [1, 2, 3] #}
{{ [1, 2, 3] | join(', ') }}         {# 1, 2, 3 #}
{{ [1, 2, 3] | min }}                {# 1 #}
{{ [1, 2, 3] | max }}                {# 3 #}
{{ [1, 2, 3] | sum }}                {# 6 #}

{# Dictionary filters #}
{{ dict | combine(other_dict) }}     {# Combine dictionaries #}
{{ dict | dict2items }}              {# Convert to list of items #}
{{ items | items2dict }}             {# Convert list to dictionary #}

{# Path filters #}
{{ path | basename }}                {# Extract filename #}
{{ path | dirname }}                 {# Extract directory #}
{{ path | expanduser }}              {# Expand ~ to home dir #}
{{ path | realpath }}                {# Canonicalize path #}

{# Date filters #}
{{ date_var | to_datetime }}         {# Convert to datetime #}
{{ date_var | to_datetime('%Y-%m-%d') }} {# With format #}
{{ '%Y-%m-%d' | strftime }}          {# Current date formatted #}

{# Encoding filters #}
{{ string | b64encode }}             {# Base64 encode #}
{{ encoded | b64decode }}            {# Base64 decode #}
{{ dict | to_json }}                 {# Convert to JSON #}
{{ json_string | from_json }}        {# Parse JSON #}
{{ dict | to_yaml }}                 {# Convert to YAML #}
{{ yaml_string | from_yaml }}        {# Parse YAML #}
{{ string | md5 }}                   {# MD5 hash #}
{{ string | sha1 }}                  {# SHA1 hash #}
{{ string | to_uuid }}               {# Convert to UUID #}

{# Ansible-specific filters #}
{{ var | default('default_value') }} {# Use default if undefined #}
{{ string | quote }}                 {# Shell-quote string #}
{{ path | expandvars }}              {# Expand env vars in path #}
{{ secret | password_hash('sha512') }} {# Generate password hash #}
{{ string | regex_replace(pattern, replacement) }} {# Replace using regex #}
{{ list | map('extract', dict) }}    {# Extract values from dict using keys in list #}
```

### Template Control Structures
```jinja
{# Conditionals #}
{% if ansible_os_family == 'Debian' %}
server {
    listen {{ nginx_port | default(80) }};
}
{% endif %}

{# Loops #}
{% for user in users %}
user {{ user.name }} {{ user.shell | default('/bin/bash') }};
{% endfor %}

{# Loop variables #}
{% for item in items %}
Item {{ loop.index }}: {{ item }}
{% if not loop.last %},{% endif %}
{% endfor %}

{# Loop control #}
{% for item in items %}
{% if loop.first %}First: {% endif %}
{{ item }}
{% if loop.last %}Last!{% endif %}
{% endfor %}

{# Filters in loops #}
{% for user in users | sort(attribute='name') %}
{{ user.name }}
{% endfor %}

{# Conditional expressions #}
{{ 'odd' if value % 2 == 1 else 'even' }}

{# Set variables #}
{% set var = 'value' %}
{{ var }}

{# Macros (reusable template fragments) #}
{% macro user_info(name, email) %}
  Name: {{ name }}
  Email: {{ email }}
{% endmacro %}

{{ user_info('John', 'john@example.com') }}
```

### Template Inheritance
```jinja
{# base.conf.j2 #}
server {
    {% block server_config %}
    listen 80;
    server_name example.com;
    {% endblock %}
    
    {% block locations %}
    location / {
        root /var/www/html;
    }
    {% endblock %}
}

{# vhost.conf.j2 #}
{% extends "base.conf.j2" %}

{% block server_config %}
listen {{ port | default(80) }};
server_name {{ server_name }};
{% endblock %}

{% block locations %}
{{ super() }}
location /api {
    proxy_pass http://backend;
}
{% endblock %}
```

### Template Includes
```jinja
{# main.conf.j2 #}
server {
    listen 80;
    
    {% include "server_name.conf.j2" %}
    
    {% include "locations/" + location_file %}
}

{# server_name.conf.j2 #}
server_name {{ server_name }};
```

### Template Debug
```jinja
{# Print all variables #}
{{ vars | to_nice_json }}

{# Print specific variable #}
{{ variable | to_json }}

{# Debug template logic #}
{% if debug | default(false) %}
Debug info:
- OS: {{ ansible_os_family }}
- Distribution: {{ ansible_distribution }}
- IP: {{ ansible_default_ipv4.address }}
{% endif %}
```

## Error Handling and Debugging

### Handling Errors
```yaml
# Ignore errors
- name: This might fail but we'll continue
  command: /bin/false
  ignore_errors: yes
  
# Custom condition to fail
- name: Check application status
  command: /usr/bin/check_app.sh
  register: app_status
  failed_when: "'ERROR' in app_status.stdout"
  
# Custom condition for change
- name: Always report as changed
  command: /bin/true
  changed_when: true
  
- name: Never report as changed
  shell: wall "System maintenance in 5 minutes"
  changed_when: false
  
# Block-level error handling
- name: Database operations with error handling
  block:
    - name: Create database
      mysql_db:
        name: app_db
        state: present
        
    - name: Import schema
      mysql_db:
        name: app_db
        state: import
        target: /tmp/schema.sql
  rescue:
    - name: Log error
      lineinfile:
        path: /var/log/ansible_errors.log
        line: "Database setup failed: {{ ansible_date_time.iso8601 }}"
        create: yes
        
    - name: Send notification
      mail:
        to: admin@example.com
        subject: "Database setup failed"
        body: "Check logs for details"
  always:
    - name: Cleanup temp files
      file:
        path: /tmp/schema.sql
        state: absent
```

### Timeouts and Retries
```yaml
# Task timeout
- name: Long-running task
  command: /usr/bin/long_process.sh
  async: 3600  # Run for up to 1 hour
  poll: 0      # Don't wait (fire and forget)
  
# Check on async task
- name: Start backup
  command: /usr/bin/backup.sh
  async: 3600
  poll: 0
  register: backup_job
  
- name: Check backup status
  async_status:
    jid: "{{ backup_job.ansible_job_id }}"
  register: job_result
  until: job_result.finished
  retries: 30
  delay: 60
  
# Retry until success
- name: Wait for service to be available
  uri:
    url: http://localhost:8080/healthcheck
    status_code: 200
  register: result
  until: result.status == 200
  retries: 10
  delay: 5
```

### Debugging
```yaml
# Simple debug message
- name: Print message
  debug:
    msg: "This is a debug message"
  
# Print variable value
- name: Show variable
  debug:
    var: my_variable
    
# Print complex variable with formatting
- name: Pretty print
  debug:
    msg: "{{ my_complex_var | to_nice_yaml }}"
    
# Conditional debugging
- name: Debug when condition is met
  debug:
    msg: "Debug info: {{ debug_info }}"
  when: verbosity | default(false) | bool
  
# Use verbosity levels
- name: Debug level 1
  debug:
    msg: "Basic debug info"
    verbosity: 1  # Show with -v
    
- name: Debug level 3
  debug:
    msg: "Detailed debug info"
    verbosity: 3  # Show with -vvv
    
# Fail task with custom message
- name: Verify requirements
  fail:
    msg: "Python 3.6+ is required, found: {{ ansible_python.version.major }}.{{ ansible_python.version.minor }}"
  when: ansible_python.version.major < 3 or
        (ansible_python.version.major == 3 and ansible_python.version.minor < 6)
        
# Pause execution
- name: Pause for 5 seconds
  pause:
    seconds: 5
    
- name: Pause for user input
  pause:
    prompt: "Press Enter to continue or Ctrl+C to abort"
```

## Vault and Secrets Management

### Creating and Editing Encrypted Files
```yaml
# Create new encrypted file
ansible-vault create secrets.yml

# Edit existing encrypted file
ansible-vault edit secrets.yml

# View encrypted file
ansible-vault view secrets.yml

# Encrypt existing file
ansible-vault encrypt plain.yml

# Decrypt file
ansible-vault decrypt secrets.yml

# Change vault password
ansible-vault rekey secrets.yml
```

### Encrypted Variables in Playbooks
```yaml
# Encrypt string
ansible-vault encrypt_string 'secret_value' --name 'api_key'

# Result for use in playbook
api_key: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          39636466303164343830393035333631633239333063623365633264633036323763343131626539
          3166383164363064323937613565343633336134636165380a653531636639343335303664646362
          33333639626134613631396633306539623638373431303239613433666364393631346664316337
          3663613533313937300a353069663730646336393166663933316633623339636464326432363263
          3337

# Use encrypted variable in playbook
- name: Configure API access
  template:
    src: api_config.j2
    dest: /etc/api_config.conf
  vars:
    api_key: !vault |
             $ANSIBLE_VAULT;1.1;AES256
             39636466303164343830393035333631633239333063623365633264633036323763343131626539
             3166383164363064323937613565343633336134636165380a653531636639343335303664646362
             33333639626134613631396633306539623638373431303239613433666364393631346664316337
             3663613533313937300a353069663730646336393166663933316633623339636464326432363263
             3337
```

### Using Vault in Playbooks
```yaml
# Run playbook with vault password
ansible-playbook playbook.yml --ask-vault-pass

# Use password file
ansible-playbook playbook.yml --vault-password-file=~/.vault_pass

# Use script that returns password
ansible-playbook playbook.yml --vault-password-file=get_vault_pass.py

# Multiple vault IDs
ansible-playbook playbook.yml --vault-id dev@dev_vault_pass --vault-id prod@prod_vault_pass

# Create file with specific vault ID
ansible-vault create --vault-id dev@dev_vault_pass dev_secrets.yml
```

### Vault Best Practices
```yaml
# Use different vault passwords for different environments
ansible-vault encrypt --vault-id dev@prompt dev_secrets.yml
ansible-vault encrypt --vault-id prod@prompt prod_secrets.yml

# Store only sensitive data in encrypted files
# Good: separate sensitive and non-sensitive data
# vars/main.yml (not encrypted)
app_name: myapp
app_port: 8080

# vars/secrets.yml (encrypted)
db_password: securepassword
api_token: secrettoken

# Include encrypted file in playbook
- name: Configure application
  hosts: app_servers
  vars_files:
    - vars/main.yml
    - vars/secrets.yml
  tasks:
    - name: Configure database connection
      template:
        src: db_config.j2
        dest: /etc/myapp/db.conf
```

## Tags

### Basic Tag Usage
```yaml
# Tagging tasks
- name: Install nginx
  apt:
    name: nginx
    state: present
  tags: nginx

# Multiple tags on a task
- name: Configure firewall
  iptables:
    chain: INPUT
    jump: ACCEPT
    protocol: tcp
    destination_port: 80
  tags:
    - firewall
    - web
    - security
    
# Tagging plays
- name: Web server configuration
  hosts: webservers
  tags: web
  tasks:
    - name: Install packages
      apt:
        name: "{{ item }}"
        state: present
      loop:
        - nginx
        - php-fpm
```

### Special Tags
```yaml
# Always run tag (ignores tag selection)
- name: Update apt cache
  apt:
    update_cache: yes
  tags:
    - always
    
# Never run tag (unless explicitly requested)
- name: Debug task
  debug:
    msg: "Configuration values: {{ config }}"
  tags:
    - never
    - debug
```

### Tag Inheritance
```yaml
# Tags on includes and imports
- name: Include tasks with tags
  include_tasks: nginx_tasks.yml
  tags:
    - web
    - nginx
    
# Tags on roles
- name: Set up webserver
  hosts: webservers
  roles:
    - role: nginx
      tags: web
```

### Running Tagged Tasks
```bash
# Run only tasks with specific tags
ansible-playbook playbook.yml --tags "web,db"

# Skip tasks with specific tags
ansible-playbook playbook.yml --skip-tags "notifications,backups"

# List tags in playbook
ansible-playbook playbook.yml --list-tags
```

## Performance Optimization

### Configuration Tuning
```ini
# ansible.cfg performance settings
[defaults]
forks = 20                     # Parallel processes (default: 5)
fact_caching = jsonfile        # Cache facts between runs
fact_caching_connection = /tmp/facts_cache  # Cache location
fact_caching_timeout = 86400   # Cache timeout in seconds (1 day)
gathering = smart              # Only gather when needed
host_key_checking = False      # Disable SSH key verification
pipelining = True              # Reduce SSH operations
internal_poll_interval = 0.001 # Faster polling for async tasks

[ssh_connection]
pipelining = True              # Reduce SSH operations
ssh_args = -o ControlMaster=auto -o ControlPersist=60s  # SSH multiplexing
control_path = /tmp/ansible-ssh-%%h-%%p-%%r  # Control path
```

### Minimizing Overhead
```yaml
# Disable fact gathering when not needed
- hosts: all
  gather_facts: no
  tasks:
    - name: Simple task
      debug:
        msg: "No facts needed"
        
# Gather only required facts
- hosts: all
  gather_facts: yes
  gather_subset:
    - '!all'
    - '!min'
    - hardware
    - network
  tasks:
    - name: Network tasks
      debug:
        var: ansible_default_ipv4
        
# Run operations in batch
- name: Update packages in batch
  apt:
    name:
      - nginx
      - postgresql
      - redis-server
    state: present
  # More efficient than individual tasks or loops
```

### Parallel Execution
```yaml
# Set forks at playbook level
- hosts: all
  serial: 10  # Process 10 hosts at a time
  tasks:
    - name: Restart services
      service:
        name: "{{ service_name }}"
        state: restarted
        
# Rolling update pattern
- hosts: webservers
  serial: "25%"  # Process 25% of hosts at a time
  tasks:
    - name: Take out of load balancer
      # ...
      
    - name: Update application
      # ...
      
    - name: Return to load balancer
      # ...
      
# Free strategy (fully parallel tasks)
- hosts: all
  strategy: free
  tasks:
    - name: Independent task
      # ...
```

### Delegated and Local Operations
```yaml
# Delegate operations to specific host
- name: Update load balancer
  shell: add_backend {{ inventory_hostname }}
  delegate_to: loadbalancer.example.com
  
# Delegate to localhost
- name: API call
  uri:
    url: https://api.example.com/notify
    method: POST
    body_format: json
    body:
      host: "{{ inventory_hostname }}"
      status: "updated"
  delegate_to: localhost
  
# Run once and delegate
- name: Database initialization
  command: /usr/bin/initialize_db.sh
  run_once: true
  delegate_to: "{{ groups['database'][0] }}"
  
# Local action shorthand
- name: Check website
  local_action:
    module: uri
    url: "https://{{ inventory_hostname }}"
    status_code: 200
```

## Testing and Validation

### Syntax Checking
```bash
# Check playbook syntax
ansible-playbook playbook.yml --syntax-check

# Check role syntax
ansible-playbook roles/test.yml --syntax-check
```

### Dry Run (Check Mode)
```bash
# Test without making changes
ansible-playbook playbook.yml --check

# Check mode with diff
ansible-playbook playbook.yml --check --diff
```

### Validate Task Results
```yaml
- name: Get service status
  command: systemctl status nginx
  register: service_status
  changed_when: false  # Don't report as changed
  
- name: Verify service is running
  assert:
    that:
      - "'active (running)' in service_status.stdout"
      - service_status.rc == 0
    fail_msg: "Nginx is not running!"
    success_msg: "Nginx is running correctly"
    
# Multiple assertions with custom messages
- name: Verify Python environment
  assert:
    that:
      - ansible_python.version.major == 3
      - ansible_python.version.minor >= 6
    fail_msg: "Python 3.6+ is required, found: {{ ansible_python.version.major }}.{{ ansible_python.version.minor }}"
```

### Molecule (Testing Framework)
```yaml
# molecule/default/molecule.yml
---
dependency:
  name: galaxy
driver:
  name: docker
platforms:
  - name: instance
    image: ubuntu:20.04
provisioner:
  name: ansible
verifier:
  name: ansible

# molecule/default/verify.yml
---
- name: Verify
  hosts: all
  tasks:
    - name: Check nginx is installed
      package:
        name: nginx
        state: present
      check_mode: yes
      register: pkg_check
      failed_when: pkg_check.changed
      
    - name: Check nginx is running
      service:
        name: nginx
        state: started
      check_mode: yes
      register: svc_check
      failed_when: svc_check.changed
      
    - name: Test nginx response
      uri:
        url: http://localhost
        status_code: 200
```

### Testing Playbooks Against Different Distributions
```bash
# Using docker for testing
docker run -it --rm -v $(pwd):/ansible ubuntu:20.04 /bin/bash

# Inside container
apt-get update && apt-get install -y python3 python3-pip sudo
pip3 install ansible
cd /ansible
ansible-playbook -i 'localhost,' -c local playbook.yml
```

## Dynamic Inventories

### AWS EC2 Dynamic Inventory
```yaml
# aws_ec2.yml
plugin: aws_ec2
regions:
  - us-east-1
  - us-west-1
keyed_groups:
  - key: tags.Environment
    prefix: env
  - key: tags.Role
    prefix: role
  - key: instance_type
    prefix: type
groups:
  webservers: "'web' in (tags.Role|lower)"
  production: "'prod' in (tags.Environment|lower)"
compose:
  ansible_host: public_ip_address
  ansible_user: "'ubuntu' if 'ubuntu' in image.name else 'ec2-user'"
  postgres_port: "5432 if 'postgres' in tags.Role else ''"
```

### Azure Dynamic Inventory
```yaml
# azure_rm.yml
plugin: azure_rm
include_vm_resource_groups:
  - production-rg
  - staging-rg
keyed_groups:
  - prefix: tag
    key: tags
exclude_host_filters:
  - powerstate != 'running'
```

### GCP Dynamic Inventory
```yaml
# gcp_compute.yml
plugin: gcp_compute
projects:
  - my-project-id
regions:
  - us-central1
  - us-east1
zones:
  - us-central1-a
keyed_groups:
  - key: labels
    prefix: label
hostnames:
  - name
compose:
  ansible_host: networkInterfaces[0].accessConfigs[0].natIP
```

### Custom Dynamic Inventory Script
```python
#!/usr/bin/env python3
# custom_inventory.py

import argparse
import json
import sys

def get_inventory():
    inventory = {
        '_meta': {
            'hostvars': {
                'host1': {
                    'ansible_host': '10.0.0.1',
                    'role': 'web'
                },
                'host2': {
                    'ansible_host': '10.0.0.2',
                    'role': 'db'
                }
            }
        },
        'webservers': {
            'hosts': ['host1']
        },
        'databases': {
            'hosts': ['host2']
        },
        'all': {
            'children': ['webservers', 'databases']
        }
    }
    return inventory

def main():
    parser = argparse.ArgumentParser()
    parser.add_argument('--list', action='store_true')
    parser.add_argument('--host', action='store')
    args = parser.parse_args()

    inventory = get_inventory()
    
    if args.list:
        print(json.dumps(inventory))
    elif args.host:
        host_vars = inventory['_meta']['hostvars'].get(args.host, {})
        print(json.dumps(host_vars))
    else:
        parser.print_help()

if __name__ == '__main__':
    main()
```

## Best Practices

### Project Structure
```
project_root/
│
├── ansible.cfg             # Project-specific configuration
├── inventory/
│   ├── production/         # Production inventory
│   │   ├── hosts.yml       # Production hosts
│   │   ├── group_vars/     # Production group variables
│   │   └── host_vars/      # Production host variables
│   │
│   └── staging/            # Staging inventory
│       ├── hosts.yml       # Staging hosts
│       ├── group_vars/     # Staging group variables
│       └── host_vars/      # Staging host variables
│
├── playbooks/              # Playbooks
│   ├── site.yml            # Main playbook
│   ├── webservers.yml      # Webserver-specific playbook
│   └── databases.yml       # Database-specific playbook
│
├── roles/                  # Roles
│   ├── common/             # Common role
│   ├── webserver/          # Webserver role
│   └── database/           # Database role
│
├── group_vars/             # Group variables (all environments)
│   └── all.yml             # Variables for all groups
│
├── host_vars/              # Host variables (all environments)
│
├── files/                  # Static files
│
├── templates/              # Templates
│
├── library/                # Custom modules
│
├── filter_plugins/         # Custom filter plugins
│
├── requirements.yml        # Dependencies (roles and collections)
│
└── collections/            # Local collections
```

### Organizing Complex Playbooks
```yaml
# site.yml - Main playbook
---
- name: Include common configuration for all hosts
  import_playbook: common.yml

- name: Configure webservers
  import_playbook: webservers.yml

- name: Configure databases
  import_playbook: databases.yml

# webservers.yml
---
- name: Configure web servers
  hosts: webservers
  roles:
    - common
    - nginx
    - php

# databases.yml
---
- name: Configure database servers
  hosts: databases
  roles:
    - common
    - postgresql
```

### Variable Precedence Best Practices
```yaml
# Default values in role defaults
# roles/nginx/defaults/main.yml
nginx_port: 80
nginx_user: www-data

# Environment-specific in group_vars
# inventory/production/group_vars/webservers.yml
nginx_port: 443  # Override for production

# Host-specific override in host_vars
# inventory/production/host_vars/web1.example.com.yml
nginx_custom_config: true
```

### Secrets Management
```yaml
# Store secrets in Ansible Vault
# inventory/production/group_vars/all/vault.yml (encrypted)
vault_db_password: securepassword
vault_api_keys:
  service1: key1
  service2: key2

# Reference vault variables in regular variables
# inventory/production/group_vars/all/vars.yml
db_password: "{{ vault_db_password }}"
api_key: "{{ vault_api_keys.service1 }}"
```

### Idempotency and Deterministic Behavior
```yaml
# Check before making changes
- name: Check if app is installed
  stat:
    path: /usr/local/bin/myapp
  register: app_binary

- name: Install app
  unarchive:
    src: myapp.tar.gz
    dest: /usr/local/bin
  when: not app_binary.stat.exists

# Use creates parameter for idempotency
- name: Run database migration
  command: ./migrate.sh
  args:
    chdir: /opt/myapp
    creates: /opt/myapp/.migration_complete
```

### Handling Different OS Families
```yaml
# Use variables for OS-specific values
- name: Include OS-specific variables
  include_vars: "{{ item }}"
  with_first_found:
    - "{{ ansible_distribution | lower }}-{{ ansible_distribution_version }}.yml"
    - "{{ ansible_distribution | lower }}.yml"
    - "{{ ansible_os_family | lower }}.yml"
    - "default.yml"
  tags: always

# OS-specific tasks
- name: Install web server
  block:
    - name: Install Apache (Debian)
      apt:
        name: apache2
        state: present
      when: ansible_os_family == "Debian"
      
    - name: Install Apache (RedHat)
      yum:
        name: httpd
        state: present
      when: ansible_os_family == "RedHat"
```

### Managing Task Output
```yaml
# Control verbosity
- name: Detailed debug output
  debug:
    var: setup_result
    verbosity: 2  # Only show with -vv or more
    
# No_log for sensitive operations
- name: Configure password
  template:
    src: credentials.j2
    dest: /etc/app/credentials.conf
  no_log: true
  
# Custom callback plugin config in ansible.cfg
[defaults]
stdout_callback = yaml  # More readable output
callback_whitelist = timer, profile_tasks  # Enable additional callbacks
```

## Troubleshooting

### Verbosity Levels
```bash
# Basic verbose
ansible-playbook playbook.yml -v

# More verbose (module arguments)
ansible-playbook playbook.yml -vv

# Extra verbose (connection info)
ansible-playbook playbook.yml -vvv

# Debug level (extended info)
ansible-playbook playbook.yml -vvvv
```

### SSH Debugging
```bash
# SSH verbosity in ansible.cfg
[ssh_connection]
ssh_args = -o ControlMaster=auto -o ControlPersist=60s -vvv

# Or on command line
ANSIBLE_SSH_ARGS='-vvv' ansible-playbook playbook.yml

# Test SSH connection manually
ssh -i keyfile.pem -vvv user@hostname
```

### Task Debugging
```yaml
# Step by step execution
ansible-playbook playbook.yml --step

# Start at specific task
ansible-playbook playbook.yml --start-at-task="Install packages"

# Use debug module
- name: Debug variable
  debug:
    var: complex_variable
    
# Debug specific elements
- name: Debug specific elements
  debug:
    msg: "Service: {{ service.name }}, Status: {{ service.status }}"
  loop: "{{ services }}"
  
# Debug registered variable
- name: Run command
  command: cat /etc/passwd
  register: passwd_content
  
- name: Debug command results
  debug:
    msg: |
      Return code: {{ passwd_content.rc }}
      Stdout: {{ passwd_content.stdout }}
      Stderr: {{ passwd_content.stderr }}
```

### Common Issues
```yaml
# Fix Python interpreter issues
- hosts: all
  gather_facts: no
  pre_tasks:
    - name: Install Python
      raw: apt-get update && apt-get install -y python3
      changed_when: false
      
    - name: Set Python interpreter
      set_fact:
        ansible_python_interpreter: /usr/bin/python3
        
    - name: Gather facts
      setup:
      
# Fix unreachable host issues
- hosts: all
  vars:
    ansible_ssh_common_args: '-o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null'
  
# Fix permission issues
- hosts: all
  become: yes
  become_method: sudo
  vars:
    ansible_become_password: "{{ lookup('env', 'ANSIBLE_SUDO_PASS') }}"
    
# Fix slow execution
- hosts: all
  gather_facts: no
  strategy: free
  serial: 10
```

### Recovery Strategies
```yaml
# Safe playbook testing with tags
- hosts: production
  gather_facts: no
  tasks:
    - name: Update configuration
      template:
        src: app.conf.j2
        dest: /etc/app/app.conf
      tags: config
      
    - name: Restart service
      service:
        name: app
        state: restarted
      tags: restart
      
# First test just the config update
ansible-playbook playbook.yml --tags config --check --diff

# Simple rollback
- hosts: webservers
  tasks:
    - name: Backup config
      copy:
        src: /etc/nginx/nginx.conf
        dest: /etc/nginx/nginx.conf.bak
        remote_src: yes
        
    - name: Update config
      template:
        src: nginx.conf.j2
        dest: /etc/nginx/nginx.conf
      register: config_update
      
    - name: Test config
      command: nginx -t
      register: nginx_test
      failed_when: nginx_test.rc != 0
      changed_when: false
      
    - name: Rollback on failure
      copy:
        src: /etc/nginx/nginx.conf.bak
        dest: /etc/nginx/nginx.conf
        remote_src: yes
      when: config_update.changed and nginx_test.failed
```
