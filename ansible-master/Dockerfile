FROM alpine:3.11.2

# Make sure we have ssh (sshpass required for identity with phrase)
RUN apk add --update ansible openssh sshpass

# Move over the assurity motd playbook
WORKDIR /opt/ansible-provisioner
COPY . .

# Move our identity over and secure it
# Structured this way only for demo purposes
RUN chmod 700 keys
RUN chmod -R 600 keys/*

# Make sure we run the playbook 
CMD ansible-playbook -vvvv -i ./hosts ./site.yml
