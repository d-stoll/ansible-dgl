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

### Execute the playbooks

To set up the DGL cluster, execute:

```{shell}
$ ansible-playbook -i hosts setup-cluster.yml
```

To re-start the DGL cluster, execute:

```{shell}
$ ansible-playbook -i hosts restart-cluster.yml
```

To update apt packages or change the DGL/Pytorch version of the DGL cluster, execute:

```{shell}
$ ansible-playbook -i hosts update-cluster.yml
```

### Configuration variables

| Variable           | Description                                                    | Default value          |
|:-------------------|:---------------------------------------------------------------|:-----------------------|
| dgl_version        | Version of Deep Graph Library (DGL)                            | 0.9.0                  |
| pytorch_version    | Version of PyTorch                                             | 1.12.1                 |
| python_version     | Version of the Python interpreter                              | 3.9                    |
| workspace          | Path of DGL workspace (shared via NFS with all nodes)          | /home/ubuntu/workspace |
| extra_pip_packages | List of Python packages to be additionally installed           | ogb, networkx          |
| ansible_user       | System username where DGL is installed (SSH access required)   | ubuntu                 |

## Advanced setup with SMB/CIFS share

By default, the playbooks installs an internally hosted NFS and shares storage from the master node with all workers. 
If you want to use an external data storage provider via samba/cifs, you can set the variable `use_cifs` to `true`. 
You have to specify the following required variables if you are using this option:

| Variable       | Description                                                    |
|:---------------|:---------------------------------------------------------------|
| mount_username | Username for SMB/CIFS share                                    |
| mount_password | Password for SMB/CIFS share                                    |
| mount_src      | Mount source path of SMB/CIFS share                            |

You can define a local secret file to store the variables:

```{shell}
$ cat > .secrets.yml << EOL
use_cifs: true
mount_username: <YOUR_USERNAME>
mount_password: <YOUR_PASSWORD>
mount_src: <YOUR_MOUNT_SOURCE>
EOL
```

and then launch the playbook with an additional variable file:

```{shell}
$ ansible-playbook -i hosts setup-cluster.yml -e ".secrets.yml"
```

Since username and password are confidential information, the playbook does not store these variables in the fstab file, 
so you have to remount on every reboot.
