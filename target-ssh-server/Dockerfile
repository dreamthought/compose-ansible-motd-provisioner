FROM ubuntu:16.04
MAINTAINER "Raf G <raf+codez@dreamthought.com>"

# 1. BASELINE DEPENDENCIES
RUN apt update
# Needed to add a ppa for 16.04 (would be better)
RUN apt install software-properties-common -y
RUN apt-add-repository ppa:ansible/ansible
RUN apt install ansible -y
RUN apt install net-tools -y


# 2. BASELINE PROVISIONING
# Although we could setup a baseline user in the Dockerfile, 
# to support the demonstration we will use Ansible for 
# _baseline_ provisoning
#
# This will put the environment in a state for remote management:
# * Creates an 'ansible' user with sudo
# * Sets up ssh authorized_keys 
RUN mkdir -p /opt/initial-setup
WORKDIR /opt/initial-setup
COPY . .
RUN ansible-playbook -i ./hosts --connection=local baseline.yml

# 3. CONVENIENCE DAEMONIZATION TO SUPPORT THE DEMONSTRATION
# Since there are no active foreground processes, I'll just tail /dev/null
# to keep the box up. This is only to keep this useless container up 
# in order to demonstrate remote provisioning.
# Note that I acknowledge that this not a good practice in general.
CMD service ssh start && tail -f /dev/null
