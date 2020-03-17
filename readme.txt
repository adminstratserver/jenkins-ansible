# PURPOSE:
# =======
#
# 1. jenkins container (port 8080) which is installed with ansible
# 2. jenkins uses SSH key and host file (stored in $PWD/jenkins_home/ansible) and ansible to access the remote_host
# 3. remote_host which has SSH_server (has public_key)
#
# CONTAINERS CREATION & SETUP:
# ============================
#
# $ docker-compose -f dc_jenkins_centos_mysql_ansible.yml up -d  --build
#
# 1. goto website: localhost:8080 and register jenkins
# 2. 'Manage Jenkins'->'Manage Plugins'->'Available (Filter & Install SSH plugin)'->restart
# 3. 'Credentials'->'Jenkins'->'Global Credentials'->'Add Credentials'->'SSH Username with private key'
#                   user: remote_user
#                   privatekey: copy and paste centos8/remote-key
# 4. 'Manage Jenkins'->'Configure System'->'SSH remote hosts'->SSH sites
#          Hostname: remote_host
#          port: 22
#          Credentials: remote_user
#         (check connection and click save)
# 5. 'New Item'->'Freestyle Project'->'Build(Invoke Ansible Playbook)'
#       Playbook Path: /var/jenkins_home/ansible/play.yml (play.yml file should be currently residing at jenkins_container) 
#       Inventry: File or host list: /var/jenkins_home/ansible/hosts
#       
#       
#
# Test: 
# =====
# login to Jenkins container: $ docker exec -it jenkins_container bash 
#                             $ cd $HOME/ansible
#                             $ ansible -i hosts -m ping test1
#                             $ ansible-playbook -i hosts play.yml (script which creates file)
# login to Remote_Host:       $ docker exec -it remote_container bash
#                             $ cat /tmp/ansible-file
#
#EXECUTION:
#=========
#
# NOTE: whenever the play.yml is changed at jenkins-ansible dir, make sure it is copied to ../jenkins/ansible first
#     : cp play.yml ../jenkins_home/ansible 
#
# Jenkins->Build Now
# $docker exec -it remote_container bash
# cat /tmp/ansible-file (see if the file is created)
