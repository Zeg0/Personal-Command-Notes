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

# blue/green deployment with folder-symlinks

```
# mkdir green
# mkdir blue
# touch green/test.sh
# touch blue/test.sh
# ...

# touch deploy.sh
############################################################
#!/bin/bash

deploy_func () {
	echo "deploy..."
}
test_func () {
	echo "test..."
}

active=blue
test=green
ln_active=active
ln_test=test
if [ "$(readlink -- $ln_active )" = blue ];
then
	active=blue
	test=green
else
	active=green
	test=blue
fi
echo "currently active is $active"
echo "deploying to $test now..."
# deploy & test
deploy_func
test_func
if [ "$1" != "-y" ];
then
	# ask to continue
	while true; do
	read -p "Do you want to swap the deployed version to active now? [y/n]" yn
	case $yn in
        	[Yy]* ) break;;
        	[Nn]* ) exit;;
        	* ) echo "Please answer yes or no.";;
	esac
	done
fi
echo "deploy and test done, swapping..."
# swap links
unlink $ln_test
unlink $ln_active
ln -s $active $ln_test
ln -s $test $ln_active
echo "now $test is active"

############################################################

# ./deploy.sh -y
# ./active/test.sh
# ./deploy.sh -y
# ./active/test.sh
# ...
```

# s3-cli step through buckets and download a file manually
```
#!/bin/bash
$BASEURL=mys3domain

if [[ -z "${ACCK}" ]]; then
   read -r -p "enter S3 ACCESS KEY: " ACCK
   export ACCK=$ACCK
fi
if [[ -z "${SECK}" ]]; then
   read -r -p "enter S3 SECRET KEY: " SECK
   export SECK=$SECK
fi

# expecting files to be versioned-tar packages of executables uploaded from a compile-server
# there are some git groups with projects, the compile server builds executables and uploads them to s3 in tar-packages
# --> GRP is the first s3 bucket
# --> PRJ is the second subbucket
# --> inside are versioned release tars

./s3-cli.exe --access-key $ACCK --secret-key $SECK ls s3://$BASEURL/
read -r -p "GIT group (- Example: 'mygroup' -) : " GRP
./s3-cli.exe --access-key $ACCK --secret-key $SECK ls s3://$BASEURL/$GRP/
read -r -p "GIT project (- Example: 'myproject' -) : " PRJ
./s3-cli.exe --access-key $ACCK --secret-key $SECK ls s3://$BASEURL/$GRP/$PRJ/
read -r -p "Enter version (- Example: latest_master or rel-1.0.0 -) : " VERS

echo "Going to download s3://$BASEURL/$GRP/$PRJ/$VERS.tar.gz"
./s3-cli.exe --access-key $ACCK --secret-key $SECK cp s3://$BASEURL/$GRP/$PRJ/$VERS.tar.gz .
echo "Try tar -xvzf $VERS.tar.gz"
tar -xvzf $VERS.tar.gz
echo "end"
```
