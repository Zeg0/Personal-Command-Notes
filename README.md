# Personal-Command-Notes
just some scribbel notes of useful commands

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
systemctl start dreceiver.service
systemctl restart dreceiver.service
systemctl stop dreceiver.service
systemctl status dreceiver.service
```
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
