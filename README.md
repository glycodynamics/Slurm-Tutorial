## SLURM basics
## General SLURM commands 

### Submitting jobs
```
#!/bin/bash
#SBATCH -p shared # partition (queue)
#SBATCH -c 1 # number of cores
#SBATCH --mem 100 # memory pool for all cores
#SBATCH -t 0-2:00 # time (D-HH:MM)
#SBATCH -o slurm.%N.%j.out # STDOUT
#SBATCH -e slurm.%N.%j.err # STDERR

for i in {1..100000}; do
echo $RANDOM >> SomeRandomNumbers.txt
done
sort SomeRandomNumbers.txt
Now you can submit your job with the command:

sbatch myscript.sh
```
### Requesting resources for interactive session

If you specifically want to request an interactive job, you can use the 'srun' option with the appropriate syntax, such as:

```
srun -N1 -c2 --gres=gpu:RTX3080:1 --mem=10GB --time=2-12:30:15 --pty /bin/bash
```
This command will allocate resources for your job with 2 processors, 1 RTX3080 GPU, 10GB of memory, and 2 days 12 hours 30 min, and 15 seconds of time  time.

Explanation:
- `-N1`: Requests one node. Do not request more than one node at the moment. 
- `-c2`: Requests 2 processors (CPU cores) for your job. Use -c4 -c8 ect for 4 and 8 CPUs. 
- `--gres=gpu:RTX3080:1`: Requests 1 RTX3080 GPU for your job. We have three kinds of GPUs, RTX3080, A5000 and A5000ADA. To request two A5000, write `--gres=gpu:A5000:2`
- `--mem=10GB`: Specifies the memory requirement for your job to be 10GB.
- `--time=2-12:30:15`: Requests two days,12 hours 30 min and 15 seconds (Format: D-HH:MM:SS; Default is 24h)
- `--pty /bin/bash`: Opens a bash shell on the allocated resources, allowing you to interact with the node.

```
run --pty /bin/bash  # gives you 1 processor for 24 hours!
srun --gres=gpu:1 --pty /bin/bash ## gives you 1 CPU and 1GPU for 24 hours!
srun -c12 --gres=gpu:1 --pty /bin/bash ## gives you 12 CPUs and 1 GPU for 24 hours!

```



### Job Information

List all current jobs for a user:
```squeue -u <username>```

List all running jobs for a user:
```squeue -u <username> -t RUNNING```

List all pending jobs for a user:
```squeue -u <username> -t PENDING```

List priority order of jobs for the current user (you) in a given partition:
```showq-slurm -o -u -q <partition>```

List detailed information for a job (useful for troubleshooting):
```scontrol show jobid -dd <jobid>```

### Job Control


To cancel one job:
```scancel <jobid>```

To cancel all the jobs for a user:
```scancel -u <username>```

To cancel all the pending jobs for a user:
```scancel -t PENDING -u <username>```

To hold a particular job from being scheduled:
```scontrol hold <jobid>```

To requeue (cancel and rerun) a particular job:
```scontrol requeue <jobid>```

To release a particular job to be scheduled:
```scontrol release <jobid>```

To hold a all the job from being scheduled:
```squeue --user $USER -t PENDING --format "scontrol hold %i" | tail -n +2 | bash```



To release a all the held jobs to be scheduled:
```squeue --user $USER -t PENDING --format "scontrol release %i" | tail -n +2 | bash```


## NODE administration

### Demons
```
sudo systemctl  restart slurmctld
sudo systemctl status slurmctld

sudo systemctl  restart slurmd
sudo systemctl status slurmd
```

### Node status change
```
sudo scontrol update nodename=fucose state=down reason=cg 
sudo scontrol update nodename=fucose state=resume reason=cg

```

Make sure the following two files have read and write permission for user slurm:
```
sudo chmod 666 /var/log/slurm/slurmctld.log
sudo chmod 666 /var/log/slurm/slurmd.log
```
#### Placing a hold on a queued job
If you want to prevent a job from running but leave it in the queue, you can place a hold on it using the scontrol hold command. The job will remain pending until you release it with the scontrol release command. A hold can be useful if you need to modify the input file for a job without losing your place in the queue.

Examples:
```
scontrol show job=JOBID
scontrol hold JOBID
scontrol release JOBID
```
Feb 28 14:03:50 fucose.pharmacy.olemiss.edu munged[105964]: munged: Error: Socket is inaccessible: execute permissions for all required on:

```
/run/munge"chmod a+x /run/munge
sudo systemctl start munge.service
sudo systemctl status munge.service
munge -n | unmunge


Note: Change node status to resume

```
#### Enable accounting
```
yum install mariadb mariadb-server -y
systemctl enable mariadb.service
mysql_install_db
systemctl start mariadb
systemctl status mariadb
systemctl start mariadb

# if it doesn't works:

journalctl -xeu mariadb.service
cd /var/log/
less messages
cd mariadb/
less mariadb.log
cat /etc/passwd
cd /var/lib/
chown -R mysql.mysql mysql
systemctl start mariadb
systemctl status mariadb.service
mysql -u root -e "create user 'slurm'@'localhost' identified by 'Amber_2024'; grant all on slurm_acct_db.* TO 'slurm'@'localhost'; create database slurm_acct_db;"
systemctl stop slurmctld.service
cd /etc/slurm/
cp slurm.conf slurm.conf.orig
vi slurm.conf
vi slurmdbd.conf
id slurm
cat /etc/passwd
vi slurmdbd.conf
touch /var/log/slurmdbd.log
ls -la /var/log/slurm/slurmctld.log
systemctl restart munge
systemctl stop slurmctld.service
systemctl restart slurmdbd.service
systemctl start slurmctld.service
systemctl status slurmdbd.service
systemctl status slurmctld.service

clush -g compute -c slurm.conf --dest /etc/slurm/
clush -g compute -c slurmdbd.conf --dest /etc/slurm/
clush -g compute systemctl restart slurmd


```



#### Editing slurm.conf and copying it to all compute nodes
```
clush -g compute systemctl restart slurmd
```



#### Steps needed after rebooting the machine (GAG):
```
sudo systemctl  start slurmctld
sudo scontrol update nodename=gag state=down reason=cg
sudo scontrol update nodename=gag state=resume reason=cg
```

### Slurm DB account admin ##
```sacctmgr show association where user=username```


```sacctmgr create user name=username account=nonglyco```


### Reports

To print all the jobs by user starting from date 2022-12-31:
```sacct -S2022-12-31 -u sushil -ojobid,start,end,alloccpu,cputime | column -t```


```sreport --parsable cluster Utilization Start=2022-04-1```



### Mounted Drive
Mount drive as below if you want to submit a job in sshfs mounted drive:
```sshfs user@ole.ms.du:/data/user/ /home/user/mount_gag -o uid=$(id -u),gid=$(id -g),allow_other```
