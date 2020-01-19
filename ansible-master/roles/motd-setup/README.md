motd-setup
=========

Sets up motd

Requirements
------------

motd ansible module

Role Variables
--------------

motd\_greeting - containing the message to display

Dependencies
------------

motd core module

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: servers
      roles:
         - { role: motd-setup, motd\_greeting: 42 }


Author Information
------------------

Raf Gemmail
