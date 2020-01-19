# Example of using Ansible to provision and test sshd, ufw and motd

## Design Choices
* Ansible - Using ansible playbooks and inventories for provisioning 
* docker-compose / docker : Mutable containers are bad, but I'm using docker 
  to simplify environment creation for the purpose of this example
* In this example Ansible is used by a _master_ server which provisions a _target_ environment.
* Secrets management (for the ssh\_pass key) is simplified to use docker secrets sourced from a 
 plain text file. We assume that a real implementation would use an encrypted store like vault or ansible vault


# Dependencies
* docker-compose wth support for compose file > v3.3
  * _Tested with docker-compose 1.21 on Linux running Docker Engine 19.03.2_ 
* Make sure 4222 is free with ``lsof -i :4222`` The _provisioned_ ssh server's 22 is forwarded to local 4222.

# Folder Structure

* __Top Level__ - docker-compose orchastration to run the example
* __target-ssh-server__ - An ubuntu 16.04 container with a _local_ ansible playbook customising several layers to a _BASELINE_ image
* __ansible-master__ - An alpine container with ansible installed and inventories + roles to harden ssh, setup firewalls, set motd and test motd.

# To RUN
* ``git checkout branch-with-keys`` 
  * __OR__ use ``ssh-keygen`` and create a key WITHOUT a passphrase (see Other Notes below for justification)
    * Copy the public key to `target-ssh-server/public-key/management-key.pub`
    * Copy the private (identity) to `ansible-master/keys/management-key-cert

* Run ``docker-compose up --build``
* To manually connect to the server ```ssh -o StrictHostChecking=no -o IdentitiesOnly=yes -p 4222 -i ./ansible-master/keys/management-key-cert ansible@localhost```

## Other Notes - post implementation learnings
* I would have liked to use inspec to test the final state, but decided on just validating motd on the server using an assert. From a risk-based perspective the connection 
to the server and content of /etc/motd suggest that this is a _reasonable_ validation; although far from complete.
* Experienced issues with ansible utilising sshpass for ssh identity password's. These connections were hanging, so I went for private keys without paswswords.
  * If this were the real world it would imply better key management would be required. Obviously, adding this to git is illadvised. Other than for a fun exercise like this.
* Needed to provision iptables and kernel dependencies, as well as passing ``cap_add: NET_ADMIN`` to the Docker engine to enable the container to manage iptables. 
* Due to issues with ssh\_pass hangs, I had to use identity keys without passphrases. We'll assume that these are provided outside of the application. Given the use of containers
  I did not want to force a user to exec on a container in order to answer a password prompt.

### Note on major-comings
#### Using Containers
In the real world we'd want our container image to be immutable and bake the runtime state into another
layer of the container image. 

_Note that while this uses containers - these are only for convenience. Container images should be immutable_
I considered using docker commit, ansible-container, packer
or something else; but that is beyond the objective of the exercise.


# Initial Plan 
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
