## SLUM basics
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
