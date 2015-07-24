Boot2Docker
===========

## Boot2Docker Commands

### Starting Boot2Docker
```bash
$> boot2docker up
```

### Stopping Boot2Docker
```bash
$> boot2docker down
```

### Connecting to Boot2Docker Host
```bash
$> boot2docker ssh
```


## Troubleshooting

###DNS-related boot2docker failures

#### Solution 1
This works by using Google's public DNS (8.8.8.8) to resolve domain names to IP addresses. 
Something is wrong with the nameserver that boot2docker is trying to use by default. This answer doesn't speak to 
why the nameserver boot2docker wants to use is busted; it bandaids the issue by using DNS that works.
Note: this will not persist after a reboot of boot2docker.

```bash
osx$ boot2docker up
osx$ boot2docker ssh
docker@boot2docker$ sudo echo "nameserver 8.8.8.8" > /etc/resolv.conf
docker@boot2docker$ sudo /etc/init.d/docker restart
```

#### Solution 2
This workaround is supposed to persist after a reboot.  It addresses the same issues as the workaround above.
See for more info:  https://www.virtualbox.org/manual/ch09.html#nat_host_resolver_proxy
```bash
$ VBoxManage controlvm "boot2docker-vm" poweroff
$ VBoxManage modifyvm "boot2docker-vm" --natdnshostresolver1 off
```
