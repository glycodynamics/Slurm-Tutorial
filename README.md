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







