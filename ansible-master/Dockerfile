FROM alpine:3.11.2

# 1. Make sure we have ssh (sshpass required for identity with phrase)
RUN apk add --update ansible openssh sshpass

# 2. Move over the assurity motd playbook
WORKDIR /opt/ansible-provisioner
COPY . .

# 3. Move our identity over and secure it
# Structured this way only for demo purposes
RUN chmod 700 keys
RUN chmod -R 600 keys/*

# 4. Make sure we run the playbook 
CMD ansible-playbook -i ./hosts ./site.yml

#FIXME: Add a debug environmental
#CMD ansible-playbook -vvvv -i ./hosts ./site.yml
