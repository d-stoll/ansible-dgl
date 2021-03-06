# Ansible playbooks for distributed DGL

Ansible playbooks for the deployment of a cluster for distributed DGL.

Only the following setup is supported and tested:

* Ubuntu 20.04 LTS on all nodes
* PyTorch as DGL backend (only CPU)
* NFS for sharing files between nodes

Make sure that all nodes have Ubuntu installed and are reachable via SSH.

## Usage

### Defining the inventory file

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

To setup the DGL cluster, execute:

```{shell}
$ ansible-playbook -i hosts setup-cluster.yml
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

| Variable        | Description                                           | Default value       |
|:----------------|:------------------------------------------------------|:--------------------|
| dgl_version     | Version of Deep Graph Library (DGL)                   | 0.8.1               |
| pytorch_version | Version of PyTorch                                    | 1.11.0              |
| python_version  | Version of the Python interpreter                     | 3.9                 |
| workspace       | Path of DGL workspace (shared via NFS with all nodes) | /home/dgl/workspace |
| python_packages | List of Python packages to be additionally installed  | ogb, networkx       |