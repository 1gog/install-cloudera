# install-cloudera





### stage
```bash
cat <<EOF >> /etc/hosts
21.0.1.26 cdh-name.stage.dev
21.0.1.27 cdh-node-01.stage.dev
21.0.1.28 cdh-node-02.stage.dev
EOF
```
repeate command on all host 
cdh-name.stage.dev
cdh-node-01.stage.dev
cdh-node-02.stage.dev


### open port
```bash
firewall-cmd --permanent --zone=public --add-port=22/tcp
firewall-cmd --reload
```

# update system
```bash
yum update -y
```
### install java jdk
```bash
yum install -y java-1.8.0-openjdk
```
