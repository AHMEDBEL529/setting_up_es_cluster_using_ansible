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
We start by creating one vm with SSH server installed in, cloning this VM to another three VMs, then making sure to establish a ssh connection between different VMs.
