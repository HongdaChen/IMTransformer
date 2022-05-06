## 1. ssh 

master node send its public key to others，and put it into ".ssh/authoried_keys/".
```
ssh-copy-id .ssh/id_rsa.pub worker
```

## 2. NFS

### 2.1 NFS-Server

```
sudo apt-get install nfs-kernel-server
```

```
mkdir cloud
```

```
# sudo vim /etc/exports
/home/username/cloud worker11(rw,sync,no_subtree_check) worker10(rw,sync,no_subtree_check) worker9(rw,sync,no_subtree_check)
```

```
sudo exportfs -a
```

Run the above command, every time you make a change to `/etc/exports`.

If required, restart the `nfs` server

```
sudo service nfs-kernel-server restart
```

### 2.2 NFS-Worker

```
sudo apt-get install -y nfs-common 
```

```
mkdir cloud
```

 And now, mount the shared directory like 

```
#sudo mount -t nfs server_hostname:/home/username/cloud ~/cloud
sudo mount -t nfs worker12:/home/ubuntu/cloud ~/cloud
```

 To check the mounted directories, 

```
df -h
```

 To make the mount permanent so you don’t have to manually mount the shared directory everytime you do a system reboot, you can create an entry in your file systems table - i.e., `/etc/fstab` file like this: 

```
$ cat /etc/fstab
#MPI CLUSTER SETUP
manager:/home/mpiuser/cloud /home/mpiuser/cloud nfs
```

## 3. Run

```
mpiexec -n 4 --host server:2,worker1:3,worker2:3 python run.py
```

`mpiexec -n 7 --host worker12:1,worker11:2,worker10:2,worker9:2 python3 run.py`

