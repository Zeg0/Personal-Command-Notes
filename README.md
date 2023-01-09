# Personal-Command-Notes
just some scribbel notes of useful commands

# Crontab

https://crontab.guru/#0_1_*_*_*
```
EDITOR=nano crontab -e
```

# Unix System Infos

```
df -h
du -h -d 1 /var/
uname -a
ps -aux
whoami
netstat -tulpn | grep LISTEN
lsof -i -P -n | grep LISTEN

```

# Get Root

```
sudo su -
su -
```

# ssh

```
ssh servername
nano ~/.ssh/config
ssh-keygen -t rsa
```
```
Host servername
        HostName 11.22.33.44
        # IdentityFile ~/.ssh/server_keyfile.pem
        User ubuntu
```

# Install Go on Windows-WSL / without apt

```
# sudo rm -rf /usr/local/go* && sudo rm -rf /usr/local/go

OS=linux
ARCH=amd64
VERSION=1.18
cd $HOME
wget https://storage.googleapis.com/golang/go$VERSION.$OS-$ARCH.tar.gz
tar -xvf go$VERSION.$OS-$ARCH.tar.gz
mv go go-$VERSION
sudo mv go-$VERSION /usr/local
go --v
```

# systemd service files
```
systemctl start my_abc.service
systemctl restart my_abc.service
systemctl stop my_abc.service
systemctl status my_abc.service
```
--> /etc/systemd/system/my_abc.service
```
[Unit]
Description="SOME EXECUTABLE SERVICE -ABC"
After=network.target

[Service]
Environment="KEY=VALUE"
Type=forking
User=root
Group=root
ExecStart=/home/zego/executable.bin -arg1 abc
RuntimeDirectory=/home/zego/
WorkingDirectory=/home/zego/
Restart=on-failure
Type=simple
RestartSec=3
TimeoutStopSec=30s

[Install]
WantedBy=multi-user.target"
``` 

# Windows neue CMD-Shell als anderer User abc spawnen

```
runas /profile /env /user:abc cmd
```

# list unprocessed nats messages in one channel for each consumer
```
#!/bin/bash
credfile=/home/my-wrk.creds
cafile=/home/nats-ca.cer
server=nats.localhost
#### nats $*  --creds $credfile --tlsca=$cafile -s $server 
channel=NATS_TEST_CHANNEL_CONSUMER
nats --creds $credfile --tlsca=$cafile -s $server consumer report $channel | grep -v '───────' | grep -v 'Consumer report for' | awk -F'│' '{print $2 $8}'
```
