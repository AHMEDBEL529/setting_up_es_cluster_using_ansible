# Setting up Elasticsearch Cluster using ansible :
## Introduction :
Elasticsearch is a powerful open-source, RESTful, distributed real-time search and analytics engine which provides the ability for full-text search. The aim of this project is to deploy Elasticsearrch cluster using Ansible for
automation.
## Overview :
![Screenshot 2022-11-23 175940](https://user-images.githubusercontent.com/77440761/203608648-f1577d12-2ce9-44a7-a336-1bcd36f366c0.png)
  In our project, we will use four VMs : 
  - Three ES nodes :
    - One master node.
    - Two data nodes.
  - One control VM where ansible will be installed to manage the cluster.
## Setup Requirements :
![Screenshot 2022-11-23 at 19-44-21 Excalidraw — Collaborative whiteboarding made easy](https://user-images.githubusercontent.com/77440761/203624486-ac0b6555-8ef3-4478-9cb0-ea1b3957e2ec.png)
## How to create the VMs :
We start by creating one vm with SSH server installed in, cloning this VM to make another three VMs, then making sure to establish a ssh connection between different VMs without password authentication.
## Installing Ansible :
We start by installing Ansible on the Control VM using the following commands (I'm using Ubuntu in this case):
```console
$ sudo apt update
$ sudo apt install software-properties-common
$ sudo apt-add-repository --yes --update ppa:ansible/ansible
$ sudo apt install ansible
```
To make sure that Ansible is installed properly let's query the version :
```console
$ ansible --version
ansible [core 2.13.6]
  config file = None
  configured module search path = ['/home/nodes/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/lib/python3/dist-packages/ansible
  ansible collection location = /home/nodes/.ansible/collections:/usr/share/ansible/collections
  executable location = /usr/bin/ansible
  python version = 3.10.6 (main, Nov  2 2022, 18:53:38) [GCC 11.3.0]
  jinja version = 3.0.3
  libyaml = True
```
## Importing Elasticsearch ansible role :
After installation of Ansible, we can import the Elasticsearch ansible role to the Control VM:
```console
$ ansible-galaxy install elastic.elasticsearch,v7.13.3
```
## Creating Elasticsearch Playbook :
Now let’s create a Playbook file for deployment:
```console
$ vim elk.yml
```
My Playbook file looks like this :
```
- hosts: elk_master_nodes
  roles:
    - role: elastic.elasticsearch
  vars:
    es_version: 7.10.0
    es_enable_xpack: false
    es_data_dirs:
      - "/data/elasticsearch/data"
    es_log_dir: "/data/elasticsearch/logs"
    es_java_install: true
    es_heap_size: "1g"
    es_config:
      cluster.name: "elk-cluster"
      cluster.initial_master_nodes: ["10.5.5.12:9300"] #replace with the ip adress of the master node
      discovery.seed_hosts: ["10.5.5.12:9300","10.5.3.115:9300","10.5.0.101:9300"] #replace with ip adresses of different nodes. 
      http.port: 9200
      node.data: false
      node.master: true
      bootstrap.memory_lock: false
      network.host: '0.0.0.0'
    es_plugins:
     - plugin: ingest-attachment

- hosts: elk_data_nodes
  roles:
    - role: elastic.elasticsearch
  vars:
    es_version: 7.10.0
    es_enable_xpack: false
    es_data_dirs:
      - "/data/elasticsearch/data"
    es_log_dir: "/data/elasticsearch/logs"
    es_java_install: true
    es_config:
      cluster.name: "elk-cluster"
      cluster.initial_master_nodes: ["10.5.5.12:9300"] #replace with the ip adress of the master node
      discovery.seed_hosts: ["10.5.5.12:9300","10.5.3.115:9300","10.5.0.101:9300"] #replace with ip adresses of different nodes. 
      http.port: 9200
      node.data: true
      node.master: false
      bootstrap.memory_lock: false
      network.host: '0.0.0.0'
    es_plugins:
      - plugin: ingest-attachment
```
## Creating the inventory file :
Now let’s create the inventory file for deployment:
```console
$ vim hosts
```
```
[servers]
node1 ansible_host=10.5.5.12
node2 ansible_host=10.5.3.115
node3 ansible_host=10.5.0.101

[elk_master_nodes]
node1
[elk_data_nodes]
node2
node3
```
We can test the connection to the nodes by typing the following command :
```console
$ ansible all -m ping -u nodes -i ~/hosts
```
here's the output in my case :
```
node3 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
node2 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
node1 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}

```
