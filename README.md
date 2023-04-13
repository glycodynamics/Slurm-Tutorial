## SLURM basics
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

Make sure the file folowing two files have read and write permission to user slurm:
```
sudo chmod 666 /var/log/slurm/slurmctld.log
sudo chmod 666 /var/log/slurm/slurmd.log
```
####Placing a hold on a queued job
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


Note: Chnage node status to resume

```



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
donesort SomeRandomNumbers.txt

Now you can submit your job with the command:

sbatch myscript.sh
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



### Reports

To print all the jobs by user starting from date 2022-12-31:
```sacct -S2022-12-31 -u sushil -ojobid,start,end,alloccpu,cputime | column -t```


```sreport --parsable cluster Utilization Start=2022-04-1```



### Mounted Drive
Mount drive as below if you want to submit a job in sshfs mounted drive:
```sshfs user@ole.ms.du:/data/user/ /home/user/mount_gag -o uid=$(id -u),gid=$(id -g),allow_other```
