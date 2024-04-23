To install Wazuh in ubuntu systems run the follwing command : 
Quick Install : 
    curl -sO https://packages.wazuh.com/4.7/wazuh-install.sh && sudo bash ./wazuh-install.sh -a
    
Manual Install : 
# create a folder called wazuh-installer for all our setup files.
    mkdir wazuh-installer
    cd wazuh-installer
    
1- Certificate creation
# Download the wazuh-certs-tool.sh script and the config.yml configuration file
    curl -sO https://packages.wazuh.com/4.7/wazuh-certs-tool.sh
    curl -sO https://packages.wazuh.com/4.7/config.yml

# Edit ./config.yml and replace the node names and IP values with the corresponding names and IP addresses. You need to do this for all Wazuh server, Wazuh indexer, and Wazuh dashboard nodes. Add as many node fields as needed. 
---------------------------------
nodes:
  # Wazuh indexer nodes
  indexer:
    - name: node-1
      ip: "<indexer-node-ip>"
    #- name: node-2
    #  ip: "<indexer-node-ip>"
    #- name: node-3
    #  ip: "<indexer-node-ip>"

  # Wazuh server nodes
  # If there is more than one Wazuh server
  # node, each one must have a node_type
  server:
    - name: wazuh-1
      ip: "<wazuh-manager-ip>"
    #  node_type: master
    #- name: wazuh-2
    #  ip: "<wazuh-manager-ip>"
    #  node_type: worker
    #- name: wazuh-3
    #  ip: "<wazuh-manager-ip>"
    #  node_type: worker

  # Wazuh dashboard nodes
  dashboard:
    - name: dashboard
      ip: "<dashboard-node-ip>"
  ---------------------------------

# Run ./wazuh-certs-tool.sh to create the certificates. For a multi-node cluster, these certificates need to be later deployed to all Wazuh instances in your cluster.
    bash ./wazuh-certs-tool.sh -A

# Compress all the necessary files.
    tar -cvf ./wazuh-certificates.tar -C ./wazuh-certificates/ .
    rm -rf ./wazuh-certificates


2. Nodes installation.
# Installing package dependencies.
    apt-get install debconf adduser procps

# Adding Wazuh repository.
  apt-get install gnupg apt-transport-https
  curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | gpg --no-default-keyring --keyring gnupg-ring:/usr/share/keyrings/wazuh.gpg --import && chmod 644 /usr/share/keyrings/wazuh.gpg

# Adding the repository.
  echo "deb [signed-by=/usr/share/keyrings/wazuh.gpg] https://packages.wazuh.com/4.x/apt/ stable main" | tee -a /etc/apt/sources.list.d/wazuh.list
  apt-get update

# Installing Wazuh indexer.
  apt-get -y install wazuh-indexer
