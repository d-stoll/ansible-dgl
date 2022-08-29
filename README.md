# Ansible playbooks for distributed DGL

Ansible playbooks for the deployment of a cluster for distributed DGL.

Only the following setup is supported and tested:

* Ubuntu 20.04 LTS on all nodes
* PyTorch as DGL backend (only CPU)
* NFS for sharing files between nodes

Make sure that all nodes have Ubuntu installed and are reachable via SSH.

## Usage

### Define the inventory file

Create a new ansible inventory containing the IP addresses of the master and worker instances:

```{shell}
$ cat > hosts << EOL
[master]
<YOUR_MASTER_IP>

[worker]
<YOUR_WORKER_IP_1>
<YOUR_WORKER_IP_2>
<YOUR_WORKER_IP_3>
EOL
```

### Set required variables

The playbooks contain parts for which you have to define mandatory variables such as NFS username and password. 

You can define a local secret file to store the variables:

```{shell}
$ cat > .secrets.yml << EOL
mount_username: <YOUR_USERNAME>
mount_password: <YOUR_PASSWORD>
mount_src: <YOUR_MOUNT_SOURCE>
EOL
```

### Execute the playbooks

To set up the DGL cluster, execute:

```{shell}
$ ansible-playbook -i hosts setup-cluster.yml -e ".secrets.yml"
```

To stop the DGL cluster, execute:

```{shell}
$ ansible-playbook -i hosts stop-cluster.yml
```

To re-start the DGL cluster, execute:

```{shell}
$ ansible-playbook -i hosts start-cluster.yml
```

To update apt packages or change the DGL/Pytorch version of the DGL cluster, execute:

```{shell}
$ ansible-playbook -i hosts update-cluster.yml
```

### Configuring the deployment

Required variables are marked with *

| Variable           | Description                                           | Default value       |
|:-------------------|:------------------------------------------------------|:--------------------|
| dgl_version        | Version of Deep Graph Library (DGL)                   | 0.9.0               |
| pytorch_version    | Version of PyTorch                                    | 1.11.0              |
| python_version     | Version of the Python interpreter                     | 3.9                 |
| workspace          | Path of DGL workspace (shared via NFS with all nodes) | /home/dgl/workspace |
| extra_pip_packages | List of Python packages to be additionally installed  | ogb, networkx       |
| mount_username *   | Username for SMB/CIFS share                           |                     |
| mount_password *   | Password for SMB/CIFS share                           |                     |
| mount_src *        | Mount source path of SMB/CIFS share                   |                     |