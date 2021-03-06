# Ansible Gotchas, Tricks and Tools

## Synopsis

**TITLE** Ansible Gotchas, Tricks and Tools

**DESCRIPTION** Ansible is a great configuration management tool. However like all tools there are certain aspects of it one must be aware of to fully utilize its capabilities and not to get stuck when a problem comes up. In this presentation we walk through examples of issues that were faced and solutions that was come up with. We will also be taking a look on some tools we can use to have a faster development cycle.

**ABSTRACT** Ansible documentation is well written however there are certain aspects of it that may not be immediately apparent to the developer. To this extent we will be go over the following subjects:

- Structure of an Ansible project
- Roles structure
- Playbook structure
- Playbook vs Role
- Host files (Parents and children)
- Variables and their scopes
- Roles and dependencies
- Templates
- Conditionals
- Tools:
    ansible-linter
    ansible-galaxy
    ansible-playbook --syntax-check
    ansible-playbook --list-hosts
    ansible-playbook --list-tasks
    ansible-playbook --list-tags
    ansible-playbook --step
    ansible-playbook --diff <-- awesome for templates

## Demonstration

1. `ansible-playbook -i hosts_one playbooks/structure.yml -v`

    Notes:
        - Checkout the following:
        ```
            group_vars
            roles
                structure
                    defaults
                    meta
                    tasks
                    templates
            playbooks
            hosts_one
        ```
2. Move roles before task.
    ```
        ansible-playbook -i hosts_one playbooks/structure.yml -v
    ```
    Gotchas:

        - Roles run before tasks

3. Hosts and host groups
    ```
        ansible-playbook -i hosts_one playbooks/hosts.yml -v
        ansible-playbook -i hosts.d playbooks/hosts.yml -v
        ansible-playbook playbooks/hosts.yml -v
    ```
    Gotchas:

        - If using directory for hosts naming of host files are important. Example: change allhosts to zhosts
        - Just because you specified hosts in a sequence doesn't mean runs will be in a sequence
        - Specify your hosts file otherwise be ready to get the unexpected (i.e. /etc/ansible/hosts might get picked up!)

4. Variables
    ```
       ansible-playbook -i hosts.d playbooks/variables.yml -v
    ```
    Gotchas:

        - Ansible variable priority, its a thing!


        ```
            1.x, the precedence is as follows (with the last listed variables winning prioritization):
            “role defaults”, which lose in priority to everything and are the most easily overridden
            variables defined in inventory
            facts discovered about a system
            “most everything else” (command line switches, vars in play, included vars, role vars, etc.)
            connection variables (ansible_user, etc.)
            extra vars (-e in the command line) always win
        ```

        ```
            In 2.x, we have made the order of precedence more specific (with the last listed variables winning prioritization):

            role defaults
            inventory vars
            inventory group_vars
            inventory host_vars
            playbook group_vars
            playbook host_vars
            host facts
            play vars
            play vars_prompt
            play vars_files
            registered vars
            set_facts
            role and include vars
            block vars (only for tasks in block)
            task vars (only for the task)
            extra vars (always win precedence)
        ```


        - Variables can also be overwritten by roles if you are not careful. A good way around this is to use role name when defining variables.

5. Creating a role is pretty easy. Simply use ansible-galaxy tool like so:

    ansible-galaxy init roles/dependencies01

It is pretty awesome that they already have a tool for this. Simply fill in the blanks and have fun!
Run the following line for this exercises:

    ansible-playbook playbooks/dependencies.yml -v

    Gotcha:

    - Although it is cool to trust dependencies you must remember that if you are doing multiple plays in a single playbook your role might run twice.
