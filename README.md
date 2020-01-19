# Example of using Ansible to provision and test sshd, ufw and motd

## Design Choices
* Ansible - Using ansible playbooks and inventories for provisioning 
* docker-compose / docker : Mutable containers are bad, but I'm using docker 
  to simplify environment creation for the purpose of this example
* In this example Ansible is used by a _master_ server which provisions a _target_ environment.
* Secrets management (for the ssh\_pass key) is simplified to use docker secrets sourced from a 
 plain text file. We assume that a real implementation would use an encrypted store like vault or ansible vault
 
### Note on major-comings
#### Using Containers
In the real world we'd want our container image to be immutable and bake the runtime state into another
layer of the container image. 

_Note that while this uses containers - these are only for convenience. Container images should be immutable_
I considered using docker commit, ansible-container, packer
or something else; but that is beyond the objective of the exercise.


# Plan 
[top level playbook]
 - target\_ssh\_server: local playbook to bring up to a cattle base-line
 - ansible-master:
    - copy: motd
 - role:
    - motd - using a custom version rather than one from galaxy 
    galaxy for demonstration purposes
    - harden-ssh  
    - validate_motd
        - vars: message_to_validate
